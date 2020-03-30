---
title: Kwerendy dotyczące danych w wersji Preview — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Pojęcia dotyczące zapytań o dane i omówienie interfejsu API HTTP REST w usłudze Azure Time Series Insights Preview.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284895"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Wyszukiwanie danych w usłudze Azure Time Series Insights Preview

Usługa Azure Time Series Insights umożliwia wykonywanie zapytań o dane dotyczące zdarzeń i metadanych przechowywanych w środowisku za pośrednictwem interfejsów API powierzchni publicznej. Te interfejsy API są również używane przez [Eksploratora usługi Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Trzy podstawowe kategorie interfejsu API są dostępne w usłudze Time Series Insights:

* **Interfejsy API środowiska:** te interfejsy API umożliwiają kwerendy w samym środowisku usługi Time Series Insights. Mogą one służyć do zbierania listy środowisk wywołującego ma dostęp do i środowiska metadanych.
* **Interfejsy API programu Time Series Model-Query (TSM-Q):** Umożliwia tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) operacji metadanych przechowywanych w modelu szeregów czasowych środowiska. Mogą one służyć do uzyskiwania dostępu do wystąpień, typów i hierarchii oraz edytowania ich.
* **Interfejsy API kwerendy szeregów czasowych (TSQ):** Umożliwia pobieranie danych telemetrycznych lub zdarzeń rejestrowanych przez dostawcę źródłowego i umożliwia wykonywanie obliczeń i agregacji danych przy użyciu zaawansowanych funkcji skalarnych i agregujących.

Usługa Time Series Insights używa do wyrażania obliczeń języka wyrażenia opartego na ciągach znaków [czasowych, wyrażenia szeregów czasowych (TSX).](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

## <a name="azure-time-series-insights-core-apis"></a>Podstawowe interfejsy API usługi Azure Time Series Insights

Obsługiwane są następujące podstawowe interfejsy API.

[![Omówienie kwerendy szeregów czasowych](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Interfejsy API środowiska

* [Pobierz środowiska interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Zwraca listę środowisk, do których osoba dzwoniąca jest autoryzowana.
* [Interfejs API dostępności środowiska](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Zwraca rozkład liczby zdarzeń w `$ts`sygnatury czasowej zdarzenia . Ten interfejs API pomaga określić, czy istnieją jakieś zdarzenia w środowisku, zwracając liczbę zdarzeń podzielonych na interwały czasu, jeśli istnieją.
* [Pobierz interfejs API schematu zdarzeń:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)zwraca metadane schematu zdarzenia dla danego zakresu wyszukiwania. Ten interfejs API pomaga pobrać wszystkie metadane i właściwości dostępne w schemacie dla danego zakresu wyszukiwania.

## <a name="time-series-model-query-tsm-q-apis"></a>Interfejsy API programu "Model-kwerenda"(TSM-Q)

Większość z tych interfejsów API obsługuje operację wykonywania partii, aby włączyć operacje CRUD partii na wielu jednostek modelu szeregów czasowych:

* [Ustawienia modelu INTERFEJSU API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)Włącza *GET* i *PATCH* na typ domyślny i nazwę modelu środowiska.
* [Typy INTERFEJSU API:](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)Włącza CRUD na typach szeregów czasowych i ich skojarzonych zmiennych.
* [Interfejs API hierarchii:](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)Włącza CRUD w hierarchiach szeregów czasowych i skojarzonych z nimi ścieżkach pól.
* [Interfejs API wystąpień:](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)Włącza CRUD w wystąpieniach szeregów czasowych i skojarzonych z nimi polach wystąpień. Ponadto interfejs API wystąpień obsługuje następujące operacje:
  * [Wyszukiwanie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Pobiera częściową listę trafień w wyszukiwaniu wystąpień szeregów czasowych na podstawie atrybutów wystąpień.
  * [Sugestia:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)Wyszukuje i sugeruje częściową listę trafień w wyszukiwaniu wystąpień szeregów czasowych na podstawie atrybutów wystąpień.

## <a name="time-series-query-tsq-apis"></a>Interfejsy API kwerendy szeregów czasowych (TSQ)

Te interfejsy API są dostępne we wszystkich sklepach w naszym wielowarstwowym rozwiązaniu magazynu w usłudze Time Series Insights. Parametry adresu URL kwerendy są używane do określania [typu magazynu,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) który kwerenda powinna wykonać:

* [Pobierz zdarzenia interfejsu API:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)Umożliwia kwerendy i pobieranie nieprzetworzonych zdarzeń i sygnatury czasowe skojarzone zdarzenia, ponieważ są one rejestrowane w usługi Time Series Insights od dostawcy źródłowego. Ten interfejs API umożliwia pobieranie nieprzetworzonych zdarzeń dla danego identyfikatora szeregów czasowych i zakresu wyszukiwania. Ten interfejs API obsługuje podział na strony, aby pobrać kompletny zestaw danych odpowiedzi dla wybranego wejścia. 

* [Pobierz interfejs API serii:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)Umożliwia wykonywanie zapytań i pobierania obliczonych wartości i skojarzonych sygnatur czasowych zdarzeń przez zastosowanie obliczeń zdefiniowanych przez zmienne dla zdarzeń nieprzetworzonych. Zmienne te można zdefiniować w modelu szeregów czasowych lub w yłącze w kwerendzie. Ten interfejs API obsługuje podział na strony, aby pobrać kompletny zestaw danych odpowiedzi dla wybranego wejścia. 

* [Interfejs API serii agregacji:](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)Umożliwia wykonywanie zapytań i pobierania zagregowanych wartości i skojarzonych sygnatur czasowych interwałów przez zastosowanie obliczeń zdefiniowanych przez zmienne w przypadku zdarzeń nieprzetworzonych. Zmienne te można zdefiniować w modelu szeregów czasowych lub w yłącze w kwerendzie. Ten interfejs API obsługuje podział na strony, aby pobrać kompletny zestaw danych odpowiedzi dla wybranego wejścia. 
  
  Dla określonego zakresu wyszukiwania i interwału ten interfejs API zwraca zagregowane odpowiedzi na zmienną na interwał dla identyfikatora szeregów czasowych. Liczba interwałów w zestawie danych odpowiedzi jest obliczana przez zliczanie znaczników epoki (liczba milisekund, które upłynęły od epoki Uniksa - 1 stycznia 1970 r.) i dzielenie znaczników przez rozmiar zakresu interwału określony w kwerendzie.

  Sygnatury czasowe zwracane w zestawie odpowiedzi są z lewej granice interwału, a nie próbkowania zdarzeń z interwału. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o różnych zmiennych, które można zdefiniować w [modelu szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Dowiedz się więcej o tym, jak wysyłać zapytania do danych z [Eksploratora usługi Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
