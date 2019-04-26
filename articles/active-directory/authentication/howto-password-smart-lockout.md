---
title: Zapobieganie ataki siłowe przy użyciu usługi Azure AD inteligentne blokada — usługi Azure Active Directory
description: Usługa Azure Active Directory, inteligentnej blokady pomaga chronić swoją organizację przed atakami siłowymi próby odgadnięcia hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c81a9f3891130f1c6fc2f1a665d7065fb983227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358144"
---
# <a name="azure-active-directory-smart-lockout"></a>Usługa Azure Active Directory, inteligentnej blokady

Blokada Smart pomocna, jeśli chcesz blokowanie złośliwych podmiotów, którzy są próby odgadnięcia hasła użytkowników lub używać metod siłowych. Może rozpoznać logowań pochodzących z prawidłowych użytkowników i je traktować inaczej niż te, które osoby atakujące i innych nieznanych źródeł. Blokada Smart blokuje osoby atakujące, pozwalając użytkownikom w dalszym ciągu dostęp do swoich kont i produktywności jednocześnie.

Domyślnie inteligentnej blokady blokady z konta prób logowania przez minutę po 10 nieudanych prób. Blokady konta po każdej kolejne nieudane próby logowania, jedną minutę przy pierwszym i dłuższy w przypadku kolejnych prób.

Blokada Smart śledzi ostatnie trzy skróty nieprawidłowego hasła, aby uniknąć zwiększenie licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi nieprawidłowe hasło wiele razy, to zachowanie nie spowoduje konta do blokady.

 > [!NOTE]
 > Skrót śledzenia funkcji nie jest dostępna dla klientów przy użyciu uwierzytelniania przekazywanego, włączone, ponieważ uwierzytelnianie odbywa się w środowisku lokalnym nie znajduje się w chmurze.

Inteligentnej blokady jest zawsze włączona dla wszystkich klientów usługi Azure AD przy użyciu ustawień domyślnych, które oferują odpowiedniej kombinacji zabezpieczeń i użyteczności. Dostosowanie ustawień inteligentnej blokady, na wartości specyficzne dla Twojej organizacji wymaga usługi Azure AD podstawowa lub wyższej licencji dla użytkowników.

Za pomocą inteligentnej blokady nie gwarantuje, że oryginalny użytkownik będzie nigdy nie będzie blokowane. Gdy inteligentnej blokady blokady konta użytkownika, spróbujemy najlepsze blokady użytkownika oryginalnego. Usługa blokady próbuje upewnij się, że nieupoważnione osoby nie może uzyskać dostęp do oryginalnego konta.  

* Każde centrum danych usługi Azure Active Directory śledzi blokady niezależnie. Użytkownik będzie miał (threshold_limit * datacenter_count) liczba prób, jeśli użytkownik naciśnie każde centrum danych.
* Inteligentnej blokady używa znanych vs nieznanej lokalizacji w celu rozróżnienia między nieuprawnione i użytkownikiem, oryginalnym. Lokalizacje znane i nieznanych mają oddzielne blokada liczników.

Blokada Smart można zintegrować z hybrydowych wdrożeń, przy użyciu synchronizacji skrótów haseł lub uwierzytelniania przekazywanego, aby chronić lokalnych kont usługi Active Directory są zablokowane przez osoby atakujące. Przez odpowiednie ustawienie zasad inteligentnej blokady w usłudze Azure AD, ataków można odfiltrowane zanim osiągną one w lokalnej usłudze Active Directory.

Korzystając z [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta.md), należy upewnić się, że:

* Wartość progowa blokady usługi Azure AD jest **mniej** niż próg blokady konta usługi Active Directory. Ustaw wartości, tak aby próg blokady konta usługi Active Directory to co najmniej dwa lub trzy razy dłużej niż wartość progowa blokady usługi Azure AD. 
* Czas trwania blokady usługi Azure AD **w ciągu kilku sekund** jest **dłużej** niż Active Directory Zresetuj licznik blokady konta po okresie **minut**.

> [!IMPORTANT]
> Aktualnie administrator nie można odblokować kont użytkowników w chmurze, jeśli zostały one zablokowane przez możliwość inteligentnej blokady. Administrator musi czekać na czas trwania blokady wygaśnie.

## <a name="verify-on-premises-account-lockout-policy"></a>Sprawdź zasady blokady konta w środowisku lokalnym

Użyj poniższych instrukcji, aby sprawdzić zasady blokady konta usługi lokalnej usługi Active Directory:

1. Otwórz narzędzie do zarządzania zasadami grupy.
2. Edytowanie zasad grupy, która zawiera zasady blokady konta w organizacji, na przykład **domyślne zasady domeny**.
3. Przejdź do **konfiguracji komputera** > **zasady** > **ustawienia Windows** > **ustawienia zabezpieczeń**   >  **Zasady konta** > **zasady blokady konta**.
4. Sprawdź swoje **próg blokady konta** i **zerowanie licznika blokady konta po** wartości.

![Zmodyfikuj Zasady blokady konta w usłudze Active Directory w środowisku lokalnym](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Zarządzanie wartościami inteligentnej blokady w usłudze Azure AD

Zgodnie z wymaganiami organizacji, inteligentnej blokady wartości może być konieczne można dostosować. Dostosowanie ustawień inteligentnej blokady, na wartości specyficzne dla Twojej organizacji wymaga usługi Azure AD podstawowa lub wyższej licencji dla użytkowników.

Aby sprawdzić, lub zmodyfikuj wartości inteligentnej blokady dla Twojej organizacji, użyj następujących czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij pozycję **usługi Azure Active Directory**, następnie **metod uwierzytelniania**.
1. Ustaw **próg blokady**, zależnie od liczby nieudanych operacji logowania są dozwolone w konto przed jego pierwszym blokady. Wartość domyślna wynosi 10.
1. Ustaw **czas trwania blokady w ciągu kilku sekund**, do długości w ciągu kilku sekund w każdej blokady. Wartość domyślna to 60 sekund (co minutę).

> [!NOTE]
> Przy pierwszym zalogowaniu po blokady nie powiedzie się także, konto blokuje ponownie. Jeśli konto blokady wielokrotnie, zwiększony czas trwania blokady.

![Dostosuj zasady blokady inteligentne usługi Azure AD w witrynie Azure portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak ustalić, czy funkcję inteligentnej blokady działa lub nie

Po wyzwoleniu próg blokady smart zablokowaniu konta zostanie wyświetlony następujący komunikat:

**Twoje konto zostało tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później, a jeśli nadal występują problemy, skontaktuj się z administratorem.**

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak zablokować nieprawidłowych haseł w organizacji za pomocą usługi Azure AD.](howto-password-ban-bad.md)
* [Konfigurowanie samoobsługowego resetowania hasła, aby umożliwić użytkownikom odblokować własne konta.](quickstart-sspr.md)
