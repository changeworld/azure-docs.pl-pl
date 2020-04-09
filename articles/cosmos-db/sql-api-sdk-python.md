---
title: 'Usługa Azure Cosmos DB: interfejs API języka SQL Python, zestaw SDK & zasoby'
description: Dowiedz się wszystkiego o interfejsie API i SDK języka SQL Python, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją zestawu SDK języka Azure Cosmos DB Python.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: b81a3921ec11d589dadbdebd698ab9ad67d7649c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982909"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw Azure Cosmos DB Python SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Plik danych o zmianach w sieci .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Wykonawca luzem - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Pobierz SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API języka Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instrukcje instalacji SDK**|[Instrukcje instalacji zestawów SDK języka Python](https://github.com/Azure/azure-cosmos-python)|
|**Współtworzenie sdk**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Rozpoczęcie pracy**|[Wprowadzenie do sdk języka Python](sql-api-python-application.md)|
|**Aktualnie obsługiwana platforma**|[Python 2.7](https://www.python.org/downloads/) i [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Dodano obsługę typu danych MultiPolygon
* Poprawka błędu w zasadach ponawiania poprawek odczytu sesji
* Poprawka problemu z nieprawidłowymi problemami z dopełnieniem podczas dekodowania podstawowych ciągów 64

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Poprawka błędu w locationcache
* Logika ponawiania ponawiania punktu końcowego poprawki błędu
* Stała dokumentacja

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Obsługa zapisów wieloregionowych.
* Obszar nazw został zmieniony na azure.cosmos.
* Pojęcia dotyczące kolekcji i dokumentów zostały zmienione na kontener i element, document_client zmieniono nazwę na cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Dodano obsługę serwera proxy
* Dodano obsługę odczytu kanału zmian
* Dodano obsługę nagłówków przydziałów kolekcji
* Poprawka błędu dla problemu z tokenami dużych sesji
* Poprawka błędu dla interfejsu API ReadMedia
* Poprawka błędu w pamięci podręcznej zakresu kluczy partycji

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Zaktualizowano dokumentację, aby odwoływać się do usługi Azure Cosmos DB zamiast usługi Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Ta wersja SDK wymaga najnowszej wersji [emulatora usługi Azure Cosmos DB.](https://aka.ms/cosmosdb-emulator)

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Poprawka błędu dla słownika agregacji.
* Poprawka błędu przycinania ukośników w łączu zasobu.
* Dodano testy kodowania Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję wyłączania weryfikacji TLS podczas uruchamiania emulatora bazy danych Usługi Cosmos.
* Usunięto ograniczenie modułu żądań zależnych, aby mieć dokładnie 2.10.0.
* Obniżono minimalną przepustowość w kolekcjach podzielonych na partycje z 10 100 RU/s do 2500 RU/s.
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.
* Wersja interfejsu API REST wpadła na '2017-01-19' w tej wersji.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Wprowadzono zmiany redakcyjne w komentarzach do dokumentacji.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Dodano obsługę Pythona 3.5.
* Dodano obsługę buforowania połączeń przy użyciu modułu żądań.
* Dodano obsługę spójności sesji.
* Dodano obsługę zapytań TOP/ORDERBY dla kolekcji podzielonych na partycje.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Dodano obsługę zasad ponawiania dla żądań ograniczonych. (Żądania ograniczone otrzymują zbyt duży wyjątek, kod błędu 429). Domyślnie usługa Azure Cosmos DB ponawia dziewięć razy dla każdego żądania, gdy zostanie napotkany kod błędu 429, honorując ponawianie czasu po wierszu w nagłówku odpowiedzi. Czas stałego interwału ponawiania próby można teraz ustawić jako część właściwości RetryOptions w obiekcie ConnectionPolicy, jeśli chcesz zignorować ponowny ponawianieczasu zwróconego przez serwer między ponownych prób. Usługa Azure Cosmos DB czeka teraz maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Ten czas można również zastąpić we właściwości RetryOptions na ConnectionPolicy obiektu.
* Usługa Cosmos DB zwraca teraz x-ms-throttle-retry-count i x-ms-throttle-retry-wait-time-ms jako nagłówki odpowiedzi w każdym żądaniu, aby oznaczyć liczbę ponownych prób przepustnicy i skumulowany czas oczekiwania między ponownych prób.
* Usunięto RetryPolicy klasy i odpowiedniej właściwości (retry_policy) narażone na document_client klasy i zamiast tego wprowadzono RetryOptions klasy odsłaniając RetryOptions właściwości connectionpolicy klasy, które mogą służyć do zastąpienia niektórych domyślnych opcji ponawiania.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę kont bazy danych w wielu regionach.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Dodano obsługę funkcji Time To Live (TTL) dla dokumentów.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Poprawki błędów związane z partycjonowanie po stronie serwera, aby umożliwić znaki specjalne w ścieżce klucza partycji.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Zaimplementowano [kolekcje podzielone na partycje](partition-data.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Dodaj program rozpoznawania partycji Hash & Range, aby pomóc w aplikacji dzielenia na fragmenty na wielu partycjach.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Zaimplementuj upsert. Nowe metody UpsertXXX dodane do obsługi funkcji Upsert.
* Implementowanie routingu opartego na identyfikatorze. Brak zmian w publicznym interfejsie API, wszystkie zmiany wewnętrzne.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Obsługuje indeks GeoSpatial.
* Sprawdza poprawność właściwości identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie mogą zawierać ?, \, /, #, znaków ani kończyć spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania V2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Obsługuje połączenie proxy.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Zwolnij & daty przejścia na emeryturę
Firma Microsoft powiadamia o wycofaniu sdk sdk z co najmniej **12-miesięcznym** wyprzedzeniem w celu usprawnienia przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego sdk, jako takie zaleca się, aby zawsze uaktualnić do najnowszej wersji SDK tak wcześnie, jak to możliwe. 

Każde żądanie do usługi Cosmos DB przy użyciu wycofanego SDK są odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje interfejsu API języka Python dla SQL przed wersją **1.0.0** zostały wycofane **29 lutego 2016**r. 
> 
> 

> [!WARNING]
> Wszystkie wersje 1.x i 2.x interfejsu API języka Python dla SQL zostaną wycofane **30 sierpnia 2020**r. 
> 
> 

<br/>

| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 listopada 2018 r. |--- |
| [3.0.1](#3.0.1) |4 października 2018 r. |--- |
| [2.3.3](#2.3.3) |8 września 2018 r. |30 sierpnia 2020 r. |
| [2.3.2](#2.3.2) |8 maja 2018 r. |30 sierpnia 2020 r. |
| [2.3.1](#2.3.1) |21 grudnia 2017 r. |30 sierpnia 2020 r. |
| [2.3.0](#2.3.0) |10 listopada 2017 r. |30 sierpnia 2020 r. |
| [2.2.1](#2.2.1) |29 września 2017 r. |30 sierpnia 2020 r. |
| [2.2.0](#2.2.0) |10 maja 2017 |30 sierpnia 2020 r. |
| [2.1.0](#2.1.0) |1 maja 2017 r. |30 sierpnia 2020 r. |
| [2.0.1](#2.0.1) |30 października 2016 r. |30 sierpnia 2020 r. |
| [2.0.0](#2.0.0) |29 września 2016 r. |30 sierpnia 2020 r. |
| [1.9.0](#1.9.0) |7 lipca 2016 r. |30 sierpnia 2020 r. |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |30 sierpnia 2020 r. |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |30 sierpnia 2020 r. |
| [1.6.1](#1.6.1) |8 kwietnia 2016 r. |30 sierpnia 2020 r. |
| [1.6.0](#1.6.0) |29 marca 2016 r. |30 sierpnia 2020 r. |
| [1.5.0](#1.5.0) |3 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.4.2](#1.4.2) |6 października 2015 r. |30 sierpnia 2020 r. |
| 1.4.1 |6 października 2015 r. |30 sierpnia 2020 r. |
| [1.2.0](#1.2.0) |6 sierpnia 2015 r. |30 sierpnia 2020 r. |
| [1.1.0](#1.1.0) |9 lipca 2015 r. |30 sierpnia 2020 r. |
| [1.0.1](#1.0.1) |25 maja 2015 r. |30 sierpnia 2020 r. |
| [1.0.0](#1.0.0) |7 kwietnia 2015 r. |30 sierpnia 2020 r. |
| 0.9.4-prelease |14 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.3-prelease |9 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.2-prelease |25 listopada 2014 r. |29 lutego 2016 r. |
| 0.9.1-prelease |23 września 2014 r. |29 lutego 2016 r. |
| 0.9.0-prelease |21 sierpnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) 

