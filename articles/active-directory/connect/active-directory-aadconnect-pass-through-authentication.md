---
title: 'Azure AD Connect: Passthrough-Authentifizierung | Microsoft-Dokumentation'
description: "In diesem Artikel wird beschrieben, wie die Passthrough-Authentifizierung mit Azure Active Directory (Azure AD) funktioniert und wie sie Azure AD-Anmeldungen durch die Überprüfung von Benutzerkennwörtern anhand des lokalen Active Directory ermöglicht."
services: active-directory
keywords: "Was ist die Pass-Through-Authentifizierung mit Azure AD Connect?, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 6987ad8f36de55ac49785b41bb193721aa78321b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2017
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Benutzeranmeldung mit der Azure Active Directory-Passthrough-Authentifizierung

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Was ist die Azure Active Directory-Passthrough-Authentifizierung?

Mit der Azure AD-Passthrough-Authentifizierung (Azure Active Directory) können sich Benutzer mit denselben Kennwörtern sowohl bei lokalen als auch bei cloudbasierten Anwendungen anmelden. Diese Funktion stellt eine benutzerfreundlichere Oberfläche für Ihre Benutzer bereit, weil ein Kennwort wegfällt, und reduziert die Kosten des IT-Helpdesks, da Benutzer seltener vergessen, wie sie sich anmelden. Wenn Benutzer sich mithilfe von Azure AD anmelden, überprüft dieses Feature die Benutzerkennwörter direkt anhand Ihres lokalen Active Directory.

<iframe width="560" height="315" src="https://www.youtube.com/embed/PyeAC85Gm7w" frameborder="0" allowfullscreen></iframe>

Diese Funktion ist eine Alternative zum [Kennworthashsynchronisierung von Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), die Organisationen auch eine Möglichkeit zur Cloudauthentifizierung bereitstellt. Die Sicherheits- und Compliancerichtlinien einiger Organisationen verbieten es jedoch, Benutzerkennwörter selbst im Hashformat an externe Empfänger zu senden. Die Passthrough-Authentifizierung ist die richtige Lösung für solche Organisationen.

![Azure AD-Passthrough-Authentifizierung](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Sie können die Passthrough-Authentifizierung mit dem Feature zum [nahtlosen einmaligen Anmelden](active-directory-aadconnect-sso.md) (Single Sign-On, SSO) kombinieren. Auf diese Weise müssen Ihre Benutzer, wenn sie über ihren Unternehmenscomputer innerhalb des Unternehmensnetzwerks auf Anwendungen zugreifen, nicht einmal ihre Kennwörter eingeben.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Wichtige Vorteile der Azure AD-Passthrough-Authentifizierung

- *Die Benutzeroberfläche*
  - Benutzer verwenden für die Anmeldung bei lokalen und cloudbasierten Anwendungen das gleiche Kennwort.
  - Benutzer verbringen Zeit weniger mit dem Lösen von Kennwortproblemen mit dem IT-Helpdesk.
  - Benutzer können die [Self-Service-Kennwortverwaltung](../active-directory-passwords-overview.md) selbst in der Cloud durchführen.
- *Einfache Bereitstellung und Verwaltung*
  - Bereitstellungen oder Netzwerkkonfigurationen müssen nicht mehr komplex und lokal sein.
  - Es muss nur ein einfacher Agent lokal installiert werden.
  - Es gibt keinen zusätzlichen Verwaltungsaufwand. Der Agent empfängt Verbesserungen und Fehlerbehebungen automatisch.
- *Schützen*
  - Lokale Kennwörter werden niemals in irgendeiner Form in der Cloud gespeichert.
  - Der Agent stellt innerhalb des Netzwerks nur ausgehende Verbindungen her. Daher muss der Agent nicht in einem Umkreisnetzwerk (auch als DMZ bezeichnet) installiert werden.
  - Ihre Benutzerkonten werden durch die nahtlose Zusammenarbeit mit [Azure AD-Richtlinien für den bedingten Zugriff](../active-directory-conditional-access-azure-portal.md), einschließlich der Multi-Factor Authentication (MFA), und durch das [Filtern von Brute-Force-Kennwortangriffen](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) geschützt.
- *Hoch verfügbar*
  - Zusätzliche Agents können auf mehrere lokalen Servern installiert werden, um eine hohe Verfügbarkeit von Anmeldeanforderungen bereitzustellen.

## <a name="feature-highlights"></a>Wichtige Features

- Benutzeranmeldungen in allen browserbasierten Webanwendungen und Microsoft Office-Clientanwendungen werden unterstützt, die die [moderne Authentifizierung](https://aka.ms/modernauthga) verwenden.
- Bei den Benutzernamen für die Anmeldung kann es sich entweder um den lokalen Standardbenutzernamen (`userPrincipalName`) oder um ein anderes (als `Alternate ID` bezeichnetes) Attribut handeln, das in Azure AD Connect konfiguriert ist.
- Das Feature funktioniert nahtlos mit Features für den [bedingten Zugriff](../active-directory-conditional-access-azure-portal.md) wie die Multi-Factor Authentication (MFA), mit der Sie Ihre Benutzer schützen können.
- Es ist in eine cloudbasierte [Self-Service-Kennwortverwaltung](../active-directory-passwords-overview.md) integriert, einschließlich Rückschreiben von Kennwörtern auf lokalem Active Directory und Kennwortschutz, indem häufig verwendete Kennwörter gesperrt werden.
- Umgebungen mit mehreren Gesamtstrukturen werden unterstützt, wenn Gesamtstruktur-Vertrauensstellungen zwischen Ihren AD-Gesamtstrukturen bestehen und das Namensuffixrouting ordnungsgemäß konfiguriert ist.
- Es handelt sich um ein kostenloses Feature, sodass Sie für dessen Verwendung keine kostenpflichtigen Editionen von Azure AD benötigen.
- Dies kann über [Azure AD Connect](active-directory-aadconnect.md) aktiviert werden.
- Dazu wird ein einfacher lokaler Agent verwendet, der auf Kennwortüberprüfungsanforderungen lauscht und darauf reagiert.
- Die Installation von mehreren Agents stellt hohe Verfügbarkeit von Anmeldeanforderungen bereit.
- So werden Ihre lokalen Konten gegen Brute-Force-Kennwortangriffe in der Cloud [geschützt](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart**](active-directory-aadconnect-pass-through-authentication-quick-start.md): Einrichten und Ausführen der Passthrough-Authentifizierung mit Azure AD
- [**Smart Lockout**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Konfigurieren der Smart Lockout-Funktion für Ihren Mandanten zum Schutz der Benutzerkonten.
- [**Aktuelle Einschränkungen**](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Informationen zu den unterstützten und nicht unterstützten Szenarien
- [**Technische Einzelheiten**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – Funktionsweise dieses Features verstehen
- [**Häufig gestellte Fragen**](active-directory-aadconnect-pass-through-authentication-faq.md) – Antworten auf häufig gestellte Fragen
- [**Problembehandlung**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Beheben häufig auftretender Probleme mit dieser Funktion
- [**Ausführliche Informationen zur Sicherheit**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): zusätzliche ausführliche technische Informationen zum Feature.
- [**Nahtlose SSO mit Azure AD**](active-directory-aadconnect-sso.md): Informationen zu dieser Ergänzungsfunktion
- [**UserVoice:**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen
