---
title: Warstwa usług ogólnego przeznaczenia
description: Dowiedz się więcej o warstwie ogólnego przeznaczenia usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255888"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Warstwa usług ogólnego przeznaczenia — usługa Azure SQL Database

> [!NOTE]
> Warstwa usług ogólnego przeznaczenia w modelu zakupów opartym na corach wirtualnych jest nazywana standardową warstwą usług w modelu zakupu opartym na USŁUDZE DTU. Aby zapoznać się z porównaniem modelu zakupów opartego na procesorach vCore z modelem zakupów opartym na USŁUDZE DTU, zobacz [Tworzenie modeli i zasobów w bazie danych SQL usługi Azure .](sql-database-purchase-models.md)

Usługa Azure SQL Database jest oparta na architekturze aparatu bazy danych programu SQL Server dostosowanej do środowiska w chmurze, aby zapewnić dostępność na 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy warstwy usług, które są używane w usłudze Azure SQL Database, z których każda ma różne modele architektury. Te warstwy usług są następujące:

- Zastosowania ogólne
- Krytyczne dla biznesu
- Hiperskala

Model architektury dla warstwy usług ogólnego przeznaczenia opiera się na separacji danych obliczeniowych i magazynu. Ten model architektury opiera się na wysokiej dostępności i niezawodności magazynu obiektów Blob platformy Azure, który w sposób przejrzysty replikuje pliki bazy danych i gwarantuje brak utraty danych w przypadku awarii podstawowej infrastruktury.

Na poniższej ilustracji przedstawiono cztery węzły w standardowym modelu architektonicznym z oddzielonymi warstwami obliczeniowymi i magazynowymi.

![Oddzielenie danych obliczeniowych i pamięci masowej](media/sql-database-managed-instance/general-purpose-service-tier.png)

W modelu architektury dla warstwy usług ogólnego przeznaczenia istnieją dwie warstwy:

- Bezstanowa warstwa obliczeniowa, `sqlservr.exe` która jest uruchomiona proces i zawiera tylko dane przejściowe i buforowane (na przykład — plan pamięci podręcznej, puli buforów, puli magazynu kolumn). Ten bezstanowy węzeł programu SQL Server jest obsługiwany przez usługę Azure Service Fabric, która inicjuje proces, kontroluje kondycję węzła i w razie potrzeby wykonuje funkcję failover w innym miejscu.
- Stanowa warstwa danych z plikami bazy danych (.mdf/.ldf), które są przechowywane w magazynie obiektów Blob platformy Azure. Usługa Azure Blob storage gwarantuje, że nie będzie utraty danych dowolnego rekordu, który jest umieszczany w dowolnym pliku bazy danych. Usługa Azure Storage ma wbudowaną dostępność/nadmiarowość danych, która zapewnia, że każdy rekord w pliku dziennika lub stronie w pliku danych zostanie zachowany, nawet jeśli proces programu SQL Server ulegnie awarii.

Za każdym razem, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony, część podstawowej infrastruktury ulegnie awarii lub jeśli w procesie programu SQL Server zostanie wykryty jakiś krytyczny problem, usługa Azure Service Fabric przeniesie bezstanowy proces programu SQL Server do innego bezstanowego węzła obliczeniowego. Istnieje zestaw węzłów zapasowych, które oczekują na uruchomienie nowej usługi obliczeniowej, jeśli nastąpi przebłajenie awaryjne węzła podstawowego w celu zminimalizowania czasu pracy awaryjnej. Dane w warstwie magazynu platformy Azure nie ma wpływu, a pliki danych/dziennika są dołączone do nowo zainicjowany proces SQL Server. Ten proces gwarantuje dostępność 99,99%, ale może mieć pewien wpływ na wydajność dużego obciążenia, które jest uruchomione ze względu na czas przejścia i fakt, że nowy węzeł programu SQL Server rozpoczyna się od zimnej pamięci podręcznej.

## <a name="when-to-choose-this-service-tier"></a>Kiedy wybrać tę warstwę usług

Warstwa usług ogólnego przeznaczenia jest domyślną warstwą usług w usłudze Azure SQL Database, która jest przeznaczona dla większości obciążeń ogólnych. Jeśli potrzebujesz w pełni zarządzanego aparatu bazy danych z 99,99% SLA z opóźnieniem magazynu od 5 do 10 ms, które są zgodne z usługą Azure SQL IaaS w większości przypadków, warstwa ogólnego przeznaczenia jest opcją dla Ciebie.

## <a name="next-steps"></a>Następne kroki

- Znajdź charakterystykę zasobu (liczba rdzeni, we/wy, pamięć) warstwy ogólnego przeznaczenia/standardu w [wystąpieniu zarządzanym,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)pojedyncza baza danych w [modelu rdzeni wirtualnych](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) lub [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)lub pula elastyczna w [modelu rdzeni wirtualnych](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) i [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Dowiedz się więcej o warstwach Krytyczne dla [firm](sql-database-service-tier-business-critical.md) i [Hiperskala.](sql-database-service-tier-hyperscale.md)
- Dowiedz się więcej o [sieci szkieletowej usług](../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, zobacz [Ciągłość działania](sql-database-business-continuity.md).
