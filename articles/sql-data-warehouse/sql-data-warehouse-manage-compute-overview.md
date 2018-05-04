---
title: Zarządzanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Więcej informacji na temat skalowania wydajności możliwości w usłudze Azure SQL Data Warehouse. Skalowanie w poziomie przez dostosowanie wartości dwu lub niższe koszty wstrzymując hurtowni danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ca6d34d3b670bfd05a9b65fe9e6b260120e3a5b8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Zarządzanie obliczeniowych w magazynie danych SQL Azure
Informacje o zarządzaniu zasobów obliczeniowych w magazynie danych SQL Azure. Niższe koszty wstrzymując hurtowni danych albo skalować w hurtowni danych w celu spełnienia wymagań dotyczących wydajności. 

## <a name="what-is-compute-management"></a>Co to jest obliczeń management?
Architektura usługi SQL Data Warehouse oddziela magazynu i zasobów obliczeniowych, umożliwiając niezależne skalowanie. W związku z tym można skalować obliczeń do wymagań dotyczących wydajności niezależne od magazynu danych. Można też wstrzymywać i wznawiać zasoby obliczeniowe. Fizyczne konsekwencją tej architektury jest to, że [rozliczeń](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) dla zasobów obliczeniowych i magazynu jest oddzielona. Jeśli nie musisz użyć magazynu danych przez pewien czas, można zmniejszyć koszty obliczeń wstrzymując obliczeń. 

## <a name="scaling-compute"></a>Skalowanie obliczeń
Można skalować w poziomie lub skalować obliczeń wstecz przez dostosowanie wartości właściwości [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) ustawienie dla magazynu danych. Ładowanie i zapytań wydajności może zwiększyć liniowo jak dodać więcej jednostek magazynu danych. 

Skalowalny w poziomie kroki opisane w artykule [portalu Azure](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), lub [T-SQL](quickstart-scale-compute-tsql.md) Przewodniki Szybki Start. Można również wykonywać operacje skalowania w poziomie przy użyciu [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Do wykonania operacji skalowania, SQL Data Warehouse najpierw Kasuje wszystkie zapytania przychodzące i wycofa następnie transakcji w celu zapewnienia spójnego stanu. Skalowanie występuje tylko po ukończeniu wycofywania transakcji. Dla operacji skalowania system odłącza warstwy magazynu z węzłów obliczeniowych, dodaje węzłów obliczeniowych i następnie reattaches warstwy magazynu do warstwy obliczeniowej. Każdy magazyn danych jest przechowywana jako 60 dystrybucji, które jest rozmieszczana równomiernie do węzłów obliczeniowych. Dodawanie więcej węzłów obliczeniowych dodaje więcej mocy obliczeniowej. Miarę wzrostu liczby węzłów obliczeniowych, zmniejsza liczbę podziału w każdym węźle obliczeń, zapewniając więcej mocy obliczeniowej dla zapytań. Podobnie zmniejszając jednostki magazynu danych zmniejsza liczbę węzłów obliczeniowych, co zmniejsza zasoby obliczeniowe dla zapytania.

W poniższej tabeli przedstawiono, jak zmienić liczbę dystrybucje na węzeł obliczeń zmiany jednostki magazynu danych.  DWU6000 zapewnia 60 węzłów obliczeniowych i osiąga znacznie większą wydajność zapytań, niż DWU100. 

| Jednostki magazynu danych  | \# węzły obliczeniowe | \# dystrybucji na węzeł |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Znajdowanie właściwego rozmiaru jednostki magazynu danych

Aby wyświetlić skalowania, zwłaszcza w przypadku większych jednostki magazynu danych, zwiększenia wydajności chcesz użyć co najmniej 1 TB zestawu danych. Aby znaleźć najlepszego numeru jednostki magazynu danych magazynu danych, spróbuj skalowanie w górę i w dół. Po załadowaniu danych, uruchom kilka zapytań z różnymi liczbami jednostki magazynu danych. Ponieważ skalowanie odbywa się szybko, możesz wypróbować różne poziomy wydajności w ciągu godziny lub mniej. 

Zalecenia dotyczące znajdowania najlepszego numeru danych magazynu jednostki:

- Dla magazynu danych na programowanie rozpocząć przez wybranie mniejszej liczby jednostki magazynu danych.  Dobry punkt wyjścia jest DW400 lub DW200.
- Monitorowanie wydajności aplikacji, obserwowania liczbę jednostek magazynu danych, wybranych w porównaniu do wydajności, które należy obserwować.
- Załóżmy skali liniowej i określić, ile trzeba zwiększyć lub zmniejszyć jednostki magazynu danych. 
- Kontynuuj, wprowadzić zmiany, aż do uzyskania optymalnej wydajności poziom dla potrzeb biznesowych.

## <a name="when-to-scale-out"></a>Kiedy skalować w poziomie
Skalowanie w poziomie jednostki magazynu danych ma wpływ tych aspektów wydajności:

- Liniowo poprawia wydajność systemu pod kątem skanowania, agregacji i CTAS instrukcje.
- Zwiększa liczbę czytelników i zapisywania ładowania danych.
- Maksymalna liczba równoczesnych zapytań i gniazda współbieżności.

Zalecenia dotyczące Kiedy skalować w poziomie danych magazynu jednostki:

- Przed wykonaniem operacji ładowania i przekształcania dużej ilości danych skalować w poziomie, aby udostępnić te dane szybciej.
- W godzinach szczytu skalowanie w poziomie do uwzględnienia większej liczby równoczesnych zapytań. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co zrobić, jeśli skalowania nie poprawia wydajności?

Dodawanie zwiększenie równoległości jednostki magazynu danych. Jeśli praca jest podzielona równomiernie między węzły obliczeń, dodatkowe równoległości poprawia wydajność zapytań. Jeśli skalowanie w poziomie nie jest zmiana wydajności, dostępne są niektóre przyczyny, dlaczego taka sytuacja może wystąpić. Między dystrybucje może być niesymetryczna danych lub kwerend może wprowadzenie dużą ilość ruchu danych. Aby sprawdzić problemy z wydajnością zapytania, zobacz [Rozwiązywanie problemów z wydajnością](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Wstrzymywanie i wznawianie obliczeniowe
Wstrzymywanie obliczeń powoduje, że warstwy magazynowania można odłączyć od węzłów obliczeniowych. Zasoby obliczeniowe są wydawane z Twojego konta. Możesz nie są naliczane obliczania podczas obliczeń jest wstrzymana. Wznawianie obliczeń reattaches magazynu węzłami obliczeniowymi i wznawia opłat obliczania. Po zatrzymaniu magazynu danych:

* Zasoby obliczeniowe i pamięci są zwracane do puli zasobów dostępnych w centrum danych
* Koszt jednostkowy magazynu danych są zera na czas trwania pauzy.
* Nie wpływa na magazyn danych i danych pozostaje niezmieniona. 
* Usługa SQL Data Warehouse anuluje wszystkie uruchomione lub umieszczonych w kolejce operacji.

Po wznowieniu pracy hurtowni danych:

* Usługa SQL Data Warehouse uzyskuje zasobów obliczeniowych i pamięci dla ustawienie jednostek magazynu danych.
* Obliczeniowe opłat za Twoje Wznów jednostki magazynu danych.
* Dane staną się dostępne.
* Po magazynu danych jest w trybie online, możesz konieczne ponowne uruchomienie zapytania obciążenia.

Jeśli chcesz zawsze magazyn danych dostępny, należy wziąć pod uwagę skalowanie w dół najmniejszy rozmiar, a nie wstrzymanie. 

Dla wstrzymać i wznowić czynności, zobacz [portalu Azure](pause-and-resume-compute-portal.md), lub [PowerShell](pause-and-resume-compute-powershell.md) Przewodniki Szybki Start. Można również użyć [wstrzymać interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) lub [wznowić interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Opróżnianie transakcji przed wstrzymywaniem i skalowaniem
Firma Microsoft zaleca stosowanie istniejących transakcji zakończyć przed rozpoczęciem operacji pauzę lub skali.

Gdy ma zostać przeprowadzona procedura wstrzymania lub skalowania usługi SQL Data Warehouse, po zainicjowaniu odpowiedniego żądania następuje anulowanie zapytań w tle.  Anulowanie prostego zapytania typu SELECT to szybka operacja, która nie ma prawie żadnego wpływu na czas wstrzymywania lub skalowania wystąpienia.  Może jednak nie być możliwe szybkie zatrzymanie zapytań transakcyjnych, które modyfikują dane lub ich strukturę.  **Zapytania transakcyjne należy z założenia wykonać w całości lub wycofać ich zmiany.**  Całkowite cofnięcie wyników działania zapytania transakcyjnego może trwać równie długo lub nawet dłużej niż pierwotna zmiana wprowadzona przez zapytanie.  Na przykład w przypadku anulowania zapytania, którego zadaniem było usunięcie wierszy i które było uruchomione przez godzinę, może upłynąć kolejna godzina, zanim system z powrotem wstawi wiersze, które zostały usunięte.  W przypadku uruchomienia procedury wstrzymywania lub skalowania w toku transakcji operacja wstrzymywania lub skalowania może zająć dużo czasu, ponieważ zanim będzie możliwe jej wykonanie, zmiany muszą zostać w pełni cofnięte.

Zobacz też [opis transakcji](sql-data-warehouse-develop-transactions.md), i [optymalizacji transakcji](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatyzacja zarządzania obliczeń
Aby zautomatyzować operacje zarządzania obliczeniowe, zobacz [Zarządzaj obliczeniowe o usługę Azure functions](manage-compute-with-azure-functions.md).

Każdego skalowalnego w poziomie, Wstrzymaj i operacje wznawiania może potrwać kilka minut. Czy możesz skalowania, wstrzymywanie, wznawianie automatycznie, zaleca się wdrożenie logiki, aby upewnić się, że niektóre operacje została ukończona przed wykonaniem innej akcji. Sprawdzanie stanu magazynu danych przy użyciu różnych punktów końcowych umożliwia prawidłowo zaimplementować automatyzacji takich operacji. 

Aby sprawdzić stan magazynu danych, zobacz [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) lub [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Szybki Start. Możesz również sprawdzić stan magazynu danych z [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Uprawnienia

Skalowanie w magazynie danych wymaga uprawnienia opisane w [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Wstrzymywanie i wznawianie wymagają [Współautor bazy danych SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) uprawnienia, w szczególności Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Kolejne kroki
Innym aspektem zarządzania zasoby obliczeniowe jest przydziału zasobów obliczeniowych różnych dla poszczególnych zapytań. Aby uzyskać więcej informacji, zobacz [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md).
