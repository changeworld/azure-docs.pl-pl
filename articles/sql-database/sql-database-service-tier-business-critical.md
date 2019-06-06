---
title: Warstwa biznesowa krytyczne — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat warstwy usługi Azure SQL Database krytyczne dla działania firmy
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 90989a9105405f1784b3be9ab59f55cd3433feaf
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479226"
---
# <a name="business-critical-tier---azure-sql-database"></a>Warstwa biznesowa krytyczne — usługi Azure SQL Database

> [!NOTE]
> Warstwie krytyczne dla działania firmy nosi nazwę wersji Premium w model zakupu jednostek DTU. Dla porównania modelu zakupu opartego na rdzeniach wirtualnych za pomocą modelu zakupu opartego na jednostkach DTU, zobacz [zakupu modeli i zasobów bazy danych SQL Azure](sql-database-purchase-models.md).

Usługa Azure SQL Database jest oparty na architekturę aparatu bazy danych programu SQL Server, która jest uwzględniany w środowisku chmury w celu zapewnienia dostępności 99,99%, nawet w przypadku wystąpienia awarii infrastruktury. Istnieją trzy modele architektury, które są używane w usłudze Azure SQL Database:
- Ogólnego przeznaczenia/Standard 
- Business Critical/Premium
- Hiperskala

Premium/krytyczne modelu warstwy usług opiera się na klastrze procesy aparatu bazy danych. Ten model architektury opiera się na fakt, że jest zawsze kworum węzłów aparatu bazy danych dostępności i ma negatywny wpływ na wydajność minimalne obciążenie nawet w trakcie czynności konserwacyjnych.

Platforma Azure uaktualnia i poprawek podstawowego systemu operacyjnego, sterowników i aparatu bazy danych programu SQL Server sposób niewidoczny dla użytkownika za pomocą minimalny czas przestoju dla użytkowników końcowych. 

Dostępność — wersja Premium jest włączona w warstwach usług Premium i krytyczne dla działania firmy z usługi Azure SQL Database i jest ona przeznaczona dla dużych obciążeń, które nie tolerują żadnego wpływu na wydajność, z powodu operacji konserwacji.

W modelu premium bazy danych Azure SQL database integruje zasobów obliczeniowych i magazynowych w pojedynczym węźle. Wysoka dostępność w tym modelu architektury jest osiągana przez funkcję replikacji obliczeniowej (proces aparatu bazy danych programu SQL Server) i pamięci masowej (SSD podłączonych lokalnie), które zostały wdrożone w czterech węzłów klastra, przy użyciu technologii podobne do programu SQL Server [Always On Grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Klastra węzłów aparatu bazy danych](media/sql-database-managed-instance/business-critical-service-tier.png)

Zarówno SQL bazy danych, proces aparatu i podstawowych plików mdf/ldf są umieszczane w tym samym węźle za pomocą podłączonych lokalnie magazynu SSD zapewnianie małych opóźnień do obciążenia. Wysoka dostępność jest implementowany przy użyciu technologii podobne do programu SQL Server [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Każda baza danych działa w klastrze z węzłów bazy danych przy użyciu jednej podstawowej bazy danych, który jest dostępny dla obciążenia klientów i trzy procesy dodatkowej zawierającego kopie danych. Węzeł podstawowy stale wypychanie zmian do węzłów pomocniczych w celu zapewnienia, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii jakiegokolwiek powodu. Tryb failover odbywa się przez aparat bazy danych programu SQL Server — jedna replika pomocnicza staje się węzeł podstawowy i nową replikę pomocniczą jest utworzone w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego.

Ponadto takie aplikacje zawierają wbudowany w klaster krytyczne dla działania firmy [odczytu skalowalnego w poziomie](sql-database-read-scale-out.md) funkcją, która udostępnia bezpłatnie z jest opłata w wysokości wbudowanego węzła tylko do odczytu, który może służyć do uruchamiania tylko do odczytu zapytań (na przykład raporty), które nie powinny mieć wpływ na wydajność podstawowego obciążenia.

## <a name="when-to-choose-this-service-tier"></a>Kiedy należy wybrać tej warstwie usługi?

Warstwy usług krytycznych biznesowych jest przeznaczona dla aplikacji, które wymagają małego opóźnienia odpowiedzi z podstawowym magazynem dysków SSD (1 – 2 ms w średniej), szybkie odzyskiwanie w przypadku awarii podstawowej infrastruktury ani konieczności kolejkowego raporty, analizy i tylko do odczytu zapytania, które są wolne od opłat do odczytu repliki pomocniczej podstawowej bazy danych.

## <a name="next-steps"></a>Kolejne kroki

- Znajdź właściwości zasobów (liczba rdzeni, we/wy, pamięć) warstwy krytyczne dla działania firmy w [wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), pojedyncze bazy danych w [modelu rdzenia wirtualnego](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) lub [modelu jednostek DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), lub elastyczne Pula w [modelu rdzenia wirtualnego](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) i [modelu jednostek DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Dowiedz się więcej o [ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [Hiperskali](sql-database-service-tier-hyperscale.md) warstw.
- Dowiedz się więcej o [usługi Service Fabric](../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość prowadzenia działalności biznesowej](sql-database-business-continuity.md).
