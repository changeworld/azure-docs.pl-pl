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
ms.openlocfilehash: 4aa0e7da1e77334a8b9048dab4a461133f220bd1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582464"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurowanie ważności obciążenia w usłudze Azure Synapse Analytics

Ustawienie ważności w Synapse SQL dla platformy Azure Synapse umożliwia wpływ na planowanie zapytań. Kwerendy o wyższym znaczeniu będą zaplanowane do uruchomienia przed kwerendami o niższym znaczeniu. Aby przypisać znaczenie do kwerend, należy utworzyć klasyfikator obciążenia.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążenia z ważnością

Często w scenariuszu magazynu danych masz użytkowników, którzy potrzebują ich zapytania, aby uruchomić szybko.  Użytkownik może być kierownictwo firmy, którzy muszą uruchamiać raporty lub użytkownik może być analitykiem z kwerendą adhoc. Tworzenie klasyfikatora obciążenia, aby przypisać znaczenie do kwerendy.  Poniższe przykłady używają nowej składni [klasyfikatora tworzenia obciążenia,](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) aby utworzyć dwa klasyfikatory.  Nazwa członkowna może być pojedynczym użytkownikiem lub grupą. Klasyfikacje poszczególnych użytkowników mają pierwszeństwo przed klasyfikacjami ról. Aby znaleźć istniejących użytkowników magazynu danych, uruchom:

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
