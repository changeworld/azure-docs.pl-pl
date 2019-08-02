---
title: Limity zasobów serwera Azure SQL Database | Microsoft Docs
description: Ten artykuł zawiera omówienie limitów zasobów serwera Azure SQL Database dla pojedynczych baz danych i pul elastycznych. Zawiera również informacje dotyczące tego, co się dzieje po osiągnięciu lub przekroczeniu limitów zasobów.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: 175f694cbe46f871349136c9ce91888b6de48d21
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566857"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limity zasobów SQL Database dla serwera Azure SQL Database

Ten artykuł zawiera omówienie limitów zasobów SQL Database dla serwera SQL Database, który zarządza pojedynczymi bazami danych i pulami elastycznymi. Zawiera również informacje dotyczące tego, co się dzieje po osiągnięciu lub przekroczeniu limitów zasobów.

> [!NOTE]
> W przypadku limitów wystąpień zarządzanych zapoznaj się z tematem [SQL Database limitów zasobów dla wystąpień zarządzanych](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maksymalne limity zasobów

| Resource | Limit |
| :--- | :--- |
| Bazy danych na serwer | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |  
| Przydział jednostek DTU/eDTU na serwer | 54,000 |  
| przydział rdzeń wirtualny na serwer/wystąpienie | 540 |
| Maksymalna liczba pul na serwer | Ograniczone przez liczbę DTU lub rdzeni wirtualnych. Na przykład jeśli każda pula ma 1000 DTU, serwer może obsługiwać pule 54.|
|||

> [!NOTE]
> Aby uzyskać więcej wartości przydziału/eDTU jednostek DTU, przydziału rdzeń wirtualny lub większej liczby serwerów niż domyślna, można przesłać nowe żądanie obsługi w Azure Portal dla subskrypcji z typem problemu "przydział". Limity przydziału jednostek DTU/eDTU i bazy danych na serwer ograniczają liczbę pul elastycznych na serwer.
> [!IMPORTANT]
> Ponieważ liczba baz danych zbliża się do limitu na serwer SQL Database, mogą wystąpić następujące elementy:
> - Zwiększanie opóźnień w uruchamianiu zapytań względem bazy danych Master.  Obejmuje to widoki statystyk wykorzystania zasobów, takich jak sys. resource_stats.
> - Zwiększenie opóźnienia w operacjach zarządzania i portalu renderowania, który obejmuje wyliczanie baz danych na serwerze.

### <a name="storage-size"></a>Rozmiar magazynu
- W przypadku pojedynczych baz danych rources zapoznaj się z limitami [zasobów opartymi na](sql-database-dtu-resource-limits-single-databases.md) jednostkach DTU lub [limitami zasobów opartymi na rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitów zasobów bazy danych

### <a name="compute-dtus-and-edtus--vcores"></a>Obliczenia (DTU i jednostek eDTU/rdzeni wirtualnych)

Gdy użycie obliczeniowe bazy danych (mierzone przez DTU i jednostek eDTU, lub rdzeni wirtualnych) stanie się wysokie, opóźnienia zapytań rosną i mogą nawet przekroczyć limit czasu. W tych warunkach zapytania mogą być umieszczane w kolejce przez usługę i są udostępniane zasoby do wykonania, ponieważ zasób staje się bezpłatny.
W przypadku wystąpienia dużej mocy obliczeniowej opcje ograniczenia obejmują:

- Zwiększenie rozmiaru obliczeniowego bazy danych lub puli elastycznej w celu zapewnienia bazy danych większej ilości zasobów obliczeniowych. Zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Optymalizowanie zapytań w celu zmniejszenia wykorzystania zasobów dla każdego zapytania. Aby uzyskać więcej informacji, zobacz [dostrajanie/podpowiedzi zapytań](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Magazyn

Gdy używane miejsce na bazę danych osiągnie limit rozmiaru, wstawia i aktualizuje bazę danych, która zwiększa niepowodzenie rozmiaru danych, a klienci odbierają [komunikat o błędzie](sql-database-develop-error-messages.md). WYBÓR i usunięcie bazy danych powiodło się.

W przypadku wystąpienia dużej ilości miejsca, opcje ograniczenia obejmują:

- Zwiększenie maksymalnego rozmiaru bazy danych lub elastycznej puli lub dodanie większej ilości miejsca do magazynowania. Zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Jeśli baza danych znajduje się w puli elastycznej, można przenieść bazę danych poza pulę, aby jej miejsce do magazynowania nie było udostępniane innym baz danych.
- Zmniejsz rozmiar bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sesje i procesy robocze (żądania)

Maksymalna liczba sesji i procesów roboczych zależy od warstwy usług i rozmiaru obliczeń (DTU i jednostek eDTU). Nowe żądania są odrzucane, gdy osiągnięto limity sesji lub procesu roboczego, a klienci otrzymają komunikat o błędzie. Liczba dostępnych połączeń może być kontrolowana przez aplikację, ale liczba współbieżnych procesów roboczych często jest trudniejsza do oszacowania i kontroli. Jest to szczególnie prawdziwe w przypadku szczytowych okresów ładowania, gdy osiągnięto limity zasobów bazy danych i procesy robocze są ustawiane z powodu dłuższych uruchomionych zapytań.

W przypadku wystąpienia wysokiego poziomu sesji lub procesu wyłączania, opcje ograniczenia obejmują:

- Zwiększenie warstwy usług lub rozmiaru obliczeniowego bazy danych lub puli elastycznej. Zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Optymalizowanie zapytań w celu ograniczenia wykorzystania zasobów dla każdego zapytania, jeśli przyczyną zwiększonego wykorzystania procesów roboczych jest rywalizacja o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [dostrajanie/podpowiedzi zapytań](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Ocena szybkości dziennika transakcji 
Współczynnik dziennika transakcji ładu to proces w Azure SQL Database używany do ograniczania wysokich szybkości pozyskiwania dla obciążeń takich jak wstawianie zbiorcze, wybieranie do i kompilacje indeksów. Limity te są śledzone i wymuszane na poziomie podrzędnym według szybkości generowania rekordów dziennika, ograniczając przepływność bez względu na to, ile systemu IOs można wystawić względem plików danych.  Stawki za generowanie dzienników transakcji są obecnie skalowane liniowie do punktu, który jest zależny od sprzętu, a maksymalna szybkość rejestrowania może być 96 MB/s z modelem zakupów rdzeń wirtualny. 

> [!NOTE]
> Rzeczywiste fizyczne pliki dziennika transakcji w systemie IOs nie podlegają ograniczeniom ani nie są ograniczone. 

Stawki dzienników są ustawiane tak, aby można je było osiągnąć i utrzymywać w różnych scenariuszach, podczas gdy ogólny system może utrzymywać swoją funkcjonalność przy minimalnym wpływie na obciążenie użytkownikami. Częstotliwość rejestrowania gwarantuje, że kopie zapasowe dziennika transakcji pozostają w ramach opublikowanej umowy SLA.  To zarządzanie uniemożliwia również nadmierne zaległości w replikach pomocniczych.

Ponieważ rekordy dziennika są generowane, każda operacja jest oceniana i oceniana pod kątem tego, czy należy opóźnić, aby zachować maksymalny żądany współczynnik dziennika (MB/s na sekundę). Opóźnienia nie są dodawane, gdy rekordy dziennika są opróżniane do magazynu, podczas generowania stawki dziennika są stosowane zasady zarządzania.

Rzeczywiste stawki za generowanie dzienników nałożone w czasie wykonywania mogą również mieć wpływ na mechanizmy przesyłania opinii, tymczasowo zmniejszając dopuszczalne stawki dzienników, dzięki czemu system może być stabilny. Zarządzanie miejscem w pliku dziennika, unikanie uruchamiania w warunkach wolnego miejsca w dzienniku i mechanizmów replikacji grupy dostępności może tymczasowo obniżyć ogólne limity systemu. 

Współczynnik dziennika: kształtowanie ruchu gubernatora jest przedstawiane przez następujące typy oczekiwania (uwidocznione w pliku [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ oczekiwania | Uwagi |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Ograniczanie bazy danych |
| POOL_LOG_RATE_GOVERNOR | Ograniczanie puli |
| INSTANCE_LOG_RATE_GOVERNOR | Ograniczanie poziomu wystąpienia |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Kontrola opinii, replikacja fizyczna grupy dostępności w warstwie Premium/Krytyczne dla działania firmy nie jest zachowywana |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Kontrola opinii, ograniczanie stawek aby uniknąć braku miejsca w dzienniku |
|||

W przypadku napotkania limitu szybkości rejestrowania, który hamuje pożądaną skalowalność, należy wziąć pod uwagę następujące opcje:
- Skaluj w górę do większej warstwy w celu uzyskania maksymalnej liczby dzienników 96 MB/s. 
- Jeśli ładowanie danych jest przejściowe, tj. dane przejściowe w procesie ETL, można je załadować do bazy danych tempdb (co jest w minimalnym stopniu rejestrowane). 
- W przypadku scenariuszy analitycznych Załaduj do tabeli objętej usługą magazynu kolumn. Zmniejsza to wymaganą szybkość rejestrowania z powodu kompresji. Ta technika zwiększa wykorzystanie procesora CPU i ma zastosowanie tylko do zestawów danych, które korzystają z klastrowanych indeksów magazynu kolumn. 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat DTU i jednostek eDTU, zobacz [DTU i jednostek eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Aby uzyskać informacje na temat limitów rozmiaru bazy danych tempdb, zobacz [tempdb w Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
