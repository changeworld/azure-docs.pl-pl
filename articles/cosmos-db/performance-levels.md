---
title: Wycofane poziomy wydajności usługi Azure Cosmos DB
description: Dowiedz się więcej o poziomach wydajności S1, S2 i S3 wcześniej dostępnych w usłudze Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 40735f91e2ca58cc42f723c7993686d92f0e5ff0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623341"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Wycofywanie poziomów wydajności S1, S2 i S3

> [!IMPORTANT] 
> Poziomy wydajności S1, S2 i S3 omówione w tym artykule są wycofywane i nie są już dostępne dla nowych kont usługi Azure Cosmos DB.
>

W tym artykule przedstawiono omówienie poziomów wydajności S1, S2 i S3 i omówiono, jak kolekcje, które używają tych poziomów wydajności mogą być migrowane do pojedynczych partycjonowanych kolekcji. Po przeczytaniu tego artykułu będziesz mógł odpowiedzieć na następujące pytania:

- [Dlaczego poziomy wydajności S1, S2 i S3 są wycofywane?](#why-retired)
- [Jak kolekcje pojedynczych partycji i kolekcje podzielone na partycje porównać do poziomów wydajności S1, S2, S3?](#compare)
- [Co muszę zrobić, aby zapewnić nieprzerwany dostęp do moich danych?](#uninterrupted-access)
- [Jak zmieni się moja kolekcja po migracji?](#collection-change)
- [Jak zmienią się rozliczenia po migracji do kolekcji pojedynczych partycji?](#billing-change)
- [Co zrobić, jeśli potrzebuję więcej niż 20 GB miejsca?](#more-storage-needed)
- [Czy można zmienić poziomy wydajności S1, S2 i S3 przed planowaną migracją?](#change-before)
- [Jak przeprowadzić migrację z poziomów wydajności S1, S2 i S3 do kolekcji pojedynczych partycji samodzielnie?](#migrate-diy)
- [Jak mam wpływ, jeśli jestem klientem EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Dlaczego poziomy wydajności S1, S2 i S3 są wycofywane?

Poziomy wydajności S1, S2 i S3 nie oferują elastyczności, którą zapewnia standardowa oferta usługi Azure Cosmos DB. W przypadku poziomów wydajności S1, S2, S3 zarówno przepustowość, jak i pojemność pamięci masowej były wstępnie ustawione i nie oferowały elastyczności. Usługa Azure Cosmos DB oferuje teraz możliwość dostosowywania przepływności i magazynu, oferując znacznie większą elastyczność w możliwości skalowania w miarę zmiany potrzeb.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekcje pojedynczych partycji i kolekcje podzielone na partycje porównać do poziomów wydajności S1, S2, S3?

W poniższej tabeli porównano opcje przepływności i magazynu dostępne w kolekcjach pojedynczych partycji, kolekcjach podzielonych na partycje i poziomach wydajności S1, S2 i S3. Oto przykład dla regionu Us East 2:

|   |Kolekcja podzielona na partycje|Kolekcja pojedynczej partycji|S1|S2|S3|
|---|---|---|---|---|---|
|Maksymalna przepływność|Unlimited (nieograniczony)|10K RU/s|250 ru/s|1 K RU/s|2,5 K RU/s|
|Minimalna przepustowość|2,5 K RU/s|400 RU/s|250 ru/s|1 K RU/s|2,5 K RU/s|
|Maksymalna ilość miejsca do magazynowania|Unlimited (nieograniczony)|20 GB|20 GB|20 GB|20 GB|
|Cena (miesięczna)|Przepustowość: $6 / 100 RU/s<br><br>Pamięć masowa: $0.25/GB|Przepustowość: $6 / 100 RU/s<br><br>Pamięć masowa: $0.25/GB|zł.|zł.|zł.|

Jesteś klientem EA? Jeśli tak, zobacz [Jak mam wpływ na to, czy jestem klientem EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co muszę zrobić, aby zapewnić nieprzerwany dostęp do moich danych?

Jeśli masz kolekcję S1, S2 lub S3, należy przeprowadzić migrację kolekcji do pojedynczej kolekcji partycji programowo [przy użyciu pliku .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak zmieni się moja kolekcja po migracji?

Jeśli masz kolekcję S1, można przeprowadzić migrację do pojedynczej kolekcji partycji z przepływnością 400 RU/s. 400 jednostek RU/s jest najniższą przepływnością dostępną w przypadku kolekcji pojedynczych partycji. Jednak koszt 400 JEDNOST/s w pojedynczej kolekcji partycji jest w przybliżeniu taki sam, jak płaciłeś za kolekcję S1 i 250 RU/ s - więc nie płacisz za dodatkowe 150 RU / s dostępne dla Ciebie.

Jeśli masz kolekcję S2, można przeprowadzić migrację do pojedynczej kolekcji partycji z 1 K RU/s. Nie zobaczysz żadnych zmian na poziomie przepływności.

Jeśli masz kolekcję S3, można przeprowadzić migrację do pojedynczej kolekcji partycji z 2,5 K RU/s. Nie zobaczysz żadnych zmian na poziomie przepływności.

W każdym z tych przypadków po migracji kolekcji będzie można dostosować poziom przepływności lub skalować go w górę i w dół w razie potrzeby, aby zapewnić użytkownikom dostęp o małym opóźnieniu. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak zmienią się rozliczenia po migracji do kolekcji pojedynczych partycji?

Zakładając, że masz 10 kolekcji S1, 1 GB miejsca na magazyn dla każdego, w regionie wschodnie stanów USA i migracji tych 10 kolekcji S1 do 10 pojedynczych kolekcji partycji na 400 RU/s (minimalny poziom). Rachunek będzie wyglądać następująco, jeśli zachowasz kolekcje 10 pojedynczych partycji przez cały miesiąc:

![Jak porównywano ceny S1 dla 10 kolekcji z 10 kolekcjami przy użyciu cen dla pojedynczej kolekcji partycji](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Co zrobić, jeśli potrzebuję więcej niż 20 GB miejsca?

Niezależnie od tego, czy masz kolekcję z poziomem wydajności S1, S2 lub S3, czy masz kolekcję pojedynczych partycji, z których wszystkie mają 20 GB dostępnego miejsca, możesz użyć narzędzia do migracji danych usługi Azure Cosmos DB, aby przeprowadzić migrację danych do podzielonej na partycje kolekcji z praktycznie nieograniczoną przestrzeń dyskową. Aby uzyskać informacje na temat korzyści płynących z kolekcji podzielonej na partycje, zobacz [Partycjonowanie i skalowanie w usłudze Azure Cosmos DB.](sql-api-partition-data.md) 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Czy można zmienić poziomy wydajności S1, S2 i S3 przed planowaną migracją?

Tylko istniejące konta o wydajności S1, S2 i S3 można zmieniać i programowo zmieniać warstwy poziomu wydajności [przy użyciu pliku .NET SDK](#migrate-diy). Jeśli zmienisz z S1, S3 lub S3 do jednej kolekcji partycji, nie można powrócić do poziomów wydajności S1, S2 lub S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak przeprowadzić migrację z poziomów wydajności S1, S2 i S3 do kolekcji pojedynczych partycji samodzielnie?

Można przeprowadzić migrację z poziomów wydajności S1, S2 i S3 do kolekcji pojedynczych partycji programowo [przy użyciu pliku .NET SDK](#migrate-diy). Można to zrobić samodzielnie przed planowaną migracją, aby skorzystać z elastycznych opcji przepływności dostępnych w kolekcjach pojedynczych partycji.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrowanie do kolekcji pojedynczych partycji przy użyciu sdk .NET

W tej sekcji opisano tylko zmienianie poziomu wydajności kolekcji przy użyciu [interfejsu API SQL .NET,](sql-api-sdk-dotnet.md)ale proces jest podobny dla naszych innych pakietów SDK.

Oto fragment kodu do zmiany przepływności kolekcji do 5000 jednostek żądań na sekundę:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Odwiedź [MSDN,](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) aby zobaczyć dodatkowe przykłady i dowiedzieć się więcej o naszych metodach oferty:

* [**ReadOfferAsync (OdczytOfferAsync)**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync (OdczytUjufertyFeedAsync)**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ZamieńOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery (Kwerenda tworzenia)**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak mam wpływ, jeśli jestem klientem EA?

Klienci EA będą chronieni przed ceną do końca bieżącej umowy.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat cen i zarządzania danymi za pomocą usługi Azure Cosmos DB, zapoznaj się z tymi zasobami:

1.  [Partycjonowanie danych w usłudze Cosmos DB](sql-api-partition-data.md). Zrozumienie różnicy między kontenerem pojedynczej partycji i kontenerów podzielonych na partycje, a także porady dotyczące implementowania strategii partycjonowania, aby bezproblemowo skalować.
2.  [Ceny usługi Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Dowiedz się więcej o kosztach inicjowania obsługi administracyjnej przepływności i korzystania z magazynu.
3.  [Jednostki żądające](request-units.md). Zrozumienie zużycia przepływności dla różnych typów operacji, na przykład Odczyt, Zapis, Kwerenda.
