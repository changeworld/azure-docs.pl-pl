---
title: 'Szybki Start: Konfigurowanie izolacji obciążeń — T-SQL'
description: Aby skonfigurować izolację obciążenia, Użyj języka T-SQL.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bd3ad98116b18a77a77e8f6f327689d0ebb7dd21
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200519"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Szybki Start: Konfigurowanie izolacji obciążenia przy użyciu języka T-SQL

W tym przewodniku szybki start utworzysz grupę obciążeń i klasyfikator w celu zarezerwowania zasobów na potrzeby ładowania danych. Grupa obciążenia przydzieli 20% zasobów systemowych na ładowane dane.  Klasyfikator obciążeń przypisze żądania do grupy obciążeń ładowania danych.  Dzięki obciążeniu 20% w przypadku ładowania danych są one gwarantowane zasobom, które trafią umowy SLA.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Tworzenie wystąpienia usługi SQL Analytics w usłudze Azure Synapse Analytics może skutkować powstaniem nowej, rozliczanej usłudze.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Wymagania wstępne
 
W tym przewodniku szybki start założono, że masz już wystąpienie usługi SQL Analytics w usłudze Azure Synapse i że masz uprawnienia sterujące bazą danych. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure portal](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Utwórz nazwę logowania dla ładowania danych

Utwórz SQL Server Logowanie do uwierzytelniania w bazie danych `master` przy użyciu polecenia [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) for "ELTLogin'".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Tworzenie użytkownika

[Tworzenie użytkownika](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin" w mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Tworzenie grupy obciążeń
Utwórz [grupę obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) dla obciążeń dataloads z izolacją 20%.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Tworzenie klasyfikatora obciążeń

Utwórz [klasyfikator obciążeń](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) , aby zamapować ELTLogin do grupy obciążeń dataloads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Wyświetlanie istniejących grup obciążeń i klasyfikatorów oraz wartości czasu wykonywania

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

- Jeśli chcesz zachować dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz puli SQL. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję Magazyn danych.

    ![Oczyszczanie zasobów](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz pozycję **Uruchom**.

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, wybierz pozycję **mynewserver-20180430.Database.Windows.NET** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz pozycję Moja **zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

- Grupa obciążeń została już utworzona. Uruchom kilka zapytań jako ELTLogin, aby zobaczyć, jak są one wykonywane. Zobacz sekcję [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) , aby wyświetlić zapytania i przypisaną grupę obciążeń.
- Aby uzyskać więcej informacji na temat zarządzania obciążeniem usługi SQL Analytics, zobacz [zarządzanie obciążeniami](sql-data-warehouse-workload-management.md) i [izolacja obciążeń](sql-data-warehouse-workload-isolation.md).
