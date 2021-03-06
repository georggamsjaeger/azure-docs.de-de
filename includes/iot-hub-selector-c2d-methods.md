> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Frühere Tutorials ([Erste Schritte mit IoT Hub] und [Senden von C2D-Nachrichten mit IoT Hub]) veranschaulichen die grundlegenden Gerät-zu-Cloud- und Cloud-zu-Gerät-Messagingfunktionen von IoT Hub. IoT Hub gibt Ihnen außerdem die Möglichkeit, nicht dauerhafte Methoden auf Geräten aus der Cloud aufzurufen. Direkte Methoden stellen eine Anforderung-Antwort-Interaktion mit einem Gerät dar, die einem HTTPS-Aufruf darin ähnelt, dass sie unverzüglich (nach einem vom Benutzer angegebenen Timeout) zu einem Erfolg oder Fehler führt, um den Benutzer über den Status des Aufrufs zu informieren. [Aufrufen einer direkten Methode auf einem Gerät][lnk-devguide-methods] beschreibt direkte Methoden ausführlicher und bietet Hinweise dazu, wann statt C2D-Nachrichten oder gewünschten Eigenschaften eher direkte Methoden verwendet werden sollten.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Verwenden des Azure-Portals zum Erstellen eines IoT Hubs und einer Geräteidentität im IoT Hub
* Erstellen einer simulierten Geräte-App, die eine direkte Methode aufweist, die von der Cloud aufgerufen werden kann
* Erstellen einer Konsolen-App, die eine direkte Methode auf der simulierten Geräte-App über Ihren IoT-Hub aufruft


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Senden von C2D-Nachrichten mit IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Erste Schritte mit IoT Hub]: ../articles/iot-hub/iot-hub-node-node-getstarted.md