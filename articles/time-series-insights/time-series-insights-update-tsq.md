---
title: Azure zapytań czasu Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Podczas badania danych czasu Series Insights w wersji zapoznawczej platformy Azure.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: bbf682df2df7a8cdc9fedb36aa4244fc5c0e9488
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243997"
---
# <a name="data-querying"></a>Wykonywanie zapytań dotyczących danych

Usługi Azure Insights serii czasu (wersja zapoznawcza) umożliwia danych zapytań o zdarzeń i metadane przechowywane w środowisku przy użyciu publicznych interfejsów API urządzenia surface. Te interfejsy API są również używane w [czasu Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md).

Trzy podstawowe kategorie interfejsu API są dostępne w usłudze Time Series Insights:

* **Interfejsy API w środowisku**: Umożliwia zapytaniom samego środowiska usługi Time Series Insights. Przykłady zapytań listy środowisk, którymi obiekt wywołujący ma dostęp do i metadane środowiska.

* **Interfejsy API (TSM-Q) dla zapytania w modelu serii czasu**: Umożliwia służy do tworzenia, odczytu, aktualizacji i usuwania operacji na metadanych w środowisku części modelu szeregów czasowych. Przykładami są wystąpienia, typy i hierarchii.

* **Szeregi czasowe zapytania interfejsy API (TSQ)** : Umożliwia pobieranie danych zdarzenia, jak jest rejestrowana od dostawcy źródła. Te interfejsy API mogą wykonywać operacje, przekształcania, łączenie i wykonywania obliczeń na danych szeregów czasowych.

[Języka wyrażeń serii czasu (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) jest kategorią czwarty zaawansowane. Aby włączyć skład zaawansowanych obliczeń używa modeli szeregów czasowych.

## <a name="azure-time-series-insights-preview-core-apis"></a>Usługa Azure czas Series Insights w wersji zapoznawczej podstawowe interfejsy API

Następujące podstawowe interfejsy API są obsługiwane.

[![Omówienie zapytań w serii czasu](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Interfejsy API w środowisku

Dostępne są następujące funkcje interfejsu API środowiska:

* [Środowisko API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Zwraca listę środowisk, czy obiekt wywołujący jest autoryzowany do uzyskania dostępu.
* [Uzyskaj dostępność środowiska interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Zwraca rozkład liczby zdarzeń za pośrednictwem sygnatura czasowa zdarzenia `$ts`. Ten interfejs API pomaga ustalić, czy istnieją jakiekolwiek zdarzenia w sygnaturę czasową, zwracając liczbę zdarzeń, jeśli takie istnieją.
* [Pobieranie schematu zdarzeń API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Zwraca metadane schematu zdarzeń dla podanego zakresu. Ten interfejs API pomaga pobrać wszystkie metadane i właściwości dostępne w schemacie dla podanego zakresu.

## <a name="time-series-model-query-tsm-q-apis"></a>Interfejsy API (TSM-Q) dla zapytania w modelu serii czasu

Dostępne są następujące interfejsy API modelu zapytania serii czasu:

* [Ustawienia interfejsu API modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Umożliwia pobieranie i stosowanie poprawek na domyślny typ i nazwa modelu środowiska.
* [Typy interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Włącza CRUD na typy szeregów czasowych i ich skojarzone zmienne.
* [Interfejs API hierarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Włącza CRUD na hierarchii szeregów czasowych i ich skojarzone pole ścieżki.
* [Wystąpienia interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Włącza CRUD na wystąpieniach szeregów czasowych i ich pól skojarzonego wystąpienia.

## <a name="time-series-query-tsq-apis"></a>Interfejsów API zapytań (TSQ) serii czasu

Dostępne są następujące interfejsy API zapytań serii czasu:

* [Pobierz zdarzenia interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Umożliwia wysyłanie kwerend i pobieranie danych usługi Time Series Insights z zdarzenia są rejestrowane w usłudze Time Series Insights od dostawcy źródła.

* [Rozpoczynanie serii API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Umożliwia wysyłanie kwerend i pobieranie danych usługi Time Series Insights z przechwytywanych zdarzeń przy użyciu danych zarejestrowanych w sieci. Wartości, które są zwracane są oparte na zmienne, które zostały zdefiniowane w modelu lub podać wbudowane.

    >[!NOTE]
    > Klauzula agregacji jest ignorowany, nawet jeśli jest określone w modelu, lub podać wbudowane.

  Rozpoczynanie serii interfejs API zwraca wartość szeregów czasowych dla każdej zmiennej dla każdego interwału. Wartość typu Time Series jest w formacie, który korzysta z danych wyjściowych JSON z kwerendy usługi Time Series Insights. Wartości, które są zwracane są oparte na identyfikator serii czasu i zbiór zmiennych, które zostały udostępnione.

* [Agreguj serii API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Umożliwia wysyłanie kwerend i pobieranie danych usługi Time Series Insights z przechwytywanych zdarzeń przez pobieranie próbek i agregowanie zarejestrowanych danych.

  Łączny serii interfejsu API zwraca wartość szeregów czasowych dla każdej zmiennej dla każdego interwału. Wartości są oparte na identyfikator serii czasu i zbiór zmiennych, które zostały udostępnione. Łączny API serii osiąga redukcji przy użyciu zmiennych przechowywanych w modelu szeregów czasowych lub podano wbudowaną, aby agregacji lub przykładowe dane.

  Obsługiwane typy agregacji: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure czas serii szczegółowych informacji.

- Przeczytaj serii czasu Insights (wersja zapoznawcza) [modelowania danych](./time-series-insights-update-tsm.md) artykułu.

- Odkryj [najważniejsze wskazówki podczas wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md).
