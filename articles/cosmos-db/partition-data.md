---
title: Partycjonowanie i skalowanie w poziomie w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat działa jak partycjonowania bazy danych rozwiązania Cosmos Azure, jak skonfigurować partycje i kluczy partycji i Wybieranie klucza prawego partycji aplikacji.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d083181b379301ae80e6577ccc3ac8f142767db3
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261087"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partycja i skali w usłudze Azure DB rozwiązania Cosmos

[Azure DB rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/) jest usługą bazy danych globalnie rozproszone i wiele modeli pomaga uzyskać szybkie, przewidywalną wydajność. Go skaluje bezproblemowo wraz z aplikacji jako ich przyrostu. Ten artykuł zawiera omówienie sposobu modele partycjonowania działa w przypadku wszystkich danych w usłudze Azure DB rozwiązania Cosmos. Opisuje również, jak można skonfigurować bazy danych Azure rozwiązania Cosmos kontenery skutecznie skalowania aplikacji.

Partycjonowanie i kluczy partycji zostały omówione w tym wideo:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Podział na partycje w Azure rozwiązania Cosmos bazy danych
W usłudze Azure DB rozwiązania Cosmos można przechowywać i zapytania na danych bez schematu z pojedynczą cyfrą milisekundy opóźnienia w dowolnej skali. Azure DB rozwiązania Cosmos zawiera kontenery do przechowywania danych o nazwie *kolekcje* (w przypadku dokumentów) *wykresy*, lub *tabel*. 

Kontenery są zasoby logiczne i może obejmować co najmniej jednej partycji fizycznej lub serwerów. Liczba partycji zależy od bazy danych rozwiązania Cosmos Azure na podstawie rozmiaru magazynu i zainicjowanej kontener lub zestaw kontenerów przepływności. 

A *fizycznych* partycji jest stałą zastrzeżone magazynu kopii SSD połączone z zmiennej ilością zasobów obliczeniowych (Procesora i pamięci). Każda partycja fizycznych są replikowane wysokiej dostępności. Każdy zestaw kontenerów może udostępnić co najmniej jednej partycji fizycznej. Zarządzanie partycji fizycznej pełni zarządza bazy danych Azure rozwiązania Cosmos i nie trzeba pisania złożonego kodu lub Zarządzanie partycjami. Azure kontenery DB rozwiązania Cosmos jest nieograniczony pod względem pamięci masowej i przepływność. 

A *logicznej* jest partycją w partycji fizycznej, która przechowuje wszystkie dane skojarzone z jedną partycją wartości klucza. Wiele partycji logicznej może zakończyć w tej samej partycji fizycznej. Na poniższym diagramie jeden kontener ma trzy partycje logiczne. Każdej partycji logicznej przechowuje dane dla jednego klucza partycji, LAX AMS i MEL odpowiednio. Nie można każdej partycji logicznej LAX AMS i MEL rosnąć przekroczenie limitu maksymalnej partycji logicznej wynosi 10 GB. 

![Partycjonowanie zasobów](./media/introduction/azure-cosmos-db-partitioning.png) 

Gdy kontener spełnia [partycjonowania wymagania wstępne](#prerequisites), Partycjonowanie jest całkowicie niewidoczne dla aplikacji. Azure DB rozwiązania Cosmos obsługuje szybkie odczyty i zapisy, zapytania logiki transakcyjnej, poziomy spójności i szczegółowej kontroli dostępu za pomocą metody/interfejsów API do zasobu jeden kontener. Usługa obsługuje przesyłanie danych między fizycznymi i logicznymi partycjami i routingu żądań zapytania do prawej partycji. 

## <a name="how-does-partitioning-work"></a>Jak działa partycjonowania

Jak działa partycjonowania? Każdy element musi mieć *klucza partycji* i *klucz wiersza*, które jednoznacznie zidentyfikować. Klucz partycji działa jako partycji logicznej danych i zapewnia bazy danych Azure rozwiązania Cosmos z granicą fizycznych dystrybucję danych partycji fizycznej. Dane dla jednej partycji logicznej musi znajdować się wewnątrz jednej partycji fizycznej, ale zarządza zarządzania partycji fizycznej bazy danych Azure rozwiązania Cosmos. 

Krótko mówiąc Oto jak partycjonowania działa w usłudze Azure DB rozwiązania Cosmos:

* Udostępnić zbiór kontenerów bazy danych Azure rozwiązania Cosmos **T** przepływności RU/s (liczba żądań na sekundę).
* W tle bazy danych rozwiązania Cosmos Azure udostępnia partycji fizycznej potrzebne do obsługi **T** żądań na sekundę. Jeśli **T** jest większa niż maksymalna przepustowość dla każdej partycji fizycznej **t**, następnie bazy danych Azure rozwiązania Cosmos przepisy **N = T/t** partycji fizycznej. Wartość maksymalna przepustowość na partition(t) jest konfigurowana przy bazy danych Azure rozwiązania Cosmos, ta wartość jest przypisywana na podstawie całkowitej udostępnionej przepływności i konfiguracji sprzętu, używane. 
* Azure DB rozwiązania Cosmos klawiszy skrótów obszaru klucza partycji są przydziela równomiernie w poprzek **N** partycji fizycznej. To liczba partycji logicznej każdego hosty fizyczne partycji jest **1/N** * liczba wartości kluczy partycji.
* Gdy partycji fizycznej **p** osiągnie limit magazynu bazy danych rozwiązania Cosmos Azure bezproblemowo dzieli **p** do dwóch nowych partycji fizycznej **p1** i **p2**. Rozpowszechnia wartości odpowiadających około połowy klucze do wszystkich nowych partycji fizycznej. Podziel operacji jest całkowicie niewidoczna dla aplikacji. Jeśli wszystkie dane w partycji fizycznej należy do tego samego klucza partycji logicznej partycji fizycznej osiągnie limit magazynu, operację podziału nie występuje. Jest to spowodowane wszystkie dane dla klucza jednej partycji logicznej musi znajdować się w tej samej partycji fizycznej. W takim przypadku można zastosować klucza strategii partycjonowania.
* Podczas obsługi administracyjnej przepływności wyższy niż **t * N**, bazy danych Azure rozwiązania Cosmos dzieli przynajmniej jednej partycji fizycznej do obsługi wyższej przepustowości.

Semantyka kluczy partycji są nieco inne odpowiadające semantykę każdego API, jak pokazano w poniższej tabeli:

| Interfejs API | Klucz partycji | Klucz wiersza |
| --- | --- | --- |
| SQL | Ścieżka do klucza partycji niestandardowych | Naprawiono polecenie `id` | 
| MongoDB | klucz w niezależnych niestandardowych  | Naprawiono polecenie `_id` | 
| Gremlin | Właściwość klucza partycji niestandardowych | Naprawiono polecenie `id` | 
| Tabela | Naprawiono polecenie `PartitionKey` | Naprawiono polecenie `RowKey` | 

Azure DB rozwiązania Cosmos używa skrótu na podstawie partycjonowania. Podczas zapisywania elementu bazy danych Azure rozwiązania Cosmos skróty wartość klucza partycji i używa skrótu wynik w celu określenia, które partycji do przechowywania elementu w. Azure DB rozwiązania Cosmos przechowuje wszystkie elementy z tym samym kluczem partycji w tej samej partycji fizycznej. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Najlepsze rozwiązania w przypadku wybrania klucza partycji

Wybór klucza partycji jest ważnych decyzji, które należy podjąć w czasie projektowania. Wybierz nazwę właściwości, który ma szeroki zakres wartości i ma nawet wzorce dostępu. Jest najlepszym rozwiązaniem jest klucz partycji z dużą liczbą unikatowe wartości (np., setek lub tysięcy). Umożliwia równomierne rozłożenie obciążenia na tych wartości. Klucz partycji idealna to taki, który pojawia się często jako filtr zapytania i jest wystarczające, aby upewnić się, że rozwiązanie jest skalowalna.

Jeśli partycji fizycznej osiągnie limit magazynu i danych na partycji ma ten sam klucz partycji, zwraca bazy danych Azure rozwiązania Cosmos *"klucza partycji Osiągnięto maksymalny rozmiar 10 GB"* komunikat i partycja nie jest podzielona. Wybór stanowi dobry klucz partycji jest bardzo ważne. Partycje fizycznych są koncepcji wewnętrznej bazy danych Azure rozwiązania Cosmos i przejściowych. Azure DB rozwiązania Cosmos jest automatycznie skalowany liczby partycji fizycznej oparte na obciążenie. Dlatego nie należy corelate projektu bazy danych na podstawie liczby partycji fizycznej zamiast tego należy upewnić się, że wybierz klucz partycji w prawo (partycji logicznej). 

Wybierz klucz partycji tak, aby:

* Dystrybucja magazynu jest nawet przez wszystkich kluczy.
* Rozkład woluminu żądań w danym punkcie w czasie jest nawet przez wszystkich kluczy.
* Zapytania, które są wywoływane z wysoką współbieżności może wydajnie kierowane przez dołączenie klucza partycji do predykatu filtru.  
* Wybieranie klucza partycji o wyższej wartości cardinality jest zazwyczaj preferowana — becaue go zwykle zapewnia większą dystrybucji i skalowalność. Na przykład klucz złożony może zostać utworzona przez łączenie wartości z wiele właściwości w celu zwiększenia kardynalność. 

Gdy wybierz klucz partycji z powyżej zagadnienia, nie trzeba martwić się o numer partycji lub jaka przepustowość jest przydzielona dla każdej partycji fizycznej, zgodnie z bazy danych Azure rozwiązania Cosmos skaluje limit liczby partycji fizycznej i można również skalować pojedynczych partycji zgodnie z potrzebami.

Kontenery DB rozwiązania Cosmos Azure mogą być tworzone jako *stałej* lub *nieograniczone* w portalu Azure. Kontenery o stałym rozmiarze są ograniczone do 10 GB, a ich maksymalna przepływność wynosi 10 000 jednostek żądań na sekundę. Aby utworzyć kontener jako nieograniczone, należy określić klucz partycji i minimalnej przepustowości 1 000 RU/s. Kontenery DB rozwiązania Cosmos Azure można również konfigurować udostępnianie przepustowość między zestaw kontenery, w których każdego kontenera należy zdefiniuj partycji klucza i może zwiększyć nieograniczone.

Jest dobrym rozwiązaniem, aby sprawdzić, jak dane będzie rozmieszczona na partycje. Aby sprawdzić rozkład danych w portalu, przejdź do swojego konta bazy danych rozwiązania Cosmos Azure i kliknij **metryki** w **monitorowanie** sekcji, a następnie kliknij polecenie **magazynu** kartę, aby zobaczyć sposób sieci dane na partycje w różnych fizycznych partycji.

![Partycjonowanie zasobów](./media/partition-data/partitionkey-example.png)

Po lewej stronie powyższy obraz pokazuje wynik klucza partycji zły i prawej powyższy obraz pokazuje wyniku, gdy wybrano stanowi dobry klucz partycji. W obrazie po lewej stronie widać, że dane nie jest równomiernie rozłożony między partycji. Należy starań wybrać klucz partycji, który rozprowadza danych, więc podobny do prawego obrazu.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Wymagania wstępne dotyczące podziału na partycje

W przypadku partycji fizycznej do Podziel automatycznie na **p1** i **p2** zgodnie z opisem w [jak działa partycjonowania](#how-does-partitioning-work), należy utworzyć kontener o przepustowości 1 000 RU/s lub więcej (lub udziału przepływności zestawu kontenerów), i musi zostać dostarczona klucza partycji. Podczas tworzenia kontenera (np. kolekcji, wykres i tabelę) w portalu Azure, wybierz **nieograniczone** opcji pojemności magazynu przeprowadzać nieograniczone skalowania. 

Jeśli utworzono kontener w portalu Azure lub programistycznie, przepływności początkowej został 1 000 RU/s lub więcej, a podano klucz partycji, możesz skorzystać z nieograniczone skalowanie bez zmian, z kontenerem. Obejmuje to **stałe** kontenerów, ile początkowej kontener został utworzony z co najmniej 1 000 RU/s przepustowości i określono klucz partycji.

Wszystkie kontenery skonfigurowany tak, aby udostępnić przepływności jako część zestawu kontenery są traktowane jako **nieograniczone** kontenerów.

Jeśli utworzono **stałe** kontener z bez klucza partycji lub przepływności mniej niż 1 000 RU/s, będzie kontenera nie automatycznego skalowania zgodnie z opisem w tym artykule. Aby przeprowadzić migrację danych z kontenera stałym nieograniczone kontenera (na przykład jeden z co najmniej 1 000 RU/s i klucz partycji), należy użyć [narzędzia migracji danych](import-data.md) lub [zmiany źródła danych biblioteki](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Partycjonowania i zainicjowaną przepływności
Azure DB rozwiązania Cosmos zaprojektowano pod kątem przewidywalnej wydajności. Po utworzeniu kontenera lub zbiór kontenery zarezerwować przepływność w zakresie  *[jednostek żądania](request-units.md) (RU) na sekundę*. Każde żądanie sprawia, że dodatkowy RU, który jest proporcjonalny do liczby zasobów systemowych, takich jak procesor CPU, pamięci i wykorzystanych w ramach operacji We/Wy. Odczyt dokumentu 1 KB z spójność sesji zużywa 1 RU. Odczytu wynosi 1 RU niezależnie od liczby elementów przechowywane i liczbę jednoczesnych żądań, w którym są uruchomione w tym samym czasie. Większe elementy wymagają wyższych RUs zależnie od rozmiaru. Jeśli wiesz, rozmiar jednostki i liczba odczytów potrzebnych do obsługi aplikacji, można udostępnić dokładną ilość przepływności wymaganej na potrzeby aplikacji. 

> [!NOTE]
> Aby w pełni wykorzystać zainicjowanej kontener lub zestaw kontenerów przepływności, musisz wybrać klucza partycji, która umożliwia równomierne rozkładanie żądania między wszystkie wartości klucza partycji distinct.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Praca z Azure rozwiązania Cosmos DB API
Portalu Azure lub interfejsu wiersza polecenia Azure umożliwia tworzenie kontenerów i skalować je w dowolnym momencie. W tej sekcji przedstawiono sposób tworzenia kontenery i określ klucz zainicjowaną przepływności i partycji przy użyciu każdego interfejsu API.


### <a name="sql-api"></a>Interfejs API SQL
Poniższy przykład przedstawia sposób tworzenia kontenera (kolekcja) przy użyciu interfejsu API SQL. 

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

Można znaleźć elementu (dokument) przy użyciu `GET` metody interfejsu API REST lub przy użyciu `ReadDocumentAsync` w jednym z zestawów SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Aby uzyskać więcej informacji, zobacz [partycjonowania w usłudze Azure DB rozwiązania Cosmos przy użyciu interfejsu API SQL](sql-api-partition-data.md).

### <a name="mongodb-api"></a>Interfejs API usługi MongoDB
Przy użyciu interfejsu API bazy danych MongoDB można utworzyć kolekcję podzielonej przez ulubionego narzędzia, sterownika lub zestawu SDK. W tym przykładzie używamy powłoki Mongo utworzyć kolekcję.

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

Aby utworzyć tabelę przy użyciu interfejsu API tabeli, należy użyć `CreateIfNotExists` metody. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Przepływność jest ustawiony jako argument `CreateIfNotExists`. Klucz partycji niejawnie zostanie utworzona jako `PartitionKey` wartość. 

Pojedynczy element można pobrać za pomocą następującego kodu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Aby uzyskać więcej informacji, zobacz [opracowanie przy użyciu interfejsu API tabeli](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Interfejs API języka Gremlin

Przy użyciu interfejsu API Gremlin można użyć portalu Azure lub interfejsu wiersza polecenia Azure utworzyć kontener, który reprezentuje wykres. Alternatywnie ponieważ bazy danych Azure rozwiązania Cosmos jest wiele modeli, można jednego z innych interfejsów API tworzyć i skalować kontenera programu graph.

Za pomocą klucza partycji i identyfikator w Gremlin można odczytać wierzchołków ani krawędzi. Na przykład na wykresie region ("USA") jako klucza partycji i "Seattle" jako klucza wiersza, można znaleźć wierzchołka przy użyciu następującej składni:

```
g.V(['USA', 'Seattle'])
```

Krawędź można odwoływać się przy użyciu klucza partycji i klucz wiersza.

```
g.E(['USA', 'I5'])
```

Aby uzyskać więcej informacji, zobacz [przy użyciu wykresu podzielonym na partycje w usłudze Azure DB rozwiązania Cosmos](graph-partitioning.md).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Projektowanie pod kątem skalowania
Skalować skutecznie bazy danych rozwiązania Cosmos platformy Azure, musisz wybierz stanowi dobry klucz partycji podczas tworzenia Twojej kontenera. Istnieją dwa główne zagadnienia dotyczące wybierania stanowi dobry klucz partycji:

* **Zapytania granic i transakcje**. Wybór klucza partycji powinien równoważenie konieczność użycia transakcji z wymaganiem, aby rozpowszechniają jednostek wielu kluczy partycji, aby upewnić się, skalowalna. W jednym extreme można ustawić ten sam klucz partycji dla wszystkich elementów, ale ta opcja może ograniczać skalowalność rozwiązania. Y. można przypisać klucza partycji unikatowy dla każdego elementu. Ten wybór jest wysoce skalowalna, ale uniemożliwia używanie transakcji wielu dokumentów za pomocą procedur składowanych i wyzwalaczy. Klucz partycji idealne pozwala na użycie wydajność zapytań i jest wystarczające, aby upewnić się, że rozwiązanie jest skalowalna. 
* **Nie magazynu i wydajności wąskich gardeł**. Należy wybrać właściwość, która umożliwia zapis do być rozproszone na różnych różne wartości. Żądania do tego samego klucza partycji nie może przekraczać udostępnionej przepływności przydzielane do partycji i zostaną ograniczone szybkości. Dlatego jest ważne pobrać klucz partycji, który nie powoduje "punkty aktywne" w aplikacji. Ponieważ wszystkie dane dla klucza jednej partycji musi być przechowywany w partycji, należy unikać klucze partycji o dużych ilości danych dla tej samej wartości. 

Oto kilka rzeczywistych scenariuszy i klucze partycji dla każdego:
* W przypadku wdrażania profilu użytkownika wewnętrznej bazie danych, *identyfikator użytkownika* jest dobrym rozwiązaniem dla klucza partycji.
* Jeśli są przechowywane dane IoT, na przykład stan urządzenia *identyfikator urządzenia* jest dobrym rozwiązaniem dla klucza partycji.
* Jeśli używasz bazy danych Azure rozwiązania Cosmos rejestrowanie dane szeregów czasowych, *hostname* lub *identyfikator procesu* jest dobrym rozwiązaniem dla klucza partycji.
* Jeśli masz architektura wielodostępnej, *Identyfikatorem dzierżawy* jest dobrym rozwiązaniem dla klucza partycji.

W niektórych zastosowań, takich jak profile IoT i użytkownika, klucz partycji może być identyczny z *identyfikator* (klucz dokumentu). W innych osób, takich jak dane szeregów czasowych, może być kluczem partycji, który różni się od *identyfikator*.

### <a name="partitioning-and-loggingtime-series-data"></a>Partycjonowania i rejestrowanie szeregi danych
Jednym z typowe przypadki użycia w usłudze Azure DB rozwiązania Cosmos jest rejestrowanie i telemetrii. Ważne jest pobrania stanowi dobry klucz partycji w tym scenariuszu, ponieważ może być konieczne odczytu/zapisu ogromnych ilości danych. Wybór dla klucza partycji zależy od ceny odczytu i zapisu oraz rodzaje zapytań, które chcesz uruchomić. Poniżej przedstawiono kilka wskazówek na temat wybierania stanowi dobry klucz partycji:

* Jeśli Twoje przypadek użycia obejmuje mała liczba operacji zapisu, które gromadzą się przez długi czas i uzyskiwanie przez zakresy sygnatury czasowe z innymi filtrami, użyj pakiet zbiorczy sygnatury czasowej. Na przykład dobrym rozwiązaniem jest użycie daty jako klucza partycji. Z tej metody można zapytanie dotyczące wszystkich danych dla określonej daty z jednej partycji. 
* Jeśli obciążenie intensywnie zapisu, która jest bardzo często używane w tym scenariuszu, Użyj klucza partycji, który nie jest oparty na znacznik czasu. Tak, rozpowszechniać i skalować zapisy równomiernie bazy danych Azure rozwiązania Cosmos w różnych partycji. W tym miejscu *hostname*, *identyfikator procesu*, *identyfikator działania*, lub inna właściwość o dużej kardynalności jest dobrym rozwiązaniem. 
* Innym rozwiązaniem jest rozwiązanie hybrydowe, gdzie masz wiele kontenerów, po jednym dla każdego dnia miesięcznie, a klucz partycji to bardziej szczegółowego właściwości, takie jak *hostname*. Takie podejście charakteryzuje się korzyści, które można ustawić różne przepływności dla każdego kontenera lub grupy kontenerów na podstawie potrzeb skalowalność i wydajność i przedział czasu. Na przykład kontener dla bieżącego miesiąca może zostać zainicjowana obsługa wyższej przepustowości, ponieważ służy odczytuje i zapisuje. Ostatnich miesięcy może być przygotowana o niższych przepustowości, ponieważ służą one tylko operacji odczytu.

### <a name="partitioning-and-multitenancy"></a>Partycjonowanie i wielodostępności
W przypadku wdrażania aplikacji wielodostępnym, przy użyciu bazy danych Azure rozwiązania Cosmos istnieją dwa popularne projektów wziąć pod uwagę: *klucza partycji co dla każdego dzierżawcy* i *jeden kontener dla każdego dzierżawcy*. Poniżej przedstawiono zalet i wad dla każdego:

* **Klucz partycji co dla każdego dzierżawcy**. W tym modelu dzierżaw są wspólnie przechowywane w jeden kontener. Można wykonywać zapytań i wstawiania do pojedynczej dzierżawy na jednej partycji. Można też wdrożyć logiki transakcyjnej dla wszystkich elementów należących do dzierżawy. Ponieważ wiele dzierżaw udział kontener, magazynowania i udostępnionej przepływności mogą lepsze wykorzystanie puli zasobów dla wszystkich dzierżawców w jeden kontener zamiast inicjowania obsługi administracyjnej dla każdego dzierżawcy. Wadą jest to, że nie masz izolację wydajności dla każdego dzierżawcy. Zwiększa przepływność, aby zagwarantować wydajność będą stosowane do całego kontenera z wszystkich dzierżawcami i zwiększa docelowej dla pojedynczego dzierżawcy.
* **Jeden kontener dla każdego dzierżawcy**. W tym modelu każdy dzierżawca ma własnych kontenera, i może zarezerwować przepływności z gwarantowaną wydajności dla każdego dzierżawcy. Ten model jest bardziej ekonomiczne rozwiązanie dla wielodostępnych aplikacji z kilku dzierżawcami.

Można również użyć metody hybrydowych, razem colocates małych dzierżawców i izoluje większych dzierżawcy do ich własnych kontenerów.

## <a name="next-steps"></a>Następne kroki
W tym artykule podaliśmy omówienie pojęć i najlepsze rozwiązania dla skalowanie i partycjonowania w usłudze Azure DB rozwiązania Cosmos. 

* Dowiedz się więcej o [udostępnionej przepływności w usłudze Azure DB rozwiązania Cosmos](request-units.md).
* Dowiedz się więcej o [globalne dystrybucji w usłudze Azure DB rozwiązania Cosmos](distribute-data-globally.md).



