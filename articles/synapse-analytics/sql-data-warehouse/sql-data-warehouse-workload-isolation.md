---
title: Izolacja obciążeń
description: Wskazówki dotyczące ustawiania izolacji obciążenia za pomocą grup obciążeń w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5d81dc1f4da6e952061496fa348d0f8e87b00b81
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742977"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Izolacja grupy obciążenia usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule wyjaśniono, jak grupy obciążenia mogą służyć do konfigurowania izolacji obciążenia, zawiera zasoby i stosowania reguł środowiska uruchomieniowego do wykonywania kwerend.

## <a name="workload-groups"></a>Grupy obciążeń

Grupy obciążenia są kontenerami dla zestawu żądań i są podstawą sposobu konfigurowania zarządzania obciążeniem, w tym izolacji obciążenia w systemie.  Grupy obciążenia są tworzone przy użyciu składni [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Prosta konfiguracja zarządzania obciążeniem może zarządzać obciążeniami danych i zapytaniami użytkowników.  Na przykład grupa obciążenia `wgDataLoads` o nazwie zdefiniuje aspekty obciążenia dla danych ładowanych do systemu. Ponadto grupa obciążenia `wgUserQueries` o nazwie zdefiniuje aspekty obciążenia dla użytkowników uruchamianych kwerendy do odczytu danych z systemu.

W poniższych sekcjach zostanie podświetlić, jak grupy obciążenia zapewniają możliwość definiowania izolacji, hermetyzacji, żądania definicji zasobów i przestrzegania reguł wykonywania.

## <a name="workload-isolation"></a>Izolacja obciążeń

Izolacja obciążenia oznacza, że zasoby są zarezerwowane wyłącznie dla grupy obciążenia.  Izolacja obciążenia jest osiągana przez skonfigurowanie parametru MIN_PERCENTAGE_RESOURCE na większą niż zero w składni [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  W przypadku obciążeń ciągłego wykonywania, które muszą być zgodne z ścisłymi umowami SLA, izolacja zapewnia, że zasoby są zawsze dostępne dla grupy obciążenia.

Konfigurowanie izolacji obciążenia niejawnie definiuje gwarantowany poziom współbieżności. Na przykład grupa obciążenia `MIN_PERCENTAGE_RESOURCE` z zestawem 30% i `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ustawioną na 2% jest gwarantowana współbieżność 15.  Poziom współbieżności jest gwarantowana, ponieważ 15-2% slotów zasobów są zarezerwowane w ramach `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` grupy obciążenia przez cały czas (niezależnie od sposobu konfiguracji).  Jeśli `REQUEST_MAX_RESOURCE_GRANT_PERCENT` jest `REQUEST_MIN_RESOURCE_GRANT_PERCENT` większa `CAP_PERCENTAGE_RESOURCE` niż `MIN_PERCENTAGE_RESOURCE` i jest większa niż dodatkowe zasoby są dodawane na żądanie.  Jeśli `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` i są `CAP_PERCENTAGE_RESOURCE` równe `MIN_PERCENTAGE_RESOURCE`i jest większa niż , dodatkowa współbieżność jest możliwe.  Rozważmy poniższą metodę określania gwarantowanej współbieżności:

[Gwarantowana współbieżność]`MIN_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Istnieją określone minimalne wartości realnych poziomu usług dla min_percentage_resource.  Aby uzyskać więcej informacji, zobacz [Wartości efektywne, aby](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values) uzyskać więcej informacji.

W przypadku braku izolacji obciążenia żądania działają w [puli współużytkowane](#shared-pool-resources) zasoby.  Dostęp do zasobów w puli udostępnionej nie jest gwarantowany i jest przypisywany na podstawie [ważności.](sql-data-warehouse-workload-importance.md)

Konfigurowanie izolacji obciążenia należy wykonać ostrożnie, ponieważ zasoby są przydzielane do grupy obciążenia, nawet jeśli w grupie obciążenia nie ma żadnych aktywnych żądań. Nadmierna konfiguracja izolacji może prowadzić do zmniejszenia ogólnego wykorzystania systemu.

Użytkownicy powinni unikać rozwiązania do zarządzania obciążeniem, które konfiguruje izolację obciążenia w 100%: 100% izolacji jest osiągane, gdy suma min_percentage_resource skonfigurowana we wszystkich grupach obciążenia wynosi 100%.  Ten typ konfiguracji jest zbyt restrykcyjne i sztywne, pozostawiając niewiele miejsca na żądania zasobów, które są przypadkowo błędnie klasyfikowane. Istnieje aprowizacja zezwalana na wykonywanie jednego żądania z grup obciążenia, które nie są skonfigurowane do izolacji. Zasoby przydzielone do tego żądania pojawią się jako zero w systemach DMV i pożyczą smallrc poziom dotacji zasobów z zasobów zarezerwowanych systemowych.

> [!NOTE]
> Aby zapewnić optymalne wykorzystanie zasobów, należy wziąć pod uwagę rozwiązanie do zarządzania obciążeniem, które wykorzystuje pewną izolację, aby upewnić się, że ujednolici są spełnione i mieszane z udostępnionymi zasobami, które są dostępne na podstawie [znaczenia obciążenia.](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Ograniczenie obciążenia

Ograniczenie obciążenia odnosi się do ograniczania ilości zasobów, z których może korzystać grupa obciążenia.  Ograniczenie obciążenia jest osiągane przez skonfigurowanie parametru CAP_PERCENTAGE_RESOURCE na mniej niż 100 w składni [GRUPY TWORZENIA OBCIĄŻENIA.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Należy wziąć pod uwagę scenariusz, w którym użytkownicy potrzebują dostępu do odczytu do systemu, dzięki czemu mogą uruchamiać analizę warunkową za pośrednictwem zapytań ad hoc.  Tego typu żądania mogą mieć negatywny wpływ na inne obciążenia, które są uruchomione w systemie.  Konfigurowanie zamknięcia zapewnia, że ilość zasobów jest ograniczona.

Konfigurowanie zamknięcia obciążenia niejawnie definiuje maksymalny poziom współbieżności.  Przy CAP_PERCENTAGE_RESOURCE ustawiona na 60%, a REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawiona na 1%, dla grupy obciążenia dozwolony jest poziom współbieżności do 60 współbieżności.  Należy wziąć pod uwagę metodę zawartą poniżej do określania maksymalnej współbieżności:

[Maksymalna współbieżność]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Efektywna CAP_PERCENTAGE_RESOURCE grupy obciążenia nie osiągnie 100% po utworzeniu grup obciążenia z MIN_PERCENTAGE_RESOURCE na poziomie większym niż zero.  Zobacz [sys.dm_workload_management_workload_groups_stats,](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby uzyskać efektywne wartości środowiska uruchomieniowego.

## <a name="resources-per-request-definition"></a>Definicja zasobów na żądanie

Grupy obciążenia zapewniają mechanizm definiowania minimalnej i maksymalnej ilości zasobów, które są przydzielane na żądanie z parametrami REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT w składni [GRUPY TWORZENIA OBCIĄŻENIA.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Zasoby w tym przypadku są procesora CPU i pamięci.  Konfigurowanie tych wartości określa, ile zasobów i jaki poziom współbieżności można osiągnąć w systemie.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT jest parametrem opcjonalnym, który domyślnie ma tę samą wartość, która jest określona dla REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Podobnie jak wybór klasy zasobów, konfigurowanie REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawia wartość dla zasobów wykorzystywanych przez żądanie.  Ilość zasobów wskazanych przez ustawioną wartość jest gwarantowana do alokacji do żądania przed rozpoczęciem wykonywania.  Dla klientów migrujących z klas zasobów do grup obciążenia, należy rozważyć następujące jak artykuł [do](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) mapowania z klas zasobów do grup obciążenia jako punkt wyjścia.

Konfigurowanie REQUEST_MAX_RESOURCE_GRANT_PERCENT do wartości większej niż REQUEST_MIN_RESOURCE_GRANT_PERCENT umożliwia systemowi przydzielenie większej ilości zasobów na żądanie.  Podczas planowania żądania system określa rzeczywistą alokację zasobów do żądania, która jest między REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT, na podstawie dostępności zasobów w puli udostępnionej i bieżącego obciążenia w systemie.  Zasoby muszą istnieć w [puli zasobów udostępnionych,](#shared-pool-resources) gdy kwerenda jest zaplanowana.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT mają skuteczne wartości, które są zależne od wartości MIN_PERCENTAGE_RESOURCE skuteczne i CAP_PERCENTAGE_RESOURCE.  Zobacz [sys.dm_workload_management_workload_groups_stats,](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby uzyskać efektywne wartości środowiska uruchomieniowego.

## <a name="execution-rules"></a>Reguły wykonywania

W systemach raportowania ad hoc klienci mogą przypadkowo wykonywać zapytania o ucieczkę, które poważnie wpływają na produktywność innych osób.  Administratorzy systemu są zmuszeni do spędzania czasu zabijając uciekinierów zapytań, aby zwolnić zasoby systemowe.  Grupy obciążeń oferują możliwość skonfigurowania reguły limitu czasu wykonania kwerendy w celu anulowania kwerend, które przekroczyły określoną wartość.  Reguła jest konfigurowana `QUERY_EXECUTION_TIMEOUT_SEC` przez ustawienie parametru w składni [CREATE WORKLOAD GROUP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Zasoby puli udostępnionej

Zasoby puli udostępnionej to zasoby, które nie są skonfigurowane do izolacji.  Grupy obciążenia z MIN_PERCENTAGE_RESOURCE ustawiona na zero zasobów dźwigni w puli udostępnionej do wykonywania żądań.  Grupy obciążenia o CAP_PERCENTAGE_RESOURCE większej niż MIN_PERCENTAGE_RESOURCE również używane zasoby udostępnione.  Ilość zasobów dostępnych w puli udostępnionej jest obliczana w następujący sposób.

[Wspólna pula] = 100 - [suma `MIN_PERCENTAGE_RESOURCE` wszystkich grup obciążenia]

Dostęp do zasobów w puli udostępnionej jest przydzielany na podstawie [ważności.](sql-data-warehouse-workload-importance.md)  Żądania o tym samym poziomie ważności będą uzyskiwać dostęp do zasobów puli udostępnionej na zasadzie pierwszego cala/pierwszego wyjścia.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: konfigurowanie izolacji obciążenia](quickstart-configure-workload-isolation-tsql.md)
- [TWORZENIE GRUPY OBCIĄŻENIA](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Konwertuj klasy zasobów na grupy obciążeń](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitorowanie portalu zarządzania obciążeniem](sql-data-warehouse-workload-management-portal-monitor.md).  
