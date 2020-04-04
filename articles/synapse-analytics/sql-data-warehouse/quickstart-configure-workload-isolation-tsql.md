---
title: 'Szybki start: konfigurowanie izolacji obciążenia — T-SQL'
description: Użyj T-SQL, aby skonfigurować izolację obciążenia.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d3d1b9af0b26fa775beb78b313937890cb9287b3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633773"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Szybki start: konfigurowanie izolacji obciążenia przy użyciu funkcji T-SQL

W tym przewodniku Szybki start szybko utworzysz grupę obciążenia i klasyfikujesz zasoby do zarezerwowania zasobów do ładowania danych. Grupa obciążenia przydzieli 20% zasobów systemowych do ładowania danych.  Klasyfikator obciążenia przypisze żądania do grupy obciążenia obciążenia ładowania danych.  Z 20% izolacji dla ładowania danych, są one gwarantowane zasoby trafić łas.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

> [!NOTE]
> Utworzenie wystąpienia usługi SQL Analytics w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi podlegającej rozliczaniu.  Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start zakłada, że masz już wystąpienie usługi SQL Analytics w usłudze Azure Synapse i masz uprawnienia do bazy danych CONTROL. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Tworzenie logowania dla danychloads

Utwórz logowanie uwierzytelniania `master` programu SQL Server w bazie danych przy użyciu [create login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dla 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Tworzenie użytkownika

[Tworzenie użytkownika](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "ELTLogin", w mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Tworzenie grupy obciążenia

Utwórz [grupę obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dla danychloadów z izolacją 20%.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Tworzenie klasyfikatora obciążenia

Utwórz [klasyfikator obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) do mapowania ELTLogin do grupy obciążenia DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Wyświetlanie istniejących grup i klasyfikatorów obciążenia oraz wartości czasu wykonywania

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz puli SQL. Wstrzymując obliczenia, opłaty są naliczane tylko za magazyn danych. Gdy będziesz gotowy do pracy z danymi, wznowić obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby oczyścić zasoby.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz w magazynie danych.

    ![Oczyszczanie zasobów](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj.** Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz przycisk **Start**.

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe lub magazyn, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, zaznacz **mynewserver-20180430.database.windows.net** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz **myResourceGroup**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

- Utworzono grupę obciążenia. Uruchom kilka zapytań jako ELTLogin, aby zobaczyć, jak działają. Zobacz [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby wyświetlić kwerendy i przypisaną grupę obciążenia.
- Aby uzyskać więcej informacji na temat zarządzania obciążeniami synapse SQL, zobacz [Zarządzanie obciążeniem](sql-data-warehouse-workload-management.md) i [izolacja obciążenia](sql-data-warehouse-workload-isolation.md).
