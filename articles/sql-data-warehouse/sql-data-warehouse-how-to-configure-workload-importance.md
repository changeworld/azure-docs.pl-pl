---
title: Konfigurowanie znaczenie obciążenia w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić poziom ważności żądania.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7121d2f022f9c9a5bbc02f04955d2857f3ec986f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241234"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Konfigurowanie znaczenie obciążenia w usłudze Azure SQL Data Warehouse

Ustawianie ważności w usłudze SQL Data Warehouse pozwala na wpłynąć na planowanie zapytania. Zapytania o ważności wyższym zostanie zaplanowane do uruchomienia przed zapytania o niższe ważności. Aby przypisać wagę do zapytania, musisz utworzyć klasyfikatora obciążenia.

## <a name="create-a-workload-classifier-with-importance"></a>Tworzenie klasyfikatora obciążenia o ważności

Często w przypadku scenariusza magazynu danych masz użytkowników, którzy potrzebują ich zapytań do szybkiego uruchamiania.  Użytkownik może być kierownictwo firmy, którzy muszą uruchamiać raporty lub użytkownik może być analityk uruchamianie zapytań ad hoc. Możesz utworzyć klasyfikatora obciążenia, aby przypisać wagę do zapytania.  Poniższe przykłady korzystać z nowych [tworzenie klasyfikatora obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) składnię tworzenia dwoma klasyfikatorami.  Membername może być jednego użytkownika lub grupę. Klasyfikacje indywidualnego użytkownika mają pierwszeństwo przed klasyfikacje roli. Aby znaleźć istniejących użytkowników magazynu danych, uruchom polecenie:

```sql
Select name from sys.sysusers
```

Aby utworzyć klasyfikatora obciążenie, dla użytkownika o ważności wyższym Uruchom:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Aby utworzyć klasyfikatora obciążenia dla użytkownika, uruchamianie zapytań ad hoc przy użyciu niższe ważności, uruchom:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat Zarządzanie obciążeniami zobacz [klasyfikacji obciążenia](sql-data-warehouse-workload-classification.md)
- Aby uzyskać więcej informacji na temat znaczenia, zobacz [znaczenie obciążenia](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Przejdź do zarządzania i monitorowania obciążenia znaczenie ](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
