---
title: Azure Active Directory v2.0-Endpunkt | Microsoft-Dokumentation
description: "Eine Einführung in die Entwicklung von Anwendungen mit Anmeldung per Microsoft-Konto und Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: bd090450fad0be855240788c4cfa9dc58c1c4c6d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Anmelden von Microsoft-Konto- und Azure Active Directory-Benutzern in einer einzelnen Anwendung
In der Vergangenheit benötigten Anwendungsentwickler, die sowohl persönliche Microsoft-Konten als auch Geschäftskonten über Azure Active Directory unterstützen möchten, eine Integration für zwei separate Systeme. Der Azure AD v2.0-Endpunkt (Azure Active Directory) führt zur Vereinfachung dieses Prozesses eine neue Authentifizierungs-API-Version ein. Mit dem Azure AD v2.0-Endpunkt ist die Anmeldung über beide Arten von Konten mit einer einzelnen Integration möglich. Anwendungen, die den Azure AD v2.0-Endpunkt verwenden, können auch die REST-APIs aus der [Microsoft Graph-API](https://graph.microsoft.io) mit beiden Kontotypen nutzen.

## <a name="getting-started"></a>Erste Schritte
Wählen Sie in der Liste weiter unten Ihre bevorzugte Plattform aus, um eine Anwendung mit den Open Source-Bibliotheken und -Frameworks von Microsoft zu erstellen. Sie können auch das OAuth 2.0-Protokoll und das OpenID Connect-Protokoll verwenden, um Protokollnachrichten direkt ohne Verwendung einer Authentifizierungsbibliothek zu senden und zu empfangen.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Weitere Informationen zum Azure AD v2.0-Endpunkt
Lernen Sie die Möglichkeiten kennen, die Ihnen der Azure AD v2.0-Endpunkt bietet:

* Informieren Sie sich über die [Arten von Anwendungen, die Sie mit dem Azure AD v2.0-Endpunkt erstellen können](active-directory-v2-flows.md).
* Machen Sie sich mit den [Einschränkungen](active-directory-v2-limitations.md) des Azure AD v2.0-Endpunkts vertraut.
* Das folgende Video bietet einen Überblick über den Azure AD v2.0-Endpunkt:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Zusätzliche Ressourcen
Ausführliche Informationen zur Plattform des Azure AD v2.0-Endpunkts finden Sie unter den folgenden Links:

* [v2.0-Protokolle – OAuth 2.0 und OpenID Connect](active-directory-v2-protocols.md)
* [Azure Active Directory v2.0-Tokenreferenz](active-directory-v2-tokens.md)
* [Azure Active Directory v2.0-Authentifizierungsbibliotheken](active-directory-v2-libraries.md)
* [Bereiche, Berechtigungen und Zustimmung im Azure Active Directory v2.0-Endpunkt](active-directory-v2-scopes.md)
* [Microsoft Graph-API](https://graph.microsoft.io)

> [!NOTE]
> Wenn Sie nur Geschäfts-, Schul- und Unikonten aus Azure Active Directory anmelden müssen, lesen Sie das [Azure AD-Entwicklerhandbuch](active-directory-developers-guide.md). Der Azure AD v2.0-Endpunkt ist für die Verwendung durch Entwickler vorgesehen, die explizit eine Anmeldung bei persönlichen Microsoft-Konten benötigen.

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
