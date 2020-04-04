---
title: Zapobieganie atakom przy użyciu inteligentnej blokady — usługa Azure Active Directory
description: Inteligentna blokada usługi Azure Active Directory pomaga chronić organizację przed atakami siłowymi próbującymi odgadnąć hasła
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ae942ed189dc4245a9a0b282daf4cad5323536
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652577"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory smart lockout (Inteligentna blokada w usłudze Azure Active Directory)

Inteligentna blokada pomaga w blokowaniu złych aktorów, którzy próbują odgadnąć hasła użytkowników lub użyć metod brutalnej siły, aby dostać się do. Może rozpoznawać logowania pochodzące od prawidłowych użytkowników i traktować je inaczej niż osoby atakujące i inne nieznane źródła. Inteligentna blokada blokuje osoby atakujące, umożliwiając jednocześnie użytkownikom dostęp do ich kont i produktywność.

Domyślnie inteligentna blokada blokuje konto przed próbami logowania przez minutę po 10 nieudanych próbach. Konto blokuje się ponownie po każdej kolejnej nieudanej próbie logowania, na minutę na początku i dłużej w kolejnych próbach.

Inteligentna blokada śledzi trzy ostatnie złe skróty haseł, aby uniknąć zwiększania licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wiele razy, to zachowanie nie spowoduje blokady konta.

 > [!NOTE]
 > Funkcja śledzenia skrótów nie jest dostępna dla klientów z włączonym uwierzytelnianiem przekazu, ponieważ uwierzytelnianie odbywa się lokalnie, a nie w chmurze.

Wdrożenia federacyjne przy użyciu usług AD FS 2016 i AF FS 2019 mogą umożliwić podobne korzyści za pomocą [blokady dodatkowej sieci AD FS i inteligentnej blokady extranet.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)

Inteligentna blokada jest zawsze włączona dla wszystkich klientów usługi Azure AD z tymi ustawieniami domyślnymi, które oferują odpowiednią kombinację zabezpieczeń i użyteczności. Dostosowywanie ustawień blokady inteligentnej z wartościami specyficznymi dla organizacji wymaga płatnych licencji usługi Azure AD dla użytkowników.

Korzystanie z inteligentnej blokady nie gwarantuje, że oryginalny użytkownik nigdy nie zostanie zablokowany. Gdy inteligentna blokada blokuje konto użytkownika, staramy się jak najlepiej, aby nie zablokować oryginalnego użytkownika. Usługa blokady próbuje upewnić się, że złe podmioty nie mogą uzyskać dostępu do oryginalnego konta użytkownika.  

* Każde centrum danych usługi Azure Active Directory śledzi blokadę niezależnie. Użytkownik będzie miał (threshold_limit * datacenter_count) liczbę prób, jeśli użytkownik trafi do każdego centrum danych.
* Smart Lockout używa znanej lokalizacji a nieznanej lokalizacji, aby odróżnić złego aktora od oryginalnego użytkownika. Nieznane i znane lokalizacje będą miały oddzielne liczniki blokady.

Inteligentną blokadę można zintegrować z wdrożeniami hybrydowymi, używając synchronizacji skrótów haseł lub uwierzytelniania przekazywanego w celu ochrony lokalnych kont usługi Active Directory przed zablokowaniem przez osoby atakujące. Ustawiając zasady inteligentnej blokady w usłudze Azure AD odpowiednio, ataki mogą być odfiltrowywałe, zanim dotrą do lokalnej usługi Active Directory.

Podczas korzystania z [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta.md)należy upewnić się, że:

* Próg blokady usługi Azure AD jest **mniejszy** niż próg blokady konta usługi Active Directory. Ustaw wartości tak, aby próg blokady konta usługi Active Directory był co najmniej dwa lub trzy razy dłuższy niż próg blokady usługi Azure AD. 
* Czas trwania blokady usługi Azure AD musi być ustawiony dłużej niż licznik blokady konta serwera Active Directory po czasie trwania. Należy pamiętać, że czas trwania usługi Azure AD jest ustawiony w sekundach, podczas gdy czas trwania usługi AD jest ustawiony w minutach. 

Na przykład jeśli chcesz, aby licznik usługi Azure AD był wyższy niż ad, następnie usługi Azure AD będzie 120 sekund (2 minuty), podczas gdy lokalna usługa AD jest ustawiona na 1 minutę (60 sekund).

> [!IMPORTANT]
> Obecnie administrator nie może odblokować kont użytkowników w chmurze, jeśli zostały one zablokowane przez funkcję Smart Lockout. Administrator musi poczekać na wygaśnięcie czasu trwania blokady. Jednak użytkownik może odblokować za pomocą samoobsługowego resetowania hasła (SSPR) z zaufanego urządzenia lub lokalizacji.

## <a name="verify-on-premises-account-lockout-policy"></a>Weryfikowanie zasad blokady konta lokalnego

Aby zweryfikować lokalne zasady blokady konta usługi Active Directory, należy użyć następujących instrukcji:

1. Otwórz narzędzie Zarządzanie zasadami grupy.
2. Edytuj zasady grupy, które obejmują zasady blokady konta organizacji, na przykład **domyślne zasady domeny**.
3. Przejdź do **zasad konfiguracji** > komputera**Ustawienia** > **Windows Settings** > **ustawień zabezpieczeń Ustawienia** > zabezpieczeń**Zasady** > **blokady konta**.
4. Sprawdź **próg blokady konta** i **Zresetuj licznik blokady konta po** wartościach.

![Modyfikowanie lokalnych zasad blokady konta usługi Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Zarządzanie inteligentnymi wartościami blokady usługi Azure AD

W zależności od wymagań organizacyjnych może być konieczne dostosowanie wartości inteligentnej blokady. Dostosowywanie ustawień blokady inteligentnej z wartościami specyficznymi dla organizacji wymaga płatnych licencji usługi Azure AD dla użytkowników.

Aby sprawdzić lub zmodyfikować wartości blokady inteligentnej w organizacji, należy wykonać następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję *Azure Active Directory*. Wybierz **metody uwierzytelniania** > **Authentication methods** > zabezpieczeń**Ochrona hasłem**.
1. Ustaw **próg blokady**, na podstawie liczby nieudanych logów są dozwolone na koncie przed jego pierwszej blokady. Wartość domyślna to 10.
1. Ustaw **czas trwania blokady w sekundach**, na długość w sekundach każdej blokady. Wartość domyślna to 60 sekund (jedna minuta).

> [!NOTE]
> Jeśli pierwsze logowanie po blokadzie również zakończy się niepowodzeniem, konto zostanie ponownie zablokowane. Jeśli konto blokuje się wielokrotnie, zwiększa się czas trwania blokady.

![Dostosowywanie zasad inteligentnej blokady usługi Azure AD w witrynie Azure portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak ustalić, czy funkcja inteligentnej blokady działa, czy nie

Po wyzwoleniu progu inteligentnej blokady zostanie wyświetlony następujący komunikat, gdy konto jest zablokowane:

**Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później, a jeśli nadal masz problemy, skontaktuj się z administratorem.**

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak zablokować złe hasła w organizacji przy użyciu usługi Azure AD.](howto-password-ban-bad.md)
* [Skonfiguruj samoobsługowe resetowanie hasła, aby umożliwić użytkownikom odblokowanie własnych kont.](quickstart-sspr.md)
