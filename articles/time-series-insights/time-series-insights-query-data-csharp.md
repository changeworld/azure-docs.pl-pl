---
title: Wykonywanie zapytań dotyczących danych ze środowiska GA C# przy użyciu kodu-Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights przy użyciu aplikacji C#niestandardowej nadanej w programie.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: c1c4f139dedc9be11a5f19730221ccec77139200
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962020"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights GA przy użyciuC#

W C# tym przykładzie pokazano, jak używać [interfejsów API zapytania ga](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) do wykonywania zapytań dotyczących danych w środowiskach Azure Time Series Insights.

> [!TIP]
> Wyświetl przykłady C# kodu GA w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="summary"></a>Podsumowanie

Poniższy przykładowy kod ilustruje następujące funkcje:

* Jak uzyskać token dostępu za pośrednictwem Azure Active Directory przy użyciu [programu Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Jak przekazać token uzyskany dostęp w nagłówku `Authorization` kolejnych żądań interfejsu API zapytań. 

* Przykład wywołuje każdy interfejs API GA zapytania, pokazujący, jak są wysyłane żądania HTTP do:
    * [Uzyskaj interfejs API środowisk](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) , aby zwrócić środowiska, do których użytkownik ma dostęp
    * [Pobierz interfejs API dostępności środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Pobierz interfejs API metadanych środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) , aby pobrać metadane środowiska
    * [Interfejs API zdarzeń pobierania środowisk](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Pobierz interfejs API agregacji środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Jak korzystać z interfejsów API zapytań GA przy użyciu programu WSS do wysyłania komunikatów do:

   * [Pobierz strumień interfejsu API zdarzeń środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Pobierz strumień API usługi agregowania środowiskowego](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Wymagania wstępne i Instalator

Przed skompilowaniem i uruchomieniem przykładowego kodu wykonaj następujące czynności:

1. [Zainicjuj obsługę środowiska Azure Time Series Insightsowego](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .

1. Skonfiguruj środowisko Azure Time Series Insights dla Azure Active Directory zgodnie z opisem w temacie [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). 

1. Zainstaluj wymagane zależności projektu.

1. Edytuj przykładowy kod poniżej, zastępując każdy **#DUMMY #** odpowiednim identyfikatorem środowiska.

1. Wykonaj kod w programie Visual Studio.

## <a name="project-dependencies"></a>Zależności projektu

Zalecamy użycie najnowszej wersji programu Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — wersja 16.4.2 +

Przykładowy kod ma dwie wymagane zależności:

* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9 Package.
* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) — pakiet 9.0.1.

Dodaj pakiety przy użyciu narzędzia [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C#przykładowy kod

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.
