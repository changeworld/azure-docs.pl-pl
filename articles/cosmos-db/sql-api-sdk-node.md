---
title: 'Azure DB rozwiązania Cosmos: SQL interfejsu API środowiska Node.js SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejsu API środowiska Node.js SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany wprowadzone od każdej wersji zestawu SDK platformy Azure rozwiązania Cosmos DB Node.js.
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68c124bc2a89c819eb72310138ddd5d0c7739c4d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure rozwiązania Cosmos DB Node.js SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródła danych zmian .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Pobierz zestaw SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacji interfejsu API środowiska node.js](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Instrukcje dotyczące instalacji zestawu SDK**</td><td>[Instrukcje dotyczące instalacji](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Przyczyniają się do zestawu SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Przykłady**</td><td>[Przykłady kodu node.js](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**Samouczek z wprowadzeniem**</td><td>[Wprowadzenie do zestawu Node.js SDK](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**Samouczek aplikacji sieci Web**</td><td>[Tworzenie aplikacji sieci web Node.js za pomocą bazy danych Azure rozwiązania Cosmos](sql-api-nodejs-application.md)</td></tr>

<tr><td>**Bieżący obsługiwanych platform**</td><td> 
[6.x node.js](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

### <a name="1.14.2"/>1.14.2</a>
* Zaktualizowana dokumentacja odwołania do bazy danych rozwiązania Cosmos Azure zamiast usługi Azure DocumentDB.
* Dodano obsługę ustawienie proxyUrl w ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Drobne poprawki dla systemów plików z uwzględnieniem wielkości liter.

### <a name="1.14.0"/>1.14.0</a>
* Dodaje obsługę spójność sesji.
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora DB rozwiązania Cosmos Azure dostępne do pobrania z https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Podziel potwierdzone cross partycji zapytań.
* Dodaje obsługuje łącza zasobów z początkowe i końcowe ukośniki (i odpowiadających testów).

### <a name="1.12.2"/>1.12.2</a>
*   Dokumentacja programu npm stałej.

### <a name="1.12.1"/>1.12.1</a>
* Stała usterki executeStoredProcedure, w którym dokumenty ma specjalne znaków Unicode (LS, PS).
* Stałe usterki w obsłudze dokumenty zawierające znaki Unicode w kluczu partycji.
* Stały obsługę tworzenia kolekcji z nośnika nazwy. Problem Github #114.
* Stałe obsługę token autoryzacji uprawnień. Problem Github #178.

### <a name="1.12.0"/>1.12.0</a>
* Dodano nową obsługę [poziomu spójności](consistency-levels.md) o nazwie ConsistentPrefix.
* Dodano obsługę UriFactory.
* Stałe błędów obsługę standardu Unicode. Problem GitHub #171.

### <a name="1.11.0"/>1.11.0</a>
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i Śr.).
* Dodano opcja kontrolowania stopień równoległości dla wielu partycji zapytań.
* Dodano opcję wyłączenie protokołu SSL weryfikacji podczas uruchamiania emulatora DB rozwiązania Cosmos Azure.
* Obniżony minimalnej przepustowości w kolekcji partycjonowanych z 10,100 RU/s do 2500 RU/s.
* Stałe błędów token kontynuacji dla kolekcji jednej partycji. Problem Github #107.
* Stałe błędów executeStoredProcedure obsługi 0 jako pojedynczy parametr w. Problem Github #155.

### <a name="1.10.2"/>1.10.2</a>
* Stałe agenta użytkownika nagłówek do uwzględnienia wersja zestawu SDK.
* Oczyszczanie kodu pomocniczych.

### <a name="1.10.1"/>1.10.1</a>
* Wyłączanie weryfikacji protokołu SSL podczas korzystania z zestawu SDK pod kątem emulator(hostname=localhost).
* Dodano obsługę Włączanie rejestrowania skryptu podczas wykonywania procedury składowanej.

### <a name="1.10.0"/>1.10.0</a>
* Dodano obsługę dla wielu partycji zapytania równoległe.
* Dodano obsługę TOP/ORDER BY zapytania dla kolekcji partycjonowanych.

### <a name="1.9.0"/>1.9.0</a>
* Obsługa zasad dodano ponownych prób dla żądań z ograniczeniem przepustowości. (Ograniczeniem przepustowości żądania odbierania żądania szybkość zbyt duży wyjątek, kod błędu 429.) Domyślnie bazy danych Azure rozwiązania Cosmos ponowi próbę dziewięciokrotnie dla każdego żądania po napotkaniu błędu kodu 429 ramach czasu retryAfter nagłówka odpowiedzi. Czas interwału ponawiania stałym teraz można ustawić jako część właściwości RetryOptions w obiekcie ConnectionPolicy aby zignorować godzinę retryAfter zwrócony przez serwer między ponownymi próbami. Azure DB rozwiązania Cosmos teraz czeka na maksymalnie 30 sekund dla każdego żądania jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Teraz również może zostać przesłonięta we właściwości RetryOptions ConnectionPolicy obiektu.
* Rozwiązania cosmos DB teraz zwraca x-ms ograniczania liczby ponownych prób i x-ms-throttle-retry-wait-time-ms nagłówków odpowiedzi każde żądanie do oznaczania przepustnicy ponów liczby i skumulowany czas żądanie oczekiwało między ponownymi próbami.
* Klasa RetryOptions został dodany, udostępnianie właściwości RetryOptions klasy ConnectionPolicy, który może służyć do zastępowania niektórych domyślne opcje ponownych prób.

### <a name="1.8.0"/>1.8.0</a>
* Dodano obsługę konta w przypadku bazy danych.

### <a name="1.7.0"/>1.7.0</a>
* Dodano obsługę funkcji czas do Live(TTL) dokumentów.

### <a name="1.6.0"/>1.6.0</a>
* Zaimplementowane [kolekcje partycjonowane](partition-data.md) i [poziomy wydajności zdefiniowanych przez użytkownika](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Usunięte usterki RangePartitionResolver.resolveForRead gdzie nie został zwrócił łącza z powodu nieprawidłowych concat wyników.

### <a name="1.5.5"/>1.5.5</a>
* Stałe hashParitionResolver resolveForRead(): gdy nie podany klucz partycji została Zgłaszanie wyjątku, zamiast zwracać listę wszystkich zarejestrowanych łączy.

### <a name="1.5.4"/>1.5.4</a>
* Rozwiązuje problem [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -agenta w wersji dedykowanej HTTPS: unikać modyfikacji globalne agenta do celów bazy danych Azure rozwiązania Cosmos. Użyj dedykowanych agenta dla wszystkich żądań lib.

### <a name="1.5.3"/>1.5.3</a>
* Rozwiązuje problem [#81](https://github.com/Azure/azure-documentdb-node/issues/81) — poprawnie obsłużyć myślniki identyfikatory nośników.

### <a name="1.5.2"/>1.5.2</a>
* Rozwiązuje problem [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -odbiornika EventEmitter wyciek ostrzeżenie.

### <a name="1.5.1"/>1.5.1</a>
* Rozwiązuje problem [#92](https://github.com/Azure/azure-documentdb-node/issues/90) — Zmień nazwę folderu skrót do wyznaczania wartości skrótu dla systemów z uwzględnieniem wielkości liter.

### <a name="1.5.0"/>1.5.0</a>
* Implementowanie obsługi dzielenia na fragmenty przez dodanie skrótów & zakres rozpoznawania nazw partycji.

### <a name="1.4.0"/>1.4.0</a>
* Implementuje Upsert. Nowych metod upsertXXX na documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Pominięto przenieść numery wersji wyrównania z innych zestawów SDK.

### <a name="1.2.2"/>1.2.2</a>
* Q podziału niesie obietnice otok dla nowego repozytorium.
* Zaktualizuj do pliku pakietu do rejestru npm.

### <a name="1.2.1"/>1.2.1</a>
* Implementuje identyfikator oparte na routingu.
* Rozwiązuje problem [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -bieżącej właściwości powoduje konflikt z obiekt current() metody.

### <a name="1.2.0"/>1.2.0</a>
* Dodano obsługę dla indeksu dane geograficzne.
* Weryfikuje właściwość identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie może zawierać?, /, #, &#47; &#47;, znaków ani kończyć spacją.
* Dodaje nowego nagłówka "indeksu przekształcania toku" do ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementuje zasady indeksowania V2.

### <a name="1.0.3"/>1.0.3</a>
* Problem [#40](https://github.com/Azure/azure-documentdb-node/issues/40) — zaimplementowana eslint grunt konfiguracje podstawowe i promise zestawu SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -otoki ze zobowiązania nie zawiera nagłówka z powodu błędu.

### <a name="1.0.1"/>1.0.1</a>
* Zaimplementowany możliwość wykonywania kwerend konflikty, dodając readConflicts, readConflictAsync i queryConflicts.
* Zaktualizowana dokumentacja interfejsu API.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) — błąd client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Wersja & daty wycofania
Firma Microsoft udostępnia powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze Uaktualnij zestaw SDK najnowszą tak szybko jak to możliwe.

Każde żądanie przy użyciu rozwiązania Cosmos DB wycofane zestawu SDK jest odrzucane przez usługę.

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
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
| [1.5.1](#1.5.1) |4 stycznia 2016 r. |--- |
| [1.5.0](#1.5.0) |31 grudnia 2015 r. |--- |
| [1.4.0](#1.4.0) |06 października 2015 |--- |
| [1.3.0](#1.3.0) |06 października 2015 |--- |
| [1.2.2](#1.2.2) |10 września 2015 |--- |
| [1.2.1](#1.2.1) |15 sierpnia 2015 |--- |
| [1.2.0](#1.2.0) |05 sierpnia 2015 |--- |
| [1.1.0](#1.1.0) |09 lipca 2015 r. |--- |
| [1.0.3](#1.0.3) |04 czerwiec 2015 r. |--- |
| [1.0.2](#1.0.2) |23 maja 2015 r. |--- |
| [1.0.1](#1.0.1) |15 maja 2015 r. |--- |
| [1.0.0](#1.0.0) |08 kwietnia 2015 r. |--- |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

