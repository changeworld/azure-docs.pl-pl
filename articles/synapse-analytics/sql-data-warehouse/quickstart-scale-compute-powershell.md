---
title: Skalowanie obliczeń dla puli SQL Synapse (Azure PowerShell)
description: Można skalować obliczenia dla puli sql synapse (magazyn danych) przy użyciu programu Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350790"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Szybki start: skalowanie obliczeń dla puli SQL Synapse za pomocą programu Azure PowerShell

Można skalować obliczenia dla puli sql synapse (magazyn danych) przy użyciu programu Azure PowerShell. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ten przewodnik Szybki start zakłada, że masz już pulę SQL, którą można skalować. Jeśli chcesz go utworzyć, użyj [create and connect - portal,](create-data-warehouse-portal.md) aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby zobaczyć, której subskrypcji używasz, uruchom [get-azsubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Jeśli chcesz użyć innej subskrypcji niż domyślna, uruchom [program Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Wyszukiwanie informacji w magazynie danych

Znajdź nazwę bazy danych, nazwę serwera oraz grupę zasobów magazynu danych, który chcesz wstrzymać i wznowić.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla magazynu danych.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Kliknij **pozycję Usługa Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie nawigacji w witrynie Azure portal.
3. Wybierz **mySampleDataWarehouse** ze strony **usługi Azure Synapse Analytics (dawniej SQL DW),** aby otworzyć magazyn danych.

    ![Nazwa serwera i grupa zasobów](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Zanotuj nazwę magazynu danych, która będzie używana jako nazwa bazy danych. Pamiętaj, że magazyn danych jest jednym z typów bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów. W poleceniach pauzy i wznowienia użyjesz nazwy serwera i grupy zasobów.
5. Użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na poprzednim obrazie pełna nazwa serwera jest sqlpoolservername.database.windows.net. Używamy **sqlpoolservername** jako nazwy serwera w pochocimiu programu PowerShell.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

W puli SQL można zwiększyć lub zmniejszyć zasoby obliczeniowe, dostosowując jednostki magazynu danych. Postępując według czynności opisanych w artykule [Tworzenie i łączenie — portal](create-data-warehouse-portal.md) utworzono bazę danych **mySampleDataWarehouse** z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić jednostki magazynu danych, należy użyć polecenia cmdlet Programu PowerShell [Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) W poniższym przykładzie ustawia jednostki magazynu danych na DW300c dla bazy danych **mySampleDataWarehouse**, która jest hostowana w grupie **zasobów resourcegroupname** na serwerze **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Sprawdzanie stanu magazynu danych

Aby wyświetlić bieżący stan magazynu danych, należy użyć polecenia cmdlet [programu PowerShell get-AzSqlDatabase.](/powershell/module/az.sql/get-azsqldatabase) To polecenie cmdlet pokazuje stan bazy danych **mySampleDataWarehouse** w **resourcegroupgroupname** ResourceGroup i **sqlpoolservername.database.windows.net**serwera .

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Wynik operacji będzie podobny do poniższego:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

**Stan** bazy danych jest widoczny w danych wyjściowych. W tym przypadku możesz zobaczyć, że baza danych jest w trybie online.  Po uruchomieniu tego polecenia użytkownik powinien otrzymać wartość stanu Online, Wstrzymywanie, Wznawianie, Skalowanie lub Wstrzymana.

Aby wyświetlić stan samodzielnie, użyj następującego polecenia:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Następne kroki
Teraz dowiesz się, jak skalować obliczenia dla puli SQL. Aby dowiedzieć się więcej o puli SQL, przejdź do samouczka ładowania danych.

> [!div class="nextstepaction"]
>[Ładowanie danych do puli SQL](load-data-from-azure-blob-storage-using-polybase.md)
