---
title: Monitorowanie znaczenia obciążenia i zarządzanie nim
description: Dowiedz się, jak zarządzać i monitorować ważność poziomu żądań w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fdac8ebc56291292559f6718de5f0092c7d8e063
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350469"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Zarządzanie i monitorowanie znaczenia obciążenia w usłudze Azure Synapse Analytics

Zarządzanie i monitorowanie zarządzania poziomem zarządzania żądaniem usługi SQL Analytics w usłudze Azure Synapse przy użyciu obiektów DMV i widoków wykazu.

## <a name="monitor-importance"></a>Monitorowanie ważności

Monitorowanie ważności przy użyciu nowej kolumny ważności w widoku zarządzania dynamicznego [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)
Poniższa kwerenda monitorująca pokazuje czas przesyłania i czas rozpoczęcia kwerend. Przejrzyj czas przesyłania i czas rozpoczęcia wraz ze znaczeniem, aby zobaczyć, jak ważne wpłynęło planowanie.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Aby dokładniej przyjrzeć się harmonogramowi zapytań, użyj widoków katalogu.

## <a name="manage-importance-with-catalog-views"></a>Zarządzanie ważnośćą za pomocą widoków katalogu

Widok katalogu sys.workload_management_workload_classifiers zawiera informacje o klasyfikatorach. Aby wykluczyć klasyfikatory zdefiniowane przez system, które mapują do klas zasobów, wykonaj następujący kod:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Widok katalogu [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)zawiera informacje o parametrach używanych do tworzenia klasyfikatora.  Poniższa kwerenda pokazuje, że ExecReportsClassifier został utworzony na ```membername``` parametr dla wartości z ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![wyniki kwerendy](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Aby uprościć błędy w rozwiązywaniu problemów, zalecamy usunięcie mapowania ról klasy zasobów podczas tworzenia klasyfikatorów obciążenia. Poniższy kod zwraca istniejące członkostwa roli klasy zasobów. Uruchom sp_droprolemember dla każdego ```membername``` zwróconego z odpowiedniej klasy zasobów.
Poniżej znajduje się przykład sprawdzania istnienia przed upuszczeniem klasyfikatora obciążenia:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat klasyfikacji, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).
- Aby uzyskać więcej informacji na temat ważności, zobacz [Znaczenie obciążenia pracą](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do konfigurowania ważności obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md)
