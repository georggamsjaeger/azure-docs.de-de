---
title: "Storm-Starter-Beispiele für Apache Storm in HDInsight - Azure | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie mit Apache Storm und den Storm-Starter-Beispielen für HDInsight Big Data-Analysen durchführen und Daten in Echtzeit verarbeiten."
keywords: Storm-Starter, Apache Storm-Beispiel
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/06/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: d8b7d5830684b5e19eadd1b145a933527c2aa9fd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2017
---
#<a name="get-started-with-apache-storm-on-hdinsight-using-the-storm-starter-examples"></a>Erste Schritte mit Apache Storm in HDInsight mithilfe der Storm-Starter-Beispiele

Erfahren Sie, wie Sie Apache Storm in HDInsight mithilfe der Storm-Starter-Beispiele verwenden.

Apache Storm ist ein skalierbares, fehlertolerantes, verteiltes Echtzeit-Berechnungssystem für die Verarbeitung von Datenströmen. Mit Storm in Azure HDInsight können Sie einen cloudbasierten Storm-Cluster erstellen, der Big Data-Analysen in Echtzeit durchführt.

> [!IMPORTANT]
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Erfahrung mit SSH und SCP**. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-storm-cluster"></a> Erstellen eines Storm-Clusters

Verwenden Sie die folgenden Schritte, um ein Cluster vom Typ „Storm in HDInsight“ zu erstellen:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **+ NEU**, **Daten + Analysen** und anschließend auf **HDInsight**.

    ![Erstellen eines HDInsight-Clusters](./media/apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. Geben Sie auf dem Blatt **Grundeinstellungen** die folgenden Informationen ein:

    * **Clustername**: Der Name des HDInsight-Clusters.
    * **Abonnement**: Wählen Sie das zu verwendende Abonnement aus.
    * **Benutzername für Clusteranmeldung** und **Kennwort für Clusteranmeldung**: Die Anmeldung beim Zugriff auf den Cluster über HTTPS. Sie verwenden diese Anmeldeinformationen für den Zugriff auf Dienste wie z.B. die Ambari-Webbenutzeroberfläche oder die REST-API.
    * **Secure Shell (SSH)-Benutzername**: Die für den Clusterzugriff über SSH verwendete Anmeldung. Das Kennwort ist standardmäßig mit dem Kennwort für die Clusteranmeldung identisch.
    * **Ressourcengruppe**: Die Ressourcengruppe, in der der Cluster erstellt wird.
    * **Standort**: Die Azure-Region, in der der Cluster erstellt werden soll.

   ![Wählen Sie das Abonnement aus.](./media/apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Wählen Sie **Clustertyp** aus, und legen Sie dann auf dem Blatt **Clusterkonfiguration** die folgenden Werte fest:

    * **Clustertyp**: Storm

    * **Betriebssystem**: Linux

    * **Version**: Storm 1.1.0 (HDI 3.6)

    * **Clusterebene**: Standard

   Klicken Sie abschließend auf die Schaltfläche **Auswählen**, um die Einstellungen zu speichern.

    ![Auswählen des Clustertyps](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. Legen Sie nach dem Auswählen des Clustertyps mit der Schaltfläche __Auswählen__ den Clustertyp fest. Schließen Sie dann mit der Schaltfläche __Weiter__ die grundlegende Konfiguration ab.

5. Wählen Sie auf dem Blatt **Speicher** ein Speicherkonto aus, oder erstellen Sie eines. Behalten Sie für die Schritte in diesem Dokument für die weiteren Felder auf diesem Blatt die Standardwerte bei. Speichern Sie mit der Schaltfläche __Weiter__ die Speicherkonfiguration.

    ![Festlegen der Speicherkontoeinstellungen für HDInsight](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. Überprüfen Sie auf dem Blatt **Zusammenfassung** die Konfiguration für den Cluster. Ändern Sie ggf. falsche Einstellungen mithilfe der Link zum __Bearbeiten__. Verwenden Sie abschließend die Schaltfläche__Erstellen__, um den Cluster zu erstellen.

    ![Zusammenfassung der Clusterkonfiguration](./media/apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > Das Erstellen des Clusters kann bis zu 20 Minuten dauern.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Ausführen eines Storm-Starter-Beispiels in HDInsight

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!TIP]
    > Der SSH-Client weist unter Umständen darauf hin, dass die Echtheit des Hosts nicht bestätigt werden kann. Geben Sie in diesem Fall `yes` ein, um den Vorgang fortzusetzen.

    > [!NOTE]
    > Wenn Sie zum Schutz Ihres SSH-Benutzerkontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden, um den passenden privaten Schlüssel anzugeben. Beispiel: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie zum Starten einer Beispieltopologie den folgenden Befehl.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    > [!NOTE]
    > Unter älteren Versionen von HDInsight ist der Klassenname der Topologie `storm.starter.WordCountTopology` anstelle von `org.apache.storm.starter.WordCountTopology`.

    Mit diesem Befehl wird die WordCount-Beispieltopologie im Cluster mit dem Anzeigenamen „Wordcount“ gestartet. Nach dem Zufallsprinzip werden Sätze generiert und die Instanzen jedes Worts in den Sätzen gezählt.

    > [!NOTE]
    > Wenn Ihre eigenen Topologien an den Cluster gesendet werden, müssen Sie zuerst die JAR-Datei mit dem Cluster kopieren, bevor Sie den Befehl `storm` verwenden. Kopieren Sie die Datei mit dem Befehl `scp`. Beispiel: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Das Beispiel „WordCount“ und andere Storm-Starter-Beispiele sind unter `/usr/hdp/current/storm-client/contrib/storm-starter/` bereits im Cluster enthalten.

Wenn Sie die Quelle für die Storm-Starter-Beispiele anzeigen möchten, finden Sie den Code unter [https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter). Dieser Link gilt für Storm 1.1.x, das mit HDInsight 3.6 bereitgestellt wird. Verwenden Sie für andere Versionen von Storm die Schaltfläche __Verzweigung__ am oberen Rand der Seite zum Auswählen einer anderen Storm-Version.

## <a name="monitor-the-topology"></a>Überwachen der Topologie

Die Storm-Benutzeroberfläche bietet eine Weboberfläche zum Arbeiten mit ausgeführten Topologien und befindet sich auf dem HDInsight-Cluster.

Führen Sie die folgenden Schritte aus, um die Topologie mithilfe der Storm-Benutzeroberfläche zu überwachen:

1. Rufen Sie in einem Webbrowser die folgende URL auf, um die Storm-Benutzeroberfläche anzuzeigen: `https://CLUSTERNAME.azurehdinsight.net/stormui`. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters.

    > [!NOTE]
    > Wenn Sie dazu aufgefordert werden, einen Benutzernamen und ein Kennwort anzugeben, geben Sie den Namen des Cluster-Administrators (Admin) und das entsprechende Kennwort ein, die Sie beim Erstellen des Clusters verwendet haben.

2. Klicken Sie unter **Topologiezusammenfassung** in der Spalte **Name** auf den Eintrag **wordcount**. Informationen zur Topologie werden angezeigt.

    ![Storm-Dashboard mit Informationen zur Storm-Starter-WordCount-Topologie.](./media/apache-storm-tutorial-get-started-linux/topology-summary.png)

    Auf dieser Seite werden die folgenden Informationen angezeigt:

    * **Topologiestatistik** – Grundlegende Informationen zur Leistung der Topologie, aufgeteilt in Zeitfenster.

        > [!NOTE]
        > Durch die Auswahl eines bestimmten Zeitfensters werden die Informationen in den anderen Abschnitten der Seite entsprechend angepasst.

    * **Spouts** – Grundlegende Informationen zu Spouts, einschließlich des letzten von einem Spout zurückgegebenen Fehlers.

    * **Bolts** – Grundlegende Informationen zu Bolts.

    * **Topologiekonfiguration** – Ausführliche Informationen zur Konfiguration der Topologie.

    Außerdem gibt diese Seite die für die Topologie ausführbaren Aktionen an:

    * **Aktivieren** – Setzt die Verarbeitung einer deaktivierten Topologie fort.

    * **Deaktivieren** – Hält eine aktive Topologie an.

    * **Ausgleichen** – Passt die Parallelität der Topologie an. Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Durch Ausgleichen wird die Parallelität angepasst, um die höhere/geringere Anzahl der Knoten im Cluster zu kompensieren. Weitere Informationen finden Sie unter [Understanding the Parallelism of a Storm Topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (Grundlegendes zur Parallelität einer Storm-Topologie, in englischer Sprache).

    * **Beenden** – Beendet eine Storm-Topologie nach dem angegebenen Zeitlimit.

3. Wählen Sie auf dieser Seite einen Eintrag aus dem Abschnitt **Spouts** oder **Bolts** aus. Informationen zur ausgewählten Komponente werden angezeigt.

    ![Storm-Dashboard mit Informationen zu ausgewählten Komponenten.](./media/apache-storm-tutorial-get-started-linux/component-summary.png)

    Auf dieser Seite werden die folgenden Informationen angezeigt:

    * **Statistik für Spout/Bolt** – Grundlegende Informationen zur Leistung der Komponente, aufgeteilt in Zeitfenster.

        > [!NOTE]
        > Durch die Auswahl eines bestimmten Zeitfensters werden die Informationen in den anderen Abschnitten der Seite entsprechend angepasst.

    * **Eingabestatistik** (nur Bolt) – Informationen zu Komponenten, die vom Bold konsumierte Daten generieren.

    * **Ausgabestatistik** – Informationen zu den von diesem Bold ausgegebenen Daten.

    * **Executors** – Informationen zu Instanzen dieser Komponente.

    * **Fehler** – Von dieser Komponente generierte Fehler.

4. Zum Anzeigen der Spout- oder Bolt-Details wählen Sie im Abschnitt **Executors** einen Eintrag aus der Spalte **Port** aus, um die Details einer bestimmten Instanz der Komponente anzuzeigen.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    In diesem Beispiel kam das Wort **sieben** 1493957 Mal vor. So oft wurde dieses Wort seit dem Start der Topologie erkannt.

## <a name="stop-the-topology"></a>Beenden der Topologie

Kehren Sie zur **Topologiezusammenfassung** der WordCount-Topologie zurück, und klicken Sie im Abschnitt mit den **Topologieaktionen** auf die Schaltfläche **Beenden**. Geben Sie auf Aufforderung für die Verzögerung vor dem Beenden der Topologie 10 (Sekunden) ein. Nach dieser Wartezeit wird die Topologie im Abschnitt **Storm-UI** des Dashboards nicht mehr angezeigt.

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Falls beim Erstellen eines HDInsight-Clusters ein Problem auftritt, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](../hdinsight-administer-use-portal-linux.md#create-clusters) an.

## <a id="next"></a>Nächste Schritte

In diesem Lernprogramm zu Apache Storm haben Sie die Grundlagen der Arbeit mit Storm in HDInsight erlernt. Anschließend erfahren Sie, wie Sie [Java-basierte Topologien mit Maven entwickeln](apache-storm-develop-java-topology.md).

Wenn Sie bereits mit der Entwicklung von Java-basierten Topologien vertraut sind und eine vorhandene Topologie in HDInsight bereitstellen möchten, lesen Sie die Informationen unter [Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight unter Linux](apache-storm-deploy-monitor-topology-linux.md).

Als .NET-Entwickler können Sie C#- oder hybride C#/Java-Topologien mit Visual Studio erstellen. Weitere Informationen finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Hadoop-Tools für Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Beispieltechnologien, die mit Storm in HDInsight verwendet werden können, finden Sie in den folgenden Beispielen:

* [Beispiele für Storm-Topologien für Storm in HDInsight](apache-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[preview-portal]: https://portal.azure.com/
