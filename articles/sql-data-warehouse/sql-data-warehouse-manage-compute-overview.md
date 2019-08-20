---
title: Zarządzanie zasobem obliczeniowym w Azure SQL Data Warehouse | Microsoft Docs
description: Dowiedz się więcej o możliwościach skalowania wydajności w Azure SQL Data Warehouse. Skalowanie w poziomie przez dostosowanie jednostek dwu lub niższych kosztów przez wstrzymanie magazynu danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f0935ccc4c4274bfab0c589ef158d4ea0bef455c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575324"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Zarządzanie obliczeniami w Azure SQL Data Warehouse
Dowiedz się więcej na temat zarządzania zasobami obliczeniowymi w Azure SQL Data Warehouse. Niższe koszty przez wstrzymanie magazynu danych lub skalowanie magazynu danych w celu spełnienia wymagań dotyczących wydajności. 

## <a name="what-is-compute-management"></a>Co to jest zarządzanie obliczeniami?
Architektura SQL Data Warehouse oddziela magazyn i obliczeniowe, umożliwiając każdemu skalowanie niezależnie. W związku z tym można skalować obliczenia w celu spełnienia wymagań dotyczących wydajności niezależnie od magazynu danych. Możesz również wstrzymywać i wznawiać zasoby obliczeniowe. Naturalna konsekwencja tej architektury polega na tym [](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) , że rozliczenia na potrzeby obliczeniowe i magazyny są oddzielone. Jeśli nie musisz używać hurtowni danych przez pewien czas, możesz zaoszczędzić koszty obliczeń, zatrzymując obliczenia. 

## <a name="scaling-compute"></a>Skalowanie obliczeniowe
Obliczenia można skalować w poziomie lub skali z powrotem przez dostosowanie ustawienia [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) dla magazynu danych. Ładowanie i wydajność zapytań można zwiększyć liniowo w miarę dodawania większej liczby jednostek magazynu danych. 

Aby zapoznać się z krokami skalowania, zobacz Przewodniki Szybki Start dotyczące [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)lub [T-SQL](quickstart-scale-compute-tsql.md) . Można również wykonywać operacje skalowania w poziomie za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Aby wykonać operację skalowania, SQL Data Warehouse najpierw Kasuj wszystkie zapytania przychodzące, a następnie wycofywanie transakcji w celu zapewnienia spójnego stanu. Skalowanie odbywa się tylko po zakończeniu wycofywania transakcji. W przypadku operacji skalowania system odłącza warstwę magazynu od węzłów obliczeniowych, dodaje węzły obliczeniowe, a następnie ponownie dołącza warstwę magazynu do warstwy obliczeniowej. Każdy magazyn danych jest przechowywany jako dystrybucje 60, które są równomiernie dystrybuowane do węzłów obliczeniowych. Dodanie większej liczby węzłów obliczeniowych zwiększa moc obliczeniową. Wraz ze wzrostem liczby węzłów obliczeniowych liczba rozkładów na węzeł obliczeniowy zmniejsza się, co zapewnia większą moc obliczeniową dla zapytań. Podobnie, zmniejszenie jednostek magazynu danych zmniejsza liczbę węzłów obliczeniowych, co zmniejsza zasoby obliczeniowe dla zapytań.

W poniższej tabeli przedstawiono, w jaki sposób liczba dystrybucji na węzeł obliczeniowy zmienia się w miarę zmiany jednostek magazynu danych.  DWU6000 udostępnia 60 węzłów obliczeniowych i osiąga znacznie wyższą wydajność zapytań niż DWU100. 

| Jednostki magazynu danych  | \#węzłów obliczeniowych | \#z dystrybucji na węzeł |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Znajdowanie odpowiedniego rozmiaru jednostek magazynu danych

Aby zobaczyć korzyści wynikające z wydajności skalowania w poziomie, szczególnie w przypadku większych jednostek magazynu danych, należy użyć co najmniej jednego zestawu danych o rozmiarze 1 TB. Aby znaleźć najlepszą liczbę jednostek magazynu danych dla magazynu danych, spróbuj skalować w górę i w dół. Po załadowaniu danych Uruchom kilka zapytań z różnymi liczbami jednostek magazynu danych. Ze względu na to, że skalowanie odbywa się szybko, możesz wypróbować różne poziomy wydajności w ciągu godziny lub mniej. 

Zalecenia dotyczące wyszukiwania najlepszej liczby jednostek magazynu danych:

- W przypadku magazynu danych w trakcie tworzenia Zacznij od wybrania mniejszej liczby jednostek magazynu danych.  Dobrym punktem początkowym jest DW400 lub wartości DW200.
- Monitoruj wydajność aplikacji, obserwując liczbę wybranych jednostek magazynu danych w porównaniu do podanej wydajności.
- Założono skalę liniową i określić, ile potrzeba do zwiększenia lub zmniejszenia jednostek magazynu danych. 
- Kontynuuj wprowadzanie zmian, dopóki nie osiągniesz optymalnego poziomu wydajności dla wymagań firmy.

## <a name="when-to-scale-out"></a>Kiedy skalować w poziomie
Skalowanie jednostek magazynu danych wpływa na następujące aspekty wydajności:

- Liniowie poprawia wydajność systemu na potrzeby skanów, agregacji i instrukcji CTAS.
- Zwiększa liczbę czytników i składników zapisywania do ładowania danych.
- Maksymalna liczba współbieżnych zapytań i miejsc współbieżności.

Zalecenia dotyczące skalowania jednostek magazynu danych w poziomie:

- Zanim zaczniesz wykonywać duże operacje ładowania lub przekształcania danych, Skaluj w poziomie, aby zapewnić szybsze dostęp do danych.
- W czasie szczytu godzin pracy można skalować w poziomie, aby pomieścić większą liczbę współbieżnych zapytań. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co w przypadku skalowania w dół nie poprawia wydajności?

Dodanie jednostek magazynu danych zwiększa równoległość. Jeśli prace są równo podzielone między węzłami obliczeniowymi, dodatkowa równoległość zwiększa wydajność zapytań. Jeśli skalowanie w dół nie zmienia wydajności, istnieje kilka powodów, dla których może się to zdarzyć. Dane mogą być pochylone w ramach dystrybucji lub mogą wprowadzać duże ilości danych. Aby zbadać problemy z wydajnością zapytań, zobacz [Rozwiązywanie problemów z wydajnością](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Wstrzymywanie i wznawianie obliczeń
Wstrzymywanie obliczeń powoduje odłączenie warstwy magazynu od węzłów obliczeniowych. Zasoby obliczeniowe są wydawane z Twojego konta. Nie jest naliczana opłata za obliczenia, gdy obliczenie jest wstrzymane. Wznawianie obliczeń ponownie dołącza magazyn do węzłów obliczeniowych i wznawia opłaty za obliczenia. W przypadku wstrzymania magazynu danych:

* Zasoby obliczeniowe i pamięci są zwracane do puli dostępnych zasobów w centrum danych.
* Koszt jednostkowy magazynu danych jest równy zero przez czas wstrzymania.
* Nie dotyczy to przechowywania danych, a Twoje dane pozostają nienaruszone. 
* SQL Data Warehouse anuluje wszystkie uruchomione lub kolejkowane operacje.

Po wznowieniu magazynu danych:

* SQL Data Warehouse uzyskuje zasoby obliczeniowe i pamięci dla ustawienia jednostek magazynu danych.
* Opłaty za usługę obliczeniową dla jednostek magazynu danych są wznawiane.
* Twoje dane staną się dostępne.
* Gdy magazyn danych jest w trybie online, należy ponownie uruchomić zapytania dotyczące obciążenia.

Jeśli zawsze chcesz uzyskać dostęp do magazynu danych, rozważ przeskalowanie go do najmniejszego rozmiaru, a nie wstrzymanie. 

Aby uzyskać instrukcje dotyczące wstrzymywania i wznawiania, zobacz [Azure Portal](pause-and-resume-compute-portal.md)lub Przewodniki Szybki Start dla [programu PowerShell](pause-and-resume-compute-powershell.md) . Można również użyć [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) lub [wznowić interfejs API REST](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Opróżnianie transakcji przed wstrzymywaniem i skalowaniem
Zalecamy zezwolenie na zakończenie istniejących transakcji przed zainicjowaniem operacji wstrzymywania lub skalowania.

Gdy ma zostać przeprowadzona procedura wstrzymania lub skalowania usługi SQL Data Warehouse, po zainicjowaniu odpowiedniego żądania następuje anulowanie zapytań w tle.  Anulowanie prostego zapytania typu SELECT to szybka operacja, która nie ma prawie żadnego wpływu na czas wstrzymywania lub skalowania wystąpienia.  Może jednak nie być możliwe szybkie zatrzymanie zapytań transakcyjnych, które modyfikują dane lub ich strukturę.  **Zapytania transakcyjne należy z założenia wykonać w całości lub wycofać ich zmiany.**  Całkowite cofnięcie wyników działania zapytania transakcyjnego może trwać równie długo lub nawet dłużej niż pierwotna zmiana wprowadzona przez zapytanie.  Na przykład w przypadku anulowania zapytania, którego zadaniem było usunięcie wierszy i które było uruchomione przez godzinę, może upłynąć kolejna godzina, zanim system z powrotem wstawi wiersze, które zostały usunięte.  W przypadku uruchomienia procedury wstrzymywania lub skalowania w toku transakcji operacja wstrzymywania lub skalowania może zająć dużo czasu, ponieważ zanim będzie możliwe jej wykonanie, zmiany muszą zostać w pełni cofnięte.

Zobacz też [Omówienie transakcji](sql-data-warehouse-develop-transactions.md)i [Optymalizowanie transakcji](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatyzowanie zarządzania obliczeniami
Aby zautomatyzować operacje zarządzania obliczeniami, zobacz [Zarządzanie obliczeniami przy użyciu usługi Azure Functions](manage-compute-with-azure-functions.md).

Każda operacja skalowania w poziomie, Wstrzymaj i Wznów może potrwać kilka minut. W przypadku automatycznego skalowania, wstrzymywania lub wznawiania zalecamy wdrożenie logiki, aby upewnić się, że niektóre operacje zostały zakończone przed przejściem do innej akcji. Sprawdzenie stanu magazynu danych za pomocą różnych punktów końcowych pozwala na prawidłowe wdrożenie automatyzacji takich operacji. 

Aby sprawdzić stan magazynu danych, zobacz Przewodnik Szybki Start dotyczący [programu PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) lub [języka T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) . Możesz również sprawdzić stan magazynu danych za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Uprawnienia

Skalowanie magazynu danych wymaga uprawnień opisanych w [instrukcji ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Polecenie Wstrzymaj i Wznów wymaga uprawnienia współautora [bazy danych SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) , w tym Microsoft. SQL/serwery/bazy danych/działanie.


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z artykułem Przewodnik dotyczący [zarządzania obliczeniami](manage-compute-with-azure-functions.md) inny aspekt zarządzania zasobami obliczeniowymi polega na przydzielaniu różnych zasobów obliczeniowych dla poszczególnych zapytań. Aby uzyskać więcej informacji, zobacz [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md).
