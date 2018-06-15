---
title: Korzystanie z widoków T-SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące korzystania z widoków T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 28280a067e7008c20361e0a0041c81ba84e7f74c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525999"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Widoki w magazynie danych Azure SQL
Porady dotyczące korzystania z widoków T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. 

## <a name="why-use-views"></a>Dlaczego warto używać widoków?
Widoki można na wiele sposobów poprawy jakości rozwiązania.  W tym artykule omówiono sposób wzbogacić rozwiązania z widoków, a także ograniczenia, które należy wziąć pod uwagę kilka przykładów.

> [!NOTE]
> Składnia CREATE VIEW nie omówiono w tym artykule. Aby uzyskać więcej informacji, zobacz [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) dokumentacji.
> 
> 

## <a name="architectural-abstraction"></a>Abstrakcja architektury
Wspólnego wzorca aplikacji jest ponownie utworzyć tabel za pomocą tworzenia tabeli jako wybierz (CTAS) następuje zmiana nazwy wzorzec podczas ładowania danych obiektu.

Poniższy przykład dodaje Data nowych rekordów do wymiaru daty. Należy zwrócić uwagę, jak nową tabelę DimDate_New, utworzenia, a następnie zmieniona na zastępowanie wersji oryginalnej tabeli.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Jednak ta metoda może spowodować tabel znajdujących się i znika z punktu widzenia użytkownika, a także "Tabela nie istnieje" komunikaty o błędach. Widoków może służyć do zapewnienia użytkowników z warstwą prezentacji spójne, przy jednoczesnym obiektów zostały zmienione. Dzięki dostępowi do danych za pośrednictwem widoków, użytkownicy nie muszą widoczność w tabelach. Ta warstwa umożliwia spójną obsługę użytkowników podczas sprawdzeniu, czy projektanci magazyn danych można rozwijać modelu danych. Można sformułować tabel oznacza, że projektanci mogą używać CTAS w celu zwiększenia wydajności podczas procesu ładowania danych.   

## <a name="performance-optimization"></a>Optymalizacja wydajności
Widoki można również używać do wymuszania zoptymalizowanych pod kątem wydajności sprzężenia między tabelami. Na przykład widoku można zastosować klucza dystrybucji nadmiarowe jako część łącząca kryteriów, aby zminimalizować przenoszenia danych. Inną zaletą widoku można wymusić określonej kwerendy lub łącząca wskazówki. Korzystanie z widoków w ten sposób gwarantuje, że sprzężenia zawsze są realizowane w sposób optymalny uniknięcie do zapamiętania prawidłowa konstrukcja dla ich sprzężeń przez użytkowników.

## <a name="limitations"></a>Ograniczenia
Widoki SQL Data Warehouse są przechowywane jako zawierają tylko metadane. W związku z tym nie dostępne są następujące opcje:

* Nie jest dostępna opcja powiązanie schematu
* Nie można aktualizować tabel podstawowych za pośrednictwem widoku
* Nie można tworzyć widoki w tabelach tymczasowych
* Nie jest obsługiwane dla ROZWIJANIA / wskazówki NOEXPAND
* Brak widoków indeksowanych w usłudze SQL Data Warehouse

## <a name="next-steps"></a>Kolejne kroki
Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


