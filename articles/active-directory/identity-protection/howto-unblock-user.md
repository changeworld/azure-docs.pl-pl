---
title: Jak odblokować użytkowników przy użyciu Azure Active Directory Identity Protection | Microsoft Docs
description: Dowiedz się, jak odblokować użytkowników, którzy zostali Zablokowani przy użyciu zasad Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3756435703c4e8c887a4e7b9d4f75a6701d840
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126253"
---
# <a name="how-to-unblock-users"></a>Instrukcje: Odblokowywanie użytkowników

Za pomocą Azure Active Directory Identity Protection można skonfigurować zasady blokowania użytkowników w przypadku spełnienia skonfigurowanych warunków. Zwykle Zablokowane kontakty użytkowników działu pomocy technicznej mogą zostać odblokowane. W tym artykule opisano czynności, które można wykonać w celu odblokowania zablokowanego użytkownika.

## <a name="determine-the-reason-for-blocking"></a>Określ przyczynę zablokowania

Pierwszym krokiem odblokowania użytkownika jest określenie typu zasad, które zablokowały użytkownika, ponieważ następne kroki są zależne od niego.
W przypadku Azure Active Directory Identity Protection użytkownik może być zablokowany przy użyciu zasad ryzyka logowania lub zasad dotyczących ryzyka użytkownika.

Możesz uzyskać typ zasad, które blokują użytkownika z nagłówka w oknie dialogowym, który został przedstawiony użytkownikowi podczas próby logowania:

| Zasady | Okno dialogowe użytkownika |
| --- | --- |
| Ryzyko związane z logowaniem |![Zablokuj logowanie](./media/howto-unblock-user/02.png) |
| Ryzyko związane z użytkownikiem |![Zablokowane konto](./media/howto-unblock-user/104.png) |

Użytkownik zablokowany przez:

* Zasady dotyczące ryzyka związanego z logowaniem są również znane jako podejrzane logowanie
* Zasady ryzyka użytkownika są również znane jako ryzyko związane z ryzykiem

## <a name="unblocking-suspicious-sign-ins"></a>Odblokowywanie podejrzanych logowań

Aby odblokować podejrzane logowanie, dostępne są następujące opcje:

1. **Zaloguj się ze znanej lokalizacji lub urządzenia** — typową przyczyną zablokowanych podejrzanych logowań są próby logowania z nieznanych lokalizacji lub urządzeń. Użytkownicy mogą szybko określić, czy jest to powód blokowania, próbując zalogować się ze znajomej lokalizacji lub urządzenia.
2. **Wyklucz z zasad** — Jeśli uważasz, że Bieżąca konfiguracja zasad logowania powoduje problemy dla określonych użytkowników, możesz wykluczyć z niej użytkowników. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Wyłącz zasady** — Jeśli sądzisz, że konfiguracja zasad powoduje problemy dla wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Odblokowanie kont zagrożonych

Aby odblokować konto na ryzyko, dostępne są następujące opcje:

1. **Resetowanie hasła** — możesz zresetować hasło użytkownika. 
2. **Odrzuć wszystkie wykrycia ryzyka** — zasady ryzyka dla użytkowników blokują użytkownika, jeśli osiągnięto skonfigurowany poziom ryzyka użytkownika na potrzeby blokowania dostępu. Poziom ryzyka użytkownika można zmniejszyć, ręcznie zamykając zgłoszone wykrycie zagrożeń. 
3. **Wyklucz z zasad** — Jeśli uważasz, że Bieżąca konfiguracja zasad logowania powoduje problemy dla określonych użytkowników, możesz wykluczyć z niej użytkowników. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Wyłącz zasady** — Jeśli sądzisz, że konfiguracja zasad powoduje problemy dla wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Następne kroki
 
Czy chcesz dowiedzieć się więcej o Azure AD Identity Protection? Wyewidencjonuj [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
