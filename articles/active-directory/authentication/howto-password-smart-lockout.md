---
title: Zapobieganie ataków siłowych przy użyciu usługi Azure AD inteligentnej blokady
description: Usługa Azure Active Directory, inteligentnej blokady pomaga chronić swoją organizację przed atakami siłowymi próby odgadnięcia hasła
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: b0fded9f5543d151091955c0b0d645bf9db16b7d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158587"
---
# <a name="azure-active-directory-smart-lockout"></a>Usługa Azure Active Directory, inteligentnej blokady

Inteligentnej blokady blokowania złośliwych podmiotów, którzy są próby odgadnięcia hasła użytkowników lub używać metod siłowych korzysta z rozwiązań inteligentnych w chmurze. Tej analizy może być rozpoznawany logowań pochodzących z prawidłowych użytkowników i je traktować inaczej niż te, które osoby atakujące i innych nieznanych źródeł. Blokada Smart blokuje osoby atakujące, pozwalając użytkownikom w dalszym ciągu dostęp do swoich kont i produktywności jednocześnie.

Domyślnie inteligentnej blokady blokady z konta prób logowania przez minutę po 10 nieudanych próbach. Blokady konta po każdej kolejne nieudane próby logowania, jedną minutę przy pierwszym i dłuższy w przypadku kolejnych prób.

Inteligentnej blokady jest zawsze włączona dla wszystkich klientów usługi Azure AD przy użyciu ustawień domyślnych, które oferują odpowiedniej kombinacji zabezpieczeń i użyteczności. Dostosowanie ustawień inteligentnej blokady, na wartości specyficzne dla Twojej organizacji wymaga usługi Azure AD podstawowa lub wyższej licencji dla użytkowników.

Blokada Smart można zintegrować z hybrydowych wdrożeń, przy użyciu synchronizacji skrótów haseł lub uwierzytelniania przekazywanego, aby chronić lokalnych kont usługi Active Directory są zablokowane przez osoby atakujące. Przez odpowiednie ustawienie zasad inteligentnej blokady w usłudze Azure AD, ataków można odfiltrowane zanim osiągną one w lokalnej usłudze Active Directory.

Korzystając z [uwierzytelniania przekazywanego](../connect/active-directory-aadconnect-pass-through-authentication.md), należy upewnić się, że:

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

![Zmodyfikuj Zasady blokady konta usługi Active Directory w środowisku lokalnym za pomocą obiektu zasad grupy](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Zarządzanie wartościami inteligentnej blokady w usłudze Azure AD

Zgodnie z wymaganiami organizacji, inteligentnej blokady wartości może być konieczne można dostosować. Dostosowanie ustawień inteligentnej blokady, na wartości specyficzne dla Twojej organizacji wymaga usługi Azure AD podstawowa lub wyższej licencji dla użytkowników.

Aby sprawdzić, lub zmodyfikuj wartości inteligentnej blokady dla Twojej organizacji, użyj następujących czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij pozycję **usługi Azure Active Directory**, następnie **metod uwierzytelniania**.
1. Ustaw **próg blokady**, zależnie od liczby nieudanych operacji logowania są dozwolone w konto przed jego pierwszym blokady. Wartość domyślna wynosi 10.
1. Ustaw **czas trwania blokady w ciągu kilku sekund**, do długości w ciągu kilku sekund w każdej blokady. Wartość domyślna to 60 sekund (co minutę).

> [!NOTE]
> Przy pierwszym zalogowaniu po blokady nie powiedzie się także, konto blokuje ponownie. Jeśli konto blokady wielokrotnie, zwiększony czas trwania blokady.

![Dostosuj zasady blokady inteligentne usługi Azure AD w witrynie Azure portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak zablokować nieprawidłowych haseł w organizacji za pomocą usługi Azure AD.](howto-password-ban-bad.md)

[Konfigurowanie samoobsługowego resetowania hasła, aby umożliwić użytkownikom odblokować własne konta.](quickstart-sspr.md)