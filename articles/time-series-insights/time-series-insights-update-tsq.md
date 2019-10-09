---
title: Azure Time Series Insights zapytań o dane w wersji zapoznawczej | Microsoft Docs
description: Azure Time Series Insights zapytań o dane.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 7b7d041b678ccf2a476afc6d0744f6971349677e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034869"
---
# <a name="data-querying"></a>Wykonywanie zapytań dotyczących danych

Wersja zapoznawcza Azure Time Series Insights umożliwia wykonywanie zapytań dotyczących zdarzeń i metadanych przechowywanych w środowisku za pośrednictwem publicznych interfejsów API. Te interfejsy API są również używane w [eksploratorze Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).

Trzy podstawowe kategorie interfejsu API są dostępne w Time Series Insights:

* **Interfejsy API środowiska**: umożliwiają wykonywanie zapytań dotyczących środowiska Time Series Insightsowego. Przykłady zapytań to lista środowisk, do których obiekt wywołujący ma dostęp i metadane środowiska.

* **Interfejsy szeregów czasowych (TSM-Q) API**: umożliwia tworzenie, odczytywanie, aktualizowanie i usuwanie metadanych przechowywanych w części środowisko modelu szeregów czasowych. Przykłady to wystąpienia, typy i hierarchie.

* **Interfejsy API kwerendy szeregów czasowych (TSQ)** : umożliwia pobieranie danych zdarzeń, które są rejestrowane przez dostawcę źródłowego. Te interfejsy API mogą wykonywać operacje przekształcania, łączenia i wykonywania obliczeń w danych szeregów czasowych.

[Język wyrażenia szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) jest zaawansowaną czwartą kategorią. Używa on modeli szeregów czasowych, aby umożliwić składanie zaawansowanych obliczeń.

## <a name="azure-time-series-insights-preview-core-apis"></a>Podstawowe interfejsy API Azure Time Series Insights wersja zapoznawcza

Obsługiwane są następujące podstawowe interfejsy API.

[Przegląd zapytania @no__t 1Time Series](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Interfejsy API środowiska

Dostępne są następujące interfejsy API środowiska:

* [Pobierz interfejs API środowiska](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): zwraca listę środowisk, do których obiekt wywołujący ma autoryzację dostępu.
* [Pobierz interfejs API dostępności środowiska](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Zwraca rozkład liczby zdarzeń przez sygnaturę czasową zdarzenia `$ts`. Ten interfejs API pomaga ustalić, czy istnieją jakieś zdarzenia w sygnaturze czasowej przez zwrócenie liczby zdarzeń, jeśli takie istnieją.
* [Pobierz interfejs API schematu zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): zwraca metadane schematu zdarzenia dla danego zakresu wyszukiwania. Ten interfejs API pomaga pobrać wszystkie metadane i właściwości dostępne w schemacie dla danego przedziału wyszukiwania.

## <a name="time-series-model-query-tsm-q-apis"></a>Model szeregów czasowych — interfejsy API zapytań (TSM-Q)

Dostępne są następujące interfejsy API:

* [Interfejs API ustawień modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Włącza funkcję get i patch dla typu domyślnego oraz nazwy modelu środowiska.
* [Interfejs API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): włącza CRUD w typach szeregów czasowych i skojarzonych z nimi zmiennych.
* [Interfejs API hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): włącza CRUD w hierarchiach szeregów czasowych i skojarzonych z nimi ścieżkach pól.
* [Interfejs API wystąpień](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): włącza CRUD dla wystąpień szeregów czasowych i skojarzonych z nimi pól wystąpień.

## <a name="time-series-query-tsq-apis"></a>Interfejsy API zapytań szeregów czasowych (TSQ)

Dostępne są następujące interfejsy API zapytań szeregów czasowych:

* [Interfejs API uzyskiwania zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): umożliwia wykonywanie zapytań i pobieranie danych Time Series Insights ze zdarzeń, ponieważ są one rejestrowane w Time Series Insights od dostawcy źródłowego.

* [Pobierz interfejs API serii](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): umożliwia wykonywanie zapytań i pobieranie danych Time Series Insights z przechwyconych zdarzeń przy użyciu danych zarejestrowanych w sieci. Zwracane wartości są oparte na zmiennych, które zostały zdefiniowane w modelu lub dostarczone wewnętrznie.

    >[!NOTE]
    > Klauzula agregacji jest ignorowana, nawet jeśli jest określona w modelu lub udostępniana wewnętrznie.

  Interfejs API pobierania serii zwraca wartość szeregów czasowych dla każdej zmiennej dla każdego interwału. Wartość szeregu czasowego jest formatem, który Time Series Insights używa dla wyjściowego JSON z zapytania. Zwracane wartości są zależne od identyfikatora szeregów czasowych i zestawu zmiennych, które zostały dostarczone.

* [Interfejs API serii agregujących](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): umożliwia wykonywanie zapytań i pobieranie danych Time Series Insights z przechwyconych zdarzeń przez próbkowanie i agregowanie zarejestrowanych danych.

  Interfejs API serii agregujących zwraca wartość szeregów czasowych dla każdej zmiennej dla każdego interwału. Wartości są zależne od identyfikatora szeregów czasowych i zestawu zmiennych, które zostały dostarczone. Interfejs API serii agregowanej osiąga redukcję przy użyciu zmiennych przechowywanych w modelu szeregów czasowych lub dostarczonych wewnętrznie do agregowania lub próbkowania danych.

  Obsługiwane typy agregowania: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [magazynie i przychodzących danych](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure Time Series Insights.

- Zapoznaj się z artykułem [modelowanie](./time-series-insights-update-tsm.md) Time Series Insights w wersji zapoznawczej.

- Poznaj [najlepsze rozwiązania podczas wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md).
