---
title: Usługa Azure Cosmos DB Interfejs API języka SQL Python, zasoby & zestawu SDK
description: Dowiedz się więcej o interfejsie API języka SQL Python i zestawie SDK, w tym o datach wydania, datach wycofania i zmianach między poszczególnymi wersjami Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: ed90c22fa8c5b94567a9886ca71c9b35fbb103f0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624621"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK dla interfejsu API SQL: Informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanał informacyjny zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Pobierz zestaw SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API języka Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instrukcje dotyczące instalacji zestawu SDK**|[Instrukcje dotyczące instalacji zestawu SDK języka Python](https://github.com/Azure/azure-cosmos-python)|
|**Przyczynia się do zestawu SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK języka Python](sql-api-python-application.md)|
|**Bieżąca obsługiwana platforma**|[Python 2,7](https://www.python.org/downloads/) i [Python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Dodano obsługę elementu DataType MultiPolygon
* Poprawka błędu w zasadach ponawiania próby odczytu sesji
* Poprawka usterki dla nieprawidłowych problemów z uzupełnianiem podczas dekodowania podstawowych 64 ciągów

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Poprawka błędu w LocationCache
* Usterka logiki ponawiania punktu końcowego
* Stała dokumentacja

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Obsługa zapisu w ramach wieloregionu.
* Przestrzeń nazw została zmieniona na Azure. Cosmos.
* Pojęcia dotyczące kolekcji i dokumentów zostały zmienione na kontener i element, document_client zmieniono nazwę na cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Dodano obsługę serwera proxy
* Dodano obsługę kanału informacyjnego zmiany odczytu
* Dodano obsługę nagłówków przydziału kolekcji
* Poprawka dla problemu z tokenami dużej sesji
* Poprawka dla interfejsu API ReadMedia
* Poprawka w pamięci podręcznej zakresów kluczy partycji

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Zaktualizowana dokumentacja dotycząca referencyjnego Azure Cosmos DB zamiast platformy Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora usługi Azure Cosmos dostępne do pobrania z https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Poprawka usterki dla słownika agregacji.
* Poprawka usterki dla przycinania ukośników w łączu zasobów.
* Dodano testy dla kodowania Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę nowego poziomu spójności, nazywana ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Dodano opcję wyłączania weryfikacji SSL podczas uruchamiania w oparciu o emulator Cosmos DB.
* Usunięto ograniczenie zależnego modułu żądań, aby dokładnie 2.10.0.
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.
* Wersja interfejsu API REST dotrzymana do "2017-01-19" w tej wersji.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Wprowadzono zmiany redakcyjne w komentarzach dokumentacji.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Dodano obsługę języka Python 3,5.
* Dodano obsługę puli połączeń przy użyciu modułu żądania.
* Dodano obsługę spójności sesji.
* Dodano obsługę zapytań TOP/ORDERBY dla kolekcji partycjonowanych.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Dodano obsługę zasad ponawiania dla żądań z ograniczeniami. (Żądania ograniczające żądania odbierają zbyt duży wyjątek, kod błędu 429). Domyślnie program Azure Cosmos DB ponownych prób w przypadku każdego żądania, gdy zostanie napotkany kod błędu 429, a w nagłówku odpowiedzi zostanie osiągnięty czas retryAfter. Ustalony czas interwału ponowienia próby można teraz ustawić jako część właściwości RetryOptions obiektu ConnectionPolicy, jeśli chcesz zignorować czas retryAfter zwrócony przez serwer między ponownymi próbami. Azure Cosmos DB teraz czeka przez maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Ten czas można również zastąpić we właściwości RetryOptions obiektu ConnectionPolicy.
* Cosmos DB teraz zwraca wartość x-MS-dławienia-retry i licznik x-MS-dławienia-retry-Time-MS jako nagłówki odpowiedzi w każdym żądaniu, aby zauważyć licznik ponownych prób ograniczenia i skumulowany czas oczekiwania żądania między ponownymi próbami.
* Usunięto klasę RetryPolicy i odpowiadającą jej właściwość (retry_policy) uwidocznioną dla klasy document_client, a zamiast tego wprowadzamy klasę RetryOptions, która uwidacznia Właściwość RetryOptions klasy ConnectionPolicy, która może służyć do przesłonięcia niektórych domyślne opcje ponowień.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę multiregionalne konta baz danych.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dodano obsługę funkcji Time to Live (TTL) dla dokumentów.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Poprawki błędów powiązane z partycjonowaniem po stronie serwera, aby zezwolić na znaki specjalne w ścieżce klucza partycji.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Zaimplementowane [podzielona na partycje kolekcje](partition-data.md) i [poziomów wydajności zdefiniowanych przez użytkownika](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Dodaj skrót zak & res partycji rozwiązujący uzyskanymi fragmentowania aplikacje na wielu partycjach.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementowanie Upsert. Nowe metody UpsertXXX dodane do obsługi funkcji upsert.
* Routing oparty na identyfikator implementacji. Brak publicznego zmian interfejsu API, wszystkie zmiany wewnętrznego.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Obsługuje indeks geoprzestrzenny.
* Sprawdza poprawność właściwość identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie może zawierać?, /, #, \, znaków ani kończyć się spacją.
* Dodaje nowy nagłówek "indeks przekształcania progress" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania w wersji 2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Obsługuje połączenie z serwerem proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK.

## <a name="release--retirement-dates"></a>Data wycofania &
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszystkie żądania Cosmos DB korzystania z wycofanego zestawu SDK są odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje zestawu SDK usługi Azure SQL dla języka Python wcześniejszymi niż wersja **1.0.0** zostały wycofane z dniem **do 29 lutego 2016 r**. 
> 
> 

<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [3.0.2](#3.0.2) |LIS 15, 2018 |--- |
| [3.0.1](#3.0.1) |04, 2018 |--- |
| [2.3.3](#2.3.3) |Września 08, 2018 |--- |
| [2.3.2](#2.3.2) |Maja 08, 2018 |--- |
| [2.3.1](#2.3.1) |21 grudnia 2017 |--- |
| [2.3.0](#2.3.0) |10 listopada 2017 r. |--- |
| [2.2.1](#2.2.1) |29 wrz 2017 |--- |
| [2.2.0](#2.2.0) |10 maja 2017 |--- |
| [2.1.0](#2.1.0) |01 maja, 2017 |--- |
| [2.0.1](#2.0.1) |30 października 2016 r. |--- |
| [2.0.0](#2.0.0) |29 września, 2016 |--- |
| [1.9.0](#1.9.0) |07 lipca 2016 |--- |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |--- |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |--- |
| [1.6.1](#1.6.1) |08 kwietnia, 2016 |--- |
| [1.6.0](#1.6.0) |29 marca 2016 r. |--- |
| [1.5.0](#1.5.0) |03, 2016 |--- |
| [1.4.2](#1.4.2) |06, 2015 |--- |
| 1.4.1 |06, 2015 |--- |
| [1.2.0](#1.2.0) |06 sierpnia 2015 |--- |
| [1.1.0](#1.1.0) |09 lipca 2015 r. |--- |
| [1.0.1](#1.0.1) |25 maja 2015 |--- |
| [1.0.0](#1.0.0) |07 kwietnia 2015 r. |--- |
| 0.9.4-prelease |14 stycznia 2015 |29 lutego 2016 r. |
| 0.9.3-prelease |09, 2014 |29 lutego 2016 r. |
| 0.9.2-prelease |25 listopada 2014 |29 lutego 2016 r. |
| 0.9.1-prelease |23 września, 2014 |29 lutego 2016 r. |
| 0.9.0-prelease |21 sierpnia 2014 |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

