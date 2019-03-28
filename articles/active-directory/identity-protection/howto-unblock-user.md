---
title: Jak odblokować użytkowników za pomocą usługi Azure Active Directory Identity Protection | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odblokować użytkowników, które zostały zablokowane przez zasady usługi Azure Active Directory Identity Protection.
services: active-directory
keywords: Usługa Azure active directory identity protection Odblokuj użytkownika
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d22fa7fd3964f99c426e8e21d34dcfdea6d1b36
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516913"
---
# <a name="how-to-unblock-users"></a>Instrukcje: Odblokowywanie użytkowników

Za pomocą usługi Azure Active Directory Identity Protection można skonfigurować zasady, aby zablokować użytkowników, jeśli skonfigurowane warunki są spełnione. Zazwyczaj zablokowany użytkownik kontaktów pomocy technicznej w celu zostają odblokowane. W tym artykule opisano kroki należy wykonać, aby odblokować zablokowany użytkownik.

## <a name="determine-the-reason-for-blocking"></a>Określić przyczynę zablokowania
Jako pierwszy krok, aby odblokować użytkownika należy określić typ zasad, który został zablokowany użytkownik, ponieważ kolejnymi krokami są od niego zależne.
Za pomocą usługi Azure Active Directory Identity Protection użytkownik może zostać albo zablokowany przez zasadę ryzyka logowania lub zasad o podwyższonym ryzyku dla użytkownika.

Możesz uzyskać typ zasad, który został zablokowany użytkownik z nagłówkiem w oknie dialogowym, które zostały przedstawione dla użytkownika podczas próby logowania:

| Zasady | Okno dialogowe użytkownika |
| --- | --- |
| Ryzyko logowania |![Logowanie zablokowane](./media/howto-unblock-user/02.png) |
| Ryzyko związane z użytkownikiem |![Konto zablokowane](./media/howto-unblock-user/104.png) |

Użytkownik, który jest zablokowany przez:

* Zasady ryzyka logowania jest także znana jako podejrzane logowania
* Zasad ryzyka dla użytkownika jest nazywane również kontem na ryzyko

## <a name="unblocking-suspicious-sign-ins"></a>Odblokowywanie podejrzane logowania
Aby odblokować podejrzane logowania, dostępne są następujące opcje:

1. **Zaloguj się za pomocą znanej lokalizacji lub urządzenie** -typową przyczyną blokowania podejrzanych logowania są prób logowania z nieznanych lokalizacji lub urządzeń. Użytkownicy może szybko określić, czy to jest przyczyna blokowania próby logowania z dobrze znanych lokalizacji lub urządzenia.
2. **Wykluczone z zasad** — Jeśli uważasz, że bieżącą konfigurację zasad logowania jest przyczyną problemów pod kątem określonych użytkowników, można wykluczyć użytkowników z niego. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Wyłącz zasady** — Jeśli uważasz, że zasad konfiguracji powoduje problemy dla wszystkich użytkowników, można wyłączyć zasad. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Odblokowywanie konta narażone na ryzyko
Aby odblokować konto, na ryzyko, dostępne są następujące opcje:

1. **Resetuj hasło** — możesz zresetować hasła użytkownika. 
2. **Odrzuć wszystkie zdarzenia o podwyższonym ryzyku** — bloki zasad ryzyka użytkownika przez użytkownika, jeśli użytkownik skonfigurowany ryzyka poziom blokuje dostęp został osiągnięty. Użytkownika można zmniejszyć jego poziom ryzyka przez ręczne zamknięcie zgłoszonych zdarzeń o podwyższonym ryzyku. 
3. **Wykluczone z zasad** — Jeśli uważasz, że bieżącą konfigurację zasad logowania jest przyczyną problemów pod kątem określonych użytkowników, można wykluczyć użytkowników z niego. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Wyłącz zasady** — Jeśli uważasz, że zasad konfiguracji powoduje problemy dla wszystkich użytkowników, można wyłączyć zasad. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Kolejne kroki
 
Czy chcesz dowiedzieć się więcej o usłudze Azure AD Identity Protection? Zapoznaj się z [usługi Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
