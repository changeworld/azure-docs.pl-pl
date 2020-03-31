---
title: Optymalizacja kosztów magazynowania w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami magazynowania danych przechowywanych w usłudze Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754940"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów magazynowania w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB oferuje nieograniczoną ilość miejsca do magazynowania i przepływność. W przeciwieństwie do przepływności, które trzeba aprowizować/skonfigurować w kontenerach lub bazach danych usługi Azure Cosmos, magazyn jest rozliczany na podstawie zużycia. Są rozliczane tylko za magazyn logiczny, który zużywasz i nie musisz rezerwować żadnego miejsca z wyprzedzeniem. Magazyn automatycznie skaluje się w górę i w dół na podstawie danych, które można dodać lub usunąć do kontenera usługi Azure Cosmos.

## <a name="storage-cost"></a>Koszty magazynu

Przechowywanie jest rozliczane z jednostką GB. Lokalny magazyn z kopii SSD jest używany przez dane i indeksowanie. Całkowita ilość używanego magazynu jest równa magazyn wymagane przez dane i indeksy używane we wszystkich regionach, w których używasz usługi Azure Cosmos DB. Jeśli globalnie replikujesz konto usługi Azure Cosmos w trzech regionach, zapłacisz za całkowity koszt magazynu w każdym z tych trzech regionów. Aby oszacować zapotrzebowanie na pamięć masową, zobacz narzędzie [do planowania pojemności.](https://www.documentdb.com/capacityplanner) Koszt magazynu w usłudze Azure Cosmos DB wynosi 0,25 USD/miesiąc, zobacz [strona cennika, aby](https://azure.microsoft.com/pricing/details/cosmos-db/) uzyskać najnowsze aktualizacje. Można skonfigurować alerty do określenia magazynu używanego przez kontener usługi Azure Cosmos, do monitorowania magazynu, zobacz [Monitor Azure Cosmos DB](monitor-accounts.md)) artykuł.

## <a name="optimize-cost-with-item-size"></a>Optymalizacja kosztów za pomocą rozmiaru towaru

Usługa Azure Cosmos DB oczekuje, że rozmiar elementu będzie 2 MB lub mniej dla optymalnej wydajności i korzyści kosztowych. Jeśli potrzebujesz dowolnego elementu do przechowywania danych większych niż 2 MB, należy rozważyć przeprojektowanie schematu elementu. W rzadkich przypadkach, gdy nie można przeprojektować schematu, można podzielić element na podwykonacie i połączyć je logicznie ze wspólnym identyfikatorem(identyfikatorem). Wszystkie funkcje usługi Azure Cosmos DB działają spójnie, zakotwiczając do tego identyfikatora logicznego.

## <a name="optimize-cost-with-indexing"></a>Optymalizacja kosztów dzięki indeksowaniu

Domyślnie dane są automatycznie indeksowane, co może zwiększyć całkowitą ilość używanego magazynu. Można jednak zastosować niestandardowe zasady indeksu, aby zmniejszyć to obciążenie. Automatyczne indeksowanie, które nie zostało dostrojone za pomocą zasad, wynosi około 10-20% rozmiaru towaru. Usuwając lub dostosowując zasady indeksu, nie płacisz dodatkowych kosztów za zapisy i nie wymagasz dodatkowej przepustowości. Zobacz [Indeksowanie w usłudze Azure Cosmos DB,](indexing-policies.md) aby skonfigurować niestandardowe zasady indeksowania. Jeśli wcześniej pracowałeś z relacyjnych baz danych, możesz pomyśleć, że "indeks wszystko" oznacza podwojenie pamięci masowej lub wyższe. Jednak w usłudze Azure Cosmos DB, w przypadku mediany jest znacznie niższa. W usłudze Azure Cosmos DB obciążenie magazynowe indeksu jest zazwyczaj niskie (10-20%) nawet z automatycznym indeksowaniem, ponieważ jest przeznaczony do niskiej pojemności pamięci masowej. Zarządzając zasadami indeksowania, można kontrolować kompromis footprint indeksu i wydajność zapytań w sposób bardziej szczegółowe.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optymalizacja kosztów z czasem na życie i zmiana kanału informacyjnego

Gdy nie potrzebujesz już danych, możesz bezpiecznie usunąć je z konta usługi Azure Cosmos przy użyciu [czasu na żywo,](time-to-live.md) [zmienić źródło danych](change-feed.md) lub przeprowadzić migrację starych danych do innego magazynu danych, takiego jak magazyn obiektów blob platformy Azure lub magazyn danych platformy Azure. Z czasem na żywo lub TTL usługi Azure Cosmos DB zapewnia możliwość automatycznego usuwania elementów z kontenera po pewnym czasie. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąpić wartość na podstawie dla elementu. Po ustawieniu czasu wygaśnięcia w kontenerze lub na poziomie elementu usługa Azure Cosmos DB automatycznie usunie te elementy po upływie okresu od ostatniej modyfikacji. Za pomocą źródła danych zmian, można migrować dane do innego kontenera w usłudze Azure Cosmos DB lub do zewnętrznego magazynu danych. Migracja trwa zero czasu w dół i po zakończeniu migracji, można usunąć lub skonfigurować czas na żywo, aby usunąć źródło kontenera usługi Azure Cosmos.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optymalizacja kosztów dzięki typom danych multimedialnych 

Jeśli chcesz przechowywać typy multimediów, na przykład filmy wideo, obrazy itp., masz wiele opcji w usłudze Azure Cosmos DB. Jedną z opcji jest przechowywanie tych typów multimediów jako elementów usługi Azure Cosmos. Istnieje limit 2 MB na element i można uniknąć tego limitu, łącząc element danych w wiele podelementów. Możesz też przechowywać je w magazynie obiektów Blob platformy Azure i używać metadanych do odwoływania się do nich z elementów usługi Azure Cosmos. Istnieje wiele zalet i wad z tego podejścia. Pierwsze podejście zapewnia najlepszą wydajność pod względem opóźnienia, łasce przepływności oraz możliwości dystrybucji globalnej "pod klucz" dla typów danych multimedialnych oprócz zwykłych elementów usługi Azure Cosmos. Jednak wsparcie jest dostępne w wyższej cenie. Przechowując multimedia w magazynie obiektów Blob platformy Azure, można obniżyć koszty ogólne. Jeśli opóźnienie jest krytyczne, można użyć magazynu w wersji premium dla plików multimedialnych, do których odwołuje się elementy usługi Azure Cosmos. To integruje się natywnie z siecią CDN do obsługi obrazów z serwera brzegowego przy niższych kosztach, aby obejść ograniczenia geograficzne. W dół w tym scenariuszu jest, że masz do czynienia z dwóch usług — Azure Cosmos DB i Azure Blob storage, co może zwiększyć koszty operacyjne. 

## <a name="check-storage-consumed"></a>Sprawdź zużyte miejsce do magazynowania

Aby sprawdzić zużycie magazynu kontenera usługi Azure Cosmos, można uruchomić żądanie HEAD `x-ms-request-quota` lub `x-ms-request-usage` GET w kontenerze i sprawdzić nagłówki i. Alternatywnie podczas pracy z .NET SDK, można użyć [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))i [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) właściwości, aby uzyskać magazyn zużyty.

## <a name="using-sdk"></a>Używanie zestawu SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB, aby uzyskać następujące artykuły:

* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont usługi Azure Cosmos](optimize-cost-regions.md)

