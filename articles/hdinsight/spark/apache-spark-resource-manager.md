---
title: "Verwalten von Ressourcen für den Apache Spark-Cluster unter Azure HDInsight | Microsoft-Dokumentation"
description: "Hier erfahren Sie, wie Sie Ressourcen für Spark-Cluster unter Azure HDInsight verwalten, um die Leistung zu optimieren."
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: b2208f0553ce62be054409a415723445733708d4
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Verwalten von Ressourcen für den Apache Spark-Cluster unter Azure HDInsight 

Erfahren Sie, wie Sie auf die mit Ihrem Spark-Cluster verknüpften Benutzeroberflächen wie die Ambari-Benutzeroberfläche, die YARN-Benutzeroberfläche und den Spark-Verlaufsserver zugreifen und wie Sie die Clusterkonfiguration für optimale Leistung abstimmen.

**Voraussetzungen:**

* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Öffnen der Ambari-Webbenutzeroberfläche
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/)im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.
2. Klicken Sie für Ihren Spark-Cluster auf **Dashboard**. Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.

    ![Starten von Ambari](./media/apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "Starten des Ressourcen-Managers")
3. Damit sollte wie im Screenshot dargestellt die Ambari-Webbenutzeroberfläche gestartet werden.

    ![Ambari-Webbenutzeroberfläche](./media/apache-spark-resource-manager/ambari-web-ui.png "Ambari-Webbenutzeroberfläche")   

## <a name="open-the-spark-history-server"></a>Öffnen des Spark-Verlaufsservers
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/)im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben).
2. Klicken Sie auf dem Clusterblatt unter **Quicklinks** auf **Cluster-Dashboard**. Klicken Sie auf dem Blatt **Cluster-Dashboard** auf **Spark-Verlaufsserver**.

    ![Spark-Verlaufsserver](./media/apache-spark-resource-manager/launch-history-server.png "Spark-Verlaufsserver")

    Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.

## <a name="open-the-yarn-ui"></a>Öffnen der YARN-Benutzeroberfläche
Auf der YARN-Benutzeroberfläche können Anwendungen überwachen, die derzeit auf dem Spark-Cluster ausgeführt werden.

1. Klicken Sie auf dem Clusterblatt auf **Cluster-Dashboard** und dann auf **YARN**.

    ![YARN-Benutzeroberfläche starten](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Alternativ können Sie die YARN-Benutzeroberfläche auch über die Ambari-Benutzeroberfläche starten. Klicken Sie zum Starten der Ambari-Benutzeroberfläche auf dem Clusterblatt auf **Cluster-Dashboard** und dann auf **HDInsight-Cluster-Dashboard**. Klicken Sie auf der Ambari-Benutzeroberfläche auf **YARN**, auf **QuickLinks**, auf den aktiven Ressourcen-Manager und dann auf **Resource Manager UI** (Ressourcen-Manager-UI).
   >
   >

## <a name="the-optimum-cluster-configuration-to-run-spark-applications"></a>Die optimale Clusterkonfiguration zum Ausführen von Spark-Anwendungen
Die drei wichtigsten Parameter, die je nach Anwendungsanforderungen für die Spark-Konfiguration verwendet werden können, sind `spark.executor.instances`, `spark.executor.cores` und `spark.executor.memory`. Ein Executor ist ein Prozess, der für eine Spark-Anwendung gestartet wird. Er wird auf dem Workerknoten ausgeführt und ist für die Ausführung der Aufgaben für die Anwendung zuständig. Die Standardanzahl von Executors und die Executorgrößen für jeden Cluster werden basierend auf der Anzahl von Workerknoten und der Größe der Workerknoten berechnet. Diese Informationen werden in `spark-defaults.conf` in den Clusterhauptknoten gespeichert.

Die drei Konfigurationsparameter können auf Clusterebene (für alle Anwendungen, die im Cluster ausgeführt werden) konfiguriert werden oder für jede einzelne Anwendung angegeben werden.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändern der Parameter mit der Ambari-Benutzeroberfläche
1. Klicken Sie auf der Ambari-Benutzeroberfläche auf **Spark** und auf **Configs** (Konfigurationen), und erweitern Sie dann **Custom spark-defaults**.

    ![Festlegen von Parametern mit Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Die Standardwerte sind gut geeignet, um vier Spark-Anwendungen gleichzeitig im Cluster auszuführen. Sie können diese Werte über die Benutzeroberfläche ändern, wie unten dargestellt.

    ![Festlegen von Parametern mit Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Klicken Sie zum Speichern der Konfigurationsänderungen auf **Save** . Oben auf der Seite werden Sie aufgefordert, alle betroffenen Dienste neu zu starten. Klicken Sie auf **Restart**.

    ![Neustarten von Diensten](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändern der Parameter für eine Anwendung, die im Jupyter-Notebook ausgeführt wird
Für Anwendungen, die im Jupyter-Notebook ausgeführt werden, können Sie die `%%configure` -Magic für Konfigurationsänderungen verwenden. Im Idealfall nehmen Sie diese Änderungen am Anfang der Anwendung vor, bevor Sie die erste Codezelle ausführen. Dadurch wird sichergestellt, dass die Konfiguration auf die Livy-Sitzung angewendet wird, wenn sie erstellt wird. Wenn Sie die Konfiguration zu einem späteren Zeitpunkt in der Anwendung ändern möchten, müssen Sie den Parameter `-f` verwenden. Allerdings geht dadurch der gesamte Fortschritt in der Anwendung verloren.

Der folgende Codeausschnitt zeigt, wie die Konfiguration für eine Anwendung geändert wird, die in Jupyter ausgeführt wird.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurationsparameter müssen als JSON-Zeichenfolge übergeben werden und in der nächsten Zeile nach dem Magic-Befehl stehen, wie in der Beispielspalte gezeigt.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändern der Parameter für eine Anwendung, die mit „spark-submit“ übermittelt wird
Der folgende Befehl ist ein Beispiel dafür, wie die Konfigurationsparameter für eine Batchanwendung geändert werden, die mithilfe von `spark-submit`übermittelt wird.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändern der Parameter für eine Anwendung, die mit „cURL“ übermittelt wird
Der folgende Befehl ist ein Beispiel dafür, wie die Konfigurationsparameter für eine Batchanwendung geändert werden, die mithilfe von „cURL“ übermittelt wird.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>Wie ändere ich diese Parameter auf einem Spark Thrift-Server?
Der Spark Thrift-Server bietet JDBC/ODBC-Zugriff auf einen Spark-Cluster und wird verwendet, um Spark-SQL-Abfragen zu verarbeiten. Tools wie Power BI, Tableau usw. verwenden das ODBC-Protokoll zur Kommunikation mit dem Spark Thrift-Server, um Spark-SQL-Abfragen als Spark-Anwendung auszuführen. Wenn ein Spark-Cluster erstellt wird, werden zwei Instanzen des Spark Thrift-Servers gestartet, eine auf jedem Hauptknoten. Jeder Spark Thrift-Server wird als Spark-Anwendung auf der YARN-Benutzeroberfläche angezeigt.

Der Spark Thrift-Server nutzt die dynamische Executorzuordnung in Spark und somit wird `spark.executor.instances` nicht verwendet. Der Spark Thrift-Server verwendet stattdessen `spark.dynamicAllocation.minExecutors` und `spark.dynamicAllocation.maxExecutors`, um die Executoranzahl anzugeben. Die Konfigurationsparameter `spark.executor.cores` und `spark.executor.memory` werden verwendet, um die Executorgröße zu ändern. Sie können diese Parameter ändern, wie in den folgenden Schritten dargestellt.

* Erweitern Sie die Kategorie **Advanced spark-thrift-sparkconf**, um die Parameter `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` und `spark.executor.memory` zu aktualisieren.

    ![Konfigurieren des Spark Thrift-Servers](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Erweitern Sie die Kategorie **Custom spark-thrift-sparkconf**, um den Parameter `spark.executor.cores` zu aktualisieren.

    ![Konfigurieren des Spark Thrift-Servers](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Wie ändere ich den Treiberspeicher des Spark Thrift-Servers?
Der Treiberspeicher des Spark Thrift-Servers ist so konfiguriert, dass er 25 % der RAM-Größe des Hauptknotens umfasst, vorausgesetzt, dass die RAM-Gesamtgröße des Hauptknotens über 14 GB liegt. Sie können mit der Ambari-Benutzeroberfläche die Treiberspeicherkonfiguration ändern, wie unten dargestellt.

* Klicken Sie auf der Ambari-Benutzeroberfläche auf **Spark**, klicken Sie auf **Configs** (Konfigurationen), erweitern Sie **Advanced spark-env**, und geben Sie dann den Wert für **spark_thrift_cmd_opts** an.

    ![Konfigurieren des RAM für den Spark Thrift-Server](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>Ich verwende BI mit Spark-Cluster nicht. Wie nehme ich die Ressourcen zurück?
Da wir die dynamische Zuordnung von Spark verwenden, werden nur die Ressourcen für die beiden Anwendungsmaster vom Thrift-Server genutzt. Um diese Ressourcen freizugeben, müssen Sie die Dienste des Thrift-Servers im Cluster beenden.

1. Klicken Sie im linken Bereich der Ambari-Benutzeroberfläche auf **Spark**.
2. Klicken Sie auf der nächsten Seite auf **Spark Thrift Servers**.

    ![Neustarten des Thrift-Servers](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Sie sollte zwei Hauptknoten sehen, auf denen der Spark Thrift-Server ausgeführt wird. Klicken Sie auf einen der Hauptknoten.

    ![Neustarten des Thrift-Servers](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. Auf der nächsten Seite werden alle auf diesem Hauptknoten ausgeführten Dienste aufgeführt. Klicken Sie in der Liste auf die Dropdownschaltfläche neben „Spark Thrift Server“ und dann auf **Stop**.

    ![Neustarten des Thrift-Servers](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Wiederholen Sie diese Schritte auch auf dem anderen Hauptknoten.

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>Meine Jupyter-Notebooks werden nicht wie erwartet ausgeführt. Wie kann ich den Dienst neu starten?
Starten Sie wie oben gezeigt die Ambari-Webbenutzeroberfläche. Klicken Sie im linken Navigationsbereich auf **Jupyter**, auf **Dienstaktionen** und dann auf **Alle neu starten**. Dadurch wird der Jupyter-Dienst auf allen Stammknoten gestartet.

    ![Restart Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>Wie erkenne ich, ob mir die Ressourcen ausgehen?
Starten Sie die Benutzeroberfläche von Yarn, wie oben gezeigt. Überprüfen Sie im oberen Bildschirmbereich in der Tabelle mit den Clustermetriken die Werte in den Spalten **Memory Used** (Verwendeter Arbeitsspeicher) und **Memory Total** (Arbeitsspeicher gesamt). Wenn die beiden Werte sehr dicht beieinander liegen, sind unter Umständen nicht genügend Ressourcen für den Start der nächsten Anwendung vorhanden. Gleiches gilt für die Spalten **VCores Used** (Verwendete virtuelle Kerne) und **VCores Total** (Virtuelle Kerne gesamt). Falls in der Hauptansicht eine Anwendung mit dem Zustand **Akzeptiert** nicht in den Zustand **Ausgeführt** oder **Fehler** übergeht, kann dies ebenfalls ein Hinweis darauf sein, dass nicht genügend Ressourcen zum Starten der Anwendung zur Verfügung stehen.

    ![Resource Limit](./media/apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>Wie kann ich eine ausgeführte Anwendung beenden, um Ressourcen freizugeben?
1. Klicken Sie im linken Bereich der Benutzeroberfläche von Yarn auf **Running** (Ausgeführt). Suchen Sie in der Liste mit den ausgeführten Anwendungen nach der Anwendung, die Sie beenden möchten, und klicken auf die **ID**.

    ![Beenden einer App (1)](./media/apache-spark-resource-manager/kill-app1.png "Beenden einer App (1)")

2. Klicken Sie rechts oben auf **Kill Application** (Anwendung beenden) und anschließend auf **OK**.

    ![Beenden einer App (2)](./media/apache-spark-resource-manager/kill-app2.png "Beenden einer App (2)")

## <a name="see-also"></a>Weitere Informationen
* [Track and debug jobs running on an Apache Spark cluster in HDInsight(Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Für Datenanalysten

* [Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetry data analysis using Spark in HDInsight (Application Insight-Telemetriedatenanalyse mithilfe von Spark in HDInsight)](apache-spark-analyze-application-insight-logs.md)
* [Verwenden von Caffe in Azure HDInsight Spark für verteiltes Deep Learning](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Für Spark-Entwickler

* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Remoteausführung von Aufträgen in einem Spark-Cluster mithilfe von Livy](apache-spark-livy-rest-interface.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Spark-Streaming: Erstellen von Echtzeitstreaminganwendungen mithilfe von Spark in HDInsight](apache-spark-eventhub-streaming.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Verwenden von HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Spark-Anwendungen)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Zeppelin-Notebooks mit einem Spark-Cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter-Notebook im Spark-Cluster für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)
