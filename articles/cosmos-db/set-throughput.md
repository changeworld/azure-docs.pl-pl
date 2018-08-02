---
title: Aprowizowanie przepływności usługi Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić aprowizowanej przepływności containsers usługi Azure Cosmos DB, kolekcje, wykresów i tabel.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: 5f022f366c0247fade4cc39925e116a09b3d08de
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399094"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Ustaw i uzyskiwanie informacji o przepływności kontenerów usługi Azure Cosmos DB i bazy danych

Możesz ustawić przepływności kontenera usługi Azure Cosmos DB lub zestaw kontenerów przy użyciu witryny Azure portal lub za pomocą zestawów SDK klienta. 

**Aprowizowanie przepływności dla poszczególnych kontenera:** podczas aprowizowania przepływności zestaw kontenerów tych kontenerów udostępnianie aprowizowanej przepływności. Przepływność inicjowania obsługi administracyjnej dla poszczególnych kontenerów gwarantuje rezerwację przepustowości dla tego określonego kontenera. Podczas przypisywania jednostek żądań na sekundę na poziomie poszczególnych kontenerów, kontenery mogą być tworzone jako *stałej* lub *nieograniczone*. Kontenery o stałym rozmiarze są ograniczone do 10 GB, a ich maksymalna przepływność wynosi 10 000 jednostek żądań na sekundę. Aby utworzyć nieograniczonego kontenera, musisz określić minimalną przepustowość 1000 jednostek RU/s i [klucza partycji](partition-data.md). Ponieważ Twoje dane mogą mieć ma być podzielony na wiele partycji, jest konieczne pobranie klucza partycji, która ma wysoką Kardynalność (od 100 do milionów wartości odrębnych). Wybierając klucza partycji przy użyciu wielu różnych wartości, można zapewnić, że żądań i kontener/tabeli/wykresu może być skalowana w jednolity sposób przez usługę Azure Cosmos DB. 

**Aprowizowanie przepływności dla zestawu, kontenerów lub bazy danych:** Aprowizowanie przepływności bazy danych umożliwia udostępnianie informacji o przepływności między wszystkie kontenery, które należą do tej bazy danych. W bazie danych Azure Cosmos DB może mieć zestaw kontenerów, który udostępnia przepływności, a także kontenery, które są wyposażone w dedykowane przepływności. Podczas przypisywania jednostek żądań na sekundę w zestawie kontenerów, kontenery należące do tego zestawu są traktowane jako *nieograniczone* kontenerów i muszą określać klucz partycji.

Na podstawie aprowizowanej przepływności, usługa Azure Cosmos DB przyzna partycje fizyczne do hostowania kontenerów i dzieli dane/rebalances danych między partycjami, zgodnie z ich przyrostu. Kontener i aprowizacji poziomu przepływności bazy danych to osobne oferty i przełączania się między jedną z tych wersji wymagają migracji danych ze źródła do miejsca docelowego. Oznacza to, należy utworzyć nową bazę danych lub nową kolekcję, a następnie przeprowadzić migrację danych za pomocą [biblioteki wykonawca zbiorcze](bulk-executor-overview.md) lub [usługi Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Na poniższym obrazie przedstawiono aprowizowania przepływności na różnych poziomach:

![Aprowizacja jednostek żądania dla poszczególnych kontenerów i zestaw kontenerów](./media/request-units/provisioning_set_containers.png)

W kolejnych sekcjach dowiesz się, kroki wymagane do skonfigurowania przepływności na różnych poziomach dla konta usługi Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Aprowizowanie przepływności za pomocą witryny Azure portal

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Aprowizowanie przepływności dla kontenera (tabeli bezużytecznych/graph)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W lewym okienku nawigacji wybierz **wszystkie zasoby** i Znajdź swoje konto usługi Azure Cosmos DB.  
3. Przepływności można skonfigurować podczas tworzenia kontenera (kolekcja, wykres, tabela) lub przepływność aktualizacji dla istniejącego kontenera.  
4. Aby przypisać przepływności podczas tworzenia kontenera, otwórz **Eksplorator danych** bloku, a następnie wybierz pozycję **Nowa kolekcja** (nowy wykres, nową tabelę dla innych interfejsów API)  
5. Wypełnij formularz **Dodaj kolekcję** bloku. W poniższej tabeli opisano pola, w tym bloku:  

   |**Ustawienie**  |**Opis**  |
   |---------|---------|
   |Identyfikator bazy danych  |  Podaj unikatową nazwę, aby zidentyfikować bazy danych. Baza danych jest kontenerem logicznym co najmniej jednej kolekcji. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków /, \\, #, ? ani mieć spacji na końcu. |
   |Identyfikator kolekcji  | Podaj unikatową nazwę identyfikującą kolekcji. W przypadku identyfikatorów kolekcji obowiązują takie same wymagania dotyczące znaków, jak dla nazw baz danych. |
   |Pojemność magazynu   | Ta wartość reprezentuje pojemność magazynu bazy danych. Podczas aprowizowania przepływności dla poszczególnych kolekcji, pojemności magazynu można **stała (10 GB)** lub **nieograniczone**. Nieograniczonej pojemności magazynu, należy ustawić klucz partycji dla danych.  |
   |Przepływność   | Każdej kolekcji i bazy danych może mieć przepływność w jednostkach żądania na sekundę.  Pojemność magazynu stałych minimalna przepływność to 400 jednostek żądań na sekundę (RU/s), uzyskać nieograniczony magazyn pojemności, minimalna przepływność jest ustawiona na 1000 jednostek RU/s.|

6. Po wprowadzeniu wartości dla tych pól, zaznacz **OK** Aby zapisać ustawienia.  

   ![Ustawianie przepływności dla kolekcji](./media/set-throughput/set-throughput-for-container.png)

7. Aby zaktualizować przepływność istniejący kontener, rozwiń węzeł bazy danych i kontener, a następnie kliknij przycisk **ustawienia**. W nowym oknie wpisz nową wartość przepływności, a następnie wybierz pozycję **Zapisz**.  

   ![Aktualizuj przepływność dla kolekcji](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Aprowizowanie przepływności dla zestawu, kontenerów lub na poziomie bazy danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W lewym okienku nawigacji wybierz **wszystkie zasoby** i Znajdź swoje konto usługi Azure Cosmos DB.  
3. Podczas tworzenia bazy danych lub aktualizacji przepływności istniejącej bazy danych, możesz skonfigurować przepływność.  
4. Aby przypisać przepływności podczas tworzenia bazy danych, otwórz **Eksplorator danych** bloku, a następnie wybierz pozycję **nowej bazy danych**  
5. Wypełnij **bazy danych o identyfikatorze** wartość, sprawdź **Aprowizowanie przepływności** opcji i skonfigurować wartość przepływności. Bazy danych może być obsługiwana za pomocą przepustowość minimalną wartość 50 000 jednostek RU/s.  

   ![Ustawianie przepływności za pomocą nowej opcji bazy danych](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Aby zaktualizować przepływność dla istniejącej bazy danych, rozwiń węzeł bazy danych i kontener, a następnie kliknij przycisk **skalowania**. W nowym oknie wpisz nową wartość przepływności, a następnie wybierz pozycję **Zapisz**.  

   ![Aktualizuj przepływności bazy danych](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Aprowizowanie przepływności dla zestawu kontenerów, a także pojedynczy kontener w bazie danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W lewym okienku nawigacji wybierz **wszystkie zasoby** i Znajdź swoje konto usługi Azure Cosmos DB.  
3. Tworzenie bazy danych i przypisać jej przepływności. Otwórz **Eksplorator danych** bloku, a następnie wybierz pozycję **nowej bazy danych**  
4. Wypełnij **bazy danych o identyfikatorze** wartość, sprawdź **Aprowizowanie przepływności** opcji i skonfigurować wartość przepływności. Bazy danych może być obsługiwana za pomocą przepustowość minimalną wartość 50 000 jednostek RU/s.  

   ![Ustawianie przepływności za pomocą nowej opcji bazy danych](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Następnie należy utworzyć kolekcję w bazie danych utworzonej w powyżej kroku. Aby utworzyć kolekcję, kliknij prawym przyciskiem myszy bazę danych i wybierz pozycję **Nowa kolekcja**.  

6. W **Dodaj kolekcję** bloku, wprowadź nazwę kolekcji, a klucz partycji. Opcjonalnie może aprowizować przepływność mierzoną dla tego określonego kontenera, jeśli nie chcesz przypisać wartość przepływności, przepływności przypisanych do bazy danych jest udostępniana do kolekcji.  

   ![Opcjonalnie Ustaw przepływność dla kontenera](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Uwagi dotyczące aprowizowania przepływności

Poniżej przedstawiono pewne zagadnienia, które ułatwiają decyzję w sprawie strategii rezerwacji przepustowości.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Uwagi dotyczące aprowizowania przepływności na poziomie bazy danych

Weź pod uwagę aprowizowania przepływności na poziomie bazy danych (czyli zestaw kontenerów) w następujących przypadkach:

* Jeśli masz tuzina co najmniej liczbę kontenerów, które można udostępniać przepływność w niektóre lub wszystkie z nich.  

* Podczas migracji z jedną dzierżawą bazy danych, który jest przeznaczony do uruchamiania na IaaS hostowanych maszyn wirtualnych lub lokalnie (na przykład NoSQL lub relacyjnych baz danych) do usługi Azure Cosmos DB i mieć wiele kontenerów.  

* Jeśli chcesz wziąć pod uwagę nieplanowane skoków obciążeń przy użyciu puli przepływności na poziomie bazy danych.  

* Zamiast ustawienie przepływności na pojedynczy kontener interesują Cię wprowadzenie zagregowanej przepływności w zestawie kontenerów w bazie danych.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>Uwagi dotyczące aprowizowania przepływności na poziomie kontenera

Weź pod uwagę aprowizowania przepływności pojedynczy kontener w następujących przypadkach:

* Jeśli masz mniejszą liczbę kontenerów usługi Azure Cosmos DB.  

* Jeśli chcesz pobrać gwarantowaną przepływność na dany kontener objęte umową SLA.

## <a name="throughput-ranges"></a>Zakresy przepływności

W poniższej tabeli wymieniono przepustowość dostępna dla kontenerów:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Jedna partycja kontenera</strong></p></td>
            <td valign="top"><p><strong>Kontener podzielony na partycje</strong></p></td>
            <td valign="top"><p><strong>Zestaw kontenerów</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Przepustowość minimalna</p></td>
            <td valign="top"><p>400 jednostek żądań na sekundę</p></td>
            <td valign="top"><p>1000 jednostek żądań na sekundę</p></td>
            <td valign="top"><p>50 000 jednostek żądań na sekundę</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maksymalna przepływność</p></td>
            <td valign="top"><p>10 000 jednostek żądań na sekundę</p></td>
            <td valign="top"><p>Nieograniczona liczba</p></td>
            <td valign="top"><p>Nieograniczona liczba</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Ustawianie przepływności za pomocą interfejsu SQL API dla platformy .NET

### <a name="set-throughput-at-the-container-level"></a>Ustawianie przepływności na poziomie kontenera
Poniżej przedstawiono fragment kodu do tworzenia kontenera za pomocą 3000 jednostek żądań na sekundę dla poszczególnych kontenera przy użyciu zestawu .NET SDK interfejsu API SQL:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-for-a-set-of-containers-at-the-database-level"></a>Ustawianie przepływności zestaw kontenerów na poziomie bazy danych

Poniżej przedstawiono fragment kodu dotyczący inicjowania obsługi administracyjnej 100 000 jednostek żądania na sekundę w zestawie kontenerów za pomocą zestawu .NET SDK interfejsu API SQL:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Usługa Azure Cosmos DB działa w modelu rezerwacji przepustowości. Oznacza to, że opłaty są naliczane ilości przepływności *zarezerwowanych*, niezależnie od tego, ile że przepustowość jest aktywnie *używane*. Jak aplikacja na zmiany wzorców obciążenia, danych i użycia, można łatwo skalować w górę i w dół liczbę zarezerwowane jednostki żądania za pośrednictwem zestawów SDK lub przy użyciu [witryny Azure Portal](https://portal.azure.com).

Każdy kontener lub zestaw kontenerów, są mapowane na `Offer` zasobu w usłudze Azure Cosmos DB, która ma metadane o aprowizowanej przepływności. Przydzielone przepływności można zmienić, wyszukanie odpowiednich zasobów oferty dla kontenera, a następnie aktualizowania z nową wartością przepływności. Poniżej przedstawiono fragment kodu do zmiany przepływności kontenera do 5000 jednostek żądań na sekundę przy użyciu zestawu .NET SDK. Po zmianie przepustowości, należy odświeżyć okien już istniejących Azure portalu zmienione przepływności do wyświetlenia. 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Nie ma to wpływu na dostępność kontenera lub zestaw kontenerów, po zmianie przepływność. Zazwyczaj nowe zarezerwowaną przepływnością obowiązuje w ciągu kilku sekund w aplikacji nowa przepływność.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Ustawianie przepływności za pomocą interfejsu API SQL dla języka Java

Poniższy fragment kodu pobiera bieżący przepływności i zmienia ją na 500 jednostek RU/s. Aby uzyskać kompletny przykład kodu, zobacz [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) pliku w usłudze GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Uzyskiwanie informacji o przepływności za pomocą interfejsu API usługi MongoDB metrykami w portalu

Najprostszym sposobem, aby prawidłowo oszacować żądania opłat za jednostki dla interfejsu API usługi MongoDB bazy danych jest użycie [witryny Azure portal](https://portal.azure.com) metryki. Za pomocą *liczba żądań* i *opłata za żądanie wyrażana* wykresy, możesz uzyskać szacunkową liczbę jednostek żądania, każdy zużywa operacji i liczbę jednostek żądania zużywają względem siebie nawzajem.

![Portal metryki interfejsu API usługi MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Przekraczanie limitów zarezerwowaną przepływność w interfejsie API bazy danych MongoDB
Aplikacje, które przekroczyły aprowizowaną przepływność w kontenerze lub zestaw kontenerów będzie limited współczynnik dopóki stopę zużycia spadnie poniżej stawki aprowizowanej przepływności. W przypadku ograniczenia szybkości wewnętrznej bazy danych zakończy się żądania o `16500` kod błędu: - `Too Many Requests`. Domyślnie interfejsu API usługi MongoDB automatycznie ponawia próbę maksymalnie 10 razy przed zwróceniem `Too Many Requests` kod błędu. Jeśli otrzymujesz wiele `Too Many Requests` kody błędów, warto rozważyć dodanie logiki ponawiania obsługi procedury błędów aplikacji lub [zwiększysz aprowizowaną przepływność w kontenerze](set-throughput.md).

## <a id="GetLastRequestStatistics"></a>Pobierz opłata za żądanie wyrażana za pomocą interfejsu MongoDB API GetLastRequestStatistics polecenia

Interfejs API usługi MongoDB obsługuje polecenie niestandardowe *getLastRequestStatistics*, pobierane opłaty żądania dla danej operacji.

Na przykład w powłoki Mongo, wykonaj operację, którą chcesz zweryfikować opłata za żądanie.
```
> db.sample.find()
```

Następnie wykonaj polecenie *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Jedną z metod do oszacowania ilości zarezerwowanej przepływności wymaganej przez aplikację jest do rejestrowania żądań opłat za jednostkę, związanych z uruchamianiem typowych operacji względem elementu reprezentatywny używanych przez aplikację i następnie oszacować liczbę operacje przewidywania do wykonania każdej sekundy.

> [!NOTE]
> W przypadku typów elementów, które różnią się znacząco pod względem rozmiaru i liczby właściwości indeksowanych rejestrowania odpowiednich operacji żądania opłat za jednostkę związany z każdą *typu* dla typowego elementu o wielkości.
> 
> 

## <a id="RequestchargeGraphAPI"></a>Pobierz opłata za żądanie wyrażana dla konta interfejsów API języka Gremlin 

Oto przykład o tym, jak można uzyskać za pomocą biblioteki Gremlin.Net konta interfejsów API języka Gremlin opłata za żądanie wyrażana. 

```csharp

var response = await gremlinClient.SubmitAsync<int>(requestMsg, bindings);
                var resultSet = response.AsResultSet();
                var statusAttributes= resultSet.StatusAttributes;
```

Oprócz powyższej metody umożliwia także nagłówek "x-ms łącznie — żądanie — opłata za" dla obliczeń w jednostkach żądania.


## <a name="throughput-faq"></a>Przepływność — często zadawane pytania

**Mogę ustawić Moje przepływności na mniejszy niż 400 jednostek RU/s?**

400 jednostek RU/s jest minimalna przepływność w kontenerach jednej partycji usługi Cosmos DB (1000 jednostek RU/s jest to minimum potrzebne do kontenerów podzielonym na partycje). Żądanie jednostki są ustawiane za 100 jednostek RU/s odstępach czasu, ale przepływność nie można ustawić na 100 jednostek RU/s lub wartość mniejszy niż 400 RU/s. Jeśli szukasz ekonomiczna metoda do tworzenia i testowania usługi Cosmos DB można korzystać z BEZPŁATNEJ [emulatora usługi Azure Cosmos DB](local-emulator.md), którą można wdrożyć lokalnie bez ponoszenia kosztów. 

**Jak ustawić przepływność przy użyciu interfejsu API usługi MongoDB?**

Nie ma rozszerzenia interfejsu API usługi MongoDB można ustawić przepustowości. Zaleca się korzystania z interfejsu API SQL, jak pokazano na [skonfigurować przepływność przy użyciu interfejsu API SQL dla platformy .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacje dotyczące szacowania jednostki przepływności i żądania, zobacz [żądania jednostki i planowania przepływności w usłudze Azure Cosmos DB](request-units.md)

* Aby dowiedzieć się więcej na temat inicjowania obsługi administracyjnej i będzie skalowana w skali za pomocą usługi Cosmos DB, zobacz [partycjonowanie i skalowanie za pomocą usługi Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
