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
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901707"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Widoki w Azure SQL Data Warehouse
Widoki mogą być używane na wiele różnych sposobów ulepszania jakości rozwiązania. 

Azure SQL Data Warehouse obsługuje widoki standardowe i z materiałami. Obie są tabelami wirtualnymi utworzonymi za pomocą SELECT Expressions i prezentowanych jako tabele logiczne. Widoki hermetyzują złożoność wspólnych obliczeń danych i dodają warstwę abstrakcji do obliczeń zmian, aby nie trzeba było ponownie pisać zapytań.

## <a name="standard-view"></a>Widok standardowy
Widok standardowy oblicza swoje dane za każdym razem, gdy widok jest używany. Brak danych przechowywanych na dysku. Użytkownicy zazwyczaj używają widoków standardowych jako narzędzia, które ułatwiają organizowanie obiektów logicznych i zapytań w bazie danych. Aby użyć widoku standardowego, zapytanie musi nawiązać bezpośrednie odwołanie do niego. Aby uzyskać więcej informacji, zobacz dokumentację [tworzenia widoku](/sql/t-sql/statements/create-view-transact-sql) .

Widoki w SQL Data Warehouse są przechowywane jako metadane. W związku z tym następujące opcje są niedostępne:
* Brak opcji powiązania schematu
* Nie można zaktualizować tabel bazowych w widoku
* Nie można tworzyć widoków za pośrednictwem tabel tymczasowych
* Nie ma obsługi wskazówek rozwiń/NOEXPAND
* Brak widoków indeksowanych w SQL Data Warehouse

Widoki standardowe mogą być używane do wymuszania przełączeń zoptymalizowanych pod kątem wydajności między tabelami. Na przykład widok może zawierać nadmiarowy klucz dystrybucji jako część kryteriów sprzężenia, aby zminimalizować przenoszenie danych. Inną zaletą widoku może być wymuszenie konkretnej kwerendy lub dołączenia wskazówki. Użycie widoków w ten sposób gwarantuje, że sprzężenia są zawsze wykonywane w optymalny sposób, unikając potrzeb użytkowników do zapamiętania odpowiedniej konstrukcji sprzężeń.

## <a name="materialized-view"></a>Zmaterializowany widok
Widok z materiałami umożliwia wstępne obliczenie, przechowywanie i przechowywanie danych w Azure SQL Data Warehouse podobnie jak w przypadku tabeli. Za każdym razem, gdy jest używany widok materiałowy, nie jest wymagana żadna konieczność obliczenia. Gdy dane są ładowane do tabel podstawowych, Azure SQL Data Warehouse synchronicznie odświeża widoki z materiałami.  Optymalizator zapytań automatycznie używa wdrożonych widoków w celu zwiększenia wydajności zapytań, nawet jeśli w zapytaniu nie ma odwołań do widoków.  Zapytania korzystające z większości widoków z materiałami są złożonymi zapytaniami (zwykle zapytania z sprzężeniami i agregacjami) w dużych tabelach, które tworzą mały zestaw wyników.  

Aby uzyskać szczegółowe informacje na temat składniowe widoku i innych wymagań, zapoznaj się z tematem [Tworzenie przykładowego widoku jako wyboru](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  

Aby uzyskać wskazówki dotyczące dostrajania zapytań, sprawdź [dostrajanie wydajności w widokach z materiałami](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views). 

## <a name="example"></a>Przykład
Typowym wzorcem aplikacji jest ponowne tworzenie tabel przy użyciu CREATE TABLE jako SELECT (CTAS), a następnie zmiany nazwy wzorca obiektu podczas ładowania danych.  Poniższy przykład dodaje nowe rekordy dat do wymiaru daty. Zwróć uwagę, jak nowa tabela, DimDate_New, została najpierw utworzona, a następnie zmieniono jej nazwę, aby zastąpić oryginalną wersję tabeli.

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
Jednak takie podejście może spowodować, że tabele pojawiają się i znikają z widoku użytkownika, a także "tabela nie istnieje". Widoki mogą służyć do zapewnienia użytkownikom spójnej warstwy prezentacji podczas zmiany nazwy obiektów bazowych. Zapewniając dostęp do danych za pomocą widoków, użytkownicy nie potrzebują wglądu w tabele źródłowe. Ta warstwa zapewnia spójne środowisko użytkownika, a jednocześnie zapewnia, że projektanci magazynu danych mogą rozwijać model danych. Możliwość rozwijania tabel bazowych oznacza, że projektanci mogą używać CTAS do maksymalizowania wydajności podczas procesu ładowania danych.   

## <a name="next-steps"></a>Następne kroki
Więcej porad programistycznych znajdziesz w artykule [Omówienie programowania w usłudze SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


