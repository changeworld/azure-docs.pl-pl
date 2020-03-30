---
title: Korzystanie z widoków T-SQL
description: Porady dotyczące używania widoków T-SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.
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
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351520"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Widoki w magazynie danych SQL platformy Azure
Widoki mogą być używane na wiele różnych sposobów, aby poprawić jakość rozwiązania. 

Usługa Azure SQL Data Warehouse obsługuje widoki standardowe i zmaterializowane. Obie są tabelami wirtualnymi utworzonymi za pomocą wyrażeń SELECT i prezentowanymi kwerendom jako tabele logiczne. Widoki hermetyzują złożoność obliczeń wspólnych danych i dodaj warstwę abstrakcji do zmian obliczeniowych, dzięki czemu nie ma potrzeby przepisywania kwerend.

## <a name="standard-view"></a>Widok standardowy
Widok standardowy oblicza swoje dane za każdym razem, gdy widok jest używany. Na dysku nie są przechowywane żadne dane. Ludzie zazwyczaj używają widoków standardowych jako narzędzia, które pomaga organizować obiekty logiczne i kwerendy w bazie danych. Aby użyć widoku standardowego, kwerenda musi bezpośrednio odwoływać się do niego. Aby uzyskać więcej informacji, zobacz tworzenie dokumentacji [WIDOKU.](/sql/t-sql/statements/create-view-transact-sql)

Widoki w magazynie danych SQL są przechowywane tylko jako metadane. W związku z tym następujące opcje nie są dostępne:
* Nie ma opcji wiązania schematu
* Tabel bazowych nie można aktualizować za pomocą widoku
* Nie można tworzyć widoków za stołami tymczasowymi
* Nie ma wsparcia dla podpowiedzi EXPAND/NOEXPAND
* W magazynie danych SQL nie ma widoków indeksowanych

Widoki standardowe mogą być używane do wymuszania sprzężeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów łączenia, aby zminimalizować przenoszenie danych. Inną zaletą widoku może być wymuszenie określonej kwerendy lub dołączenie do podpowiedzi. Korzystanie z widoków w ten sposób gwarantuje, że sprzężenia są zawsze wykonywane w sposób optymalny, unikając konieczności użytkowników do zapamiętania poprawnej konstrukcji dla ich sprzężenia.

## <a name="materialized-view"></a>Zmaterializowany widok
Zmaterializowany widok wstępnie oblicza, przechowuje i przechowuje swoje dane w usłudze Azure SQL Data Warehouse, podobnie jak w tabeli. Za każdym razem, gdy używany jest zmaterialowany widok, nie jest wymagana żadna ponowna obliczenie. Gdy dane są ładowane do tabel podstawowych, usługa Azure SQL Data Warehouse synchronicznie odświeża zmaterializowane widoki.  Optymalizator kwerend automatycznie używa wdrożonych zmaterializowanych widoków w celu zwiększenia wydajności kwerendy, nawet jeśli widoki nie są odwoływane w kwerendzie.  Zapytania korzystające najbardziej ze zmaterializowanych widoków są złożone zapytania (zazwyczaj kwerendy ze sprzężenia i agregacji) w dużych tabelach, które produkują mały zestaw wyników.  

Szczegółowe informacje na temat składni widoku materializowanego i innych wymagań można znaleźć w [1944 R. Tworzenie zmaterializowanego widoku jako wybierz](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Aby uzyskać wskazówki dotyczące dostrajania zapytań, sprawdź [dostrajanie wydajności za pomocą zmaterializowanych widoków](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Przykład
Typowy wzorzec aplikacji jest ponowne tworzenie tabel przy użyciu CREATE TABLE AS SELECT (CTAS), a następnie wzorzec zmiany nazwy obiektu podczas ładowania danych.  Poniższy przykład dodaje nowe rekordy daty do wymiaru daty. Zwróć uwagę, jak najpierw tworzona jest nowa tabela, DimDate_New, a następnie zmieniona nazwa, aby zastąpić oryginalną wersję tabeli.

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
Jednak to podejście może spowodować tabele pojawiające się i znikają z widoku użytkownika, a także "tabela nie istnieje" komunikaty o błędach. Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji, podczas gdy nazwy obiektów bazowych są zmieniane. Zapewniając dostęp do danych za pośrednictwem widoków, użytkownicy nie potrzebują widoczności w tabelach źródłowych. Ta warstwa zapewnia spójne środowisko użytkownika, zapewniając jednocześnie, że projektanci magazynu danych mogą ewoluować model danych. Możliwość ewoluowania podstawowych tabel oznacza, że projektanci mogą używać CTAS, aby zmaksymalizować wydajność podczas procesu ładowania danych.   

## <a name="next-steps"></a>Następne kroki
Więcej porad dla deweloperów znajduje się w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


