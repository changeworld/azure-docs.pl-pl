---
title: Jak uwierzytelnianie i autoryzacja w interfejsie API w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6cd993e9f6c6dbf173c161de638c6c4a8b18d3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237051"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja dla interfejsu API usługi Azure czas serii szczegółowych informacji

W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji używany w niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.

> [!TIP]
> Przeczytaj o [udzielanie dostępu do danych](./time-series-insights-data-access.md) do środowiska usługi Time Series Insights w usłudze Azure Active Directory.

## <a name="service-principal"></a>Nazwa główna usługi

To oraz następujące sekcje zawierają instrukcje dotyczące konfigurowania aplikacji na dostęp do interfejsu API usługi Insights serii czasu imieniu aplikacji. Aplikację można następnie zapytania lub publikować dane referencyjne w środowisku usługi Time Series Insights przy użyciu poświadczeń aplikacji, a nie poświadczeń użytkownika.

## <a name="best-practices"></a>Najlepsze praktyki

Jeśli masz aplikację, która musi czas dostępu Series Insights:

1. Konfigurowanie aplikacji usługi Azure Active Directory.
1. [Przypisz zasady dostępu do danych](./time-series-insights-data-access.md) w środowisku usługi Time Series Insights.

Korzystanie z aplikacji, a nie poświadczeń użytkownika jest pożądane, ponieważ:

* Możesz przypisywać uprawnienia do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone do tylko co wymaga aplikacja. Na przykład można zezwolić aplikacji na odczytywanie tylko dane w konkretnym środowisku usługi Time Series Insights.
* Nie trzeba zmienić poświadczenia aplikacji, w przypadku zmiany Twoje obowiązki.
* Aby zautomatyzować uwierzytelniania, po uruchomieniu skryptu instalacji nienadzorowanej, można użyć certyfikatu lub klucza aplikacji.

Poniższe sekcje pokazują, jak wykonać te kroki w witrynie Azure portal. Artykuł koncentruje się na aplikacji pojedynczej dzierżawy, w którym aplikacja jest przeznaczona do uruchamiania w tylko jednej z organizacji. Zazwyczaj użyjesz aplikacji pojedynczej dzierżawy aplikacji line-of-business, które działają w Twojej organizacji.

## <a name="set-up-summary"></a>Konfigurowanie podsumowanie

Przepływ konfiguracji składa się z trzech kroków:

1. Tworzenie aplikacji w usłudze Azure Active Directory.
1. Autoryzuj tę aplikację, aby uzyskiwać dostęp do środowiska usługi Time Series Insights.
1. Użyj klucza i identyfikator aplikacji w celu pobrania tokenu z `https://api.timeseries.azure.com/`. Tokenu można następnie wywołać interfejs API usługi Insights serii czasu.

## <a name="detailed-setup"></a>Szczegółowe ustawienia

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

   [![Rejestrowanie nowej aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Nadaj aplikacji nazwę, wybierz typ jako **aplikacji sieci Web / interfejs API**, wybierz dowolny prawidłowy identyfikator URI dla **adres URL logowania**i kliknij przycisk **Utwórz**.

   [![Tworzenie aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Wybierz nowo utworzoną aplikację i skopiuj jej identyfikator aplikacji na swoim ulubionym edytorze tekstów.

   [![Skopiuj identyfikator aplikacji](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Wybierz **klucze**, wprowadź nazwę klucza, wybierz czas wygaśnięcia, a następnie kliknij przycisk **Zapisz**.

   [![Wybierz klucze aplikacji](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Wprowadź nazwę klucza i wygaśnięcia, a następnie kliknij przycisk Zapisz](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Skopiuj klucz z ulubionego edytora tekstu.

   [![Skopiuj klucz aplikacji](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Środowiska usługi Time Series Insights wybierz **zasady dostępu do danych** i kliknij przycisk **Dodaj**.

   [![Dodaj nowe zasady dostępu do danych do środowiska usługi Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W **Wybieranie: Użytkownicy** okna dialogowego pole, Wklej nazwę aplikacji (z **kroku 2**) lub identyfikator aplikacji (z **kroku 3**).

   [![Znajdowanie aplikacji w oknie dialogowym Wybierz użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę (**czytnika** na wykonywanie zapytań o dane, **Współautor** wykonywanie zapytań o dane i modyfikowania danych referencyjnych) i kliknij przycisk **OK**.

   [![Wybierz w oknie dialogowym Wybierz rolę czytelnika lub współautora](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, klikając **OK**.

1. Identyfikator aplikacji (z **kroku 3**) i klucz aplikacji (z **kroku 5**) można uzyskać tokenu w imieniu aplikacji. Następnie można przekazać token `Authorization` nagłówka, gdy aplikacja wywołuje interfejs API Insights serii czasu.

    Jeśli używasz języka C#, można użyć następującego kodu, można uzyskać tokenu w imieniu aplikacji. Aby uzyskać pełny przykład, zobacz [wykonywanie zapytań o dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Użyj **identyfikator aplikacji** i **klucza** w aplikacji do uwierzytelniania za pomocą usługi Azure Time Series Insights.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przykładowy kod, który wywołuje interfejs API czas serii szczegółowych informacji, zobacz [wykonywanie zapytań o dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

- Aby uzyskać informacje referencyjne interfejsu API, zobacz [dokumentację interfejsu API zapytań](/rest/api/time-series-insights/ga-query-api).

- Dowiedz się, jak [utworzyć nazwę główną usługi](../active-directory/develop/howto-create-service-principal-portal.md).
