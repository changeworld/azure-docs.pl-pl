---
title: Usługa Azure Cosmos DB Interfejs API SQL języka Python, zestaw SDK i zasoby
description: Poznaj interfejs API SQL języka Python i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami zestawu SDK Python usługi Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 9903339cbf0958893fb0d11a8c1b6ab7d156aae7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626795"
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
> * [Bulkexecutor — platforma .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkexecutor — platforma Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Pobierz zestaw SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API języka Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instrukcje dotyczące instalacji zestawu SDK**|[Instrukcje dotyczące instalacji zestawu SDK języka Python](https://github.com/Azure/azure-cosmos-python)|
|**Przyczynia się do zestawu SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Wprowadzenie**|[Wprowadzenie do zestawu SDK języka Python](sql-api-python-application.md)|
|**Bieżący obsługiwanych platform**|[Python 2.7](https://www.python.org/downloads/) i [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Dodano obsługę dla obiektów MultiPolygon typu danych
* Naprawienie usterki w sesji przeczytać zasady ponawiania
* Naprawienie usterki niepoprawne dopełnienie problemów podczas dekodowania ciągów 64 podstawowy

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Naprawienie usterki w LocationCache
* Logika ponawiania punktu końcowego naprawienie usterki
* Naprawiono dokumentację

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Obsługa wielu regionów zapisy.
* Namespace zmieniony na azure.cosmos.
* Zmieniono nazwę kontenera i elementów, document_client zmieniona na cosmos_client kolekcji i dokumentu pojęcia. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Dodano obsługę serwera proxy
* Dodano obsługę Kanał informacyjny zmian do czytania
* Dodano obsługę dla nagłówków limitu przydziału kolekcji
* Poprawka dla dużych sesji tokenów problem
* Poprawka dla ReadMedia interfejsu API
* Poprawka w pamięci podręcznej zakres kluczy partycji

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Dodano obsługę domyślnego ponowień dotyczących problemów z połączeniem.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Zaktualizowano dokumentację na odwołanie do usługi Azure Cosmos DB zamiast bazy danych DocumentDB Azure.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora usługi Azure Cosmos dostępne do pobrania z https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Naprawienie usterki agregującej słownika.
* Naprawienie usterki przycinania ukośników w link do zasobu.
* Dodano testuje pod kątem kodowanie Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę nowego poziomu spójności, nazywana ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Dodano opcję wyłączenia protokołu SSL weryfikacji przy uruchamianiu emulatora usługi Cosmos DB.
* Usunięte ograniczenia można dokładnie 2.10.0 moduł zależne żądania.
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.
* Dodano obsługę włączania rejestrowania skryptu podczas wykonywania procedury składowanej.
* Wersja interfejsu API REST zaktualizowany do "2017-01-19' w tej wersji.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Wprowadzone zmiany redakcyjne komentarze dokumentacji.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Dodano obsługę języka Python 3.5.
* Dodano obsługę buforowania połączeń przy użyciu modułu żądań.
* Dodano obsługę spójność sesji.
* Dodano obsługę TOP/ORDERBY zapytania dla kolekcji podzielonych na partycje.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Obsługa zasad ponawiania dodano żądania ograniczone. (Żądania ograniczone odbierać żądania stawki za duży wyjątek, kod błędu 429). Domyślnie usługa Azure Cosmos DB ponawia próbę dziewięć razy dla każdego żądania po napotkaniu kod błędu 429 zapewniane czasu retryAfter w nagłówku odpowiedzi. Czas interwału ponowień stałych można teraz nelze nastavit jako część właściwość RetryOptions obiektu ConnectionPolicy aby zignorować czasu retryAfter zwrócony przez serwer między ponownymi próbami. Usługa Azure Cosmos DB jest teraz czeka maksymalnie 30 sekund w przypadku każdego żądania, który jest ograniczane (niezależnie od liczby ponownych prób), a następnie zwraca odpowiedź z kodem błędu 429. Teraz można przesłonić, we właściwości RetryOptions ConnectionPolicy obiektu.
* Usługi cosmos DB teraz zwraca x-ms ograniczania — liczbę ponownych prób — i x-ms-throttle-retry-wait-time-ms nagłówków odpowiedzi każdego żądania do oznaczania dławienie, spróbuj ponownie liczba i skumulowany czas żądania oczekiwania między ponownymi próbami.
* Usunięto klasy RetryPolicy i odpowiadającą właściwość (retry_policy) udostępniane w klasie document_client i zamiast tego wprowadzono klasy RetryOptions udostępnianie właściwości RetryOptions ConnectionPolicy klasy, który może służyć do nadpisania niektóre domyślne opcje ponawiania prób.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę multiregionalne konta baz danych.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dodano obsługę funkcji czasu i Live(TTL) dokumentów.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Poprawki dotyczące partycjonowania po stronie serwera, aby zezwalać na znaki specjalne w ścieżce klucza partycji.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Zaimplementowane [podzielona na partycje kolekcje](partition-data.md) i [poziomów wydajności zdefiniowanych przez użytkownika](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Dodaj skrót zak & res partycji rozwiązujący uzyskanymi fragmentowania aplikacje na wielu partycjach.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementowanie Upsert. Nowe metody UpsertXXX dodanych do obsługi funkcji Upsert.
* Routing oparty na identyfikator implementacji. Brak publicznego zmian interfejsu API, wszystkie zmiany wewnętrznego.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Obsługa danych Geoprzestrzennych indeks.
* Sprawdza poprawność właściwość identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie może zawierać?, /, #, \, znaków ani kończyć się spacją.
* Dodaje nowy nagłówek "indeks przekształcania progress" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania w wersji 2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Obsługuje połączenia serwera proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Nowe funkcje i funkcjonalność i optymalizacje są dodawane tylko do bieżącego zestawu SDK, w związku z tym jest zalecane, zawsze uaktualnienie do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Wszelkie żądania do usługi Cosmos DB przy użyciu wycofane zestawu SDK są odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje zestawu SDK usługi Azure SQL dla języka Python wcześniejszymi niż wersja **1.0.0** zostały wycofane z dniem **do 29 lutego 2016 r**. 
> 
> 

<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 listopada 2018 r. |--- |
| [3.0.1](#3.0.1) |04 października 2018 r. |--- |
| [2.3.3](#2.3.3) |08 września 2018 r. |--- |
| [2.3.2](#2.3.2) |08 maja 2018 r. |--- |
| [2.3.1](#2.3.1) |21 grudnia 2017 r. |--- |
| [2.3.0](#2.3.0) |10 listopada 2017 r. |--- |
| [2.2.1](#2.2.1) |29 września 2017 r. |--- |
| [2.2.0](#2.2.0) |10 maja 2017 |--- |
| [2.1.0](#2.1.0) |01 maja 2017 r. |--- |
| [2.0.1](#2.0.1) |30 października 2016 r. |--- |
| [2.0.0](#2.0.0) |29 września 2016 r. |--- |
| [1.9.0](#1.9.0) |07 lipca 2016 r. |--- |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |--- |
| [1.7.0](#1.7.0) |26 kwietnia 2016 r. |--- |
| [1.6.1](#1.6.1) |08 kwietnia 2016 r. |--- |
| [1.6.0](#1.6.0) |29 marca 2016 r. |--- |
| [1.5.0](#1.5.0) |03 stycznia 2016 roku. |--- |
| [1.4.2](#1.4.2) |06 października 2015 r. |--- |
| 1.4.1 |06 października 2015 r. |--- |
| [1.2.0](#1.2.0) |06 sierpnia 2015 r. |--- |
| [1.1.0](#1.1.0) |09 lipca 2015 r. |--- |
| [1.0.1](#1.0.1) |25 maja 2015 r. |--- |
| [1.0.0](#1.0.0) |07 kwietnia 2015 r. |--- |
| 0.9.4-prelease |14 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.3-prelease |09 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.2-prelease |25 listopada 2014 r. |29 lutego 2016 r. |
| 0.9.1-prelease |23 września 2014 r. |29 lutego 2016 r. |
| 0.9.0-prelease |21 sierpnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi. 

