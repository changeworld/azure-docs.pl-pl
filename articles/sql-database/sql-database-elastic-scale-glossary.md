---
title: Elastyczne bazy danych narzędzia słownik | Dokumentacja firmy Microsoft
description: Wyjaśnienie pojęcia dotyczące narzędzi elastycznej bazy danych
services: sql-database
documentationcenter: ''
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 010d4f6d9f33b8fb6f1622321a3b566f68f28f5f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="elastic-database-tools-glossary"></a>Elastyczne słownik Narzędzia bazy danych
Poniższe terminy są definiowane dla [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md), funkcja bazy danych SQL Azure. Narzędzia są używane do zarządzania [mapy niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md)i obejmują [biblioteki klienta](sql-database-elastic-database-client-library.md), [narzędzia do scalania podziału](sql-database-elastic-scale-overview-split-and-merge.md), [pule elastyczne](sql-database-elastic-pool.md)i [zapytania](sql-database-elastic-query-overview.md). 

Te terminy są używane w [Dodawanie niezależnych, za pomocą narzędzi elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md) i [za pomocą klasy RecoveryManager rozwiązywania problemów mapy niezależnego fragmentu](sql-database-elastic-database-recovery-manager.md).

![Elastyczne warunki skali][1]

**Baza danych**: baza danych SQL Azure. 

**Routing zależnych danych**: włącza aplikację do nawiązania połączenia niezależnych danego klucza dzielenia na fragmenty określonych funkcji. Zobacz [danych zależnych routingu](sql-database-elastic-scale-data-dependent-routing.md). Porównaj z  **[zapytania wielu niezależnych](sql-database-elastic-scale-multishard-querying.md)**.

**Globalny identyfikator niezależnego fragmentu mapy**: mapy między dzielenia na fragmenty kluczy i ich odpowiednich fragmentów w **zestaw niezależnych**. Mapy niezależnych globalne są przechowywane w **menedżera map niezależnego fragmentu**. Porównaj z **mapy lokalnego niezależnych**.

**Lista niezależnych mapy**: mapy niezależnego fragmentu, w których dzielenia na fragmenty klucze są mapowane pojedynczo. Porównaj z **niezależnego fragmentu Map zakres**.   

**Mapa lokalnych niezależnych**: przechowywane na niezależnego fragmentu, mapa lokalnych niezależnego fragmentu zawiera mapowania dla shardlets, który znajduje się na niezależnego fragmentu.

**Wiele niezależnych zapytania**: mógł wystawiać zapytanie wielu odłamków; zestawy wyników są zwracane, używając semantyki UNION ALL (znanej także jako "rozdysponowywania zapytania"). Porównaj z **danych zależnych routingu**.

**Wielodostępne** i **pojedynczej dzierżawy**: Pokazuje dzierżawy pojedynczej bazy danych i bazy danych wielu dzierżawców:

![Bazy danych jednym i wieloma dzierżawcami](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Oto reprezentację **podzielonej** jednym i wieloma dzierżawcami baz danych. 

![Bazy danych jednym i wieloma dzierżawcami](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Zakres niezależnych mapy**: mapy niezależnego fragmentu, w którym strategii dystrybucji niezależnego fragmentu opiera się na różnych zakresów wartości ciągłe. 

**Tabele odwołań dla**: tabel, które nie są podzielonej, ale są replikowane między fragmentów. Na przykład kodów pocztowych mogą być przechowywane w tabeli odniesienia. 

**Identyfikator niezależnego fragmentu**: bazy danych SQL Azure, która przechowuje dane z zestawu danych podzielonej. 

**Elastyczność niezależnego fragmentu**: możliwość wykonywania zarówno **skalowanie w poziomie** i **skalowanie w pionie**.

**Tabele podzielonej**: tabele, które są podzielonej, tj., których danych jest rozłożona odłamków na podstawie ich wartości klucza dzielenia na fragmenty. 

**Klucz dzielenia na fragmenty**: wartość kolumny, która określa, jak dane są przesyłane między fragmentów. Typ wartości może być jedną z następujących: **int**, **bigint**, **varbinary**, lub **uniqueidentifier**. 

**Zestaw niezależnych**: zbiór fragmentów, które są przypisane do tego samego mapy niezależnego fragmentu w Menedżerze mapy niezależnego fragmentu.  

**Shardlet**: wszystkie dane skojarzone z pojedynczą wartość klucz w niezależnych dzielenia na fragmenty. Shardlet jest najmniejsza możliwa przenoszenie danych do rozpowszechniania podzielonej tabel. 

**Mapa niezależnego fragmentu**: zestaw mapowań między dzielenia na fragmenty kluczy i ich odpowiednich fragmentów.

**Menedżer mapy niezależnego fragmentu**: Zarządzanie obiektu i magazynu danych zawierającą poprzedzających niezależnego fragmentu map, lokalizacje niezależnego fragmentu i mapowania dla jednego lub więcej zestawów niezależnego fragmentu.

![Mapowania][2]

## <a name="verbs"></a>Zlecenia
**Skalowanie w poziomie**: polega na skalowanie w poziomie (lub w) kolekcja odłamków przez dodanie lub usunięcie fragmentów do mapy niezależnego fragmentu, jak pokazano poniżej.

![Skalowanie w poziomie i w pionie][3]

**Scal**: polega na przenoszeniu shardlets z odłamków dwa na jeden identyfikator niezależnego fragmentu i odpowiednio aktualizowanie mapy niezależnego fragmentu.

**Przenieś Shardlet**: act przenoszenia pojedynczego shardlet różnych niezależnego fragmentu. 

**Identyfikator niezależnego fragmentu**: polega na poziomie partycjonowania identycznie strukturalnych danych między wieloma bazami danych oparte na kluczu dzielenia na fragmenty.

**Podziel**: polega na przenoszeniu shardlets kilka z jednego niezależnego fragmentu na inny identyfikator niezależnego fragmentu (zazwyczaj jest to nowy). Klucz dzielenia na fragmenty są udostępniane przez użytkownika jako punkt podziału.

**Skalowanie w pionie**: polega na skalowanie w górę (lub w dół) poziom wydajności poszczególnych niezależnego fragmentu. Na przykład zmiana niezależnych zgodne ze standardem Premium (co powoduje więcej zasobów obliczeniowych). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

