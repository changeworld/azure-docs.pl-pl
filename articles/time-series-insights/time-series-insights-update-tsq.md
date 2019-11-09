---
title: Azure Time Series Insights zapytań o dane w wersji zapoznawczej | Microsoft Docs
description: Azure Time Series Insights zapytań o dane.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: e660db5db3d1afc14a3c895e6786d1b6a8b82c13
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832419"
---
# <a name="data-querying"></a>Wykonywanie zapytań dotyczących danych

Wersja zapoznawcza Azure Time Series Insights umożliwia wykonywanie zapytań dotyczących zdarzeń i metadanych przechowywanych w środowisku za pośrednictwem publicznych interfejsów API. Te interfejsy API są również używane w [eksploratorze Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).

Trzy podstawowe kategorie interfejsu API są dostępne w Time Series Insights:

* **Interfejsy API środowiska**: te interfejsy API umożliwiają zapytania dotyczące środowiska Time Series Insightsowego. Przykłady zapytań to lista środowisk, do których obiekt wywołujący ma dostęp i metadane środowiska.
* **Interfejsy szeregów czasowych (TSM-Q) API**: umożliwia tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) operacji na metadanych przechowywanych w części środowisko modelu szeregów czasowych. Przykłady to wystąpienia, typy i hierarchie.
* **Interfejsy API kwerendy szeregów czasowych (TSQ)** : umożliwiają pobieranie danych telemetrycznych lub zdarzeń, które są rejestrowane przez dostawcę źródłowego lub przez zmniejszenie danych przy użyciu funkcji skalarnych i agregujących przechowywanych w zmiennych. Te interfejsy API mogą wykonywać operacje przekształcania, łączenia i stosowania obliczeń w danych szeregów czasowych.

Time Series Insights używa rozbudowanego języka wyrażeń opartego na ciągach, czyli [wyrażenia szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)dla obliczeń ekspresowych.

## <a name="azure-time-series-insights-preview-core-apis"></a>Podstawowe interfejsy API Azure Time Series Insights wersja zapoznawcza

Obsługiwane są następujące podstawowe interfejsy API.

[Przegląd zapytania dotyczącego szeregów czasowych ![](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Interfejsy API środowiska

Dostępne są następujące interfejsy API środowiska:

* [Pobierz interfejs API środowiska](/rest/api/time-series-insights/management/environments/get): zwraca listę środowisk, do których obiekt wywołujący ma autoryzację dostępu.
* [Pobierz interfejs API dostępności środowiska](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Zwraca rozkład liczby zdarzeń w `$ts`znacznika czasu zdarzenia. Ten interfejs API pomaga ustalić, czy istnieją jakieś zdarzenia w sygnaturze czasowej przez zwrócenie liczby zdarzeń, jeśli takie istnieją.
* [Pobierz interfejs API schematu zdarzeń](/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): zwraca metadane schematu zdarzenia dla danego zakresu wyszukiwania. Ten interfejs API pomaga pobrać wszystkie metadane i właściwości dostępne w schemacie dla danego przedziału wyszukiwania.

## <a name="time-series-model-query-tsm-q-apis"></a>Model szeregów czasowych — interfejsy API zapytań (TSM-Q)

Dostępne są następujące interfejsy API dotyczące modelu szeregów czasowych. Większość z tych interfejsów API obsługuje operację wykonywania wsadowego, aby włączyć operacje wsadowe CRUD na wielu jednostkach modelu szeregów czasowych:

* [Interfejs API ustawień modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Włącza funkcję *Get* i *patch* dla typu domyślnego oraz nazwy modelu środowiska.
* [Interfejs API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): włącza CRUD w typach szeregów czasowych i skojarzonych z nimi zmiennych.
* [Interfejs API hierarchii](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): włącza CRUD w hierarchiach szeregów czasowych i skojarzonych z nimi ścieżkach pól.
* [Interfejs API wystąpień](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): włącza CRUD dla wystąpień szeregów czasowych i skojarzonych z nimi pól wystąpień. Ponadto interfejs API wystąpień obsługuje następujące operacje:
  * [Wyszukiwanie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Pobiera częściową listę trafień podczas wyszukiwania wystąpień szeregów czasowych na podstawie atrybutów wystąpienia.
  * [Sugeruj](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): wyszukuje i sugeruje częściową listę trafień podczas wyszukiwania wystąpień szeregów czasowych na podstawie atrybutów wystąpienia.

## <a name="time-series-query-tsq-apis"></a>Interfejsy API zapytań szeregów czasowych (TSQ)

Dostępne są następujące interfejsy API zapytań szeregów czasowych. Te interfejsy API są dostępne we wszystkich obsługiwanych magazynach wielowarstwowych w Time Series Insights. Parametry adresu URL zapytania służą do określania [typu magazynu](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) , na którym ma zostać wykonane zapytanie:

* [Interfejs API uzyskiwania zdarzeń](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): umożliwia wykonywanie zapytań i pobieranie danych Time Series Insights ze zdarzeń, ponieważ są one rejestrowane w Time Series Insights od dostawcy źródłowego. Ten interfejs API umożliwia pobieranie nieprzetworzonych zdarzeń dla danego identyfikatora szeregów czasowych i zakresu wyszukiwania. Ten interfejs API obsługuje stronicowanie w celu pobrania kompletnego zestawu danych dla wybranych danych wejściowych. 

* [Pobierz interfejs API serii](/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): umożliwia wykonywanie zapytań i pobieranie danych Time Series Insights z przechwyconych zdarzeń przy użyciu danych zarejestrowanych w sieci. Zwracane wartości są oparte na zmiennych, które zostały zdefiniowane w modelu lub dostarczone wewnętrznie. Ten interfejs API obsługuje stronicowanie w celu pobrania kompletnego zestawu danych dla wybranych danych wejściowych. Ten interfejs API ułatwia definiowanie właściwości lub kolumn obliczeniowych.

    >[!NOTE]
    > Klauzula agregacji jest ignorowana, nawet jeśli jest określona w modelu lub udostępniana wewnętrznie.

  Interfejs API pobierania serii zwraca wartość szeregów czasowych dla każdej zmiennej dla każdego interwału. Wartość szeregu czasowego jest formatem, który Time Series Insights używa dla wyjściowego JSON z zapytania. Zwracane wartości są zależne od identyfikatora szeregów czasowych i zestawu zmiennych, które zostały dostarczone.

* [Interfejs API serii agregujących](/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregatevariable): umożliwia wykonywanie zapytań i pobieranie danych Time Series Insights z przechwyconych zdarzeń przez próbkowanie i agregowanie zarejestrowanych danych. Ten interfejs API obsługuje ciągłość wykonywania przy użyciu [tokenów kontynuacji](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage).

  Interfejs API serii agregujących zwraca wartość szeregów czasowych dla każdej zmiennej dla każdego interwału. Wartości są zależne od identyfikatora szeregów czasowych i zestawu zmiennych, które zostały dostarczone. Interfejs API serii agregowanej osiąga redukcję przy użyciu zmiennych przechowywanych w modelu szeregów czasowych lub dostarczonych wewnętrznie do agregowania lub próbkowania danych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [magazynie i przychodzących danych](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure Time Series Insights.
- Zapoznaj się z artykułem [modelowanie](./time-series-insights-update-tsm.md) Time Series Insights w wersji zapoznawczej.
- Poznaj [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md).
