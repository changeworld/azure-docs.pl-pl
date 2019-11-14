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
ms.date: 09/24/2019
ms.openlocfilehash: 1ed57e30273c9ba3feb110e0e5acbd3bcc7ffd40
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74062909"
---
1. W [Azure Portal](https://ms.portal.azure.com/)wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji** > **nową rejestrację**.

   [![rejestrację nowej aplikacji w Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Aplikacja zostanie wyświetlona w tym miejscu po zarejestrowaniu.

1. Nadaj aplikacji nazwę i wybierz **konta w tym katalogu organizacji tylko w** celu określenia **obsługiwanych typów kont** , które mogą uzyskiwać dostęp do interfejsu API. Wybierz prawidłowy identyfikator URI, aby przekierować użytkowników po ich uwierzytelnieniu, a następnie **zarejestruj**się.

   [![utworzyć aplikację w Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Ważne informacje o aplikacji Azure Active Directory są wyświetlane w bloku **Przegląd** aplikacji na liście. Wybierz swoją aplikację w obszarze **posiadane aplikacje**, a następnie pozycję **Przegląd**.

   [![skopiować identyfikatora aplikacji](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Skopiuj **Identyfikator aplikacji (klienta)** do użycia w aplikacji klienckiej.

1. Blok **uwierzytelnianie** określa ważne ustawienia konfiguracji uwierzytelniania. 

    1. **Identyfikatory URI przekierowania** muszą być zgodne z adresem podanym w żądaniu uwierzytelniania:

        * W przypadku aplikacji hostowanych w lokalnym środowisku programistycznym wybierz pozycję **Klient publiczny (mobile & Desktop)** . Upewnij się, że dla **domyślnego typu klienta** ustawiono wartość tak.
        * W przypadku aplikacji jednostronicowych hostowanych w Azure App Service wybierz pozycję **Sieć Web**.

    1. Włącz przepływ niejawnego udzielenia przez sprawdzenie **tokenów identyfikatorów**.

   [![utworzyć nowego klucza tajnego klienta](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Kliknij pozycję **Zapisz**.

1. Wybierz kolejno pozycje **certyfikaty &** wpisy tajne i **Nowy wpis tajny klienta** , aby utworzyć hasło aplikacji, za pomocą których klient może potwierdzić swoją tożsamość.

   [![utworzyć nowego klucza tajnego klienta](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Zostanie wyświetlone hasło klucza tajnego klienta. Skopiuj klucz do ulubionego edytora tekstu.

   > [!NOTE]
   > Możesz zamiast tego zaimportować certyfikat. W celu zwiększenia bezpieczeństwa zaleca się używanie certyfikatu. Aby użyć certyfikatu, wybierz opcję **Przekaż certyfikat**.

1. Skojarz aplikację Azure Active Directory usługą Azure TIme Series Insights. Wybierz **uprawnienia interfejsu api** > **Dodaj uprawnienia** > **interfejsów API używanych przez moją organizację**. 

    [![skojarzyć interfejs API z aplikacją Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Wpisz `Azure Time Series Insights` na pasku wyszukiwania, a następnie wybierz pozycję `Azure Time Series Insights`.

1. Następnie określ rodzajowe uprawnienia interfejsu API wymagane przez aplikację. Domyślnie zostaną wyróżnione **uprawnienia delegowane** . Wybierz typ uprawnienia, a następnie wybierz pozycję **Dodaj uprawnienia**.

    [![określić rodzaj uprawnień interfejsu API wymaganych przez aplikację](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)