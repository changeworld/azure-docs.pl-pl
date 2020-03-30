---
title: Tworzenie i wykonywanie zapytań o pulę SQL synapse za pomocą programu Azure PowerShell
description: Szybko utwórz serwer logiczny puli SQL Synapse z regułą zapory na poziomie serwera przy użyciu programu Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 45982c0761fecdb456dba5dc4a5d604972b9c3e5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349309"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-with-azure-powershell"></a>Szybki start: tworzenie i wykonywanie zapytań o pulę SQL synapse za pomocą programu Azure PowerShell

Utwórz pulę SQL Synapse (magazyn danych) w usłudze Azure Synapse Analytics przy użyciu programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

> [!IMPORTANT]
> Utworzenie puli SQL może spowodować nową usługę podlegającą rozliczaniu.  Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby sprawdzić, której subskrypcji używasz, uruchom [get-azsubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Jeśli chcesz użyć innej subskrypcji niż domyślna, uruchom [program Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Tworzenie zmiennych

Zdefiniuj zmienne do wykorzystania w skryptach w tym przewodniku Szybki start.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) za pomocą polecenia [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Tworzenie serwera logicznego

Utwórz [serwer logiczny sql platformy Azure](../../sql-database/sql-database-logical-servers.md) przy użyciu polecenia [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver) Serwer logiczny zawiera grupę baz danych zarządzanych jako grupa. Poniższy przykład tworzy losowo nazwany serwer w grupie `ServerAdmin` zasobów z `ChangeYourAdminPassword1`nazwanym użytkownikiem administratorem i hasłem . Zastąp te wstępnie zdefiniowane wartości zgodnie z potrzebami.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory na poziomie serwera SQL platformy Azure](../../sql-database/sql-database-firewall-configure.md) przy użyciu polecenia [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule) Reguła zapory na poziomie serwera umożliwia aplikacji zewnętrznej, takiej jak SQL Server Management Studio lub narzędzie SQLCMD, łączenie się z pulą SQL za pośrednictwem zapory usługi puli SQL. 

W poniższym przykładzie zapora jest otwarta tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Punkty końcowe SQL komunikują się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. Jeśli tak, nie będzie można połączyć się z serwerem SQL platformy Azure, chyba że dział IT otworzy port 1433.
>


## <a name="create-a-sql-pool"></a>Tworzenie puli SQL
Poniższy przykład tworzy pulę SQL przy użyciu wcześniej zdefiniowanych zmiennych.  Określa cel usługi jako DW100c, który jest tańszym punktem wyjścia dla puli SQL. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Wymagane parametry:

* **RequestedServiceObjectiveName:** Ilość [jednostek magazynu danych,](what-is-a-data-warehouse-unit-dwu-cdwu.md) których żądasz. Zwiększenie tej kwoty zwiększa koszt obliczeń. Aby uzyskać listę obsługiwanych wartości, zobacz [limity pamięci i współbieżności](memory-concurrency-limits.md).
* **DatabaseName:** Nazwa puli SQL, którą tworzysz.
* **Nazwa serwera:** Nazwa serwera używanego do tworzenia.
* **ResourceGroupName:** Grupa zasobów, której używasz. Aby znaleźć grupy zasobów dostępne w ramach subskrypcji, użyj polecenia cmdlet Get-AzureResource.
* **Edition**: Musi być "DataWarehouse", aby utworzyć pulę SQL.

Opcjonalne parametry:

- **CollationName**: sortowanie domyślne, gdy sortowanie nie jest określone, to COLLATE SQL_Latin1_General_CP1_CI_AS. Nie można zmienić sortowania w bazie danych.
- **MaxSizeBytes**: Domyślny maksymalny rozmiar bazy danych to 240 TB. Maksymalny rozmiar ogranicza dane magazynu wierszy. Istnieje nieograniczona przestrzeń dyskowa dla danych kolumnowych.

Aby uzyskać więcej informacji na temat opcji parametrów, zobacz [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start. 

> [!TIP]
> Jeśli planujesz kontynuować pracę z późniejszymi samouczkami szybki start, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować, użyj następujących kroków, aby usunąć wszystkie zasoby utworzone przez ten szybki start w witrynie Azure portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Następne kroki

Utworzono teraz pulę SQL, utworzono regułę zapory, połączono z pulą SQL i uruchomiono kilka zapytań. Aby dowiedzieć się więcej, przejdź do artykułu [Załaduj dane do puli SQL.](load-data-from-azure-blob-storage-using-polybase.md)
