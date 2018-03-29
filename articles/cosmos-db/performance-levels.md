---
title: Wycofane poziomów wydajności bazy danych Azure rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat S1, S2 i S3 poziomy wydajności dostępnej w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62ef3c8988b0f11d6d8bcb69080f4e1b98a44641
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Wycofanie poziomy wydajności S1, S2 i S3

> [!IMPORTANT] 
> Poziomy wydajności S1, S2 i S3 omówione w tym artykule jest wycofana i nie będą już dostępne dla nowego konta bazy danych Azure rozwiązania Cosmos.
>

Ten artykuł zawiera omówienie poziomów wydajności S1, S2 i S3 i opisano, jak kolekcje, korzystających z tych poziomów wydajności będą migrowane do kolekcje z jedną partycją w 2017. Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

- [Dlaczego są wydajności S1, S2 i S3 poziomy wycofana?](#why-retired)
- [Jak kolekcje z jedną partycją i kolekcji partycjonowanych porównania S1, S2, poziomy wydajności S3?](#compare)
- [Co należy zrobić w celu zapewnienia nieprzerwanego dostępu do danych?](#uninterrupted-access)
- [Jak mojej kolekcji ulegnie zmianie po migracji](#collection-change)
- [Jak Moje rozliczeń ulegnie zmianie po używam I migracji do kolekcje z jedną partycją?](#billing-change)
- [Co zrobić, jeśli potrzebna jest więcej niż 10 GB przestrzeni dyskowej?](#more-storage-needed)
- [Czy mogę zmienić między S1, S2 i S3 poziomy wydajności przed planowanej migracji?](#change-before)
- [Jak wiedzą, gdy został zmigrowany mojej kolekcji?](#when-migrated)
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
|Przepustowość minimalna|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Maksymalna przestrzeń magazynowa|Nieograniczona liczba|10 GB|10 GB|10 GB|10 GB|
|Cena (co miesiąc)|Przepływność: $6 / 100 RU/s<br><br>Magazyn: 0,25 USD/GB|Przepływność: $6 / 100 RU/s<br><br>Magazyn: 0,25 USD/GB|$25 USD|$50 USD|$100 USD|

Czy klient EA? Jeśli tak, zobacz [am I wpływ mam EA klienta?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co należy zrobić w celu zapewnienia nieprzerwanego dostępu do danych?

Nothing, DB rozwiązania Cosmos obsługuje migrację dla Ciebie. Jeśli masz kolekcję S1, S2 lub S3 bieżącej kolekcji zostaną zmigrowane do kolekcji jednej partycji w 2017 r. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak mojej kolekcji ulegnie zmianie po migracji

Jeśli masz kolekcji S1 zostaną poddane migracji do kolekcji jednej partycji o 400 RU/s przepustowości. 400 RU/s jest najniższa przepływność dostępne kolekcje z jedną partycją. Jednak koszt 400 RU/s w kolekcji jedną partycją wynosi około takie same jak zostały płatności z kolekcji S1 i 250 RU/s –, więc nie płatność za bardzo 150 RU/s dostępne.

Jeśli masz kolekcji S2 zostaną poddane migracji do kolekcji jednej partycji z 1 K RU/s. Zostanie wyświetlone żadne zmiany do poziomu przepływności.

Jeśli masz kolekcji S3 zostaną poddane migracji do kolekcji jednej partycji z 2,5 K RU/s. Zostanie wyświetlone żadne zmiany do poziomu przepływności.

W każdym z tych przypadków po przeprowadzeniu migracji kolekcji, można dostosować poziom przepływności lub go skalować w górę i w dół w celu zapewnienia małych opóźnieniach dostępu użytkowników. Aby zmienić poziom przepływności po migracji ma kolekcji, po prostu otwórz konto DB rozwiązania Cosmos w portalu Azure, kliknij skali, wybierz kolekcję, a następnie Dostosuj poziom przepływności, jak pokazano na poniższym zrzucie ekranu:

![Jak skalować przepływność w portalu Azure](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>Jak Moje rozliczeń ulegnie zmianie po używam I migracji do kolekcje z jedną partycją?

Wykonując zawiera 10 kolekcji S1, 1 GB pamięci masowej dla każdego regionu nam wschodnie i migracji kolekcjach S1 10 do 10 kolekcje z jedną partycją na 400 RU/s (minimalny poziom). Jeśli zachowasz 10 kolekcje z jedną partycją pełny miesiąc, rachunku będzie wyglądać następująco:

![Jak S1 ceny 10 kolekcje porównuje do 10 kolekcji przy użyciu ceny Kolekcja jednej partycji](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Co zrobić, jeśli potrzebna jest więcej niż 10 GB przestrzeni dyskowej?

Czy masz kolekcję o poziomie wydajności S1, S2 lub S3 lub mieć Kolekcja jednej partycji, które mają 10 GB dostępnego miejsca, służy narzędzie do migracji danych DB rozwiązania Cosmos do migracji danych do kolekcji partycjonowanych z niemal nieograniczonego magazynu. Aby uzyskać informacje o zaletach kolekcję partycjonowaną, zobacz [dzielenia na partycje i skalowania w usłudze Azure DB rozwiązania Cosmos](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Czy mogę zmienić między S1, S2 i S3 poziomy wydajności przed planowanej migracji?

Istniejące konta tylko z wydajnością S1, S2 i S3 będzie można zmienić i zmieniać warstwy poziomu wydajności za pośrednictwem portalu lub programowo. W przypadku zmiany z S1, S3 lub S3 do kolekcji jednej partycji nie może zwracać na poziomie wydajności S1, S2 lub S3.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>Jak wiedzą, gdy został zmigrowany mojej kolekcji?

Migracja wystąpią na pod 2017 r. Jeśli masz kolekcję wykorzystującą S1 poziomy wydajności S2 lub S3, zespół DB rozwiązania Cosmos skontaktuje się z Tobą za pośrednictwem poczty e-mail przed dokonaniem migracji. Po zakończeniu migracji portalu Azure zostaną wyświetlone kolekcji używany cen warstwy standardowa.

![Jak potwierdzić kolekcji został zmigrowany do warstwy cenowej standardowa.](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak przeprowadzić migrację z S1, S2, S3 poziomów wydajności na kolekcje z jedną partycją samodzielnie?

Można przeprowadzić migrację z poziomów wydajności S1, S2 i S3 do kolekcje z jedną partycją przy użyciu portalu Azure lub programowo. Można to zrobić na własną przed planowanej migracji, aby korzystać z przepustowości elastyczne opcje dostępne z kolekcje z jedną partycją lub będziemy migrować kolekcji dla Ciebie w 2017 r.

**Aby przeprowadzić migrację do kolekcje z jedną partycją przy użyciu portalu Azure**

1. W [ **portalu Azure**](https://portal.azure.com), kliknij przycisk **bazy danych Azure rozwiązania Cosmos**, następnie wybierz konto bazy danych rozwiązania Cosmos do zmodyfikowania. 
 
    Jeśli **bazy danych Azure rozwiązania Cosmos** jest nie na pasku przechodzenia kliknij >, przewiń do **baz danych**, wybierz pozycję **bazy danych Azure rozwiązania Cosmos**, a następnie wybierz konto.  

2. W menu zasobów w obszarze **kontenery**, kliknij przycisk **skali**, wybierz kolekcję, aby zmodyfikować z listy rozwijanej, a następnie kliknij przycisk **warstwy cenowej**. Konta przy użyciu wstępnie zdefiniowanych przepływności mają warstwy cenowej S1, S2 lub S3.  W **wybierz warstwę cenową** kliknij przycisk **standardowe** Zmień przepływności zdefiniowane przez użytkownika, a następnie kliknij przycisk **wybierz** można zapisać zmiany.

    ![Zrzut ekranu przedstawiający miejsce zmienić wartość przepływności strony ustawień](./media/performance-levels/change-performance-set-thoughput.png)

3. W **skali** strony, **warstwy cenowej** jest zmieniana na **standardowe** i **przepływności (RU/s)** domyślnie wyświetlane jest okno wartość 400. Ustaw przepustowość w przedziale od 400 do 10 000 [jednostek żądania](request-units.md)/second (RU/s). **Szacowana kwota rachunku miesięczne** w dolnej części strony Aktualizacje automatyczne oszacować miesięczny koszt. 

    >[!IMPORTANT] 
    > Po zapisaniu zmian i przenieść warstwa cenowa standardowa, nie można wycofać na poziomie wydajności S1, S2 lub S3.

4. Kliknij przycisk **zapisać** Aby zapisać zmiany.

    Jeśli okaże się, że potrzebujesz więcej przepływności (większe niż 10 000 RU/s) lub więcej pamięci masowej (większe niż 10 GB) można utworzyć kolekcję partycjonowaną. Aby przeprowadzić migrację kolekcji jednej partycji do kolekcji podzielone na partycje, zobacz [migracji z jednej partycji do kolekcji partycjonowanych](sql-api-partition-data.md#migrating-from-single-partition).

    > [!NOTE]
    > Zmiana na standardowe S1, S2 lub S3 może zająć do dwóch minut.
    > 
    > 

**Aby przeprowadzić migrację do kolekcje z jedną partycją przy użyciu zestawu .NET SDK**

Inną opcją w przypadku zmiany poziomów wydajności z kolekcji jest za pomocą zestawów SDK z bazy danych Azure rozwiązania Cosmos. W tej sekcji opisano tylko zmiana wydajności kolekcji poziomu przy użyciu [interfejs API .NET SQL](sql-api-sdk-dotnet.md), ale proces jest podobny do naszych innych zestawów SDK.

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
