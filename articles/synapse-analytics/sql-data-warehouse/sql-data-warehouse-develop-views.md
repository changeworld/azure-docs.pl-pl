---
title: Korzystanie z widoków T-SQL
description: Porady dotyczące korzystania z widoków T-SQL i tworzenia rozwiązań w puli synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633395"
---
# <a name="views-in-synapse-sql-pool"></a>Widoki w puli SQL Synapse

Widoki mogą być używane na wiele różnych sposobów, aby poprawić jakość rozwiązania.

Pula SQL obsługuje zarówno widoki standardowe, jak i zmaterializowane. Obie są tabelami wirtualnymi utworzonymi za pomocą wyrażeń SELECT i prezentowanymi kwerendom jako tabele logiczne.

Widoki hermetyzują złożoność obliczeń wspólnych danych i dodaj warstwę abstrakcji do zmian obliczeniowych, dzięki czemu nie ma potrzeby przepisywania kwerend.

## <a name="standard-view"></a>Widok standardowy

Widok standardowy oblicza swoje dane za każdym razem, gdy widok jest używany. Na dysku nie są przechowywane żadne dane. Ludzie zazwyczaj używają widoków standardowych jako narzędzia, które pomaga organizować obiekty logiczne i kwerendy w bazie danych.

Aby użyć widoku standardowego, kwerenda musi bezpośrednio odwoływać się do niego. Aby uzyskać więcej informacji, zobacz tworzenie dokumentacji [WIDOKU.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Widoki w puli SQL są przechowywane tylko jako metadane. W związku z tym następujące opcje nie są dostępne:

* Nie ma opcji wiązania schematu
* Nie można aktualizować tabel bazowych za pomocą widoku
* Widoki nie mogą być tworzone za ich doami zaaukańctwo
* Nie ma wsparcia dla podpowiedzi EXPAND/NOEXPAND
* W puli SQL nie ma widoków indeksowanych

Widoki standardowe mogą być używane do wymuszania sprzężeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów łączenia, aby zminimalizować przenoszenie danych.

Inną zaletą widoku może być wymuszenie określonej kwerendy lub dołączenie do podpowiedzi. Korzystanie z widoków w ten sposób gwarantuje, że sprzężenia są zawsze wykonywane w sposób optymalny, unikając konieczności użytkowników do zapamiętania poprawnej konstrukcji dla ich sprzężenia.

## <a name="materialized-view"></a>Zmaterializowany widok

Zmaterializowany widok wstępnie oblicza, przechowuje i przechowuje swoje dane w puli SQL, podobnie jak w tabeli. Za każdym razem, gdy używany jest zmaterialowany widok, nie jest wymagana żadna ponowna obliczenie.

Gdy dane są ładowane do tabel podstawowych, pula SQL synchronicznie odświeża zmaterializowane widoki.  Optymalizator kwerend automatycznie używa wdrożonych zmaterializowanych widoków, aby zwiększyć wydajność kwerendy, nawet jeśli widoki nie są odwoływane w kwerendzie.  

Zapytania korzystające najbardziej ze zmaterializowanych widoków są złożone zapytania (zazwyczaj kwerendy ze sprzężenia i agregacji) w dużych tabelach, które produkują mały zestaw wyników.  

Szczegółowe informacje na temat składni widoku materializowanego i innych wymagań można znaleźć w [1944 R. Tworzenie zmaterializowanego widoku jako wybierz](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Aby uzyskać wskazówki dotyczące dostrajania zapytań, sprawdź [dostrajanie wydajności za pomocą zmaterializowanych widoków](performance-tuning-materialized-views.md).

## <a name="example"></a>Przykład

Typowy wzorzec aplikacji jest ponowne tworzenie tabel przy użyciu CREATE TABLE AS SELECT (CTAS), po którym następuje wzorzec zmiany nazwy obiektu podczas ładowania danych.  

Poniższy przykład dodaje nowe rekordy daty do wymiaru daty. Zwróć uwagę, jak najpierw tworzona jest nowa tabela, DimDate_New, a następnie zmieniona nazwa, aby zastąpić oryginalną wersję tabeli.

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Jednak takie podejście może spowodować tabele pojawiające się i znikają z widoku użytkownika wraz z wydawaniem "tabela nie istnieje" komunikaty o błędach.

Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji, podczas gdy nazwy obiektów leżących pod spodem są zmieniane. Zapewniając dostęp do danych za pośrednictwem widoków, użytkownicy nie potrzebują widoczności w tabelach źródłowych.

Ta warstwa zapewnia spójne środowisko użytkownika, zapewniając jednocześnie, że projektanci magazynu danych mogą ewoluować model danych. Możliwość ewoluowania podstawowych tabel oznacza, że projektanci mogą używać CTAS, aby zmaksymalizować wydajność podczas procesu ładowania danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [omówienie tworzenia puli SQL](sql-data-warehouse-overview-develop.md).
