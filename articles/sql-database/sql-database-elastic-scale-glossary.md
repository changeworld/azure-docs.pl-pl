---
title: Słownik narzędzi usługi elastycznych baz danych | Dokumentacja firmy Microsoft
description: Wyjaśnienie pojęcia dotyczące narzędzi elastycznej bazy danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585631"
---
# <a name="elastic-database-tools-glossary"></a>Słownik narzędzi usługi elastycznych baz danych

Poniższe terminy są zdefiniowane dla [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md), funkcja usługi Azure SQL Database. Te narzędzia są używane do zarządzania [dzielenie map na fragmenty](sql-database-elastic-scale-shard-map-management.md)i obejmują [biblioteki klienckiej](sql-database-elastic-database-client-library.md), [narzędzie do dzielenia i scalania](sql-database-elastic-scale-overview-split-and-merge.md), [pul elastycznych](sql-database-elastic-pool.md)i [zapytania](sql-database-elastic-query-overview.md). 

Te warunki są używane w [dodanie fragmentu, za pomocą narzędzi elastycznych baz danych](sql-database-elastic-scale-add-a-shard.md) i [używanie klasy RecoveryManager do Rozwiązywanie problemów z mapą fragmentów](sql-database-elastic-database-recovery-manager.md).

![Elastyczne skalowanie warunki][1]

**Baza danych**: Baza danych Azure SQL Database. 

**Routing zależny od danych**: Funkcje, które umożliwia połączenie z fragmentem danego klucza fragmentowania określonych aplikacji. Zobacz [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md). Porównaj  **[zapytania z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md)**.

**Mapowanie fragmentów w postaci globalnego**: Mapy między kluczy fragmentowania oraz ich odpowiednich fragmentów w ramach **zestaw fragmentów**. Mapowania fragmentów globalne są przechowywane w **Menedżera mapowań fragmentów**. Porównaj **mapowanie fragmentów w postaci lokalnej**.

**Mapowanie fragmentów w postaci listy**: Mapowania fragmentów, w których fragmentowania kluczy są mapowane indywidualnie. Porównaj **mapowania fragmentów w zakresie**.   

**Mapowanie fragmentów w postaci lokalnej**: Przechowywane na fragment, lokalnego podzielonej na fragmenty mapy zawiera mapowania dla podfragmentów, które znajdują się na fragmentu.

**Zapytanie z wieloma fragmentami**: Zdolność do wystawiania zapytanie do wielu fragmentów; zestawy wyników są zwracane, używając semantyki UNION ALL (znany także jako "zapytania wielokierunkowego"). Porównaj **routing zależny od danych**.

**Wielodostępne** i **pojedynczej dzierżawy**: To pokazuje bazę pojedynczego dzierżawy i wielodostępną bazą danych:

![Pojedynczych i wielu dzierżawców baz danych](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Poniżej przedstawiono reprezentację **podzielonej na fragmenty** pojedynczych i wielu dzierżawców baz danych. 

![Pojedynczych i wielu dzierżawców baz danych](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapowanie fragmentów w postaci zakresu**: Mapowania fragmentów w postaci w jakiej strategią dystrybucji fragmentu opiera się na wiele zakresów wartości ciągłe. 

**Odwoływać się do tabel**: Tabele, które nie są podzielone na fragmenty, ale są replikowane między fragmentami. Na przykład kody pocztowe mogą być przechowywane w tabeli referencyjnej. 

**Fragment**: Usługi Azure SQL database, która przechowuje dane z zestawu danych podzielonych na fragmenty. 

**Skrypty dotyczące elastyczności**: Możliwość wykonywania zarówno **skalowanie w poziomie** i **skalowanie w pionie**.

**Tabele podzielonej na fragmenty**: Tabele, które są podzielone na fragmenty, czyli, którego dane są rozproszone między fragmentami, na podstawie ich wartości klucza fragmentowania. 

**Klucz fragmentowania**: Wartość kolumny, która określa sposób dystrybuowania danych między fragmentami. Typ wartości może być jedną z następujących: **int**, **bigint**, **varbinary**, lub **uniqueidentifier**. 

**Zestaw fragmentów**: Kolekcja fragmentów, które są przypisane do tego samego mapowania fragmentów w Menedżera mapowań fragmentów.  

**Podfragment**: Wszystkie dane skojarzone z pojedynczą wartość klucza fragmentowania na fragmentu. Podfragment jest najmniejsza możliwa przenoszenia danych, gdy Dystrybucja tabel podzielonej na fragmenty. 

**Mapowanie fragmentów w postaci**: Zestaw mapowań między kluczy fragmentowania oraz ich odpowiednich fragmentów.

**Menedżera mapowań fragmentów**: Zarządzanie obiektu i magazynu danych zawierającego map(s) fragmentów, lokalizacji fragmentów i mapowania dla co najmniej jeden zestaw fragmentów.

![Mapowania][2]

## <a name="verbs"></a>Zlecenia
**Skalowanie w poziomie**: Czynność skalowania w poziomie (lub w) to zbiór fragmentów, dodając lub usuwając fragmentami w celu mapowania fragmentów w postaci, jak pokazano poniżej.

![Skalowanie w poziomie i pionie][3]

**Scal**: Akt przenoszenie podfragmentów z dwóch fragmentów w jednym fragmencie i aktualizowanie mapowania fragmentów w związku z tym.

**Przenieś Podfragmentu**: Czynność Przenoszenie jednego podfragment w innym fragmencie. 

**Fragment**: Czynność partycjonowanie poziome identycznie ustrukturyzowanych danych w wielu bazach danych na podstawie klucza fragmentowania.

**Podziel**: Czynność przenoszenie kilku podfragmentów z jednego fragmentu do innego fragmentu (zazwyczaj nowe). Klucz fragmentowania jest dostarczone przez użytkownika jako punkt podziału.

**Skalowanie w pionie**: Czynność skalowania w górę (lub w dół) rozmiar obliczeń poszczególnych fragmentów. Na przykład zmiana fragmentu warstwy standardowej na Premium (co powoduje większej ilości zasobów obliczeniowych). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

