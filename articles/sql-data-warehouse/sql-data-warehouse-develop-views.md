---
title: Korzystanie z widoków języka T-SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące Korzystanie z widoków języka T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d4321f8aef6e754d8a1c5b16ac82b4fa62c40949
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873607"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Widoki w usłudze Azure SQL Data Warehouse
Porady dotyczące Korzystanie z widoków języka T-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. 

## <a name="why-use-views"></a>Dlaczego warto używać widoków?
Widoki można na wiele różnych sposobów poprawy jakości rozwiązania.  W tym artykule przedstawiono kilka przykładów sposobu wzbogacić swoje rozwiązanie przy użyciu widoków, a także ograniczenia, które należy wziąć pod uwagę.

> [!NOTE]
> W tym artykule nie omówiono składni, aby utworzyć widok. Aby uzyskać więcej informacji, zobacz [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) dokumentacji.
> 
> 

## <a name="architectural-abstraction"></a>Abstrakcja architektury
Typowym wzorcem aplikacji jest ponownie utworzyć tabel za pomocą utworzyć TABLE AS SELECT (CTAS) następuje zmiana nazwy wzorzec jednoczesnym ładowania danych obiektu.

Poniższy przykład dodaje nowe rekordy do wymiaru daty. Zwróć uwagę, jak nową tabelę DimDate_New, jest pierwszy tworzone i następnie zmieniona na zastępowanie wersji oryginalnej tabeli.

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

Jednak takie podejście może spowodować tabel znajdujących się i znika z punktu widzenia użytkownika, a także "Tabela nie istnieje" komunikaty o błędach. Widoki może służyć do udzielania z warstwą prezentacji spójne przy jednoczesnym obiekty źródłowe zostały zmienione. Dzięki możliwości udzielania dostępu do danych za pośrednictwem widoków, użytkownicy nie muszą wgląd w tabelach źródłowych. Ta warstwa zapewnia spójne środowisko użytkownika, mimo że zapewnienie, że magazyn danych w projektantach można rozwijać modelu danych. Możliwości rozwój tabel podstawowych oznacza, że projektanci mogą używać instrukcji CTAS, aby zmaksymalizować wydajność podczas procesu ładowania danych.   

## <a name="performance-optimization"></a>Optymalizacja wydajności
Widoki być wykorzystywane w taki sposób, aby wymusić zoptymalizowane pod kątem wydajności sprzężenia między tabelami. Na przykład widoku można zastosować klucza dystrybucji nadmiarowe jako część przyłączany kryteria, aby zminimalizować przenoszenia danych. Kolejną korzyścią wynikającą z widoku można wymusić użytego zapytania lub przyłączany wskazówki. Korzystanie z widoków w ten sposób gwarantuje sprzężeń są zawsze wykonywane w sposób optymalny, unikając konieczności użytkownikom na zapamiętywanie poprawne konstrukcji dla ich sprzężeń.

## <a name="limitations"></a>Ograniczenia
Widoki w usłudze SQL Data Warehouse są przechowywane jako tylko metadane. W związku z tym nie dostępne są następujące opcje:

* Nie ma żadnej opcji powiązanie schematu
* Nie można zaktualizować tabel podstawowych za pośrednictwem widoku
* Nie można utworzyć widoków z użyciem tabele tymczasowe
* Nie jest obsługiwane dla rozwiń / wskazówki NOEXPAND
* Istnieją nie widoków indeksowanych w usłudze SQL Data Warehouse

## <a name="next-steps"></a>Kolejne kroki
Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


