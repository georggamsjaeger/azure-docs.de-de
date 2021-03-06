---
title: "Erhöhen der Skalierung für Apache Kafka – Azure HDInsight | Microsoft-Dokumentation"
description: "Erfahren Sie, wie Sie verwaltete Datenträger für Apache Kafka-Cluster in Azure HDInsight konfigurieren, um die Skalierbarkeit zu erhöhen."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: e0fef242810148938e62dab9f77ee77b543fbbc0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2017
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurieren von Speicher und Skalierbarkeit für Apache Kafka in HDInsight

Erfahren Sie, wie Sie die Anzahl der von Apache Kafka in HDInsight verwendeten verwalteten Datenträger konfigurieren.

Kafka in HDInsight verwendet den lokalen Datenträger der virtuellen Computer im HDInsight-Cluster. Da Kafka sehr E/A-intensiv ist, wird [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) verwendet, um einen hohen Durchsatz zu ermöglichen und mehr Speicher pro Knoten bereitzustellen. Wenn herkömmliche virtuelle Festplatten (VHD) für Kafka verwendet werden, ist jeder Knoten auf 1 TB beschränkt. Mit verwalteten Datenträgern können Sie mehrere Datenträger verwenden, um für jeden Knoten im Cluster 16 TB zu erzielen.

Das folgende Diagramm zeigt einen Vergleich zwischen Kafka in HDInsight vor verwalteten Datenträgern und Kafka in HDInsight mit verwalteten Datenträgern:

![Diagramm, das Kafka in HDInsight mit einer einzelnen VHD pro VM im Vergleich zu mehreren verwalteten Datenträgern pro VM zeigt](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurieren von verwalteten Datenträgern: Azure-Portal

1. Führen Sie die Schritte unter [Erstellen eines HDInsight-Clusters](../hdinsight-hadoop-create-linux-clusters-portal.md) aus, um die übliche Vorgehensweise zum Erstellen eines Clusters mit dem Portal zu verstehen. Führen Sie den Vorgang zum Erstellen eines Portals nicht aus.

2. Verwenden Sie im Abschnitt __Clustergröße__ das Feld __Datenträger pro Workerknoten__, um die Anzahl von Datenträgern zu konfigurieren.

    > [!NOTE]
    > Der Typ des verwalteten Datenträgers kann entweder __Standard__ (HDD) oder __Premium__ (SSD) sein. Premium-Datenträger werden mit virtuellen Computern der DS- und GS-Serie verwendet. Alle anderen virtuellen Computertypen verwenden den Standardtyp.

    ![Abbildung des Abschnitts „Clustergröße“ mit hervorgehobenen Datenträgern pro Workerknoten](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurieren von verwalteten Datenträgern: Resource Manager-Vorlage

Um die Anzahl der von den Workerknoten in einem Kafka-Cluster verwendeten Datenträger zu steuern, verwenden Sie den folgenden Abschnitt der Vorlage:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Eine vollständige Vorlage, die das Konfigurieren verwalteter Datenträger veranschaulicht, finden Sie unter [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Kafka in HDInsight finden Sie in den folgenden Dokumenten:

* [Verwenden von MirrorMaker zum Erstellen eines Replikats von Kafka in HDInsight](apache-kafka-mirroring.md)
* [Verwenden von Apache Storm mit Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Verwenden von Apache Spark mit Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Herstellen einer Verbindung mit Kafka über eine Azure Virtual Network-Instanz](apache-kafka-connect-vpn-gateway.md)

* [HDInsight-Blog über verwaltete Datenträger mit Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)