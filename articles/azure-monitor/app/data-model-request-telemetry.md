---
title: Model danych telemetrii żądania — Application Insights platformy Azure
description: Application Insights model danych dla telemetrii żądania
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671906"
---
# <a name="request-telemetry-application-insights-data-model"></a>Dane telemetryczne żądania: Application Insights model danych

Element telemetrii żądania (w [Application Insights](../../azure-monitor/app/app-insights-overview.md)) reprezentuje sekwencję logiczną wykonywania wyzwalaną przez zewnętrzne żądanie do aplikacji. Każde wykonanie żądania jest identyfikowane przez unikatowe `ID` i `url` zawierające wszystkie parametry wykonania. Można grupować żądania według logicznych `name` i definiować `source` tego żądania. Wykonanie kodu może spowodować `success` lub `fail` i ma pewne `duration`. Zarówno wykonanie sukcesu, jak i niepowodzenia mogą być pogrupowane w `resultCode`. Godzina rozpoczęcia dla danych telemetrycznych żądania zdefiniowanej na poziomie koperty.

Dane telemetryczne żądania obsługują standardowy model rozszerzalności przy użyciu niestandardowych `properties` i `measurements`.

## <a name="name"></a>Name (Nazwa)

Nazwa żądania reprezentuje ścieżkę kodu wykonywaną w celu przetworzenia żądania. Niska wartość kardynalności umożliwiająca lepsze grupowanie żądań. W przypadku żądań HTTP reprezentuje ona metodę HTTP i szablon ścieżki adresu URL, takie jak `GET /values/{id}` bez rzeczywistej wartości `id`.

Application Insights Web SDK wysyła nazwę żądania "AS IS" w odniesieniu do wielkości liter. W grupowaniu w interfejsie użytkownika jest rozróżniana wielkość liter, dlatego `GET /Home/Index` są zliczane niezależnie od `GET /home/INDEX` nawet wtedy, gdy często powodują to ten sam kontroler i wykonywanie akcji. Przyczyną tego jest to, że adresy URL w ogólności uwzględnia [wielkość](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)liter. Możesz chcieć sprawdzić, czy wszystkie `404` wystąpiły dla adresów URL wpisanych wielką literą. Więcej informacji na temat kolekcji nazw żądań można znaleźć przez ASP.NET Web SDK w [wpisie w blogu](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maksymalna długość: 1024 znaków

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania żądania. Służy do korelacji między żądaniem a innymi elementami telemetrii. Identyfikator powinien być globalnie unikatowy. Aby uzyskać więcej informacji, zobacz stronę [korelacji](../../azure-monitor/app/correlation.md) .

Maksymalna długość: 128 znaków

## <a name="url"></a>Url

Adres URL żądania ze wszystkimi parametrami ciągu zapytania.

Maksymalna długość: 2048 znaków

## <a name="source"></a>Element źródłowy

Źródło żądania. Przykłady to klucz Instrumentacji obiektu wywołującego lub adres IP obiektu wywołującego. Aby uzyskać więcej informacji, zobacz stronę [korelacji](../../azure-monitor/app/correlation.md) .

Maksymalna długość: 1024 znaków

## <a name="duration"></a>Czas trwania

Czas trwania żądania w formacie: `DD.HH:MM:SS.MMMMMM`. Musi być wartością dodatnią i mniejszą niż `1000` dni. To pole jest wymagane jako dane telemetryczne żądania reprezentuje operację z początkiem i końcem.

## <a name="response-code"></a>Kod odpowiedzi

Wynik wykonania żądania. Kod stanu HTTP dla żądań HTTP. Może to być `HRESULT` wartość lub typ wyjątku dla innych typów żądań.

Maksymalna długość: 1024 znaków

## <a name="success"></a>Powodzenie

Wskazanie powodzenia lub nieudanych wywołań. To pole jest wymagane. Gdy nie ustawiono jawnie `false`-żądanie jest uznawane za pomyślne. Ustaw tę wartość na `false`, jeśli operacja została przerwana przez wyjątek lub zwróciła kod wyniku błędu.

W przypadku aplikacji sieci Web Application Insights zdefiniować żądanie jako pomyślne, gdy kod odpowiedzi jest mniejszy niż `400` lub równy `401`. Istnieją jednak przypadki, gdy to domyślne mapowanie nie jest zgodne z semantyką aplikacji. Kod odpowiedzi `404` może wskazywać brak rekordów, które mogą być częścią regularnego przepływu. Może także wskazywać na przerwane łącze. W przypadku uszkodzonych linków można nawet zaimplementować bardziej zaawansowaną logikę. Przerwane linki można oznaczyć jako błędy tylko wtedy, gdy te linki znajdują się w tej samej witrynie przez analizowanie odwołującego adresu URL. Lub Oznacz je jako błędy podczas uzyskiwania dostępu z firmowej aplikacji mobilnej. Podobnie `301` i `302` wskazuje błąd podczas uzyskiwania dostępu z klienta, który nie obsługuje przekierowania.

Częściowo zaakceptowane `206` zawartości mogą wskazywać na niepowodzenie żądania ogólnego. Na przykład, Application Insights punkt końcowy odbiera partie elementów telemetrii jako pojedyncze żądanie. Zwraca `206`, gdy niektóre elementy w partii nie zostały przetworzone pomyślnie. Zwiększenie współczynnika `206` wskazuje problem, który należy zbadać. Podobna logika ma zastosowanie do `207` wiele stanów, w których sukces może być najgorszeniem oddzielnych kodów odpowiedzi.

Więcej informacji na temat kodu wyniku żądania i kodu stanu można znaleźć w [wpisie w blogu](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- [Zapisz dane telemetryczne żądania niestandardowego](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Dowiedz się, jak [skonfigurować aplikację ASP.NET Core](../../azure-monitor/app/asp-net.md) przy użyciu Application Insights.
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
