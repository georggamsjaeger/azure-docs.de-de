---
title: "Löschen eines HDInsight-Clusters – Azure | Microsoft-Dokumentation"
description: "Informationen zu den verschiedenen Möglichkeiten, einen HDInsight-Cluster zu löschen."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6bacd40627b815c949491b70f8290e40b79e488c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2017
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Löschen eines HDInsight-Clusters mithilfe von Browser, PowerShell oder Azure-Befehlszeilenschnittstelle

Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden. In diesem Artikel erfahren Sie, wie Sie einen Cluster mithilfe des Azure-Portals, Azure PowerShell und der Azure-Befehlszeilenschnittstelle 1.0 löschen.

> [!IMPORTANT]
> Das Löschen eines HDInsight-Clusters führt nicht zum Löschen der zugeordneten Azure Storage-Konten oder Data Lake Store-Instanz. Sie können die in diesen Diensten gespeicherten Daten künftig wiederverwenden.

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihren HDInsight-Cluster aus. Wenn Ihr HDInsight-Cluster nicht mit dem Dashboard verknüpft ist, können Sie ihn anhand seines Namens über das Suchfeld suchen.
   
    ![Portalsuche](./media/hdinsight-delete-cluster/navbar.png)

2. Wählen Sie aus den Clustereinstellungen das Symbol **Löschen** aus. Wenn Sie dazu aufgefordert werden, wählen Sie **Ja**, um den Cluster zu löschen.
   
    ![Löschen-Symbol](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl zum Löschen des Clusters:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters.

## <a name="azure-cli-10"></a>Azure-Befehlszeilenschnittstelle 1.0

Verwenden Sie an einer Eingabeaufforderung den folgenden Befehl zum Löschen des Clusters:

    azure hdinsight cluster delete CLUSTERNAME

Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters.

> [!NOTE]
> Azure CLI 2.0 bietet derzeit (Stand: 23. Oktober 2017) keine Unterstützung für das Löschen von HDInsight-Clustern an.