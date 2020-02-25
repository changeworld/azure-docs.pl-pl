---
title: 'Azure Cosmos DB: Interfejs API SQL języka Java, zestaw SDK i zasoby'
description: Poznaj interfejs API SQL języka Java i zestawu SDK, w tym daty wydania, daty wycofania i zmiany między poszczególnymi wersjami zestawu SDK usługi Azure Cosmos DB SQL języka Java.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: 514982727509788918c159e07f8061962df32336
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77558932"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Usługa Azure Cosmos DB Java SDK dla interfejsu API SQL: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
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

Zestaw SDK języka Java dla interfejsu API SQL obsługuje operacje synchroniczne. Aby zapewnić obsługę asynchroniczną, należy użyć [interfejsu API usługi Async Java dla języka SQL](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Pobieranie zestawu SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentacja interfejsu API**|[Dokumentacja interfejsu API języka Java](/java/api/com.microsoft.azure.documentdb)|
|**Współtworzenie zestawu SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Rozpoczęcie pracy**|[Wprowadzenie do zestawu Java SDK](sql-api-java-get-started.md)|
|**Samouczek aplikacji sieci Web**|[Tworzenie aplikacji sieci Web za pomocą Azure Cosmos DB](sql-api-java-application.md)|
|**Minimalne obsługiwane środowisko uruchomieniowe**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Informacje o wersji

### <a name="a-name247247"></a><a name="2.4.7"/>2.4.7
* Rozwiązuje problem z limitem czasu puli połączeń.
* Naprawia odświeżanie tokenu uwierzytelniania podczas wewnętrznych ponownych prób.

### <a name="a-name246246"></a><a name="2.4.6"/>2.4.6
* Zaktualizowano prawidłowy tag zasad repliki po stronie klienta na databaseAccount i databaseAccounte odczyty konfiguracji z pamięci podręcznej.

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Unikaj ponawiania próby przy nieprawidłowym błędzie zakresu kluczy partycji, jeśli użytkownik poda pkRangeId.

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Odświeżone pamięci podręcznej zakresów kluczy partycji.
* Poprawka w scenariuszu, w którym zestaw SDK nie obsługuje wskazówki podziału partycji z serwera i powoduje nieprawidłowe Odświeżanie pamięci podręcznych routingu po stronie klienta.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Odświeżone Odświeżanie pamięci podręcznej kolekcji.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Dodano obsługę pobierania komunikatu o wyjątku wewnętrznym z ciągu diagnostyki żądania.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Wprowadzono wersję interfejsu API w systemie PartitionKeyDefinition.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Dodano obsługę oddzielnego limitu czasu dla trybu bezpośredniego.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Zużywanie pustego komunikatu o błędzie z usługi i Tworzenie wyjątku klienta dokumentu.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Udoskonalenie połączenia gniazda, Dodawanie SoKeepAlive domyślnego true.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę ciągu diagnostyki żądania.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Naprawiono usterkę w PartitionKey dla skrótu v2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Dodano obsługę indeksów złożonych.
* Naprawiono usterkę w programie Global Endpoint Manager, aby wymusić odświeżanie.
* Stała usterka dla upserts z warunkiem wstępnym w trybie bezpośrednim.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Naprawiono usterkę w pamięci podręcznej adresów bramy.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Obsługa wielu regionów zapisu dodawane w trybie bezpośredniego.
* Dodano obsługę obsługi IOExceptions zgłoszony jako wyjątki ServiceUnavailable z serwera proxy.
* Usunięto usterkę w zasadach ponawiania odnajdywania punktów końcowych.
* Naprawiono usterkę w celu zapewnienia wskaźnika o wartości null, które nie są zgłaszane wyjątki w BaseDatabaseAccountConfigurationProvider.
* Rozwiązano błąd, aby upewnić się, że QueryIterator nie zwraca wartości null.
* Usunięto usterkę w celu zapewnienia, że duże PartitionKey jest dozwolone

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Dodano dla trybu bramy obsługę wielu region zapisu.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Usunięto usterkę w partycji odczytu klucza zakresów dla zapytania.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Usunięto usterkę podczas ustawiania rozmiaru nagłówka token kontynuacji w trybie DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Dodano niepowodzenia przesyłania strumieniowego za pośrednictwem pomocy technicznej.
* Dodano obsługę niestandardowych metadanych.
* Sesja ulepszona logika obsługi.
* Usunięto usterkę w pamięci podręcznej zakres kluczy partycji.
* Usunięto usterkę NPE w trybie bezpośredniego.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Dodano obsługę unikatowego indeksu.
* Dodano obsługę ograniczenie rozmiaru token kontynuacji w opcjach źródła danych.
* Usunięto usterkę w serializacji Json (sygnatura czasowa).
* Usunięto usterkę w serializacji Json (enum).
* Zależność od uaktualnione do 2.9.5 elementu databind na com.fasterxml.jackson.core:jackson.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Buforowanie w trybie bezpośredniego połączenia ulepszona.
* Ulepszona wzrost pobierania z wyprzedzeniem dla innych orderby wielu partycji zapytania.
* Ulepszone Generowanie UUID.
* Ulepszona logika spójność sesji.
* Dodano obsługę multipolygon.
* Dodano obsługę statystyk zakresu klucza partycji dla kolekcji.
* Usunięto usterkę w obsłudze wielu regionów.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Zwiększono wydajność serializacji Json.
* Ta wersja zestawu SDK wymaga najnowszej wersji Azure Cosmos DB emulatora dostępnej do pobrania z https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Wewnętrzny zmiany dla bibliotek znajomych firmy Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Rozwiązano problem podczas odczytu jedną partycję kluczy zakresów.
* Rozwiązano problem z ResourceID analizy, która ma wpływ na bazę danych z krótkimi nazwami.
* Ustalić przyczynę problemu, kodując partycję kluczy.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Poprawki błędów krytycznych żądania przetwarzania podczas dzieli dane partycji.
* Rozwiązano problem z poziomów spójności silne i BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Dodano obsługę nowego poziomu spójności, nazywana ConsistentPrefix.
* Usunięto usterkę w kolekcji sesji w trybie do czytania.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Włączona obsługa kolekcji partycjonowanej z jako mało jako klient może dysponować 2500 jednostek żądań na sekundę i skalować w przyrostach 100 RU/s.
* Usunięto usterkę w macierzystym zestawie, co może spowodować wyjątek NullRef w niektórych zapytaniach.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Usunięto usterkę w Konfiguracja aparatu zapytań, które mogą powodować wyjątki dotyczące zapytań w trybie bramy.
* Naprawiono kilka błędów w kontenerze sesji, który może spowodować wyjątek "Nie można odnaleźć zasobu właściciela", w przypadku żądań od razu po utworzeniu kolekcji.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, Suma i średnia). Zobacz [Obsługa agregacji](sql-query-aggregates.md).
* Dodano obsługę zestawienia zmian.
* Dodano obsługę za pomocą RequestOptions.setPopulateQuotaInfo, informacje o limitach przydziału kolekcji.
* Dodano obsługę rejestrowanie skryptu procedury składowanej za pośrednictwem RequestOptions.setScriptLoggingEnabled.
* Naprawiono usterkę polegającą na tym, że zapytanie w trybie DirectHttps może przestać odpowiadać, gdy wystąpią błędy ograniczania.
* Usunięto usterkę w trybie spójność sesji.
* Naprawiono usterkę, która może spowodować obiektu NullReferenceException w HttpContext, gdy liczba żądań jest wysoka.
* Zwiększono wydajność DirectHttps trybu.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Obsługa oparta na wystąpieniach proxy dodano prostego klienta za pomocą interfejsu API ConnectionPolicy.setProxy().
* Dodano DocumentClient.close() interfejsu API prawidłowe zamknięcie wystąpienia DocumentClient.
* Poprawia wydajność zapytań w trybie połączenie bezpośrednie wyprowadzanie planu zapytania z natywnego zestawu, zamiast bramy.
* Ustaw FAIL_ON_UNKNOWN_PROPERTIES = false, dzięki czemu użytkownicy nie muszą definiować JsonIgnoreProperties w ich obiektu typu POJO.
* Zaprojektowane od nowa, rejestrowanie użycia SLF4J.
* Naprawiono kilka innych błędów w czytniku spójności.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Usunięto usterkę z zarządzaniem połączeniami, aby zapobiec przeciekom połączenia w trybie bezpośredniego połączenia.
* Rozwiązano usterkę w zapytaniu TOP, w której może zgłosić wyjątek NullReference.
* Zwiększono wydajność dzięki zmniejszeniu liczby wywołań sieci dla wewnętrznych pamięci podręcznych.
* Dodano został zwrócony kod stanu ActivityID i żądań z identyfikatora URI w DocumentClientException w celu ułatwienia rozwiązywania problemów.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Rozwiązano problem z zarządzaniem połączeniami dotyczące stabilności.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Dodano obsługę BoundedStaleness poziomu spójności.
* Dodano obsługę połączenie bezpośrednie dla operacji CRUD dla kolekcji podzielonych na partycje.
* Usunięto usterkę w zapytań bazy danych przy użyciu języka SQL.
* Usunięto usterkę w pamięci podręcznej sesji, gdzie tokenu sesji może być nieprawidłowo ustawiona.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Dodano obsługę dla wielu partycji zapytania równolegle.
* Dodano obsługę TOP/ORDER BY zapytania dla kolekcji podzielonych na partycje.
* Dodano obsługę silnej spójności.
* Dodano obsługę nazwy na podstawie żądania, korzystając z połączenia bezpośredniego.
* Stała się ActivityId pozostają spójne we wszystkich ponownych prób wykonania żądania.
* Naprawiono usterki związane z pamięci podręcznej sesji podczas odtwarzania kolekcji o takiej samej nazwie.
* Dodano wielokąta i typy danych LineString, podczas określania zasad dla zapytań przestrzennych grodzenia indeksowania kolekcji.
* Rozwiązane problemy z dokumentacji języka Java dla języka Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Usunięto usterkę w PartitionKeyDefinitionMap buforowanie kolekcji z jedną partycją i nie wprowadzać dodatkowe pobierania żądania klucza partycji.
* Naprawiono usterkę, aby nie ponowić próbę, gdy została podana wartość klucza partycji niepoprawne.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Dodano obsługę multiregionalne konta baz danych.
* Dodano obsługę automatycznego ponowienie dotyczące żądania ograniczone opcje, aby dostosować max ponownych prób i ponawiania maksymalny czas oczekiwania.  Zobacz RetryOptions i ConnectionPolicy.getRetryOptions().
* Przestarzałe IPartitionResolver na podstawie niestandardowego kodu partycjonowania. Użyj kolekcji podzielonych na partycje dla nowszej magazynu i przepływności.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Obsługa zasad ponawiania dodano dla limitów szybkości.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Dodano czas live (TTL) — Pomoc techniczna dla dokumentów.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Wdrożone [kolekcje partycjonowane](partition-data.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Usunięto usterkę w HashPartitionResolver do generowania wartości skrótu w little endian aby były zgodne z innych zestawów SDK.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Dodaj skrót zak & res partycji rozwiązujący uzyskanymi fragmentowania aplikacje na wielu partycjach.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementowanie Upsert. Nowe metody upsertXXX dodanych do obsługi funkcji Upsert.
* Routing oparty na identyfikator implementacji. Brak publicznego zmian interfejsu API, wszystkie zmiany wewnętrznego.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Pominięto wersji, aby wyświetlić numer wersji w sposób zgodny z innymi zestawami SDK

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Obsługa danych Geoprzestrzennych indeksu
* Weryfikuje Właściwość identyfikatora dla wszystkich zasobów. Identyfikatory dla zasobów nie mogą zawierać znaków?,/, #, \, ani kończyć się spacją.
* Dodaje nowy nagłówek "indeks przekształcania progress" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zasady indeksowania w wersji 2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK W WERSJI OGÓLNIE DOSTĘPNEJ

## <a name="release-and-retirement-dates"></a>Daty wydania i wycofania
Firma Microsoft zapewni powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcjonalność i optymalizacje są dodawane tylko do bieżącego zestawu SDK, w związku z tym jest zalecane, zawsze uaktualnienie do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

Wszelkie żądania do usługi Cosmos DB przy użyciu wycofane zestawu SDK zostanie odrzucone przez usługę.

> [!WARNING]
> Wszystkie wersje **1. x** zestawu SQL SDK dla języka Java zostaną wycofane z **30 maja 2020**.
> 
>

> [!WARNING]
> Wszystkie wersje zestawu SQL SDK dla języka Java sprzed wersji **1.0.0** zostały wycofane **29 lutego 2016**.
> 
> 

<br/>

| Wersja | Data wydania | Data wygaśnięcia |
| --- | --- | --- |
| [2.4.7](#2.4.7) |20 lutego 2020 |--- |
| [2.4.6](#2.4.6) |24 stycznia 2020 |--- |
| [2.4.5](#2.4.5) |Lis 10, 2019 |--- |
| [2.4.4](#2.4.4) |24 października 2019 |--- |
| [2.4.2](#2.4.2) |26 SEP, 2019 |--- |
| [2.4.1](#2.4.1) |18 lipca 2019 |--- |
| [2.4.0](#2.4.0) |04, 2019 |--- |
| [2.3.0](#2.3.0) |24 kwietnia 2019 |--- |
| [Regulamin](#2.2.3) |16 kwietnia 2019 |--- |
| [ppkt](#2.2.2) |Kwi 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [pkt](#2.1.1) |13 grudnia 2018 |--- |
| [2.1.0](#2.1.0) |20 listopada 2018 |--- |
| [2.0.0](#2.0.0) |21 września 2018 r. |--- |
| [1.16.4](#1.16.4) |10 września 2018 r. |30 maja 2020 |
| [1.16.3](#1.16.3) |09 września 2018 r. |30 maja 2020 |
| [1.16.2](#1.16.2) |29 czerwca 2018 r. |30 maja 2020 |
| [1.16.1](#1.16.1) |16 maja 2018 r. |30 maja 2020 |
| [1.16.0](#1.16.0) |15 marca 2018 r. |30 maja 2020 |
| [1.15.0](#1.15.0) |14 listopada 2017 r. |30 maja 2020 |
| [1.14.0](#1.14.0) |28 października 2017 r. |30 maja 2020 |
| [1.13.0](#1.13.0) |25 sierpnia 2017 r. |30 maja 2020 |
| [1.12.0](#1.12.0) |11 lipca 2017 r. |30 maja 2020 |
| [1.11.0](#1.11.0) |10 maja 2017 |30 maja 2020 |
| [1.10.0](#1.10.0) |11 marca 2017 r. |30 maja 2020 |
| [1.9.6](#1.9.6) |21 lutego 2017 r. |30 maja 2020 |
| [1.9.5](#1.9.5) |31 stycznia 2017 r. |30 maja 2020 |
| [1.9.4](#1.9.4) |24 listopada 2016 r. |30 maja 2020 |
| [1.9.3](#1.9.3) |30 października 2016 r. |30 maja 2020 |
| [1.9.2](#1.9.2) |28 października 2016 r. |30 maja 2020 |
| [1.9.1](#1.9.1) |26 października 2016 r. |30 maja 2020 |
| [1.9.0](#1.9.0) |03 października 2016 r. |30 maja 2020 |
| [1.8.1](#1.8.1) |30 czerwca 2016 r. |30 maja 2020 |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |30 maja 2020 |
| [1.7.1](#1.7.1) |30 kwietnia 2016 r. |30 maja 2020 |
| [1.7.0](#1.7.0) |27 kwietnia 2016 r. |30 maja 2020 |
| [1.6.0](#1.6.0) |29 marca 2016 r. |30 maja 2020 |
| [1.5.1](#1.5.1) |Do 31 grudnia 2015 r. |30 maja 2020 |
| [1.5.0](#1.5.0) |04 grudnia 2015 r. |30 maja 2020 |
| [1.4.0](#1.4.0) |05 października 2015 r. |30 maja 2020 |
| [1.3.0](#1.3.0) |05 października 2015 r. |30 maja 2020 |
| [1.2.0](#1.2.0) |05 sierpnia 2015 r. |30 maja 2020 |
| [1.1.0](#1.1.0) |09 lipca 2015 r. |30 maja 2020 |
| 1.0.1 |12 maja 2015 r. |30 maja 2020 |
| [1.0.0](#1.0.0) |07 kwietnia 2015 r. |30 maja 2020 |
| 0.9.5-prelease |09 marca 2015 r. |29 lutego 2016 r. |
| 0.9.4-prelease |17 lutego 2015 r. |29 lutego 2016 r. |
| 0.9.3-prelease |13 stycznia 2015 r. |29 lutego 2016 r. |
| 0.9.2-prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.1-prelease |19 grudnia 2014 r. |29 lutego 2016 r. |
| 0.9.0-prelease |10 grudnia 2014 r. |29 lutego 2016 r. |

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

