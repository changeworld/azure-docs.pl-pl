---
title: Usługa Azure SQL Database — odczyt zapytania w replikach | Dokumentacja firmy Microsoft
description: Usługi Azure SQL Database umożliwia równoważenie obciążenia tylko do odczytu obciążeń przy użyciu pojemności repliki tylko do odczytu - o nazwie odczytu skalowalnego w poziomie.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: 1b452fb0bac91429793f8d55e439c36c70784722
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492720"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Użyj tylko do odczytu repliki, aby równoważyć obciążenie obciążeń związanych z zapytaniami tylko do odczytu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jako część [architektura wysokiej dostępności](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), każda baza danych w warstwie Premium, krytyczne dla działania firmy lub w Hiperskali zostanie automatycznie aprowizowana z repliki podstawowej i wiele replik pomocniczych. Repliki pomocnicze są aprowizowane za pomocą tego samego rozmiaru obliczeń jako repliki podstawowej. **Odczytu skalowalnego w poziomie** funkcja umożliwia równoważenie obciążenia bazy danych SQL Database tylko do odczytu obciążeń przy użyciu pojemność jednej z replik tylko do odczytu nie udostępniały repliki do odczytu i zapisu. Dzięki temu obciążenie tylko do odczytu zostanie odizolowana od głównej obciążenia odczytu i zapisu, a nie ma wpływu na jego wydajność. Ta funkcja jest przeznaczona dla aplikacji, które obejmują logicznie oddzielone obciążeń tylko do odczytu, takich jak analiza. Można ich także umożliwia czerpanie korzyści wydajności przy użyciu tej dodatkowej pojemności bez dodatkowych kosztów.

Na poniższym diagramie przedstawiono go za pomocą krytyczne dla działania firmy bazy danych.

![Repliki tylko do odczytu](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkcja odczytu skalowalnego w poziomie jest włączona domyślnie w nowej warstwie Premium, krytyczne dla działania firmy i baz danych na dużą skalę. Jeśli skonfigurowano parametry połączenia SQL `ApplicationIntent=ReadOnly`, aplikacja zostanie przekierowany przez bramę do tej bazy danych repliki tylko do odczytu. Aby uzyskać informacje dotyczące sposobu używania `ApplicationIntent` właściwości, zobacz [Określanie przeznaczenia aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Jeśli chcesz upewnić się, że aplikacja łączy się z repliką podstawową, niezależnie od wartości `ApplicationIntent` ustawienie w parametrach połączenia SQL, musisz jawnie wyłączyć odczytu skalowalnego w poziomie podczas tworzenia bazy danych lub zmieniania jego konfiguracji. Na przykład jeśli Uaktualnij bazę danych z warstwy Standard lub ogólnego przeznaczenia do warstwy Premium, krytyczne dla działania firmy lub w Hiperskali i upewnić się, że wszystkie swoje połączenia w dalszym ciągu przejdź do repliki podstawowej, należy wyłączyć odczyt skalowalnego w poziomie. Aby uzyskać szczegółowe informacje na temat ją wyłączyć, zobacz [włączać i wyłączać odczytu skalowalnego w poziomie](#enable-and-disable-read-scale-out).

> [!NOTE]
> Zapytania Data Store, Extended Events, program SQL Profiler i funkcje inspekcji nie są obsługiwane w trybie tylko do odczytu replikach. 

## <a name="data-consistency"></a>Spójność danych

Jedną z zalet repliki jest, że repliki są zawsze w stanie transakcyjnie spójne, ale w różnych punktach w czasie może wystąpić pewne małe opóźnienie między różnych replik. Odczyt skalowalnego w poziomie obsługuje spójność na poziomie sesji. Oznacza to, jeśli sesji tylko do odczytu połączy się ponownie po błędzie połączenia spowodowany przez niedostępność replik, może zostać przekierowany do repliki, który nie jest w 100% aktualny z repliką odczytu i zapisu. Podobnie jeśli aplikacja zapisuje dane przy użyciu sesji odczytu / zapisu i natychmiast odczyta go przy użyciu sesji tylko do odczytu, jest to możliwe, że najnowsze aktualizacje nie są natychmiast widoczne w replice. Opóźnienie jest spowodowane przez operację powtórzenia dziennika transakcji asynchronicznego.

> [!NOTE]
> Brakuje opóźnienia w replikacji w regionie i tej sytuacji jest rzadkie.

## <a name="connect-to-a-read-only-replica"></a>Połącz się z repliką tylko do odczytu

Po włączeniu odczytu skalowalnego w poziomie dla bazy danych, `ApplicationIntent` opcji w parametrach połączenia, udostępniane przez klienta określa, czy połączenie jest kierowany do repliki zapisu lub do repliki tylko do odczytu. W szczególności jeśli `ApplicationIntent` wartość `ReadWrite` (wartość domyślna), połączenie zostanie skierowany do repliki do odczytu i zapisu bazy danych. Jest to taka sama jak istniejące zachowanie. Jeśli `ApplicationIntent` wartość `ReadOnly`, połączenie jest kierowany do repliki tylko do odczytu.

Na przykład następujący ciąg połączenia klient łączy się z repliką tylko do odczytu (zastępując elementy w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jedną z następujących ciągów połączenia klient łączy się z repliką odczytu i zapisu (zastępując elementy w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Sprawdź, czy połączenie z repliką tylko do odczytu

Aby sprawdzić, czy nawiązano połączenie z repliką tylko do odczytu, uruchamiając następujące zapytanie. Zwróci READ_ONLY po podłączeniu do repliki tylko do odczytu.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> W danym momencie tylko jeden z zawsze włączonych replik jest dostępna przez sesje tylko do odczytu.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorowanie i rozwiązywanie problemów z repliki tylko do odczytu

Po podłączeniu do repliki tylko do odczytu, możesz uzyskać dostęp za pomocą metryk wydajności `sys.dm_db_resource_stats` DMV. Aby uzyskać dostęp do statystyki planu zapytania, należy użyć `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` i `sys.dm_exec_sql_text` dynamicznych widoków zarządzania.

> [!NOTE]
> Widok DMV `sys.resource_stats` w logicznej głównej bazy danych zwraca dane dotyczące użycia i magazynu Procesora repliki podstawowej.


## <a name="enable-and-disable-read-scale-out"></a>Włączanie i wyłączanie odczytu skalowalnego w poziomie

Odczyt skalowalnego w poziomie jest włączona domyślnie w warstwach Premium, krytyczne dla działania firmy i na dużą skalę. Nie można włączyć odczytu skalowalnego w poziomie w następujących warstwach usługi podstawowa, standardowa lub ogólnego przeznaczenia. Odczyt skalowalnego w poziomie jest automatycznie wyłączana w Hiperskali baz danych skonfigurowanych z replikami 0. 

Można wyłączyć i ponownie włączyć odczytu skalowalnego w poziomie na pojedynczych baz danych i elastycznej puli baz danych w warstwie Premium lub krytyczne dla działania firmy warstwie przy użyciu następujących metod.

> [!NOTE]
> Możliwość wyłączenia odczytu skalowalnego w poziomie znajduje się w celu zgodności z poprzednimi wersjami.

### <a name="azure-portal"></a>Azure Portal

Może zarządzać ustawienie poziomie Sacle odczytu **Konfiguruj** bloku bazy danych. 

### <a name="powershell"></a>PowerShell

Zarządzanie odczytu skalowalnego w poziomie w programie Azure PowerShell wymaga grudnia 2016 r. wersja programu Azure PowerShell lub nowszej. Dla najnowszej wersji programu PowerShell, zobacz [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Można wyłączyć lub włączyć ponownie odczytu skalowalnego w poziomie w programie Azure PowerShell, wywołując [AzSqlDatabase zestaw](/powershell/module/az.sql/set-azsqldatabase) polecenia cmdlet i przekazując żądaną wartość — `Enabled` lub `Disabled` — dla `-ReadScale` parametru. 

Aby wyłączyć odczytu skalowalnego w poziomie w istniejącej bazie danych (zamianę elementów w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Aby wyłączyć odczytu skalowalnego w poziomie na nową bazę danych (zamianę elementów w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Aby ponownie włączyć odczytu skalowalnego w poziomie w istniejącej bazie danych (zamianę elementów w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>Interfejs API REST

Tworzenie bazy danych za pomocą odczytu skalowalnego w poziomie wyłączone lub zmienić ustawienia dla istniejącej bazy danych, użyj następującej metody przy użyciu `readScale` właściwością `Enabled` lub `Disabled` podobnie jak poniżej przykładowe żądanie.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Aby uzyskać więcej informacji, zobacz [baz danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Przy użyciu bazy danych TempDB na repliki tylko do odczytu

Bazy danych TempDB nie są replikowane do repliki tylko do odczytu. Każdej repliki ma własną wersję bazy danych TempDB, które zostało utworzone po utworzeniu repliki. Zapewnia, że bazy danych TempDB można aktualizować i mogą być modyfikowane podczas wykonywanie zapytań. Jeśli obciążenie tylko do odczytu, zależy od tego, przy użyciu obiektów bazy danych TempDB, należy utworzyć te obiekty jako część skryptu zapytania. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Odczyt skalowalnego w poziomie przy użyciu replikacji geograficznej bazy danych

Jeśli używasz odczytu skalowalnego w poziomie do obciążeń tylko do odczytu Równoważenie obciążenia w bazie danych, która jest replikowana geograficznie (na przykład, jako członek grupy trybu failover), upewnij się, że odczytu skalowalnego w poziomie jest włączona na podstawowe i pomocnicze bazy danych replikowanej geograficznie. Ta konfiguracja będzie upewnij się, gdy aplikacja nawiązuje połączenie z nową podstawową po włączeniu trybu failover będzie kontynuowane tego samego środowiska usługi równoważenia obciążenia. Jeśli łączysz się do pomocniczej bazy danych replikowanej geograficznie skalę odczytu włączone, sesje z `ApplicationIntent=ReadOnly` będą kierowane do jednej z replik taki sam sposób, firma Microsoft trasy połączeń w głównej bazie danych.  Sesje bez `ApplicationIntent=ReadOnly` będą kierowane do repliki podstawowej replikowanej geograficznie pomocniczej, który również jest tylko do odczytu. Ponieważ replikowanej geograficznie pomocniczej bazy danych nie ma punktu końcowego innej niż podstawowa baza danych, w przeszłości pomocniczy dostęp do jej nie musi ustawić `ApplicationIntent=ReadOnly`. Aby zapewnić zgodność z poprzednimi wersjami `sys.geo_replication_links` Pokazuje widok DMV `secondary_allow_connections=2` (każde połączenie klienta jest dozwolone).

> [!NOTE]
> Działanie okrężne ani żadnych innych ze zrównoważonym obciążeniem routingu między lokalnym repliki pomocniczej bazy danych nie jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat oferty bazy danych SQL na dużą skalę, zobacz [warstwy usługi w Hiperskali](./sql-database-service-tier-hyperscale.md).
