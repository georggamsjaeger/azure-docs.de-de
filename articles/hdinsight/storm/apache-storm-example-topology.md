---
title: Apache Storm-Beispieltopologien in HDInsight | Microsoft Docs
description: "Eine Liste von Beispieltopologien, die mit Apache Storm in HDInsight erstellt und getestet wurden, einschließlich der grundlegenden C#- und Java-Topologien, und mit Event Hubs funktionieren."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: 1a9093255b7f9281afbcca0ea04654780ebf5b89
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2017
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Storm-Beispieltopologien und -komponenten für Apache Storm auf HDInsight

Es folgt eine Liste von Beispielen zur Verwendung mit Apache Storm in HDInsight, die von Microsoft erstellt und verwaltet wird. Diese Beispiele decken eine Vielzahl von Themen ab – von der Erstellung grundlegender C#- und Java-Topologien bis hin zur Arbeit mit Azure-Diensten wie Event Hubs, Cosmos DB, SQL-Datenbank, HBase in HDInsight und Azure Storage. Einige Beispiele demonstrieren außerdem die Arbeit mit nicht-Azure oder sogar nicht-Microsoft-Technologien, wie SignalR und Socket.IO.

| Beschreibung | Zeigt | Sprache/Framework |
|:--- |:--- |:--- |
| [Schreiben von Daten in Azure Data Lake-Speicher aus Apache Storm](apache-storm-write-data-lake-store.md) |Schreiben von Daten in Azure Data Lake-Speicher |Java |
| [Quelle für Event Hub-Spout und -Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Quelle für Event Hub-Spout und -Bolt |Java |
| [Entwickeln von Java-basierten Topologien für Apache Storm in HDInsight][5797064f] |Maven |Java |
| [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio][16fce2d1] |HDInsight Tools für Visual Studio |C#, Java |
| [Verarbeiten von Ereignissen von Azure Event Hubs mit Storm in HDInsight (C#)][844d1d81] |Event Hubs |C# und Java |
| [Verarbeitung von Ereignissen von Azure-Event-Hubs mit Storm auf HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Event Hubs |Java |
| [Analysieren von Sensordaten mit Storm und HBase in HDInsight][ab894747] |Event Hubs, HBase, Socket.IO, Web-Dashboard |C#, Java, JavaScript, HTML |
| [Verarbeiten von Fahrzeugsensordaten von Event Hubs mit Storm in HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Extrahieren, Transformieren und Laden (ETL) von Azure Event Hubs auf HBase mit Storm in HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [C#-Storm-Topologie-Vorlagenprojekt für die Arbeit mit Azure-Diensten über Storm in HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL-Datenbank, HBase, SignalR |C#, Java |
| [Skalierbarkeits-Benchmarks für das Lesen von Azure Event Hubs mit Storm in HDInsight][d6c540e3] |Nachrichtendurchsatz, Event Hubs, SQL-Datenbank |C#, Java |
| [Verwenden von Python mit Storm in HDInsight](apache-storm-develop-python-topology.md) |Python-Komponenten mit einer Flux-Topologie |Python |
| [Verwenden von Kafka mit Storm in HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm: Lese- und Schreibvorgänge für Apache Kafka | Java |

### <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Apache Storm in HDInsight][2b8c3488]
* [Erfahren Sie, wie Sie Storm-Topologien mit Storm in HDInsight bereitstellen und verwalten][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Erfahren Sie, wie Sie einen Storm-Cluster in HDInsight erstellen und das Storm-Dashboard zum Bereitstellen von Beispieltopologien verwenden."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Erfahren Sie, wie Sie Topologien mit dem webbasierten Storm-Dashboard und der Storm-Benutzeroberfläche oder den HDInsight-Tools für Visual Studio bereitstellen und verwalten."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Erfahren Sie, wie Sie C#-Storm-Topologien mithilfe der HDInsight-Tools für Visual Studio erstellen."
[5797064f]:apache-storm-develop-java-topology.md "Erfahren Sie, wie Sie Storm-Topologien in Java mit Maven erstellen, indem Sie eine Grundtopologie zur Wortzählung erstellen."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Erfahren Sie, wie Sie Daten von Azure Event Hubs mit Storm in HDInsight lesen und schreiben."
[ab894747]:apache-storm-sensor-data-analysis.md "Erfahren Sie, wie Sie Apache Storm in HDInsight zum Verarbeiten von Sensordaten von Azure Event Hubs, zum Darstellen mit „D3.js“ und (optional) zum Speichern in HBase verwenden."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Erfahren Sie, wie Sie eine Storm-Topologie verwenden, um Azure Event Hubs-Nachrichten und Azure Cosmos DB-Dokumente zur Datenreferenzierung zu lesen und Daten in Azure Storage zu speichern."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Enthält mehrere Topologien, um den Durchsatz beim Lesen von Azure Event Hubs und das Speichern in einer SQL-Datenbank unter Verwendung von Apache Storm in HDInsight zu veranschaulichen."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Erfahren Sie, wie Sie Daten von Azure Event Hubs lesen, die Daten aggregieren und transformieren und dann in HBase in HDInsight speichern."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Dieses Projekt enthält Vorlagen für Spouts, Bolts und Topologien für die Interaktion mit verschiedenen Azure-Diensten wie Event Hubs, Cosmos DB und SQL-Datenbank."

