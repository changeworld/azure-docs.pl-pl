---
title: Jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować zasady rejestracji uwierzytelniania wieloskładnikowego Azure AD Identity Protection.
services: active-directory
keywords: Usługa Azure active directory identity protection odnajdywania aplikacji w chmurze, zarządzanie aplikacji, zabezpieczenia, ryzyka, poziom ryzyka, luk w zabezpieczeniach, zasady zabezpieczeń
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: e626260dba3155ef56ee4a784aab2c6fd6897295
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581363"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Instrukcje: Konfigurowanie zasad rejestracji usługi Multi-Factor authentication

Usługa Azure AD Identity Protection pomaga w zarządzaniu wdrożenie rejestracji uwierzytelniania wieloskładnikowego (MFA) przez skonfigurowanie zasad. W tym artykule opisano, jakie zasady mogą służyć do wiadomość sposobu ich konfigurowania.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Co to są zasady rejestracji uwierzytelniania wieloskładnikowego?

Usługa Azure Multi-Factor authentication jest metodą weryfikacji tożsamości, która wymaga użycia więcej niż tylko nazwy użytkownika i hasła. Zapewnia drugą warstwę zabezpieczeń do logowania użytkowników i transakcji.  
Firma Microsoft zaleca, wymagają usługi Azure Multi-Factor authentication do logowania użytkownika, ponieważ jego:

* Zapewnia silne uwierzytelnianie z szerokim zakresem prostych opcji weryfikacji
* Odgrywa kluczową rolę w przygotowywanie organizacji do ochrony i odzyskiwania z naruszeń konta

![Zasady dotyczące ryzyka związanego z użytkownika](./media/howto-mfa-policy/1019.png "zasad ryzyka dla użytkownika")

Aby uzyskać więcej informacji, zobacz [co to jest uwierzytelnianie wieloskładnikowe systemu Azure?](../authentication/multi-factor-authentication.md)

## <a name="configuration"></a>Konfigurowanie

**Aby otworzyć okno dialogowe związanej z nimi konfiguracji**:

- Na **usługi Azure AD Identity Protection** bloku, w **Konfiguruj** kliknij **rejestracji uwierzytelniania wieloskładnikowego**.

    ![Zasady MFA](./media/howto-mfa-policy/1019.png "zasad MFA")

### <a name="settings"></a>Ustawienia

* Ustaw użytkowników i grup, których dotyczą te zasady:

    ![Zasady MFA](./media/howto-mfa-policy/1020.png "zasad MFA")
* Ustawienie kontroli, które mają być egzekwowane po wyzwoleniu zasady::  

    ![Zasady MFA](./media/howto-mfa-policy/1021.png "zasad MFA")
* Przełącz stan zasad:

    ![Zasady MFA](./media/howto-mfa-policy/403.png "zasad MFA")
* Wyświetl bieżący stan rejestracji:

    ![Zasady MFA](./media/howto-mfa-policy/1022.png "zasad MFA")

Omówienie środowiska użytkownika Zobacz:

* [Uwierzytelnianie wieloskładnikowe rejestracji w usłudze flow](flows.md#multi-factor-authentication-registration).  
* [Logowania środowisk przy użyciu usługi Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie programu Azure AD Identity Protection, zobacz [Omówienie usługi Azure AD Identity Protection](overview.md).
