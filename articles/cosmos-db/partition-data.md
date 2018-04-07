---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat działa jak partycjonowania bazy danych rozwiązania Cosmos Azure, jak skonfigurować partycje i kluczy partycji i Wybieranie klucza prawego partycji aplikacji.
services: cosmos-db
author: arramac
manager: kfile
editor: monicar
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16b0ddd45c8e524798a453af7c731af28f5f5c2d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partycja i skali w usłudze Azure DB rozwiązania Cosmos

[Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) jest usługą bazy danych globalnie rozproszone, multimodel pomaga uzyskać szybkie, przewidywalną wydajność. Go skaluje bezproblemowo wraz z aplikacji jako ich przyrostu. Ten artykuł zawiera omówienie sposobu modele partycjonowania działa w przypadku wszystkich danych w usłudze Azure DB rozwiązania Cosmos. Opisuje również, jak można skonfigurować bazy danych Azure rozwiązania Cosmos kontenery skutecznie skalowania aplikacji.

Partycjonowanie i kluczy partycji omówiono to wideo z usługi Azure rozwiązania Cosmos bazy danych Menedżera programów, Andrew Liu:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Podział na partycje w Azure rozwiązania Cosmos bazy danych
W usłudze Azure DB rozwiązania Cosmos można przechowywać i zapytania na danych bez schematu wraz z czasem odpowiedzi kolejności z milisekundy na dowolnym poziomie. Azure DB rozwiązania Cosmos zawiera kontenery do przechowywania danych o nazwie *kolekcje* (w przypadku dokumentów) *wykresy*, lub *tabel*. 

Kontenery są zasoby logiczne i może obejmować co najmniej jednej partycji fizycznej lub serwerów. Liczba partycji zależy od bazy danych rozwiązania Cosmos Azure na podstawie rozmiaru magazynu i udostępnionej przepływności kontenera. 

Partycji fizycznej jest stałą zastrzeżone magazynu kopii dysków SSD. Każda partycja fizycznych są replikowane wysokiej dostępności. Co najmniej jednej partycji fizycznej tworzą kontenera. Zarządzanie partycji fizycznej pełni zarządza bazy danych Azure rozwiązania Cosmos i nie trzeba pisania złożonego kodu lub Zarządzanie partycjami. Azure kontenery DB rozwiązania Cosmos jest nieograniczony pod względem pamięci masowej i przepływność. 

Partycji logicznej jest partycji w obrębie partycji fizycznej, która przechowuje wszystkie dane skojarzone z jedną partycją wartości klucza. Partycji logicznej ma maksymalnie 10 GB. Na poniższym diagramie jeden kontener ma trzy partycje logiczne. Każdej partycji logicznej przechowuje dane dla jednego klucza partycji, LAX AMS i MEL odpowiednio. Nie można każdej partycji logicznej LAX AMS i MEL rosnąć przekroczenie limitu maksymalnej partycji logicznej wynosi 10 GB. 

![Partycjonowanie zasobów](./media/introduction/azure-cosmos-db-partitioning.png) 

Jeśli kolekcja spełnia [partycjonowania wymagania wstępne](#prerequisites), Partycjonowanie jest niewidoczny dla aplikacji. Azure DB rozwiązania Cosmos obsługuje szybkie odczyty i zapisy, zapytania logiki transakcyjnej, poziomy spójności i szczegółowej kontroli dostępu za pomocą metody/interfejsów API do zasobu jeden kontener. Uchwyty usługi routing i dystrybucji danych między fizycznymi i logicznymi partycjami zapytania żądania do prawej partycji. 

## <a name="how-does-partitioning-work"></a>Jak działa partycjonowania

Jak działa partycjonowania? Każdy element musi mieć klucz partycji i klucz wiersza, które identyfikują go. Klucz partycji działa jako partycji logicznej danych i zapewnia bazy danych Azure rozwiązania Cosmos z granicą fizycznych dystrybucję danych partycji fizycznej. Należy pamiętać, że dane dla jednej partycji logicznej musi znajdować się wewnątrz jednej partycji fizycznej, ale zarządza zarządzania partycji fizycznej bazy danych Azure rozwiązania Cosmos. 

Krótko mówiąc Oto jak partycjonowania działa w usłudze Azure DB rozwiązania Cosmos:

* Udostępnianie kontenera Azure DB rozwiązania Cosmos z **T** żądania na drugi przepływności.
* W tle bazy danych rozwiązania Cosmos Azure udostępnia potrzebny, aby obsługiwać partycje **T** żądań na sekundę. Jeśli **T** jest większa niż maksymalna przepustowość dla każdej partycji **t**, następnie bazy danych Azure rozwiązania Cosmos przepisy **N = T/t** partycji.
* Azure DB rozwiązania Cosmos klawiszy skrótów obszaru klucza partycji są przydziela równomiernie w poprzek **N** partycji. Dlatego każdy hostów partition (partycji fizycznej) **1/N** (partycje logiczne) wartości klucza partycji.
* Gdy partycji fizycznej **p** osiągnie limit magazynu bazy danych rozwiązania Cosmos Azure bezproblemowo dzieli **p** do dwóch nowych partycji, **p1** i **p2** . Rozpowszechnia wartości odpowiadających około połowa klucze do każdej partycji. Tę samą operację jest niewidoczny dla aplikacji. Jeśli wszystkie dane w partycji fizycznej należy do tego samego klucza partycji logicznej partycji fizycznej osiągnie limit magazynu, operację podziału nie występuje. Jest to spowodowane wszystkie dane dla klucza jednej partycji logicznej musi znajdować się w tej samej partycji fizycznej i fizycznej partycji nie można dzielić p1 i p2. W takim przypadku można zastosować klucza strategii partycjonowania.
* Podczas obsługi administracyjnej przepływności wyższy niż **t * N**, bazy danych Azure rozwiązania Cosmos dzieli jedną lub więcej partycji do obsługi wyższej przepustowości.

Semantyka kluczy partycji są nieco inne odpowiadające semantykę każdego API, jak pokazano w poniższej tabeli:

| Interfejs API | Klucz partycji | Klucz wiersza |
| --- | --- | --- |
| Azure Cosmos DB | Ścieżka do klucza partycji niestandardowych | Naprawiono polecenie `id` | 
| MongoDB | Niestandardowy klucz udostępniony  | Naprawiono polecenie `_id` | 
| Graph | Właściwość klucza partycji niestandardowych | Naprawiono polecenie `id` | 
| Tabela | Naprawiono polecenie `PartitionKey` | Naprawiono polecenie `RowKey` | 

Azure DB rozwiązania Cosmos używa skrótu na podstawie partycjonowania. Podczas zapisywania elementu bazy danych Azure rozwiązania Cosmos skróty wartość klucza partycji i używa skrótu wynik w celu określenia, które partycji do przechowywania elementu w. Azure DB rozwiązania Cosmos przechowuje wszystkie elementy z tym samym kluczem partycji w tej samej partycji fizycznej. Wybór klucza partycji jest ważnych decyzji, które należy podjąć w czasie projektowania. Należy wybrać nazwę właściwości, który ma szeroki zakres wartości i ma nawet wzorce dostępu. Jeśli partycji fizycznej przez nią miejsce osiągnie limit magazynu jego i tego samego klucza partycji znajduje się na wszystkich danych na partycji bazy danych Azure rozwiązania Cosmos zwraca błąd "klucza partycji Osiągnięto maksymalny rozmiar 10 GB", a partycja nie jest podzielona, w związku z tym wybierania klucza partycji jest bardzo importu ANT decyzji.

> [!NOTE]
> Jest najlepszym rozwiązaniem będzie mieć klucz partycji wiele różnych wartości (kilkuset do kilku tysięcy co najmniej).
>

Kontenery DB rozwiązania Cosmos Azure mogą być tworzone jako *stałej* lub *nieograniczone* w portalu Azure. Kontenery o stałym rozmiarze są ograniczone do 10 GB, a ich maksymalna przepływność wynosi 10 000 jednostek żądań na sekundę. Aby utworzyć kontener jako nieograniczone, należy określić minimalnej przepustowości 1 000 RU/s i należy określić klucz partycji.

Należy dobrze, aby sprawdzić, jak dane są przesyłane w partycji. Aby to sprawdzić w portalu, przejdź do swojego konta bazy danych rozwiązania Cosmos Azure i kliknij pozycję **metryki** w **monitorowanie** sekcji, a następnie w prawym okienku kliknij **magazynu** kartę, aby zobaczyć, jak dane są podzielone na partycje w różnych fizycznych partycji.

![Partycjonowanie zasobów](./media/partition-data/partitionkey-example.png)

Po lewej stronie obraz przedstawia wynik klucza partycji zły i prawy obraz przedstawia wynik stanowi dobry klucz partycji. W obrazie po lewej stronie można zauważyć, że dane nie jest równomiernie rozłożony między partycji. Należy dążyć do rozpowszechniania danych, więc wykresie wygląda podobnie do prawego obrazu.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Wymagania wstępne dotyczące podziału na partycje

W przypadku partycji fizycznej do Podziel automatycznie na **p1** i **p2** zgodnie z opisem w [jak działa partycjonowania](#how-does-partitioning-work), należy utworzyć kontener o przepustowości 1 000 RU/s lub więcej , i musi zostać dostarczona klucza partycji. Podczas tworzenia kontenera w portalu Azure, wybierz **nieograniczone** opcji pojemności magazynu, aby móc korzystać z partycjonowania i automatyczne skalowanie. 

Jeśli utworzono kontener w portalu Azure lub programistycznie i początkowej przepływności został 1 000 RU/s lub więcej i dane obejmują klucz partycji, można wykorzystać partycjonowania bez zmian do kontenera programu — w tym **stałe**  rozmiaru kontenerów, tak długo, jak początkowej kontener został utworzony co najmniej 1 000 RU/s w througput i klucz partycji znajduje się w danych.

Jeśli utworzono **stałe** rozmiar kontener z nie partycji klucza lub utworzonych **stałe** rozmiaru kontenera o przepływności mniejszym niż 1 000 RU/s, kontenera nie może automatycznie podziału zgodnie z opisem w tym artykule. Do migracji danych z kontenera takie kontenerem nieograniczone (jeden z co najmniej 1 000 RU/s przepustowości i klucz partycji), należy użyć [narzędzia migracji danych](import-data.md) lub [zmiany źródła danych biblioteki](change-feed.md) do Przeprowadź migrację zmiany. 

## <a name="partitioning-and-provisioned-throughput"></a>Partycjonowania i zainicjowaną przepływności
Azure DB rozwiązania Cosmos zaprojektowano pod kątem przewidywalnej wydajności. Po utworzeniu kontenera zarezerwować przepływność w zakresie  *[jednostek żądania](request-units.md) (RU) na sekundę*. Każde żądanie przypisano dodatkowego RU, która jest proporcjonalny do liczby zasobów systemowych, takich jak procesor CPU, pamięci i wykorzystanych w ramach operacji We/Wy. Odczyt dokumentu 1 KB z spójność sesji zużywa 1 RU. Odczytu wynosi 1 RU niezależnie od liczby elementów przechowywane i liczbę jednoczesnych żądań, w którym są uruchomione w tym samym czasie. Większe elementy wymagają wyższych RUs zależnie od rozmiaru. Jeśli wiesz, rozmiar jednostki i liczba odczytów potrzebnych do obsługi aplikacji, można udostępnić dokładną ilość wymaganych przez aplikację do odczytu wymaga przepływności. 

> [!NOTE]
> Uzyskanie pełnej przepływności kontenera, musisz wybrać klucza partycji, który umożliwia równomierne rozkładanie żądań między niektórych wartości klucza partycji distinct.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Praca z Azure rozwiązania Cosmos DB API
Portalu Azure lub interfejsu wiersza polecenia Azure umożliwia tworzenie kontenerów i skalować je w dowolnym momencie. W tej sekcji przedstawiono sposób tworzenia kontenery i określ definicję klucza przepływności i partycji w każdej z obsługiwanych interfejsów API.

### <a name="azure-cosmos-db-api"></a>Interfejs API Azure rozwiązania Cosmos bazy danych
Poniższy przykład przedstawia sposób tworzenia kontenera (kolekcja) przy użyciu interfejsu API Azure rozwiązania Cosmos bazy danych. 

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

Można odczytać elementu (dokument) przy użyciu `GET` metoda w interfejsie API REST lub przy użyciu `ReadDocumentAsync` w jednym z zestawów SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>Interfejs API usługi MongoDB
Przy użyciu interfejsu API bazy danych MongoDB można utworzyć kolekcję podzielonej przez ulubionego narzędzia, sterownika lub zestawu SDK. W tym przykładzie używamy powłoki Mongo w celu utworzenia kolekcji.

W powłoki Mongo:

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

Aby utworzyć tabelę przy użyciu interfejsu API Azure rozwiązania Cosmos DB tabeli, należy użyć metody CreateIfNotExists. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Przepływność jest ustawiony jako argument CreateIfNotExists.

Klucz partycji niejawnie zostanie utworzona jako `PartitionKey` wartość. 

Pojedynczy element można pobrać za pomocą następującego fragmentu kodu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Aby uzyskać więcej informacji, zobacz [opracowanie przy użyciu interfejsu API tabeli](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>Interfejs API programu Graph

Z interfejsu API programu Graph muszą używać portalu Azure lub interfejsu wiersza polecenia Azure, można utworzyć kontenerów. Alternatywnie ponieważ bazy danych Azure rozwiązania Cosmos jest multimodel, służy jednego z innych modeli można tworzyć i skalować kontenera programu graph.

Za pomocą klucza partycji i identyfikator w Gremlin można odczytać wierzchołków ani krawędzi. Na przykład na wykresie region ("USA") jako klucza partycji i "Seattle" jako klucza wiersza, można znaleźć wierzchołka przy użyciu następującej składni:

```
g.V(['USA', 'Seattle'])
```

Krawędź można odwoływać się przy użyciu klucza partycji i klucz wiersza.

```
g.E(['USA', 'I5'])
```

Aby uzyskać więcej informacji, zobacz [Gremlin obsługę bazy danych Azure rozwiązania Cosmos](gremlin-support.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Projekt do podziału
Skalować skutecznie bazy danych rozwiązania Cosmos platformy Azure, musisz wybierz stanowi dobry klucz partycji podczas tworzenia Twojej kontenera. Istnieją dwa główne zagadnienia dotyczące wybierania klucza partycji:

* **Granic zapytania i transakcji**. Wybór klucza partycji powinien równoważenie trzeba włączyć transakcje wymaganie rozpowszechniają jednostek wielu kluczy partycji, aby upewnić się, skalowalna. W jednym extreme można ustawić ten sam klucz partycji dla wszystkich elementów, ale ta opcja może ograniczać skalowalność rozwiązania. Y. można przypisać klucza partycji unikatowy dla każdego elementu. Ten wybór jest wysoce skalowalna, ale uniemożliwia używanie transakcji wielu dokumentów za pomocą procedur składowanych i wyzwalaczy. Klucz partycji idealne pozwala na użycie wydajność zapytań i jest wystarczające, aby upewnić się, że rozwiązanie jest skalowalna. 
* **Nie magazynu i wydajności wąskich gardeł**. Należy wybrać właściwość, która umożliwia zapis do być rozproszone na różnych różne wartości. Żądania do tego samego klucza partycji nie może przekraczać przepływność jednej partycji, są ograniczane. Dlatego jest ważne pobrać klucz partycji, który nie powoduje "punkty aktywne" w aplikacji. Ponieważ wszystkie dane dla klucza jednej partycji musi być przechowywany w partycji, należy unikać klucze partycji o dużych ilości danych dla tej samej wartości. 

Oto kilka rzeczywistych scenariuszy i klucze partycji dla każdego:
* W przypadku wdrażania zaplecze profilu użytkownika, identyfikator użytkownika jest dobrym rozwiązaniem w przypadku klucza partycji.
* Jeśli są przechowywane dane IoT, na przykład stanu urządzenia, identyfikator urządzenia jest dobrym rozwiązaniem w przypadku klucza partycji.
* Jeśli używasz bazy danych Azure rozwiązania Cosmos rejestrowanie dane szeregów czasowych, identyfikator hosta lub procesu jest dobrym rozwiązaniem w przypadku klucza partycji.
* Jeśli masz architektura wielodostępnej, identyfikator dzierżawy jest dobrym rozwiązaniem w przypadku klucza partycji.

W niektórych zastosowań, takich jak profile IoT i użytkownika, klucz partycji może być taki sam jak identyfikator (klucz dokumentu). W innych osób takich jak dane szeregów czasowych może być kluczem partycji, który różni się od identyfikatora.

### <a name="partitioning-and-loggingtime-series-data"></a>Partycjonowania i rejestrowanie szeregi danych
Jeden z typowe przypadki użycia w usłudze Azure DB rozwiązania Cosmos jest rejestrowanie i telemetrii. Należy wybrać stanowi dobry klucz partycji, ponieważ może być konieczne odczytu/zapisu ogromnych ilości danych. Wybór zależy od ceny odczytu i zapisu oraz rodzaje zapytań, które chcesz uruchomić. Poniżej przedstawiono kilka wskazówek na temat wybierania stanowi dobry klucz partycji:

* Jeśli Twoje przypadek użycia obejmuje mała liczba operacji zapisu, które gromadzą się przez długi czas i uzyskiwanie przez zakresy sygnatury czasowe i inne filtry, użyj pakiet zbiorczy sygnaturę czasową. Na przykład dobrym rozwiązaniem jest użycie daty jako klucza partycji. Z tej metody można zapytanie dotyczące wszystkich danych daty z jednej partycji. 
* Jeżeli obciążenie jest zapisywany ciężki, który jest bardziej popularne, należy użyć klucza partycji, który nie jest oparty na sygnaturę czasową. Z tej metody bazy danych Azure rozwiązania Cosmos można rozpowszechniają zapisy równomiernie różnych partycji. W tym miejscu nazwę hosta, identyfikator procesu, identyfikator działania lub inna właściwość o dużej kardynalności jest dobrym rozwiązaniem. 
* Innym rozwiązaniem jest hybrydowego co gdzie ma wiele kontenerów, po jednym dla każdego dnia miesięcznie, a klucz partycji to szczegółowe właściwości, takie jak nazwa hosta. Takie podejście charakteryzuje się korzyści, które można ustawić różne przepływności oparte na przedział czasu. Na przykład kontener dla bieżącego miesiąca jest udostępniane z wyższej przepustowości, ponieważ służy odczytuje i zapisuje. Ostatnich miesięcy są udostępniane z niższych przepływności ponieważ służą one tylko operacji odczytu.

### <a name="partitioning-and-multitenancy"></a>Partycjonowanie i wielodostępności
W przypadku wdrażania wielodostępnej aplikacji przy użyciu bazy danych Azure rozwiązania Cosmos, istnieją dwa popularne wzorce: Klucz partycji co dla każdej dzierżawy i jeden kontener dla każdego dzierżawcy. Poniżej przedstawiono zalet i wad dla każdego:

* **Klucz partycji co dla każdego dzierżawcy**. W tym modelu dzierżaw są zawsze umieszczane w jeden kontener. Ale zapytań i wstawiania dla elementów w ramach pojedynczej dzierżawy można wykonywać na jednej partycji. Można też wdrożyć logiki transakcyjnej dla wszystkich elementów w ramach dzierżawy. Ponieważ wiele dzierżaw udział kontener, można zmniejszyć koszty magazynu i wydajności puli zasobów dla dzierżawcy w jeden kontener zamiast inicjowania obsługi administracyjnej dodatkowych wysokość dla każdego dzierżawcy. Wadą jest to, że nie masz izolację wydajności dla każdego dzierżawcy. Zwiększa wydajność/przepływności dotyczą całego kontenera i zwiększa docelowej dla dzierżawców.
* **Jeden kontener dla każdego dzierżawcy**. W tym modelu każdy dzierżawca ma własnych kontenera, i może zarezerwować wydajności dla każdego dzierżawcy. Azure DB rozwiązania Cosmos nowych inicjowania obsługi administracyjnej cennika ten model jest bardziej ekonomiczne rozwiązanie dla wielodostępnych aplikacji z kilku dzierżawcami.

Można również użyć kombinacji/warstwowego podejścia, umożliwiający collocates małych dzierżawcy i przeprowadzanie migracji większych dzierżawcy do ich własnych kontenera.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule podaliśmy omówienie pojęć i najlepsze rozwiązania dla partycjonowania z jakiegokolwiek interfejsu API Azure rozwiązania Cosmos bazy danych. 

* Dowiedz się więcej o [udostępnionej przepływności w usłudze Azure DB rozwiązania Cosmos](request-units.md).
* Dowiedz się więcej o [globalne dystrybucji w usłudze Azure DB rozwiązania Cosmos](distribute-data-globally.md).



