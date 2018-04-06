---
title: Azure SQL Database - odczytu zapytań w replikach | Dokumentacja firmy Microsoft
description: Baza danych SQL Azure pozwala, aby załadować saldo tylko do odczytu obciążeń przy użyciu pojemności repliki tylko do odczytu - o nazwie odczytu skalowalnego w poziomie.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.openlocfilehash: 26204d5bd61d193a3d08e26f98faf77ecc367a94
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Użyj repliki tylko do odczytu, aby załadować równoważenie obciążeń zapytania tylko do odczytu (wersja zapoznawcza)

**Skalowalne odczytu** umożliwia ładowanie saldo Azure SQL Database tylko do odczytu obciążeń przy użyciu pojemności repliki tylko do odczytu. 

## <a name="overview-of-read-scale-out"></a>Omówienie odczytu skalowalnego w poziomie

Każda baza danych w warstwie Premium ([na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers.md#dtu-based-purchasing-model)) lub w warstwie Business krytyczne ([na podstawie vCore model kupna](sql-database-service-tiers.md#vcore-based-purchasing-model-preview)) jest automatycznie udostępniane z kilku replik Always ON obsługuje dostępności umowy dotyczącej poziomu usług. Te repliki są udostępniane z tym samym poziomie wydajności co używanych przez połączenia zwykłej bazy danych repliki do odczytu / zapisu. **Odczytu skalowalnego w poziomie** funkcja pozwala załadować saldo bazy danych SQL tylko do odczytu obciążeń przy użyciu pojemności repliki tylko do odczytu i nie udostępniały repliki do odczytu / zapisu. Dzięki temu obciążenie tylko do odczytu zostanie odizolowana od głównego obciążenia zapisu i odczytu i nie będzie miało wpływ na wydajność. Funkcja jest przeznaczona dla aplikacji, które obejmują logicznie oddzielone obciążeń tylko do odczytu, takich jak analiza i w związku z tym można udogodnień wydajności przy użyciu tej dodatkowej pojemności bez dodatkowych kosztów.

Do korzystania z funkcji odczytu skalowalnego w poziomie z określoną bazę danych, musisz jawnie włączyć go podczas tworzenia bazy danych lub później, zmieniając konfigurację przy użyciu programu PowerShell, wywołując [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [ Nowe AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) poleceń cmdlet lub przy użyciu interfejsu API usługi Azure Resource Manager REST [baz danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate) metody. 

Po odczytu skalowalnego w poziomie jest włączona dla bazy danych, aplikacji nawiązywania połączenia z bazą danych zostanie skierowany do repliki do odczytu / zapisu lub tylko do odczytu repliki tej bazy danych zgodnie z `ApplicationIntent` właściwości skonfigurowane w aplikacji Parametry połączenia. Aby uzyskać informacje dotyczące `ApplicationIntent` właściwości, zobacz [określenia przeznaczenia aplikacji](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

> [!NOTE]
> Podczas udostępniania wersji zapoznawczej magazyn danych zapytań i zdarzeń rozszerzonych nie są obsługiwane w trybie tylko do odczytu replikach.

## <a name="data-consistency"></a>Spójność danych

Jedną z zalet AlwasyON jest replik zawsze znajdują się w stanie spójna transakcyjnie, że w różnych punktach w czasie mogą istnieć niektóre małe opóźnienia między innej repliki. Odczyt skalowalnego w poziomie obsługuje spójności z poziomu sesji. Oznacza to, połączenie sesji tylko do odczytu po wystąpieniu błędu połączenia spowodowane niedostępności replik, może zostać przekierowane do repliki, który nie jest aktualny repliki do odczytu / zapisu w 100%. Podobnie jeśli aplikacja zapisuje dane przy użyciu sesji odczytu i zapisu i natychmiast odczytuje go przy użyciu sesji tylko do odczytu, jest to możliwe, że najnowsze aktualizacje nie są od razu widoczne. Jest to spowodowane Powtórz dziennika transakcji w replikach jest asynchroniczne.

> [!NOTE]
> Brakuje opóźnienia replikacji w obrębie regionu i ta sytuacja jest rzadko.


## <a name="connecting-to-a-read-only-replica"></a>Łączenie z repliki tylko do odczytu

Po włączeniu odczytu skalowalnego w poziomie dla bazy danych, `ApplicationIntent` opcji w parametrach połączenia przez klienta określa, czy połączenie jest kierowany do repliki zapisu lub do repliki tylko do odczytu. W szczególności jeśli `ApplicationIntent` wartość jest `ReadWrite` (wartość domyślna), połączenie zostanie przekierowany do repliki do odczytu / zapisu bazy danych. Jest to zachowanie istniejącej identyczne. Jeśli `ApplicationIntent` wartość jest `ReadOnly`, połączenie jest kierowany do repliki do odczytu.

Na przykład następujący ciąg połączenia klient łączy się z repliką tylko do odczytu (zastępowanie elementów w nawiasach ostrych przy użyciu prawidłowych wartości dla danego środowiska i upuszczanie nawiasu ostrego):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jedną z następujących ciągów połączenia klient łączy się z repliki do odczytu / zapisu (zastępowanie elementów w nawiasach ostrych przy użyciu prawidłowych wartości dla danego środowiska i upuszczanie nawiasu ostrego):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Włączanie i wyłączanie odczytu skalowalnego w poziomie przy użyciu programu Azure PowerShell

Zarządzanie odczytu skalowalnego w poziomie w programie Azure PowerShell wymaga grudnia 2016 wersji programu Azure PowerShell lub nowszej. W najnowszej wersji programu PowerShell, zobacz [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Włącz lub wyłącz odczytu skalowalnego w poziomie w programie Azure PowerShell wywołując [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) polecenia cmdlet i przekazując żądaną wartość — `Enabled` lub `Disabled` — dla `-ReadScale` parametru. Alternatywnie można użyć [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) , aby utworzyć nową bazę danych z odczytu skalowalnego w poziomie włączone.

Na przykład aby włączyć odczytu skalowalnego w poziomie dla istniejącej bazy danych (zastępowanie elementów w nawiasach ostrych przy użyciu prawidłowych wartości dla danego środowiska i upuszczanie nawiasu ostrego):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Aby wyłączyć odczytu skalowalnego w poziomie istniejącej bazy danych (zastępowanie elementów w nawiasach ostrych przy użyciu prawidłowych wartości dla danego środowiska i upuszczanie nawiasu ostrego):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Aby utworzyć nową bazę danych za pomocą odczytu skalowalnego w poziomie włączone (zastępowanie elementów w nawiasach ostrych przy użyciu prawidłowych wartości dla danego środowiska i upuszczanie nawiasu ostrego):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Włączanie i wyłączanie odczytu skalowalnego w poziomie przy użyciu interfejsu API REST bazy danych SQL Azure

Utwórz bazę danych z odczytu skalowalnego w poziomie włączony, albo do włączyć lub wyłączyć odczytu skalowalnego w poziomie istniejącej bazy danych, Utwórz lub zaktualizuj odpowiednia jednostka bazy danych z `readScale` ustawioną właściwość `Enabled` lub `Disabled` jak poniżej przykładowy żądanie.

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

Aby uzyskać dodatkowe informacje, zobacz [baz danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak ustawić odczytu skalowalnego w poziomie przy użyciu programu PowerShell, zobacz [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) polecenia cmdlet.
- Aby dowiedzieć się, jak ustawić odczytu skalowalnego w poziomie przy użyciu interfejsu API REST, zobacz [baz danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate).
