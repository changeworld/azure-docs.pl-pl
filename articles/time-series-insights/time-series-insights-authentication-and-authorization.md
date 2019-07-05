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
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543990"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja dla interfejsu API usługi Azure czas serii szczegółowych informacji

W tym dokumencie opisano, jak zarejestrować aplikację w usłudze Azure Active Directory przy użyciu nowego bloku Azure Active Directory. Aplikacje zarejestrowane w usłudze Azure Active Directory pozwalają użytkownikom na uwierzytelnianie i być autoryzowane do używania interfejsu API platformy Azure czas serii Insight skojarzony ze środowiskiem usługi Time Series Insights.

## <a name="service-principal"></a>Nazwa główna usługi

Poniżej opisano sposób konfigurowania aplikacji na dostęp do interfejsu API usługi Insights serii czasu imieniu aplikacji. Aplikacja może, a następnie wysyłać zapytania lub publikować dane referencyjne w środowisku usługi Time Series Insights przy użyciu poświadczeń własnej aplikacji, za pomocą usługi Azure Active Directory.

## <a name="summary-and-best-practices"></a>Podsumowanie i najlepszych rozwiązań

Przepływu rejestracji aplikacji w usłudze Azure Active Directory obejmuje trzy główne kroki.

1. [Rejestrowanie aplikacji](#azure-active-directory-app-registration) w usłudze Azure Active Directory.
1. Autoryzuj aplikację, aby [dostęp do danych do środowiska usługi Time Series Insights](#granting-data-access).
1. Użyj **identyfikator aplikacji** i **klucz tajny klienta** do pobrania tokenu z `https://api.timeseries.azure.com/` w swojej [aplikację kliencką](#client-app-initialization). Tokenu można następnie wywołać interfejs API usługi Insights serii czasu.

Na **kroku 3**, oddzielając poświadczeń użytkownika i aplikacji umożliwia:

* Przypisywanie uprawnień do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone do tylko co wymaga aplikacja. Na przykład można zezwolić aplikacji na odczytywanie danych tylko z określonym środowiskiem usługi Time Series Insights.
* Izolowanie aplikacji zabezpieczenia od tworzenia użytkownika poświadczeń uwierzytelniania przy użyciu **klucz tajny klienta** lub certyfikatu zabezpieczeń. W rezultacie poświadczenia aplikacji nie są zależne od poświadczeń określonego użytkownika. Jeśli zmieni się rolę użytkownika, aplikacja nie wymaga nowych poświadczeń lub dalszej konfiguracji. Jeśli użytkownik zmieni swoje hasło, dostęp do aplikacji nie wymaga nowych poświadczeń ani kluczy.
* Uruchamianie skryptu instalacji nienadzorowanej przy użyciu **klucz tajny klienta** lub certyfikatu zabezpieczeń, zamiast poświadczeń określonego użytkownika (konieczności istnieć).
* Zabezpieczenie dostępu do interfejsu API Azure czas serii szczegółowe informacje, należy użyć certyfikatu zabezpieczeń, zamiast hasła.

> [!IMPORTANT]
> Postępuj zgodnie z zasadą **oddzielenie obaw** (opisanych w tym scenariuszu powyżej) podczas konfigurowania zasad zabezpieczeń usługi Azure Time Series Insights.

> [!NOTE]
> * Artykuł koncentruje się na aplikacji pojedynczej dzierżawy, w którym aplikacja jest przeznaczona do uruchamiania w tylko jednej z organizacji.
> * Zazwyczaj użyjesz aplikacji pojedynczej dzierżawy aplikacji line-of-business, które działają w Twojej organizacji.

## <a name="detailed-setup"></a>Szczegółowe ustawienia

### <a name="azure-active-directory-app-registration"></a>Rejestracja aplikacji w usłudze Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udzielanie dostępu do danych

1. Środowiska usługi Time Series Insights wybierz **zasady dostępu do danych** i wybierz **Dodaj**.

   [![Dodaj nowe zasady dostępu do danych do środowiska usługi Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W **Wybieranie: Użytkownicy** okna dialogowego pole, Wklej albo **Nazwa aplikacji** lub **identyfikator aplikacji** z sekcji rejestracji aplikacji usługi Azure Active Directory.

   [![Znajdowanie aplikacji w oknie dialogowym Wybierz użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę. Wybierz **czytnika** przesyłać zapytania dotyczące danych lub **Współautor** do wysyłania zapytań dotyczących danych i zmianę danych referencyjnych. Kliknij przycisk **OK**.

   [![Wybierz czytelnika lub współautora w oknie dialogowym Wybierz rolę użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, wybierając **OK**.

   > [!TIP]
   > Przeczytaj o [udzielanie dostępu do danych](./time-series-insights-data-access.md) do środowiska usługi Time Series Insights w usłudze Azure Active Directory.

### <a name="client-app-initialization"></a>Inicjowanie aplikacji klienta

1. Użyj **identyfikator aplikacji** i **klucz tajny klienta** (klucz aplikacji) w sekcji rejestracji aplikacji usługi Azure Active Directory można uzyskać tokenu w imieniu aplikacji.

    W C#, poniższy kod może uzyskać tokenu w imieniu aplikacji. Aby uzyskać pełny przykład, zobacz [wykonywanie zapytań o dane przy użyciu języka C#](time-series-insights-query-data-csharp.md).

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

1. Następnie można przekazać token `Authorization` nagłówka, gdy aplikacja wywołuje interfejs API Insights serii czasu.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać przykładowy kod, który wywołuje interfejs API GA czas serii szczegółowych informacji, zobacz [kwerendy danych przy użyciu C# ](./time-series-insights-query-data-csharp.md).

- Aby uzyskać przykłady kodu interfejsu API usługi Insights (wersja zapoznawcza) czas serii, zobacz [podglądu zapytania danych przy użyciu C# ](./time-series-insights-update-query-data-csharp.md).

- Aby uzyskać informacje referencyjne interfejsu API, zobacz [dokumentację interfejsu API zapytań](/rest/api/time-series-insights/ga-query-api).

- Dowiedz się, jak [utworzyć nazwę główną usługi](../active-directory/develop/howto-create-service-principal-portal.md).