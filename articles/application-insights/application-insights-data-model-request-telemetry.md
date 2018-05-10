---
title: Model danych Telemetrycznych Insights aplikacji Azure — żądanie Telemetrii | Dokumentacja firmy Microsoft
description: Model danych usługi Insights aplikacji dla dane telemetryczne żądania
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: e0bdaf132474d8e5eaac6a9c65093d27d673d343
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="request-telemetry-application-insights-data-model"></a>Żądanie telemetrii: model danych usługi Application Insights

Element dane telemetryczne żądania (w [usługi Application Insights](app-insights-overview.md)) reprezentuje logicznej sekwencji wykonywania wyzwalane przez żądanie zewnętrzne do aplikacji. Każdy wykonywania żądania jest identyfikowany przez unikatowy `ID` i `url` zawierający wszystkie parametry wykonywania. Żądania można grupować wg logicznej `name` i zdefiniuj `source` tego żądania. Wykonanie kodu może spowodować `success` lub `fail` i ma określony `duration`. Może być zgrupowane wykonaniami sukcesów i niepowodzeń przez `resultCode`. Godzina rozpoczęcia dla dane telemetryczne żądania zdefiniowane na poziomie schematu envelope.

Żądanie telemetrii obsługuje modelu rozszerzalności standardowe przy użyciu niestandardowych `properties` i `measurements`.

## <a name="name"></a>Name (Nazwa)

Nazwa żądania reprezentuje ścieżkę kodu do przetworzenia żądania. Wartość Niski Kardynalność umożliwiają grupowanie lepiej żądań. Dla żądania HTTP reprezentuje metody HTTP i szablon ścieżki adresu URL, takie jak `GET /values/{id}` bez rzeczywistego `id` wartość.

Application Insights w sieci web zestawu SDK wysyła Nazwa żądania "w jakim jest" w odniesieniu do rozróżniania wielkości liter. Grupowanie w interfejsie użytkownika jest rozróżniana wielkość liter, `GET /Home/Index` jest liczony oddzielnie od `GET /home/INDEX` mimo że często powoduje w tej samej wykonywania akcji i kontrolerów. Przyczyny tego jest adresy URL na ogół są [z uwzględnieniem wielkości liter](http://www.w3.org/TR/WD-html40-970708/htmlweb.html). Możesz sprawdzić, czy wszystkie `404` dla adresów URL wpisany wielkimi literami. Możesz przeczytać więcej w kolekcji żądanie nazwy przez zestaw SDK sieci Web ASP.Net w [wpis w blogu](http://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maksymalna długość: 1024 znaki

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania żądania. Używane na potrzeby korelacji między żądaniem i innych elementów telemetrii. Identyfikator powinien być globalnie unikatowe. Aby uzyskać więcej informacji, zobacz [korelacji](application-insights-correlation.md) strony.

Maksymalna długość: 128 znaków

## <a name="url"></a>Url

Adres URL żądania z wszystkich parametrów ciągu zapytania.

Maksymalna długość: 2048 znaków

## <a name="source"></a>Element źródłowy

Źródło żądania. Przykłady są klucza Instrumentacji wywołującego lub adres ip obiektu wywołującego. Aby uzyskać więcej informacji, zobacz [korelacji](application-insights-correlation.md) strony.

Maksymalna długość: 1024 znaki

## <a name="duration"></a>Czas trwania

Żądaj czasu trwania w formacie: `DD.HH:MM:SS.MMMMMM`. Musi być dodatnia i mniejsza niż `1000` dni. To pole jest wymagane, ponieważ operacja z początku i końcu reprezentuje dane telemetryczne żądania.

## <a name="response-code"></a>Kod odpowiedzi

Wynik wykonania żądania. Kod stanu HTTP dla żądania HTTP. Być może jest ona `HRESULT` wartość lub wyjątek typu dla innych typów żądań.

Maksymalna długość: 1024 znaki

## <a name="success"></a>Powodzenie

Oznaczenia wywołanie powiodła się czy nie. Pole jest wymagane. Jeśli nie Ustaw jawnie na `false` -uznane za udane żądania. Ta wartość `false` Jeśli operacja została przerwana przez wyjątek lub zwrócił błąd o kodzie wynik.

Dla aplikacji sieci web usługi Application Insights Definiuj żądanie nie powiodło się, gdy kod odpowiedzi jest mniejsza `400` lub równa `401`. Jeśli to domyślne mapowanie nie są zgodne semantycznego aplikacji istnieją jednak przypadki. Kod odpowiedzi `404` może wskazywać "żadne rekordy", które mogą być częścią regularnego przepływu. Może także wskazywać przerwany link. Przerwane łącza można zaimplementować nawet bardziej zaawansowanych logiki. Przerwane łącza można oznaczyć jako błędy tylko wtedy, gdy te linki znajdują się w tej samej lokacji analizując adres url odwołania. Lub oznaczyć je jako błędy podczas uzyskiwania dostępu do aplikacji mobilnych firmy. Podobnie `301` i `302` wskazuje błąd podczas uzyskiwania dostępu do klienta, który nie obsługuje przekierowania.

Częściowo zaakceptowane zawartości `206` może wskazywać na błąd ogólny żądania. Na przykład punkt końcowy usługi Application Insights otrzymuje partii elementów telemetrii jako pojedyncze żądanie. Zwraca `206` Jeśli niektóre elementy w partii nie zostały przetworzone pomyślnie. Współczynnik zwiększania `206` wskazuje problem, który musi być zbadana. Podobne logika dotyczy `207` wiele stanów gdzie Powodzenie może być najgorszy kodów odpowiedzi oddzielne.

Możesz przeczytać więcej w wyniku żądania kodu i kodu stanu w [wpis w blogu](http://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Miar niestandardowych

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Pisanie niestandardowych żądanie telemetrii](app-insights-api-custom-events-metrics.md#trackrequest)
- Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.
- Dowiedz się, jak [Konfigurowanie platformy ASP.NET Core](app-insights-asp-net.md) aplikacji z usługą Application Insights.
- Zapoznaj się z [platform](app-insights-platforms.md) obsługiwane przez usługę Application Insights.
