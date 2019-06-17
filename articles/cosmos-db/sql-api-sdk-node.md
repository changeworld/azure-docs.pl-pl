---
title: Usługa Azure Cosmos DB Interfejs API SQL platformy Node.js, zestaw SDK i zasoby
description: Poznaj interfejs API SQL platformy Node.js i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami zestawu SDK środowiska Node.js usługi Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 1cb6889305e5f6bce5728039712a1834dc2e9353
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626744"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw SDK środowiska Node.js usługi Azure Cosmos DB dla interfejsu API SQL: Informacje o wersji i zasoby
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

|Resource  |Łącze  |
|---------|---------|
|Pobierz zestaw SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentacja interfejsu API  |  [Dokumentacja referencyjna zestawu SDK języka JavaScript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instrukcje dotyczące instalacji zestawu SDK  |  [Instrukcje dotyczące instalacji](https://github.com/Azure/azure-cosmos-js#installation)
|Przyczynia się do zestawu SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Przykłady | [Przykładów kodu node.js](sql-api-nodejs-samples.md)
| Wprowadzenie — samouczek | [Rozpoczynanie pracy z zestawem SDK JavaScript](sql-api-nodejs-get-started.md)
| Samouczek dotyczący aplikacji sieci Web | [Tworzenie aplikacji sieci web Node.js za pomocą usługi Azure Cosmos DB](sql-api-nodejs-application.md)
| Bieżący obsługiwanych platform | [Node.js 6.x](https://nodejs.org/en/blog/release/v6.10.3/) — wartość wymagana w przypadku zestawu SDK w wersji 2.0.0 lub nowszym.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Informacje o wersji

### <a name="2.0.5"/>2.0.5</a>
* Dodaje interfejs dla węzła typu agenta. Typescript użytkownicy nie muszą już zainstalował @types/node jako zależność
* Preferowane lokalizacje teraz są poprawnie uznawane.
* Ulepszenia dotyczące współtworzenia dokumentacji dla deweloperów
* Różne poprawki Literówka

### <a name="2.0.4"/>2.0.4</a>
* Poprawki wpisz definicji wydania, wprowadzona w 2.0.3

### <a name="2.0.3"/>2.0.3</a>
* Usuń `big-integer` zależności
* Przełącz się do dyrektywy odwołań dla typu AsyncIterable. Użytkownicy języka typescript nie trzeba dostosować ich ustawienia "lib".
* Błąd pisowni poprawki

### <a name="2.0.2"/>2.0.2</a>
* Usuń łącza plik readme

### <a name="2.0.1"/>2.0.1</a>
* Napraw implementacji interfejsu ponownych prób

### <a name="2.0.0"/>2.0.0</a>
* GA 2.0.0 wersję zestawu SDK języka JavaScript
* Dodano obsługę zapisów w wielu regionach.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 wersji 2.0.0 zestaw JavaScript SDK w wersji zapoznawczej.
* Nowy model obiektów za pomocą metod CosmosClient najwyższego poziomu i Podziel na odpowiednich klas bazy danych, kontenerów i elementu. 
* Obsługa [zapewnianej w umowie](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* Zestaw SDK jest konwertowana na TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* Naprawiono dokumentację npm.

### <a name="1.14.3"/>1.14.3</a>
* Dodano obsługę domyślnego ponowień dotyczących problemów z połączeniem.
* Dodano obsługę do odczytu kolekcji zmiany źródła danych.
* Naprawiono sesji spójności usterkę, sporadycznie powodującą "odczytu sesji nie jest dostępna".
* Dodano obsługę metryki zapytania.
* Zmodyfikowane maksymalną liczbę połączeń http agenta.

### <a name="1.14.2"/>1.14.2</a>
* Zaktualizowano dokumentację na odwołanie do usługi Azure Cosmos DB zamiast bazy danych DocumentDB Azure.
* Dodano obsługę proxyUrl ustawienie ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Drobne poprawki dla systemów plików z uwzględnieniem wielkości liter.

### <a name="1.14.0"/>1.14.0</a>
* Dodaje obsługę spójność sesji.
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora usługi Azure Cosmos dostępne do pobrania z https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Podziel potwierdzone wielu partycji zapytań.
* Dodaje obsługiwanych przez link do zasobu z początkowe i końcowe ukośniki (i odpowiadających testów).

### <a name="1.12.2"/>1.12.2</a>
*   Naprawiono dokumentację npm.

### <a name="1.12.1"/>1.12.1</a>
* Usunięto usterkę executeStoredProcedure, w którym dokumenty ma specjalne znaków Unicode (LS, PS).
* Usunięto usterkę podczas obsługi dokumentów ze znakami Unicode w kluczu partycji.
* Naprawiono obsługę tworzenia kolekcji za pomocą nośnika nazwy. Problem w usłudze GitHub #114.
* Naprawiono obsługę token autoryzacji uprawnień. Problem w usłudze GitHub #178.

### <a name="1.12.0"/>1.12.0</a>
* Dodano obsługę nowego [poziomu spójności](consistency-levels.md) o nazwie ConsistentPrefix.
* Dodano obsługę UriFactory.
* Usunięto usterkę obsługi standardu Unicode. Problem w usłudze GitHub #171.

### <a name="1.11.0"/>1.11.0</a>
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia).
* Dodano opcję do kontrolowania stopień równoległości dla wielu partycji zapytań.
* Dodano opcję wyłączenia protokołu SSL weryfikacji przy uruchamianiu emulatora usługi Azure Cosmos DB.
* Obniżona minimalna przepływność na kolekcji podzielonych na partycje z 10,100 jednostek RU/s 2500 jednostek RU/s.
* Naprawiono usterkę tokenu kontynuacji dla kolekcji z jedną partycją. Problem w usłudze GitHub #107.
* Naprawiono usterkę executeStoredProcedure obsługi 0 jako pojedynczy parametr. Problem w usłudze GitHub #155.

### <a name="1.10.2"/>1.10.2</a>
* Naprawiono agenta użytkownika nagłówek do uwzględnienia wersji zestawu SDK.
* Oczyszczanie kodu.

### <a name="1.10.1"/>1.10.1</a>
* Wyłączanie weryfikacji protokołu SSL, korzystając z zestawu SDK pod kątem emulator(hostname=localhost).
* Dodano obsługę włączania rejestrowania skryptu podczas wykonywania procedury składowanej.

### <a name="1.10.0"/>1.10.0</a>
* Dodano obsługę dla wielu partycji zapytania równolegle.
* Dodano obsługę TOP/ORDER BY zapytania dla kolekcji podzielonych na partycje.

### <a name="1.9.0"/>1.9.0</a>
* Obsługa zasad ponawiania dodano żądania ograniczone. (Żądania ograniczone odbierać żądania stawki za duży wyjątek, kod błędu 429). Domyślnie usługa Azure Cosmos DB ponawia próbę dziewięć razy dla każdego żądania po napotkaniu kod błędu 429 zapewniane czasu retryAfter w nagłówku odpowiedzi. Czas interwału ponowień stałych można teraz nelze nastavit jako część właściwość RetryOptions obiektu ConnectionPolicy aby zignorować czasu retryAfter zwrócony przez serwer między ponownymi próbami. Usługa Azure Cosmos DB jest teraz czeka maksymalnie 30 sekund w przypadku każdego żądania, który jest ograniczane (niezależnie od liczby ponownych prób), a następnie zwraca odpowiedź z kodem błędu 429. Teraz można przesłonić, we właściwości RetryOptions ConnectionPolicy obiektu.
* Usługi cosmos DB teraz zwraca x-ms ograniczania — liczbę ponownych prób — i x-ms-throttle-retry-wait-time-ms nagłówków odpowiedzi każdego żądania do oznaczania dławienie, spróbuj ponownie liczba i skumulowany czas żądania oczekiwania między ponownymi próbami.
* Klasa RetryOptions został dodany, udostępnianie właściwości RetryOptions klasy ConnectionPolicy, który może służyć do nadpisania niektóre domyślne opcje ponawiania prób.

### <a name="1.8.0"/>1.8.0</a>
* Dodano obsługę multiregionalne konta baz danych.

### <a name="1.7.0"/>1.7.0</a>
* Dodano obsługę funkcji czasu i Live(TTL) dokumentów.

### <a name="1.6.0"/>1.6.0</a>
* Zaimplementowane [podzielona na partycje kolekcje](partition-data.md) i [poziomów wydajności zdefiniowanych przez użytkownika](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Usunięto usterkę RangePartitionResolver.resolveForRead, w którym nie została zwraca łącza z powodu nieprawidłowych concat wyników.

### <a name="1.5.5"/>1.5.5</a>
* Naprawiono hashPartitionResolver resolveForRead(): Po klucza partycji, nie podano został zgłaszania wyjątku, zamiast zwracać listę wszystkich zarejestrowanych łączy.

### <a name="1.5.4"/>1.5.4</a>
* Rozwiązano problem [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -Agent protokołu HTTPS w wersji dedykowanej: Należy unikać modyfikowania globalne właściwości agenta dla celów usługi Azure Cosmos DB. Użyj dedykowanych agenta dla wszystkich żądań lib.

### <a name="1.5.3"/>1.5.3</a>
* Rozwiązano problem [#81](https://github.com/Azure/azure-documentdb-node/issues/81) — poprawnie obsługiwać myślniki identyfikatory nośników.

### <a name="1.5.2"/>1.5.2</a>
* Rozwiązano problem [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -odbiornika EventEmitter przecieku ostrzeżenie.

### <a name="1.5.1"/>1.5.1</a>
* Rozwiązano problem [#92](https://github.com/Azure/azure-documentdb-node/issues/90) — Zmień nazwę folderu wyznaczania wartości skrótu do wyznaczania wartości skrótu dla systemów z uwzględnieniem wielkości liter.

### <a name="1.5.0"/>1.5.0</a>
* Obsługa fragmentowania implementują przez dodanie skrótów zak & res rozwiązujący partycji.

### <a name="1.4.0"/>1.4.0</a>
* Implementowanie Upsert. Nowe metody upsertXXX na documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Pominięte, aby wyświetlić numery wersji w sposób zgodny z innymi zestawami SDK.

### <a name="1.2.2"/>1.2.2</a>
* Podział funkcji pytania i odpowiedzi zapewnia otok dla nowego repozytorium.
* Zaktualizuj plik pakietu dla rejestr pakietów npm.

### <a name="1.2.1"/>1.2.1</a>
* Implementuje identyfikator, na podstawie routingu.
* Rozwiązano problem [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -bieżącą właściwość typu jest w konflikcie z current() metody.

### <a name="1.2.0"/>1.2.0</a>
* Dodano obsługę dla danych Geoprzestrzennych indeksu.
* Sprawdza poprawność właściwość identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie może zawierać?, /, #, &#47; &#47;, znaków ani kończyć się spacją.
* Dodaje nowy nagłówek "indeks przekształcania progress" ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementuje zasady indeksowania w wersji 2.

### <a name="1.0.3"/>1.0.3</a>
* Problem [#40](https://github.com/Azure/azure-documentdb-node/issues/40) — zaimplementowane eslint grunt konfiguracje w obszarach podstawowych i promise zestawu SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -otoki obietnic nie zawiera nagłówka z powodu błędu.

### <a name="1.0.1"/>1.0.1</a>
* Zaimplementowano możliwość wysyłania zapytań do konfliktów, dodając readConflicts readConflictAsync i queryConflicts.
* Zaktualizowano dokumentację interfejsu API.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) — błąd client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Wersja & dat wycofywania
Firma Microsoft zapewnia powiadomienie co najmniej **12 miesięcy** ewentualnej wycofanie zestawu SDK w celu złagodzenia przejścia do nowszych/obsługiwanych wersji.

Nowe funkcje i funkcjonalność i optymalizacje są dodawane tylko do bieżącego zestawu SDK, w związku z tym zalecane jest, zawsze uaktualnienie do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

Wszelkie żądania do usługi Cosmos DB przy użyciu wycofane zestawu SDK jest odrzucane przez usługę.

<br/>

| Version | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 sierpnia 2018 r. |--- |
| [1.14.4](#1.14.4) |03 maja 2018 r. |--- |
| [1.14.3](#1.14.3) |03 maja 2018 r. |--- |
| [1.14.2](#1.14.2) |21 grudnia 2017 r. |--- |
| [1.14.1](#1.14.1) |10 listopada 2017 r. |--- |
| [1.14.0](#1.14.0) |9 listopada 2017 r. |--- |
| [1.13.0](#1.13.0) |11 października 2017 r. |--- |
| [1.12.2](#1.12.2) |10 sierpnia 2017 r. |--- |
| [1.12.1](#1.12.1) |10 sierpnia 2017 r. |--- |
| [1.12.0](#1.12.0) |10 maja 2017 |--- |
| [1.11.0](#1.11.0) |16 marca 2017 r. |--- |
| [1.10.2](#1.10.2) |27 stycznia 2017 r. |--- |
| [1.10.1](#1.10.1) |22 grudnia 2016 r. |--- |
| [1.10.0](#1.10.0) |03 października 2016 r. |--- |
| [1.9.0](#1.9.0) |07 lipca 2016 r. |--- |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |--- |
| [1.7.0](#1.7.0) |26 kwietnia 2016 r. |--- |
| [1.6.0](#1.6.0) |29 marca 2016 r. |--- |
| [1.5.6](#1.5.6) |08 marca 2016 r. |--- |
| [1.5.5](#1.5.5) |02 lutego 2016 r. |--- |
| [1.5.4](#1.5.4) |01 lutego 2016 r. |--- |
| [1.5.2](#1.5.2) |26 stycznia 2016 r. |--- |
| [1.5.2](#1.5.2) |22 stycznia 2016 r. |--- |
| [1.5.1](#1.5.1) |4 stycznia 2016 roku. |--- |
| [1.5.0](#1.5.0) |Do 31 grudnia 2015 r. |--- |
| [1.4.0](#1.4.0) |06 października 2015 r. |--- |
| [1.3.0](#1.3.0) |06 października 2015 r. |--- |
| [1.2.2](#1.2.2) |10 września 2015 r. |--- |
| [1.2.1](#1.2.1) |15 sierpnia 2015 r. |--- |
| [1.2.0](#1.2.0) |05 sierpnia 2015 r. |--- |
| [1.1.0](#1.1.0) |09 lipca 2015 r. |--- |
| [1.0.3](#1.0.3) |04 czerwca 2015 r. |--- |
| [1.0.2](#1.0.2) |23 maja 2015 r. |--- |
| [1.0.1](#1.0.1) |15 maja 2015 r. |--- |
| [1.0.0](#1.0.0) |08 kwietnia 2015 r. |--- |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

