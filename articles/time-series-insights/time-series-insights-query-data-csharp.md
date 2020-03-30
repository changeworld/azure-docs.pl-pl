---
title: Zapytanie o dane ze środowiska GA przy użyciu kodu języka C# — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak badać dane ze środowiska usługi Azure Time Series Insights przy użyciu niestandardowej aplikacji napisanej w języku C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987975"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Zapytanie o dane ze środowiska ga usługi Azure Time Series Insights przy użyciu języka C #

W tym przykładzie języka C# pokazano, jak używać [interfejsów API kwerendy GA](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) do wykonywania zapytań o dane ze środowisk GA usługi Azure Time Series Insights.

> [!TIP]
> Wyświetl przykłady kodu GA [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)C# w .

## <a name="summary"></a>Podsumowanie

Poniższy przykładowy kod przedstawia następujące funkcje:

* Jak uzyskać token dostępu za pośrednictwem usługi Azure Active Directory przy użyciu [programu Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Jak przekazać ten nabyty token `Authorization` dostępu w nagłówku kolejnych żądań interfejsu API kwerendy. 

* Przykładowy wywołuje każdy z interfejsów API kwerendy GA demonstrujących sposób wysyłania żądań HTTP do:
    * [Pobierz interfejs API środowiska,](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) aby zwrócić środowiska, do które użytkownik ma dostęp
    * [Pobierz interfejs API dostępności środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Pobieranie interfejsu API metadanych środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) w celu pobrania metadanych środowiska
    * [Pobierz interfejs API zdarzeń środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Pobierz interfejs API agregacji środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Jak wchodzić w interakcje z interfejsami API kwerendy GA przy użyciu WSS do wiadomości:

   * [Pobierz interfejs API przesyłany strumieniowo ze zdarzeniami środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Pobierz interfejs API agregacji środowiska przesyłany strumieniowo](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Wymagania wstępne i konfiguracja

Wykonaj następujące kroki przed skompilowaniem i uruchomieniem przykładowego kodu:

1. [Aprowizuj](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) środowisko usługi GA Azure Time Series Insights.
1. Skonfiguruj środowisko usługi Azure Time Series Insights dla usługi Azure Active Directory zgodnie z opisem w [temacie Uwierzytelnianie i autoryzacja.](time-series-insights-authentication-and-authorization.md) 
1. Zainstaluj wymagane zależności projektu.
1. Edytuj przykładowy kod poniżej, zastępując każdy **#DUMMY#** odpowiednim identyfikatorem środowiska.
1. Wykonaj kod wewnątrz programu Visual Studio.

## <a name="project-dependencies"></a>Zależności projektu

Zaleca się używanie najnowszej wersji programu Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — wersja 16.4.2+

Przykładowy kod ma dwie wymagane zależności:

* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) — pakiet 3.13.9.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - pakiet 9.0.1.

Pobierz pakiety w programie Visual Studio 2019, wybierając opcję **Kompilacja** > **rozwiązania kompilacji.**

Alternatywnie, dodaj pakiety za pomocą [NuGet 2.12+](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Przykładowy kod języka C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o kwerendach, przeczytaj [odwołanie do interfejsu API kwerendy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Przeczytaj, jak [połączyć aplikację JavaScript przy użyciu sdk klienta](https://github.com/microsoft/tsiclient) do usługi Time Series Insights.
