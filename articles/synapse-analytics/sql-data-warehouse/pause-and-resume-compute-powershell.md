---
title: Wstrzymanie i wznowienie obliczeń w puli sql synapse za pomocą programu Azure PowerShell
description: Za pomocą programu Azure PowerShell można wstrzymać i wznowić pulę SQL Synapse (magazyn danych). zasobów obliczeniowych.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 44ce56238140de90145f69e966c94be8572c7749
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252306"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Szybki start: wstrzymanie i wznowienie obliczeń w puli sql synapse za pomocą programu Azure PowerShell

Za pomocą programu Azure PowerShell można wstrzymać i wznowić zasoby obliczeniowe puli SQL Synapse (hurtownia danych).
Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ten przewodnik Szybki start zakłada, że masz już pulę SQL, którą można wstrzymać i wznowić. Jeśli chcesz go utworzyć, możesz użyć [create and connect - portal,](create-data-warehouse-portal.md) aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Aby zobaczyć, której subskrypcji używasz, uruchom [get-azsubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Jeśli chcesz użyć innej subskrypcji niż domyślna, uruchom [program Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>Wyszukuj informacje o puli SQL

Znajdź nazwę bazy danych, nazwę serwera i grupę zasobów dla puli SQL, którą zamierzasz wstrzymać i wznowić.

Wykonaj następujące kroki, aby znaleźć informacje o lokalizacji dla puli SQL:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Kliknij **pozycję Usługa Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie witryny Azure portal.
1. Wybierz **mySampleDataWarehouse** ze strony **usługi Azure Synapse Analytics (dawniej SQL DW).** Zostanie otwarta pula SQL.

    ![Nazwa serwera i grupa zasobów](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Zapisz nazwę puli SQL, która jest nazwą bazy danych. Ponadto zanotuj nazwę serwera i grupy zasobów.
1. Użyj tylko pierwszej części nazwy serwera w poleceniach cmdlet programu PowerShell. Na poprzednim obrazie pełna nazwa serwera jest sqlpoolservername.database.windows.net. Używamy **sqlpoolservername** jako nazwy serwera w pochocimiu programu PowerShell.

## <a name="pause-compute"></a>Wstrzymanie obliczeń

Aby zaoszczędzić koszty, można wstrzymać i wznowić zasoby obliczeniowe na żądanie. Na przykład jeśli nie używasz bazy danych w nocy i w weekendy, można wstrzymać go w tych godzinach i wznowić go w ciągu dnia.

>[!NOTE]
>Nie ma żadnych opłat za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Jednak nadal są naliczane opłaty za przechowywanie.

Aby wstrzymać bazę danych, należy użyć polecenia cmdlet [Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Poniższy przykład wstrzymuje pulę SQL o nazwie **mySampleDataWarehouse** hostowane na serwerze o nazwie **sqlpoolservername**. Serwer znajduje się w grupie zasobów platformy Azure o nazwie **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

Poniższy przykład pobiera bazę danych do obiektu $database. Następnie przesunie obiekt do [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Wyniki są przechowywane w wyniku obiektuBaza danych. Końcowe polecenie pokazuje wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Wznów obliczeń

Aby uruchomić bazę danych, należy użyć polecenia cmdlet [Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) W poniższym przykładzie rozpoczyna się baza danych o nazwie **mySampleDataWarehouse** hostowana na serwerze o nazwie **sqlpoolservername**. Serwer znajduje się w grupie zasobów platformy Azure o nazwie **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

W następnym przykładzie pobiera bazę danych do obiektu $database. Następnie potoki obiektu [do Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i przechowuje wyniki w $resultDatabase. Końcowe polecenie pokazuje wyniki.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Sprawdź stan operacji puli SQL

Aby sprawdzić stan puli SQL, użyj polecenia cmdlet [Get-AzSqlDataBaseActivity.](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć pulę SQL.

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij pulę SQL.

    ![Oczyszczanie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. Gdy pula SQL jest wstrzymana, zostanie wyświetlony przycisk **Start.**  Aby wznowić obliczenia, kliknij przycisk **Uruchom**.

3. Aby usunąć pulę SQL, aby nie była naliczana opłata za zasoby obliczeniowe lub magazyn, kliknij przycisk **Usuń**.

4. Aby usunąć utworzony serwer SQL, kliknij **przycisk sqlpoolservername.database.windows.net**, a następnie kliknij przycisk **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o puli SQL, przejdź do artykułu [Załaduj dane do puli SQL.](load-data-from-azure-blob-storage-using-polybase.md) Aby uzyskać dodatkowe informacje dotyczące zarządzania możliwościami obliczeniowymi, zobacz [omówienie zarządzania obliczeniami.](sql-data-warehouse-manage-compute-overview.md)
