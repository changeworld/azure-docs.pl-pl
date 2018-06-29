---
title: Zapobieganie atakom metodą siłową przy użyciu blokady inteligentnej usługi Azure AD
description: Azure Active Directory blokady inteligentnej pomaga chronić organizację przed atakami siłowymi odgadnięcia hasła
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: d5beb5ce6e167cd100bec2ed54dc6ea0e78ba37b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036002"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory inteligentne blokady.

Blokady inteligentnej używa analizy chmury w celu zablokowania nieupoważnione, które próbują odgadnięcia hasła użytkowników lub za pomocą metod siłowych można uzyskać w. Tego analizy może rozpoznawać logowania pochodzące z prawidłowych użytkowników i je traktować inaczej niż te, które osoby atakujące i innych nieznanych źródeł. Blokady inteligentnej można zablokować osoby atakujące podczas, dzięki czemu użytkownicy nadal uzyskiwać dostęp do swoich kont i produktywności.

Blokady inteligentnej jest zawsze włączona dla wszystkich klientów usługi Azure AD przy użyciu ustawień domyślnych, które oferują odpowiedniej kombinacji zabezpieczeń i użyteczność. Dostosowywanie ustawień blokady inteligentnej, wartościami specyficznego dla organizacji, wymaga Azure AD podstawowa lub wyższej licencji dla użytkowników.

Blokady inteligentnej można zintegrować z wdrożeń hybrydowych, ochrona lokalnych kont usługi Active Directory z jest zablokowane przez osoby atakujące za pomocą synchronizacji skrótów haseł lub uwierzytelnianie przekazywane. Przez ustawienie zasad blokowania inteligentne w usłudze Azure AD odpowiednio ataków można odfiltrowane przed dotarciem w lokalnej usłudze Active Directory.

Korzystając z [uwierzytelniania przekazywanego](../connect/active-directory-aadconnect-pass-through-authentication.md), należy upewnić się, że:

   * Progu blokady konta usługi Azure AD jest **mniej** niż próg blokady konta usługi Active Directory. Ustaw wartości, tak aby próg blokady konta usługi Active Directory jest co najmniej dwa lub trzy razy dłuższy niż progu blokady konta usługi Azure AD. 
   * Czas trwania blokady usługi Azure AD **w sekundach** jest **dłużej** od usługi Active Directory Zresetuj licznik blokady konta po okresie **minut**.

> [!IMPORTANT]
> Obecnie administrator nie można odblokować konta użytkowników chmury, jeśli zostały one zablokowane przez możliwość blokady inteligentnej. Administrator musi czekać na czas trwania blokady wygaśnie.

## <a name="verify-on-premises-account-lockout-policy"></a>Sprawdzanie zasad blokady konta lokalnego

Użyj poniższych instrukcji, aby sprawdzić zasad blokady konta z lokalnymi usługi Active Directory:

1. Otwórz narzędzie Zarządzanie zasadami grupy.
2. Edytowanie zasad grupy, zawierający zasad blokady konta w organizacji, na przykład **domyślne zasady domeny**.
3. Przejdź do **Konfiguracja komputera** > **zasady** > **ustawienia systemu Windows** > **ustawienia zabezpieczeń**   >  **Zasady konta** > **zasad blokady konta**.
4. Sprawdź Twojej **próg blokady konta** i **resetowania licznik blokady konta po** wartości.

![Modyfikowanie zasad blokady konta usługi Active Directory lokalnej za pomocą obiektu zasad grupy](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Zarządzaj wartościami blokady inteligentnej usługi Azure AD

Zgodnie z wymaganiami organizacji, wartości blokady inteligentny może być konieczne można dostosować. Dostosowywanie ustawień blokady inteligentnej, wartościami specyficznego dla organizacji, wymaga Azure AD podstawowa lub wyższej licencji dla użytkowników.

Aby sprawdzić lub zmodyfikuj wartości inteligentne blokady dla Twojej organizacji, należy użyć następujące czynności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com)i kliknij przycisk **usługi Azure Active Directory**, następnie **metod uwierzytelniania**.
1. Ustaw **próg blokady**zgodnie mogą ile niepowodzenia logowania na koncie przed jego pierwszym blokady. Wartość domyślna to 10.
1. Ustaw **czas trwania blokady w sekundach**, długość w sekundach każdej blokady.

> [!NOTE]
> Jeśli przy pierwszym logowaniu po blokady nie powiedzie się także, konto blokuje ponownie. Jeśli konto blokuje wielokrotnie, zwiększony czas trwania blokady.

![Dostosuj zasady blokady inteligentnej usługi Azure AD w portalu Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak zakaz nieprawidłowych haseł w organizacji za pomocą usługi Azure AD.](howto-password-ban-bad.md)

[Konfigurowanie samoobsługowego resetowania hasła, aby umożliwić użytkownikom odblokowanie ich kont.](quickstart-sspr.md)