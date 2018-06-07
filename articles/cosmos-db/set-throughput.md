---
title: Przepływność udostępniania dla bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić udostępnionej przepływności dla containsers bazy danych Azure rozwiązania Cosmos, kolekcje, wykresów i tabel.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: sngun
ms.openlocfilehash: 777655d8976990396b2c78a5b6d977a92b1a2335
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614081"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Ustawianie i pobieranie przepływności bazy danych Azure rozwiązania Cosmos kontenery i bazy danych

Można ustawić przepływności kontenera Azure DB rozwiązania Cosmos lub zestaw kontenerów przy użyciu portalu Azure lub za pomocą zestawów SDK klienta. Podczas obsługi administracyjnej przepływności zestawu kontenery tych kontenerach udostępnianie udostępnionej przepływności. Przepływność inicjowania obsługi administracyjnej dla poszczególnych kontenerów zagwarantuje rezerwację przepustowości dla określonego kontenera. Z drugiej strony inicjowania obsługi administracyjnej przepływności bazy danych pozwala na współużytkowanie przepływności wśród wszystkich kontenerów, które należą do tej bazy danych. W bazie danych bazy danych Azure rozwiązania Cosmos może mieć zestaw kontenerów, których udostępnianie przepływności, a także kontenerów, które są wyposażone w dedykowane przepływności. 

W oparciu o udostępnionej przepływności, bazy danych Azure rozwiązania Cosmos przyzna fizycznej partycji do obsługi danych kontenerów i podziałów/rebalances między partycjami jako ich przyrostu.

Podczas przypisywania RU/s na poziomie poszczególnych kontenera, kontenerów może zostać utworzony jako *stałej* lub *nieograniczone*. Kontenery o stałym rozmiarze są ograniczone do 10 GB, a ich maksymalna przepływność wynosi 10 000 jednostek żądań na sekundę. Aby utworzyć kontener nieograniczone, należy określić minimalną przepustowość 1 000 RU/s i [klucza partycji](partition-data.md). Ponieważ danych może być konieczne można podzielić na wiele partycji, jest konieczne pobranie klucz partycji, który ma dużej kardynalności (od 100 do milionów unikatowe wartości). Wybierając klucza partycji z wielu różnych wartości upewnieniu się, że żądania i wykres kontenera/tabeli mogą być skalowane jednolicie Azure DB rozwiązania Cosmos. 

Podczas przypisywania RU/s w zestawie kontenerów, kontenerów należących do tego zestawu są traktowane jako *nieograniczone* kontenery i muszą określać klucz partycji.

![Inicjowanie obsługi administracyjnej jednostki żądania dla poszczególnych kontenerów i zestaw kontenerów](./media/request-units/provisioning_set_containers.png)

W tym artykule przedstawiono kroki wymagane do skonfigurowania przepływności na różnych poziomach konta bazy danych Azure rozwiązania Cosmos. 

## <a name="provision-throughput-by-using-azure-portal"></a>Zainicjuj obsługę przepływność przy użyciu portalu Azure

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Przepływność udostępniania dla kontenera (kolekcji wykres/tabela)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W nawigacji po lewej stronie, wybierz **wszystkie zasoby** i Znajdź konto bazy danych Azure rozwiązania Cosmos.  
3. Przepływność można skonfigurować podczas tworzenia kontenera (kolekcji, wykres, tabelę) lub przepływności aktualizacji dla istniejącego kontenera.  
4. Aby przypisać przepływności podczas tworzenia kontenera, otwórz **Eksploratora danych** bloku, a następnie wybierz **nowej kolekcji** (nowy wykres, nową tabelę dla innych interfejsów API)  
5. Wypełnij formularz **Dodaj kolekcji** bloku. Pola w tym bloku są opisane w poniższej tabeli:  

   |**Ustawienie**  |**Opis**  |
   |---------|---------|
   |Identyfikator bazy danych  |  Podaj unikatową nazwę identyfikującą bazy danych. Baza danych jest kontenerem logicznym z co najmniej jedną kolekcję. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków /, \\, #, ? ani mieć spacji na końcu. |
   |Identyfikator kolekcji  | Podaj unikatową nazwę identyfikującą kolekcji. W przypadku identyfikatorów kolekcji obowiązują takie same wymagania dotyczące znaków, jak dla nazw baz danych. |
   |Pojemność magazynu   | Ta wartość przedstawia pojemność magazynu bazy danych. Podczas inicjowania obsługi administracyjnej przepływność dla poszczególnych kolekcji, pojemności magazynu może być **stałe (10 GB)** lub **nieograniczone**. Pojemność magazynu nieograniczone wymaga ustawienia klucza partycji dla danych.  |
   |Przepływność   | Każdej kolekcji i bazy danych mogą mieć przepływności w jednostkach żądań na sekundę.  Stałe pojemności minimalna przepływność wynosi 400 jednostek żądań na sekundę (RU/s), nieograniczony magazyn pojemności, minimalna przepustowość ustawiono 1000 RU/s.|

6. Po wprowadzeniu wartości dla tych pól, wybierz **OK** Aby zapisać ustawienia.  

   ![Zestaw przepływność dla kolekcji](./media/set-throughput/set-throughput-for-container.png)

7. Aby zaktualizować przepływność dla istniejącego kontenera, rozwiń węzeł bazy danych i kontener, a następnie kliknij przycisk **ustawienia**. W nowym oknie, wpisz nową wartość przepływności, a następnie wybierz **zapisać**.  

   ![Zaktualizuj przepływność dla kolekcji](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Przepływność udostępniania dla zestawu kontenerów lub na poziomie bazy danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W nawigacji po lewej stronie, wybierz **wszystkie zasoby** i Znajdź konto bazy danych Azure rozwiązania Cosmos.  
3. Przepływność można skonfigurować podczas tworzenia bazy danych lub aktualizacji przepływności istniejącej bazy danych.  
4. Aby przypisać przepływności podczas tworzenia bazy danych, otwórz **Eksploratora danych** bloku, a następnie wybierz **nowej bazy danych**  
5. Wypełnij **bazy danych o identyfikatorze** wartość wyboru **przepływności należy** opcji i skonfigurować wartość przepływności. Bazy danych może zostać zainicjowana obsługa przepustowość minimalną wartość 50 000 RU/s.  

   ![Ustaw przepustowość z nowej opcji bazy danych](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Aby zaktualizować przepływności istniejącej bazy danych, rozwiń węzeł bazy danych i kontener, a następnie kliknij przycisk **skali**. W nowym oknie, wpisz nową wartość przepływności, a następnie wybierz **zapisać**.  

   ![Przepływność aktualizacji bazy danych](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Przepływność udostępniania dla zestawu kontenerów, jak również poszczególnych kontenera w bazie danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. W nawigacji po lewej stronie, wybierz **wszystkie zasoby** i Znajdź konto bazy danych Azure rozwiązania Cosmos.  
3. Utwórz bazę danych i przypisać przepływności. Otwórz **Eksploratora danych** bloku, a następnie wybierz **nowej bazy danych**  
4. Wypełnij **bazy danych o identyfikatorze** wartość wyboru **przepływności należy** opcji i skonfigurować wartość przepływności. Bazy danych może zostać zainicjowana obsługa przepustowość minimalną wartość 50 000 RU/s.  

   ![Ustaw przepustowość z nowej opcji bazy danych](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Następnie utwórz kolekcję w ramach bazy danych utworzonej w powyżej kroku. Aby utworzyć kolekcję, kliknij prawym przyciskiem myszy bazę danych i wybierz **nowej kolekcji**.  

6. W **Dodaj kolekcji** bloku, wprowadź nazwę kolekcji, a klucz partycji. Opcjonalnie można udostępnić przepustowości dla określonego kontenera, jeśli nie chcesz przypisać wartość przepływności, przepływności przypisane do bazy danych jest udostępniony w kolekcji.  

   ![Opcjonalnie ustawić przepływności kontenera](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Zagadnienia dotyczące inicjowania obsługi administracyjnej przepływności

Poniżej znajdują się pewne kwestie, które ułatwiają podejmowanie decyzji o strategię rezerwacji przepustowości.

Należy wziąć pod uwagę, udostępniania przepływności na poziomie bazy danych (do którego jest zestaw kontenery) w następujących przypadkach:

* Jeśli masz dwanaście lub więcej liczbę kontenerów, które można udostępniać przepustowość między niektóre lub wszystkie z nich.  

* Podczas migracji z pojedynczej dzierżawy bazy danych, która jest przeznaczony do uruchamiania na IaaS hostowanych maszyn wirtualnych lub lokalnymi (na przykład NoSQL lub relacyjnych baz danych) do bazy danych rozwiązania Cosmos Azure i mają wiele kontenerów.  

* Jeśli chcesz wziąć pod uwagę nieplanowane największego obciążenia przy użyciu puli przepływności na poziomie bazy danych.  

* Zamiast ustawienia przepustowości w kontenerze poszczególnych myślisz o przygotowaniu łącznej przepływności zestawu kontenery w bazie danych.

Należy wziąć pod uwagę, udostępniania przepływności na poszczególnych kontenera w następujących przypadkach:

* Jeśli masz mniejszą liczbę kontenerów bazy danych Azure rozwiązania Cosmos.  

* Jeśli chcesz pobrać gwarantowane przepustowość do danego kontenera przez umowy dotyczącej poziomu usług.

## <a name="throughput-ranges"></a>Przepływność zakresów

W poniższej tabeli wymieniono dostępne dla kontenerów przepływności:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Kontener jednej partycji</strong></p></td>
            <td valign="top"><p><strong>Kontener podzielonym na partycje</strong></p></td>
            <td valign="top"><p><strong>Zbiór kontenerów</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Przepustowość minimalna</p></td>
            <td valign="top"><p>400 jednostek żądań na sekundę</p></td>
            <td valign="top"><p>1000 jednostek żądania na sekundę</p></td>
            <td valign="top"><p>50 000 jednostek żądań na sekundę</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Maksymalna przepustowość</p></td>
            <td valign="top"><p>10 000 jednostek żądań na sekundę</p></td>
            <td valign="top"><p>Nieograniczona liczba</p></td>
            <td valign="top"><p>Nieograniczona liczba</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Ustaw przepływność przy użyciu interfejsu API SQL dla platformy .NET

Oto fragment kodu dotyczący tworzenia kontenera o 3000 jednostki żądania na sekundę dla poszczególnych kontenera przy użyciu zestawu SDK .NET interfejsu API SQL:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Oto fragment kodu dla inicjowania obsługi administracyjnej 100 000 jednostek na sekundę żądania w zestawie kontenerów przy użyciu zestawu SDK .NET interfejsu API SQL:

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

Azure DB rozwiązania Cosmos działa modelu rezerwacji przepustowości. Oznacza to, że są rozliczane ilości przepływności *zastrzeżone*, niezależnie od tego, jaka część tego przepływności jest aktywnie *używane*. Aplikacją na obciążenia, danych i stosowania zmian wzorce, można łatwo skalować liczbę w górę i w dół zastrzeżone RUs za pomocą zestawów SDK lub przy użyciu [Azure Portal](https://portal.azure.com).

Każdy kontener lub zbiór kontenerów, jest mapowany na `Offer` zasobów w usłudze Azure DB rozwiązania Cosmos mającej metadane dotyczące udostępnionej przepływności. Możesz zmienić przydzielone przepływności wyszukiwania odpowiadający jej zasób oferta dla kontenera, a następnie zaktualizowaniem go przy użyciu nowej wartości przepływności. Oto fragment kodu do zmiany przepływności kontenera do 5000 jednostek żądania na drugi przy użyciu zestawu .NET SDK:

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

Nie ma to wpływu na dostępność z kontenera lub zbiór kontenerów, po zmianie przepływność. Zazwyczaj nowe zarezerwowaną przepływnością obowiązuje w ciągu kilku sekund na stosowanie nowych przepływności.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Aby ustawić przepływność przy użyciu interfejsu API SQL dla języka Java

Poniższy fragment kodu pobiera bieżący przepływności i zmieni 500 RU/s. Kompletny kod przykładowy, [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) pliku w witrynie GitHub. 

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

## <a id="GetLastRequestStatistics"></a>Pobierz przepływności za pomocą polecenia GetLastRequestStatistics API bazy danych MongoDB

Interfejs API bazy danych MongoDB obsługuje polecenia niestandardowych, *getLastRequestStatistics*, pobierania opłat żądania dla danej operacji.

Na przykład w powłokę Mongo, należy wykonać operację, którą chcesz zweryfikować opłata żądania dla.
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

Jedną z metod do oszacowania ilości zarezerwowaną przepływnością wymagane przez aplikację jest Rejestruj opłata jednostki żądania skojarzonego z typowymi operacjami uruchamiania elementu reprezentatywny używanych przez aplikację i następnie oszacować liczbę operacje przewidywania do wykonania w ciągu sekundy.

> [!NOTE]
> Jeśli masz typów elementów, które różnią się znacznie pod względem rozmiaru i liczby właściwości indeksowanych rejestrowania opłata jednostki żądanie dotyczy operacji związanych z każdym *typu* typowe elementu.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Uzyskiwanie przepływność przy użyciu portalu metryki interfejsu API bazy danych MongoDB

Najprostszym sposobem, aby uzyskać dobrą oszacowanie żądania opłat jednostki bazy danych MongoDB interfejsu API jest użycie [portalu Azure](https://portal.azure.com) metryki. Z *liczba żądań* i *opłat żądania* wykresy, możesz uzyskać szacunkową liczbę jednostek żądania, każdy zajmuje operacji i liczbę jednostek żądania zużywają względem siebie.

![Metryki portalu API bazy danych MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Przekraczanie limitów zarezerwowaną przepływnością w interfejsie API bazy danych MongoDB
Aplikacje, które przekraczają udostępnionej przepływności dla kontenera lub grupy kontenerów będzie ograniczony szybkość dopóki stopę zużycia spadnie poniżej elastycznie przepustowość. W przypadku ograniczenia szybkości wewnętrznej bazy danych zakończy się żądanie z `16500` kod błędu: - `Too Many Requests`. Domyślnie interfejsu API bazy danych MongoDB ma automatycznie ponawiać próbę maksymalnie 10 razy przed zwróceniem `Too Many Requests` kod błędu. W przypadku otrzymania wiele `Too Many Requests` kody błędów, warto rozważyć dodanie logiki ponawiania próby w aplikacji Błąd procedury obsługi lub [zwiększyć przepływność dla kontenera](set-throughput.md).

## <a name="throughput-faq"></a>Przepływność — często zadawane pytania

**Można ustawić Moje przepływności na mniej niż 400 RU/s?**

400 RU/s jest minimalna przepływność DB rozwiązania Cosmos kontenerów jednej partycji (minimum dla kontenerów podzielonym na partycje jest 1000 RU/s). Żądanie jednostki są ustawiane w 100 RU/s odstępach czasu, ale przepływności nie można ustawić na 100 RU/s lub dowolną wartość mniejszą niż 400 RU/s. Jeśli szukasz ekonomiczne metody umożliwiające opracowanie i przetestowanie rozwiązania Cosmos DB mogą korzystać z BEZPŁATNEJ [Azure rozwiązania Cosmos DB emulatora](local-emulator.md), które można wdrożyć lokalnie bez ponoszenia kosztów. 

**Jak ustawić przepływność przy użyciu interfejsu API bazy danych MongoDB?**

Brak bez rozszerzenia interfejsu API bazy danych MongoDB, można ustawić przepływności. Zalecane jest używanie interfejsu API SQL, jak pokazano w [można ustawić przepływność przy użyciu interfejsu API SQL dla platformy .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się o szacowaniu jednostek przepływności i żądanie, zobacz [żądania jednostki i planowania przepływności w usłudze Azure DB rozwiązania Cosmos](request-units.md)

* Aby dowiedzieć się więcej o zasięgu planety będzie DB rozwiązania Cosmos i udostępniania, zobacz [dzielenia na partycje i skalowania rozwiązania Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png
