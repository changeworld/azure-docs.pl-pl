---
title: Wycofane poziomy wydajności usługi Azure Cosmos DB
description: Więcej informacji na temat S1, S2 i S3 poziomów wydajności dostępnej w usłudze Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60928292"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Wycofanie poziomów wydajności S1, S2 i S3

> [!IMPORTANT] 
> Poziomy wydajności S1, S2 i S3 omówionych w tym artykule są zostanie wycofana i nie są już dostępne dla nowych kont usługi Azure Cosmos DB.
>

Ten artykuł zawiera omówienie poziomów wydajności S1, S2 i S3, a w tym artykule omówiono, jak kolekcje, korzystających z tych poziomów wydajności może być migrowane do jednej kolekcji podzielonych na partycje. Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

- [Dlaczego są poziomy wydajności S1, S2 i S3 są zostanie wycofana?](#why-retired)
- [Jak kolekcji z jedną partycją i kolekcji podzielonych na partycje wypadają w porównaniu do S1, S2 i S3 poziomów wydajności?](#compare)
- [Co należy zrobić, aby upewnić się, co zapewnia nieprzerwany dostęp do moich danych?](#uninterrupted-access)
- [Jak zmieni się mojej kolekcji po zakończeniu migracji?](#collection-change)
- [Jak zmieni się Moje rozliczenie, po przeprowadzeniu jestem migracji kolekcji z jedną partycją?](#billing-change)
- [Co się stanie, jeśli potrzebuję więcej niż 10 GB magazynu?](#more-storage-needed)
- [Czy mogę zmienić między S1, S2 i S3 poziomów wydajności przed planowanej migracji?](#change-before)
- [Jak przeprowadzić migrację z S1, S2 i S3 poziomach wydajności do kolekcji z jedną partycją na własną rękę?](#migrate-diy)
- [Jak mam wpływ, jeśli jestem klientem z umową EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Dlaczego to wydajności S1, S2 i S3 poziomy zostanie wycofana?

Poziomy wydajności S1, S2 i S3 nie oferują elastyczność, która zapewnia standardowej oferty w usłudze Azure Cosmos DB. Za pomocą wersji S1, S2 i S3 poziomów wydajności, przepływność oraz Magazyn pojemności zostały wstępnie ustawione i nie zaproponował elastyczność. Usługa Azure Cosmos DB oferuje teraz możliwość dostosowania Twojego przepływności i magazynu, oferując większą elastyczność możliwość skalować stosownie do potrzeb.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Jak kolekcji z jedną partycją i kolekcji podzielonych na partycje wypadają w porównaniu do S1, S2 i S3 poziomów wydajności?

W poniższej tabeli porównano opcje przepływności i magazynu, które muszą być dostępne w kolekcji podzielonych na partycje i S1, S2 i S3 poziomy wydajności w kolekcji z jedną partycją. Oto przykład dla regionu wschodnie stany USA 2:

|   |Kolekcji partycjonowanej|Kolekcja jednej partycji|S1|S2|S3|
|---|---|---|---|---|---|
|Maksymalna przepływność|Nieograniczona liczba|10 K jednostek RU/s|250 RU/s|1 K jednostek RU/s|2.5 tys. jednostek RU/s|
|Przepływność minimalna|2.5 tys. jednostek RU/s|400 RU/s|250 RU/s|1 K jednostek RU/s|2.5 tys. jednostek RU/s|
|Pamięci masowej|Nieograniczona liczba|10 GB|10 GB|10 GB|10 GB|
|Cena (miesięcznie)|Przepływność: $6 / 100 jednostek RU/s<br><br>Miejsca do magazynowania: 0,25 USD/GB|Przepływność: $6 / 100 jednostek RU/s<br><br>Miejsca do magazynowania: 0,25 USD/GB|25 USD|50 USD|100 USD|

Jesteś klientem z umową EA? Jeśli tak, zobacz [jak am I ulec zmianie, jeśli jestem klientem z umową EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Co należy zrobić, aby upewnić się, co zapewnia nieprzerwany dostęp do moich danych?

Jeśli masz kolekcję S1, S2 lub S3, należy zmigrować kolekcję do kolekcji jednej partycji programowo [przy użyciu zestawu .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Jak zmieni się mojej kolekcji po zakończeniu migracji?

Jeśli masz kolekcję S1, można migrować je do kolekcji jednej partycji z 400 jednostek RU/s przepływności. 400 jednostek RU/s jest najniższy przepływność dostępne z kolekcji z jedną partycją. Jednak koszt 400 jednostek RU/s w kolekcji z jedną partycją to około takie same jak zostały płacenia za pomocą kolekcji S1 i 250 jednostek RU/s —, więc nie płacisz za bardzo 150 jednostek RU/s dostępne dla Ciebie.

W przypadku kolekcji S2 można migrować je do kolekcji jednej partycji z 1 K jednostek RU/s. Zostanie wyświetlony bez zmian do poziomu przepływności.

W przypadku kolekcji S3 można migrować je do kolekcji jednej partycji z 2,5 tysięcy jednostek RU/s. Zostanie wyświetlony bez zmian do poziomu przepływności.

W każdym z tych przypadków po przeprowadzeniu migracji kolekcji, można dostosować poziom przepływności lub przeskalujesz ją w górę i w dół odpowiednio do potrzeb zapewnienie o małych opóźnieniach dostępu dla użytkowników. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Jak zmieni się Moje rozliczenie, po przeprowadzeniu migracji kolekcji z jedną partycją?

Zakładając, że możesz mieć 10 kolekcji S1, 1 GB magazynu dla każdej w regionie wschodnie stany USA i migracji tych 10 kolekcji S1 do kolekcji z jedną partycją 10 na 400 jednostek żądań na sekundę (minimalny poziom). Jeśli trzymasz 10 kolekcji z jedną partycją dla całego miesiąca, rachunek będzie wyglądać następująco:

![Jak S1 cennika 10 kolekcji porównuje 10 kolekcji przy użyciu ceny dla kolekcji z jedną partycją](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>Co się stanie, jeśli potrzebuję więcej niż 10 GB magazynu?

Kolekcji z poziomem wydajności S1, S2 lub S3, czy masz kolekcję jednej partycji, które mają 10 GB miejsca dostępne narzędzie migracji danych systemu Azure Cosmos DB służy do migracji danych do kolekcji partycjonowanej z praktycznie nieograniczony magazyn. Aby uzyskać informacje o zaletach kolekcji partycjonowanej, zobacz [partycjonowanie i skalowanie w usłudze Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Czy mogę zmienić między S1, S2 i S3 poziomów wydajności przed planowanej migracji?

Tylko istniejących kont o wydajności S1, S2 i S3 można zmienić i programowo zmienić poziom warstwy wydajności [przy użyciu zestawu .NET SDK](#migrate-diy). W przypadku zmiany z S1 S3 i S3 do pojedynczej partycji kolekcji nie może zwrócić się do poziomów wydajności S1, S2 lub S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Jak przeprowadzić migrację z S1, S2 i S3 poziomach wydajności do kolekcji z jedną partycją na własną rękę?

Można migrować z poziomów wydajności S1, S2 i S3 do kolekcji z jedną partycją programowo [przy użyciu zestawu .NET SDK](#migrate-diy). Można to zrobić na własną rękę przed planowanej migracji, aby korzystać z elastycznych przepływności opcjach kolekcji z jedną partycją.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrowanie do kolekcji z jedną partycją przy użyciu zestawu .NET SDK

W tej sekcji opisano tylko zmiana wydajność zbierania danych na poziomie przy użyciu [interfejsie SQL .NET API](sql-api-sdk-dotnet.md), ale proces jest podobny do naszych zestawów SDK.

Poniżej przedstawiono fragment kodu dotyczący zmiana przepływności kolekcji do 5000 jednostek żądań na sekundę:
    
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

Odwiedź stronę [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) Aby wyświetlić dodatkowe przykłady i Dowiedz się więcej o metodach naszej oferty:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Jak mam wpływ, jeśli jestem klientem z umową EA?

Klienci z umową EA będą cena chronionego aż do końca ich bieżącego kontraktu.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat cen i zarządzanie danymi za pomocą usługi Azure Cosmos DB, zapoznaj się z tymi zasobami:

1.  [Partycjonowanie danych w usłudze Cosmos DB](sql-api-partition-data.md). Należy zrozumieć różnicę między kontenerów w jednej partycji i kontenery podzielonym na partycje, a także wskazówki dotyczące implementowania strategii partycjonowania, który pozwala bezproblemowo skalować.
2.  [Cennik usługi cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Dowiedz się więcej o koszcie obsługi administracyjnej przepływności i korzystanie z magazynu.
3.  [Jednostki żądań](request-units.md). Dowiedz się, zużycie przepustowości dla różnych operacji typów, na przykład Odczyt, zapis, zapytania.
