---
title: Integration des Azure Mobile Engagement iOS SDK | Microsoft Docs
description: "Neueste Updates und Verfahren für das iOS-SDK für Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 947ea44b-00c1-450f-9a3b-74437954dc56
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 01fdbb43c21ac6932e8462f4a6507fc63e50542d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-on-ios"></a>Integrieren von Mobile Engagement unter iOS
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
>
>

In diesem Verfahren wird die einfachste Art der Aktivierung der Analyse- und Überwachungsfunktionen von Mobile Engagement in Ihrer iOS-Anwendung beschrieben.

Das Engagement SDK erfordert mindestens iOS7+ und Xcode 8+: Das Bereitstellungsziel Ihrer Anwendung muss mindestens iOS 7 sein.

> [!NOTE]
> Wenn Sie auf XCode 7 tatsächlich nicht verzichten können, bietet sich das [iOS Engagement SDK 3.2.4](https://aka.ms/r6oouh)an. Im Reichweitenmodul dieser Vorgängerversion tritt bei Ausführung auf iOS 10-Geräten ein bekannter Fehler auf. Weitere Details finden Sie unter [Integration des Reichweitenmoduls](mobile-engagement-ios-integrate-engagement-reach.md). Wenn Sie das SDK mit der Version 3.2.4 nutzen, können Sie das Importieren von `UserNotifications.framework` im nächsten Schritt überspringen.
>
>

Die folgenden Schritte sind ausreichend, um den Bericht von Protokollen zu aktivieren, die zur Berechnung aller Statistiken zu Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Informationen notwendig sind. Der Bericht von Protokollen, die zur Berechnung anderer Statistiken wie Ereignisse, Fehler und Aufträge erforderlich sind, muss manuell mithilfe der Engagement-API erfolgen (siehe [Verwenden der Engagement-API auf iOS](mobile-engagement-ios-use-engagement-api.md)), da diese Statistiken von der Anwendung abhängig sind.

## <a name="embed-the-engagement-sdk-into-your-ios-project"></a>Einbetten des Engagement-SDKs in Ihr iOS-Projekt
* Laden Sie das iOS-SDK [hier](http://aka.ms/qk2rnj)herunter.
* Fügen Sie das Engagement-SDK zum iOS-Projekt hinzu. Klicken Sie in Xcode mit der rechten Maustaste auf das Projekt, und wählen Sie **„Add files to ...“** und anschließend den Ordner `EngagementSDK` aus.
* Engagement erfordert, dass zusätzliche Frameworks funktionieren: Öffnen Sie im Projektexplorer Ihren Projektbereich, und wählen Sie das richtige Ziel aus. Öffnen Sie dann die Registerkarte **„Build phases“**. Fügen Sie anschließend im Menü **„Link Binary With Libraries“** die folgenden Frameworks hinzu:

  * `UserNotifications.framework` – Legen Sie den Link als `Optional` fest.
  * `AdSupport.framework` – Legen Sie den Link als `Optional` fest.
  * `SystemConfiguration.framework`
  * `CoreTelephony.framework`
  * `CFNetwork.framework`
  * `CoreLocation.framework`
  * `libxml2.dylib`

> [!NOTE]
> Das AdSupport-Framework kann entfernt werden. Dieses Framework ist für Engagement zum Erfassen der IDFA erforderlich. Die IDFA-Erfassung kann jedoch mit \<ios-sdk-engagement-idfa\> deaktiviert werden, um der neuen Apple-Richtlinie zu dieser ID zu entsprechen.
>
>

## <a name="initialize-the-engagement-sdk"></a>Initialisieren des Engagement-SDK
Sie müssen Ihre Anwendungsstellvertretung ändern:

* Importieren Sie am Anfang der Implementierungsdatei den Engagement-Agent.

      [...]
      #import "EngagementAgent.h"
* Initialisieren Sie Engagement innerhalb der Methode „**applicationDidFinishLaunching:**“ oder „**application:didFinishLaunchingWithOptions:**“:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
      {
        [...]
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
        [...]
      }

## <a name="basic-reporting"></a>Grundlegende Berichterstellung
### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Empfohlene Methode: Überladen der `UIViewController` -Klassen
Um den Bericht für alle Protokolle zu aktivieren, die von Engagement zum Berechnen von Benutzern, Sitzungen, Aktivitäten, Abstürzen und technischen Statistiken erforderlich sind, müssen Sie einfach dafür sorgen, dass all Ihre `UIViewController`-Unterklassen von den entsprechenden `EngagementViewController`-Klassen abgeleitet werden (gleiche Regel für `UITableViewController` -\> `EngagementTableViewController`).

**Ohne Engagement:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Mit Engagement:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternative Methode: Rufen Sie `startActivity()` manuell auf
Wenn Sie die `UIViewController`-Klassen nicht überladen können oder möchten, können Sie die Aktivitäten stattdessen durch direktes Aufrufen der Methoden von `EngagementAgent` starten.

> [!IMPORTANT]
> Das iOS SDK ruft die `endActivity()`-Methode automatisch auf, wenn die Anwendung geschlossen wird. Daher wird *dringend* empfohlen, bei jeder Änderung der Benutzeraktivität die Methode `startActivity` und *niemals* die Methode `endActivity` aufzurufen, da hierdurch die Beendigung der aktuellen Sitzung erzwungen wird.
>
>

## <a name="location-reporting"></a>Speicherortberichte
Die Apple-Nutzungsbedingungen gestatten es nicht, dass Anwendungen die Standortnachverfolgung nur für statistische Zwecke verwenden. Daher wird empfohlen, die Standortberichte nur zu aktivieren, wenn Ihre Anwendung die Standortnachverfolgung auch aus einem anderen Grund verwendet.

Ab iOS 8 müssen Sie eine Beschreibung für die Verwendung der Standortdienste durch Ihre App bereitstellen, indem Sie für den Schlüssel [NSLocationWhenInUseUsageDescription] oder [NSLocationAlwaysUsageDescription] in der Datei „Info.plist“ Ihrer App eine Zeichenfolge festlegen. Wenn Sie mit Engagement im Hintergrund einen Standortbericht erstellen möchten, fügen Sie den Schlüssel „NSLocationAlwaysUsageDescription“ hinzu. In allen anderen Fällen fügen Sie den Schlüssel „NSLocationWhenInUseUsageDescription“ hinzu. Beachten Sie, dass Sie auf iOS 11 für die Berichterstellung im Hintergrund sowohl NSLocationAlwaysAndWhenInUseUsageDescription als auch NSLocationWhenInUseUsageDescription benötigen.

### <a name="lazy-area-location-reporting"></a>Verzögerte Bereichsspeicherortberichte
Die verzögerte Berichterstellung für Bereichsspeicherorte ermöglicht es Ihnen, das Land, die Region und den Ort zu melden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN). Der Gerätebereich wird höchstens einmal pro Sitzung gemeldet. Das GPS wird niemals verwendet, daher hat diese Art von Standortbericht sehr geringe (oder fast keine) Auswirkungen auf den Akku.

Die gemeldeten Bereiche werden dazu verwendet, um geografische Statistiken zu Benutzern, Sitzungen, Ereignissen und Fehlern zu berechnen. Sie können auch als Kriterium für Reach-Kampagnen verwendet werden. Der für ein Gerät zuletzt gemeldete bekannte Bereich kann mithilfe der [Geräte-API]abgerufen werden.

Fügen Sie die folgende Zeile hinter der Initialisierung des Engagement-Agent hinzu, um verzögerte Standortberichte zu aktivieren:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Echtzeit-Berichterstellung für Speicherorte
Mithilfe der Echtzeit-Berichterstellung für Speicherorte können der Längen- und Breitengrad gemeldet werden, die Geräten zugeordnet sind. Diese Art der Berichterstellung für Speicherorte verwendet ausschließlich Netzwerkspeicherorte (auf Basis von Zell-ID oder WLAN), und die Berichterstellung ist nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung).

Echtzeit-Speicherorte werden *NICHT* zum Berechnen von Statistiken verwendet. Ihr einziger Zweck ist es, die Verwendung des Echtzeit-Geofencing \<Reach-Audience-geofencing\>-Kriteriums in Reach-Kampagnen zu ermöglichen.

Fügen Sie die folgende Zeile hinter der Initialisierung des Engagement-Agent hinzu, um die Echtzeit-Berichterstellung für Speicherorte zu aktivieren:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS-basierte Berichterstellung
Die Echtzeit-Berichterstellung für Speicherorte verwendet standardmäßig nur netzwerkbasierte Speicherorte. Fügen Sie Folgendes hinzu, um die Verwendung von GPS-basierten Speicherorten (die viel genauer sind) zu aktivieren:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Berichterstellung im Hintergrund
Die Echtzeit-Berichterstellung für Speicherorte ist standardmäßig nur aktiv, wenn die Anwendung im Vordergrund ausgeführt wird (d. h. während einer Sitzung). Fügen Sie Folgendes hinzu, um die Berichterstellung auch im Hintergrund zu aktivieren:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [!NOTE]
> Wenn die Anwendung im Hintergrund ausgeführt wird, werden nur netzwerkbasierte Speicherorte gemeldet, auch wenn Sie das GPS aktiviert haben.
>
>

Durch die Implementierung dieser Funktion wird [startMonitoringSignificantLocationChanges] aufgerufen, wenn die Anwendung in den Hintergrund wechselt. Beachten Sie, dass Ihre Anwendung im Hintergrund automatisch neu gestartet wird, wenn ein neues Standortereignis eintrifft.

## <a name="advanced-reporting"></a>Erweiterte Berichterstellung
Wenn Sie anwendungsspezifische Ereignisse, Fehler und Aufträge optional melden möchten, müssen Sie die Engagement-API über die Methoden der `EngagementAgent` -Klasse verwenden. Ein Objekt dieser Klasse kann durch Aufrufen der statischen `[EngagementAgent shared]` -Methode abgerufen werden.

Die Engagement-API ermöglicht es, alle erweiterten Funktionen von Engagement zu verwenden. Ausführliche Informationen finden Sie unter „Verwenden der Engagement-API unter iOS (sowie in der technischen Dokumentation der `EngagementAgent`-Klasse).

## <a name="disable-idfa-collection"></a>Deaktivieren der IDFA-Erfassung
Engagement verwendet [IDFA] standardmäßig zum eindeutigen Identifizieren von Benutzern. Wenn Sie nicht an anderer Stelle in der App Werbung verwenden, werden Sie möglicherweise vom App Store-Überprüfungsprozess abgelehnt. Die IDFA-Erfassung kann durch Hinzufügen eines Präprozessormakros `ENGAGEMENT_DISABLE_IDFA` zur PCH-Datei deaktiviert werden (oder in den `Build Settings` Ihrer Anwendung). Dadurch wird sichergestellt, dass im Anwendungsbuild keine Verweise auf `ASIdentifierManager`, `advertisingIdentifier` oder `isAdvertisingTrackingEnabled` enthalten sind.

Integration in der Datei **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Sie können überprüfen, ob die IDFA-Erfassung in Ihrer Anwendung ordnungsgemäß deaktiviert ist, indem Sie die Engagement-Testprotokolle prüfen. Weitere Informationen finden Sie in der Dokumentation zum Integrationstest \<ios-sdk-engagement-test-idfa\>.

## <a name="disable-log-reporting"></a>Deaktivieren der Protokollberichterstellung
### <a name="using-a-method-call"></a>Verwenden eines Methodenaufrufs
Sie können Folgendes aufrufen, wenn von Engagement keine Protokolle mehr gesendet werden sollen:

    [[EngagementAgent shared] setEnabled:NO];

Dieser Aufruf ist persistent: er verwendet `NSUserDefaults` zum Speichern der Informationen.

Sie können die Protokollberichterstellung erneut aktivieren, indem Sie dieselbe Funktion mit `YES`aufrufen.

### <a name="integration-in-your-settings-bundle"></a>Integration im Einstellungsbündel
Anstatt diese Funktion aufzurufen, können Sie diese Einstellung auch direkt in der vorhandenen Datei `Settings.bundle` integrieren. Die Zeichenfolge `engagement_agent_enabled` muss als Einstellungsbezeichner verwendet und einem Umschalter (`PSToggleSwitchSpecifier`) zugeordnet werden.

Das folgende Beispiel von `Settings.bundle` veranschaulicht die Implementierung:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Geräte-API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
