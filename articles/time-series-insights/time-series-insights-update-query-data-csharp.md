---
title: Zapytanie o dane ze środowiska w wersji Zapoznawczej przy użyciu języka C# — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak badać dane ze środowiska usługi Azure Time Series Insights przy użyciu aplikacji napisanej w języku C#.
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76980991"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Zapytanie o dane ze środowiska usługi Azure Time Series Insights Preview przy użyciu języka C #

W tym przykładzie języka C# pokazano, jak kwerendy danych z interfejsów API dostępu do danych w [wersji Zapoznawczej](https://docs.microsoft.com/rest/api/time-series-insights/preview) w środowiskach usługi Azure Time Series Insights Preview.

> [!TIP]
> Wyświetl przykłady kodu podglądu języka C# w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)pliku .

## <a name="summary"></a>Podsumowanie

Poniższy przykładowy kod przedstawia następujące funkcje:

* Obsługa automatycznego generowania SDK z [usługi Azure AutoRest](https://github.com/Azure/AutoRest).
* Jak uzyskać token dostępu za pośrednictwem usługi Azure Active Directory przy użyciu [programu Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
* Jak przekazać ten nabyty token `Authorization` dostępu w nagłówku kolejnych żądań interfejsu API dostępu do danych. 
* Przykład zawiera interfejs konsoli pokazujący, jak żądania HTTP są dokonywane do:

    * [Interfejs API środowisk podglądu](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [Pobierz interfejs API dostępności środowiska](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) i pobierz interfejs API [schematu zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [Interfejs API kwerendy w wersji zapoznawczej](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [Pobierz interfejs API zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents), [Pobierz interfejs API serii](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)i pobierz interfejs API [serii agregującej](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [Interfejsy API modelu szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [Pobierz interfejs API hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get) i [interfejsy API wsadowe hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [Pobierz interfejs API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get) typów i [typy interfejsu API wsadowego](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [Pobierz interfejs API wystąpień](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get) i [wystąpienia wsadowego interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* Zaawansowane [funkcje wyszukiwania](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features) i [TSX.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="prerequisites-and-setup"></a>Wymagania wstępne i konfiguracja

Wykonaj następujące kroki przed skompilowaniem i uruchomieniem przykładowego kodu:

1. [Aprowizuj](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment) środowisko usługi Azure Time Series Insights w wersji Zapoznawczej.
1. Skonfiguruj środowisko usługi Azure Time Series Insights dla usługi Azure Active Directory zgodnie z opisem w [temacie Uwierzytelnianie i autoryzacja.](time-series-insights-authentication-and-authorization.md) 
1. Uruchom [generatecode.bat,](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) jak określono w [Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md) do generowania zależności klientów usługi Time Series Insights Preview.
1. Otwórz `TSIPreviewDataPlaneclient.sln` rozwiązanie i `DataPlaneClientSampleApp` ustaw jako projekt domyślny w programie Visual Studio.
1. Zainstaluj wymagane zależności projektu, wykonując kroki opisane [poniżej](#project-dependencies) i skompiluj przykład z plikiem wykonywalnym. `.exe`
1. Uruchom `.exe` plik, klikając go dwukrotnie.

## <a name="project-dependencies"></a>Zależności projektu

Zaleca się używanie najnowszej wersji programu Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — wersja 16.4.2+

Przykładowy kod ma kilka wymaganych zależności, które można wyświetlić w pliku [packages.config.](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)

Pobierz pakiety w programie Visual Studio 2019, wybierając opcję **Kompilacja** > **rozwiązania kompilacji.** 

Alternatywnie należy dodać każdy pakiet przy użyciu [nuget 2.12+](https://www.nuget.org/). Przykład:

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>Przykładowy kod języka C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * Przykładowy kod może być wykonywany bez zmiany domyślnych zmiennych środowiskowych.
> * Przykładowy kod zostanie skompilowany do aplikacji konsoli wykonywalnej platformy .NET.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o kwerendach, przeczytaj [odwołanie do interfejsu API kwerendy](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Przeczytaj, jak [połączyć aplikację JavaScript przy użyciu sdk klienta](https://github.com/microsoft/tsiclient) do usługi Time Series Insights.
