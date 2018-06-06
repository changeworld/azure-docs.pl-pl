---
title: Wycofane poziomów wydajności bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat S1, S2 i S3 poziomy wydajności dostępnej w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d1bb7551e6dfb6c42853ab95096f17f5285c69c1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796652"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Wycofanie poziomy wydajności S1, S2 i S3

> [!IMPORTANT] 
> Poziomy wydajności S1, S2 i S3 omówione w tym artykule jest wycofana i nie będą już dostępne dla nowego konta bazy danych Azure rozwiązania Cosmos.
>

Ten artykuł zawiera omówienie poziomów wydajności S1, S2 i S3 i opisano, jak kolekcje, korzystających z tych poziomów wydajności mogą być migrowane do jednej kolekcji partycjonowanych. Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

- [Dlaczego są poziomy wydajności S1, S2 i S3 są wycofana?](#why-retired)
- [Jak kolekcje z jedną partycją i kolekcji partycjonowanych porównania S1, S2, poziomy wydajności S3?](#compare)
- [Co należy zrobić w celu zapewnienia nieprzerwanego dostępu do danych?](#uninterrupted-access)
- [Jak mojej kolekcji ulegnie zmianie po migracji](#collection-change)
- [Jak Moje rozliczeń ulegnie zmianie po używam I migracji do kolekcje z jedną partycją?](#billing-change)
- [Co zrobić, jeśli potrzebna jest więcej niż 10 GB przestrzeni dyskowej?](#more-storage-needed)
- [Czy mogę zmienić między S1, S2 i S3 poziomy wydajności przed planowanej migracji?](#change-before)
- [Jak przeprowadzić migrację z S1, S2, S3 poziomów wydajności na kolekcje z jedną partycją samodzielnie?](#migrate-diy)
- [Jak m wpływ mam EA klienta?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Dlaczego są wydajności S1, S2 i S3 poziomy wycofana?

Poziomy wydajności S1, S2 i S3 nie oferują elastyczność, która udostępnia standardowy oferty Azure DB rozwiązania Cosmos. S1, S2, poziomy wydajności S3, zarówno przepustowość i pojemność pamięci masowej zostały wstępnie ustawiony i nie zaproponował elastyczność. Azure DB rozwiązania Cosmos oferuje możliwość dostosowywania Twojej przepływność i Magazyn, oferuje znacznie większą elastyczność i możliwość skalować stosownie do potrzeb.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekcje z jedną partycją i kolekcji partycjonowanych porównania S1, S2, poziomy wydajności S3?

W poniższej tabeli porównano opcje przepływność i Magazyn, które muszą być dostępne w kolekcji partycjonowanych i S1, S2, S3 poziomy wydajności w kolekcje z jedną partycją. Oto przykład dla regionu nam wschodnie 2:

|   |Kolekcja podzielonym na partycje|Kolekcja jednej partycji|S1|S2|S3|
|---|---|---|---|---|---|
|Maksymalna przepustowość|Nieograniczona liczba|10 K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Przepływność minimalna|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Maksymalna przestrzeń magazynowa|Nieograniczona liczba|10 GB|10 GB|10 GB|10 GB|
|Cena (co miesiąc)|Przepływność: $6 / 100 RU/s<br><br>Magazyn: 0,25 USD/GB|Przepływność: $6 / 100 RU/s<br><br>Magazyn: 0,25 USD/GB|$25 USD|$50 USD|$100 USD|

Czy klient EA? Jeśli tak, zobacz [am I wpływ mam EA klienta?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co należy zrobić w celu zapewnienia nieprzerwanego dostępu do danych?

Jeśli masz kolekcję S1, S2 lub S3, należy zmigrować kolekcji do kolekcji jednej partycji programowo [przy użyciu zestawu .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak mojej kolekcji ulegnie zmianie po migracji

Jeśli masz S1 kolekcji można migrować je do kolekcji jednej partycji o 400 RU/s przepustowości. 400 RU/s jest najniższa przepływność dostępne kolekcje z jedną partycją. Jednak koszt 400 RU/s w kolekcji jedną partycją wynosi około takie same jak zostały płatności z kolekcji S1 i 250 RU/s –, więc nie płatność za bardzo 150 RU/s dostępne.

Jeśli masz S2 kolekcji można migrować je do Kolekcja jednej partycji o 1 K RU/s. Zostanie wyświetlone żadne zmiany do poziomu przepływności.

Jeśli masz kolekcji S3, można migrować je do kolekcji jednej partycji z 2,5 K RU/s. Zostanie wyświetlone żadne zmiany do poziomu przepływności.

W każdym z tych przypadków po przeprowadzeniu migracji kolekcji, można dostosować poziom przepływności lub go skalować w górę i w dół w celu zapewnienia małych opóźnieniach dostępu użytkowników. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak Moje rozliczeń ulegnie zmianie po migracji I Aby kolekcje z jedną partycją?

Wykonując zawiera 10 kolekcji S1, 1 GB pamięci masowej dla każdego regionu nam wschodnie i migracji kolekcjach S1 10 do 10 kolekcje z jedną partycją na 400 RU/s (minimalny poziom). Jeśli zachowasz 10 kolekcje z jedną partycją pełny miesiąc, rachunku będzie wyglądać następująco:

![Jak S1 ceny 10 kolekcje porównuje do 10 kolekcji przy użyciu ceny Kolekcja jednej partycji](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Co zrobić, jeśli potrzebna jest więcej niż 10 GB przestrzeni dyskowej?

Określa, czy masz Kolekcja o poziomie wydajności S1, S2 lub S3 lub mieć Kolekcja jednej partycji, które mają 10 GB miejsca do magazynowania dostępne, można użyć narzędzia migracji danych DB rozwiązania Cosmos Azure do migracji danych do kolekcji partycjonowanych z niemal nieograniczony magazyn. Aby uzyskać informacje o zaletach kolekcję partycjonowaną, zobacz [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Czy mogę zmienić między S1, S2 i S3 poziomy wydajności przed planowanej migracji?

Istniejące konta tylko z S1, S2 i S3 wydajności można zmienić i programowo zmienić warstwy poziomu wydajności [przy użyciu zestawu .NET SDK](#migrate-diy). W przypadku zmiany z S1, S3 lub S3 do kolekcji jednej partycji nie może zwracać na poziomie wydajności S1, S2 lub S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak przeprowadzić migrację z S1, S2, S3 poziomów wydajności na kolekcje z jedną partycją samodzielnie?

Można migrować z poziomów wydajności S1, S2 i S3 do kolekcje z jedną partycją programowo [przy użyciu zestawu .NET SDK](#migrate-diy). Można to zrobić na własną przed planowanej migracji, aby korzystać z przepustowości elastyczne opcje dostępne z kolekcje z jedną partycją.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrowanie do kolekcje z jedną partycją przy użyciu zestawu .NET SDK

W tej sekcji opisano tylko zmiana wydajności kolekcji poziomu przy użyciu [interfejs API .NET SQL](sql-api-sdk-dotnet.md), ale proces jest podobny do naszych innych zestawów SDK.

Oto fragment kodu dla zmiana przepływność kolekcji do 5000 jednostek żądań na sekundę:
    
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

Odwiedź stronę [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) Aby wyświetlić dodatkowe przykłady i Dowiedz się więcej o naszym metody oferta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak m wpływ mam EA klienta?

Umowa EA klienci będą cen chronione aż do zakończenia ich bieżący kontrakt.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o cenach i zarządzanie danymi z bazy danych Azure rozwiązania Cosmos, zapoznaj się z tymi zasobami:

1.  [Partycjonowanie danych w bazie danych rozwiązania Cosmos](sql-api-partition-data.md). Opis różnicy między kontenera jednej partycji i kontenery podzielonym na partycje, a także wskazówki dotyczące implementowania strategii partycjonowania bezproblemowo skalowania.
2.  [Cennik rozwiązania cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Więcej informacji na temat kosztów obsługi przepływności i korzystanie z magazynu.
3.  [Jednostek żądania](request-units.md). Zrozumienie zużycie przepustowości dla różnych operacji typów, na przykład Odczyt, zapis zapytania.
