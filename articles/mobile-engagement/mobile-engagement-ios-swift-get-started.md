---
title: "Erste Schritte mit Azure Mobile Engagement für iOS in Swift | Microsoft Docs"
description: "Erfahren Sie mehr über die Verwendung von Azure Mobile Engagement mit Analysefunktionen und Pushbenachrichtigungen für iOS-Apps."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Erste Schritte mit Azure Mobile Engagement für iOS-Apps in Swift
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Engagement die Nutzung Ihrer App verstehen und Pushbenachrichtigungen an segmentierte Benutzer einer iOS-Anwendung senden können.
In diesem Lernprogramm erstellen Sie eine leere iOS-App, die einfache Daten erfasst und Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts (APNS) empfängt.

Für dieses Lernprogramm ist Folgendes erforderlich:

* Xcode 8 (Installation über MAC App Store)
* Das [Mobile Engagement iOS SDK]
* Ein Pushbenachrichtigungszertifikat (P12), das Sie im Apple Dev Center abrufen können.

> [!NOTE]
> Dieses Tutorial verwendet Swift Version 3.0. 
> 
> 

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Engagement-Lernprogramme für iOS-Apps.

> [!NOTE]
> Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).
> 
> 

## <a id="setup-azme"></a>Einrichten von Mobile Engagement für Ihre iOS-App
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End
In diesem Lernprogramm wird eine "einfache Integration" dargestellt. Dabei handelt es sich um den minimalen erforderlichen Satz zur Sammlung von Daten und zum Senden einer Pushbenachrichtigung. Die vollständige Dokumentation zur Integration finden Sie im Artikel [Integrieren von Mobile Engagement unter iOS](mobile-engagement-ios-sdk-overview.md).

Wir erstellen eine einfache App mit Xcode, um die Integration zu veranschaulichen:

### <a name="create-a-new-ios-project"></a>Erstellen eines neuen iOS-Projekts
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Verbinden Sie Ihre App mit dem Mobile Engagement-Back-End.
1. Laden Sie das [Mobile Engagement iOS SDK]
2. Extrahieren Sie die „.tar.gz“-Datei in einem Ordner auf Ihrem Computer.
3. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie „Add files to ...“ aus.
   
    ![][1]
4. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben, und wählen Sie den Ordner `EngagementSDK` aus. Anschließend drücken Sie auf „OK“.
   
    ![][2]
5. Öffnen Sie die Registerkarte `Build Phases` und fügen Sie im Menü `Link Binary With Libraries` Frameworks wie folgt hinzu:
   
    ![][3]
6. Erstellen Sie einen Bridging-Header, um die Objective-C-APIs des SDK verwenden zu können. Wählen Sie hierzu "Datei" > "Neu" > "Datei" > "iOS" > "Quelle" > "Headerdatei" aus.
   
    ![][4]
7. Bearbeiten Sie die Bridging-Headerdatei, um Mobile Engagement-Objective-C-Code für den Swift-Code verfügbar zu machen, und fügen Sie die folgenden Importanweisungen hinzu:
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. Stellen Sie unter "Buildeinstellungen" sicher, dass die Buildeinstellung "Objective-C Bridging Header" unter "Swift Compiler – Code Generation" einen Pfad zu diesem Header aufweist. Der Pfad kann beispielsweise wie folgt lauten: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (je nach Pfad)**
   
   ![][6]
9. Wechseln Sie zurück zum Azure-Portal auf der Seite *Verbindungsinformationen* Ihrer App und kopieren Sie die Verbindungszeichenfolge.
   
   ![][5]
10. Fügen Sie die Verbindungszeichenfolge in den `didFinishLaunchingWithOptions` Delegaten ein.
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>Aktivieren der Überwachung in Echtzeit
Um mit dem Senden von Daten zu beginnen und sicherzustellen, dass die Benutzer aktiv sind, müssen Sie mindestens einen Bildschirm (Aktivität) an das Mobile Engagement-Back-End schicken.

1. Öffnen Sie die Datei **ViewController.swift**, und ersetzen Sie die Basisklasse von **ViewController** durch **EngagementViewController**:
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>Verbinden der App mit Überwachung in Echtzeit
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Aktivieren von Pushbenachrichtigungen und In-App-Messaging
Mit Mobile Engagement können Sie mit Ihren Benutzern interagieren und diese mit Push-Benachrichtigungen und In-App-Nachrichten im Rahmen von Kampagnen ERREICHEN. Dieses Modul nennt sich REACH im Mobile Engagement-Portal.
In den folgenden Abschnitten wird Ihre App für den Empfang eingerichtet.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Aktivieren der App für den Empfang von stillen Pushbenachrichtigungen
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Fügen Sie die Reach-Bibliothek zum Projekt hinzu.
1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt.
2. Wählen Sie `Add file to ...`.
3. Navigieren Sie zu dem Ordner, in dem Sie das SDK extrahiert haben.
4. Wählen Sie den Ordner `EngagementReach`.
5. Klicken Sie auf "Hinzufügen".
6. Bearbeiten Sie die Bridging-Headerdatei, um Mobile Engagement-Objective-C-Reach-Header verfügbar zu machen, und fügen Sie die folgenden Importanweisungen hinzu:
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Ändern des Anwendungsdelegaten
1. Erstellen Sie innerhalb von `didFinishLaunchingWithOptions` ein Reichweitenmodul, und übergeben Sie es an die vorhandene Engagement-Initialisierungszeile:
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Aktivieren Ihrer App für den Empfang von APNS-Pushbenachrichtigungen.
1. Fügen Sie die folgende Zeile in die `didFinishLaunchingWithOptions` Methode ein:
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. Fügen Sie die `didRegisterForRemoteNotificationsWithDeviceToken`-Methode wie folgt hinzu:
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. Fügen Sie die `didReceiveRemoteNotification:fetchCompletionHandler:` -Methode wie folgt hinzu:
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
