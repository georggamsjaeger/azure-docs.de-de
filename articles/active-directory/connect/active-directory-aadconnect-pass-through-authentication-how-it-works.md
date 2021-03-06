---
title: "Azure AD Connect: Passthrough-Authentifizierung – Funktionsweise | Microsoft-Dokumentation"
description: In diesem Artikel wird die Funktionsweise der Passthrough-Authentifizierung mit Azure Active Directory beschrieben.
services: active-directory
keywords: "Passthrough-Authentifizierung mit Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, einmaliges Anmelden"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 00420e07957e7d8fe68bb8f1a1742110429d16b8
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Passthrough-Authentifizierung mit Azure Active Directory: Technische Einzelheiten
Dieser Artikel enthält eine Übersicht über die Funktionsweise der Passthrough-Authentifizierung mit Azure Active Directory (Azure AD). Ausführliche technische und sicherheitsbezogene Informationen finden Sie im Artikel [Azure Active Directory-Passthrough-Authentifizierung – ausführliche Informationen zur Sicherheit](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funktionsweise der Passthrough-Authentifizierung mit Azure Active Directory

Wenn ein Benutzer versucht, sich bei einer durch Azure AD gesicherten Anwendung anzumelden und die Passthrough-Authentifizierung im Mandanten aktiviert ist, geschieht Folgendes:

1. Der Benutzer versucht, auf eine Anwendung zuzugreifen (z.B. [Outlook-Web-App](https://outlook.office365.com/owa/)).
2. Wenn der Benutzer nicht bereits angemeldet ist, wird der Benutzer zur Azure AD-Seite **Benutzeranmeldung** umgeleitet.
3. Der Benutzer gibt auf der Azure AD-Anmeldeseite seinen Benutzernamen und sein Kennwort ein und klickt anschließend auf die Schaltfläche **Anmelden**.
4. Nach dem Empfang der Anmeldeanforderung platziert Azure AD den Benutzernamen und das (mit einem öffentlichen Schlüssel verschlüsselte) Kennwort in einer Warteschlange.
5. Ein lokaler Authentifizierungs-Agent ruft den Benutzernamen und das verschlüsselte Kennwort aus der Warteschlange ab.
6. Der Agent entschlüsselt das Kennwort mithilfe des privaten Schlüssels.
7. Der Agent überprüft dann mithilfe von Standard-Windows-APIs den Benutzernamen und das Kennwort in Active Directory (ein ähnlicher Mechanismus wie bei den Active Directory-Verbunddiensten (AD FS)). Beim Benutzernamen kann es sich entweder um den lokalen Standardbenutzernamen (in der Regel `userPrincipalName`) handeln oder um ein anderes (als `Alternate ID` bezeichnetes) Attribut, das in Azure AD Connect konfiguriert ist.
8. Der lokale Active Directory-Domänencontroller (DC) wertet die Anforderung aus und gibt die entsprechende Antwort (Erfolg, Fehler, Kennwort abgelaufen oder Benutzer gesperrt) an den Agenten zurück.
9. Der Authentifizierungs-Agent gibt diese Antwort wiederum an Azure AD zurück.
10. Azure AD wertet die Antwort aus und gibt eine entsprechende Antwort an den Benutzer zurück. Beispiel: Der Benutzer wird sofort angemeldet, oder es wird Azure Multi-Factor Authentication angefordert.
11. Wenn die Anmeldung erfolgreich ist, kann der Benutzer auf die Anwendung zugreifen.

Das folgende Diagramm veranschaulicht die dafür notwendigen Schritte und Komponenten:

![Passthrough-Authentifizierung](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Nächste Schritte
- [Aktuelle Einschränkungen:](active-directory-aadconnect-pass-through-authentication-current-limitations.md) Informationen zu den unterstützten und nicht unterstützten Szenarien
- [Schnellstart:](active-directory-aadconnect-pass-through-authentication-quick-start.md) Aktivieren und Ausführen der Passthrough-Authentifizierung von Azure AD
- [Smart Lockout:](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) Konfigurieren der Smart Lockout-Funktion für Ihren Mandanten, um Benutzerkonten zu schützen
- [Häufig gestellte Fragen:](active-directory-aadconnect-pass-through-authentication-faq.md) Antworten auf häufig gestellte Fragen
- [Problembehandlung:](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) Informationen zum Beheben von allgemeinen Problemen, die bei der Passthrough-Authentifizierung auftreten können
- [Ausführliche Informationen zur Sicherheit:](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) Technische Informationen zur Passthrough-Authentifizierung
- [Nahtloses SSO mit Azure AD:](active-directory-aadconnect-sso.md) Informationen zu dieser Ergänzungsfunktion
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Anfordern neuer Features über das Azure Active Directory-Forum

