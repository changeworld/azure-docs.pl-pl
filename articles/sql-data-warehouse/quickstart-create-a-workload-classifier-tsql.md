---
title: 'Szybki Start: Tworzenie klasyfikatora obciążeń — T-SQL '
description: Użyj języka T-SQL, aby utworzyć klasyfikator obciążeń o wysokiej ważności.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 1375605b6dab385b53af9212023767003e686e60
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646293"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Szybki Start: Tworzenie klasyfikatora obciążeń przy użyciu języka T-SQL

W tym przewodniku szybki start utworzysz klasyfikator obciążeń o wysokiej ważności dla DYREKTORa w organizacji. Ten klasyfikator obciążeń umożliwi zajęcie przez dyrektorów naczelnych pierwszeństwa przed innymi zapytaniami o niższej ważności kolejki.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Utworzenie bazy danych w usłudze SQL Data Warehouse może skutkować powstaniem nowej usługi płatnej.  Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już SQL Data Warehouse i masz uprawnienia sterujące bazą danych. Jeżeli chcesz utworzyć taki magazyn, skorzystaj z przewodnika [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Utwórz nazwę logowania dla TheCEO

Utwórz SQL Server Logowanie do uwierzytelniania w bazie danych `master` przy użyciu polecenia [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) for "TheCEO".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Tworzenie użytkownika

[Tworzenie użytkownika](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO" w mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Tworzenie klasyfikatora obciążeń

Utwórz [klasyfikator obciążeń](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) dla "TheCEO" o wysokiej ważności.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Wyświetl istniejące klasyfikatory

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

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z magazynu danych. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz pozycję Magazyn danych.

    ![Oczyszczanie zasobów](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, wybierz pozycję **Uruchom**.

3. Aby usunąć magazyn danych, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń**.

4. Aby usunąć utworzony serwer SQL, wybierz pozycję **mynewserver-20180430.Database.Windows.NET** na poprzednim obrazie, a następnie wybierz pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, wybierz pozycję Moja **zasobów**, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

- Klasyfikator obciążeń został utworzony. Uruchom kilka zapytań jako TheCEO, aby zobaczyć, jak są one wykonywane. Zobacz sekcję [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) , aby wyświetlić zapytania i przypisane znaczenie.
- Aby uzyskać więcej informacji na temat zarządzania obciążeniem Azure SQL Data Warehouse, zobacz temat [ważność obciążeń](sql-data-warehouse-workload-importance.md) i [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).
- Zapoznaj się z artykułami z artykułu jak, aby [skonfigurować ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz jak [zarządzać i monitorować zarządzanie obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
