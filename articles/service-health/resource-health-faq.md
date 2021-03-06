---
title: "Azure Resource Health – FAQ | Microsoft-Dokumentation"
description: "Übersicht über Azure Resource Health"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/05/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 794117b6f383bdd1851681864e99b3c1ef077f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health – FAQ
Erfahren Sie die Antworten auf häufig gestellte Fragen zu Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Was ist Azure Resource Health?
Resource Health hilft Ihnen bei der Diagnose und beim Anfordern von Support, wenn ein Azure-Problem Ihre Ressourcen beeinträchtigt. Der Dienst informiert Sie über die aktuelle und frühere Integrität Ihrer Ressourcen und unterstützt Sie beim Beheben von Problemen. Resource Health bietet technischen Support, wenn Sie Unterstützung bei Azure-Dienstproblemen benötigen.  

## <a name="what-is-the-resource-health-intended-for"></a>Wofür ist Resource Health vorgesehen?
Sobald ein Problem mit einer Ressource erkannt wird, kann Resource Health die Grundursache diagnostizieren. Es bietet Hilfe zur Behandlung des Problems und technischen Support, wenn Sie weitere Hilfe zu Azure-Dienstproblemen benötigen.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Welche Integritätsprüfungen werden von Resource Health ausgeführt?
Resource Health führt auf der Grundlage des [Ressourcentyps](resource-health-checks-resource-types.md) verschiedene Überprüfungen durch. Diese Überprüfungen wurden entwickelt, um drei Typen von Problemen zu implementieren: 
- Ungeplante Ereignisse, z.B. einen unerwarteten Hostneustart
- Geplante Ereignisse wie geplante Host-BS-Updates
- Durch Benutzeraktionen ausgelöste Ereignisse, z.B. Neustart eines virtuellen Computers durch einen Benutzer

## <a name="what-does-each-of-the-health-status-mean"></a>Was bedeuten die einzelnen Integritätsstatus?
Es gibt drei verschiedene Integritätsstatus:
- Verfügbar: In der Azure-Plattform liegen keine bekannten Probleme vor, die diese Ressource beeinträchtigen könnten.
- Nicht verfügbar: Resource Health hat Probleme festgestellt, die die Ressource beeinträchtigen.
- Unbekannt: Resource Health kann die Integrität einer Ressource nicht bestimmen, da der Dienst keine Informationen mehr zur Ressource erhält. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Was bedeutet der unbekannte Status? Stimmt mit meiner Ressource etwas nicht?
Der Integritätsstatus wird auf „Unbekannt“ festgelegt, wenn Resource Health zu einer bestimmten Ressource keine Informationen mehr erhält. Dieser Status ist zwar keine definitive Angabe des Ressourcenzustands, aber in Fällen, in denen Sie auf Probleme stoßen, kann er auf ein Azure-Problem hinweisen.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Wie kann ich Hilfe für eine Ressource erhalten, die nicht verfügbar ist?
Sie können auf dem Resource Health-Blatt eine Supportanfrage senden. Sie benötigen keine Supportvereinbarung mit Microsoft, um eine Anfrage zu öffnen, wenn die Ressource aufgrund von Plattformereignissen nicht verfügbar ist.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Differenziert Resource Health, ob Nichtverfügbarkeit auf Plattformprobleme zurückzuführen ist, oder auf Aktionen, die ich durchgeführt habe?
Ja, wenn eine Ressource nicht verfügbar ist, identifiziert Resource Health die Grundursache gemäß einer dieser Kategorien: 
-   Benutzerinitiierte Aktion
-   Geplantes Ereignis 
-   Ungeplantes Ereignis

Im Portal werden benutzerinitiierte Aktionen mit einem blauen Benachrichtigungssymbol angezeigt, geplante und ungeplante Ereignisse mit einem roten Warnsymbol. Weitere Informationen finden Sie in der [Übersicht über Azure Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kann ich Resource Health in meine Überwachungstools integrieren?
Resource Health ist ein Dienst, der Ihnen helfen soll, Azure-Dienstprobleme zu diagnostizieren und zu beheben, die Ihre Ressourcen beeinträchtigen. Sie können zwar den Integritätsstatus mit der Resource Health-API programmgesteuert abrufen, doch Sie sollten Metriken verwenden, um Ihre Ressourcen zu überwachen. Sobald ein Problem festgestellt wird, unterstützt Resource Health Sie bei der Ursachenermittlung und führt Sie durch Aktionen zur Problembehandlung. In der [Dokumentation zu Überwachung und Diagnose](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) erfahren Sie mehr darüber, wie Sie Metriken verwenden können, um Ihre Ressourcen zu überprüfen.

## <a name="where-do-i-find-resource-health"></a>Wo finde ich Resource Health?
Nachdem Sie sich beim Azure-Portal angemeldet haben, stehen Ihnen mehrere Möglichkeiten zur Verfügung, auf Resource Health zuzugreifen:
- Wechseln Sie zu Ihrer Ressource. Wählen Sie im linken Navigationsbereich **Resource Health** aus.
- Gehen Sie zum Blatt „Azure Monitor“.  Wählen Sie im linken Navigationsbereich **Resource Health** aus.
- Öffnen Sie das Blatt **Hilfe und Support**, indem Sie oben rechts im Portal das Fragezeichen und dann die Option **Hilfe und Support** auswählen. Nachdem das Blatt geöffnet wurde, wählen Sie **Resource Health** aus.

Sie können auch mit der Resource Health-API Informationen über den Integritätsstatus Ihrer Ressourcen erhalten.

## <a name="is-resource-health-available-for-all-resource-types"></a>Steht Resource Health für alle Ressourcentypen zur Verfügung?
Die Liste der durch Resource Health unterstützten Integritätsprüfungen und Ressourcentypen finden Sie [hier](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Was kann ich tun, wenn meine Ressource als verfügbar angezeigt wird, aber ich davon ausgehe, dass dies nicht der Fall ist?
Bei der Überprüfung der Integrität einer Ressource können Sie rechts unter dem Integritätsstatus auf **Falschen Integritätsstatus melden** klicken. Vor der Übermittlung des Berichts können Sie zusätzliche Details angeben, um zu belegen, warum Sie glauben, dass der aktuelle Integritätsstatus falsch ist.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Steht Resource Health für alle Azure-Regionen zur Verfügung? 
Resource Health ist in allen Azure-Regionen mit Ausnahme der folgenden verfügbar:
- US Government, Virginia
- US Government, Iowa
- US DoD, Osten
- US DoD, Mitte
- Deutschland, Mitte
- Deutschland, Nordosten
- China, Osten
- China, Norden

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>Wie unterscheidet sich Resource Health vom Dashboard zur Dienstintegrität oder den Dienstbenachrichtigungen des Azure-Portals?
Die von Resource Health bereitgestellten Informationen sind präziser als die vom Dashboard zur Azure-Dienstintegrität bereitgestellten Informationen.

Während [Azure-Status](https://status.azure.com) und die Dienstbenachrichtigungen des Portals Sie über Dienstprobleme informieren, die einen großen Kundenkreis (z.B. eine Azure-Region) betreffen, deckt Resource Health spezifischere Ereignisse auf, die nur für die jeweilige Ressource relevant sind. Wenn z.B. ein Host unerwartet neu startet, warnt Resource Health nur die Kunden, deren virtuelle Computer auf diesem Host ausgeführt wurden.

Bitte beachten Sie: Um Ihnen einen vollständigen Überblick über Ereignisse zu bieten, die Auswirkungen auf Ihre Ressourcen haben, deckt Resource Health auch Ereignisse auf, die in Dienstbenachrichtigungen und dem Dashboard zur Dienstintegrität veröffentlicht werden.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Muss ich Resource Health für jede Ressource aktivieren?
Nein, Integritätsinformationen sind über Resource Health für alle Ressourcentypen verfügbar. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Müssen wir Resource Health für meine Organisation aktivieren?
Nr.  Der Zugriff auf Azure Resource Health im Azure-Portal setzt keine Einrichtung voraus.

## <a name="is-resource-health-available-free-of-charge"></a>Ist Resource Health kostenlos verfügbar?
Ja.  Azure Resource Health ist kostenlos.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Welche Empfehlungen gibt Resource Health?
Basierend auf dem Integritätsstatus gibt Resource Health Ihnen Empfehlungen mit dem Ziel, die Zeit zu reduzieren, die Sie in die Problembehandlung investieren. Bei verfügbaren Ressourcen konzentrieren sich die Empfehlungen auf das Beheben der häufigsten Probleme, mit denen Kunden konfrontiert werden. Wenn die Ressource aufgrund eines ungeplanten Azure-Ereignisses nicht verfügbar ist, liegt der Schwerpunkt darauf, Sie während des Wiederherstellungsprozesses und danach zu unterstützen. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Resource Health:
-  [Übersicht über Azure Resource Health](Resource-health-overview.md)
-  [Über Azure Resource Health verfügbare Ressourcentypen und Integritätsüberprüfungen](resource-health-checks-resource-types.md)
