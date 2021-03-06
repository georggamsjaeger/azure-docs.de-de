---
title: Abfragen von Daten aus einer Azure Time Series Insights-Umgebung mit C#-Code | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie durch Codieren einer in der .NET-Sprache C# (C-Sharp) geschriebenen benutzerdefinierten App Daten aus einer Azure Time Series Insights-Umgebung abgefragt werden.
services: time-series-insights
ms.service: time-series-insights
author: ankryach
ms.author: ankryach
manager: jhubbard
editor: MicrosoftDocs/tsidocs
reviewer: v-mamcge, jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 77d3135790bbfd79679d188e3b3f1af876d2b905
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2017
---
# <a name="query-data-from-the-azure-time-series-insights-environment-using-c"></a>Abfragen von Daten aus einer Azure Time Series Insights-Umgebung mit C#

Dieses C#-Beispiel veranschaulicht, wie Daten aus einer Azure Time Series Insights-Umgebung abgefragt werden.
Das Beispiel zeigt einige einfache Beispiele für die Verwendung der Abfrage-API:
1. Rufen Sie zur Vorbereitung das Zugriffstoken mithilfe der Azure Active Directory-API ab. Übergeben Sie dieses Token im `Authorization`-Header jeder Abfrage-API-Anforderung. Informationen zum Einrichten nicht interaktiver Anwendungen finden Sie unter [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md). Stellen Sie außerdem sicher, dass alle Konstanten, die zu Anfang des Beispiels definiert wurden, ordnungsgemäß festgelegt sind.
2. Die Liste der Umgebungen, auf die der Benutzer Zugriff hat, wird abgerufen. Eine der Umgebungen wird als relevant übernommen, und für diese Umgebung werden weitere Daten abgefragt.
3. Als Beispiel für eine HTTPS-Anforderung werden Verfügbarkeitsdaten für die relevante Umgebung abgefragt.
4. Als Beispiel für eine Websocketanforderung werden aggregierte Ereignisdaten für die relevante Umgebung abgefragt. Daten werden für den gesamten Verfügbarkeitszeitbereich abgefragt.

## <a name="c-example"></a>C#-Beispiel

```csharp
using System;
using System.Diagnostics;
using System.IO;
using System.Net;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace TimeSeriesInsightsQuerySample
{
    class Program
    {
        // For automated execution under application identity,
        // use application created in Active Directory.
        // To create the application in AAD, follow the steps provided here:
        // https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-authentication-and-authorization

        // SET the application ID of application registered in your Azure Active Directory
        private static string ApplicationClientId = "#DUMMY#";

        // SET the application key of the application registered in your Azure Active Directory
        private static string ApplicationClientSecret = "#DUMMY#";

        // SET the Azure Active Directory tenant.
        private static string Tenant = "#DUMMY#.onmicrosoft.com";

        public static async Task SampleAsync()
        {
            // 1. Acquire an access token.
            string accessToken = await AcquireAccessTokenAsync();

            // 2. Obtain list of environments and get environment FQDN for the environment of interest.
            string environmentFqdn;
            {
                Uri uri = new UriBuilder("https", "api.timeseries.azure.com")
                {
                    Path = "environments",
                    Query = "api-version=2016-12-12"
                }.Uri;
                HttpWebRequest request = WebRequest.CreateHttp(uri);
                request.Method = "GET";
                request.Headers.Add("x-ms-client-application-name", "TimeSeriesInsightsQuerySample");
                request.Headers.Add("Authorization", "Bearer " + accessToken);

                using (WebResponse webResponse = await request.GetResponseAsync())
                using (var sr = new StreamReader(webResponse.GetResponseStream()))
                {
                    string responseJson = await sr.ReadToEndAsync();

                    JObject result = JsonConvert.DeserializeObject<JObject>(responseJson);
                    JArray environmentsList = (JArray)result["environments"];
                    if (environmentsList.Count == 0)
                    {
                        // List of user environments is empty, fallback to sample environment.
                        environmentFqdn = "10000000-0000-0000-0000-100000000108.env.timeseries.azure.com";
                    }
                    else
                    {
                        // Assume the first environment is the environment of interest.
                        JObject firstEnvironment = (JObject)environmentsList[0];
                        environmentFqdn = firstEnvironment["environmentFqdn"].Value<string>();
                    }
                }
            }
            Console.WriteLine("Using environment FQDN '{0}'", environmentFqdn);

            // 3. Obtain availability data for the environment and get availability range.
            DateTime fromAvailabilityTimestamp;
            DateTime toAvailabilityTimestamp;
            {
                Uri uri = new UriBuilder("https", environmentFqdn)
                {
                    Path = "availability",
                    Query = "api-version=2016-12-12"
                }.Uri;
                HttpWebRequest request = WebRequest.CreateHttp(uri);
                request.Method = "GET";
                request.Headers.Add("x-ms-client-application-name", "TimeSeriesInsightsQuerySample");
                request.Headers.Add("Authorization", "Bearer " + accessToken);

                using (WebResponse webResponse = await request.GetResponseAsync())
                using (var sr = new StreamReader(webResponse.GetResponseStream()))
                {
                    string responseJson = await sr.ReadToEndAsync();

                    JObject result = JsonConvert.DeserializeObject<JObject>(responseJson);
                    JObject range = (JObject)result["range"];
                    fromAvailabilityTimestamp = range["from"].Value<DateTime>();
                    toAvailabilityTimestamp = range["to"].Value<DateTime>();
                }
            }
            Console.WriteLine(
                "Obtained availability range [{0}, {1}]",
                fromAvailabilityTimestamp,
                toAvailabilityTimestamp);

            // 4. Get aggregates for the environment:
            //    group by Event Source Name and calculate number of events in each group.
            {
                // Assume data for the whole availablility range is requested.
                DateTime from = fromAvailabilityTimestamp;
                DateTime to = toAvailabilityTimestamp;

                JObject inputPayload = new JObject(
                    // Send HTTP headers as a part of the message since .NET WebSocket does not support
                    // sending custom headers on HTTP GET upgrade request to WebSocket protocol request.
                    new JProperty("headers", new JObject(
                        new JProperty("x-ms-client-application-name", "TimeSeriesInsightsQuerySample"),
                        new JProperty("Authorization", "Bearer " + accessToken))),
                    new JProperty("content", new JObject(
                        new JProperty("aggregates", new JArray(new JObject(
                            new JProperty("dimension", new JObject(
                                new JProperty("uniqueValues", new JObject(
                                    new JProperty("input", new JObject(
                                        new JProperty("builtInProperty", "$esn"))),
                                    new JProperty("take", 1000))))),
                            new JProperty("measures", new JArray(new JObject(
                                new JProperty("count", new JObject()))))))),
                        new JProperty("searchSpan", new JObject(
                            new JProperty("from", from),
                            new JProperty("to", to))))));

                JToken responseContent = await ReadWebSocketResponseAsync(environmentFqdn, "aggregates", inputPayload);

                Console.WriteLine("Dimension Value\t\tCount");
                // Number of items corresponds to number of aggregates in input payload.
                // In this sample list of aggregates in input payload contains only 1 item since request contains 1 aggregate.
                JObject aggregate = (JObject)((JArray)responseContent)[0];
                JArray dimensionValues = (JArray)aggregate["dimension"];
                JArray measures = (JArray)aggregate["measures"];
                Debug.Assert(
                    dimensionValues.Count == measures.Count,
                    "Number of measures and dimensions should match.");
                for (int i = 0; i < dimensionValues.Count; i++)
                {
                    string currentDimensionValue = (string)dimensionValues[i];
                    JArray currentMeasureValues = (JArray)measures[i];
                    double currentCount = (double)currentMeasureValues[0];

                    Console.WriteLine("{0}\t\t{1}", currentDimensionValue, currentCount);
                }
            }

            // 5. Get events for the environment.
            {
                // Assume data for the whole availablility range is requested.
                DateTime from = fromAvailabilityTimestamp;
                DateTime to = toAvailabilityTimestamp;

                int requestedEventCount = 10;

                JObject inputPayload = new JObject(
                    // Send HTTP headers as a part of the message since .NET WebSocket does not support
                    // sending custom headers on HTTP GET upgrade request to WebSocket protocol request.
                    new JProperty("headers", new JObject(
                        new JProperty("x-ms-client-application-name", "TimeSeriesInsightsQuerySample"),
                        new JProperty("Authorization", "Bearer " + accessToken))),
                    new JProperty("content", new JObject(
                        new JProperty("take", requestedEventCount),
                        new JProperty("searchSpan", new JObject(
                            new JProperty("from", from),
                            new JProperty("to", to))))));

                JToken responseContent = await ReadWebSocketResponseAsync(environmentFqdn, "events", inputPayload);

                // Response content has a list of events under "events" property.
                JArray events = (JArray)responseContent["events"];
                int eventCount = events.Count;
                Console.WriteLine("Requested {0} events, actually acquired {0}:", requestedEventCount, eventCount);
                for (int i = 0; i < eventCount; i++)
                {
                    JObject currentEvent = (JObject)events[i];
                    JArray values = (JArray)currentEvent["values"];
                    Console.WriteLine("{{$ts: {0}, values: {1}}}", currentEvent["$ts"].ToString(), String.Join(",", values));
                }
            }
        }

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (ApplicationClientId == "#DUMMY#" || ApplicationClientSecret == "#DUMMY#" || Tenant.StartsWith("#DUMMY#"))
            {
                throw new Exception(
                    $"Use the link {"https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'ApplicationClientId', 'ApplicationClientSecret' and 'Tenant'.");
            }

            var authenticationContext = new AuthenticationContext(
                $"https://login.windows.net/{Tenant}",
                TokenCache.DefaultShared);

            AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
                resource: "https://api.timeseries.azure.com/",
                clientCredential: new ClientCredential(
                    clientId: ApplicationClientId,
                    clientSecret: ApplicationClientSecret));

            // Show interactive logon dialog to acquire token on behalf of the user.
            // Suitable for native apps, and not on server-side of a web application.
            //AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
            //    resource: "https://api.timeseries.azure.com/",
            //    // Set well-known client ID for Azure PowerShell
            //    clientId: "1950a258-227b-4e31-a9cf-717495945fc2",
            //    // Set redirect URI for Azure PowerShell
            //    redirectUri: new Uri("urn:ietf:wg:oauth:2.0:oob"),
            //    parameters: new PlatformParameters(PromptBehavior.Auto));

            return token.AccessToken;
        }

        private static async Task<JToken> ReadWebSocketResponseAsync(string environmentFqdn, string path, JObject inputPayload)
        {
            var webSocket = new ClientWebSocket();

            // Establish web socket connection.
            Uri uri = new UriBuilder("wss", environmentFqdn)
            {
                Path = path,
                Query = "api-version=2016-12-12"
            }.Uri;
            await webSocket.ConnectAsync(uri, CancellationToken.None);

            // Send input payload.
            byte[] inputPayloadBytes = Encoding.UTF8.GetBytes(inputPayload.ToString());
            await webSocket.SendAsync(
                new ArraySegment<byte>(inputPayloadBytes),
                WebSocketMessageType.Text,
                endOfMessage: true,
                cancellationToken: CancellationToken.None);

            // Read response messages from web socket.
            JToken responseContent = null;
            using (webSocket)
            {
                while (true)
                {
                    string message;
                    using (var ms = new MemoryStream())
                    {
                        // Write from socket to memory stream.
                        const int bufferSize = 16 * 1024;
                        var temporaryBuffer = new byte[bufferSize];
                        while (true)
                        {
                            WebSocketReceiveResult response = await webSocket.ReceiveAsync(
                                new ArraySegment<byte>(temporaryBuffer),
                                CancellationToken.None);

                            ms.Write(temporaryBuffer, 0, response.Count);
                            if (response.EndOfMessage)
                            {
                                break;
                            }
                        }

                        // Reset position to the beginning to allow reads.
                        ms.Position = 0;

                        using (var sr = new StreamReader(ms))
                        {
                            message = sr.ReadToEnd();
                        }
                    }

                    JObject messageObj = JsonConvert.DeserializeObject<JObject>(message);

                    // Stop reading if error is emitted.
                    if (messageObj["error"] != null)
                    {
                        break;
                    }

                    // Actual response contents is wrapped into "content" object.
                    responseContent = messageObj["content"];

                    // Stop reading if 100% of completeness is reached.
                    if (messageObj["percentCompleted"] != null &&
                        Math.Abs((double)messageObj["percentCompleted"] - 100d) < 0.01)
                    {
                        break;
                    }
                }

                // Close web socket connection.
                if (webSocket.State == WebSocketState.Open)
                {
                    await webSocket.CloseAsync(
                        WebSocketCloseStatus.NormalClosure,
                        "CompletedByClient",
                        CancellationToken.None);
                }
            }

            return responseContent;
        }

        static void Main(string[] args)
        {
            Task.Run(async () => await SampleAsync()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Abfrage-API-Referenz](/rest/api/time-series-insights/time-series-insights-reference-queryapi)
