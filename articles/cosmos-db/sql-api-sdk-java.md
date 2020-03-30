---
title: 'Usługa Azure Cosmos DB: interfejs API środowiska Java SQL, zestaw SDK & zasoby'
description: Dowiedz się wszystkiego o interfejsie API i SDK języka JAVA SQL, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją narzędzia Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: 514982727509788918c159e07f8061962df32336
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77558932"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw JavaDK usługi Azure Cosmos DB dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Plik danych o zmianach w sieci .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java (asynchroniczny)](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Reszta](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Wykonawca luzem - Java](sql-api-sdk-bulk-executor-java.md)

Zestaw SDK java interfejsu API SQL obsługuje operacje synchroniczne. Aby uzyskać obsługę asynchronisty, użyj [interfejsu API SQL Async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Pobieranie SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API w języku Java](/java/api/com.microsoft.azure.documentdb)|
|**Współtworzenie sdk**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Wprowadzenie**|[Wprowadzenie do sdk Java SDK](sql-api-java-get-started.md)|
|**Samouczek aplikacji sieci Web**|[Tworzenie aplikacji sieci Web za pomocą usługi Azure Cosmos DB](sql-api-java-application.md)|
|**Minimalny obsługiwany czas wykonywania**|[Zestaw java development (JDK) 7+](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="247"></a><a name="2.4.7"/>2.4.7
* Rozwiązuje problem limitu czasu puli połączeń.
* Rozwiązuje odświeżanie tokenu auth podczas wewnętrznych ponownych prób.

### <a name="246"></a><a name="2.4.6"/>2.4.6
* Zaktualizowano poprawny tag zasad repliki po stronie klienta w bazie danychKontakon i dokonał odczytów konfiguracji konta bazy danych z pamięci podręcznej.

### <a name="245"></a><a name="2.4.5"/>2.4.5
* Unikanie ponownych prób w przypadku błędu nieprawidłowego zakresu kluczy partycji, jeśli użytkownik udostępnia pkRangeId.

### <a name="244"></a><a name="2.4.4"/>2.4.4
* Odświeża się zoptymalizowana pamięć podręczna zakresu kluczy partycji.
* Rozwiązuje scenariusz, w którym zestaw SDK nie oferuje wskazówki podziału partycji z serwera i powoduje odświeżenie niepoprawnych pamięci podręcznych routingu po stronie klienta.

### <a name="242"></a><a name="2.4.2"/>2.4.2
* Odświeżanie zoptymalizowanej pamięci podręcznej kolekcji.

### <a name="241"></a><a name="2.4.1"/>2.4.1
* Dodano obsługę pobierania wewnętrznego komunikatu wyjątku z ciągu diagnostycznego żądania.

### <a name="240"></a><a name="2.4.0"/>2.4.0
* Wprowadzono wersję interfejsu API na PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Dodano oddzielną obsługę limitu czasu dla trybu bezpośredniego.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Korzystanie z błędu null komunikat z usługi i tworzenia wyjątku klienta dokumentu.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Poprawa połączenia gniazda, dodawanie SoKeepAlive domyślnie true.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę ciągów diagnostyki żądań.

### <a name="213"></a><a name="2.1.3"/>2.1.3
* Naprawiono błąd w PartitionKey dla Hash V2.

### <a name="212"></a><a name="2.1.2"/>2.1.2
* Dodano obsługę indeksów złożonych.
* Naprawiono błąd w globalnym menedżerze punktów końcowych, aby wymusić odświeżenie.
* Naprawiono błąd dla upsertów z warunkami wstępnymi w trybie bezpośrednim.

### <a name="211"></a><a name="2.1.1"/>2.1.1
* Naprawiono błąd w pamięci podręcznej adresów bramy.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Dodano obsługę zapisu w wielu regionach dla trybu bezpośredniego.
* Dodano obsługę IOExceptions zgłaszane jako ServiceNiedostępne wyjątki, z serwera proxy.
* Naprawiono błąd w zasadach ponawiania ponawiania punktów końcowych.
* Naprawiono błąd, aby upewnić się, że wyjątki wskaźnika null nie są generowane w BaseDatabaseAccountConfigurationProvider.
* Naprawiono błąd, aby upewnić się, że QueryIterator nie zwraca wartości null.
* Naprawiono błąd, aby upewnić się, że duży PartitionKey jest dozwolony

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Dodano obsługę zapisu w wielu regionach dla trybu bramy.

### <a name="1164"></a><a name="1.16.4"/>1.16.4
* Naprawiono błąd w odczytu partycji Zakresy kluczy dla kwerendy.

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* Naprawiono błąd podczas ustawiania rozmiaru nagłówka tokenu kontynuacji w trybie DirectHttps.

### <a name="1162"></a><a name="1.16.2"/>1.16.2
* Dodano obsługę przesyłania strumieniowego w trybie fail over.
* Dodano obsługę niestandardowych metadanych.
* Ulepszona logika obsługi sesji.
* Naprawiono błąd w pamięci podręcznej zakresu kluczy partycji.
* Naprawiono błąd NPE w trybie bezpośrednim.

### <a name="1161"></a><a name="1.16.1"/>1.16.1
* Dodano obsługę unique index.
* Dodano obsługę ograniczania rozmiaru tokenu kontynuacji w opcjach kanału informacyjnego.
* Naprawiono błąd w Json Serialization (sygnatura czasowa).
* Naprawiono błąd w Json Serialization (wyliczenia).
* Zależność od com.fasterxml.jackson.core:jackson-databind uaktualniona do 2.9.5.

### <a name="1160"></a><a name="1.16.0"/>1.16.0
* Ulepszone buforowanie połączeń w trybie bezpośrednim.
* Ulepszone ulepszenia przedfetchem dla kwerendy partycji krzyżowej nienazwiązane przez kolejność.
* Ulepszona generacja UUID.
* Ulepszona logika spójności sesji.
* Dodano obsługę multipolygonu.
* Dodano obsługę statystyk zakresu kluczy partycji dla kolekcji.
* Naprawiono błąd w obsłudze wielu regionów.

### <a name="1150"></a><a name="1.15.0"/>1.15.0
* Poprawiono wydajność serializacji Json.
* Ta wersja SDK wymaga najnowszej wersji emulatora usługi https://aka.ms/cosmosdb-emulatorAzure Cosmos DB dostępnej do pobrania z .

### <a name="1140"></a><a name="1.14.0"/>1.14.0
* Zmiany wewnętrzne w bibliotekach znajomych firmy Microsoft.

### <a name="1130"></a><a name="1.13.0"/>1.13.0
* Naprawiono błąd podczas odczytywania zakresów kluczy pojedynczej partycji.
* Naprawiono błąd w analizowaniu identyfikatora zasobów, który wpływał na bazę danych z krótkimi nazwami.
* Naprawiono przyczyną problemu kodowanie klucza partycji.

### <a name="1120"></a><a name="1.12.0"/>1.12.0
* Krytyczne poprawki błędów, aby zażądać przetwarzania podczas podziału partycji.
* Naprawiono błąd związany z poziomem spójności Silne i OgraniczoneStaleness.

### <a name="1110"></a><a name="1.11.0"/>1.11.0
* Dodano obsługę nowego poziomu spójności o nazwie ConsistentPrefix.
* Naprawiono błąd podczas odczytywania kolekcji w trybie sesji.

### <a name="1100"></a><a name="1.10.0"/>1.10.0
* Włączono obsługę kolekcji podzielonej na partycje z zaledwie 2500 RU/s i skalować w przyrostach 100 RU/s.
* Naprawiono błąd w natywnym zestawie, który może powodować wyjątek NullRef w niektórych kwerendach.

### <a name="196"></a><a name="1.9.6"/>1.9.6
* Naprawiono błąd w konfiguracji aparatu kwerend, który mógł powodować wyjątki dla zapytań w trybie bramy.
* Naprawiono kilka błędów w kontenerze sesji, które mogą powodować wyjątek "Nie znaleziono zasobu właściciela" dla żądań natychmiast po utworzeniu kolekcji.

### <a name="195"></a><a name="1.9.5"/>1.9.5
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG). Zobacz [Obsługa agregacji](sql-query-aggregates.md).
* Dodano obsługę kanału zmian.
* Dodano obsługę informacji o przydziałach kolekcji za pośrednictwem pliku RequestOptions.setPopulateQuotaInfo.
* Dodano obsługę rejestrowania skryptów procedur składowanych za pośrednictwem pliku RequestOptions.setScriptLoggingEnabled.
* Naprawiono błąd, który powodował, że kwerenda w trybie DirectHttps mogła przestać odpowiadać w przypadku wystąpienia błędów przepustnicy.
* Naprawiono błąd w trybie spójności sesji.
* Naprawiono błąd, który może powodować NullReferenceException w HttpContext, gdy szybkość żądania jest wysoka.
* Poprawiono wydajność trybu DirectHttps.

### <a name="194"></a><a name="1.9.4"/>1.9.4
* Dodano prostą obsługę serwera proxy opartą na wystąpieniu klienta za pomocą interfejsu API ConnectionPolicy.setProxy().
* Dodano interfejs API DocumentClient.close(), aby poprawnie zamknąć wystąpienie DocumentClient.
* Poprawiono wydajność kwerend w trybie łączności bezpośredniej, wyprowadzając plan kwerend z zestawu macierzystego zamiast bramy.
* Ustaw FAIL_ON_UNKNOWN_PROPERTIES = false, dzięki czemu użytkownicy nie muszą definiować właściwości JsonIgnoreProperties w ich POJO.
* Refaktoryzowane rejestrowanie do korzystania z SLF4J.
* Naprawiono kilka innych błędów w czytniku spójności.

### <a name="193"></a><a name="1.9.3"/>1.9.3
* Naprawiono błąd w zarządzaniu połączeniami, aby zapobiec wyciekom połączeń w trybie łączności bezpośredniej.
* Naprawiono błąd w kwerendzie TOP, gdzie może zgłaszać wyjątek NullReference.
* Zwiększona wydajność dzięki zmniejszeniu liczby wywołań sieciowych dla wewnętrznych pamięci podręcznych.
* Dodano kod stanu, ActivityID i identyfikator żądania URI w DocumentClientException dla lepszego rozwiązywania problemów.

### <a name="192"></a><a name="1.9.2"/>1.9.2
* Naprawiono błąd w zarządzaniu połączeniami w celu uzyskania stabilności.

### <a name="191"></a><a name="1.9.1"/>1.9.1
* Dodano obsługę poziomu spójności BoundedStaleness.
* Dodano obsługę łączności bezpośredniej dla operacji CRUD dla kolekcji podzielonych na partycje.
* Naprawiono błąd podczas wykonywania zapytań o bazę danych za pomocą języka SQL.
* Naprawiono błąd w pamięci podręcznej sesji, w którym token sesji mógł być ustawiony niepoprawnie.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Dodano obsługę zapytań równoległych między partycjami.
* Dodano obsługę zapytań TOP/ORDER BY dla kolekcji podzielonych na partycje.
* Dodano obsługę silnej spójności.
* Dodano obsługę żądań opartych na nazwach podczas korzystania z łączności bezpośredniej.
* Poprawiono, aby ActivityId zachować spójność we wszystkich ponownych prób żądania.
* Naprawiono błąd związany z pamięcią podręczną sesji podczas odtworzenia kolekcji o tej samej nazwie.
* Dodano wielokąty i linestring datatypes określając zasady indeksowania kolekcji dla geo-ogrodzenia kwerend przestrzennych.
* Naprawiono problemy z Java Doc dla Java 1.8.

### <a name="181"></a><a name="1.8.1"/>1.8.1
* Naprawiono błąd w PartitionKeyDefinitionMap do pamięci podręcznej kolekcji pojedynczych partycji i nie do żądania dodatkowych kluczy partycji pobierania.
* Naprawiono błąd, aby nie ponawiać próby po podaniu niepoprawnej wartości klucza partycji.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę kont bazy danych w wielu regionach.
* Dodano obsługę automatycznego ponawiania prób w przypadku ograniczonych żądań z opcjami dostosowywania maksymalnej próby ponawiania i maksymalnego czasu oczekiwania ponawiania prób.  Zobacz RetryOptions i ConnectionPolicy.getRetryOptions().
* Przestarzały iPartitionResolver na podstawie niestandardowego kodu partycjonowania. Użyj kolekcji podzielonych na partycje dla większej wydajności magazynu i przepływności.

### <a name="171"></a><a name="1.7.1"/>1.7.1
* Dodano obsługę zasad ponawiania prób dla ograniczania stawek.  

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Dodano czas do życia (TTL) wsparcie dla dokumentów.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Zaimplementowano [kolekcje podzielone na partycje](partition-data.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md).

### <a name="151"></a><a name="1.5.1"/>1.5.1
* Naprawiono błąd w HashPartitionResolver do generowania wartości skrótu w little-endian być zgodne z innymi SDK.

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Dodaj program rozpoznawania partycji Hash & Range, aby pomóc w aplikacji dzielenia na fragmenty na wielu partycjach.

### <a name="140"></a><a name="1.4.0"/>1.4.0
* Zaimplementuj upsert. Nowe metody upsertXXX dodane do obsługi funkcji Upsert.
* Implementowanie routingu opartego na identyfikatorze. Brak zmian w publicznym interfejsie API, wszystkie zmiany wewnętrzne.

### <a name="130"></a><a name="1.3.0"/>1.3.0
* Wersja pominięta, aby doprowadzić numer wersji w zgodzie z innymi SDK

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Obsługuje indeks geoprzestrzańskowy
* Sprawdza poprawność właściwości ID dla wszystkich zasobów. Identyfikatory zasobów nie mogą zawierać ?, \, /, #, znaków ani kończyć spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania v2

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Daty zwolnienia i wycofania
Firma Microsoft powiadomi o wycofaniu sdk sdk z co najmniej **12-miesięcznym** wyprzedzeniem w celu usprawnienia przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego sdk, jako takie zaleca się, aby zawsze uaktualnić do najnowszej wersji SDK tak wcześnie, jak to możliwe.

Każde żądanie do usługi Cosmos DB przy użyciu wycofanego SDK zostanie odrzucone przez usługę.

> [!WARNING]
> Wszystkie wersje **1.x** SDK SQL dla Javy zostaną wycofane **30 maja 2020**r.
> 
>

> [!WARNING]
> Wszystkie wersje SDK SQL dla javy przed wersją **1.0.0** zostały wycofane **29 lutego 2016**.
> 
> 

<br/>

| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [2.4.7](#2.4.7) |20 lutego 2020 r. |--- |
| [2.4.6](#2.4.6) |24 stycznia 2020 r. |--- |
| [2.4.5](#2.4.5) |10 listopada 2019 r. |--- |
| [2.4.4](#2.4.4) |24 października 2019 r. |--- |
| [2.4.2](#2.4.2) |26 września 2019 r. |--- |
| [2.4.1](#2.4.1) |Lip 18, 2019 |--- |
| [2.4.0](#2.4.0) |4 maja 2019 r. |--- |
| [2.3.0](#2.3.0) |24 kwietnia 2019 r. |--- |
| [2.2.3](#2.2.3) |16 kwietnia 2019 r. |--- |
| [2.2.2](#2.2.2) |Kwi 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |13 marca 2019 r. |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [2.1.1](#2.1.1) |13 grudnia 2018 r. |--- |
| [2.1.0](#2.1.0) |20 listopada 2018 r. |--- |
| [2.0.0](#2.0.0) |21 września 2018 r. |--- |
| [1.16.4](#1.16.4) |10 września 2018 r. |30 maja 2020 r. |
| [1.16.3](#1.16.3) |9 września 2018 r. |30 maja 2020 r. |
| [1.16.2](#1.16.2) |29 czerwca 2018 r. |30 maja 2020 r. |
| [1.16.1](#1.16.1) |16 maja 2018 r. |30 maja 2020 r. |
| [1.16.0](#1.16.0) |15 marca 2018 r. |30 maja 2020 r. |
| [1.15.0](#1.15.0) |14 listopada 2017 r. |30 maja 2020 r. |
| [1.14.0](#1.14.0) |28 października 2017 r. |30 maja 2020 r. |
| [1.13.0](#1.13.0) |25 sierpnia 2017 r. |30 maja 2020 r. |
| [1.12.0](#1.12.0) |11 lipca 2017 r. |30 maja 2020 r. |
| [1.11.0](#1.11.0) |10 maja 2017 |30 maja 2020 r. |
| [1.10.0](#1.10.0) |11 marca 2017 r. |30 maja 2020 r. |
| [1.9.6](#1.9.6) |21 lutego 2017 r. |30 maja 2020 r. |
| [1.9.5](#1.9.5) |31 stycznia 2017 r. |30 maja 2020 r. |
| [1.9.4](#1.9.4) |24 listopada 2016 r. |30 maja 2020 r. |
| [1.9.3](#1.9.3) |30 października 2016 r. |30 maja 2020 r. |
| [1.9.2](#1.9.2) |28 października 2016 r. |30 maja 2020 r. |
| [1.9.1](#1.9.1) |26 października 2016 r. |30 maja 2020 r. |
| [1.9.0](#1.9.0) |3 października 2016 r. |30 maja 2020 r. |
| [1.8.1](#1.8.1) |30 czerwca 2016 r. |30 maja 2020 r. |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |30 maja 2020 r. |
| [1.7.1](#1.7.1) |30 kwietnia 2016 r. |30 maja 2020 r. |
| [1.7.0](#1.7.0) |27 kwietnia 2016 r. |30 maja 2020 r. |
| [1.6.0](#1.6.0) |29 marca 2016 r. |30 maja 2020 r. |
| [1.5.1](#1.5.1) |31 grudnia 2015 r. |30 maja 2020 r. |
| [1.5.0](#1.5.0) |4 grudnia 2015 r. |30 maja 2020 r. |
| [1.4.0](#1.4.0) |5 października 2015 r. |30 maja 2020 r. |
| [1.3.0](#1.3.0) |5 października 2015 r. |30 maja 2020 r. |
| [1.2.0](#1.2.0) |5 sierpnia 2015 r. |30 maja 2020 r. |
| [1.1.0](#1.1.0) |9 lipca 2015 r. |30 maja 2020 r. |
| 1.0.1 |12 maja 2015 r. |30 maja 2020 r. |
| [1.0.0](#1.0.0) |7 kwietnia 2015 r. |30 maja 2020 r. |
| 0,9,5-prelease |9 marca 2015 r. |29 lutego 2016 r. |
| 0.9.4-prelease |17 lutego 2015 r. |29 lutego 2016 r. |
| 0.9.3-prelease |13 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.2-prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.1-prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.0-prelease |10 grudnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

