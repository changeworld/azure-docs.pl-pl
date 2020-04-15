---
title: Uwierzytelnianie i autoryzacja interfejsu API — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla niestandardowej aplikacji, która wywołuje interfejs API usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380804"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights

W tym dokumencie opisano sposób rejestrowania aplikacji w usłudze Azure Active Directory przy użyciu nowego bloku usługi Azure Active Directory. Aplikacje zarejestrowane w usłudze Azure Active Directory umożliwiają użytkownikom uwierzytelnianie i autoryzowanie do korzystania z interfejsu API usługi Azure Time Series Insight skojarzonego ze środowiskiem usługi Time Series Insights.

> [!IMPORTANT]
> Usługa Azure Time Series Insights obsługuje obie następujące biblioteki uwierzytelniania:
> * Nowsza [biblioteka uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Biblioteka uwierzytelniania usługi Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Jednostka usługi

W poniższych sekcjach opisano sposób konfigurowania aplikacji w celu uzyskania dostępu do interfejsu API usługi Time Series Insights w imieniu aplikacji. Aplikacja może następnie kwerendy lub publikowania danych referencyjnych w środowisku usługi Time Series Insights przy użyciu własnych poświadczeń aplikacji za pośrednictwem usługi Azure Active Directory.

## <a name="summary-and-best-practices"></a>Podsumowanie i najlepsze praktyki

Przepływ rejestracji aplikacji usługi Azure Active Directory obejmuje trzy główne kroki.

1. [Zarejestruj aplikację](#azure-active-directory-app-registration) w usłudze Azure Active Directory.
1. Autoryzuj aplikację, aby mieć [dostęp do danych do środowiska Time Series Insights](#granting-data-access).
1. Użyj **identyfikatora aplikacji** i **klucza tajnego klienta,** aby uzyskać token z `https://api.timeseries.azure.com/` aplikacji [klienckiej](#client-app-initialization). Token może następnie służyć do wywoływania interfejsu API usługi Time Series Insights.

W **kroku 3,** oddzielenie poświadczeń aplikacji i użytkownika umożliwia:

* Przypisz uprawnienia do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone tylko do tego, czego wymaga aplikacja. Na przykład można zezwolić aplikacji na odczytywanie danych tylko z określonego środowiska usługi Time Series Insights.
* Wyizolować zabezpieczenia aplikacji od poświadczeń uwierzytelniania użytkownika podczas tworzenia przy użyciu klucza **tajnego klienta** lub certyfikatu zabezpieczeń. W rezultacie poświadczenia aplikacji nie są zależne od poświadczeń określonego użytkownika. Jeśli zmieni się rola użytkownika, aplikacja nie musi wymagać nowych poświadczeń lub dalszej konfiguracji. Jeśli użytkownik zmieni swoje hasło, cały dostęp do aplikacji nie wymaga nowych poświadczeń ani kluczy.
* Uruchom skrypt nienadzorowany przy użyciu klucza **tajnego klienta** lub certyfikatu zabezpieczeń, a nie poświadczeń określonego użytkownika (wymagając, aby był obecny).
* Użyj certyfikatu zabezpieczeń, a nie hasła, aby zabezpieczyć dostęp do interfejsu API usługi Azure Time Series Insights.

> [!IMPORTANT]
> Podczas konfigurowania zasad zabezpieczeń usługi Azure Time Series Insights należy przestrzegać zasady **separacji problemów** (opisanej w tym scenariuszu powyżej).

> [!NOTE]
> * Artykuł koncentruje się na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do uruchamiania tylko w jednej organizacji.
> * Zazwyczaj będziesz używać aplikacji z jedną dzierżawą dla aplikacji biznesowych, które są uruchamiane w organizacji.

## <a name="detailed-setup"></a>Szczegółowa konfiguracja

### <a name="azure-active-directory-app-registration"></a>Rejestracja aplikacji usługi Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udzielanie dostępu do danych

1. W środowisku Usługi Time Series Insights wybierz pozycję **Zasady dostępu do danych** i wybierz pozycję **Dodaj**.

   [![Dodawanie nowych zasad dostępu do danych do środowiska Usługi Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W oknie dialogowym **Wybieranie użytkownika** wklej **nazwę aplikacji** lub **identyfikator aplikacji** z sekcji rejestracji aplikacji usługi Azure Active Directory.

   [![Znajdowanie aplikacji w oknie dialogowym Wybieranie użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę. Wybierz **opcję Czytelnik,** aby zbadać dane lub **współautor** kwerendy danych i zmienić dane referencyjne. Kliknij przycisk **OK**.

   [![Wybieranie czytnika lub współautora w oknie dialogowym Wybieranie roli użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, wybierając przycisk **OK**.

   > [!TIP]
   > Aby uzyskać zaawansowane opcje dostępu do danych, przeczytaj [artykuł przyznający dostęp do danych](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Inicjowanie aplikacji klienckiej

* Deweloperzy mogą używać [biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) lub [biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) do uwierzytelniania za pomocą usługi Azure Time Series Insights.

* Na przykład, aby uwierzytelnić się przy użyciu usługi ADAL:

   1. Użyj **identyfikatora aplikacji** i **klucza tajnego klienta** (klucz aplikacji) z sekcji rejestracji aplikacji usługi Azure Active Directory, aby uzyskać token w imieniu aplikacji.

   1. W języku C# poniższy kod można uzyskać token w imieniu aplikacji. Aby uzyskać pełną próbkę, przeczytaj [dane kwerendy przy użyciu języka C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Token można następnie przekazać `Authorization` w nagłówku, gdy aplikacja wywołuje interfejs API usługi Time Series Insights.

* Alternatywnie deweloperzy mogą wybrać uwierzytelnić przy użyciu msal. Przeczytaj o [migracji do usługi MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) i zobacz nasze [dane referencyjne zarządzania ga dla środowiska usługi Azure Time Series Insights przy użyciu c# artykuł,](time-series-insights-manage-reference-data-csharp.md) aby dowiedzieć się więcej. 

## <a name="common-headers-and-parameters"></a>Typowe nagłówki i parametry

W tej sekcji opisano typowe nagłówki żądań HTTP i parametry używane do wysyłania zapytań względem interfejsów GA i interfejsów API usługi Time Series Insights. Wymagania specyficzne dla interfejsu API są bardziej szczegółowe w [dokumentacji referencyjnej interfejsu API rest](https://docs.microsoft.com/rest/api/time-series-insights/)usługi Time Series Insights.

> [!TIP]
> Przeczytaj [odwołanie do interfejsu API rest platformy Azure,](https://docs.microsoft.com/rest/api/azure/) aby dowiedzieć się więcej na temat korzystania z interfejsów API REST, żądań HTTP i obsługi odpowiedzi HTTP.

### <a name="authentication"></a>Authentication

Aby wykonać uwierzytelnione zapytania względem [interfejsów API REST usługi Time Series Insights,](https://docs.microsoft.com/rest/api/time-series-insights/)prawidłowy token nośnika OAuth 2.0 musi zostać przekazany w [nagłówku Autoryzacja](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) przy użyciu wybranego klienta REST (Postman, JavaScript, C#). 

> [!TIP]
> Przeczytaj hostowany [przykładowy zestaw SDK klienta](https://tsiclientsample.azurewebsites.net/) usługi Azure Time Series Insights, aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsów API usługi Time Series Insights programowo przy użyciu [zestawu SDK klienta JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) wraz z wykresami i wykresami.

### <a name="http-headers"></a>Nagłówki HTTP

Wymagane nagłówki żądań są opisane poniżej.

| Wymagany nagłówek żądania | Opis |
| --- | --- |
| Autoryzacja | Aby uwierzytelnić się za pomocą usługi Time Series Insights, w nagłówku **autoryzacji** musi zostać przekazany prawidłowy token nośnika OAuth 2.0. | 

> [!IMPORTANT]
> Token musi być wystawiony dokładnie `https://api.timeseries.azure.com/` do zasobu (znany również jako "odbiorców" tokenu).
> * Twój [Listonosz](https://www.getpostman.com/) **AuthURL** będzie zatem:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`jest prawidłowa, ale `https://api.timeseries.azure.com` nie jest.

Opcjonalne nagłówki żądań są opisane poniżej.

| Opcjonalny nagłówek żądania | Opis |
| --- | --- |
| Typ zawartości | jest `application/json` obsługiwany. |
| x-ms-client-request-id | Identyfikator żądania klienta. Usługa rejestruje tę wartość. Umożliwia usługi do śledzenia operacji między usługami. |
| x-ms-client-session-id | Identyfikator sesji klienta. Usługa rejestruje tę wartość. Umożliwia usłudze śledzenie grupy powiązanych operacji między usługami. |
| x-ms-client-application-name | Nazwa aplikacji, która wygenerowała to żądanie. Usługa rejestruje tę wartość. |

Opcjonalne, ale zalecane nagłówki odpowiedzi są opisane poniżej.

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| Typ zawartości | Obsługiwane `application/json` jest tylko. |
| x-ms-request-id | Identyfikator żądania wygenerowanego przez serwer. Może służyć do kontaktów z firmą Microsoft w celu zbadania żądania. |
| x-ms-właściwość-nie-found-zachowanie | Nagłówek opcjonalnej odpowiedzi interfejsu API ga. Możliwe wartości `ThrowError` to (domyślnie) lub `UseNull`. |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Więcej informacji na temat wymaganych i opcjonalnych informacji o kwerendach można znaleźć w [dokumentacji referencyjnej](https://docs.microsoft.com/rest/api/time-series-insights/).

Wymagane parametry ciągu zapytania adresu URL zależą od wersji interfejsu API.

| Release | Możliwe wartości wersji interfejsu API |
| --- |  --- |
| Ogólna dostępność | `api-version=2016-12-12`|
| Wersja zapoznawcza | `api-version=2018-11-01-preview` |
| Wersja zapoznawcza | `api-version=2018-08-15-preview` |

Opcjonalne parametry ciągu zapytania url obejmują ustawienie limitu czasu dla czasów wykonywania żądania HTTP.

| Opcjonalny parametr kwerendy | Opis | Wersja |
| --- |  --- | --- |
| `timeout=<timeout>` | Limit czasu po stronie serwera dla wykonania żądania HTTP. Dotyczy tylko [środowiska Get Zdarzenia](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) i [Pobierz środowisko agreguje interfejsy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API. Wartość limitu czasu powinna być na przykład `"PT20S"` w formacie czasu trwania ISO `1-30 s`8601 i powinna znajdować się w zakresie . Wartością `30 s`domyślną jest . | Ogólna dostępność |
| `storeType=<storeType>` | W środowiskach podglądu z włączonym ciepłym magazynem `WarmStore` kwerenda może być wykonywana na lub . `ColdStore` Ten parametr w kwerendzie definiuje, na którym magazynie kwerenda powinna być wykonywana. Jeśli nie zostanie zdefiniowana, kwerenda zostanie wykonana w chłodni. Aby zbadać ciepły magazyn, **storeType** musi `WarmStore`być ustawiony na . Jeśli nie zostanie zdefiniowana, kwerenda zostanie wykonana w chłodni. | Wersja zapoznawcza |

## <a name="next-steps"></a>Następne kroki

- Przykładowy kod, który wywołuje interfejs API usługi GA Time Series Insights, przeczytaj [dane kwerendy w języku C#](./time-series-insights-query-data-csharp.md).

- W przypadku przykładów kodu interfejsu API usługi Preview dla aplikacji Time Series Insights przeczytaj [dane podglądu kwerend przy użyciu języka C#](./time-series-insights-update-query-data-csharp.md).

- Aby uzyskać informacje o odwołaniu do interfejsu API, przeczytaj dokumentację [odwołania do interfejsu API kwerendy.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Dowiedz się, jak [utworzyć jednostkę usługi](../active-directory/develop/howto-create-service-principal-portal.md).
