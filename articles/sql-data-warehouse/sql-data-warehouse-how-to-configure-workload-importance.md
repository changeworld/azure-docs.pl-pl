---
title: Konfigurowanie znaczenia obciążenia
description: Dowiedz się, jak ustawić ważność poziomu żądania w usłudze Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196802"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurowanie ważności obciążenia w usłudze Azure Synapse Analytics

Ustawienie znaczenie w analizie SQL dla usługi Azure Synapse pozwala na planowanie zapytań. Zapytania o wyższym znaczeniu zostaną zaplanowane do uruchomienia przed zapytaniami o niższej ważności. Aby przypisać ważność do zapytań, należy utworzyć klasyfikator obciążeń.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążeń o ważności

Często w scenariuszu hurtowni danych masz użytkowników, którzy potrzebują swoich zapytań do szybkiego uruchomienia.  Użytkownik może być dyrektorem firmy, którzy muszą uruchamiać raporty lub użytkownik może być analitykiem, na którym działa zapytanie ad hoc. Tworzysz klasyfikator obciążeń, aby przypisać ważność do zapytania.  W poniższych przykładach użyto nowej składni [klasyfikatora tworzenia obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) w celu utworzenia dwóch klasyfikatorów.  Członekname może być pojedynczym użytkownikiem lub grupą. Klasyfikacje poszczególnych użytkowników mają pierwszeństwo przed klasyfikacjami ról. Aby znaleźć istniejących użytkowników magazynu danych, uruchom polecenie:

```sql
Select name from sys.sysusers
```

Aby utworzyć klasyfikator obciążeń dla użytkownika o wyższej ważności:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Aby utworzyć klasyfikator obciążeń dla użytkownika, na którym są wykonywane zapytania ad hoc z niższym przebiegiem ważności:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat zarządzania obciążeniem, zobacz [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md)
- Aby uzyskać więcej informacji na temat ważności, zobacz [ważność obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do pozycji Zarządzaj i monitoruj ważność obciążenia](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
