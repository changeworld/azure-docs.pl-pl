---
title: Optymalizuj koszt usługi storage w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami magazynu dla danych przechowywanych w usłudze Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 71f1f8896126728277ba6f0bf2c0ded1b2a608b7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967247"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optymalizuj koszt usługi storage w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia nieograniczony magazyn i przepustowość. W odróżnieniu od przepustowości, które mają do aprowizacji/konfigurowanie na baz danych lub kontenerów w usłudze Azure Cosmos magazynu jest rozliczana na podstawie na podstawie użycia. Opłaty są naliczane tylko za logicznej magazynu, których można używać, i nie masz żadnych magazyn rezerwacji z wyprzedzeniem. Magazyn automatycznie skalować w górę i w dół na podstawie danych, które powodują dodawanie lub usuwanie do kontenera usługi Azure Cosmos DB.

## <a name="storage-cost"></a>Koszt usługi Storage

Magazyn jest rozliczany z jednostką GB. Lokalny magazyn opartych na dyskach SSD jest używany przez Twoje dane i indeksowania. Całkowita ilość miejsca, używany jest równa magazyn wymagany przez dane i indeksy używane we wszystkich regionach, gdy używasz usługi Azure Cosmos DB. Jeśli konto usługi Azure Cosmos jest globalnie replikować w trzech regionach, będzie płacisz łączny koszt magazynu w każdym z tych trzech regionach. Aby oszacować wymaganego do magazynowania, zobacz [planowania pojemności](https://www.documentdb.com/capacityplanner) narzędzia. Koszt usługi storage w usłudze Azure Cosmos DB jest GB 0,25 USD/miesiąc, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) najnowsze aktualizacje. Możesz skonfigurować alerty do określenia miejsca używanego przez kontener usługi Azure Cosmos do monitorowania magazynu, zobacz [monitora usługi Azure Cosmos DB](monitor-accounts.md)) artykułu.

## <a name="optimize-cost-with-item-size"></a>Optymalizowanie kosztów za pomocą rozmiar elementu

Usługa Azure Cosmos DB oczekuje, że rozmiar elementu 2 MB lub mniej, aby uzyskać optymalną wydajność i oszczędność kosztów. Dowolny element, aby przechowywać więcej niż 2 MB danych, należy wziąć pod uwagę przeprojektowanie schematu elementu. W rzadkich, nie można zmieniać schematu można podzielić elementów podrzędnych i logicznie łączyć je przy użyciu typowych identifier(ID). Wszystkie funkcje usługi Azure Cosmos DB, działać spójnie przez Zakotwiczanie do tego identyfikatora logicznego.

## <a name="optimize-cost-with-indexing"></a>Optymalizowanie kosztów za pomocą indeksowania

Domyślnie dane są automatycznie indeksowane, który może zwiększyć całkowitą ilość używanego miejsca. Jednak można zastosować zasady niestandardowe indeksu, aby zmniejszyć to obciążenie. Automatyczne indeksowanie, która nie została ona dostrojona za pomocą zasad jest około 10-20% rozmiaru elementu. Przez usunięcie lub dostosowywanie zasad indeksu, nie płacić za dodatkowych kosztów do zapisu i nie wymagają dodatkowej przepływności. Zobacz [indeksowanie w usłudze Azure Cosmos DB](indexing-policies.md) skonfigurować niestandardowe zasady indeksowania. Użytkownicy mający doświadczenie z relacyjnych baz danych przed, użytkownik może uznać, "index wszystko" oznacza podwojenie magazynu lub nowszej. Jednak w usłudze Azure Cosmos DB, w przypadku mediany, jest znacznie niższy. W usłudze Azure Cosmos DB narzut na przechowywanie indeksu jest zazwyczaj niski (10-20%) nawet w przypadku automatycznego indeksowania, ponieważ jest ona przeznaczona dla rozmiaru magazynu niski. Zarządzając zasady indeksowania, można kontrolować kosztem indeksu zużycia i zwiększa wydajność zapytań, w sposób bardziej szczegółowo.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optymalizowanie kosztów przy użyciu czasu na żywo i zestawienia zmian

Po nie są już potrzebne dane możesz bezpiecznie usunąć go z konta usługi Azure Cosmos przy użyciu [czas wygaśnięcia](time-to-live.md), [zestawienia zmian](change-feed.md) lub stare dane można migrować do innego magazynu danych, takie jak magazyn obiektów blob platformy Azure lub magazynu danych platformy Azure. Czas wygaśnięcia lub czas wygaśnięcia usługi Azure Cosmos DB zapewnia możliwość automatycznie usunąć elementy z kontenera po upływie pewnego czasu. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąp wartość na podstawie poszczególnych elementów. Po ustawieniu czas wygaśnięcia w kontenerze, lub na poziomie elementu, usługi Azure Cosmos DB automatycznie usunie te elementy po upływie okresu czasu od czasu ostatniej modyfikacji. Za pomocą zestawienia zmian, możliwe jest migrowanie danych do dowolnego innego kontenera w usłudze Azure Cosmos DB lub do magazynu danych zewnętrznych. Migracja trwa zero czas przestoju i podczas pracy przeprowadzić migrację, możesz usunąć lub skonfigurować czas wygaśnięcia można usunąć kontenera źródła w usłudze Azure Cosmos.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optymalizowanie kosztów z typami danych multimediów 

Jeśli chcesz przechowywać zaawansowanych typów nośników, na przykład filmy wideo, obrazów, itp., masz kilka opcji w usłudze Azure Cosmos DB. Jedną z opcji jest do przechowywania tych typów multimediów jako elementy usługi Azure Cosmos. Jest ograniczona do 2 MB na element, a limit ten można uniknąć, łańcuch elementu danych do wielu podrzędnych. Lub można przechowywać je w usłudze Azure Blob storage i użyj metadane, aby odwoływać się do nich z elementów Azure Cosmos. Istnieje kilka zalet i wad, w przypadku tej metody. Pierwszym sposobem pobiera najlepszej wydajności pod kątem opóźnienia, przepływność umowy SLA, a także możliwości kompleksowa dystrybucja globalna dla typów danych rozbudowane treści multimedialne, oprócz regularnych elementów Azure Cosmos. Jednak obsługa jest dostępna w cenie wyższy. Dzięki przechowywaniu multimediów w usłudze Azure Blob storage, można zmniejszyć koszty ogólne. Jeśli czas oczekiwania ma kluczowe znaczenie, można użyć usługa premium storage dla plików multimediów, które są wywoływane z elementów w usłudze Azure Cosmos. Integruje się to natywnie przy użyciu usługi CDN do udostępniania obrazów z serwera granicznego przy niskich kosztach obejścia ograniczeń geograficznie. Dół strony, w tym scenariuszu jest to, czy masz do czynienia z dwóch usług — Azure Cosmos DB i Azure Blob storage, co może zwiększyć koszty operacyjne. 

## <a name="check-storage-consumed"></a>Sprawdź wykorzystany Magazyn

Aby sprawdzić użycie magazynu dla kontenera usługi Azure Cosmos, można uruchomić żądanie HEAD lub GET do kontenera i sprawdzić `x-ms-request-quota` i `x-ms-request-usage` nagłówków. Alternatywnie, pracując przy użyciu zestawu .NET SDK, można użyć [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), i [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) właściwości, aby pobrać zajmowanego w magazynie.

## <a name="using-sdk"></a>Przy użyciu zestawu SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do Dowiedz się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)

