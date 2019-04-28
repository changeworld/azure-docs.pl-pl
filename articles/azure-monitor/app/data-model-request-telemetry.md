---
title: Model danych Telemetrycznych szczegółowych informacji w aplikacji platformy Azure — dane telemetryczne dotyczące żądań | Dokumentacja firmy Microsoft
description: Model danych szczegółowych informacji aplikacji dla telemetrii żądania
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: fef016d87cc60bc916fdcb08f92171e115221fe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900529"
---
# <a name="request-telemetry-application-insights-data-model"></a>Dane telemetryczne dotyczące żądań: Model danych usługi Application Insights

Elementu telemetrii żądania (w [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md)) reprezentuje Sekwencja logiczna wykonywania wyzwalana przez żądanie zewnętrzne do aplikacji. Wykonanie każdego żądania jest identyfikowane za pomocą unikatowych `ID` i `url` zawierający wszystkie parametry wykonywania. Żądania można pogrupować wg logiczne `name` i zdefiniuj `source` tego żądania. Wykonywanie kodu może spowodować `success` lub `fail` i ma określony `duration`. Liczba wykonań sukcesów i niepowodzeń, które mogą być zgrupowane jeszcze bardziej przez `resultCode`. Godzina rozpoczęcia dla telemetrii żądania zdefiniowane na poziomie schematu envelope.

Żądanie telemetrii obsługuje model standardowy rozszerzalność przy użyciu niestandardowego `properties` i `measurements`.

## <a name="name"></a>Name (Nazwa)

Nazwa żądania reprezentuje ścieżkę kodu do przetwarzania żądania. Kardynalność niskiej wartości lepiej grupowanie żądań. Dla żądań HTTP, jego reprezentuje metody HTTP i szablon ścieżki adresu URL, takich jak `GET /values/{id}` bez rzeczywistego `id` wartość.

Application Insights w sieci web zestaw SDK wysyła Nazwa żądania "w jakim jest" w odniesieniu do rozróżniania wielkości liter. Grupowanie w interfejsie użytkownika jest uwzględniana wielkość liter, `GET /Home/Index` jest liczone oddzielnie od `GET /home/INDEX` mimo że często powodują one ten sam wykonywania akcji i kontrolerów. Przyczyna tego jest to, że adresy URL ogólnie rzecz biorąc są [liter](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Warto sprawdzić, czy wszystkie `404` wystąpiło adresy URL wpisane pisane wielkimi literami. Możesz przeczytać więcej na żądania nazwy kolekcji przez zestaw SDK usługi sieci Web platformy ASP.NET w [wpis w blogu](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Maksymalna długość: 1024 znaki

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania żądania. Używane na potrzeby korelacji między żądaniem i innych elementów telemetrii. Identyfikator musi być unikatowa w skali globalnej. Aby uzyskać więcej informacji, zobacz [korelacji](../../azure-monitor/app/correlation.md) strony.

Maksymalna długość: 128 znaków

## <a name="url"></a>Url

Adres URL żądania z wszystkie parametry ciągu zapytania.

Maksymalna długość: 2048 znaków

## <a name="source"></a>Element źródłowy

Źródło żądania. Przykłady to klucz Instrumentacji obiektu wywołującego lub adres ip obiektu wywołującego. Aby uzyskać więcej informacji, zobacz [korelacji](../../azure-monitor/app/correlation.md) strony.

Maksymalna długość: 1024 znaki

## <a name="duration"></a>Czas trwania

Czas trwania w formacie żądania: `DD.HH:MM:SS.MMMMMM`. Musi być dodatnia i mniejsza niż `1000` dni. To pole jest wymagane, ponieważ dane telemetryczne żądania reprezentuje operację, podając rozpoczęcia i zakończenia.

## <a name="response-code"></a>Kod odpowiedzi

Wynik wykonywania żądania. Kod stanu HTTP dla żądania HTTP. Może być `HRESULT` typu wartości lub wyjątków dla innych typów żądań.

Maksymalna długość: 1024 znaki

## <a name="success"></a>Powodzenie

Wskazanie wywołanie powodzeniem lub niepowodzeniem. To pole jest wymagane. Gdy Nieustawione jawnie na `false` — żądanie jest traktowane jako zakończy się powodzeniem. Ustaw tę wartość na `false` czy operacji zostało przerwane przez wyjątek zwrócił błąd o kodzie wyniku.

Dla aplikacji sieci web usługi Application Insights zdefiniuj żądanie jako pomyślne gdy kod odpowiedzi jest mniejsza niż `400` lub równa `401`. Jeśli to domyślne mapowanie nie odpowiada semantycznego aplikacji istnieją jednak przypadki. Kod odpowiedzi `404` może wskazywać "żadnych rekordów", które mogą być częścią regularnego przepływu. Ponadto może to oznaczać przerwanego linku. Zerwane linki można zaimplementować nawet bardziej zaawansowanych logiki. Uszkodzone linki można oznaczyć jako błędy tylko wtedy, gdy te linki znajdują się w tej samej lokacji, analizując odwołania adresu url. Lub oznaczyć je jako błędy podczas uzyskiwania dostępu do aplikacji mobilnych firmy. Podobnie `301` i `302` wskazuje błąd podczas uzyskiwania dostępu do klienta, który nie obsługuje przekierowania.

Częściowo zaakceptowane zawartości `206` może wskazywać na błąd żądania ogólnego. Na przykład punkt końcowy usługi Application Insights odbiera partii elementów telemetrii jako pojedynczego żądania. Zwraca `206` gdy niektóre elementy w partii nie zostały przetworzone pomyślnie. Rosnąca liczba `206` wskazuje problem, który musi sprawdzić. Podobne logika dotyczy `207` wiele stanów, w których Powodzenie mogą być najgorszy kodów odpowiedzi oddzielne.

Możesz przeczytać więcej w wyniku żądania kodu i kod stanu w [wpis w blogu](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Kolejne kroki

- [Zapisywanie żądania niestandardowych danych telemetrycznych](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Zobacz [modelu danych](data-model.md) dla usługi Application Insights typów i danych modelu.
- Dowiedz się, jak [Konfigurowanie platformy ASP.NET Core](../../azure-monitor/app/asp-net.md) aplikacji za pomocą usługi Application Insights.
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
