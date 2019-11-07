---
title: Monitorowanie znaczenia obciążenia i zarządzanie nim
description: Dowiedz się, jak zarządzać i monitorować ważność poziomu żądania w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692712"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Zarządzanie i monitorowanie znaczenia obciążeń w Azure SQL Data Warehouse

Zarządzaj i monitoruj ważność poziomu żądań w Azure SQL Data Warehouse przy użyciu widoków widoków DMV i wykazu.

## <a name="monitor-importance"></a>Ważność monitora

Monitoruj ważność przy użyciu kolumny Nowa ważność w dynamicznym widoku zarządzania [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
Poniższe zapytanie monitorowania przedstawia czas przesyłania i godzinę rozpoczęcia dla zapytań. Przejrzyj czas przesyłania i czas rozpoczęcia wraz z ważnością, aby zobaczyć, jak znaczenie ma wpływ na planowanie.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Aby dojrzeć się do sposobu planowania zapytań, użyj widoków wykazu.

## <a name="manage-importance-with-catalog-views"></a>Zarządzanie ważnośćmi przy użyciu widoków wykazu

Widok wykazu sys. workload_management_workload_classifiers zawiera informacje o klasyfikatorach w wystąpieniu Azure SQL Data Warehouse. Aby wykluczyć klasyfikatory zdefiniowane przez system, które są mapowane na klasy zasobów, wykonaj następujący kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Widok wykazu, [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), zawiera informacje na temat parametrów używanych podczas tworzenia klasyfikatora.  Poniższe zapytanie pokazuje, że ExecReportsClassifier został utworzony na ```membername``` parametr dla wartości ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Wyniki zapytania](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Aby uprościć Rozwiązywanie problemów z błędną klasyfikacją, zaleca się usunięcie mapowań ról klasy zasobów podczas tworzenia klasyfikatorów obciążeń. Poniższy kod zwraca istniejące członkostwa ról klasy zasobów. Uruchom sp_droprolemember dla każdego ```membername``` zwróconego z odpowiedniej klasy zasobów.
Poniżej znajduje się przykład sprawdzania istnienia przed porzuceniem klasyfikatora obciążenia:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat klasyfikacji, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).
- Aby uzyskać więcej informacji na temat ważności, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do konfigurowania ważności obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md)
