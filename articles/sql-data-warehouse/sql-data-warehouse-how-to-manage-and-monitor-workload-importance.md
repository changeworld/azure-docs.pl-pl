---
title: Zarządzanie i monitorowanie obciążenia znaczenie w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać i monitorować poziom ważności żądania
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 8d6cd80f569be58b520a53faa0d4bda8f86af104
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003494"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Zarządzanie i monitorowanie obciążenia znaczenie w usłudze Azure SQL Data Warehouse

Zarządzanie i monitorowanie żądań poziomu ważności w usłudze Azure SQL Data Warehouse przy użyciu dynamicznych widoków zarządzania i widokach katalogów.

## <a name="monitor-importance"></a>Monitor ważności

Monitorowanie znaczenie przy użyciu nowej kolumny znaczenie w [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) dynamicznego widoku zarządzania.
Poniższe zapytanie pokazuje czas zgłoszenia i uruchomienie zapytania dotyczące monitorowania. Przejrzyj podczas przesyłania i uruchomienie wraz z znaczenie, aby zobaczyć, jak znaczenie wpływ na planowanie.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Aby Spójrz dalej na do jak zapytania są harmonogramu, użyj widoków wykazu.

## <a name="manage-importance-with-catalog-views"></a>Zarządzanie znaczenie za pomocą widoków wykazu

W widoku wykazu sys.workload_management_workload_classifiers zawiera informacje dotyczące klasyfikatorów w wystąpieniu usługi Azure SQL Data Warehouse. Aby wykluczyć klasyfikatorów zdefiniowaną przez system, które mapują do klasy zasobów wykonaj następujący kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Widoku wykazu [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), zawiera informacje na temat parametrów użytych w procesie tworzenia klasyfikatora.  Poniżej przedstawiono zapytanie, które ExecReportsClassifier został utworzony na ```membername``` parametr wartościami ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Wyniki zapytania](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Aby uprościć rozwiązywania problemów z błędną klasyfikację, zalecane jest usunięcie mapowania roli klasy zasobów, tworzenie klasyfikatorów obciążenia. Poniższy kod zwraca istniejący zasób klasy członkostwa w roli. Uruchom sp_droprolemember dla każdej ```membername``` zwrócony z odpowiedniej klasy zasobów.
Poniżej przedstawiono przykład sprawdzanie istnienia przed porzuceniem klasyfikatora obciążenia:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat klasyfikacji, zobacz [klasyfikacji obciążenia](sql-data-warehouse-workload-classification.md).
- Aby uzyskać więcej informacji na temat znaczenia, zobacz [znaczenie obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do konfigurowania znaczenie obciążenia ](sql-data-warehouse-how-to-configure-workload-importance.md)
