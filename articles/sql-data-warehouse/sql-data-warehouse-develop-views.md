---
title: Korzystanie z widoków T-SQL w Azure SQL Data Warehouse | Microsoft Docs
description: Porady dotyczące korzystania z widoków T-SQL w Azure SQL Data Warehouse tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479527"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Widoki w Azure SQL Data Warehouse
Porady dotyczące korzystania z widoków T-SQL w Azure SQL Data Warehouse tworzenia rozwiązań. 

## <a name="why-use-views"></a>Dlaczego warto używać widoków?
Widoki mogą być używane na wiele różnych sposobów ulepszania jakości rozwiązania.  W tym artykule przedstawiono kilka przykładów wzbogacania rozwiązania o widoki, a także ograniczenia, które należy wziąć pod uwagę.


> [!IMPORTANT]
> Zapoznaj się z nową składnią widoku z materiałami w obszarze [Utwórz materiałowy widok jako wybrany](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Aby uzyskać więcej informacji, zobacz [Informacje o wersji](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> Składnia instrukcji CREATE VIEW nie została omówiona w tym artykule. Aby uzyskać więcej informacji, zobacz dokumentację [tworzenia widoku](/sql/t-sql/statements/create-view-transact-sql) .
> 

## <a name="architectural-abstraction"></a>Abstrakcja architektury

Typowym wzorcem aplikacji jest ponowne tworzenie tabel przy użyciu CREATE TABLE jako SELECT (CTAS), a następnie zmiany nazwy wzorca obiektu podczas ładowania danych.

Poniższy przykład dodaje nowe rekordy dat do wymiaru daty. Zwróć uwagę, jak nowa tabela, DimDate_New, została najpierw utworzona, a następnie zmieniono jej nazwę, aby zastąpić oryginalną wersję tabeli.

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

Jednak takie podejście może spowodować, że tabele pojawiają się i znikają z widoku użytkownika, a także "tabela nie istnieje". Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji podczas zmiany nazwy obiektów bazowych. Zapewniając dostęp do danych za pomocą widoków, użytkownicy nie potrzebują wglądu w tabele źródłowe. Ta warstwa zapewnia spójne środowisko użytkownika, a jednocześnie zapewnia, że projektanci magazynu danych mogą rozwijać model danych. Możliwość rozwijania tabel bazowych oznacza, że projektanci mogą używać CTAS do maksymalizowania wydajności podczas procesu ładowania danych.   

## <a name="performance-optimization"></a>Optymalizacja wydajności
Widoków można także użyć do wymuszania przełączeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów sprzężenia, aby zminimalizować przenoszenie danych. Inną zaletą widoku może być wymuszenie konkretnej kwerendy lub dołączenia wskazówki. Użycie widoków w ten sposób gwarantuje, że sprzężenia są zawsze wykonywane w optymalny sposób, unikając potrzeb użytkowników do zapamiętania odpowiedniej konstrukcji sprzężeń.

## <a name="limitations"></a>Ograniczenia
Widoki w SQL Data Warehouse są przechowywane jako metadane. W związku z tym następujące opcje są niedostępne:

* Brak opcji powiązania schematu
* Nie można zaktualizować tabel bazowych w widoku
* Nie można tworzyć widoków za pośrednictwem tabel tymczasowych
* Nie ma obsługi wskazówek rozwiń/NOEXPAND
* Brak widoków indeksowanych w SQL Data Warehouse

## <a name="next-steps"></a>Następne kroki
Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


