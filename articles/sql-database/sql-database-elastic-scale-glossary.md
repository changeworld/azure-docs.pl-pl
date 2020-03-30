---
title: Słowniczek narzędzi elastycznej bazy danych
description: Wyjaśnienie terminów używanych dla narzędzi elastycznej bazy danych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: ab972db78cd213497fb96486b3e16b01f2c4c6eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823624"
---
# <a name="elastic-database-tools-glossary"></a>Słowniczek narzędzi elastycznej bazy danych

Następujące terminy są zdefiniowane dla [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md), funkcji usługi Azure SQL Database. Narzędzia służą do zarządzania [mapami niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md)i obejmują [bibliotekę klienta,](sql-database-elastic-database-client-library.md) [narzędzie scalania dzielenia,](sql-database-elastic-scale-overview-split-and-merge.md) [elastyczne pule](sql-database-elastic-pool.md)i [kwerendy.](sql-database-elastic-query-overview.md) 

Terminy te są używane w [Dodawanie niezależnego fragmentu przy użyciu narzędzi elastycznej bazy danych](sql-database-elastic-scale-add-a-shard.md) i za pomocą [recoveryManager klasy, aby rozwiązać problemy mapy niezależnego fragmentu](sql-database-elastic-database-recovery-manager.md).

![Terminy skali elastycznej][1]

**Baza danych:** baza danych SQL platformy Azure. 

**Routing zależny od danych:** Funkcja, która umożliwia aplikacji łączenie się z niezależnego fragmentu, biorąc pod uwagę określony klucz dzielenia na fragmenty. Zobacz [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md). Porównaj z **[kwerendą wielosprzężową](sql-database-elastic-scale-multishard-querying.md)**.

**Mapa odłamków globalnych:** Mapa między kluczami dzielenia na fragmenty a ich odpowiednimi fragmentami w **zestawie niezależnego fragmentu.** Mapa odłamków globalnych jest przechowywana w **menedżerze map niezależnego fragmentu**. Porównaj z **mapą odłamków lokalnych**.

**Mapa niezależnego fragmentu listy:** mapa niezależnego fragmentu, na której klawisze dzielenia na fragmenty są mapowane indywidualnie. Porównaj z **mapą odłamków zasięgu**.   

**Mapa odłamków lokalnych:** Przechowywane na niezależnego fragmentu, mapa lokalnego niezależnego fragmentu zawiera mapowania shardlets, które znajdują się na niezależnego fragmentu.

**Kwerenda wielodyskładnikowa:** Możliwość wystawiania kwerendy względem wielu fragmentów; zestawy wyników są zwracane przy użyciu semantyki UNION ALL (znanej również jako "zapytanie fan-out"). Porównaj **z routingu zależnego od danych**.

**Wielodostępne** i **jednodostępne:** Pokazuje bazę danych z jedną dzierżawą i wielodostępną bazę danych:

![Bazy danych dla pojedynczych i wielodostępnych](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Oto **reprezentacja podzielonej** bazy danych pojedynczych i wielodostępnych. 

![Bazy danych dla pojedynczych i wielodostępnych](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa niezależnego fragmentu zakresu:** mapa niezależnego fragmentu, na której strategia dystrybucji fragmentów opiera się na wielu zakresach ciągłych wartości. 

**Tabele odwołań:** Tabele, które nie są podzielone na fragmenty, ale są replikowane przez fragmenty. Na przykład kody pocztowe mogą być przechowywane w tabeli odwołań. 

**Niezależnego fragmentu:** baza danych SQL platformy Azure, która przechowuje dane z podzielonego zestawu danych. 

**Elastyczność niezależnego fragmentu:** Możliwość wykonywania zarówno **skalowania poziomego,** jak i **pionowego.**

**Tabele podzielone na**fragmenty: tabele, które są podzielone na fragmenty, czyli których dane są dystrybuowane między fragmentami na podstawie ich wartości klucza dzielenia na fragmenty. 

**Klucz dzielenia na fragmenty:** Wartość kolumny, która określa sposób dystrybucji danych między fragmentami. Typ wartości może być jednym z następujących: **int**, **bigint**, **varbinary**lub **uniqueidentifier**. 

**Zestaw niezależnego fragmentu:** Kolekcja fragmentów, które są przypisane do tej samej mapy niezależnego fragmentu w menedżerze mapy niezależnego fragmentu.  

**Shardlet:** Wszystkie dane skojarzone z pojedynczą wartością klucza dzielenia na fragmenty na niezależnego fragmentu. Shardlet jest najmniejszą jednostką przenoszenia danych możliwe podczas redystrybucji tabel podzielonych. 

**Mapa niezależnego fragmentu:** Zestaw mapowań między kluczami dzielenia na fragmenty i ich odpowiednich fragmentów.

**Menedżer mapy niezależnego fragmentu:** obiekt zarządzania i magazyn danych, który zawiera mapę fragmentów(s), lokalizacje fragmentów i mapowania dla jednego lub więcej zestawów niezależnego fragmentu.

![Mapowania][2]

## <a name="verbs"></a>zlecenia
**Skalowanie w poziomie:** Czynność skalowania w poziomie (lub w) kolekcji fragmentów przez dodanie lub usunięcie fragmentów do mapy niezależnego fragmentu, jak pokazano poniżej.

![Skalowanie poziome i pionowe][3]

**Scalanie:** Akt przenoszenia shardlets z dwóch fragmentów do jednego fragmentu i odpowiednio aktualizowanie mapy niezależnego fragmentu.

**Shardlet przenieść:** akt przenoszenia pojedynczego shardlet do innego fragmentu. 

**Shard:** Akt poziomo partycjonowanie identycznie ustrukturyzowanych danych w wielu bazach danych na podstawie klucza dzielenia na fragmenty.

**Split:** Akt przenoszenia kilku shardlets z jednego niezależnego fragmentu do drugiego (zazwyczaj nowy) fragment. Klucz dzielenia na fragmenty jest dostarczany przez użytkownika jako punkt podziału.

**Skalowanie w pionie:** Działanie skalowania w górę (lub w dół) rozmiar obliczeń pojedynczego fragmentu. Na przykład zmiana niezależnego fragmentu ze standardowego na premium (co powoduje więcej zasobów obliczeniowych). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

