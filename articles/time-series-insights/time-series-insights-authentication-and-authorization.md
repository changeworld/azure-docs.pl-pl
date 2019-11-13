---
title: Uwierzytelnianie i autoryzacja interfejsu API — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla aplikacji niestandardowej, która wywołuje interfejs API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 37a409ab28728fe40c5f054d5e9a40cb20774450
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007107"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja interfejsu API Azure Time Series Insights

W tym dokumencie opisano sposób rejestrowania aplikacji w Azure Active Directory przy użyciu bloku nowy Azure Active Directory. Aplikacje zarejestrowane w Azure Active Directory umożliwiają użytkownikom uwierzytelnianie w usłudze i Zezwalanie na korzystanie z interfejsu API usługi Azure Time Series Insights skojarzonego ze środowiskiem Time Series Insights.

## <a name="service-principal"></a>Nazwa główna usługi

W poniższych sekcjach opisano sposób konfigurowania aplikacji w celu uzyskiwania dostępu do interfejsu API Time Series Insights w imieniu aplikacji. Aplikacja może następnie badać lub publikować dane referencyjne w środowisku Time Series Insights przy użyciu własnych poświadczeń aplikacji za pośrednictwem Azure Active Directory.

## <a name="summary-and-best-practices"></a>Podsumowanie i najlepsze rozwiązania

Przepływ rejestracji aplikacji Azure Active Directory obejmuje trzy główne kroki.

1. [Zarejestruj aplikację](#azure-active-directory-app-registration) w Azure Active Directory.
1. Autoryzuj aplikację w taki sposób, aby miała [dostęp do danych środowiska Time Series Insights](#granting-data-access).
1. Użyj **identyfikatora aplikacji** i **klucza tajnego klienta** , aby uzyskać token z `https://api.timeseries.azure.com/` w [aplikacji klienckiej](#client-app-initialization). Tokenu można następnie użyć do wywołania interfejsu API Time Series Insights.

Na **krok 3**oddzielenie poświadczeń aplikacji i użytkownika pozwala:

* Przypisywanie uprawnień do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone tylko do wymaganej aplikacji. Można na przykład zezwolić aplikacji na odczytywanie danych tylko z określonego środowiska Time Series Insights.
* Odizolowanie zabezpieczeń aplikacji od tworzenia poświadczeń uwierzytelniania użytkownika przy użyciu **klucza tajnego klienta** lub certyfikatu zabezpieczeń. W związku z tym poświadczenia aplikacji nie są zależne od poświadczeń określonego użytkownika. Jeśli rola użytkownika ulegnie zmianie, aplikacja nie musi wymagać nowych poświadczeń lub dalszej konfiguracji. Jeśli użytkownik zmieni hasło, cały dostęp do aplikacji nie wymaga nowych poświadczeń ani kluczy.
* Uruchom skrypt nienadzorowany przy użyciu **klucza tajnego klienta** lub certyfikatu zabezpieczeń, a nie poświadczeń określonego użytkownika (wymaganie ich obecności).
* Użyj certyfikatu zabezpieczeń zamiast hasła, aby zabezpieczyć dostęp do interfejsu API Azure Time Series Insights.

> [!IMPORTANT]
> Postępuj zgodnie z zasadami **rozdzielenia problemów** (opisanymi w tym scenariuszu) podczas konfigurowania zasad zabezpieczeń Azure Time Series Insights.

> [!NOTE]
> * Artykuł koncentruje się na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do działania tylko w jednej organizacji.
> * Zwykle używasz aplikacji z jedną dzierżawą dla aplikacji biznesowych, które działają w organizacji.

## <a name="detailed-setup"></a>Szczegółowa konfiguracja

### <a name="azure-active-directory-app-registration"></a>Rejestracja aplikacji Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udzielanie dostępu do danych

1. Dla środowiska Time Series Insights wybierz pozycję **zasady dostępu do danych** i wybierz pozycję **Dodaj**.

   [![dodać nowe zasady dostępu do danych do środowiska Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W oknie dialogowym **Wybieranie użytkownika** wklej **nazwę aplikacji** lub **Identyfikator aplikacji** z sekcji Azure Active Directory rejestracji aplikacji.

   [![znaleźć aplikacji w oknie dialogowym Wybieranie użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę. Wybierz opcję **czytelnik** , aby wykonać zapytanie dotyczące danych lub **współautora** , aby wykonać zapytanie o dane i zmienić dane referencyjne Kliknij przycisk **OK**.

   [![Wybieranie czytnika lub współautora w oknie dialogowym Wybieranie roli użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, wybierając **przycisk OK**.

   > [!TIP]
   > Przeczytaj o [udzielaniu dostępu do danych](./time-series-insights-data-access.md) w środowisku Time Series Insights w programie Azure Active Directory.

### <a name="client-app-initialization"></a>Inicjowanie aplikacji klienta

1. Użyj **identyfikatora aplikacji** i klucza **tajnego klienta** (klucza aplikacji) z sekcji Azure Active Directory rejestracji aplikacji, aby uzyskać token w imieniu aplikacji.

    W C#programie Poniższy kod może uzyskać token w imieniu aplikacji. Aby uzyskać pełny przykład, zobacz [dane zapytania przy C#użyciu ](time-series-insights-query-data-csharp.md).

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

1. Token można następnie przesłać do nagłówka `Authorization`, gdy aplikacja wywołuje interfejs API Time Series Insights.

## <a name="common-headers-and-parameters"></a>Wspólne nagłówki i parametry

W tej sekcji opisano typowe nagłówki i parametry żądań HTTP służące do wykonywania zapytań dotyczących Time Series Insights GA i Podgląd interfejsów API. Wymagania dotyczące interfejsu API są szczegółowo opisane w [dokumentacji dotyczącej interfejsu API REST Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Authentication

Aby wykonać uwierzytelnione zapytania dotyczące [Time Series Insights interfejsów API REST](https://docs.microsoft.com/rest/api/time-series-insights/), należy przesłać prawidłowy token okaziciela OAuth 2,0 w [nagłówku autoryzacji](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) za pomocą wybranego przez siebie klienta REST (Poster, JavaScript, C#). 

> [!IMPORTANT]
> Token musi być wystawiony dokładnie dla `https://api.timeseries.azure.com/` zasobu (znany również jako "odbiorcy" tokenu).
> * Z tego względu Twój **AuthURLcy** Twojego [ogłaszania](https://www.getpostman.com/) jest zgodny z: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Zapoznaj się z [przykładową wizualizacją zestawu SDK klienta](https://tsiclientsample.azurewebsites.net/) hostowanego Azure Time Series Insights, aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API Time Series Insights programowo przy użyciu [zestawu SDK klienta języka JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) , a także wykresów

### <a name="http-headers"></a>Nagłówki HTTP

Wymagane nagłówki żądania:

- `Authorization` uwierzytelniania i autoryzacji, należy przesłać prawidłowy token okaziciela OAuth 2,0 w nagłówku autoryzacji. Token musi być wystawiony dokładnie dla `https://api.timeseries.azure.com/` zasobu (znany również jako "odbiorcy" tokenu).

Opcjonalne nagłówki żądań:

- Obsługiwane są `application/json` tylko `Content-type`.
- `x-ms-client-request-id` — identyfikator żądania klienta. Usługa rejestruje tę wartość. Umożliwia usłudze śledzenie operacji między usługami.
- `x-ms-client-session-id` — identyfikator sesji klienta. Usługa rejestruje tę wartość. Umożliwia usłudze Śledzenie grupy powiązanych operacji między usługami.
- `x-ms-client-application-name` — nazwa aplikacji, która wygenerowała to żądanie. Usługa rejestruje tę wartość.

Nagłówki odpowiedzi:

- Obsługiwane są `application/json` tylko `Content-type`.
- Identyfikator żądania wygenerowany przez serwer `x-ms-request-id`. Można go użyć do skontaktowania się z firmą Microsoft w celu zbadania żądania.

### <a name="http-parameters"></a>Parametry HTTP

Parametry ciągu zapytania wymaganego adresu URL:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Opcjonalne parametry ciągu zapytania URL:

- `timeout=<timeout>` — limit czasu po stronie serwera dla wykonywania żądania. Dotyczy tylko [zdarzeń Get Environment](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) i [Get Environment](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API. Wartość limitu czasu powinna mieć format czasu trwania ISO 8601, na przykład `"PT20S"` i powinna znajdować się w zakresie `1-30 s`. Wartość domyślna to `30 s`.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przykładowym kodem, który wywołuje interfejs API usługi GA Time Series Insights, zobacz [dane zapytania przy użyciu C# ](./time-series-insights-query-data-csharp.md).

- Przykłady kodu interfejsu API Time Series Insights w wersji zapoznawczej znajdują się w temacie [Query Preview Data using C# ](./time-series-insights-update-query-data-csharp.md).

- Informacje referencyjne dotyczące interfejsu API znajdują się w temacie [Dokumentacja interfejsu API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Dowiedz się, jak [utworzyć jednostkę usługi](../active-directory/develop/howto-create-service-principal-portal.md).