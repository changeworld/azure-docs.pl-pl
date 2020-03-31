---
title: Zarządzanie zasobem obliczeniowym dla puli SQL
description: Dowiedz się więcej o możliwościach skalowania w poziomie wydajności w puli SQL usługi Azure Synapse Analytics. Skalowanie w poziomie przez dostosowanie jednostek DU lub obniżenie kosztów przez wstrzymanie magazynu danych.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4c6c4d97282387fbcee1d7e8b55b95c01e3dded5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351616"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Zarządzanie obliczeniami w magazynie danych usługi Azure Synapse Analytics

Dowiedz się więcej o zarządzaniu zasobami obliczeniowymi w puli SQL usługi Azure Synapse Analytics. Obniż koszty, wstrzymując pulę SQL lub skaluj magazyn danych, aby sprostać wymaganiom wydajności. 

## <a name="what-is-compute-management"></a>Co to jest zarządzanie obliczeniami?

Architektura magazynu danych oddziela magazyn i obliczenia, umożliwiając każdemu skalowanie niezależnie. W rezultacie można skalować zasoby obliczeniowe w celu spełnienia wymagań związanych z wydajnością niezależnie od magazynu danych. Można również wstrzymywać i wznawiać działanie zasobów obliczeniowych. Naturalną konsekwencją tej architektury jest to, że [rozliczenia](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) za zasoby obliczeniowe i magazynowe są oddzielne. Jeśli przez pewien czas nie musisz używać magazynu, możesz zaoszczędzić na kosztach zasobów obliczeniowych, wstrzymując obliczenia. 

## <a name="scaling-compute"></a>Skalowanie obliczeń

Można skalować w poziomie lub skalować obliczenia, dostosowując ustawienie [jednostek magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) dla puli SQL. Wydajność ładowania i zapytań można zwiększać liniowo w miarę dodawania większej liczby jednostek magazynu danych. 

Aby uzyskać szczegółowe kroki, zobacz [Azure portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)lub [T-SQL](quickstart-scale-compute-tsql.md) szybki start. Można również wykonywać operacje skalowania w poziomie za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Aby wykonać operację skalowania, pula SQL najpierw zabija wszystkie zapytania przychodzące, a następnie wycofuje transakcje, aby zapewnić spójny stan. Skalowanie jest realizowane dopiero po ukończeniu wycofywania transakcji. W przypadku operacji skalowania system odłącza warstwę magazynu od węzłów obliczeniowych, dodaje węzły obliczeniowe, a następnie ponownie dołącza warstwę magazynu do warstwy obliczeniowej. Każda pula SQL jest przechowywana jako 60 dystrybucji, które są równomiernie dystrybuowane do węzłów obliczeniowych. Dodanie większej liczby węzłów obliczeniowych zwiększa moc obliczeniową. Wraz ze wzrostem liczby węzłów obliczeniowych zmniejsza się liczba dystrybucji na węzeł obliczeniowy, zapewniając większą moc obliczeniową dla zapytań. Podobnie zmniejszając jednostki magazynu danych zmniejsza liczbę węzłów obliczeniowych, co zmniejsza zasoby obliczeniowe dla zapytań.

W poniższej tabeli pokazano, jak zmienia się liczba dystrybucji na węzeł obliczeniowy w miarę zmiany jednostek magazynu danych.  DW30000c zapewnia 60 węzłów obliczeniowych i osiąga znacznie wyższą wydajność zapytań niż DW100c. 

| Jednostki magazynu danych  | \#węzłów obliczeniowych | \#rozkładów na węzeł |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c.   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Znajdowanie odpowiedniego rozmiaru jednostek magazynu danych

Aby wyświetlić korzyści wydajności skalowania w poziomie, szczególnie dla większych jednostek magazynu danych, należy użyć co najmniej 1 TB zestawu danych. Aby znaleźć najlepszą liczbę jednostek magazynu danych dla puli SQL, spróbuj skalować w górę i w dół. Uruchom kilka zapytań z różną liczbą jednostek magazynu danych po załadowaniu danych. Ponieważ skalowanie jest szybkie, można wypróbować różne poziomy wydajności w ciągu godziny lub mniej. 

Zalecenia dotyczące znajdowania najlepszej liczby jednostek magazynu danych:

- Dla puli SQL w rozwoju, należy rozpocząć od wybrania mniejszej liczby jednostek magazynu danych.  Dobrym punktem wyjścia jest DW400c lub DW200c.
- Monitoruj wydajność aplikacji, obserwując liczbę jednostek magazynu danych wybranych w porównaniu z obserwowaną wydajnością.
- Załóż skalę liniową i określ, ile trzeba zwiększyć lub zmniejszyć jednostki magazynu danych. 
- Kontynuuj wprowadzanie zmian, aż osiągniesz optymalny poziom wydajności dla twoich wymagań biznesowych.

## <a name="when-to-scale-out"></a>Kiedy skalować w poziomie

Skalowanie jednostek magazynu danych ma wpływ na te aspekty wydajności:

- Liniowo poprawia wydajność systemu do skanowania, agregacji i instrukcji CTAS.
- Zwiększa liczbę czytników i modułów zapisu do ładowania danych.
- Maksymalna liczba równoczesnych zapytań i gniazd współbieżności.

Zalecenia dotyczące skalowania w poziomie jednostek magazynu danych:

- Przed wykonaniem operacji ładowania lub przekształcania danych, skalowanie w poziomie, aby dane były dostępne szybciej.
- W godzinach szczytu pracy skaluj w poziomie, aby pomieścić większą liczbę równoczesnych zapytań. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co zrobić, jeśli skalowanie w poziomie nie poprawia wydajności?

Dodawanie jednostek magazynu danych zwiększających równoległość. Jeśli praca jest równomiernie podzielona między węzły obliczeniowe, dodatkowe równoległości zwiększa wydajność kwerendy. Jeśli skalowanie w poziomie nie zmienia wydajności, istnieją pewne powody, dla których może się to zdarzyć. Dane mogą być wypaczone w całej dystrybucji lub kwerendy mogą być wprowadzenie dużej ilości przenoszenia danych. Aby zbadać problemy z wydajnością kwerend, zobacz [Rozwiązywanie problemów z wydajnością](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Wstrzymywanie i wznawianie procesów obliczeniowych

Wstrzymanie obliczeń powoduje, że warstwa magazynu odłączyła się od węzłów obliczeniowych. Zasoby obliczeniowe są zwalniane z twojego konta. Nie są naliczane opłaty za obliczenia, gdy obliczenia są wstrzymane. Wznowienie obliczeń ponownie dołącza magazyn do węzłów obliczeniowych i wznawia opłaty za obliczenia. Po wstrzymaniu puli SQL:

* Zasoby obliczeniowe i pamięci są zwracane do puli dostępnych zasobów w centrum danych
* Koszty jednostkowe magazynu danych są zerowe dla czasu trwania pauzy.
* Nie ma to wpływu na przechowywanie danych, a dane pozostają nienaruszone. 
* Wszystkie uruchomione lub umieszczone w kolejce operacje są anulowane.

Po wznowieniu puli SQL:

* Pula SQL uzyskuje zasoby obliczeniowe i pamięci dla ustawienia jednostek magazynu danych.
* Wznowienie obliczania opłat za jednostki magazynu danych.
* Twoje dane staną się dostępne.
* Po puli SQL jest w trybie online, należy ponownie uruchomić kwerendy obciążenia.

Jeśli zawsze chcesz, aby pula SQL była dostępna, należy rozważyć skalowanie go do najmniejszego rozmiaru, a nie wstrzymywanie. 

Aby uzyskać kroki wstrzymania i wznowienia, zobacz [witrynę Azure portal](pause-and-resume-compute-portal.md)lub szybki start programu [PowerShell.](pause-and-resume-compute-powershell.md) Można również użyć [interfejsu API wstrzymania rest](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) lub interfejsu API [życiorysu REST](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Opróżnianie transakcji przed wstrzymywaniem i skalowaniem

Zaleca się zezwolenie na zakończenie istniejących transakcji przed zainicjowaniem operacji wstrzymania lub skalowania.

Po wstrzymaniu lub skalowaniu puli SQL, w kulisach zapytania są anulowane podczas inicjowania żądania wstrzymania lub skalowania. Anulowanie prostego zapytania typu SELECT to szybka operacja, która nie ma prawie żadnego wpływu na czas wstrzymywania lub skalowania wystąpienia.  Może jednak nie być możliwe szybkie zatrzymanie zapytań transakcyjnych, które modyfikują dane lub ich strukturę. **Zapytania transakcyjne należy z założenia wykonać w całości lub wycofać ich zmiany.** Całkowite cofnięcie wyników działania zapytania transakcyjnego może trwać równie długo lub nawet dłużej niż pierwotna zmiana wprowadzona przez zapytanie. Na przykład w przypadku anulowania zapytania, którego zadaniem było usunięcie wierszy i które było uruchomione przez godzinę, może upłynąć kolejna godzina, zanim system z powrotem wstawi wiersze, które zostały usunięte. W przypadku uruchomienia procedury wstrzymywania lub skalowania w toku transakcji operacja wstrzymywania lub skalowania może zająć dużo czasu, ponieważ zanim będzie możliwe jej wykonanie, zmiany muszą zostać w pełni cofnięte.

Zobacz też [Opis transakcji](sql-data-warehouse-develop-transactions.md)i [Optymalizacja transakcji](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatyzacja zarządzania obliczeniami

Aby zautomatyzować operacje zarządzania obliczeniami, zobacz [Zarządzanie obliczeniami za pomocą funkcji platformy Azure](manage-compute-with-azure-functions.md).

Każda z operacji skalowania w poziomie, wstrzymania i wznawiania może potrwać kilka minut. Jeśli skalowanie, wstrzymywanie lub wznawianie automatycznie, zaleca się implementowanie logiki, aby upewnić się, że niektóre operacje zostały zakończone przed kontynuowaniem innej akcji. Sprawdzanie stanu puli SQL za pośrednictwem różnych punktów końcowych umożliwia poprawne zaimplementowanie automatyzacji takich operacji. 

Aby sprawdzić stan puli SQL, zobacz szybki start programu [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) lub [T-SQL.](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Stan puli SQL można również sprawdzić za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Uprawnienia

Skalowanie puli SQL wymaga uprawnień opisanych w [alter database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Wstrzymaj i Wznów wymaga uprawnienia [współautora bazy danych SQL,](../../role-based-access-control/built-in-roles.md#sql-db-contributor) w szczególności microsoft.sql/servers/databases/action.


## <a name="next-steps"></a>Następne kroki
Zobacz sposób prowadzenia do [zarządzania obliczeniami](manage-compute-with-azure-functions.md) Innym aspektem zarządzania zasobami obliczeniowymi jest przydzielanie różnych zasobów obliczeniowych dla poszczególnych zapytań. Aby uzyskać więcej informacji, zobacz [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md).
