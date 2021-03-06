---
title: "Azure Site Recovery-Bereitstellungsplaner für „Hyper-V zu Azure“ | Microsoft-Dokumentation"
description: "In diesem Artikel wird die Ausführung des Azure Site Recovery-Bereitstellungsplaners im Modus für das Szenario „Hyper-V zu Azure“ beschrieben."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: bb4ec5cfd455ab0cc22ab693c2a07eed9883dc76
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2017
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Ausführen des Azure Site Recovery-Bereitstellungsplaners für „Hyper-V zu Azure“

## <a name="modes-of-running-deployment-planner"></a>Modi der Bereitstellungsplaner-Ausführung
Sie können das Befehlszeilentool (ASRDeploymentPlanner.exe) in einem der folgenden vier Modi ausführen: 
1.  [Abrufen der VM-Liste](#get-vm-list-for-profiling-hyper-v-vms)
2.  [Profilerstellung](#profile-hyper-v-vms)
3.  [Berichterstellung](#generate-report)
4.  [Durchsatzberechnung](#get-throughput)

Führen Sie zuerst das Tool aus, um die Liste mit den VMs eines einzelnen oder mehrerer Hyper-V-Hosts abzurufen.  Führen Sie das Tool anschließend im Modus für die Profilerstellung aus, um für die VM die Datenänderung und den IOPS-Wert zu erfassen. Führen Sie im Tool anschließend die Berichterstellung durch, um die Netzwerkbandbreite und die Speicheranforderungen zu ermitteln.

## <a name="get-vm-list-for-profiling-hyper-v-vms"></a>Abrufen der VM-Liste für die Profilerstellung für Hyper-V-VMs
Zuerst benötigen Sie eine Liste mit den VMs, für die die Profilerstellung durchgeführt werden soll. Verwenden Sie den Modus GetVMList des Bereitstellungsplaner-Tools, um die Liste mit den VMs, die auf mehreren Hyper-V-Hosts vorhanden sind, mit einem einzelnen Befehl zu generieren. Nachdem Sie die vollständige Liste generiert haben, können Sie VMs, für die keine Profilerstellung durchgeführt werden soll, aus der Ausgabedatei entfernen. Nutzen Sie die Ausgabedatei anschließend für alle anderen Vorgänge: Profilerstellung, Berichterstellung und Durchsatzberechnung.

Sie können die VM-Liste generieren, indem Sie im Tool auf einen Hyper-V-Cluster oder einen eigenständigen Hyper-V-Host (oder eine Kombination) verweisen.

### <a name="command-line-parameters"></a>Befehlszeilenparameter
Die folgende Tabelle enthält eine Liste mit den erforderlichen und optionalen Parametern des Tools für die Ausführung im Modus GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| Parametername | Beschreibung |
|---|---|
| -Operation | GetVMList |
| -User | Benutzername zum Herstellen der Verbindung mit dem Hyper-V-Host oder Hyper-V-Cluster. Der Benutzer muss über Administratorzugriff verfügen.|
|-ServerListFile | Die Datei, in der die Liste mit den Servern enthalten ist, auf denen sich die VMs für die Profilerstellung befinden. Der Dateipfad kann absolut oder relativ sein. Die Datei sollte in jeder Zeile eine der folgenden Angaben enthalten:<ul><li>Hyper-V-Hostname oder -IP-Adresse</li><li>Hyper-V-Clustername oder -IP-Adresse</li></ul><br>Beispiel: Die Datei „ServerList.txt“ enthält die folgenden Server:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Optional) Der UNC-Pfad (Universal Naming Convention) oder lokale Verzeichnispfad zum Speichern der Daten, die während dieses Vorgangs generiert werden. Wenn keine Angabe vorhanden ist, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
|-OutputFile| (Optional) Die Datei, in der die Liste mit den VMs gespeichert wird, die von den jeweiligen Hyper-V-Servern abgerufen werden. Wenn der Name nicht angegeben wird, werden die Details in „VMList.txt“ gespeichert.  Verwenden Sie die Datei zum Starten der Profilerstellung, nachdem Sie die VMs entfernt haben, für die der Vorgang nicht durchgeführt werden soll.|
|-Password|(Optional) Kennwort zum Herstellen der Verbindung mit dem Hyper-V-Host.   Wenn das Kennwort nicht als Parameter angegeben ist, werden Sie später bei der Ausführung des Befehls zum Eingeben aufgefordert.|

### <a name="how-does-getvmlist-discovery-work"></a>Funktionsweise der GetVMList-Ermittlung
**Hyper-V-Cluster**: Wenn in der Datei mit der Serverliste der Name eines Hyper-V-Clusters angegeben wird, ermittelt das Tool alle Hyper-V-Knoten des Clusters und ruft die VMs der einzelnen Hyper-V-Hosts ab.

**Hyper-V-Host**: Wenn der Name eines Hyper-V-Hosts angegeben wird, prüft das Tool zuerst, ob dieser zu einem Cluster gehört. Wenn ja, werden die Knoten abgerufen, die zum Cluster gehören. Anschließend werden die VMs jedes Hyper-V-Hosts abgerufen. 

Sie können in einer Datei die Anzeigenamen oder IP-Adressen der VMs, für die die Profilerstellung durchgeführt werden soll, auch manuell auflisten.

Öffnen Sie die Ausgabedatei im Editor, und kopieren Sie die Namen aller VMs, für die Profile erstellt werden sollen, in eine andere Datei (z.B. „ProfileVMList.txt“). Fügen Sie einen VM-Namen pro Zeile ein. Diese Datei wird als Eingabe für den Parameter „-VMListFile“ des Tools für alle anderen Vorgänge verwendet: Profilerstellung, Berichterstellung und Durchsatzberechnung.

### <a name="example-1-to-store-the-list-of-vms-in-a-file"></a>Beispiel 1: Speichern der Liste mit den VMs in einer Datei
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

### <a name="example-2-to-store-the-list-of-vms-at-the-default-location-ie--directory-path"></a>Beispiel 2: Speichern der Liste mit den VMs am Standardspeicherort, z.B. -Directory-Pfad
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Durchführen der Profilerstellung für Hyper-V-VMs
Im Profilerstellungsmodus stellt das Bereitstellungsplaner-Tool jeweils eine Verbindung mit den einzelnen Hyper-V-Hosts her, um Leistungsdaten zu den VMs zu sammeln. 

* Die Profilerstellung wirkt sich nicht negativ auf die Leistung der Produktions-VMs aus, weil keine direkte Verbindung damit hergestellt wird. Alle Leistungsdaten werden für den Hyper-V-Host erfasst.
* Das Tool fragt den Hyper-V-Host einmal alle 15 Sekunden ab, um sicherzustellen, dass die Genauigkeit der Profilerstellung gewährleistet ist, und speichert den Mittelwert der Leistungsindikatordaten für jede Minute.
* Das Tool führt die VM-Migration von Knoten zu Knoten im Cluster und die Speichermigration auf einem Host nahtlos durch.

### <a name="get-vm-list-to-profile"></a>Abrufen der VM-Liste für die Profilerstellung
Lesen Sie die Informationen zum [GetVMList](#get-vm-list-for-profiling-hyper-v-vms)-Vorgang, um eine Liste mit VMs für die Profilerstellung zu erstellen.

Nachdem Sie die Liste mit den VMs für die Profilerstellung vorliegen haben, können Sie für das Tool den Modus für die Profilerstellung ausführen. 

### <a name="command-line-parameters"></a>Befehlszeilenparameter
Die folgende Tabelle enthält eine Liste mit den erforderlichen und optionalen Parametern des Tools für die Ausführung im Profilerstellungsmodus. Das Tool ist für die Szenarien „VMware zu Azure“ und „Hyper-V zu Azure“ gleich. Die folgenden Parameter gelten für Hyper-V: 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| Parametername | Beschreibung |
|---|---|
| -Operation | StartProfiling |
| -User | Benutzername zum Herstellen der Verbindung mit dem Hyper-V-Host oder Hyper-V-Cluster. Der Benutzer muss über Administratorzugriff verfügen.|
| -VMListFile | Die Datei mit der Liste der VMs, für die die Profilerstellung durchgeführt werden soll. Der Dateipfad kann absolut oder relativ sein. Für Hyper-V ist diese Datei die Ausgabedatei des Vorgangs GetVMList. Falls Sie die Vorbereitung manuell durchführen, sollte die Datei einen Servernamen oder eine IP-Adresse gefolgt vom VM-Namen mit dem Trennzeichen „\“ pro Zeile enthalten. Der in der Datei angegebene VM-Name sollte dem VM-Namen auf dem Hyper-V-Host entsprechen.<ul>Beispiel: Die Datei „VMList.txt“ enthält die folgenden VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Minuten). Der Mindestwert lautet 30 Minuten.|
|-NoOfHoursToProfile|Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Stunden).|
|-NoOfDaysToProfile |Gibt an, wie lange die Profilerstellung durchgeführt werden soll (in Tagen). Es wird empfohlen, die Profilerstellung länger als 7 Tage durchzuführen. So kann sichergestellt werden, dass das Workloadmuster in Ihrer Umgebung im angegebenen Zeitraum eingehalten und verwendet wird, um eine genaue Empfehlung zu erhalten.|
|-Virtualization|Geben Sie den Virtualisierungstyp an (VMware oder Hyper-V).|
|-Directory|(Optional) Der UNC-Pfad (Universal Naming Convention) oder lokale Verzeichnispfad zum Speichern der Daten, die während des Profilerstellungsvorgangs generiert werden. Wenn keine Angabe vorhanden ist, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
|-Password|(Optional) Das Kennwort zum Herstellen der Verbindung mit dem Hyper-V-Host. Wenn Sie es hier nicht angeben, werden Sie später während der Ausführung des Befehls zur Eingabe aufgefordert.|
|-StorageAccountName|(Optional) Der Name des Speicherkontos zur Ermittlung des Durchsatzes, der für die Replikation von Daten aus der lokalen Umgebung in Azure erreichbar ist. Mit dem Tool werden Testdaten in dieses Speicherkonto hochgeladen, um den Durchsatz zu berechnen.|
|-StorageAccountKey|(Optional) Der Schlüssel des Speicherkontos, der zum Zugreifen auf das Speicherkonto verwendet wird. Navigieren Sie im Azure-Portal zu „Speicherkonten“ > <Storage account name> > „Einstellungen“ > „Zugriffsschlüssel“ > „Key1“ (oder primärer Zugriffsschlüssel für das klassische Speicherkonto).|
|-Environment|(Optional) Dies ist Ihre Azure Storage-Zielkontoumgebung. Diese drei Werte sind möglich: „AzureCloud“, „AzureUSGovernment“ und „AzureChinaCloud“. Der Standardwert ist „AzureCloud“. Verwenden Sie den Parameter, wenn Sie als Azure-Zielregion Clouds vom Typ „Azure US Government“ oder „Azure China“ verwenden.|

Es wird empfohlen, die Profilerstellung für Ihre VMs länger als 7 Tage durchzuführen. Wenn sich das Muster der Datenänderung innerhalb eines Monats ändert, ist es ratsam, die Profilerstellung in die Woche mit der höchsten Datenänderung zu legen. Die beste Möglichkeit besteht darin, die Profilerstellung 31 Tage lang durchzuführen, um eine bessere Empfehlung zu erhalten. Während des Zeitraums der Profilerstellung wird „ASRDeploymentPlanner.exe“ weiter ausgeführt. Im Tool wird der Zeitraum für die Profilerstellung in Tagen eingegeben. Sie können die Profilerstellung einige Stunden oder Minuten lang durchführen, um kurz das Tool bzw. den Ablauf (Proof of Concept) zu testen. Die kürzeste zulässige Dauer für die Profilerstellung beträgt 30 Minuten. 

Während der Profilerstellung können Sie optional den Namen eines Speicherkontos und den dazugehörigen Schlüssel übergeben, um den Durchsatz zu ermitteln, der für Azure Site Recovery bei der Replikation vom Hyper-V-Server zu Azure erreicht werden kann. Wenn der Name des Speicherkontos und der Schlüssel während der Profilerstellung nicht übergeben werden, wird der erreichbare Durchsatz vom Tool nicht berechnet.

Sie können mehrere Instanzen des Tools für verschiedene Gruppen von VMs ausführen. Stellen Sie sicher, dass die VM-Namen in den Gruppen für die Profilerstellung nicht mehr als einmal vorkommen. Wenn Sie beispielsweise Profile für zehn VMs (VM1 bis VM10) erstellt haben und nach einigen Tagen Profile für fünf weitere VMs (VM11 bis VM15) erstellen möchten, können Sie das Tool für die zweite Gruppe von VMs (VM11 bis VM15) über eine andere Befehlszeilenkonsole ausführen. Stellen Sie hierbei sicher, dass die zweite Gruppe von VMs keine Namen der VMs aus der ersten Profilerstellungsinstanz enthält, oder verwenden Sie für die zweite Ausführung ein anderes Ausgabeverzeichnis. Wenn zwei Instanzen des Tools für die Profilerstellung derselben VMs verwendet werden und dabei dasselbe Ausgabeverzeichnis genutzt wird, ist der generierte Bericht fehlerhaft. 

VM-Konfigurationen werden zu Beginn des Profilerstellungsvorgangs einmal erfasst und in einer Datei mit dem Namen „VMDetailList.xml“ gespeichert. Diese Informationen werden für die Berichterstellung verwendet. Alle Änderungen der VM-Konfiguration (z.B. erhöhte Anzahl von Kernen, Datenträgern oder NICs) vom Anfang bis zum Ende der Profilerstellung werden nicht erfasst. Wenn sich die Konfiguration einer VM während der Profilerstellung geändert hat, können Sie diese Problemumgehung nutzen, um bei der Berichterstellung die aktuellen VM-Details abzurufen:

* Sichern Sie die Datei „VMdetailList.xml“, und löschen Sie die Datei an ihrem aktuellen Speicherort.
* Übergeben Sie die Argumente „-User“ und „-Password“ während der Berichterstellung.

Mit dem Befehl für die Profilerstellung werden im Verzeichnis der Profilerstellung mehrere Dateien generiert. Löschen Sie keine Dateien, weil sich dies auf die Berichterstellung auswirkt.

### <a name="examples"></a>Beispiele
#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Beispiel 1: VM-Profilerstellung für 30 Tage, Ermittlung des Durchsatzes von lokal zu Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Beispiel 2: VM-Profilerstellung für 15 Tage
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Beispiel 3: VM-Profilerstellung für 60 Minuten zum schnellen Testen des Tools
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Beispiel 4: VM-Profilerstellung für 2 Stunden zu Proof of Concept-Zwecken
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

>[NOTE!]
>
* Wenn der Server, auf dem das Tool ausgeführt wird, neu gestartet wird oder abstürzt oder wenn Sie das Tool mit STRG+C schließen, werden die Profilerstellungsdaten beibehalten. Es kann aber sein, dass die letzten 15 Minuten der Daten für die Profilerstellung fehlen. Führen Sie das Tool in diesem Fall im Profilerstellungsmodus aus, nachdem der Server neu gestartet wurde.
* Wenn der Name des Speicherkontos und der dazugehörige Schlüssel übergeben werden, misst das Tool den Durchsatz im letzten Schritt der Profilerstellung. Falls das Tool geschlossen wird, bevor die Profilerstellung abgeschlossen ist, wird der Durchsatz nicht berechnet. Zur Ermittlung des Durchsatzes vor dem Generieren des Berichts können Sie den GetThroughput-Vorgang über die Befehlszeilenkonsole ausführen. Andernfalls sind im Bericht keine Informationen zum Durchsatz enthalten.
* Azure Site Recovery unterstützt keine VMs, die über iSCSI- und Pass-Through-Datenträger verfügen. Das Tool kann aber keine iSCSI- und Pass-Through-Datenträger erkennen, die an VMs angefügt sind, und auch keine Profilerstellung dafür durchführen.

## <a name="generate-report"></a>Erstellen des Berichts
Das Tool generiert eine makrofähige Microsoft Excel-Datei (XLSM-Datei) als Berichtsausgabe, in der alle Empfehlungen für die Bereitstellung zusammengefasst sind. Der Bericht hat den Namen „DeploymentPlannerReport_<unique numeric identifier>.xlsm“ und wird im angegebenen Verzeichnis gespeichert.
Nach Abschluss der Profilerstellung können Sie das Tool im Berichterstellungsmodus ausführen. 

### <a name="command-line-parameters"></a>Befehlszeilenparameter
Die folgende Tabelle enthält eine Liste mit den erforderlichen und optionalen Toolparametern für die Ausführung im Modus für die Berichterstellung. Das Tool ist für die Szenarien „VMware zu Azure“ und „Hyper-V zu Azure“ gleich. Die folgenden Parameter gelten für Hyper-V:
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| Parametername | Beschreibung |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | Die Datei mit der Liste mit den VMs für die Profilerstellung, für die der Bericht erstellt werden soll. Der Dateipfad kann absolut oder relativ sein. Für Hyper-V ist diese Datei die Ausgabedatei des Vorgangs GetVMList. Falls Sie die Vorbereitung manuell durchführen, sollte die Datei einen Servernamen oder eine IP-Adresse gefolgt vom VM-Namen mit dem Trennzeichen „\“ pro Zeile enthalten. Der in der Datei angegebene VM-Name sollte dem VM-Namen auf dem Hyper-V-Host entsprechen.<ul>Beispiel: Die Datei „VMList.txt“ enthält die folgenden VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Geben Sie den Virtualisierungstyp an (VMware oder Hyper-V).|
|-Directory|(Optional) Der UNC-Pfad (Universal Naming Convention) oder lokale Verzeichnispfad des Speicherorts, an dem die Daten der Profilerstellung (während der Profilerstellung generierte Dateien) gespeichert werden. Diese Daten werden für die Erstellung des Berichts benötigt. Wenn kein Name angegeben wird, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
| -User | (Optional) Benutzername zum Herstellen der Verbindung mit dem Hyper-V-Host oder Hyper-V-Cluster. Der Benutzer muss über Administratorzugriff verfügen.<br>Der Benutzername und das Kennwort dienen zum Abrufen der aktuellen Konfigurationsinformationen von VMs, z.B. Anzahl von Datenträgern, Anzahl von Kernen, Anzahl von NICs usw., für die Verwendung im Bericht. Wenn keine Angaben gemacht werden, werden die Konfigurationsinformationen verwendet, die während der Profilerstellung gesammelt wurden.|
|-Password|(Optional) Das Kennwort zum Herstellen der Verbindung mit dem Hyper-V-Host. Wenn Sie es hier nicht angeben, werden Sie später während der Ausführung des Befehls zur Eingabe aufgefordert.|
| -DesiredRPO | (Optional) Der gewünschte RPO-Wert (Recovery Point Objective) in Minuten. Der Standardwert ist 15 Minuten.|
| -Bandwidth | (Optional) Bandbreite in MBit/s. Der Parameter wird zum Berechnen des RPO-Werts verwendet, der für die angegebene Bandbreite erzielt werden kann. |
| -StartDate | (Optional) Das Startdatum und die Uhrzeit im Format MM-TT-JJJJ:HH:MM (24-Stunden-Format). *StartDate* muss zusammen mit *EndDate* angegeben werden. Wenn „StartDate“ angegeben ist, wird der Bericht für die Profilerstellungsdaten erstellt, die zwischen „StartDate“ und „EndDate“ erfasst wurden. |
| -EndDate | (Optional) Das Enddatum und die Uhrzeit im Format MM-TT-JJJJ:HH:MM (24-Stunden-Format). *EndDate* muss zusammen mit *StartDate* angegeben werden. Wenn „EndDate“ angegeben ist, wird der Bericht für die Profilerstellungsdaten erstellt, die zwischen „StartDate“ und „EndDate“ erfasst wurden. |
| -GrowthFactor | (Optional) Der Zuwachsfaktor als Prozentsatz. Der Standardwert ist 30 Prozent. |
| -UseManagedDisks | (Optional) UseManagedDisks – Ja/Nein. Die Standardeinstellung ist „Ja“. Für Anzahl der virtuellen Computer in einer einzelnen Speicherkontoplatzierung wird bei der Berechnung berücksichtigt, dass das Failover bzw. Testfailover von virtuellen Computern auf einem verwalteten Datenträger statt auf einem nicht verwalteten Datenträger erfolgt. |
|-SubscriptionId |(Optional) Die GUID für das Abonnement. Verwenden Sie diesen Parameter, um den Bericht zur Kostenvorkalkulation mit dem aktuellen Preis zu generieren, der auf Ihrem Abonnement, dem Angebot zu Ihrem Abonnement und Ihrer jeweiligen Azure-Zielregion in der gewählten Währung basiert.|
|-TargetRegion|(Optional) Die Azure-Zielregion für die Replikation. Verwenden Sie diesen Parameter, um einen Bericht mit der jeweiligen Azure-Zielregion zu erstellen, da Azure über unterschiedliche Kosten pro Region verfügt.<br>Die Standardeinstellung ist „USA, Westen 2“ bzw. die zuletzt verwendete Zielregion.<br>Weitere Informationen finden Sie in der Liste mit den [unterstützten Zielregionen](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Optional) Das Angebot, das dem jeweiligen Abonnement zugeordnet ist. Die Standardeinstellung ist MS-AZR-0003P (nutzungsbasierte Bezahlung).|
|-Currency|(Optional) Die Währung, in der Kosten im generierten Bericht angezeigt werden. Die Standardeinstellung ist „US-Dollar ($)“ bzw. die zuletzt verwendete Währung.<br>Weitere Informationen finden Sie in der Liste mit den [unterstützten Währungen](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

### <a name="examples"></a>Beispiele
#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Beispiel 1: Berichterstellung mit Standardwerten, wenn sich die Profilerstellungsdaten auf dem lokalen Laufwerk befinden
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Beispiel 2: Berichterstellung, wenn sich die Profilerstellungsdaten auf einem Remoteserver befinden
Sie sollten über Lese-/Schreibzugriff für das Remoteverzeichnis verfügen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V - -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-that-you-will-be-provision-for-the-replication"></a>Beispiel 3: Berichterstellung mit einer bestimmten Bandbreite, die Sie für die Replikation bereitstellen
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Beispiel 4: Berichterstellung mit Zuwachsfaktor von 5 Prozent (anstelle der Standardeinstellung von 30 Prozent) 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Beispiel 5: Berichterstellung mit einer Teilmenge der Profilerstellungsdaten
Es kann beispielsweise sein, dass Sie über Profilerstellungsdaten für 30 Tage verfügen und den Bericht nur für 20 Tage erstellen möchten.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minutes-rpo"></a>Beispiel 6: Berichterstellung für RPO von 5 Minuten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Beispiel 7: Berichterstellung für die Azure-Region „Indien, Süden“ mit Indischer Rupie und spezifischer Angebots-ID
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Für die Berechnung verwendeter Perzentilwert
**Welcher Perzentilstandardwert der Leistungsmetriken, die während der Profilerstellung gesammelt wurden, wird vom Tool für die Berichterstellung verwendet?**

Für das Tool werden standardmäßig die Werte des 95. Perzentils für Lese/Schreib-IOPS, Schreib-IOPS und die Datenänderung verwendet, die während der Profilerstellung aller VMs gesammelt wurden. Mit dieser Metrik wird sichergestellt, dass Spitzen (100. Perzentil), die für Ihre VMs aufgrund von temporären Ereignissen auftreten können, nicht zur Ermittlung des Bandbreitenbedarfs für Ihr Zielspeicherkonto und Ihre Quelle herangezogen werden. Ein temporäres Ereignis kann beispielsweise ein Sicherungsauftrag sein, der einmal pro Tag ausgeführt wird, oder eine regelmäßige Datenbankindizierung, eine Aktivität zur Erstellung eines Analyseberichts oder ähnliche Point-in-Time-Ereignisse mit kurzer Lebensdauer.

Bei Verwendung der Werte des 95. Perzentils erhalten Sie ein genaues Bild der echten Workloadmerkmale und erzielen die beste Leistung, wenn die Workloads in Azure ausgeführt werden. Dieser Wert muss normalerweise nicht geändert werden. Falls Sie den Wert doch ändern möchten (z.B. in das 90. Perzentil), können Sie die Konfigurationsdatei „ASRDeploymentPlanner.exe.config“ im Standardordner aktualisieren und speichern, um einen neuen Bericht für die vorhandenen Profilerstellungsdaten zu erstellen.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Informationen zum Zuwachsfaktor
**Warum sollte ich den Zuwachsfaktor beim Planen von Bereitstellungen berücksichtigen?**
Es ist wichtig, dass Sie in Bezug auf Ihre Workloadmerkmale den Zuwachs und eine potenzielle vermehrte Nutzung im Laufe der Zeit berücksichtigen. Wenn sich Ihre Workloadmerkmale nach der Einrichtung des Schutzes ändern, können Sie in Bezug auf den Schutz nicht zu einem anderen Speicherkonto wechseln, ohne den Schutz zu deaktivieren und anschließend wieder zu aktivieren.

Angenommen, Ihre VM ist derzeit für ein Standardspeicherkonto für die Replikation eingerichtet. Die Wahrscheinlichkeit ist hoch, dass es im Laufe der nächsten drei Monate zu mehreren Änderungen kommt:

* Die Anzahl von Benutzern der Anwendung, die auf der VM ausgeführt wird, erhöht sich.
* Aufgrund der sich daraus ergebenden erhöhten Datenänderungsrate auf der VM muss die VM auf Storage Premium umgestellt werden, damit die Azure Site Recovery-Replikation Schritt halten kann.
* Sie müssen also den Schutz deaktivieren und für ein Storage Premium-Konto wieder aktivieren.

Wir empfehlen Ihnen dringend, bei der Bereitstellungsplanung den möglichen Zuwachs zu berücksichtigen. Der Standardwert liegt zwar bei 30 Prozent, aber Sie kennen das Nutzungsmuster Ihrer Anwendungen und den projizierten Zuwachs am besten und können diese Angabe beim Erstellen des Berichts entsprechend ändern. Außerdem haben Sie die Möglichkeit, mehrere Berichte mit unterschiedlichen Zuwachsfaktoren für dieselben Profilerstellungsdaten zu erstellen, um zu ermitteln, welche Empfehlungen für die Speicherzielbandbreite und die Quellbandbreite für Sie am besten geeignet sind. 

Der erstellte Microsoft Excel-Bericht enthält die folgenden Informationen:

* [On-premises Summary](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-summary) (Lokale Zusammenfassung)
* [Empfehlungen](site-recovery-hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM&lt;-&gt;Storage Placement](site-recovery-hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) (VM/Speicher-Anordnung)
* [Compatible VMs](site-recovery-hyper-v-deployment-planner-analyze-report.md#compatible-vms) (Kompatible VMs)
* [Incompatible VMs](site-recovery-hyper-v-deployment-planner-analyze-report.md#incompatible-vms) (Inkompatible VMs)
* [On-premises Storage Requirement](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement) (Bedarf an lokalem Speicher)
* [IR Batching](site-recovery-hyper-v-deployment-planner-analyze-report.md#initial-replication-batching) (IR-Batchverarbeitung)
* [Cost Estimation](site-recovery-hyper-v-deployment-planner-cost-estimation.md) (Kostenvorkalkulation)

![Bereitstellungsplaner-Bericht](media/site-recovery-hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Durchsatzberechnung
Führen Sie das Tool im GetThroughput-Modus aus, um den Durchsatz zu schätzen, der von Azure Site Recovery während der Replikation von der lokalen Umgebung zu Azure erreicht werden kann. Das Tool berechnet den Durchsatz für den Server, auf dem das Tool ausgeführt wird. Im Idealfall ist dieser Server der Hyper-V-Server, dessen VMs geschützt werden sollen. 

### <a name="command-line-parameters"></a>Befehlszeilenparameter 
Öffnen Sie eine Befehlszeilenkonsole, und wechseln Sie in den Ordner mit dem Azure Site Recovery-Tool für die Bereitstellungsplanung. Führen Sie die Datei „ASRDeploymentPlanner.exe“ mit den unten angegebenen Parametern aus.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 Parametername | Beschreibung |
|---|---|
| -Operation | GetThroughtput |
|-Virtualization|Geben Sie den Virtualisierungstyp an (VMware oder Hyper-V).|
|-Directory|(Optional) Der UNC-Pfad (Universal Naming Convention) oder lokale Verzeichnispfad des Speicherorts, an dem die Daten der Profilerstellung (während der Profilerstellung generierte Dateien) gespeichert werden. Diese Daten werden für die Erstellung des Berichts benötigt. Wenn kein Name angegeben wird, wird das Verzeichnis mit dem Namen „ProfiledData“ unter dem aktuellen Pfad als Standardverzeichnis verwendet.|
| -StorageAccountName | Der Name des Speicherkontos zur Ermittlung der Bandbreite, die für die Replikation von Daten aus der lokalen Umgebung in Azure verbraucht wird. Mit dem Tool werden Testdaten in dieses Speicherkonto hochgeladen, um die verbrauchte Bandbreite zu ermitteln. |
| -StorageAccountKey | Der Schlüssel des Speicherkontos, der zum Zugreifen auf das Speicherkonto verwendet wird. Navigieren Sie im Azure-Portal zu „Speicherkonten“ > <*Name des Speicherkontos*> > Einstellungen > Zugriffsschlüssel > Key1.|
| -VMListFile | Die Datei mit der Liste der VMs, für die Profile erstellt werden sollen, um die verbrauchte Bandbreite zu berechnen. Der Dateipfad kann absolut oder relativ sein. Für Hyper-V ist diese Datei die Ausgabedatei des Vorgangs GetVMList. Falls Sie die Vorbereitung manuell durchführen, sollte die Datei einen Servernamen oder eine IP-Adresse gefolgt vom VM-Namen mit dem Trennzeichen „\“ pro Zeile enthalten. Der in der Datei angegebene VM-Name sollte dem VM-Namen auf dem Hyper-V-Host entsprechen.<ul>Beispiel: Die Datei „VMList.txt“ enthält die folgenden VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Optional) Dies ist Ihre Azure Storage-Zielkontoumgebung. Diese drei Werte sind möglich: „AzureCloud“, „AzureUSGovernment“ und „AzureChinaCloud“. Der Standardwert ist „AzureCloud“. Verwenden Sie den Parameter, wenn Sie als Azure-Zielregion Clouds vom Typ „Azure US Government“ oder „Azure China“ nutzen.|


Mit dem Tool werden im angegebenen Verzeichnis mehrere Dateien vom Typ „asrvhdfile<#>.vhd“ (wobei „#“ für eine Zahl steht) mit 64 MB erstellt. Mit dem Tool werden diese Dateien in das Speicherkonto hochgeladen, um den Durchsatz zu ermitteln. Nach dem Messen des Durchsatzes löscht das Tool diese Dateien aus dem Speicherkonto und vom lokalen Server. Wenn das Tool aus irgendeinem Grund beendet wird, während der Durchsatz berechnet wird, werden die Dateien nicht aus dem Speicher oder vom lokalen Server gelöscht. Sie müssen sie manuell löschen.

Der Durchsatz wird zu einem angegebenen Zeitpunkt gemessen. Hierbei handelt es sich um den maximalen Durchsatz, der von Azure Site Recovery während der Replikation erreicht werden kann, wenn alle anderen Faktoren gleich bleiben. Falls eine Anwendung beispielsweise in demselben Netzwerk auf einmal mehr Bandbreite verbraucht, variiert der tatsächliche Durchsatz während der Replikation. Das Ergebnis des gemessenen Durchsatzes ist anders, wenn der GetThroughput-Vorgang ausgeführt wird, während die geschützten VMs über eine hohe Datenänderungsrate verfügen. Wir empfehlen Ihnen, das Tool zu unterschiedlichen Zeiten während der Profilerstellung auszuführen, um zu verstehen, welche Durchsatzstufen jeweils erreicht werden können. Im Bericht wird vom Tool der zuletzt gemessene Durchsatz angezeigt.
    
### <a name="example"></a>Beispiel
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

[NOTE!]
>
> Führen Sie das Tool auf einem Server aus, der über die gleichen Speicher- und CPU-Merkmale wie ein Hyper-V-Server verfügt.
>
> Legen Sie die empfohlene Bandbreite für die Replikation so fest, dass der RPO-Wert in 100 Prozent der Fälle erreicht wird. Nach dem Festlegen der passenden Bandbreite sollten Sie wie folgt vorgehen, falls vom Tool kein Anstieg des erzielten Durchsatzes gemeldet wird:
>
>  1. Überprüfen Sie, ob der Azure Site Recovery-Durchsatz durch die Dienstqualität (Quality of Service, QoS) des Netzwerks eingeschränkt wird.
>
>  2. Überprüfen Sie, ob sich Ihr Azure Site Recovery-Tresor in der nächstgelegenen physisch unterstützten Microsoft Azure-Region befindet, um die Netzwerkwartezeit zu verringern.
>
>  3. Überprüfen Sie Ihre lokalen Speichermerkmale, um zu ermitteln, ob Sie die Hardware (z.B. Wechsel von HDD auf SSD) verbessern können.
>

## <a name="next-steps"></a>Nächste Schritte
* [Analysieren des generierten Berichts](site-recovery-hyper-v-deployment-planner-analyze-report.md)