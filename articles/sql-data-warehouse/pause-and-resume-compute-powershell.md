---
title: 'Szybki Start: Wstrzymywanie & wznawiania obliczeń — PowerShell '
description: Użyj programu PowerShell, aby wstrzymać obliczenia w Azure SQL Data Warehouse w celu oszczędności kosztów. Wznów obliczanie, gdy wszystko jest gotowe do korzystania z magazynu danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 148f3f99aac5ce01bd88f1efaecb5821ae94f50a
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539162"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-azure-powershell"></a>Szybki Start: Wstrzymywanie i wznawianie obliczeń w Azure SQL Data Warehouse z Azure PowerShell

Użyj Azure PowerShell, aby wstrzymać obliczenia w Azure SQL Data Warehouse w celu oszczędności kosztów. [Wznów Obliczanie](sql-data-warehouse-manage-compute-overview.md) , gdy wszystko jest gotowe do korzystania z magazynu danych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym przewodniku szybki start założono, że masz już SQL Data Warehouse, które można wstrzymywać i wznawiać. Jeśli trzeba ją utworzyć, możesz utworzyć magazyn danych o nazwie **mySampleDataWarehouse**przy użyciu programu [Create and Connect-Portal](create-data-warehouse-portal.md) .

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby sprawdzić, której subskrypcji używasz, uruchom polecenie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Jeśli musisz użyć innej subskrypcji niż domyślna, uruchom polecenie [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Wyszukiwanie informacji w magazynie danych

Znajdź nazwę bazy danych, nazwę serwera oraz grupę zasobów magazynu danych, który chcesz wstrzymać i wznowić.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla magazynu danych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij opcję **Bazy danych SQL** po lewej stronie witryny Azure Portal.
3. Wybierz opcję **mySampleDataWarehouse** ze strony **Bazy danych SQL**. Zostanie otwarty magazyn danych.

    ![Nazwa serwera i grupa zasobów](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Zapisz nazwę magazynu danych, która jest nazwą bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów.
6. Jeśli serwer to foo.database.windows.net, użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na poprzedniej ilustracji pełną nazwą serwera jest newserver-20171113.database.windows.net. Porzuć sufiks i Użyj **newserver-20171113** jako nazwy serwera w poleceniu cmdlet programu PowerShell.

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby zaoszczędzić koszty, możesz wstrzymywać i wznawiać zasoby obliczeniowe na żądanie. Na przykład jeśli baza danych nie jest używana w porze nocnej i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia. Nie jest naliczana opłata za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Opłata za magazyn jest jednak nadal naliczana.

Aby wstrzymać bazę danych, należy użyć polecenia cmdlet [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) . Poniższy przykład wstrzymuje magazyn danych o nazwie **mySampleDataWarehouse** hostowanej na serwerze o nazwie **newserver-20171113**. Serwer należy do grupy zasobów platformy Azure o nazwie Moja **resourceName**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

Odmiana, w tym następnym przykładzie, pobiera bazę danych do obiektu $database. Następnie przekazuje obiekt do [zawieszenia-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Wyniki są przechowywane w obiekcie resultDatabase. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Wznów Obliczanie

Aby uruchomić bazę danych, należy użyć polecenia cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) . W poniższym przykładzie jest uruchamiana baza danych o nazwie mySampleDataWarehouse hostowana na serwerze o nazwie newserver-20171113. Serwer należy do grupy zasobów platformy Azure o nazwie Moja ResourceName.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

Odmiana, w tym następnym przykładzie, pobiera bazę danych do obiektu $database. Następnie przekazuje obiekt do [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) i zapisuje wyniki w $resultDatabase. Końcowe polecenie wyświetla wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Sprawdź stan operacji hurtowni danych

Aby sprawdzić stan magazynu danych, użyj polecenia cmdlet [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) .

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Płacisz za jednostki magazynu danych i dane przechowywane w Twoim magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, Wstrzymaj obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij magazyn danych.

    ![Oczyszczanie zasobów](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, kliknij przycisk **Uruchom**.

3. Aby usunąć magazyn danych i nie płacić za obliczenia oraz magazynowanie, kliknij przycisk **Usuń**.

4. Aby usunąć utworzony serwer SQL, kliknij pozycję **mynewserver-20171113.Database.Windows.NET**, a następnie kliknij pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.


## <a name="next-steps"></a>Następne kroki

Teraz wstrzymane i wznowione obliczenia dla magazynu danych. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.

> [!div class="nextstepaction"]
> [Ładowanie danych do SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
