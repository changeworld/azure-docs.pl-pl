---
title: Izolacja obciążenia
description: Wskazówki dotyczące ustawiania izolacji obciążeń z grupami obciążeń w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/04/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: a31498ec5459604d89fa72a6f2a003dbc1189eed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685375"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>Izolacja grupy obciążeń SQL Data Warehouse (wersja zapoznawcza)

W tym artykule wyjaśniono, jak grupy obciążeń mogą służyć do konfigurowania izolacji obciążeń, zawierają zasoby i stosować reguły środowiska uruchomieniowego na potrzeby wykonywania zapytań.

## <a name="workload-groups"></a>Grupy obciążeń

Grupy obciążeń są kontenerami dla zestawu żądań i są podstawą sposobu zarządzania obciążeniami, w tym izolacji obciążeń, w systemie.  Grupy obciążeń są tworzone przy użyciu składni [tworzenia grupy obciążeń](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Prosta konfiguracja zarządzania obciążeniami umożliwia zarządzanie obciążeniami danych i zapytaniami użytkowników.  Na przykład grupa obciążeń o nazwie `wgDataLoads` będzie definiować aspekty obciążeń dla danych ładowanych do systemu. Ponadto Grupa obciążeń o nazwie `wgUserQueries` będzie definiować aspekty obciążeń dla użytkowników uruchamiających zapytania w celu odczytu danych z systemu.

W poniższych sekcjach opisano sposób, w jaki grupy obciążeń zapewniają możliwość definiowania izolacji, zawierania, definiowania zasobów żądań i przestrzegania reguł wykonywania.

## <a name="workload-isolation"></a>Izolacja obciążenia

Izolacja obciążenia oznacza, że zasoby są zarezerwowane wyłącznie dla grupy obciążenia.  Izolacja obciążenia jest uzyskiwana przez skonfigurowanie parametru MIN_PERCENTAGE_RESOURCE na wartość większą niż zero w składni [tworzenia grupy obciążeń](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  W przypadku obciążeń ciągłego wykonywania, które muszą być zgodne z ścisłą umowy SLA, izolacja gwarantuje, że zasoby są zawsze dostępne dla grupy obciążenia. 

Konfigurowanie izolacji obciążeń niejawnie definiuje gwarantowany poziom współbieżności.  W przypadku MIN_PERCENTAGE_RESOURCE o wartości 30% i REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawionej na 2% poziom współbieżności jest gwarantowany dla grupy obciążenia.  Rozważmy poniższą metodę określania gwarantowanej współbieżności:

[Gwarantowane współbieżność] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Istnieją określone minimalne wartości poziomu usługi dla min_percentage_resource.  Aby uzyskać więcej informacji, zobacz [efektywne wartości](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) w celu uzyskania dalszych szczegółów.

W przypadku braku izolacji obciążeń żądania działają w [udostępnionej puli](#shared-pool-resources) zasobów.  Dostęp do zasobów w puli udostępnionej nie jest gwarantowany i jest przypisywany na podstawie [ważności](sql-data-warehouse-workload-importance.md) .

Konfigurowanie izolacji obciążeń powinno odbywać się z zachowaniem ostrożności, ponieważ zasoby są przypisywane do grupy obciążeń, nawet jeśli w grupie obciążenia nie ma aktywnych żądań.  Zbyt konfiguracja izolacji może prowadzić do zmniejszenia całkowitego użycia systemu.

Użytkownicy powinni unikać rozwiązania do zarządzania obciążeniami, które konfigurują 100% izolacja obciążenia: 100% zostanie osiągnięty, gdy suma min_percentage_resource skonfigurowana dla wszystkich grup obciążeń równa 100%.  Ten typ konfiguracji jest nadmiernie restrykcyjny i sztywny, pozostawiając nieco wolnego miejsca dla żądań zasobów, które są przypadkowo nieprawidłowo klasyfikowane.  Istnieje możliwość zezwalania na wykonywanie jednego żądania z grup obciążeń, które nie są skonfigurowane do izolacji.  Zasoby przydzielone do tego żądania będą wyświetlane jako zero w systemach widoków DMV i pożyczą sobie poziom przydziału zasobów z zasobów zarezerwowanych systemowo.

> [!NOTE] 
> Aby zapewnić optymalne wykorzystanie zasobów, należy wziąć pod uwagę rozwiązanie do zarządzania obciążeniami, które wykorzystuje pewną izolację, aby zapewnić, że umowy SLA są spełnione i zmieszane z udostępnionymi zasobami, które są dostępne na podstawie [ważności obciążenia](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Zawieranie obciążeń

Zawieranie obciążenia odnosi się do ograniczenia ilości zasobów, które może zużywać Grupa obciążeń.  Zawieranie obciążenia jest osiągane przez skonfigurowanie parametru CAP_PERCENTAGE_RESOURCE na wartość mniejszą niż 100 w składni [tworzenia grupy obciążeń](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Rozważmy scenariusz, w którym użytkownicy muszą mieć dostęp do odczytu do systemu, aby można było uruchomić analizę analizy warunkowej za pośrednictwem zapytań ad hoc.  Te typy żądań mogą mieć negatywny wpływ na inne obciążenia działające w systemie.  Skonfigurowanie zawierania gwarantuje, że ilość zasobów jest ograniczona.

Konfigurowanie zawiera niejawnie zdefiniowanie maksymalnego poziomu współbieżności.  Mając CAP_PERCENTAGE_RESOURCE ustawiony na 60%, a REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawiony na 1%, do grupy obciążeń jest dozwolony poziom współbieżności 60.  Rozważmy metodę uwzględnioną poniżej w celu określenia maksymalnej współbieżności:

[Maks. współbieżność] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Efektywna CAP_PERCENTAGE_RESOURCE grupy obciążeń nie osiągnie 100%, gdy tworzone są grupy obciążeń z MIN_PERCENTAGE_RESOURCE na poziomie większym niż zero.  Aby uzyskać efektywne wartości środowiska uruchomieniowego, zobacz sekcję [sys. DM _workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) .

## <a name="resources-per-request-definition"></a>Zasoby na definicję żądania

Grupy obciążeń zapewniają mechanizm definiowania minimalnej i maksymalnej ilości zasobów przyznanych na żądanie z parametrami REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT w składni [tworzenia grupy obciążeń](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Zasoby w tym przypadku to procesor CPU i pamięć.  Skonfigurowanie tych wartości wymusza, ile zasobów i jaki poziom współbieżności można osiągnąć w systemie.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT jest opcjonalnym parametrem, który domyślnie ma tę samą wartość, która jest określona dla REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Podobnie jak w przypadku wybrania klasy zasobów, skonfigurowanie REQUEST_MIN_RESOURCE_GRANT_PERCENT ustawia wartość zasobów wykorzystywanych przez żądanie.  Ilość zasobów wskazywanych przez wartość zestawu jest gwarantowana do alokacji żądania przed rozpoczęciem wykonywania.  W przypadku klientów migrowania z klas zasobów do grup obciążeń należy wziąć pod uwagę [instrukcje dotyczące sposobu](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) mapowania z klas zasobów do grup obciążeń jako punktu początkowego.

Skonfigurowanie REQUEST_MAX_RESOURCE_GRANT_PERCENT na wartość większą niż REQUEST_MIN_RESOURCE_GRANT_PERCENT umożliwia systemowi przydzielenie większej liczby zasobów na żądanie.  Podczas planowania żądania system określa rzeczywistą alokację zasobów do żądania, która należy do zakresu od REQUEST_MIN_RESOURCE_GRANT_PERCENT do REQUEST_MAX_RESOURCE_GRANT_PERCENT, na podstawie dostępności zasobów w puli udostępnionej i bieżącego obciążenia systemami.  Zasoby muszą znajdować się w [udostępnionej puli](#shared-pool-resources) zasobów po zaplanowaniu zapytania.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT i REQUEST_MAX_RESOURCE_GRANT_PERCENT mają obowiązujące wartości, które są zależne od efektywnych wartości MIN_PERCENTAGE_RESOURCE i CAP_PERCENTAGE_RESOURCE.  Aby uzyskać efektywne wartości środowiska uruchomieniowego, zobacz sekcję [sys. DM _workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) .

## <a name="execution-rules"></a>Reguły wykonywania

W systemach raportowania ad hoc klienci mogą przypadkowo wykonywać zapytania dotyczące przemijających, które poważnie wpływają na produktywność innych.  Administratorzy systemu są zmuszeni do poświęcania czasu na zabijanie zapytań w celu zwolnienia zasobów systemowych.  Grupy obciążeń oferują możliwość skonfigurowania reguły limitu czasu wykonywania zapytania, aby anulować zapytania, które przekroczyły określoną wartość.  Reguła jest konfigurowana przez ustawienie parametru `QUERY_EXECUTION_TIMEOUT_SEC` w składni [tworzenia grupy obciążeń](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .

## <a name="shared-pool-resources"></a>Zasoby puli udostępnionej

Zasoby puli udostępnionej są zasobami nieskonfigurowanymi do izolacji.  Grupy obciążeń z MIN_PERCENTAGE_RESOURCE ustawionym na zero wykorzystują zasoby w udostępnionej puli do wykonywania żądań.  Grupy obciążeń o CAP_PERCENTAGE_RESOURCE większym niż MIN_PERCENTAGE_RESOURCE również używają zasobów udostępnionych.  Ilość zasobów dostępnych w puli udostępnionej jest obliczana w następujący sposób.

[Pula udostępniona] = 100-[suma `MIN_PERCENTAGE_RESOURCE` we wszystkich grupach obciążeń]

Dostęp do zasobów w puli udostępnionej jest przypisywany na podstawie [ważności](sql-data-warehouse-workload-importance.md) .  Żądania o takim samym poziomie ważności będą uzyskiwać dostęp do zasobów puli udostępnionej przy pierwszej lub pierwszej kolejności.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Konfigurowanie izolacji obciążenia](quickstart-configure-workload-isolation-tsql.md)
- [UTWÓRZ GRUPĘ OBCIĄŻEŃ](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Przekonwertuj klasy zasobów na grupy obciążeń](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
