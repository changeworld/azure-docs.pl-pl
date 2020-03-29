---
title: Stosowanie przekształcenia SQL
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Zastosuj transformację SQL w usłudze Azure Machine Learning do uruchomienia kwerendy SQLite na wejściowych zestawach danych w celu przekształcenia danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2e44a4861e2522b766aab9c7151d76c471dd2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314542"
---
# <a name="apply-sql-transformation"></a>Stosowanie przekształcenia SQL

W tym artykule opisano moduł projektanta usługi Azure Machine Learning (wersja zapoznawcza).

Za pomocą modułu Zastosuj transformację SQL można:
  
-   Tworzenie tabel dla wyników i zapisywanie zestawów danych w przenośnej bazie danych.  
  
-   Wykonywanie niestandardowych przekształceń na typach danych lub tworzenie agregatów.  
  
-   Wykonywanie instrukcji zapytań SQL w celu filtrowania lub zmieniania danych i zwracania wyników kwerendy jako tabeli danych.  

> [!IMPORTANT]
> Aparat SQL używany w tym module jest **SQLite**. Aby uzyskać więcej informacji na temat składni SQLite, zobacz [SQL jako rozumiane przez SQLite, aby](https://www.sqlite.org/index.html) uzyskać więcej informacji.  

## <a name="how-to-configure-apply-sql-transformation"></a>Jak skonfigurować stosowanie transformacji SQL  

Moduł może zająć do trzech zestawów danych jako dane wejściowe. W przypadku odwoływania się do zestawów danych podłączonych `t1` `t2`do `t3`każdego portu wejściowego należy użyć nazw , i . Numer tabeli wskazuje indeks portu wejściowego.  
  
Pozostały parametr jest kwerendą SQL, która używa składni SQLite. Podczas wpisywania wielu wierszy w polu tekstowym **skryptu SQL** użyj średnika, aby zakończyć każdą instrukcję. W przeciwnym razie podziały wierszy są konwertowane na spacje.  

Ten moduł obsługuje wszystkie standardowe instrukcje składni SQLite. Aby uzyskać listę nieobsługiwałych instrukcji, zobacz sekcję [Uwagi techniczne.](#technical-notes)

##  <a name="technical-notes"></a>Uwagi techniczne  

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

-   Wejście jest zawsze wymagane na porcie 1.  
  
-   W przypadku identyfikatorów kolumn zawierających spację lub inne znaki specjalne należy zawsze ująć identyfikator kolumny w nawiasach kwadratowych lub podwójnych cudzysłowach, gdy odwołują się do kolumny w `SELECT` lub `WHERE` klauzulach.  
  
### <a name="unsupported-statements"></a>Nieobsługiwały instrukcje  

Chociaż SQLite obsługuje wiele standardu ANSI SQL, nie zawiera wielu funkcji obsługiwanych przez komercyjne systemy relacyjnej bazy danych. Aby uzyskać więcej informacji, zobacz [SQL jako rozumiane przez SQLite](http://www.sqlite.org/lang.html). Należy również pamiętać o następujących ograniczeniach podczas tworzenia instrukcji SQL:  
  
- SQLite używa dynamicznego wpisywania wartości, zamiast przypisywania typu do kolumny, jak w większości relacyjnych systemów baz danych. Jest słabo wpisany i umożliwia konwersję typu niejawnego.  
  
- `LEFT OUTER JOIN`jest realizowany, ale `RIGHT OUTER JOIN` `FULL OUTER JOIN`nie lub .  

- Można używać `RENAME TABLE` `ADD COLUMN` i instrukcji `ALTER TABLE` z polecenia, ale inne klauzule `DROP COLUMN` `ALTER COLUMN`nie `ADD CONSTRAINT`są obsługiwane, w tym , i .  
  
- Można utworzyć widok w SQLite, ale następnie widoki są tylko do odczytu. Nie można `DELETE`wykonać `INSERT`, `UPDATE` lub instrukcji w widoku. Można jednak utworzyć wyzwalacz, który uruchamia `DELETE` `INSERT`się `UPDATE` przy próbie , lub w widoku i wykonać inne operacje w treści wyzwalacza.  
  

Oprócz listy nieobsługiwałych funkcji podanych na oficjalnej stronie SQLite, następująca wiki zawiera listę innych nieobsługiwałych funkcji: [SQLite - Nieobsługiwuchom](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
