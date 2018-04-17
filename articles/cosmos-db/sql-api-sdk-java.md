---
title: 'Azure rozwiązania Cosmos bazy danych: Interfejsu API języka Java SQL, zestaw SDK & zasobów | Dokumentacja firmy Microsoft'
description: Dowiedz się wszystkiego o interfejsu API języka Java SQL i zestawu SDK, w tym daty wydania, daty wycofania i zmiany wprowadzone od każdej wersji zestawu SDK Java SQL platformy Azure rozwiązania Cosmos bazy danych.
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 11/14/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a89b2e2d352b614323c7d7ab136aec325c590b2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure rozwiązania Cosmos DB Java SDK dla interfejsu API SQL: informacje o wersji i zasoby
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

Zestaw SDK Java interfejsu API SQL obsługuje synchronicznych operacji. Obsługa komunikacji asynchronicznej, można użyć [SQL zestawu SDK Java asynchroniczne dla interfejsu API](sql-api-sdk-async-java.md). 

<table>

<tr><td>**Pobierz zestaw SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**Dokumentacja interfejsu API**</td><td>[Dokumentacji interfejsu API języka Java](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Przyczyniają się do zestawu SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Wprowadzenie**</td><td>[Rozpoczynanie pracy z zestawu Java SDK](sql-api-java-get-started.md)</td></tr>

<tr><td>**Samouczek aplikacji sieci Web**</td><td>[Tworzenie aplikacji sieci Web z bazy danych Azure rozwiązania Cosmos](sql-api-java-application.md)</td></tr>

<tr><td>**Minimalna obsługiwana środowiska wykonawczego**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Zwiększona wydajność serializacji Json.
* Ta wersja zestawu SDK wymaga najnowszej wersji emulatora DB rozwiązania Cosmos Azure dostępne do pobrania z https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Wewnętrzny zmiany dla bibliotek znajomych firmy Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Rozwiązano problem podczas odczytywania klucza zakresy jednej partycji.
* Rozwiązano problem w ResourceID analizy, który ma wpływ na bazę danych krótkie nazwy.
* Stałe przyczyną problemu kodowanie klucza partycji.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Poprawki błędów krytycznych do żądania przetwarzania podczas podziałów partycji.
* Rozwiązano problem z poziomy spójności silne i BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
* Stałe błędów w kolekcji sesji w trybie odczytu.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Włączona obsługa kolekcji partycjonowanych jako niskie jako 2500 RU/s i skalować z przyrostem 100 RU/s.
* Rozwiązane usterki w natywny zestaw, co może spowodować wyjątek NullRef niektórych kwerend.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Rozwiązane usterki w Konfiguracja aparatu zapytania, który może powodować wyjątki dla zapytań w trybie bramy.
* Stałe kilka błędów w kontenerze sesji, która może spowodować wyjątek "Nie można odnaleźć zasobu właściciela" żądań natychmiast po utworzeniu kolekcji.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i Śr.). Zobacz [Obsługa agregacji](sql-api-sql-query.md#Aggregates).
* Dodano obsługę zmiany źródła danych.
* Dodano obsługę za pośrednictwem RequestOptions.setPopulateQuotaInfo, informacje o limicie przydziału kolekcji.
* Dodano obsługę rejestrowania skryptu procedury składowanej za pośrednictwem RequestOptions.setScriptLoggingEnabled.
* Stałe usterki, gdzie mogą wykraczać zapytania w trybie DirectHttps, gdy wystąpią błędy ograniczania.
* Rozwiązane usterki w tryb spójność sesji.
* Stałe błędów, które mogą spowodować NullReferenceException element HttpContext po wysoki współczynnik żądań.
* Lepsza wydajność DirectHttps tryb.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Dodano proste klienta oparte na wystąpienie obsługi serwera proxy z interfejsem API ConnectionPolicy.setProxy().
* Dodano DocumentClient.close() interfejsu API do prawidłowego zamknięcia DocumentClient wystąpienia.
* Poprawia wydajność zapytań w trybie bezpośrednie połączenie między wyprowadzanie planu zapytania z natywny zestaw zamiast bramy.
* Ustaw FAIL_ON_UNKNOWN_PROPERTIES = false, dlatego użytkownicy nie muszą definiować JsonIgnoreProperties w ich typu POJO.
* Rejestrowanie refaktoryzowane, aby użyć SLF4J.
* Stała kilka innych błędów spójności czytnika.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Stała zarządzania połączenia, aby zapobiec przeciekom połączenia w trybie bezpośrednie połączenie między usterki.
* Stała usterki zapytania TOP, w którym może zgłosić wyjątek NullReferenece.
* Lepszą wydajność dzięki zmniejszeniu liczby wywołań sieci dla wewnętrznej pamięci podręcznej.
* Kod stanu dodany, ActivityID i identyfikator URI żądania w DocumentClientException w celu ułatwienia rozwiązywania problemów.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Rozwiązano problem z zarządzaniem połączeniami dla stabilności.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Dodano obsługę BoundedStaleness poziomu spójności.
* Dodano obsługę bezpośrednie połączenie między dla operacji CRUD dla kolekcji partycjonowanych.
* Stałe błędów badania bazy danych z programu SQL.
* Rozwiązane usterki w pamięci podręcznej sesji, gdzie może być niepoprawna tokenu sesji.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Dodano obsługę dla wielu partycji zapytania równoległe.
* Dodano obsługę TOP/ORDER BY zapytania dla kolekcji partycjonowanych.
* Dodano obsługę wysoki poziom spójności.
* Dodano obsługę żądań na podstawie nazwy przy użyciu bezpośrednie połączenie między.
* Stała ActivityId pozostaną niezmienione we wszystkich ponownych prób wykonania żądania.
* Stałe błędów związane z pamięci podręcznej sesji podczas odtwarzania kolekcji o tej samej nazwie.
* Dodano wielokąta i typy danych LineString podczas określania kolekcji indeksowania zasad dla zapytań przestrzennych grodzenia.
* Rozwiązane problemy z dokumentem Java dla języka Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Rozwiązane usterki w PartitionKeyDefinitionMap do buforowania kolekcje z jedną partycją i nie wprowadzać dodatkowe pobierania żądania klucza partycji.
* Stałe błędów nie próbę, gdy została podana wartość klucza partycji niepoprawne.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę konta w przypadku bazy danych.
* Dodano obsługę automatycznego ponowienie dotyczące żądań ograniczeniem przepustowości z opcjami, aby dostosować max ponownych prób i ponawiania maksymalny czas oczekiwania.  Zobacz RetryOptions i ConnectionPolicy.getRetryOptions().
* Przestarzałe IPartitionResolver na podstawie niestandardowych kodów partycjonowania. Użyj kolekcji partycjonowanych wyżej magazynu i przepustowości.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Obsługa zasad ponawiania dodano ograniczania.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Czas do live (TTL) — Pomoc techniczna dla dokumentów.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Zaimplementowane [kolekcje partycjonowane](partition-data.md) i [poziomy wydajności zdefiniowanych przez użytkownika](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Rozwiązane usterki w HashPartitionResolver do generowania wartości skrótu w little endian było spójne z innych zestawów SDK.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Dodaj skrót & zakres partycji rozwiązujący pomagające dzielenia na fragmenty aplikacji między wieloma partycjami.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementuje Upsert. Dodane w celu obsługi funkcji Upsert nowych metod upsertXXX.
* Na podstawie Identyfikatora wdrożenie routingu. Brak zmian publicznego interfejsu API, wszystkie zmiany wewnętrznego.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Pominięto wersji można wyświetlić numer wersji wyrównania z innych zestawów SDK

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Obsługuje dane geograficzne indeksu
* Weryfikuje właściwość identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie może zawierać?, /, #, \, znaków ani kończyć spacją.
* Dodaje nowego nagłówka "indeksu przekształcania toku" do ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Wersja i wycofania dat
Firma Microsoft udostępni powiadomienia co najmniej **12 miesięcy** klienta z wyprzedzeniem wycofanie SDK w celu złagodzenia przejścia do nowszej/nieobsługiwaną wersję.

Nowe funkcje i funkcjonalność i optymalizację, które są dodawane tylko do bieżącego zestawu SDK, w związku jest zalecane, zawsze uaktualnienie SDK najnowszą tak szybko jak to możliwe.

Każde żądanie do rozwiązania Cosmos bazy danych przy użyciu wycofane zestawu SDK będą odrzucane przez usługę.

> [!WARNING]
> Wszystkie wersje programu SQL zestawu SDK dla języka Java poprzedzające wersję **1.0.0** zostały wycofane na **29 lutego 2016**.
> 
> 

<br/>

| Wersja | Data wydania | Dacie wycofania |
| --- | --- | --- |
| [1.15.0](#1.15.0) |14 listopada 2017 r. |--- |
| [1.14.0](#1.14.0) |28 OCT 2017 r. |--- |
| [1.13.0](#1.13.0) |25 sierpnia 2017 r. |--- |
| [1.12.0](#1.12.0) |11 lipca 2017 r. |--- |
| [1.11.0](#1.11.0) |10 maja 2017 |--- |
| [1.10.0](#1.10.0) |11 marca 2017 r. |--- |
| [1.9.6](#1.9.6) |21 lutego 2017 r. |--- |
| [1.9.5](#1.9.5) |31 stycznia 2017 r. |--- |
| [1.9.4](#1.9.4) |24 listopada 2016 r. |--- |
| [1.9.3](#1.9.3) |30 października 2016 r. |--- |
| [1.9.2](#1.9.2) |28 października 2016 r. |--- |
| [1.9.1](#1.9.1) |26 października 2016 r. |--- |
| [1.9.0](#1.9.0) |03 października 2016 r. |--- |
| [1.8.1](#1.8.1) |30 czerwca 2016 r. |--- |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |--- |
| [1.7.1](#1.7.1) |30 kwietnia 2016 r. |--- |
| [1.7.0](#1.7.0) |27 kwietnia 2016 r. |--- |
| [1.6.0](#1.6.0) |29 marca 2016 r. |--- |
| [1.5.1](#1.5.1) |31 grudnia 2015 r. |--- |
| [1.5.0](#1.5.0) |04 grudnia 2015 r. |--- |
| [1.4.0](#1.4.0) |05 października 2015 |--- |
| [1.3.0](#1.3.0) |05 października 2015 |--- |
| [1.2.0](#1.2.0) |05 sierpnia 2015 |--- |
| [1.1.0](#1.1.0) |09 lipca 2015 r. |--- |
| [1.0.1](#1.0.1) |12 maja 2015 r. |--- |
| [1.0.0](#1.0.0) |07 kwietnia 2015 r. |--- |
| 0.9.5-prelease |09 marca 2015 roku. |29 lutego 2016 r. |
| 0.9.4-prelease |17 lutego 2015 |29 lutego 2016 r. |
| 0.9.3-prelease |13 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.2-prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.1-prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.0-prelease |10 grudnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat rozwiązania Cosmos bazy danych, zobacz [bazy danych programu Microsoft Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) stronę usługi.

