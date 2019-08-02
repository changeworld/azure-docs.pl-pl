---
title: Usługa Azure SQL Database warstwy Krytyczne dla działania firmy | Microsoft Docs
description: Dowiedz się więcej na temat warstwy Krytyczne dla działania firmy Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 208224e10c3acfb17bc5fd89d2d66152943811dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566749"
---
# <a name="business-critical-tier---azure-sql-database"></a>Warstwa Krytyczne dla działania firmy — Azure SQL Database

> [!NOTE]
> Warstwa Krytyczne dla działania firmy jest nazywana Premium w modelu zakupu jednostek DTU. Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](sql-database-purchase-models.md).

Azure SQL Database jest oparta na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane w Azure SQL Database:
- Ogólnego przeznaczenia/Standard 
- Krytyczne dla działania firmy/Premium
- Hiperskala

Model warstwy usług premium/Krytyczne dla działania firmy jest oparty na klastrze procesów aparatu bazy danych. Ten model architektury polega na tym, że zawsze jest kworum dostępnych węzłów aparatu bazy danych i ma minimalny wpływ na wydajność, nawet w trakcie czynności konserwacyjnych.

Platforma Azure uaktualnia i aktualizuje podstawowy system operacyjny, sterowniki oraz aparat SQL Server Database w sposób niewidoczny dla użytkowników końcowych w minimalnym czasie. 

Dostępność Premium jest włączona w warstwach usług premium i Krytyczne dla działania firmy Azure SQL Database i jest przeznaczona do intensywnych obciążeń, które nie mogą tolerować wpływu na wydajność wynikające z trwających operacji konserwacyjnych.

W modelu Premium usługa Azure SQL Database integruje obliczenia i magazyn w jednym węźle. Wysoka dostępność w tym modelu architektury jest osiągana przez replikację operacji obliczeniowych (SQL Server procesie aparatu bazy danych) i magazynu (lokalnie dołączony dysk SSD) wdrożonego w czterech węzłach klastra przy użyciu technologii podobnej do SQL Server [zawsze włączone grupy dostępności ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Klaster węzłów aparatu bazy danych](media/sql-database-managed-instance/business-critical-service-tier.png)

Zarówno proces aparatu bazy danych SQL, jak i bazowe pliki MDF/ldf są umieszczane w tym samym węźle, w którym lokalnie dołączony magazyn SSD zapewnia małe opóźnienia w obciążeniu. Wysoka dostępność jest implementowana przy użyciu technologii podobnego do SQL Server [zawsze włączonymi grupami dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Każda baza danych jest klastrem węzłów bazy danych z jedną podstawową bazą danych, która jest dostępna do obciążania klienta, oraz trzech procesów pomocniczych zawierających kopie danych. Węzeł podstawowy ciągle wypychanie zmian do węzłów pomocniczych w celu upewnienia się, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii z dowolnego powodu. Tryb failover jest obsługiwany przez aparat bazy danych SQL Server — jedna replika pomocnicza jest węzłem podstawowym, a Nowa replika pomocnicza jest tworzona w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego.

Ponadto klaster Krytyczne dla działania firmy ma wbudowaną funkcję [odczytu](sql-database-read-scale-out.md) w poziomie, która zapewnia bezpłatny, wbudowany węzeł tylko do odczytu, który może służyć do uruchamiania zapytań tylko do odczytu (na przykład raportów), które nie powinny wpływać na wydajność podstawowego roboczych.

## <a name="when-to-choose-this-service-tier"></a>Kiedy należy wybrać tę warstwę usług?

Krytyczne dla działania firmy warstwa usług została zaprojektowana dla aplikacji, które wymagają odpowiedzi o małym opóźnieniu z bazowego magazynu SSD (średnio 1-2 ms), szybkiego odzyskiwania w przypadku awarii infrastruktury podstawowej lub konieczności wyłączania raportów, analiz i tylko do odczytu. wykonuje zapytania dotyczące bezpłatnej repliki pomocniczej do odzyskania podstawowej bazy danych.

## <a name="next-steps"></a>Kolejne kroki

- Znajdź charakterystykę zasobu (liczbę rdzeni, operacji we/wy, pamięci) warstwy Krytyczne dla działania firmy w [wystąpieniu zarządzanym](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), pojedynczą bazę danych w [modelu rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) lub [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)lub elastyczną pulę w [modelu rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) i [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Dowiedz się więcej na temat warstw [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [skalowania](sql-database-service-tier-hyperscale.md) .
- Dowiedz się więcej na temat [Service Fabric](../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii [](sql-database-business-continuity.md), zobacz ciągłość działania.
