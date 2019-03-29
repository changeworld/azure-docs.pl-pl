---
title: Usługa Azure SQL Database — odczyt zapytania w replikach | Dokumentacja firmy Microsoft
description: Usługi Azure SQL Database oferuje możliwość załadować saldo tylko do odczytu obciążeń przy użyciu pojemności repliki tylko do odczytu - o nazwie odczytu skalowalnego w poziomie.
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
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619895"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Ładowanie równoważenie obciążeń związanych z zapytaniami tylko do odczytu przy użyciu repliki tylko do odczytu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

**Odczyt skalowalnego w poziomie** pozwala załadować saldo usługa Azure SQL Database tylko do odczytu obciążeń przy użyciu pojemność jednej z replik tylko do odczytu.

Każda baza danych w warstwie Premium ([modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md)) lub w warstwie krytyczne dla działania firmy ([modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md)) są automatycznie konfigurowani przy użyciu kilku zawsze włączonych replik na obsługuje umowa SLA dotycząca dostępności. Jest to zilustrowane w poniższym diagramie.

![Repliki tylko do odczytu](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Repliki pomocnicze są aprowizowane za pomocą tego samego rozmiaru obliczeń jako repliki podstawowej. **Odczytu skalowalnego w poziomie** funkcja pozwala załadować saldo bazy danych SQL Database tylko do odczytu obciążeń przy użyciu pojemność jednej z replik tylko do odczytu nie udostępniały repliki do odczytu i zapisu. Dzięki temu obciążenie tylko do odczytu zostanie odizolowana od głównej obciążenia odczytu i zapisu, a nie ma wpływu na jego wydajność. Ta funkcja jest przeznaczona dla aplikacji, które obejmują logicznie oddzielone obciążeń tylko do odczytu, takich jak analiza, a w związku z tym można także umożliwia czerpanie korzyści wydajności przy użyciu tej dodatkowej pojemności bez dodatkowych kosztów.

Funkcja odczytu skalowalnego w poziomie za pomocą określonej bazy danych, należy go najpierw włączyć podczas tworzenia bazy danych lub później, zmieniając jego konfigurację przy użyciu programu PowerShell, wywołując [AzSqlDatabase zestaw](/powershell/module/az.sql/set-azsqldatabase) lub [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) poleceń cmdlet lub za pomocą interfejsu REST API usługi Azure Resource Manager [baz danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) metody.

Po włączeniu odczytu skalowalnego w poziomie dla bazy danych aplikacji łączenie się z tą bazą danych zostanie skierowany przez bramę do repliki odczytu i zapisu lub tylko do odczytu replik tej bazy danych zgodnie z opisem w `ApplicationIntent` właściwości skonfigurowane w Parametry połączenia aplikacji. Instrukcje dotyczące `ApplicationIntent` właściwości, zobacz [Określanie przeznaczenia aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Jeśli odczyt skalowalnego w poziomie jest wyłączony lub ustaw właściwość ReadScale w warstwie usługi z nieobsługiwanego, wszystkie połączenia są kierowane do repliki odczytu i zapisu, niezależnie od `ApplicationIntent` właściwości.

> [!NOTE]
> Zapytania Data Store, Extended Events, program SQL Profiler i funkcje inspekcji nie są obsługiwane w trybie tylko do odczytu replikach. 
## <a name="data-consistency"></a>Spójność danych

Jedną z zalet repliki jest, że repliki są zawsze w stanie transakcyjnie spójne, ale w różnych punktach w czasie może wystąpić pewne małe opóźnienie między różnych replik. Odczyt skalowalnego w poziomie obsługuje spójność na poziomie sesji. Oznacza to, jeśli sesji tylko do odczytu połączy się ponownie po spowodowany przez niedostępność repliki błąd połączenia, mogą zostać przekierowane do repliki, który nie jest w 100% aktualny z repliką odczytu i zapisu. Podobnie jeśli aplikacja zapisuje dane przy użyciu sesji odczytu / zapisu i natychmiast odczyta go przy użyciu sesji tylko do odczytu, jest to możliwe, że najnowsze aktualizacje nie są natychmiast widoczne w replice. Opóźnienie jest spowodowane przez operację powtórzenia dziennika transakcji asynchronicznego.

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

Odczyt skalowalnego w poziomie jest domyślnie włączone w [wystąpienia zarządzanego](sql-database-managed-instance.md) warstwy krytyczne dla działania firmy. Powinno ono zostać jawnie włączone w [bazy danych umieszczone na serwerze bazy danych SQL](sql-database-servers.md) w warstwach Premium i krytyczne dla działania firmy. Metody włączania i wyłączania odczytu skalowalnego w poziomie opisano w tym miejscu.

### <a name="powershell-enable-and-disable-read-scale-out"></a>Program PowerShell: Włączanie i wyłączanie odczytu skalowalnego w poziomie

Zarządzanie odczytu skalowalnego w poziomie w programie Azure PowerShell wymaga grudnia 2016 r. wersja programu Azure PowerShell lub nowszej. Dla najnowszej wersji programu PowerShell, zobacz [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Włączanie lub wyłączanie odczytu skalowalnego w poziomie w programie Azure PowerShell, wywołując [AzSqlDatabase zestaw](/powershell/module/az.sql/set-azsqldatabase) polecenia cmdlet i przekazując żądaną wartość — `Enabled` lub `Disabled` — dla `-ReadScale` parametru. Alternatywnie można użyć [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) polecenia cmdlet, aby utworzyć nową bazę danych z odczytu skalowalnego w poziomie włączone.

Na przykład aby włączyć odczytać skalowalnego w poziomie dla istniejącej bazy danych (zamianę elementów w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Aby wyłączyć odczytu skalowalnego w poziomie do istniejącej bazy danych (zamianę elementów w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Aby utworzyć nową bazę danych za pomocą odczytu skalowalnego w poziomie włączone (zastępując elementy w nawiasy kątowe poprawne wartości dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>INTERFEJS API REST: Włączanie i wyłączanie odczytu skalowalnego w poziomie

Aby utworzyć bazę danych za pomocą odczytu skalowalnego w poziomie włączone, lub włączyć lub wyłączyć odczytu skalowalnego w poziomie do istniejącej bazy danych, Utwórz lub zaktualizuj odpowiednia jednostka bazy danych za pomocą `readScale` właściwością `Enabled` lub `Disabled` podobnie jak w poniższych przykładowych żądanie.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Aby uzyskać więcej informacji, zobacz [baz danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Przy użyciu bazy danych TempDB na repliki tylko do odczytu

Bazy danych TempDB nie są replikowane do repliki tylko do odczytu. Każdej repliki ma własną wersję bazy danych TempDB, które zostało utworzone po utworzeniu repliki. Zapewnia, że bazy danych TempDB można aktualizować i mogą być modyfikowane podczas wykonywanie zapytań. Jeśli obciążenie tylko do odczytu, zależy od tego, przy użyciu obiektów bazy danych TempDB, należy utworzyć te obiekty jako część skryptu zapytania. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Odczyt skalowalnego w poziomie przy użyciu replikacji geograficznej bazy danych

Jeśli używasz odczytu skalowalnego w poziomie można załadować saldo tylko do odczytu na obciążenie bazy danych, która jest replikowana geograficznie (na przykład, jako członek grupy trybu failover), upewnij się, że odczytu skalowalnego w poziomie jest włączona na podstawowe i pomocnicze bazy danych replikowanej geograficznie. Ta konfiguracja będzie upewnij się, gdy aplikacja nawiązuje połączenie z nową podstawową po włączeniu trybu failover będzie kontynuowane tego samego środowiska usługi równoważenia obciążenia. Jeśli łączysz się do pomocniczej bazy danych replikowanej geograficznie skalę odczytu włączone, sesje z `ApplicationIntent=ReadOnly` będą kierowane do jednej z replik taki sam sposób, firma Microsoft trasy połączeń w głównej bazie danych.  Sesje bez `ApplicationIntent=ReadOnly` będą kierowane do repliki podstawowej replikowanej geograficznie pomocniczej, który również jest tylko do odczytu. Replikowanej geograficznie pomocniczej bazy danych jest inny punkt końcowy niż podstawowa baza danych, dlatego w przeszłości pomocniczy dostęp do jej nie muszą ustawić `ApplicationIntent=ReadOnly`. Aby zapewnić zgodność z poprzednimi wersjami `sys.geo_replication_links` Pokazuje widok DMV `secondary_allow_connections=2` (każde połączenie klienta jest dozwolone).

> [!NOTE]
> Działanie okrężne ani żadnych innych ze zrównoważonym obciążeniem routingu między lokalnym repliki pomocniczej bazy danych nie jest obsługiwane.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak ustawić odczytu skalowalnego w poziomie przy użyciu programu PowerShell, zobacz [AzSqlDatabase zestaw](/powershell/module/az.sql/set-azsqldatabase) lub [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) polecenia cmdlet.
- Aby dowiedzieć się, jak ustawić odczytu skalowalnego w poziomie przy użyciu interfejsu API REST, zobacz [baz danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
