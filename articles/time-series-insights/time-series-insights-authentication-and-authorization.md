---
title: Uwierzytelnianie i autoryzowanie za pomocą interfejsu API w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 876f24581badb20e01271f88cb9b51b470718721
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164539"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja dla interfejsu API usługi Azure czas serii szczegółowych informacji

W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji używany w niestandardowych aplikacji, która wywołuje interfejs API Azure czas serii szczegółowych informacji.

> [!TIP]
> Przeczytaj o [udzielanie dostępu do danych](./time-series-insights-data-access.md) do środowiska usługi Time Series Insights w usłudze Azure Active Directory.

## <a name="service-principal"></a>Nazwa główna usługi

Poniżej opisano sposób konfigurowania aplikacji dostęp do interfejsu API usługi Insights serii czasu dla aplikacji. Aplikację można następnie zapytania lub publikować dane referencyjne w środowisku usługi Time Series Insights przy użyciu poświadczeń aplikacji, a nie poświadczeń użytkownika.

## <a name="best-practices"></a>Najlepsze praktyki

Jeśli masz aplikację, która musi czas dostępu Series Insights:

1. Konfigurowanie aplikacji usługi Azure Active Directory.
1. [Przypisz zasady dostępu do danych](./time-series-insights-data-access.md) w środowisku usługi Time Series Insights.

Przy użyciu poświadczeń aplikacji, a nie poświadczeń użytkownika jest pożądane, ponieważ:

* Możesz przypisywać uprawnienia do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone do tylko co wymaga aplikacja. Na przykład można zezwolić aplikacji na odczytywanie tylko dane w konkretnym środowisku usługi Time Series Insights.
* Nie trzeba zmienić poświadczenia aplikacji, w przypadku zmiany Twoje obowiązki.
* Aby zautomatyzować uwierzytelniania, po uruchomieniu skryptu instalacji nienadzorowanej, można użyć certyfikatu lub klucza aplikacji.

Poniższe sekcje pokazują, jak wykonać te kroki w witrynie Azure portal. Artykuł koncentruje się na aplikacji pojedynczej dzierżawy, w którym aplikacja jest przeznaczona do uruchamiania w tylko jednej z organizacji. Aplikacje z jedną dzierżawą jest zazwyczaj używana dla aplikacji line-of-business, które działają w Twojej organizacji.

## <a name="setup-summary"></a>Podsumowanie instalacji

Przepływ konfiguracji składa się z trzech kroków:

1. Tworzenie aplikacji w usłudze Azure Active Directory.
1. Autoryzuj tę aplikację, aby uzyskiwać dostęp do środowiska usługi Time Series Insights.
1. Użyj klucza i identyfikator aplikacji w celu pobrania tokenu z `https://api.timeseries.azure.com/`. Token następnie może służyć do wywołania interfejsu API usługi Insights serii czasu.

## <a name="detailed-setup"></a>Szczegółowe ustawienia

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**.

   [![Rejestrowanie nowej aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Nadaj aplikacji nazwę, wybierz typ jako **aplikacji sieci Web / interfejs API**, wybierz dowolny prawidłowy identyfikator URI dla **adres URL logowania**i wybierz **Utwórz**.

   [![Tworzenie aplikacji w usłudze Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Wybierz nowo utworzoną aplikację i skopiuj jej identyfikator aplikacji na swoim ulubionym edytorze tekstów.

   [![Skopiuj identyfikator aplikacji](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Wybierz **klucze**, wprowadź nazwę klucza, wybierz czas wygaśnięcia, a następnie wybierz **Zapisz**.

   [![Wybierz klucze aplikacji](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Wprowadź nazwę klucza i wygaśnięcia i wybierz opcję Zapisz](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Skopiuj klucz z ulubionego edytora tekstu.

   [![Skopiuj klucz aplikacji](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Środowiska usługi Time Series Insights wybierz **zasady dostępu do danych** i wybierz **Dodaj**.

   [![Dodaj nowe zasady dostępu do danych do środowiska usługi Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W **Wybieranie: Użytkownicy** okno dialogowe, Wklej nazwę aplikacji, w kroku 2 lub identyfikator aplikacji z kroku 3.

   [![Znajdowanie aplikacji w oknie dialogowym Wybierz użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę. Wybierz **czytnika** przesyłać zapytania dotyczące danych lub **Współautor** do wysyłania zapytań dotyczących danych i zmianę danych referencyjnych. Kliknij przycisk **OK**.

   [![Wybierz czytelnika lub współautora w oknie dialogowym Wybierz rolę użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, wybierając **OK**.

1. Można uzyskać tokenu dla aplikacji, należy użyć Identyfikatora aplikacji z kroku 3 i klucz aplikacji z kroku 5. Token może być następnie przekazywany w `Authorization` nagłówka, gdy aplikacja wywołuje interfejs API Insights serii czasu.

    Jeśli używasz C#, można użyć następującego kodu, można uzyskać tokenu dla aplikacji. Aby uzyskać pełny przykład, zobacz [wykonywanie zapytań o dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

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
