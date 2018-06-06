---
title: Azure Active Directory Identity Protection — sposób odblokowania użytkowników | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odblokować użytkowników, które zostały zablokowane przez zasady usługi Azure Active Directory Identity Protection.
services: active-directory
keywords: ochronę tożsamości usługi Azure active directory, Odblokuj użytkownika
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 1e96f24aeb0083e57618ad3e38163f50c23c55d3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713460"
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory Identity Protection — sposób odblokowania użytkowników
Z usługi Azure Active Directory Identity Protection można skonfigurować zasady blokowania użytkowników skonfigurowanych warunków. Zazwyczaj zablokowany użytkownik kontaktów pomocy technicznej, aby stać się odblokowany. W tym artykule opisano kroki można wykonać, aby odblokować zablokowanego użytkownika.

## <a name="determine-the-reason-for-blocking"></a>Określić przyczynę zablokowania
Pierwszym krokiem do odblokowanie użytkownika należy określić typ zasad, który został zablokowany użytkownik, ponieważ Twoje następne kroki są od niego zależne.
Azure Active Directory Identity Protection użytkownik może zostać albo zablokowana przez zasady logowania ryzyko lub zasad ryzyka dla użytkownika.

Możesz uzyskać typ zasad, który zablokował użytkownika z nagłówkiem w oknie dialogowym, który został przedstawiony użytkownikowi podczas próby logowania:

| Zasady | Okno dialogowe użytkownika |
| --- | --- |
| Ryzyko logowania |![Zablokowane logowania](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Ryzyko związane z użytkownikiem |![Konto zablokowane](./media/active-directory-identityprotection-unblock-howto/104.png) |

Użytkownik, który jest zablokowany przez:

* Zasady logowania ryzyko jest także znana jako podejrzane logowania
* Zasad ryzyka dla użytkownika jest także znana jako konto na ryzyko

## <a name="unblocking-suspicious-sign-ins"></a>Odblokowywanie podejrzane logowania
Aby odblokować podejrzane logowanie, masz następujące opcje:

1. **Zaloguj się z lokalizacji znanych lub urządzenia** — typową przyczyną zablokowanych podejrzane logowania są próby logowania z nieznanych lokalizacji lub urządzeń. Użytkownicy może szybko określić, czy jest to blokowania powód próba logowania z lokalizacji znanych lub urządzenia.
2. **Wyklucz z zasad** — Jeśli uważasz, że bieżąca konfiguracja zasad logowania powoduje problemy dotyczące konkretnych użytkowników, można wykluczyć użytkowników z niego. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
3. **Wyłącz zasady** — Jeśli uważasz, że konfigurację zasad powoduje problemy dotyczące wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Odblokowywanie kont na ryzyko
Aby odblokować konto zagrożone, masz następujące opcje:

1. **Zresetuj hasło** — można zresetować hasło użytkownika. Aby uzyskać więcej informacji, zobacz [resetowania ręcznego bezpiecznego hasła](active-directory-identityprotection.md#manual-secure-password-reset).
2. **Odrzuć wszystkie zdarzenia o podwyższonym ryzyku** — bloki zasad ryzyka użytkownika użytkownika, jeśli użytkownik skonfigurowany ryzyka poziom blokuje dostęp został osiągnięty. Użytkownik może zmniejszyć jego poziom ryzyka przez ręczne zamknięcie zgłoszone zdarzenia ryzyka. Aby uzyskać więcej informacji, zobacz [zamknięcie zdarzenia o podwyższonym ryzyku ręcznie](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Wyklucz z zasad** — Jeśli uważasz, że bieżąca konfiguracja zasad logowania powoduje problemy dotyczące konkretnych użytkowników, można wykluczyć użytkowników z niego. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
4. **Wyłącz zasady** — Jeśli uważasz, że konfigurację zasad powoduje problemy dotyczące wszystkich użytkowników, możesz wyłączyć zasady. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

## <a name="next-steps"></a>Kolejne kroki
 Czy chcesz dowiedzieć się więcej o usłudze Azure AD Identity Protection? Zapoznaj się z [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
