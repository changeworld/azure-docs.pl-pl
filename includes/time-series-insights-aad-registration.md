---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984593"
---
1. W [witrynie Azure portal](https://ms.portal.azure.com/)wybierz pozycję**Rejestracje** > aplikacji **usługi Azure Active Directory** > **Nowa rejestracja**.

   [![Rejestracja nowych aplikacji w usłudze Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Twoja aplikacja zostanie wyświetlona tutaj po jej zarejestrowaniu.

1. Nadaj aplikacji nazwę i wybierz **konta w tym katalogu organizacyjnym tylko** w celu określenia typów **obsługiwanych kont,** które mogą uzyskiwać dostęp do interfejsu API. Wybierz prawidłowy identyfikator URI, do których użytkownicy będą przekierowywać po uwierzytelniić, a następnie **zarejestruj**.

   [![Tworzenie aplikacji w usłudze Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Ważne informacje o aplikacji usługi Azure Active Directory są wyświetlane w załączniku **Przegląd** aplikacji na liście. Wybierz aplikację w obszarze **Posiadane aplikacje**, a następnie **Przegląd**.

   [![Kopiowanie identyfikatora aplikacji](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Skopiuj **identyfikator aplikacji (klienta)** do użycia w aplikacji klienckiej.

1. **Blok Uwierzytelniania** określa ważne ustawienia konfiguracji uwierzytelniania. 

    1. Dodaj **identyfikatory URI przekierowania** i skonfiguruj **tokeny dostępu,** wybierając **pozycję + Dodaj platformę**.

    1. Określ, czy aplikacja jest **klientem publicznym,** czy nie, wybierając **opcję Tak** lub **Nie**.

    1. Sprawdź, które konta i dzierżawcy są obsługiwane.

    [![Konfigurowanie dotacji niejawnej](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Po wybraniu odpowiedniej platformy należy skonfigurować **identyfikatory URI przekierowania** i **tokeny dostępu** w panelu bocznym po prawej stronie interfejsu użytkownika.

    1. **Przekierowanie identyfikatorów URI** musi być zgodne z adresem podanym przez żądanie uwierzytelnienia:

        * W przypadku aplikacji hostowanych w lokalnym środowisku programistycznym wybierz pozycję **Klient publiczny (mobilny & komputer)**. Upewnij się, że **klient publiczny** jest ustawiony na **Tak**.
        * W przypadku aplikacji jednostronicowych hostowanych w usłudze Azure App Service wybierz pozycję **Web**.

    1. Określ, czy **adres URL wylogowania** jest odpowiedni.

    1. Włącz niejawny przepływ dotacji, sprawdzając **tokeny programu Access** lub **tokeny identyfikatorów**.

    [![Tworzenie identyfikatorów URI przekierowania](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Kliknij **pozycję Konfiguruj**, a następnie **pozycję Zapisz**.

1. Wybierz **certyfikaty & wpisów tajnych,** a następnie **nowy klucz tajny klienta,** aby utworzyć hasło aplikacji, którego aplikacja kliencka może użyć do udowodnienia swojej tożsamości.

   [![Tworzenie nowego klucza tajnego klienta](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Następnie zostanie wyświetlone tajne hasło klienta. Skopiuj klucz do ulubionego edytora tekstu.

   > [!NOTE]
   > Zamiast tego można zaimportować certyfikat. Ze względów zwiększonych zabezpieczeń zaleca się certyfikat. Aby użyć certyfikatu, wybierz pozycję **Przekaż certyfikat**.

1. Skojarz aplikację usługi Azure Active Directory usługi Azure TIme Series Insights. Wybierz **uprawnienia** > interfejsu API Dodaj interfejsy API**uprawnień,** > **których używa moja organizacja**. 

    [![Kojarzenie interfejsu API z aplikacją usługi Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Wpisz `Azure Time Series Insights` na pasku `Azure Time Series Insights`wyszukiwania, a następnie wybierz opcję .

1. Następnie określ rodzaj uprawnienia interfejsu API, jakiego wymaga aplikacja. Domyślnie **uprawnienia delegowane zostaną wyróżnione.** Następnie wybierz typ uprawnienia, wybierz pozycję **Dodaj uprawnienia**.

    [![Określ rodzaj uprawnień interfejsu API, jakiej wymaga aplikacja](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
