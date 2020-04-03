---
title: Klasyfikacja obciążenia
description: Wskazówki dotyczące używania klasyfikacji do zarządzania współbieżnością, ważność i zasoby obliczeniowe dla zapytań w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 67f863826a2e9eb1bffcb316754ad5c40a2f2bb1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583134"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Klasyfikacja obciążenia usługi Azure Synapse Analytics

W tym artykule opisano proces klasyfikacji obciążenia przypisywania grupy obciążenia i ważność do żądań przychodzących za pomocą puli Sql Synapse w usłudze Azure Synapse.

## <a name="classification"></a>Klasyfikacja

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klasyfikacja zarządzania obciążeniem umożliwia stosowanie zasad obciążenia do żądań poprzez przypisywanie [klas zasobów](resource-classes-for-workload-management.md#what-are-resource-classes) i [ważność](sql-data-warehouse-workload-importance.md).

Chociaż istnieje wiele sposobów klasyfikowania obciążeń magazynowania danych, najprostszą i najbardziej powszechną klasyfikacją jest ładowanie i wykonywanie zapytań. Wczytujesz dane za pomocą instrukcji wstawiania, aktualizowania i usuwania.  Kwerenda danych za pomocą wybiera. Rozwiązanie do magazynowania danych często będzie miało zasady obciążenia dla działań obciążenia, takie jak przypisywanie wyższej klasy zasobów z większą liczebnością zasobów. Różne zasady obciążenia mogą mieć zastosowanie do zapytań, takich jak mniejsze znaczenie w porównaniu do działań obciążenia.

Można również podklasyfikować obciążenia obciążenia i zapytania. Podklasyfikowanie zapewnia większą kontrolę nad obciążeniami. Na przykład obciążeń kwerendy mogą składać się z odświeżania modułu, kwerend pulpitu nawigacyjnego lub kwerend ad hoc. Można sklasyfikować każde z tych obciążeń kwerendy z różnych klas zasobów lub ustawienia ważności. Obciążenie może również korzystać z podklasyfikacji. Duże przekształcenia mogą być przypisane do większych klas zasobów. Większe znaczenie może być wykorzystane do zapewnienia, że kluczowe dane sprzedaży są ładowarką przed danymi pogodowymi lub zestawieniem danych społecznościowych.

Nie wszystkie instrukcje są klasyfikowane, ponieważ nie wymagają zasobów lub mają znaczenie, aby wpłynąć na wykonanie.  Polecenia DBCC, BEGIN, COMMIT i ROLLBACK TRANSACTION nie są klasyfikowane.

## <a name="classification-process"></a>Proces klasyfikacji

Klasyfikacja puli SQL Synapse w usłudze Azure Synapse jest osiągana dzisiaj przez przypisanie użytkowników do roli, która ma przypisaną do niej odpowiednią klasę zasobów przy użyciu [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Możliwość scharakteryzowania żądań poza logowaniem do klasy zasobów jest ograniczona dzięki tej możliwości. Bogatsza metoda klasyfikacji jest teraz dostępna ze składnią [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql)  W tej składni użytkownicy puli Synapse SQL mogą przypisywać ważność i `workload_group` ilość zasobów systemowych przypisanych do żądania za pośrednictwem parametru. 

> [!NOTE]
> Klasyfikacja jest oceniana na podstawie żądania. Wiele żądań w jednej sesji można sklasyfikować inaczej.

## <a name="classification-weighting"></a>Ważenie klasyfikacji

W ramach procesu klasyfikacji trwa trwa ustalanie, która grupa obciążenia jest przypisana.  Waga idzie w następujący sposób:

|Parametr klasyfikatora |Waga   |
|---------------------|---------|
|NAZWA CZŁONKOW:UŻYTKOWNIK      |64       |
|NAZWA CZŁONKOW:ROLA      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Parametr `membername` jest obowiązkowy.  Jednak jeśli nazwa członkowna określona jest użytkownikiem bazy danych zamiast roli bazy danych, wagi dla użytkownika jest wyższa, a tym samym ten klasyfikator jest wybrany.

Jeśli użytkownik jest członkiem wielu ról z różnych klas zasobów przypisane lub dopasowane w wielu klasyfikatorów, użytkownik otrzymuje najwyższe przypisanie klasy zasobów.  To zachowanie jest zgodne z istniejącym zachowaniem przypisania klasy zasobów.

## <a name="system-classifiers"></a>Klasyfikatory systemowe

Klasyfikacja obciążenia ma klasyfikatory obciążenia systemu. Klasyfikatory systemowe mapują istniejące członkostwo w roli klasy zasobów na alokacje zasobów klasy zasobów o normalnym znaczeniu. Klasyfikatorów systemowych nie można upuścić. Aby wyświetlić klasyfikatory systemowe, można uruchomić poniższą kwerendę:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Mieszanie przydziałów klas zasobów z klasyfikatorami

Klasyfikatory systemowe utworzone w Twoim imieniu zapewniają łatwą ścieżkę migracji do klasyfikacji obciążenia. Za pomocą mapowania roli klasy zasobów z pierwszeństwem klasyfikacji, może prowadzić do błędnej klasyfikacji, jak rozpocząć tworzenie nowych klasyfikatorów z ważnością.

Poniżej przedstawiono przykładowy scenariusz:

- Istniejący magazyn danych ma użytkownika bazy danych DBAUser przypisany do roli klasy zasobów largerc. Przypisanie klasy zasobów zostało wykonane za pomocą sp_addrolemember.
- Magazyn danych jest teraz aktualizowany o zarządzanie obciążeniem.
- Aby przetestować nową składnię klasyfikacji, rola bazy danych DBARole (której DBAUser jest członkiem), ma klasyfikator utworzony dla nich mapowanie ich do mediumrc i duże znaczenie.
- Gdy DBAUser loguje się i uruchamia kwerendę, kwerenda zostanie przypisana do largerc. Ponieważ użytkownik ma pierwszeństwo przed członkostwem roli.

Aby uprościć błędy w rozwiązywaniu problemów, zalecamy usunięcie mapowania ról klasy zasobów podczas tworzenia klasyfikatorów obciążenia.  Poniższy kod zwraca istniejące członkostwa roli klasy zasobów.  Uruchom [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) dla każdej nazwy elementu członkowskiego zwróconej z odpowiedniej klasy zasobów.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat tworzenia klasyfikatora, zobacz [CREAT WORKLOAD CLASSIFIER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Zobacz szybki start, jak utworzyć klasyfikator obciążenia [Tworzenie klasyfikatora obciążenia](quickstart-create-a-workload-classifier-tsql.md).
- Zobacz artykuły infigurajnokonfigurowanie [ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz sposób [zarządzania i monitorowania zarządzania obciążeniem.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Zobacz [sys.dm_pdw_exec_requests,](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) aby wyświetlić zapytania i przypisane znaczenie.