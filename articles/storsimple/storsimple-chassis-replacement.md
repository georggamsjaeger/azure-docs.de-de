---
title: "Ersetzen des Gehäuses eines Geräts der StorSimple 8000-Serie | Microsoft-Dokumentation"
description: "Beschreibt das Entfernen und Ersetzen des primären StorSimple- oder EBOD-Gehäuses."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 537659ed-4c46-49c1-b1e4-186262f2542d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 7bc23e64331ad18c604ffaa29476766827119cd4
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2017
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Ersetzen des Gehäuses des StorSimple-Geräts
> [!NOTE]
> Das klassische Portal für StorSimple ist veraltet. Ihre StorSimple-Geräte-Manager werden gemäß dem Zeitplan für die Abschaltung automatisch in das neue Azure-Portal verschoben. Sie erhalten zu dieser Verschiebung eine E-Mail und eine Portalbenachrichtigung. Dieses Dokument wird ebenfalls bald entfernt. Den Artikel zum neuen Azure-Portal finden Sie unter [Ersetzen des Gehäuses des StorSimple-Geräts](storsimple-8000-chassis-replacement.md). Antworten auf Fragen zu dieser Verschiebung finden Sie unter [FAQ: Move to Azure portal (Verschieben in das Azure-Portal: häufig gestellte Fragen (FAQ))](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Übersicht
In diesem Tutorial wird erläutert, wie Sie das Gehäuse eines StorSimple-Geräts der 8000-Serie austauschen oder entfernen. Das StorSimple-Modell 8100 ist ein Gerät mit einem einzelnen Gehäuse, das Modell 8600 ein Gerät mit zwei Gehäusen. Beim Modell 8600 können potenziell zwei Gehäuse im Gerät ausfallen: das primäre Gehäuse oder das EBOD-Gehäuse.

In beiden Fällen wird ein leeres Ersatzgehäuse von Microsoft ausgeliefert. Die Gehäuse enthalten keine Stromversorgungs- und Kühleinheiten (PCMs), Controllermodule, SSDs (Solid-State-Festplatten), HDDs (Festplattenlaufwerke) oder EBOD-Module.

> [!IMPORTANT]
> Vor dem Entfernen und Austauschen des Gehäuses überprüfen Sie die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="remove-the-chassis"></a>Entfernen des Gehäuses
Führen Sie die folgenden Schritte aus, um das Gehäuse des StorSimple-Geräts zu entfernen.

#### <a name="to-remove-a-chassis"></a>So entfernen Sie ein Gehäuse
1. Stellen Sie sicher, dass das StorSimple-Gerät ausgeschaltet und von der Stromversorgung getrennt ist.
2. Entfernen Sie ggf. alle Netzwerk- und SAS-Kabel.
3. Entfernen Sie die Einheit aus dem Rack.
4. Entfernen Sie alle Laufwerke, und notieren Sie sich die Steckplätze, aus denen sie entfernt werden. Weitere Informationen finden Sie unter [Entfernen der Festplatte](storsimple-disk-drive-replacement.md#remove-the-disk-drive).
5. Entfernen Sie die EBOD-Controllermodule aus dem EBOD-Gehäuse (falls dies das fehlerhafte Gehäuse ist). Weitere Informationen finden Sie unter [Entfernen eines EBOD-Controllers](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 
   
    Entfernen Sie die Controller aus dem primären Gehäuse (falls dies das fehlerhafte Gehäuse ist), und notieren Sie sich die Steckplätze, aus denen sie entfernt werden. Weitere Informationen finden Sie unter [Entfernen eines Controllers](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installieren des Gehäuses
Führen Sie die folgenden Schritte aus, um das Gehäuse in Ihrem StorSimple-Gerät zu installieren.

#### <a name="to-install-a-chassis"></a>So installieren Sie ein Gehäuse
1. Installieren Sie das Gehäuse im Rack. Weitere Informationen finden Sie unter [Einbauen des StorSimple 8100-Geräts in ein Rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) oder [Einbauen des StorSimple 8600-Geräts in ein Rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Nachdem Sie das Gehäuse in das Rack eingebaut haben, installieren Sie die Controllermodule an den gleichen Positionen, an denen sie sich zuvor befanden.
3. Installieren Sie die Laufwerke an den gleichen Positionen und Steckplätzen, an denen sie zuvor installiert waren.
   
   > [!NOTE]
   > Wir empfehlen, dass Sie zuerst die SSDs installieren und dann die Festplatten.
   > 
   > 
4. Nach dem Einbau des Geräts und der Installation der Komponenten verbinden Sie das Gerät mit den entsprechenden Stromquellen, und schalten Sie es ein. Weitere Informationen finden Sie unter [Verkabeln des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) bzw. [Verkabeln des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-hardware-component-replacement.md).

