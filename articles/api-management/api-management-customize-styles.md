---
title: Anpassen des Seitenstils im Azure API Management-Entwicklerportal | Microsoft-Dokumentation
description: "Führen Sie die Schritte in dieser Schnellstartanleitung aus, um den Stil von Elementen im Azure API Management-Entwicklerportal anzupassen."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: f427663ba1c437785c8c521925d9f733c45cb40d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2017
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Anpassen des Stils der Seiten im Entwicklerportal

Es gibt drei sehr häufig verwendete Möglichkeiten, das Entwicklerportal in Azure API Management anzupassen:
 
* [Bearbeiten des Inhalts von statischen Seiten und Seitenlayoutelementen](api-management-modify-content-layout.md)
* Aktualisieren der Stile, die für Seitenelemente im gesamten Entwicklerportal verwendet werden (in diesem Leitfaden beschrieben)
* [Ändern der Vorlagen, die für vom Portal generierte Seiten verwendet werden](api-management-developer-portal-templates.md) (z.B. API-Dokumente, Produkte, Benutzerauthentifizierung)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Anpassen des Stils von Elementen auf Seiten im **Entwicklerportal**
> * Anzeigen der Änderungen

![Anpassen des Stils](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Voraussetzungen

+ Absolvieren Sie das folgende Schnellstarttutorial: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).
+ Schließen Sie darüber hinaus das folgende Tutorial ab: [Import and publish your first API](import-and-publish.md) (Importieren und Veröffentlichen Ihrer ersten API).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="customize-the-developer-portal"></a>Anpassen des Entwicklerportals

1. Wählen Sie **Übersicht**.
2. Klicken Sie oben im Fenster **Übersicht** auf die Schaltfläche **Entwicklerportal**. Alternativ können Sie auf den Link **Entwicklerportal-URL** klicken.
3. Oben links auf dem Bildschirm wird ein aus zwei Pinseln bestehendes Symbol angezeigt. Zeigen Sie auf dieses Symbol, um das Menü für die Portalanpassung zu öffnen.

    ![Anpassen des Stils](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Klicken Sie im Menü auf **Stile**, um den Bereich für die Anpassung des Stils zu öffnen.

    Alle Elemente, die über **Stile** angepasst werden können, werden auf der Seite angezeigt.
5. Geben Sie im Feld **Change variable values to customize developer portal appearance:** (Variablenwerte ändern, um die Darstellung des Entwicklerportals anzupassen) die Variable „headings-color“ ein.

    Das Element **@headings-color** wird auf der Seite angezeigt. Diese Variable legt die Farbe des Texts fest.

    ![Anpassen des Stils](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klicken Sie auf das Feld für die Variable **@headings-color**. 
    
    Die Dropdownliste für die Farbauswahl wird geöffnet.
7. Wählen Sie in der Dropdownliste für die Farbauswahl eine neue Farbe aus.

    > [!TIP]
    > Für alle Änderungen ist eine Echtzeitvorschau verfügbar. Oben im Anpassungsbereich wird eine Statusanzeige angezeigt. Nach einigen Sekunden wird die Farbe des Überschriftentexts in die neu ausgewählte Farbe geändert.

8. Klicken Sie unten links im Menü des Anpassungsbereich auf **Veröffentlichen**.
9. Klicken Sie auf **Anpassungen veröffentlichen**, um die Änderungen öffentlich verfügbar zu machen.

## <a name="view-your-change"></a>Anzeigen der Änderungen

1. Navigieren Sie zum Entwicklerportal.
2. Sie können die vorgenommenen Änderung hier sehen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Anpassen des Stils von Elementen auf Seiten im **Entwicklerportal**
> * Anzeigen der Änderungen

> [!div class="nextstepaction"]
> [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md)