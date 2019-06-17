---
title: Zarządzanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Więcej informacji o wydajności funkcje skalowania w poziomie w usłudze Azure SQL Data Warehouse. Skalowanie w poziomie przez dopasowanie liczby jednostek dwu i niższe koszty przez wstrzymanie magazynu danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 47be738a4e5dcec144d482c28e39cbe950bba3e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748938"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Zarządzanie obliczeniami w usłudze Azure SQL Data Warehouse
Dowiedz się więcej o zarządzaniu zasobów obliczeniowych w usłudze Azure SQL Data Warehouse. Niższe koszty przez wstrzymanie magazynu danych lub skalować magazyn danych do spełnienia wymagań wydajności. 

## <a name="what-is-compute-management"></a>Co to jest zarządzanie obliczeniowych?
Architektura usługi SQL Data Warehouse oddziela magazynu i mocy obliczeniowej, umożliwiając niezależne skalowanie. W rezultacie można skalować zasoby obliczeniowe, zapotrzebowanie na wydajność niezależnie od magazynu danych. Można również wstrzymywanie i wznawianie zasobów obliczeniowych. Naturalna konsekwencja tej architektury jest to, że [rozliczeń](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) zasobów obliczeniowych i magazynu jest oddzielony. Jeśli nie potrzebujesz użyć magazynu danych od pewnego czasu, można zapisać koszty operacji obliczeniowych po wstrzymaniu obliczeń. 

## <a name="scaling-compute"></a>Skalowanie obliczeń
Możesz skalować w poziomie lub skalować zasoby obliczeniowe na Wstecz, dostosowując [jednostkami magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) ustawienie dla magazynu danych. Ładowanie i zwiększa wydajność zapytań można zwiększyć liniowo, w miarę dodawania większej liczby jednostek magazynu danych. 

Skalowalny w poziomie znajduje się [witryny Azure portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), lub [języka T-SQL](quickstart-scale-compute-tsql.md) przewodników Szybki Start. Można również wykonać operacji skalowania w poziomie przy użyciu [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Do wykonywania operacji skalowania, usługa SQL Data Warehouse najpierw rozłącza wszystkie przychodzące zapytania, a następnie powoduje wycofanie transakcji w celu zapewnienia spójnego stanu. Skalowanie występuje tylko po ukończeniu wycofywania transakcji. Operacji skalowania system odłącza warstwy magazynowania z węzłów obliczeniowych, dodaje węzły obliczeniowe, a następnie ponowne dołączenie następuje ponowne warstwy magazynu względem warstwy obliczeń. Każda Hurtownia danych są przechowywane jako 60 dystrybucji, które są dystrybuowane równomiernie w węzłach obliczeniowych. Dodawanie kolejnych węzłów obliczeniowych dodaje więcej mocy obliczeniowej. W miarę zwiększania się liczby węzłów obliczeniowych, zmniejsza liczbę dystrybucje na węźle obliczeniowym, podając więcej mocy obliczeniowej zapytań. Podobnie zmniejszając liczbę jednostek magazynu danych zmniejsza liczbę węzłów obliczeniowych, co zmniejsza zasoby obliczeniowe dla zapytania.

W poniższej tabeli przedstawiono, jak zmienić liczbę dystrybucje na zmiany węzła obliczeniowego jako jednostki magazynu danych.  DWU6000 zapewnia 60 węzłów obliczeniowych i zapewnia znacznie wyższą wydajność zapytań, niż DWU100. 

| Jednostki magazynu danych  | \# węzłów obliczeniowych | \# dystrybucji na węzeł |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Znajdowanie wielkości jednostek magazynu danych

Aby wyświetlić wydajność zalety skalowania w poziomie, szczególnie w przypadku większych jednostek magazynu danych ma być co najmniej 1 TB pojemności zestawu danych. Aby znaleźć najlepszą liczbę jednostek magazynu danych dla magazynu danych, spróbuj wykonać skalowanie w górę i w dół. Uruchom kilka zapytań z różną liczbę jednostek magazynu danych po załadowaniu danych. Ponieważ skalowanie odbywa się szybko, możesz wypróbować różne poziomy wydajności w godzinę lub mniej. 

Zalecenia dotyczące znajdowania najlepsze liczba danych jednostek magazynu:

- Dla magazynu danych w trakcie opracowywania Rozpocznij od wybrania mniejszą liczbę jednostek magazynu danych.  Dobry punkt wyjścia jest DW400 lub wartości DW200.
- Monitorowanie wydajności aplikacji, liczbę jednostek magazynu danych wybrane w porównaniu do wydajności, których można zaobserwować obserwacji.
- Załóżmy skali liniowej i ustalić, ile potrzebujesz zwiększyć lub zmniejszyć liczbę jednostek magazynu danych. 
- Kontynuuj, wprowadzić zmiany, aż do poziomu optymalną wydajność dla konkretnych wymagań biznesowych.

## <a name="when-to-scale-out"></a>Podczas skalowania w poziomie
Skalowanie jednostek magazynu danych ma wpływ na następujące aspekty wydajności:

- Liniowo zwiększa wydajność systemu pod kątem skanowania, agregacje i instrukcji CTAS.
- Zwiększa liczbę czytników i składników zapisywania do ładowania danych.
- Maksymalna liczba równoczesnych zapytań oraz liczby gniazd współbieżności.

Zalecenia dotyczące skalowania w poziomie danych jednostek magazynu:

- Przed wykonaniem operacji ładowania i przekształcania dużej ilości danych, skalowanie w poziomie do szybsze udostępnienie danych.
- W godzinach szczytu skalowanie w poziomie do uwzględnienia większej liczby równoczesnych zapytań. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co zrobić, jeśli skalowanie w poziomie nie poprawia wydajność?

Dodawanie jednostki magazynu danych zwiększa równoległości. Jeśli praca jest równomiernie podzielić między węzłami obliczeniowymi, dodatkowe równoległości zwiększa wydajność zapytań. Skalowanie w poziomie nie ulega zmianie wydajność, czy niektóre przyczyny, dlaczego tak się zdarzyć. Dane mogą być niesymetryczny między dystrybucjami lub zapytań może być wprowadzenie dużą ilość ruchu danych. Aby zbadać problemy z wydajnością zapytań, zobacz [Rozwiązywanie problemów z wydajnością](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Wstrzymywanie i wznawianie procesów obliczeniowych
Wstrzymywanie obliczeń powoduje, że warstwy magazynu można odłączyć od węzłów obliczeniowych. Zasoby obliczeniowe są wydawane z Twojego konta. Nie są naliczane za obliczenia podczas wstrzymaniu obliczeń. Wznawianie procesów obliczeniowych ponowne dołączenie następuje ponowne magazynu do węzłów obliczeniowych, a następnie wznawia opłaty za obliczenia. Gdy zatrzymasz magazynu danych:

* Zasobów obliczeniowych i pamięci są zwracane do puli dostępnych zasobów w centrum danych
* Koszt jednostki magazynu danych mają wartość zero, czas trwania wstrzymania.
* Nie wpływa na magazyn danych i dane pozostają niezmienione. 
* Usługa SQL Data Warehouse anuluje wszystkie uruchomione lub umieszczone w kolejce operacji.

Po wznowieniu pracy hurtowni danych:

* Usługa SQL Data Warehouse uzyskuje zasobów obliczeniowych i pamięci dla jednostek magazynu danych, Twoje ustawienia.
* Obliczenia opłaty za swój życiorys jednostek magazynu danych.
* Twoje dane będą dostępne.
* Po w magazynie danych jest w trybie online, należy ponownie uruchomić zapytania obciążenia.

Jeśli chcesz zawsze magazyn danych dostępny, należy rozważyć skalowanie w dół do najmniejszego możliwego rozmiaru zamiast wstrzymywania. 

Aby uzyskać wstrzymać i wznowić kroków, zobacz [witryny Azure portal](pause-and-resume-compute-portal.md), lub [programu PowerShell](pause-and-resume-compute-powershell.md) przewodników Szybki Start. Można również użyć [wstrzymać interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) lub [wznowić interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Opróżnianie transakcji przed wstrzymywaniem i skalowaniem
Firma Microsoft zaleca, dzięki czemu istniejących transakcji zakończyć przed rozpoczęciem operacji wstrzymywania lub skalowania.

Gdy ma zostać przeprowadzona procedura wstrzymania lub skalowania usługi SQL Data Warehouse, po zainicjowaniu odpowiedniego żądania następuje anulowanie zapytań w tle.  Anulowanie prostego zapytania typu SELECT to szybka operacja, która nie ma prawie żadnego wpływu na czas wstrzymywania lub skalowania wystąpienia.  Może jednak nie być możliwe szybkie zatrzymanie zapytań transakcyjnych, które modyfikują dane lub ich strukturę.  **Zapytania transakcyjne należy z założenia wykonać w całości lub wycofać ich zmiany.**  Całkowite cofnięcie wyników działania zapytania transakcyjnego może trwać równie długo lub nawet dłużej niż pierwotna zmiana wprowadzona przez zapytanie.  Na przykład w przypadku anulowania zapytania, którego zadaniem było usunięcie wierszy i które było uruchomione przez godzinę, może upłynąć kolejna godzina, zanim system z powrotem wstawi wiersze, które zostały usunięte.  W przypadku uruchomienia procedury wstrzymywania lub skalowania w toku transakcji operacja wstrzymywania lub skalowania może zająć dużo czasu, ponieważ zanim będzie możliwe jej wykonanie, zmiany muszą zostać w pełni cofnięte.

Zobacz też [opis transakcji](sql-data-warehouse-develop-transactions.md), i [Optymalizowanie transakcji](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatyzowanie zarządzania obliczeń
Aby zautomatyzować operacje zarządzania obliczeniowych, zobacz [Zarządzaj obliczeniowe przy użyciu usługi Azure functions](manage-compute-with-azure-functions.md).

Wszystkich skalowalnego w poziomie, wstrzymywanie i wznawianie operacji może potrwać kilka minut. Czy możesz skalowania, wstrzymywanie, wznawianie automatycznie, zalecamy zaimplementowanie logiki w celu zapewnienia, że niektóre operacje zostały wykonane przed dalszym wykonywaniem innej akcji. Sprawdzanie stanu magazynu danych za pośrednictwem różnych punktów końcowych pozwala na poprawnego zaimplementowania automatyzacji takich operacji. 

Aby sprawdzić stan magazynu danych, zobacz [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) lub [języka T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Szybki Start. Możesz również sprawdzić stan magazynu danych przy użyciu [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Uprawnienia

Skalowanie magazynu danych wymaga uprawnienia opisane w [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Wstrzymywanie i wznawianie wymagają [Współautor bazy danych SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) uprawnienia, w szczególności Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Kolejne kroki
Inne aspekty zarządzania zasobów obliczeniowych jest przydzielanie różnych zasobów obliczeniowych dla poszczególnych zapytań. Aby uzyskać więcej informacji, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).
