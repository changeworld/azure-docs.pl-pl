---
title: 'Szybki start: tworzenie klasyfikatora obciążenia — T-SQL'
description: Użyj T-SQL, aby utworzyć klasyfikator obciążenia o dużej ważności.
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
ms.openlocfilehash: 9d41aa3447caebfeeffc05c36b5073f57c3e16cd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350803"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Szybki start: tworzenie klasyfikatora obciążenia przy użyciu funkcji T-SQL

W tym przewodniku Szybki start szybko utworzysz klasyfikator obciążenia o dużym znaczeniu dla dyrektora generalnego organizacji. Ten klasyfikator obciążenia pozwoli zapytań ceo mieć pierwszeństwo przed innymi zapytaniami o mniejszym znaczeniu w kolejce.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

> [!NOTE]
> Utworzenie wystąpienia usługi SQL Analytics w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi podlegającej rozliczaniu.  Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start zakłada, że masz już magazyn danych SQL i masz uprawnienia CONTROL DATABASE. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Tworzenie logowania do TheCEO

Utwórz logowanie uwierzytelniania `master` programu SQL Server w bazie danych przy użyciu [create login](/sql/t-sql/statements/create-login-transact-sql) dla 'TheCEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Tworzenie użytkownika

[Tworzenie użytkownika](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO", w mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Tworzenie klasyfikatora obciążenia

Tworzenie [klasyfikatora obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) dla "TheCEO" o dużej ważności.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Wyświetlanie istniejących klasyfikatorów

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Wstrzymując obliczenia, opłaty są naliczane tylko za magazyn danych. Gdy będziesz gotowy do pracy z danymi, wznowić obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby oczyścić zasoby.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz w magazynie danych.

    ![Oczyszczanie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj.** Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz przycisk **Start**.

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe lub magazyn, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, zaznacz **mynewserver-20180430.database.windows.net** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz **myResourceGroup**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

- Teraz utworzono klasyfikator obciążenia. Uruchom kilka zapytań jako TheCEO, aby zobaczyć, jak działają. Zobacz [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) aby wyświetlić zapytania i przypisane znaczenie.
- Aby uzyskać więcej informacji na temat zarządzania obciążeniami usługi SQL Analytics, zobacz [Ważność obciążenia](sql-data-warehouse-workload-importance.md) i [klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).
- Zobacz artykuły infigurajnokonfigurowanie [ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz sposób [zarządzania zarządzaniem obciążeniami i monitorowania.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
