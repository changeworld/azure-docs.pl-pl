---
title: Model danych dla danych telemetrycznych żądania — usługa Azure Application Insights
description: Model danych usługi Application Insights dla danych telemetrycznych żądania
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: d8a28063bf6780c3cace4ead81e289779b95eb9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671906"
---
# <a name="request-telemetry-application-insights-data-model"></a>Żądania danych telemetrycznych: model danych usługi Application Insights

Element telemetrii żądania (w [usłudze Application Insights)](../../azure-monitor/app/app-insights-overview.md)reprezentuje logiczną sekwencję wykonywania wyzwalane przez żądanie zewnętrzne do aplikacji. Każde wykonanie żądania jest `ID` identyfikowane przez unikatowe i `url` zawierające wszystkie parametry wykonywania. Żądania można grupować `name` według `source` logicznego i definiować to żądanie. Wykonanie kodu może `success` `fail` spowodować lub `duration`ma pewne . Zarówno sukces, jak i egzekucje `resultCode`porażki mogą być pogrupowane według . Godzina rozpoczęcia danych telemetrycznych żądania zdefiniowanych na poziomie koperty.

Żądania telemetrii obsługuje standardowy `properties` model `measurements`rozszerzalności przy użyciu niestandardowych i .

## <a name="name"></a>Nazwa

Nazwa żądania reprezentuje ścieżkę kodu pobraną do przetworzenia żądania. Niska wartość kardynalność, aby umożliwić lepsze grupowanie żądań. W przypadku żądań HTTP reprezentuje metodę `GET /values/{id}` HTTP i `id` szablon ścieżki adresu URL, podobnie jak bez rzeczywistej wartości.

SDK sieci Web usługi Application Insights wysyła nazwę żądania "tak jak jest" w odniesieniu do sprawy listu. Grupowanie w interfejsie użytkownika `GET /Home/Index` jest rozróżniana wielkość liter, więc jest liczony oddzielnie od `GET /home/INDEX` mimo że często powodują one tego samego kontrolera i wykonywania akcji. Powodem tego jest to, że adresy URL w ogóle są [rozróżniane wielkość liter](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Możesz sprawdzić, czy `404` wszystko się stało dla adresów URL wpisanych w wielkich literach. Więcej informacji na temat zbierania nazw na żądanie można przeczytać ASP.NET web SDK w poście w [blogu](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maksymalna długość: 1024 znaków

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania żądania. Służy do korelacji między żądaniami i innymi elementami telemetrii. Identyfikator powinien być unikatowy globalnie. Aby uzyskać więcej informacji, zobacz stronę [korelacji.](../../azure-monitor/app/correlation.md)

Maksymalna długość: 128 znaków

## <a name="url"></a>Url

Zażądaj adresu URL ze wszystkimi parametrami ciągu zapytania.

Maksymalna długość: 2048 znaków

## <a name="source"></a>Element źródłowy

Źródło żądania. Przykładami są klucz instrumentacji osoby dzwoniącej lub adres IP osoby dzwoniącej. Aby uzyskać więcej informacji, zobacz stronę [korelacji.](../../azure-monitor/app/correlation.md)

Maksymalna długość: 1024 znaków

## <a name="duration"></a>Czas trwania

Czas trwania żądania `DD.HH:MM:SS.MMMMMM`w formacie: . Musi być pozytywny `1000` i mniej niż dni. To pole jest wymagane, ponieważ dane telemetryczne żądania reprezentują operację z początkiem i końcem.

## <a name="response-code"></a>Kod odpowiedzi

Wynik wykonania żądania. Kod stanu HTTP dla żądań HTTP. Może to `HRESULT` być wartość lub typ wyjątku dla innych typów żądań.

Maksymalna długość: 1024 znaków

## <a name="success"></a>Powodzenie

Wskazanie udanego lub nieudanego połączenia. To pole jest wymagane. Gdy nie jest `false` ustawiona jawnie - żądanie jest uważane za pomyślne. Ustaw tę `false` wartość na jeśli operacja została przerwana przez wyjątek lub zwrócony kod wyniku błędu.

W przypadku aplikacji sieci web usługa Application Insights definiuje żądanie `400` jako `401`pomyślne, gdy kod odpowiedzi jest mniejszy lub równy . Istnieją jednak przypadki, gdy to domyślne mapowanie nie pasuje do semantyki aplikacji. Kod `404` odpowiedzi może wskazywać "brak rekordów", które mogą być częścią regularnego przepływu. Może również wskazywać na przerwane łącze. W przypadku przerwanych łączy można nawet zaimplementować bardziej zaawansowaną logikę. Przerwane łącza można oznaczyć jako błędy tylko wtedy, gdy te łącza znajdują się w tej samej witrynie, analizując adres url odsyłający. Lub oznacz je jako błędy, gdy dostęp z aplikacji mobilnej firmy. Podobnie `301` i `302` wskazuje błąd, gdy dostęp z klienta, który nie obsługuje przekierowania.

Częściowo zaakceptowana `206` zawartość może wskazywać na niepowodzenie ogólnego żądania. Na przykład punkt końcowy usługi Application Insights odbiera partię elementów telemetrycznych jako pojedyncze żądanie. Zwraca, `206` gdy niektóre elementy w partii nie zostały przetworzone pomyślnie. Zwiększenie wskaźnika `206` wskazuje na problem, który należy zbadać. Podobna logika `207` ma zastosowanie do multi-status, gdzie sukces może być najgorszy z oddzielnych kodów odpowiedzi.

Więcej informacji na temat kodu wyniku żądania i kodu stanu można przeczytać w poście w [blogu](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- [Zapisanie danych telemetrycznych żądań niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- Dowiedz się, jak [skonfigurować aplikację ASP.NET Core](../../azure-monitor/app/asp-net.md) za pomocą usługi Application Insights.
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
