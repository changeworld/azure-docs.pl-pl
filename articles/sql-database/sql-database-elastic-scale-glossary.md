---
title: Słownik narzędzi Elastic Database | Microsoft Docs
description: Wyjaśnienie terminów używanych w narzędziach Elastic Database
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
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568450"
---
# <a name="elastic-database-tools-glossary"></a>Słownik narzędzi Elastic Database

Poniższe terminy są zdefiniowane dla [narzędzi Elastic Database](sql-database-elastic-scale-introduction.md), funkcji Azure SQL Database. Narzędzia służą do zarządzania [mapami fragmentu](sql-database-elastic-scale-shard-map-management.md)oraz obejmują [bibliotekę kliencką](sql-database-elastic-database-client-library.md), [Narzędzie do dzielenia](sql-database-elastic-scale-overview-split-and-merge.md)i kwerendy, [elastyczne pule](sql-database-elastic-pool.md)i [zapytania](sql-database-elastic-query-overview.md). 

Te warunki są używane w przypadku [dodawania fragmentu przy użyciu narzędzi Elastic Database](sql-database-elastic-scale-add-a-shard.md) i [używania klasy recoverymanager do rozwiązywania problemów z mapą fragmentu](sql-database-elastic-database-recovery-manager.md).

![Terminy dotyczące skalowania elastycznego][1]

**Baza danych**: Baza danych Azure SQL Database. 

**Routing zależny od danych**: Funkcja, która umożliwia aplikacji łączenie się z fragmentu za pomocą określonego klucza fragmentowania. Zobacz [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md). Porównaj z **[kwerendą fragmentu](sql-database-elastic-scale-multishard-querying.md)** .

**Globalna mapa fragmentu**: Mapa między kluczami fragmentowania i odpowiednimi fragmentów w ramach **zestawu fragmentu**. Globalna mapa fragmentu jest przechowywana w **Menedżerze mapy fragmentu**. Porównaj z **lokalną mapą fragmentu**.

**Lista map fragmentu**: Mapa fragmentu, w której klucze fragmentowania są mapowane pojedynczo. Porównaj z **zakresem mapy fragmentu**.   

**Lokalna Mapa fragmentu**: Na fragmentu lokalna Mapa fragmentu zawiera mapowania dla podfragmentów, które znajdują się na fragmentu.

**Kwerenda fragmentu**: Możliwość wydawania zapytania względem wielu fragmentów; zestawy wyników są zwracane przy użyciu semantyki ALL (zwanej także "zapytaniem"). Porównaj z **routingiem zależnym od danych**.

**Wiele dzierżawców** i **jedna dzierżawa**: Spowoduje to wyświetlenie bazy danych z jedną dzierżawą i wielodostępną bazą danych:

![Pojedyncze i wielodostępne bazy danych](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Poniżej przedstawiono reprezentację baz danych o pojedynczej **podzielonej na fragmenty** i wielu dzierżawców. 

![Pojedyncze i wielodostępne bazy danych](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapowanie fragmentu zakresu**: Mapa fragmentu, w której strategia dystrybucji fragmentu jest oparta na wielu zakresach ciągłych wartości. 

**Tabele odwołań**: Tabele, które nie są podzielonej na fragmenty, ale są replikowane w fragmentów. Na przykład kody ZIP mogą być przechowywane w tabeli referencyjnej. 

**Fragmentu**: Baza danych Azure SQL, która przechowuje dane z zestawu danych podzielonej na fragmenty. 

**Elastyczność fragmentu**: Możliwość przeprowadzenia zarówno **skalowania poziomego** , jak i skalowania w **pionie**.

**Podzielonej na fragmenty tabele**: Tabele, które są podzielonej na fragmenty, czyli, których dane są dystrybuowane w fragmentów na podstawie ich wartości klucza fragmentowania. 

**Klucz fragmentowania**: Wartość kolumny, która określa, jak dane są dystrybuowane w fragmentów. Typ wartości może być jednym z następujących: **int**, **bigint**, **varbinary**lub **unikatowy**. 

**Fragmentu**: Kolekcja fragmentów, która jest przypisana do tej samej mapy fragmentu w Menedżerze map fragmentu.  

**Podfragmentu**: Wszystkie dane skojarzone z pojedynczą wartością klucza fragmentowania na fragmentu. Podfragmentu to najmniejsza jednostka przenoszenia danych możliwa podczas redystrybucji tabel podzielonej na fragmenty. 

**Mapa fragmentu**: Zestaw mapowań między kluczami fragmentowania i odpowiednimi fragmentów.

**Menedżer mapy fragmentu**: Obiekt zarządzania i magazyn danych zawierający mapy fragmentu, lokalizacje fragmentu i mapowania dla co najmniej jednego zestawu fragmentu.

![Mapowania][2]

## <a name="verbs"></a>Słowa
**Skalowanie w poziomie**: Czynność skalowania w górę (lub w) kolekcji fragmentów przez dodanie lub usunięcie fragmentów do mapy fragmentu, jak pokazano poniżej.

![Skalowanie w poziomie i w pionie][3]

**Scal**: Czynność przeniesienia podfragmentów z dwóch fragmentów do jednego fragmentu i odpowiednio aktualizuje mapę fragmentu.

**Podfragmentu przenoszenia**: Czynność przeniesienia pojedynczego podfragmentu do innego fragmentu. 

**Fragmentu**: W przypadku partycjonowania danych z różnych baz danych na podstawie klucza fragmentowania.

**Podział**: Czynność przeniesienia kilku podfragmentów z jednego fragmentu do innego (zazwyczaj nowość) fragmentu. Klucz fragmentowania jest dostarczany przez użytkownika jako punkt podziału.

**Skalowanie**w pionie: Czynność skalowania w górę (lub w dół) wielkości obliczeniowej pojedynczego fragmentuu. Na przykład zmiana fragmentu z standardowa na Premium (co powoduje zwiększenie zasobów obliczeniowych). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

