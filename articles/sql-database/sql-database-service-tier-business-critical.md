---
title: Warstwa usług o krytycznym znaczeniu dla firmy
description: Dowiedz się więcej o warstwie krytyczna dla usługi Azure SQL Database Business
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
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268680"
---
# <a name="business-critical-tier---azure-sql-database"></a>Warstwa krytyczna dla firmy — baza danych SQL platformy Azure

> [!NOTE]
> Warstwa Krytyczna dla firmy jest nazywana premium w modelu zakupu y DTU. Aby zapoznać się z porównaniem modelu zakupów opartego na procesorach vCore z modelem zakupów opartym na USŁUDZE DTU, zobacz [Tworzenie modeli i zasobów w bazie danych SQL usługi Azure .](sql-database-purchase-models.md)

Usługa Azure SQL Database jest oparta na architekturze aparatu bazy danych programu SQL Server, która jest dostosowana do środowiska w chmurze, aby zapewnić dostępność 99,99% nawet w przypadku awarii infrastruktury. Istnieją trzy modele architektury, które są używane w usłudze Azure SQL Database:
- Ogólnego przeznaczenia/standardu 
- Krytyczna dla biznesu/Premium
- Hiperskala

Model warstwy usług Premium/Business Critical jest oparty na klastrze procesów aparatu bazy danych. Ten model architektury opiera się na fakt, że zawsze istnieje kworum dostępnych węzłów aparatu bazy danych i ma minimalny wpływ na wydajność obciążenia nawet podczas czynności konserwacyjnych.

Uaktualnienia i poprawki platformy Azure podstawowy system operacyjny, sterowniki i sql server database engine w sposób przejrzysty z minimalnym czasie przestoju dla użytkowników końcowych. 

Dostępność w warstwie Premium jest włączona w warstwach usług Usługi Azure SQL Database w warstwach Premium i Krytycznych dla przedsiębiorstw i jest przeznaczona dla intensywnych obciążeń, które nie tolerują wpływu na wydajność z powodu trwających operacji konserwacji.

W modelu premium baza danych SQL platformy Azure integruje zasoby obliczeniowe i magazyn w jednym węźle. Wysoka dostępność w tym modelu architektonicznym jest osiągana przez replikację obliczeń (proces aparatu baz danych programu SQL Server) i magazyn (lokalnie dołączony dysk SSD) wdrożonych w klastrze czterech węzłów przy użyciu technologii podobnej do grup dostępności programu SQL Server [Always On Availability .](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

![Klaster węzłów aparatu bazy danych](media/sql-database-managed-instance/business-critical-service-tier.png)

Zarówno proces aparatu bazy danych SQL, jak i podstawowe pliki mdf/ldf są umieszczane w tym samym węźle z lokalnie dołączoną pamięcią SSD zapewniającą małe opóźnienia obciążenia. Wysoka dostępność jest implementowana przy użyciu technologii podobnej do grup dostępności programu SQL Server [Always On Availability.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Każda baza danych jest klastrem węzłów bazy danych z jedną podstawową bazą danych, która jest dostępna dla obciążenia klienta i trzema pomocniczymi procesami zawierającymi kopie danych. Węzeł podstawowy stale wypycha zmiany do węzłów pomocniczych w celu zapewnienia, że dane są dostępne w replikach pomocniczych, jeśli węzeł podstawowy ulegnie awarii z jakiegokolwiek powodu. Praca awaryjna jest obsługiwana przez aparat bazy danych programu SQL Server — jedna replika pomocnicza staje się węzłem podstawowym, a nowa replika pomocnicza jest tworzona w celu zapewnienia wystarczającej liczby węzłów w klastrze. Obciążenie jest automatycznie przekierowywane do nowego węzła podstawowego.

Ponadto klaster o znaczeniu krytycznym dla firmy ma wbudowaną funkcję [skalowania w poziomie odczytu,](sql-database-read-scale-out.md) która zapewnia bezpłatny wbudowany węzeł tylko do odczytu, który może służyć do uruchamiania zapytań tylko do odczytu (na przykład raportów), które nie powinny mieć wpływu na wydajność obciążenia podstawowego.

## <a name="when-to-choose-this-service-tier"></a>Kiedy wybrać tę warstwę usług?

Warstwa usług o krytycznym znaczeniu dla firmy jest przeznaczona dla aplikacji, które wymagają odpowiedzi o małym opóźnieniu z podstawowej pamięci masowej SSD (średnio 1-2 ms), szybkiego odzyskiwania w przypadku awarii podstawowej infrastruktury lub konieczności wyłączania raportów, analiz i tylko do odczytu zapytania do bezpłatnej, czytelnej repliki pomocniczej podstawowej bazy danych.

Główne powody, dla których należy wybrać warstwę usług o krytycznym znaczeniu dla firmy zamiast warstwy ogólnego przeznaczenia, to:
-   Wymagania dotyczące niskich opóźnień we/wy — obciążenie, które wymaga szybkiej reakcji z warstwy magazynu (średnio 1-2 milisekundy) powinno używać warstwy Krytyczne dla firmy. 
-   Częsta komunikacja między aplikacją a bazą danych. Aplikacja, która nie może wykorzystać buforowania warstwy aplikacji lub [żądania przetwarzania wsadowego](sql-database-use-batching-to-improve-performance.md) i trzeba wysłać wiele zapytań SQL, które muszą być szybko przetwarzane są dobrymi kandydatami do warstwy krytycznej firmy.
-   Duża liczba aktualizacji — operacje wstawiania, aktualizowania i usuwania modyfikują strony `CHECKPOINT` danych w pamięci (brudną stronę), które muszą być zapisane w plikach danych z działaniem. Awaria procesu potencjalnego aparatu bazy danych lub praca awaryjna bazy danych z dużą liczbą brudnych stron może wydłużyć czas odzyskiwania w warstwie ogólnego przeznaczenia. Użyj warstwy Krytyczne dla firmy, jeśli masz obciążenie, które powoduje wiele zmian w pamięci. 
-   Długotrwałe transakcje, które modyfikują dane. Transakcje, które są otwierane przez dłuższy czas, uniemożliwiają obcięcie pliku dziennika, które może zwiększyć rozmiar dziennika i liczbę [plików dziennika wirtualnego (VLF).](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch) Duża liczba VLF może spowolnić odzyskiwanie bazy danych po pracy awaryjnej.
-   Obciążenie z raportowania i zapytań analitycznych, które mogą być przekierowywane do bezpłatnej repliki pomocniczej tylko do odczytu.
- Większa odporność i szybsze odzyskiwanie po awarii. W przypadku awarii systemu baza danych w wystąpieniu podstawowym zostanie wyłączona, a jedna z replik pomocniczych zostanie natychmiast stała się nową podstawową bazą danych odczytu i zapisu, która jest gotowa do przetwarzania zapytań. Aparat bazy danych nie trzeba analizować i ponawiać transakcje z pliku dziennika i załadować wszystkie dane w buforze pamięci.
- Zaawansowana ochrona przed uszkodzeniem danych — warstwa Business Critical wykorzystuje repliki baz danych za kulisami do celów ciągłości biznesowej, a więc usługa wykorzystuje również automatyczną naprawę stron, która jest tą samą technologią, która jest tą samą technologią, która jest tą samą technologią, która jest używana w [grupach dublowania i dostępności](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)bazy danych programu SQL Server. W przypadku, gdy replika nie może odczytać strony z powodu problemu z integralnością danych, nowa kopia strony zostanie pobrana z innej repliki, zastępując nieczytelną stronę bez utraty danych lub przestoju klienta. Ta funkcja ma zastosowanie w warstwie ogólnego przeznaczenia, jeśli baza danych ma replikę pomocniczą.
- Wyższa dostępność — warstwa Krytyczna dla firmy w konfiguracji Multi-AZ gwarantuje dostępność na poziomie 99,995% w porównaniu z 99,99% warstwy ogólnego przeznaczenia.
- Szybkie odzyskiwanie geograficzne — warstwa Krytyczna dla firmy skonfigurowana z replikacją geograficzną ma gwarantowany cel punktu odzyskiwania (RPO) 5 sekund i cel czasu odzyskiwania (RTO) 30 sekund dla 100% wdrożonych godzin.

## <a name="next-steps"></a>Następne kroki

- Znajdź charakterystykę zasobów (liczba rdzeni, we/wy, pamięć) warstwy Krytyczna dla firmy w [wystąpieniu zarządzanym,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Pojedyncza baza danych w [modelu rdzeni wirtualnych](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) lub [modelu DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)lub Pula elastyczna w [modelu rdzeni wirtualnych](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) i [modelu jednostek DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Dowiedz się więcej o [warstwach Ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [hiperskali.](sql-database-service-tier-hyperscale.md)
- Dowiedz się więcej o [sieci szkieletowej usług](../service-fabric/service-fabric-overview.md).
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, zobacz [Ciągłość działania](sql-database-business-continuity.md).
