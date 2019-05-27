---
title: Klasyfikacja magazynu danych SQL | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące za pomocą funkcji klasyfikacji, aby zarządzać współbieżności, ważność i zasoby obliczeniowe dla zapytań w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: c4630e11ef4001c546aecfe74dbced346b6d30e4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000123"
---
# <a name="sql-data-warehouse-workload-classification"></a>Usługa SQL Data Warehouse obciążenia klasyfikacji

W tym artykule opisano proces klasyfikacji obciążenia usługa SQL Data Warehouse przypisywania klasy zasobów i znaczenie na przychodzące żądania.

## <a name="classification"></a>Klasyfikacja

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Obciążenie Zarządzanie Klasyfikacja umożliwia obciążenia zasady mają być stosowane do żądań poprzez przypisywanie [klasy zasobów](resource-classes-for-workload-management.md#what-are-resource-classes) i [znaczenie](sql-data-warehouse-workload-importance.md).

Dostępnych jest wiele sposobów klasyfikowania obciążeń magazynowania danych, najprostszy i najbardziej powszechnym klasyfikacji jest obciążenia i zapytania. Ładowania danych przy użyciu insert, update i usuwania instrukcji.  Wykonujesz zapytanie o dane przy użyciu wybiera. Rozwiązań magazynowania danych często mają zasady obciążenia dla działalności obciążenia, takich jak przypisywanie z wyższą klasą zasobu więcej zasobów. Różne obciążenia zasad można zastosować do zapytania, takie jak niższe ważności w porównaniu do załadowania działań.

Można również subclassify obciążeń obciążenia i zapytań. Podrzędna daje większą kontrolę obciążeń. Na przykład obciążeń związanych z zapytaniami może obejmować odświeżania modułu, pulpit nawigacyjny zapytań lub zapytań ad hoc. Można klasyfikować, każda z tych obciążeń związanych z zapytaniami, za pomocą innego zasobu klas ani ustawień ważności. Obciążenia można również korzystać z podrzędna. Duże przekształcenia można przypisać do większych klas zasobów. Wyższe znaczenie może służyć do upewnij się, że kluczowe dane dotyczące sprzedaży jest modułu ładującego przed danych o pogodzie lub strumieniowe źródło danych społecznościowych.

Nie wszystkie instrukcje są klasyfikowane nie wymagają zasobów lub potrzebujesz znaczenie w celu wywierania wpływu na wykonanie.  Polecenia DBCC, instrukcji BEGIN, zatwierdzenia i ROLLBACK TRANSACTION nie zostały sklasyfikowane.

## <a name="classification-process"></a>Proces klasyfikacji

Klasyfikacji w usłudze SQL Data Warehouse odbywa się już dziś przez przypisywanie użytkowników do roli, która ma do odpowiedniej klasy zasobów, które są przypisane do niego przy użyciu [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Możliwości w celu scharakteryzowania żądania po przekroczeniu logowania do klasy zasobów jest ograniczona dzięki tej możliwości. Bardziej zaawansowane metody klasyfikacji jest teraz dostępna z [tworzenie KLASYFIKATORA obciążenia](/sql/t-sql/statements/create-workload-classifier-transact-sql) składni.  Przy użyciu tej składni SQL Data Warehouse użytkownicy mogą przypisywać znaczenie i klasa zasobów do żądania.  

> [!NOTE]
> Klasyfikacja jest oceniane na podstawie danego żądania. Wiele żądań w jednej sesji mogą być klasyfikowane w różny sposób.

## <a name="classification-precedence"></a>Pierwszeństwo klasyfikacji

W trakcie procesu klasyfikacji priorytet jest w miejscu, aby określić, która klasa zasobu jest przypisany. Klasyfikacja na podstawie użytkownika bazy danych mają pierwszeństwo przed członkostwo w roli. Jeśli tworzysz klasyfikatora, która mapuje użytkownika bazy danych użytkownika do klasy zasobów mediumrc. Następnie mapować klasy zasobów largerc RoleA roli bazy danych (które Użytkownik_a jest elementem członkowskim). Klasyfikator, która mapuje użytkownika bazy danych do klasy zasobów mediumrc ma pierwszeństwo klasyfikatorem, który mapuje do roli bazy danych RoleA largerc klasy zasobów.

Jeśli użytkownik jest członkiem wielu ról przy użyciu klas zasobów różnych przypisane lub dopasowywany w wielu klasyfikatorów, użytkownik otrzymuje najwyższy przypisania klasy zasobów.  To zachowanie jest zgodne z istniejącego zachowania przypisania klasy zasobów.

## <a name="system-classifiers"></a>Klasyfikatorów systemu

Klasyfikacja obciążenie ma klasyfikatorów obciążenie systemu. Klasyfikatorów system mapowanie istniejących członkostw roli klasy zasobów do alokacji zasobów klasy zasobów znaczenie normalny. Nie można porzucić klasyfikatorów systemu. Aby wyświetlić klasyfikatorów systemu, można uruchomić poniższe zapytanie:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Mieszanie zasobów przypisania klasy za pomocą klasyfikatorów

Klasyfikatorów system utworzone w Twoim imieniu podać ścieżkę łatwo przeprowadzić migrację do klasyfikacji obciążenia. Za pomocą mapowania roli klasy zasobów o priorytecie klasyfikacji, może prowadzić do błędów klasyfikacji, jak przystąpieniem do tworzenia nowych klasyfikatorów o ważności.

Rozważmy następujący scenariusz:

- Istniejący magazyn danych ma DBAUser przypisany do roli klasy zasobów largerc użytkownika bazy danych. Przydział Klasa zasobu zostało wykonane z sp_addrolemember.
- Magazyn danych został zaktualizowany o zarządzanie obciążeniami.
- Aby przetestować nową składnię klasyfikacji, rola bazy danych DBARole (czyli DBAUser członkiem), ma klasyfikatora, utworzenia dla nich mapowania mediumrc i o wysokiej ważności.
- Gdy DBAUser loguje się i uruchamia kwerendę, zapytanie zostanie przypisany do largerc. Ponieważ użytkownik ma pierwszeństwo przed członkostwo w roli.

Aby uprościć rozwiązywania problemów z błędną klasyfikację, zalecane jest usunięcie mapowania roli klasy zasobów, tworzenie klasyfikatorów obciążenia.  Poniższy kod zwraca istniejący zasób klasy członkostwa w roli.  Uruchom [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) dla każdej nazwy elementu członkowskiego zwrócony z odpowiedniej klasy zasobów.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat tworzenia klasyfikatora, zobacz [tworzenie KLASYFIKATORA obciążenia (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Zobacz samouczek Szybki Start dotyczące sposobu tworzenia klasyfikatora obciążenia [tworzenie klasyfikatora obciążenia](quickstart-create-a-workload-classifier-tsql.md).
- Zobacz artykuły z instrukcjami dotyczącymi [skonfigurować wagę obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz sposób [zarządzanie i monitorowanie Zarządzanie obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Zobacz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) służy do wyświetlania kwerend i ważność przypisana.