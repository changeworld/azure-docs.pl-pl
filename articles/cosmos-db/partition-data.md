---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu partycjonowania działa w usługi Azure Cosmos DB, jak skonfigurować partycje i kluczy partycji i Wybieranie klucza partycji odpowiednie dla twojej aplikacji.
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 968651e2bd06d54c8b735bf2418e0d84b94f315d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078568"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partycja i skali w usłudze Azure Cosmos DB

[Usługa Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to usługa globalnie dystrybuowanej, wielomodelowej bazy danych zaprojektowana, aby pomóc Ci osiągnąć wysoką i przewidywalną wydajność. Płynne skalowanie wraz z aplikacji. W tym artykule omówiono sposób partycjonowania działa w przypadku wszystkich danych modeli w usłudze Azure Cosmos DB. Również zawiera opis sposobu konfigurowania skutecznie skalowanie aplikacji kontenerów usługi Azure Cosmos DB.

Usługa Azure Cosmos DB obsługuje następujące typy kontenerów wszystkich interfejsów API:

- **Kontener stały**: te kontenery można przechowywać wykres bazy danych do 10 GB w rozmiarze maksymalnie 10 000 jednostek żądań na sekundę, przydzielone do niego. Aby utworzyć kontener stały nie jest konieczne określenie właściwości klucza partycji danych.

- **Nieograniczonego kontenera**: tych kontenerów może automatycznie skalować do przechowywania wykresu powyżej limitu 10 GB za pośrednictwem partycjonowanie poziome. Każda partycja będzie przechowywać 10 GB i dane zostaną automatycznie rozmieszczane na podstawie **klucza partycji określonym**, który będzie wymagany parametr, korzystając z nieograniczonego kontenera. Tego typu kontener może przechowywać rozmiar danych praktycznie nieograniczonych możliwości korzystania i umożliwia maksymalnie 100 000 jednostek żądań na sekundę lub więcej [, kontaktując się z pomocą techniczną](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

## <a name="partitioning-in-azure-cosmos-db"></a>Partycjonowanie w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB zapewnia kontenery do przechowywania danych o nazwie kolekcje (dla dokumentów), wykresy lub tabel. Kontenery są zasobami logicznymi i mogą znajdować się jeden lub więcej partycji fizycznych lub serwerów. Liczba partycji jest ustalana na podstawie rozmiaru magazynu usługi Azure Cosmos DB i przepływnością aprowizowaną dla kontenera lub zestaw kontenerów. 

### <a name="physical-partition"></a>Partycję fizyczną

A *fizycznych* partycja jest stałą zarezerwowanych magazynów opartych na dyskach SSD w połączeniu z zmienną ilość zasobów obliczeniowych (procesora CPU i pamięci). Każdą fizyczną partycję jest replikowana w celu zapewnienia wysokiej dostępności. Każdy zestaw kontenerów może współużytkować jedną lub więcej partycji fizycznych. Zarządzanie fizyczną partycję jest w pełni zarządzana przez usługę Azure Cosmos DB, a nie masz do pisania złożonego kodu lub zarządzania partycjami. Kontenery usługi Azure Cosmos DB są nieograniczone pod względem magazynu i przepływności. Partycje fizyczne są wewnętrznej koncepcji usługi Azure Cosmos DB i przejściowych. Usługa Azure Cosmos DB automatycznie skaluje liczbę partycji fizycznych na podstawie Twojego obciążenia. Dlatego nie należy corelate projektu bazy danych na podstawie liczby partycje fizyczne zamiast tego należy koniecznie wybierz klucz partycji prawo, który określa partycje logiczne. 

### <a name="logical-partition"></a>Partycja logiczna

A *logiczne* partycja w obrębie fizyczną partycję, która przechowuje wszystkie dane, które są skojarzone z jedną wartością klucza partycji. Wiele partycji logicznej mogą znaleźć się w tej samej partycji fizycznej. Na poniższym diagramie jeden kontener ma trzy partycjami logicznymi. Każda partycja logiczna przechowuje dane dla jednego klucza partycji LAX, usługi AMS i MEL odpowiednio. Każdy z partycjami logicznymi LAX, usługi AMS i MEL nie może rosnąć poza limit maksymalny partycji logicznej wynosi 10 GB. 

![Partycjonowanie zasobów](./media/introduction/azure-cosmos-db-partitioning.png) 

Gdy kontener spełnia [partycjonowanie wymagania wstępne](#prerequisites), Partycjonowanie jest całkowicie niewidoczne dla aplikacji. Usługa Azure Cosmos DB obsługuje routing żądań zapytań do prawego partycji i dystrybuowania danych między partycjami fizycznymi i logicznymi. 

## <a name="how-does-partitioning-work"></a>Jak działa partycjonowanie

Każdy dokument musi mieć *klucza partycji* i *klucz wiersza*, która jednoznacznie zidentyfikować. Klucz partycji działa jako partycji logicznej dla swoich danych i zapewnia usługi Azure Cosmos DB przy użyciu naturalnych granic rozproszenie danych na partycje fizyczne. **Dane dla jednej partycji logicznej musi znajdować się w jednej partycji fizycznych i Zarządzanie fizyczną partycję jest zarządzana przez usługę Azure Cosmos DB**. 

Krótko mówiąc Oto jak partycjonowanie działa w usłudze Azure Cosmos DB:

* Aprowizowanie zestawu kontenerów usługi Azure Cosmos DB przy użyciu **T** przepływności jednostek RU/s (liczba żądań na sekundę).  
* Za kulisami usługi Azure Cosmos DB obsługuje potrzebny, aby obsługiwać partycje fizyczne **T** żądań na sekundę. Jeśli **T** jest większa niż maksymalna przepływność na partycję fizyczną **t**, następnie usługi Azure Cosmos DB obsługuje **N T/t =** partycje fizyczne. Wartość maksymalna przepływność na partition(t) jest skonfigurowany przez usługę Azure Cosmos DB, ta wartość jest przypisywana na podstawie łączna aprowizowana przepływność i konfiguracji sprzętowej używane.  
* Usługa Azure Cosmos DB skróty kluczy obszarach kluczy partycji są przydziela równomiernie na **N** partycje fizyczne. Zatem liczby partycji logicznej jest poszczególnych hostów fizyczną partycję **1/N** * liczba wartości klucza partycji.  
* Gdy fizyczną partycję **p** osiąga limit przestrzeni dyskowej, usługi Azure Cosmos DB bezproblemowo dzieli **p** na dwie nowe partycje fizyczne **p1** i **p2**. Rozdziela on wartości odpowiadające około połowę klucze do każdego nowe partycje fizyczne. Operacja dzielenia jest całkowicie niewidoczne dla aplikacji. Jeśli fizyczną partycję osiągnie limit przestrzeni dyskowej i wszystkich danych z partycji fizycznych należy do tego samego klucza partycji logicznej, operacja dzielenia nie występuje. Jest to spowodowane wszystkich danych dla pojedynczego logicznego klucza partycji muszą znajdować się w tej samej partycji fizycznej. W takim przypadku zostanie użyty innej partycji kluczową strategią.  
* Podczas aprowizowania przepływności wyższy niż **t * N**, usługi Azure Cosmos DB dzieli jedną lub więcej partycji fizycznych, aby obsługiwać większą przepływność.

Semantyka dla kluczy partycji różnią się nieco do dopasowania semantykę każdy interfejs API, jak pokazano w poniższej tabeli:

| Interfejs API | Klucz partycji | Klucz wiersza |
| --- | --- | --- |
| SQL | Ścieżka klucza partycji niestandardowe | Naprawiono polecenie `id` | 
| MongoDB | Klucz fragmentu niestandardowe  | Naprawiono polecenie `_id` | 
| Gremlin | Właściwość klucza partycji niestandardowe | Naprawiono polecenie `id` | 
| Tabela | Naprawiono polecenie `PartitionKey` | Naprawiono polecenie `RowKey` | 

Usługa Azure Cosmos DB używa bazujących na skrótach partycjonowania. Podczas pisania element usługi Azure Cosmos DB miesza wartość klucza partycji i używa skróconego wyniku w celu określenia partycji do przechowywania elementu w. 

> [!NOTE]
> Usługa Azure Cosmos DB przechowuje wszystkie elementy z tym samym kluczem partycji w tej samej partycji fizycznej. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Najlepsze rozwiązania, gdy wybór klucza partycji

Wybór klucza partycji jest ważnym krokiem, który należy podjąć w czasie projektowania. Wybierz nazwę właściwości, która ma szeroki zakres wartości, a nawet wzorców dostępu. Jest najlepszym rozwiązaniem jest zapewnienie klucza partycji z dużą liczbą unikatowe wartości (np. setek lub tysięcy). Umożliwia równomierne rozłożenie obciążenia między te wartości. Klucz partycji idealnym rozwiązaniem jest taki, który pojawia się często, jak filtr zapytania i ma wystarczającą właściwość kardynalność na wartość upewnij się, że Twoje rozwiązanie jest skalowalna.

Jeśli fizyczną partycję osiągnie limit przestrzeni dyskowej i danych na partycji, ma taki sam klucz partycji, usługi Azure Cosmos DB zwraca *"klucz partycji osiągnęła maksymalny rozmiar wynosi 10 GB"* nie została podzielona wiadomość i partycji. Wybór stanowi dobry klucz partycji jest bardzo ważnym krokiem. 

Wybór klucza partycji tak, aby:

* Dystrybucja magazynu jest nawet w przypadku wszystkich kluczy.  
* Wybierz klucz partycji służący do równomiernego dystrybuowania danych między partycjami.

  To dobry pomysł, aby sprawdzić, jak Twoje dane są rozproszone na partycje. Aby sprawdzić dystrybucję danych w portalu, przejdź na swoje konto usługi Azure Cosmos DB, a następnie kliknij pozycję **metryki** w **monitorowanie** sekcji, a następnie kliknij polecenie **magazynu** kartę, aby zobaczyć, jak usługi dane są partycjonowane na różne partycje fizyczne.

  ![Partycjonowanie zasobów](./media/partition-data/partitionkey-example.png)

  Po lewej stronie powyższy obraz pokazuje wynik klucza partycji zły i odpowiednie powyższy obraz pokazuje wynik, gdy wybrano stanowi dobry klucz partycji. W obrazie po lewej stronie widać, że dane nie jest równomiernie rozłożona na partycje. Należy dążyć do wybór klucza partycji, który rozdziela dane, dzięki czemu będzie on podobny do właściwy obraz.

* Optymalizowanie zapytań, aby uzyskać dane w granicach partycji, jeśli jest to możliwe. Optymalnej strategii partycjonowania byłaby wyrównana do wzorców zapytań. Zapytania, które pobierają dane z jednej partycji zapewnia maksymalną wydajność. Zapytania, które są wywoływane ze współbieżnością wysokiej można skutecznie kierować przez uwzględnienie klucza partycji w predykacie filtru.  

* Wybór klucza partycji o wyższych kardynalności ogólnie metoda preferowana — utrudniłoby zazwyczaj daje większą dystrybucji i skalowalność. Na przykład syntetycznych klucz mogą powstawać przez złączenie wartości z wiele właściwości, aby zwiększyć kardynalność.  

Wybór klucza partycji przy użyciu powyższe zagadnienia, nie trzeba martwić się o liczbie partycji lub ile przepływność jest przydzielany na partycję fizyczną zgodnie z usługi Azure Cosmos DB umożliwia skalowanie liczby partycji fizycznych, gdy można również skalować poszczególne partycje zgodnie z potrzebami.

## <a name="prerequisites"></a>Wymagania wstępne dotyczące partycjonowania

Kontenery usługi Azure Cosmos DB można utworzyć ustalona lub wartością nieograniczoną. Kontenery o stałym rozmiarze są ograniczone do 10 GB, a ich maksymalna przepływność wynosi 10 000 jednostek żądań na sekundę. Aby utworzyć kontener jako bez ograniczeń, należy określić klucz partycji i minimalnej przepustowości 1000 jednostek RU/s. Kontenery usługi Azure Cosmos DB można również utworzyć w taki sposób, że używają one przepływności. W takich przypadkach należy występowaniem każdego kontenera klucza partycji i powiększać tak, bez ograniczeń. 

Poniżej przedstawiono wymagania wstępne dotyczące należy wziąć pod uwagę, partycjonowanie i skalowanie:

* Podczas tworzenia kontenera (np. Kolekcja, wykres lub tabelę) w witrynie Azure portal wybierz **nieograniczone** opcję pojemności magazynu, aby skorzystać ze skalowania bez ograniczeń. Na partycje fizyczne podziału automatycznie do **p1** i **p2** zgodnie z opisem w [jak działa partycjonowania](#how-does-partitioning-work) artykule kontenera musi zostać utworzona z 1000 jednostek RU/s przepustowości lub więcej (lub udziału przepływności na zestaw kontenerów), a klucz partycji musi być podana. 

* Jeśli możesz utworzyć kontener z początkową przepustowość, większa niż lub równa 1000 jednostek RU/s i podaj klucz partycji, a następnie korzystać z zalet nieograniczone skalowanie bez konieczności wprowadzania zmian do kontenera. Oznacza to, nawet jeśli utworzysz **stałe** kontener, jeśli początkowe kontener jest tworzony przy przepływności co najmniej 1000 jednostek RU/s, a jeśli klucz partycji jest określony, kontener działa jako nieograniczonego kontenera.

* Wszystkie kontenery skonfigurowany tak, aby udostępnić przepływności jako część zestawu kontenerów są traktowane jako **nieograniczone** kontenerów.

Jeśli utworzono **stałe** kontenera bez partycji klucza lub przepływności, mniej niż 1000 jednostek RU/s, kontenera będzie nie automatycznego skalowania. Aby przeprowadzić migrację danych z kontener stały do nieograniczonego kontenera, musisz użyć [narzędzia migracji danych](import-data.md) lub [kanału informacyjnego zmian biblioteki](change-feed.md). 

## <a name="designing-for-partitioning"></a> Utwórz klucz partycji 
Tworzenie kontenerów i skalować je w dowolnym momencie można użyć witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. W tej sekcji pokazano, jak tworzyć kontenery i określ klucz aprowizowanej przepływności i partycji przy użyciu każdego interfejsu API.


### <a name="sql-api"></a>Interfejs API SQL
Poniższy przykład pokazuje sposób tworzenia kontenera (kolekcję) za pomocą interfejsu API SQL. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Można znaleźć elementu (dokumenty) przy użyciu `GET` metody interfejsu API REST lub przy użyciu `ReadDocumentAsync` w jednym z zestawów SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Aby uzyskać więcej informacji, zobacz [partycjonowanie w usłudze Azure Cosmos DB przy użyciu interfejsu API SQL](sql-api-partition-data.md).

### <a name="mongodb-api"></a>Interfejs API usługi MongoDB
Za pomocą interfejsu API usługi MongoDB można utworzyć kolekcji podzielonych na fragmenty za pośrednictwem Twojego ulubionego narzędzia, sterownika lub zestawu SDK. W tym przykładzie używamy powłoki Mongo do utworzenia kolekcji.

W przypadku powłoki Mongo:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Wyniki:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>Interfejs API tabel

Aby utworzyć tabelę przy użyciu interfejsu API tabeli, należy użyć `CreateIfNotExists` metody. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Aprowizowana przepływność jest ustawiony jako argument `CreateIfNotExists`. Klucz partycji jest niejawnie tworzone jako `PartitionKey` wartość. 

Możesz pobrać pojedynczą jednostkę przy użyciu następującego kodu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Aby uzyskać więcej informacji, zobacz [opracowywanie zawartości przy użyciu interfejsu API tabel](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Interfejs API języka Gremlin

Za pomocą interfejsu API języka Gremlin można użyć witryny Azure portal lub interfejsu wiersza polecenia platformy Azure do utworzenia kontenera, który reprezentuje wykresu. Alternatywnie ponieważ usługi Azure Cosmos DB to wielomodelowa, służy jednego z innych interfejsów API do tworzenia i skalowania kontenera grafu.

> [!NOTE]
> `/id` i `/label` nie są obsługiwane jako klucze partycji dla kontenera w interfejsie API języka Gremlin.

Możesz przeczytać dowolnego wierzchołka lub Microsoft edge, przy użyciu klucza partycji i Identyfikatora w języku Gremlin. Na przykład w przypadku programu graph z regionem ("USA") jako klucza partycji i "Seattle", jako klucz wiersza, można znaleźć wierzchołka przy użyciu następującej składni:

```
g.V(['USA', 'Seattle'])
```

Krawędź odwoływać się za pomocą klucza partycji i klucza wiersza.

```
g.E(['USA', 'I5'])
```

Aby uzyskać więcej informacji, zobacz [przy użyciu wykresu podzielonym na partycje w usłudze Azure Cosmos DB](graph-partitioning.md).

## <a name="form-partition-key-by-concatenating-multiple-fields"></a>Klucz partycji formularza przez złączenie wielu pól

Klucz partycji można również tworzyć, złączenie i wypełnienie wielu wartości właściwości do właściwości pojedynczej sztuczne "partitionKey" elementu. Te klucze są określane jako klucze syntetycznych.

Na przykład masz dokument, który wygląda następująco:

```json
{
"deviceId": "abc-123",
"date": 2018
}
```

Jedną z opcji jest ustawiona partitionKey /deviceId lub /date. Jeśli chcesz tworzą klucz partycji, identyfikator urządzenia i datą. Połączyć te dwie wartości w z właściwością sztuczne "partitionKey", a następnie ustaw klucz partycji /partitionKey.

```json
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

W scenariuszach w czasie rzeczywistym może mieć tysiące dokumentów, więc należy zdefiniować logikę po stronie klienta, aby połączyć wartości na klucz syntetycznych, wstawić syntetycznych klucza do dokumentów, a następnie używania jej było określenie klucza partycji.

## <a name="designing-for-scale"></a> Projektowanie pod kątem skalowania
Aby Skaluj efektywnie za pomocą usługi Azure Cosmos DB, musisz wybrać stanowi dobry klucz partycji podczas tworzenia kontenera. Istnieją dwa główne kwestie dotyczące wybierania stanowi dobry klucz partycji:

* **Zapytania granic i transakcje**. Wybór klucza partycji należy zrównoważyć potrzebę na potrzeby transakcji wymaganiem rozdystrybuować jednostek wiele kluczy partycji, aby upewnić się, skalowalne rozwiązanie. W skrajnej jeden ten sam klucz partycji można ustawić dla wszystkich elementów, ale ta opcja może ograniczać skalowalność rozwiązania. Y. można przypisać unikatowego klucza partycji dla każdego elementu. Ten wybór jest o wysokim stopniu skalowalności, ale uniemożliwia za pomocą transakcji dla wielu dokumentów za pomocą procedur składowanych i wyzwalaczy. Klucz partycji idealne umożliwia wydajne zapytania i ma wystarczające Kardynalność, aby upewnić się, że Twoje rozwiązanie jest skalowalna. 
* **Nie magazyn i wydajność wąskich gardeł**. Należy wybrać właściwość, która zezwala na zapisy być rozproszone między różne unikatowe wartości. Żądania do tego samego klucza partycji nie może przekraczać aprowizowanej przepływności przydzielane do partycji i będą ograniczone szybkości. Więc koniecznie wybierz taki klucz partycji, który nie powoduje "aktywne", w ramach aplikacji. Ponieważ wszystkie dane dla pojedynczego klucza partycji muszą być przechowywane w partycji, należy unikać klucze partycji, które mają duże ilości danych dla tej samej wartości. 

Przyjrzyjmy się kilku scenariuszy w rzeczywistych warunkach i klucze partycji dla każdego:
* W przypadku wdrażania profilu użytkownika wewnętrznej bazie danych, *identyfikator użytkownika* jest dobrym wyborem dla klucza partycji.
* Jeśli w przypadku przechowywania danych IoT, na przykład stan urządzenia *identyfikator urządzenia* jest dobrym wyborem dla klucza partycji.
* Jeśli używasz usługi Azure Cosmos DB dla rejestrowania danych szeregów czasowych *hostname* lub *identyfikator procesu* jest dobrym wyborem dla klucza partycji.
* W przypadku architektury wielodostępnej, *identyfikator dzierżawy* jest dobrym wyborem dla klucza partycji.

W niektórych zastosowań, takich jak profile użytkowników i IoT, klucz partycji może być taka sama jak Twoje *identyfikator* (klucz dokumentu). W innych, takich jak dane szeregów czasowych, Niewykluczone, że klucz partycji, która różni się od *identyfikator*.

### <a name="partitioning-and-loggingtime-series-data"></a>Dane partycjonowania i rejestrowanie/szeregów czasowych
Jednym z typowych przypadków użycia w usłudze Azure Cosmos DB jest, rejestrowania i dane telemetryczne. Ważne jest do pobrania stanowi dobry klucz partycji, w tym scenariuszu, ponieważ może być konieczne do odczytu/zapisu ogromnych ilości danych. Wybór klucza partycji zależy od tego, Twoje kursy odczytu i zapisu i rodzajów zapytań, które chcesz uruchomić. Poniżej przedstawiono kilka porad na temat sposobu wybierania stanowi dobry klucz partycji:

* Jeśli danego przypadku użycia obejmuje mała liczba operacji zapisu, które są gromadzone przez długi czas, i chcesz zapytania według zakresów sygnatury czasowe z pozostałymi filtrami, należy użyć pakiet zbiorczy sygnaturę czasową. Na przykład dobra metoda jest używana data jako klucza partycji. W przypadku tej metody można tworzyć zapytania na danych dla określonej daty z jednej partycji. 
* Jeżeli obciążenie jest procesów, który jest bardzo częsty w tym scenariuszu, należy użyć klucza partycji, który nie jest oparty na sygnaturę czasową. Jako takie, usługi Azure Cosmos DB można rozpowszechniania i skalowania równomiernie zapisów w różnych partycjach. W tym miejscu *hostname*, *identyfikator procesu*, *identyfikator działania*, lub innej właściwości o dużej kardynalności, to dobry wybór. 
* Innym rozwiązaniem jest hybrydowa, gdzie masz wiele kontenerów, jeden na każdy dzień/miesiąc, a klucz partycji to bardziej szczegółowe właściwości, takich jak *hostname*. Takie podejście ma tę zaletę, który inny przepływności można ustawić dla każdego kontenera lub zestaw kontenerów na podstawie przedział czasu i wymagań skali i wydajności. Na przykład kontener dla bieżącego miesiąca może być przygotowana z wyższej przepustowości, ponieważ obsługuje operacje odczytu i zapisu. Poprzednie miesiące może być przygotowana o niższym przepustowości, ponieważ służą one tylko operacji odczytu.

### <a name="partitioning-and-multitenancy"></a>Partycjonowanie i wielodostępności
W przypadku wdrażania aplikacji wielodostępnej za pomocą usługi Azure Cosmos DB, istnieją dwa popularne projekty do rozważenia: *klucz jednej partycji na dzierżawę* i *jeden kontener na dzierżawę*. Poniżej przedstawiono zalety i wady, dla każdego:

* **Klucz partycji co dla dzierżawy**. W tym modelu dzierżawy są wspólnie przechowywane w jednym kontenerze. Można wykonywać zapytania i operacje wstawiania do pojedynczej dzierżawy na jednej partycji. Możesz również wdrożyć logiki transakcyjnej dla wszystkich elementów należących do dzierżawy. Ponieważ wiele dzierżaw współużytkuje to kontener, możesz lepsze wykorzystanie magazynu i aprowizowanej przepływności, pula zasobów dla wszystkich dzierżaw w jednym kontenerze, a nie inicjowania obsługi administracyjnej dla każdego dzierżawcy. Wadą jest to, że nie masz izolację wydajności na dzierżawę. Zwiększa przepustowość, aby zagwarantować wydajność będzie dotyczyć całego kontenera przy użyciu wszystkich dzierżaw i zwiększa docelowe dla pojedynczego dzierżawcy.
* **Jeden kontener na dzierżawę**. W tym modelu każdy dzierżawca ma swój własny kontener, i może zarezerwować przepływność gwarantowana wydajność każdej dzierżawy. Ten model jest bardziej opłacalna dla aplikacji wielodostępnych za pomocą kilka dzierżaw.

Umożliwia także hybrydowa ze sobą colocates małymi dzierżawami, który izoluje większych dzierżaw do ich własnych kontenerów.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule zamieszczone omówienie pojęć i najlepsze rozwiązania do skalowania i partycjonowanie w usłudze Azure Cosmos DB. 

* Dowiedz się więcej o [aprowizowanej przepływności w usłudze Azure Cosmos DB](request-units.md).
* Dowiedz się więcej o [dystrybucję globalną w usłudze Azure Cosmos DB](distribute-data-globally.md).



