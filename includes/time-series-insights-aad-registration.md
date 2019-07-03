---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 07/02/2019
ms.openlocfilehash: a463e3cf475909c34054717460dc10dbba4ad8f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543951"
---
> [!IMPORTANT]
> * Nowy **usługi Azure Active Directory** > **rejestracje aplikacji** bloku zastępuje starszego **usługi Azure Active Directory** > **aplikacji rejestracje (starsza wersja)** bloku maja 2019 r.
> * Rejestracje aplikacji utworzone lub wyświetlanych w bloku starszej wersji zostanie automatycznie wyświetlona w nowym bloku.
> * Aby uzyskać szczegółowe informacje na temat migracji do nowego środowiska rejestracji aplikacji platformy Azure, przeczytaj [rejestracje aplikacji platformy Azure, szkolenia przewodnik](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) i [szybkiego startu platformy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. W [witryny Azure portal](https://ms.portal.azure.com/), wybierz opcję **usługi Azure Active Directory** > **rejestracje aplikacji** > **nowej rejestracji**.

   [![Rejestrowanie nowej aplikacji w usłudze Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Nowy panel rejestracji aplikacji usługi Azure Active Directory umożliwia filtrowanie wyświetlanych aplikacji, wybierając **należące do aplikacji**.

    Aplikację zostaną wyświetlone tutaj po zarejestrowaniu.

1. Nadaj aplikacji nazwę i wybierz pozycję **kont w tym katalogu organizacji tylko** do określenia **obsługiwane typy kont** , mogą uzyskiwać dostęp do interfejsu API. Wybierz prawidłowy identyfikator URI przekierowywania użytkowników po ich uwierzytelniania, następnie **zarejestrować**.

   [![Tworzenie aplikacji w usłudze Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Ważne informacje o aplikacji Azure Active Directory jest wyświetlany w aplikacji listy **Przegląd** bloku. Wybierz swoją aplikację w obszarze **należące do aplikacji**, następnie **Przegląd**.

   [![Skopiuj identyfikator aplikacji](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopiuj swoje **identyfikator aplikacji (klienta)** do użycia w aplikacji klienckiej.

1. **Uwierzytelniania** bloku określa ustawienia konfiguracyjne ważne uwierzytelniania. 

    1. **Identyfikatory URI przekierowania** musi odpowiadać adresowi dostarczonych przez żądanie uwierzytelnienia:

        * W przypadku aplikacji hostowanej w lokalne Środowisko deweloperskie, wybierz **klientem publicznym (mobilnych i klasycznych)** . Upewnij się ustawić **domyślny typ klienta** tak.
        * W przypadku aplikacji jednostronicowej hostowanych w usłudze Azure App Service, wybierz **Web**.

    1. Włącz przepływ przyznawanie niejawne, sprawdzając **tokeny Identyfikatora**.

   [![Utwórz nowy wpis tajny klienta](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Kliknij pozycję **Zapisz**.

1. Kojarzenie z usługi Azure Active Directory aplikacji usługi Azure TIme Series Insights. Wybierz **uprawnienia do interfejsu API** > **Dodaj uprawnienia** > **Moja organizacja korzysta z interfejsów API**. 

    [![Kojarzenie interfejsu API z aplikacji usługi Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` do wyszukiwania paska wybierz `Azure Time Series Insights`.

1. Następnie określ rodzaj uprawnień interfejsu API, których wymaga aplikacja. Domyślnie **delegowane uprawnienia** zostaną wyróżnione. Następnie wybierz typ uprawnienia, wybierz **Dodaj uprawnienia**.

    [![Określ rodzaj uprawnień wymaganych przez aplikację interfejsu API](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)