---
title: Konfigurowanie znaczenia obciążenia
description: Dowiedz się, jak ustawić ważność poziomu żądań w usłudze Azure Synapse Analytics.
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
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633365"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurowanie ważności obciążenia w usłudze Azure Synapse Analytics

Ustawienie ważności w Synapse SQL dla platformy Azure Synapse umożliwia wpływ na planowanie zapytań. Kwerendy o wyższym znaczeniu będą zaplanowane do uruchomienia przed kwerendami o niższym znaczeniu. Aby przypisać znaczenie do kwerend, należy utworzyć klasyfikator obciążenia.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążenia z ważnością

Często w scenariuszu magazynu danych masz użytkowników, którzy potrzebują ich zapytania, aby uruchomić szybko.  Użytkownik może być kierownictwo firmy, którzy muszą uruchamiać raporty lub użytkownik może być analitykiem z kwerendą adhoc. Tworzenie klasyfikatora obciążenia, aby przypisać znaczenie do kwerendy.  Poniższe przykłady używają nowej składni [klasyfikatora tworzenia obciążenia,](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby utworzyć dwa klasyfikatory. `Membername`może być pojedynczym użytkownikiem lub grupą. Klasyfikacje poszczególnych użytkowników mają pierwszeństwo przed klasyfikacjami ról. Aby znaleźć istniejących użytkowników magazynu danych, uruchom:

```sql
Select name from sys.sysusers
```

Aby utworzyć klasyfikator obciążenia, dla użytkownika o wyższym znaczeniu uruchomić:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Aby utworzyć klasyfikator obciążenia dla użytkownika z uruchomionymi kwerendami adhoc o niższym znaczeniu:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania [obciążeniem,](sql-data-warehouse-workload-classification.md) zobacz Klasyfikacja obciążeń
- Aby uzyskać więcej informacji na temat ważności, zobacz [Znaczenie obciążenia pracą](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do zarządzania i monitorowania znaczenia obciążenia](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
