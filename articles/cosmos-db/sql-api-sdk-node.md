---
title: 'Usługa Azure Cosmos DB: interfejs API węzła SQL, zestaw SDK & zasoby'
description: Dowiedz się wszystkiego o interfejsie API i SDK programu SQL Node.js, w tym datach wydania, datach wycofania i zmianach wprowadzonych między każdą wersją sdk azure cosmos DB Node.js.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: de1c1b93b813f71b321da0625bc60e0762a859c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70207960"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Zestaw SDK węzła.js usługi Azure Cosmos DB dla interfejsu API SQL: informacje o wersji i zasoby
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

|Zasób  |Link  |
|---------|---------|
|Pobierz SDK  |   [Npm](https://www.npmjs.com/package/@azure/cosmos) 
|Dokumentacja api  |  [Dokumentacja referencyjna sdk javascript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instrukcje instalacji SDK  |  [Instrukcje instalacji](https://github.com/Azure/azure-cosmos-js#installation)
|Współtworzenie sdk | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Samples | [Przykłady kodu node.js](sql-api-nodejs-samples.md)
| Samouczek Wprowadzenie | [Wprowadzenie do sdk JavaScript](sql-api-nodejs-get-started.md)
| Samouczek aplikacji sieci Web | [Tworzenie aplikacji sieci Web Node.js przy użyciu usługi Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktualnie obsługiwana platforma | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) — SDK w wersji 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) — SDK w wersji 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) — SDK w wersji 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) — SDK w wersji 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK wersja 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK wersja 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK wersja 1.x.x

## <a name="release-notes"></a>Informacje o wersji

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Ustaw domyślną odpowiedźKontynuacjaTokenLimitInKB na 1kb. Domyślnie ograniczamy to do 1kb, aby uniknąć długich nagłówków (Node.js ma globalny limit rozmiaru nagłówka). Użytkownik może ustawić to pole, aby umożliwić dłuższe nagłówki, co może pomóc wewnętrznej bazy danych optymalizacji wykonywania kwerendy.
* Usuń disableSSLVerification. Ta opcja ma nowe alternatywy opisane w [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Zezwalaj inicjały Na jawne ustawianie nagłówka klucza partycji
* Użyj pliku package.json#files, aby zapobiec publikowaniu plików obcych
* Napraw błąd sortowania mapy routingu w starszej wersji węzła+v8
* Naprawia błąd, gdy użytkownik dostarcza opcje częściowego ponowiania prób

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Zapobieganie rozwiązywaniu modułów wywoływanych za pomocą aplikacji WebPack

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Naprawia długi błąd, w którym ru były zawsze zgłaszane jako 0 dla zapytań agregujących

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 wersji v3! 🎉 Wiele nowych funkcji, poprawek błędów i kilku przełomowych zmian. Podstawowe cele tej wersji:

* Wdrażanie nowych, głównych funkcji
  * Różne zapytania
  * Kwerendy LIMIT/OFFSET
  * Żądania anulowane przez użytkownika
* Aktualizacja do najnowszej wersji interfejsu API Cosmos REST, w której wszystkie kontenery mają nieograniczoną skalę
* Ułatwianie korzystania z usługi Cosmos z przeglądarki
* Lepsze dostosowanie się do nowych wytycznych dotyczących zestawów SDK usługi Azure JS

#### <a name="migration-guide-for-breaking-changes"></a>Przewodnik po migracji dotyczący przełomowych zmian
##### <a name="improved-client-constructor-options"></a>Ulepszone opcje konstruktora klienta

Opcje konstruktora zostały uproszczone:

* MasterKey został przemianowany na klucz i przeniesiony do najwyższego poziomu
* Właściwości wcześniej w obszarze options.auth zostały przeniesione do najwyższego poziomu

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Uproszczony interfejs API queryiteratora
W wersji 2 było wiele różnych sposobów iteracji lub pobierania wyników z kwerendy. Staraliśmy się uprościć interfejs API w wersji 3 i usunąć podobne lub zduplikowane interfejsy API:

* Usuń plik iterator.next() i plik iterator.current(). Użyj fetchNext(), aby uzyskać strony wyników.
* Usuń plik iterator.forEach(). Zamiast tego należy użyć iteratorów asynchronii.
* nazwa pliku iterator.executeNext() zmieniona na iterator.fetchNext()
* nazwa pliku iterator.toArray() zmieniona na iterator.fetchAll()
* Strony są teraz właściwymi obiektami odpowiedzi zamiast zwykłych obiektów JS
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Stałe kontenery są teraz podzielone na partycje
Usługa Cosmos obsługuje teraz klucze partycji na wszystkich kontenerach, w tym tych, które zostały wcześniej utworzone jako kontenery stałe. SDK w wersji 3 aktualizuje do najnowszej wersji interfejsu API, która implementuje tę zmianę, ale nie jest przerywana. Jeśli nie podasz klucz partycji dla operacji, firma We will default to klucz systemowy, który współpracuje ze wszystkimi istniejącymi kontenerami i dokumentami.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert usunięty dla procedur przechowywanych
Wcześniej upsert był dozwolony dla kolekcji nie podzielonych na partycje, ale z aktualizacją wersji interfejsu API, wszystkie kolekcje są podzielone na partycje, więc usunęliśmy go całkowicie.

##### <a name="item-reads-will-not-throw-on-404"></a>Odczyty przedmiotów nie będą wrzucać na 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Domyślny zapis wieloregionowy
Zestaw SDK będzie teraz domyślnie zapisywać w wielu regionach, jeśli obsługuje konfigurację usługi Cosmos. Było to wcześniej opt-in zachowanie.

##### <a name="proper-error-objects"></a>Prawidłowe obiekty błędów
Żądania nie powiodły się teraz zgłosić prawidłowy błąd lub podklasy błędu. Wcześniej rzucali zwykłymi obiektami JS.

#### <a name="new-features"></a>Nowe funkcje
##### <a name="user-cancelable-requests"></a>Żądania anulowane przez użytkownika
Przejście do pobierania wewnętrznie pozwala nam używać interfejsu API abortController przeglądarki do obsługi operacji anulowania przez użytkownika. W przypadku operacji, w których potencjalnie w toku jest wiele żądań (takich jak zapytania partycji krzyżowej), wszystkie żądania operacji zostaną anulowane. Współcześni użytkownicy przeglądarki będą już mieli AbortController. Użytkownicy node.js będą musieli użyć biblioteki polifill

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Ustawianie przepływności w ramach operacji tworzenia bazy danych/kontenera
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Generowanie tokenu nagłówka zostało @azure/cosmos-signpodzielone na nową bibliotekę, . Każdy, kto bezpośrednio wywołuje interfejs API Cosmos REST, może używać @azure/cosmostego do podpisywania nagłówków przy użyciu tego samego kodu, który wywołujemy w środku.

##### <a name="uuid-for-generated-ids"></a>UUID dla wygenerowanych identyfikatorów
Wersja 2 miała niestandardowy kod do generowania identyfikatorów elementów. Przeszliśmy do dobrze znanej i utrzymywanej biblioteki społeczności uuid.

##### <a name="connection-strings"></a>Parametry połączeń
Teraz można przekazać parametry połączenia skopiowane z witryny Azure portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Lepsze działanie przeglądarki
Chociaż można było korzystać z v2 SDK w przeglądarce nie było to idealne doświadczenie. Trzeba było polifill kilka node.js wbudowanych bibliotek i użyć bundler jak Webpack lub Parcel. V3 SDK sprawia, że doświadczenie po wyjęciu z pudełka znacznie lepiej dla użytkowników przeglądarki.

* Zastąp wewnętrzne żądania za pomocą pobierania (#245)
* Usuwanie użycia buforu (#330)
* Usuń wbudowane użycie węzła na rzecz uniwersalnych pakietów/interfejsów API (#328)
* Przełączanie do kontrolera węzła-przerwania (#294)

#### <a name="bug-fixes"></a>Poprawki błędów
* Napraw ofertę przeczytaj i przywwieź testy oferty (#224)
* Naprawianie zbierania materiałów dowodowych enableendpoint (#207)
* Napraw brakujące procesory RU na podstawie wyników (#360)
* Rozwiń typ parametru kwerendy SQL (#346)
* Dodawanie ttl dodefiniacji elementów (#341)
* Napraw metryki zapytań CP (#311)
* Dodaj activityId do FeedResponse (#293)
* Przełącz _ts typu z ciągu na liczbę (#252)(#295)
* Napraw agregację opłat za żądanie (#289)
* Zezwalaj na puste klucze partycji ciągu (#277)
* Dodawanie ciągu do typu kwerendy powodującej konflikty (#237)
* Dodaj uniqueKeyPolicy do kontenera (#234)

#### <a name="engineering-systems"></a>Systemy inżynieryjne
Nie zawsze najbardziej widoczne zmiany, ale pomagają naszemu zespołowi szybciej wysyłać lepszy kod.

* Użyj zestawienia dla kompilacji produkcyjnych (#104)
* Aktualizacja do skryptu maszynowego 3.5 (#327)
* Konwertuj na odwołania do projektu TS. Wyodrębnij folder testowy (#270)
* Włącz noUnusedLocals i noUnusedParameters (#275)
* Usługi Azure Potoki YAML dla kompilacji ci (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Brak zmian w kodzie. Rozwiązano problem polegający na tym, że niektóre dodatkowe pliki zostały uwzględnione w pakiecie 2.1.4.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Napraw regionalnych trybu failover w ramach zasad ponawiania próby
* Napraw właściwość ChangeFeed hasMoreResults
* Aktualizacje zależności deweloperów
* Dodaj PolicheckExclusions.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Przełączanie typu _ts z ciągu na numer
* Naprawianie domyślnych testów indeksowania
* Backport uniqueKeyPolicy do v2
* Poprawki do debugowania wersji demonstracyjnych i demonstracyjnych

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Poprawki oferty Backport z oddziału v3
* Napraw błąd w podpisie typu executeNext()
* Poprawki literówek

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Budowanie restrukturyzacji. Umożliwia ściąganie wersji SDK w czasie kompilacji.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nowe funkcje
* Dodano obsługę ChangeFeed (#196)
* Dodano typ danych MultiPolygon do indeksowania (#191)
* Dodaj właściwość "key" do konstruktora jako alias dla masterKey (#202)

#### <a name="fixes"></a>Poprawki
* Napraw błąd, w którym next() zwracał niepoprawną wartość na iteratorze

#### <a name="engineering-improvements"></a>Ulepszenia inżynieryjne
* Dodaj test integracji dla zużycia maszynopisu (#199)
* Włącz instalację bezpośrednio z gitHub (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Dodaje interfejs dla typu agenta węzła. Użytkownicy typescript nie @types/node muszą już instalować jako zależności
* Preferowane lokalizacje są teraz odpowiednio honorowane
* Ulepszenia w zakresie tworzenia dokumentacji dewelopera
* Różne poprawki literówek

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Rozwiązuje problem z definicją typu wprowadzony w 2.0.3

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Usuwanie `big-integer` zależności
* Przełącz się do dyrektyw referencyjnych dla typu AsyncIterable. Użytkownicy typescript nie muszą już dostosowywać ustawienia "lib".
* Poprawki literówki

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Naprawianie linków readme

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Napraw implementację interfejsu ponawiania prób

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* GA wersji 2.0.0 sdk JavaScript
* Dodano obsługę zapisów wieloregionowych.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 wersji 2.0.0 zestawu JavaScript SDK do publicznej wersji zapoznawczej.
* Nowy model obiektów, z najwyższego poziomu CosmosClient i metody podzielone na odpowiednie bazy danych, kontenera i item klas. 
* Wsparcie dla [obietnic](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK przekonwertowany na TypeScript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* dokumentacji npm naprawiono.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Dodano obsługę domyślnych ponownych prób w przypadku problemów z połączeniem.
* Dodano obsługę kanału zbierania danych.
* Naprawiono błąd spójności sesji, który sporadycznie powodował ,,sesja odczytu niedostępna".
* Dodano obsługę metryk zapytań.
* Zmodyfikowano maksymalną liczbę połączeń http Agenta.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Zaktualizowano dokumentację, aby odwoływać się do usługi Azure Cosmos DB zamiast usługi Azure DocumentDB.
* Dodano obsługę ustawień proxyUrl w ConnectionPolicy.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Drobne poprawki dla systemów plików z uwzględnieniem wielkości liter.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Dodaje obsługę spójności sesji.
* Ta wersja SDK wymaga najnowszej wersji emulatora usługi https://aka.ms/cosmosdb-emulatorAzure Cosmos DB dostępnej do pobrania z .

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Podzielone sprawdzane zapytania partycji krzyżowej.
* Dodaje podpory dla łącza zasobów z ukośnikami wiodącymi i kończącymi (i odpowiednimi testami).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*   dokumentacji npm naprawiono.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Naprawiono błąd w executeStoredProcedure, gdzie dokumenty zaangażowane miał specjalne znaki Unicode (LS, PS).
* Naprawiono błąd w obsłudze dokumentów ze znakami Unicode w kluczu partycji.
* Poprawiono obsługę tworzenia kolekcji za pomocą nośnika nazw. #114 problem z gitHubem.
* Poprawiono obsługę tokenu autoryzacji uprawnień. #178 problem z gitHubem.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Dodano obsługę nowego [poziomu spójności](consistency-levels.md) o nazwie ConsistentPrefix.
* Dodano obsługę UriFactory.
* Naprawiono błąd obsługi Unicode. #171 problem gitHub.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Dodano obsługę zapytań agregacji (COUNT, MIN, MAX, SUM i AVG).
* Dodano opcję kontrolowania stopnia równoległości dla zapytań między partycjami.
* Dodano opcję wyłączania weryfikacji SSL podczas uruchamiania względem emulatora usługi Azure Cosmos DB.
* Obniżono minimalną przepustowość w kolekcjach podzielonych na partycje z 10 100 RU/s do 2500 RU/s.
* Naprawiono błąd tokenu kontynuacji dla kolekcji pojedynczej partycji. #107 problem z gitHubem.
* Naprawiono błąd executeStoredProcedure w obsłudze 0 jako pojedynczy param. #155 problem z gitHubem.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Poprawiono nagłówek agenta użytkownika, aby zawierał wersję SDK.
* Drobne oczyszczanie kodu.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Wyłączenie weryfikacji SSL podczas korzystania z SDK do kierowania emulator(nazwa hosta = localhost).
* Dodano obsługę włączania rejestrowania skryptów podczas wykonywania procedury składowanej.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Dodano obsługę zapytań równoległych między partycjami.
* Dodano obsługę zapytań TOP/ORDER BY dla kolekcji podzielonych na partycje.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Dodano obsługę zasad ponawiania dla żądań ograniczonych. (Żądania ograniczone otrzymują zbyt duży wyjątek, kod błędu 429). Domyślnie usługa Azure Cosmos DB ponawia dziewięć razy dla każdego żądania, gdy zostanie napotkany kod błędu 429, honorując ponawianie czasu po wierszu w nagłówku odpowiedzi. Czas stałego interwału ponawiania próby można teraz ustawić jako część właściwości RetryOptions w obiekcie ConnectionPolicy, jeśli chcesz zignorować ponowny ponawianieczasu zwróconego przez serwer między ponownych prób. Usługa Azure Cosmos DB czeka teraz maksymalnie 30 sekund dla każdego żądania, które jest ograniczane (niezależnie od liczby ponownych prób) i zwraca odpowiedź z kodem błędu 429. Ten czas można również zastąpić we właściwości RetryOptions na ConnectionPolicy obiektu.
* Usługa Cosmos DB zwraca teraz x-ms-throttle-retry-count i x-ms-throttle-retry-wait-time-ms jako nagłówki odpowiedzi w każdym żądaniu, aby oznaczyć liczbę ponownych prób przepustnicy i skumulowany czas oczekiwania między ponownych prób.
* Dodano klasę RetryOptions, ujawniając właściwość RetryOptions w klasie ConnectionPolicy, która może służyć do zastępowania niektórych domyślnych opcji ponawiania prób.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Dodano obsługę kont bazy danych w wielu regionach.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Dodano obsługę funkcji Time To Live (TTL) dla dokumentów.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Zaimplementowano [kolekcje podzielone na partycje](partition-data.md) i [poziomy wydajności zdefiniowane przez użytkownika](performance-levels.md).

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Poprawiono problem RangePartitionResolver.resolveForRead, w którym nie zwracał linków z powodu złego połączenia wyników.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* Fixed hashPartitionResolver resolveForRead(): Gdy nie podano klucz partycji był zgłaszanie wyjątku, zamiast zwracania listy wszystkich zarejestrowanych linków.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Rozwiązuje problem [#100](https://github.com/Azure/azure-documentdb-node/issues/100) — dedykowany agent HTTPS: Należy unikać modyfikowania agenta globalnego na potrzeby usługi Azure Cosmos DB. Użyj dedykowanego agenta dla wszystkich żądań lib.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Rozwiązuje problem [#81](https://github.com/Azure/azure-documentdb-node/issues/81) — prawidłowo obsługiwać myślniki w identyfikatorach nośników.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Rozwiązuje problem [#95](https://github.com/Azure/azure-documentdb-node/issues/95) — ostrzeżenie o wycieku odbiornika EventEmitter.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Rozwiązuje problem [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - zmień nazwę folderu Hash na skróty dla systemów z uwzględnieniem wielkości liter.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Zaimplementuj obsługę dzielenia na fragmenty przez dodanie programów rozpoznawania podziałów & zakres.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Zaimplementuj upsert. Nowe metody upsertXXX na documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Pominięte, aby dostosować numery wersji do innych zestawu SDK.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Split Q obiecuje otokę do nowego repozytorium.
* Aktualizacja do pliku pakietu dla rejestru npm.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementuje routing oparty na identyfikatorze.
* Rozwiązuje problem [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - bieżąca właściwość powoduje konflikty z metodą current().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Dodano obsługę indeksu GeoSpatial.
* Sprawdza poprawność właściwości identyfikatora dla wszystkich zasobów. Identyfikatory zasobów nie mogą zawierać ?, /, #, &#47;&#47;, znaków ani kończyć spacją.
* Dodaje nowy nagłówek "postęp transformacji indeksu" do ResourceResponse.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementuje zasady indeksowania V2.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Problem [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Zaimplementowano konfiguracje eslint i grunt w rdzeniu i obiecują SDK.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* #45 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) problem — otoka obietnic nie zawiera nagłówka z błędem.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Zaimplementowano możliwość wykonywania zapytań o konflikty przez dodanie readConflicts, readConflictAsync i queryConflicts.
* Zaktualizowana dokumentacja interfejsu API.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync error.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Zwolnij & daty przejścia na emeryturę
Firma Microsoft powiadamia o wycofaniu sdk sdk z co najmniej **12-miesięcznym** wyprzedzeniem w celu usprawnienia przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego sdk, jako takie zaleca się, aby zawsze uaktualnić do najnowszej wersji SDK tak wcześnie, jak to możliwe.

Każde żądanie do usługi Cosmos DB przy użyciu wycofanego SDK zostanie odrzucone przez usługę.

> [!WARNING]
> Wszystkie wersje **1.x** SDK klienta węzła dla interfejsu API SQL zostaną wycofane **30 sierpnia 2020 r.**. Dotyczy to tylko sdk węzła po stronie klienta i nie wpływa na skrypty po stronie serwera (procedury przechowywane, wyzwalacze i pliki UDF).
> 
>
<br/>

| Wersja | Data wydania | Data przejścia na emeryturę |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 lipca 2019 r. |--- |
| [3.0.4](#3.0.4) |22 lipca 2019 r. |--- |
| [3.0.3](#3.0.3) |17 lipca 2019 r. |--- |
| [3.0.2](#3.0.2) |9 lipca 2019 r. |--- |
| [3.0.0](#3.0.0) |28 czerwca 2019 r. |--- |
| [2.1.5](#2.1.5) |20 marca 2019 r. |--- |
| [2.1.4](#2.1.4) |15 marca 2019 r. |--- |
| [2.1.3](#2.1.3) |8 marca 2019 r. |--- |
| [2.1.2](#2.1.2) |28 stycznia 2019 r. |--- |
| [2.1.1](#2.1.1) |5 grudnia 2018 r. |--- |
| [2.1.0](#2.1.0) |4 grudnia 2018 r. |--- |
| [2.0.5](#2.0.5) |7 listopada 2018 r. |--- |
| [2.0.4](#2.0.4) |30 października 2018 r. |--- |
| [2.0.3](#2.0.3) |30 października 2018 r. |--- |
| [2.0.2](#2.0.2) |10 października 2018 r. |--- |
| [2.0.1](#2.0.1) |25 września 2018 r. |--- |
| [2.0.0](#2.0.0) |Wrzesień 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 sierpnia 2018 r. |--- |
| [1.14.4](#1.14.4) |3 maja 2018 r. |30 sierpnia 2020 r. |
| [1.14.3](#1.14.3) |3 maja 2018 r. |30 sierpnia 2020 r. |
| [1.14.2](#1.14.2) |21 grudnia 2017 r. |30 sierpnia 2020 r. |
| [1.14.1](#1.14.1) |10 listopada 2017 r. |30 sierpnia 2020 r. |
| [1.14.0](#1.14.0) |9 listopada 2017 r. |30 sierpnia 2020 r. |
| [1.13.0](#1.13.0) |11 października 2017 r. |30 sierpnia 2020 r. |
| [1.12.2](#1.12.2) |10 sierpnia 2017 r. |30 sierpnia 2020 r. |
| [1.12.1](#1.12.1) |10 sierpnia 2017 r. |30 sierpnia 2020 r. |
| [1.12.0](#1.12.0) |10 maja 2017 |30 sierpnia 2020 r. |
| [1.11.0](#1.11.0) |16 marca 2017 r. |30 sierpnia 2020 r. |
| [1.10.2](#1.10.2) |27 stycznia 2017 r. |30 sierpnia 2020 r. |
| [1.10.1](#1.10.1) |22 grudnia 2016 r. |30 sierpnia 2020 r. |
| [1.10.0](#1.10.0) |3 października 2016 r. |30 sierpnia 2020 r. |
| [1.9.0](#1.9.0) |7 lipca 2016 r. |30 sierpnia 2020 r. |
| [1.8.0](#1.8.0) |14 czerwca 2016 r. |30 sierpnia 2020 r. |
| [1.7.0](#1.7.0) |26 kwietnia 2016 |30 sierpnia 2020 r. |
| [1.6.0](#1.6.0) |29 marca 2016 r. |30 sierpnia 2020 r. |
| [1.5.6](#1.5.6) |8 marca 2016 r. |30 sierpnia 2020 r. |
| [1.5.5](#1.5.5) |2 lutego 2016 r. |30 sierpnia 2020 r. |
| [1.5.4](#1.5.4) |1 lutego 2016 r. |30 sierpnia 2020 r. |
| [1.5.2](#1.5.2) |26 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.5.2](#1.5.2) |22 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.5.1](#1.5.1) |4 stycznia 2016 r. |30 sierpnia 2020 r. |
| [1.5.0](#1.5.0) |31 grudnia 2015 r. |30 sierpnia 2020 r. |
| [1.4.0](#1.4.0) |6 października 2015 r. |30 sierpnia 2020 r. |
| [1.3.0](#1.3.0) |6 października 2015 r. |30 sierpnia 2020 r. |
| [1.2.2](#1.2.2) |10 września 2015 r. |30 sierpnia 2020 r. |
| [1.2.1](#1.2.1) |15 sierpnia 2015 r. |30 sierpnia 2020 r. |
| [1.2.0](#1.2.0) |5 sierpnia 2015 r. |30 sierpnia 2020 r. |
| [1.1.0](#1.1.0) |9 lipca 2015 r. |30 sierpnia 2020 r. |
| [1.0.3](#1.0.3) |4 czerwca 2015 r. |30 sierpnia 2020 r. |
| [1.0.2](#1.0.2) |23 maja 2015 r. |30 sierpnia 2020 r. |
| [1.0.1](#1.0.1) |15 maja 2015 r. |30 sierpnia 2020 r. |
| [1.0.0](#1.0.0) |8 kwietnia 2015 r. |30 sierpnia 2020 r. |

## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też
Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

