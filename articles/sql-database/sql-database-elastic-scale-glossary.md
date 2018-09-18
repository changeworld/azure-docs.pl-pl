---
title: Słownik narzędzi usługi elastycznych baz danych | Dokumentacja firmy Microsoft
description: Wyjaśnienie pojęcia dotyczące narzędzi elastycznej bazy danych
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: sstein
ms.openlocfilehash: 387f40204c8ab07ba0205fd74b5c6a549efff0ef
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728850"
---
# <a name="elastic-database-tools-glossary"></a>Słownik narzędzi usługi elastycznych baz danych
Poniższe terminy są zdefiniowane dla [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md), funkcja usługi Azure SQL Database. Te narzędzia są używane do zarządzania [dzielenie map na fragmenty](sql-database-elastic-scale-shard-map-management.md)i obejmują [biblioteki klienckiej](sql-database-elastic-database-client-library.md), [narzędzie do dzielenia i scalania](sql-database-elastic-scale-overview-split-and-merge.md), [pul elastycznych](sql-database-elastic-pool.md)i [zapytania](sql-database-elastic-query-overview.md). 

Te warunki są używane w [dodanie fragmentu, za pomocą narzędzi elastycznych baz danych](sql-database-elastic-scale-add-a-shard.md) i [używanie klasy RecoveryManager do Rozwiązywanie problemów z mapą fragmentów](sql-database-elastic-database-recovery-manager.md).

![Elastyczne skalowanie warunki][1]

**Baza danych**: bazy danych SQL Azure. 

**Routing zależny od danych**: funkcje, które umożliwia połączenie z fragmentem danego klucza fragmentowania określonych aplikacji. Zobacz [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md). Porównaj  **[zapytania z wieloma fragmentami](sql-database-elastic-scale-multishard-querying.md)**.

**Mapowanie fragmentów w postaci globalnego**: mapy między kluczy fragmentowania oraz ich odpowiednich fragmentów w ramach **zestaw fragmentów**. Mapowania fragmentów globalne są przechowywane w **Menedżera mapowań fragmentów**. Porównaj **mapowanie fragmentów w postaci lokalnej**.

**Mapowanie fragmentów w postaci listy**: mapowania fragmentów, w których fragmentowania kluczy są mapowane indywidualnie. Porównaj **mapowania fragmentów w zakresie**.   

**Mapowanie fragmentów w postaci lokalnej**: przechowywane na fragment, lokalnego podzielonej na fragmenty mapy zawiera mapowania dla podfragmentów, które znajdują się na fragmentu.

**Zapytanie z wieloma fragmentami**: możliwość wystawiania zapytanie do wielu fragmentów; zestawów wyników są zwracane, używając semantyki UNION ALL (znany także jako "zapytania wielokierunkowego"). Porównaj **routing zależny od danych**.

**Wielodostępne** i **pojedynczej dzierżawy**: Pokazuje bazę pojedynczego dzierżawy i wielodostępną bazą danych:

![Pojedynczych i wielu dzierżawców baz danych](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Poniżej przedstawiono reprezentację **podzielonej na fragmenty** pojedynczych i wielu dzierżawców baz danych. 

![Pojedynczych i wielu dzierżawców baz danych](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapowanie fragmentów w postaci zakresu**: mapowania fragmentów w postaci w jakiej strategią dystrybucji fragmentu opiera się na wiele zakresów wartości ciągłe. 

**Odwoływać się do tabel**: tabele, które nie są podzielone na fragmenty, ale są replikowane między fragmentami. Na przykład kody pocztowe mogą być przechowywane w tabeli referencyjnej. 

**Fragment**: bazy danych SQL Azure, która przechowuje dane z zestawu danych podzielonych na fragmenty. 

**Skrypty dotyczące elastyczności**: możliwość wykonywania zarówno **skalowanie w poziomie** i **skalowanie w pionie**.

**Tabele podzielonej na fragmenty**: tabele, które są podzielone na fragmenty, czyli, którego dane są rozproszone między fragmentami, na podstawie ich wartości klucza fragmentowania. 

**Klucz fragmentowania**: wartość w kolumnie, który określa sposób dystrybuowania danych między fragmentami. Typ wartości może być jedną z następujących: **int**, **bigint**, **varbinary**, lub **uniqueidentifier**. 

**Zestaw fragmentów**: kolekcja fragmentów, które są przypisane do tego samego mapowania fragmentów w Menedżera mapowań fragmentów.  

**Podfragment**: wszystkie dane skojarzone z pojedynczą wartość klucza fragmentowania na fragmentu. Podfragment jest najmniejsza możliwa przenoszenia danych, gdy Dystrybucja tabel podzielonej na fragmenty. 

**Mapowanie fragmentów w postaci**: zestaw mapowań między kluczy fragmentowania oraz ich odpowiednich fragmentów.

**Menedżera mapowań fragmentów**: Zarządzanie obiektu i magazynu danych zawierającego map(s) fragmentów, lokalizacji fragmentów i mapowania dla co najmniej jeden zestaw fragmentów.

![Mapowania][2]

## <a name="verbs"></a>Zlecenia
**Skalowanie w poziomie**: act skalowania w poziomie (lub w) to zbiór fragmentów, dodając lub usuwając fragmentami w celu mapowania fragmentów w postaci, jak pokazano poniżej.

![Skalowanie w poziomie i pionie][3]

**Scal**: czynność przenoszenie podfragmentów z dwóch fragmentów w jednym fragmencie i aktualizowanie mapowania fragmentów w związku z tym.

**Przenieś Podfragmentu**: czynność Przenoszenie jednego podfragment w innym fragmencie. 

**Fragment**: czynność partycjonowanie poziome identycznie struktury danych w wielu bazach danych na podstawie klucza fragmentowania.

**Podziel**: czynność przenoszenie kilku podfragmentów z jednego fragmentu do innego fragmentu (zazwyczaj nowe). Klucz fragmentowania jest dostarczone przez użytkownika jako punkt podziału.

**Skalowanie w pionie**: act skalowania w górę (lub w dół) rozmiar obliczeń poszczególnych fragmentów. Na przykład zmiana fragmentu warstwy standardowej na Premium (co powoduje większej ilości zasobów obliczeniowych). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

