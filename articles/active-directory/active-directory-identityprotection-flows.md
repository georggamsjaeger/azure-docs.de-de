---
title: Anmeldeverfahren von Azure AD Identity Protection | Microsoft Docs
description: "Hier finden Sie eine Übersicht über Anmeldeszenarien, in denen Identity Protection ein Benutzerrisiko gemindert oder beseitigt hat oder in denen aufgrund einer Richtlinie die Verwendung der mehrstufigen Authentifizierung erforderlich ist."
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 689864b15890d8bc4a8a58a37f2034c66491654a
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2017
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Anmeldeverfahren von Azure AD Identity Protection
Azure Active Directory Identity Protection bietet folgende Möglichkeiten:

* Sie können festlegen, dass sich Benutzer für die mehrstufige Authentifizierung registrieren müssen.
* Sie können sich um riskante Anmeldungen und kompromittierte Benutzer kümmern.

Die Reaktion des Systems auf diese Probleme wirkt sich auf die Anmeldung des betroffenen Benutzers aus, da keine direkte Anmeldung mit Benutzername und Kennwort mehr möglich ist. Stattdessen müssen zusätzliche Schritte ausgeführt werden, damit sich der Benutzer wieder sicher anmelden kann.

In diesem Thema können Sie sich einen Überblick über alle Fälle verschaffen, die im Zusammenhang mit der Benutzeranmeldung auftreten können.

**Mehrstufige Authentifizierung**

* Registrierung für die mehrstufige Authentifizierung

**Riskante Anmeldung**

* Wiederherstellung riskanter Anmeldungen
* Blockierte riskante Anmeldung
* Registrierung für die mehrstufige Authentifizierung während einer riskanten Anmeldung

**Gefährdeter Benutzer**

* Wiederherstellung gefährdeter Konten
* Blockiertes gefährdetes Konto

## <a name="multi-factor-authentication-registration"></a>Registrierung für die mehrstufige Authentifizierung
Sowohl bei der Wiederherstellung gefährdeter Konten als auch bei riskanten Anmeldungen ist die Benutzerfreundlichkeit am größten, wenn der Benutzer die Wiederherstellung selbst durchführen kann. Den Konten von Benutzern, die sich für die mehrstufige Authentifizierung registriert haben, ist bereits eine Telefonnummer zugeordnet, die für die Sicherheitsabfragen verwendet werden kann. Das gefährdete Konto kann ganz ohne Beteiligung des Helpdesks oder eines Administrators wiederhergestellt werden. Sie sollten daher unbedingt sicherstellen, dass sich die Benutzer für die mehrstufige Authentifizierung registrieren. 

Administratoren haben folgende Möglichkeiten:

* Sie können eine Richtlinie festlegen, die es für Benutzer erforderlich macht, ihre Konten für eine zusätzliche Sicherheitsüberprüfung einzurichten. 
* Sie können eine Übergangsphase von bis zu 30 Tagen einrichten, in der die Benutzer die Registrierung für die mehrstufige Authentifizierung überspringen können.

**Die Registrierung für die mehrstufige Authentifizierung umfasst drei Schritte:**

1. Im ersten Schritt wird der Benutzer davon in Kenntnis gesetzt, dass das Konto für die mehrstufige Authentifizierung eingerichtet werden muss. 
   
    ![Wiederherstellung](./media/active-directory-identityprotection-flows/140.png "Wiederherstellung")
2. Zur Einrichtung der mehrstufigen Authentifizierung müssen Sie dem System mitteilen, wie Sie kontaktiert werden möchten.
   
    ![Wiederherstellung](./media/active-directory-identityprotection-flows/141.png "Wiederherstellung")
3. Das System sendet Ihnen eine Abfrage, auf die Sie reagieren müssen.
   
    ![Wiederherstellung](./media/active-directory-identityprotection-flows/142.png "Wiederherstellung")

## <a name="risky-sign-in-recovery"></a>Wiederherstellung riskanter Anmeldungen
Wenn ein Administrator eine Richtlinie für Anmelderisiken konfiguriert hat, werden die betroffenen Benutzer benachrichtigt, wenn sie versuchen, sich anzumelden. 

**Das Verfahren für riskante Anmeldungen umfasst zwei Schritte:** 

1. Der Benutzer wird informiert, dass im Zusammenhang mit seiner Anmeldung eine Unregelmäßigkeit erkannt wurde (beispielsweise eine Anmeldung an einem neuen Standort, mit einem neuen Gerät oder mit einer neuen App). 
   
    ![Wiederherstellung](./media/active-directory-identityprotection-flows/120.png "Wiederherstellung")
2. Der Benutzer muss eine Sicherheitsabfrage korrekt beantworten, um seine Identität zu bestätigen. Wenn der Benutzer für die mehrstufige Authentifizierung registriert ist, muss er einen Sicherheitscode eingeben, der an seine Telefonnummer gesendet wurde. Da es sich in diesem Fall nur um eine riskante Anmeldung und nicht um ein gefährdetes Konto handelt, muss der Benutzer sein Kennwort bei diesem Verfahren nicht ändern. 
   
    ![Wiederherstellung](./media/active-directory-identityprotection-flows/121.png "Wiederherstellung")

## <a name="risky-sign-in-blocked"></a>Blockierte riskante Anmeldung
Administratoren können auch eine Richtlinie für Anmelderisiken festlegen, um Benutzer abhängig von Risikostufe bei der Anmeldung zu blockieren. Zur Aufhebung der Blockierung muss sich der Endbenutzer an einen Administrator oder an den Helpdesk wenden oder sich an einem bekannten Standort oder mit einem bekannten Gerät anmelden. In diesem Fall ist keine selbstständige Wiederherstellung mittels mehrstufiger Authentifizierung möglich.

![Wiederherstellung](./media/active-directory-identityprotection-flows/200.png "Wiederherstellung")

## <a name="compromised-account-recovery"></a>Wiederherstellung gefährdeter Konten
Wenn eine Sicherheitsrichtlinie für das Benutzerrisiko konfiguriert wurde, müssen Benutzer, die der in der Richtlinie angegebenen Risikostufe entsprechen (und somit als kompromittiert betrachtet werden), das Wiederherstellungsverfahren für kompromittierte Benutzer durchlaufen, um sich wieder anmelden zu können. 

**Das Wiederherstellungsverfahren für kompromittierte Benutzer umfasst drei Schritte:**

1. Der Benutzer wird darüber informiert, dass die Sicherheit des Kontos aufgrund von verdächtigen Aktivitäten oder kompromittierten Anmeldeinformationen gefährdet ist.
   
    ![Wiederherstellung](./media/active-directory-identityprotection-flows/101.png "Wiederherstellung")
2. Der Benutzer muss eine Sicherheitsabfrage korrekt beantworten, um seine Identität zu bestätigen. Wenn der Benutzer für die mehrstufige Authentifizierung registriert ist, kann er die Wiederherstellung selbst durchführen. Hierzu muss er einen Sicherheitscode eingeben, der an seine Telefonnummer gesendet wurde. 
   
   ![Wiederherstellung](./media/active-directory-identityprotection-flows/110.png "Wiederherstellung")
3. Am Ende des Vorgangs muss der Benutzer sein Kennwort ändern, da unter Umständen eine andere Person Zugriff auf sein Konto hatte. 
   Im Anschluss finden Sie entsprechende Screenshots.
   
   ![Wiederherstellung](./media/active-directory-identityprotection-flows/111.png "Wiederherstellung")

## <a name="compromised-account-blocked"></a>Blockiertes gefährdetes Konto
Ein Benutzer, der aufgrund einer Benutzerrisiko-Sicherheitsrichtlinie blockiert wurde, muss sich zur Aufhebung der Blockierung an einen Administrator oder an den Helpdesk wenden. In diesem Fall ist keine selbstständige Wiederherstellung mittels mehrstufiger Authentifizierung möglich.

![Wiederherstellung](./media/active-directory-identityprotection-flows/104.png "Wiederherstellung")

## <a name="reset-password"></a>Zurücksetzen des Kennworts
Wenn die Anmeldung für einen kompromittierten Benutzer gesperrt ist, kann ein Administrator ein temporäres Kennwort für ihn generieren. Der Benutzer muss sein Kennwort dann bei der nächsten Anmeldung ändern.

![Wiederherstellung](./media/active-directory-identityprotection-flows/160.png "Wiederherstellung")

## <a name="see-also"></a>Weitere Informationen
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

