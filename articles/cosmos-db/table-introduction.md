---
title: Wprowadzenie do interfejsu API tabel usługi Azure Cosmos DB
description: Dowiedz się, jak korzystać z usługi Azure Cosmos DB i interfejsu API tabel platformy Azure w celu przechowywania ogromnych wolumenów danych typu klucz-wartość i wykonywania zapytań na tych danych przy zachowaniu małych opóźnień.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: bd86b230d801f5fff8a9fb0de85f9f3025527382
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953455"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Wprowadzenie do usługi Azure Cosmos DB: Interfejs API tabel

Usługa [Azure Cosmos DB](introduction.md) zapewnia interfejs API tabel dla aplikacji korzystających z usługi Azure Table Storage, które wymagają funkcji warstwy Premium, takich jak:

* [Gotowa do użytku dystrybucja globalna](distribute-data-globally.md).
* [Dedykowana przepływność](partition-data.md) na całym świecie.
* Opóźnienie rzędu kilku milisekund na poziomie 99. percentyla.
* Gwarantowana wysoka dostępność.
* [Automatyczne indeksowanie pomocnicze](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Aplikacje korzystające z usługi Azure Table Storage mogą być migrowane do usługi Azure Cosmos DB przy użyciu interfejsu API tabel bez zmian kodu i mogą korzystać z funkcji warstwy Premium. Interfejs API tabel ma zestawy SDK klienta dostępne dla platform .NET, Java, Python i Node.js.

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) jest w trybie konserwacji i tryb wkrótce staną się przestarzałe. Przeprowadź uaktualnienie do nowej biblioteki .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) w dalszym ciągu uzyskać najnowsze funkcje, które są obsługiwane przez interfejs API tabel.

## <a name="table-offerings"></a>Oferty dotyczące tabel
Jeśli obecnie używasz usługi Azure Table Storage, po rozpoczęciu korzystania z interfejsu API tabel usługi Azure Cosmos DB uzyskasz następujące korzyści:

| | Azure Table Storage | Interfejs API tabel usługi Azure Cosmos DB |
| --- | --- | --- |
| Opóźnienie | Niewielkie, ale brak górnych granic opóźnienia. | Milisekundowe opóźnienie odczytu i zapisu oraz odczyt o opóźnieniu <10 ms i zapis o opóźnieniu <15 ms w 99. percentylu, w dowolnej skali, w dowolnym miejscu na świecie. |
| Przepływność | Zmienny model przepływności. Tabele mają limit skalowalności 20 000 operacji/s. | Wysoka skalowalność dzięki [dedykowanej zarezerwowanej przepływności na tabelę](request-units.md), gwarantowanej umowami SLA. Konta nie mają górnego limitu przepływności i obsługują >10 milionów operacji/s na tabelę. |
| Dystrybucja globalna | Jeden region z jednym opcjonalnym dodatkowym regionem odczytu, co zapewnia wysoką dostępność. Nie można zainicjować trybu failover. | [Kompleksowa dystrybucja globalna](distribute-data-globally.md) do ponad 30 regionów. Obsługa [automatycznego i ręcznego trybu failover](high-availability.md) w dowolnym momencie i każdym miejscu na świecie. |
| Indeksowanie | Tylko podstawowy indeks PartitionKey i RowKey. Brak dodatkowych indeksów. | Automatyczne i kompletne indeksowanie wszystkich właściwości, brak zarządzania indeksem. |
| Zapytanie | Wykonanie zapytania wykorzystuje indeks klucza podstawowego, a w przeciwnym przypadku skanuje. | Zapytania mogą korzystać z automatycznego indeksowania właściwości, co skraca czas odpowiedzi. |
| Spójność | Na poziomie „strong” w regionie podstawowym, na poziomie „eventual” w regionie pomocniczym. | [Pięć dobrze zdefiniowanych poziomów spójności](consistency-levels.md), równoważących dostępność, opóźnienia, przepływność i spójność w zależności od potrzeb aplikacji. |
| Cennik | Optymalizacja pod kątem magazynu. | Optymalizacja pod kątem przepływności. |
| Umowy SLA | Dostępność na poziomie 99,99%. | Umowa SLA gwarantująca dostępność na poziomie co najmniej 99,99% dla wszystkich kont w obrębie jednego regionu i wszystkich kont w wielu regionach w przypadku rozluźnionej spójności, a także dostępność do odczytu na poziomie co najmniej 99,999% dla wszystkich kont bazy danych w wielu regionach w ramach [wiodących w branży, kompleksowych umów SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) dotyczących ogólnej dostępności. |

## <a name="get-started"></a>Rozpoczęcie pracy

Utwórz konto usługi Azure Cosmos DB w witrynie [Azure Portal](https://portal.azure.com). Następnie zapoznaj się z naszym [przewodnikiem Szybki Start dotyczącym korzystania z interfejsu API tabel przy użyciu programu .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Jeśli utworzono konto interfejsu API tabel w trakcie okresu próbnego, utwórz [nowe konto interfejsu API tabel](create-table-dotnet.md#create-a-database-account), aby móc korzystać z ogólnie dostępnych zestawów SDK interfejsu API tabel.
>

## <a name="next-steps"></a>Następne kroki

Oto kilka wskazówek ułatwiających rozpoczęcie pracy:
* [Tworzenie aplikacji .NET za pomocą interfejsu API tabel](create-table-dotnet.md)
* [Opracowywanie zawartości przy użyciu interfejsu API tabel na platformie .NET](tutorial-develop-table-dotnet.md)
* [Wykonywanie zapytań dotyczących danych tabel przy użyciu interfejsu API tabel](tutorial-query-table.md)
* [Dowiedz się, jak skonfigurować dystrybucję globalną usługi Azure Cosmos DB przy użyciu interfejsu API tabel](tutorial-global-distribution-table.md)
* [Azure Cosmos DB — interfejs API tabel dla platformy .NET](table-sdk-dotnet.md)
* [Azure Cosmos DB — interfejs API tabel dla platformy Java](table-sdk-java.md)
* [Azure Cosmos DB — interfejs API tabel dla platformy Node.js](table-sdk-nodejs.md)
* [Azure Cosmos DB — zestaw SDK tabel dla języka Python](table-sdk-python.md)

