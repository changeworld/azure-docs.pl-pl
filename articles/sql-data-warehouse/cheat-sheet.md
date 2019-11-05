---
title: Arkusz Ściągawka dla usługi Azure Synapse Analytics (dawniej SQL DW) | Microsoft Docs
description: Znajdź linki i najlepsze rozwiązania umożliwiające szybkie tworzenie rozwiązań Azure Synapse Analytics (dawniej SQL DW).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: be5e8952ddfc6cb831b87f880bc281d6ceb2ba3d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492264"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Arkusz Ściągawka dla usługi Azure Synapse Analytics (dawniej SQL DW)

Ten arkusz Ściągawka zawiera pomocne wskazówki i najlepsze rozwiązania dotyczące tworzenia rozwiązań Synapse platformy Azure. 

Na poniższym rysunku przedstawiono proces projektowania magazynu danych:

![Szkic]

## <a name="queries-and-operations-across-tables"></a>Zapytania i operacje między tabelami

Jeśli wcześniej wiadomo, które operacje i zapytania będą uruchamiane w magazynie danych, można przydzielać priorytety architekturze magazynu danych dla tych operacji. Te zapytania i operacje mogą być następujące:
* Łączenie jednej lub dwóch tabel faktów z tabelami wymiarów, filtrowanie połączonej tabeli, a następnie łączenie wyników w składnicy danych.
* Wprowadzanie dużych lub małych aktualizacji do sprzedaży faktów.
* Dołączanie tylko danych do tabel.

Wcześniejsza znajomość typów operacji pomaga zoptymalizować projekt tabel.

## <a name="data-migration"></a>Migracja danych

Najpierw Załaduj dane do [Azure Data Lake Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) lub BLOB Storage platformy Azure. Następnie użyj podstawy, aby załadować dane do tabel przemieszczania. Użyj następującej konfiguracji:

| Projekt | Zalecenie |
|:--- |:--- |
| Dystrybucja | Działanie okrężne |
| Indeksowanie | Sterta |
| Partycjonowanie | Brak |
| Klasa zasobów | largerc lub xlargerc |

Dowiedz się więcej o [migracji danych], [ładowaniu danych] i [proces wyodrębniania, przekształcania i ładowania (ELT)](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Tabele rozproszone lub replikowane

Użyj następujących strategii, w zależności od właściwości tabeli:

| Typ | Doskonałe rozwiązanie dla...| Należy uważać, jeśli...|
|:--- |:--- |:--- |
| Replikowane | • Tabele małych wymiarów w schemacie gwiazdy z mniej niż 2 GB magazynu po kompresji (kompresja ok. 5x) |• Tabela zawiera wiele transakcji zapisu (takich jak wstawianie, operacja upsert, usuwanie, aktualizacja)<br></br>• Często zmieniasz aprowizację jednostek magazynu danych (DWU, Data Warehouse Unit)<br></br>• Używasz tylko 2–3 kolumn, ale tabela zawiera wiele kolumn<br></br>• Indeksujesz tabelę replikowaną |
| Działanie okrężne (ustawienie domyślne) | • Tabela tymczasowa/przejściowa<br></br> • Brak oczywistego klucza dołączania lub właściwej kolumny kandydata |• Wydajność jest niska z powodu przenoszenia danych |
| Skrót | • Tabele faktów<br></br>• Tabele o dużych wymiarach |• Nie można zaktualizować klucza dystrybucji |

**Porady:**
* Rozpocznij od działania okrężnego, ale staraj się zastosować strategię dystrybucji skrótów, aby skorzystać z zalet architektury z wysokim wskaźnikiem przetwarzania równoległego.
* Upewnij się, że typowe klucze skrótów mają ten sam format danych.
* Nie przeprowadzaj dystrybucji w formacie varchar.
* Tabele wymiarów ze wspólnym kluczem skrótu do tabeli faktów z częstymi operacjami sprzężenia mogą być rozproszonymi tabelami skrótów.
* Użyj elementu *[sys.dm_pdw_nodes_db_partition_stats]* , aby analizować skośność danych.
* Użyj elementu *[sys.dm_pdw_request_steps]* , aby analizować operacje przenoszenia danych w powiązaniu z zapytaniami i monitorować czas emisji oraz operacji mieszania. Jest to przydatne w przypadku przeglądu strategii dystrybucji.

Dowiedz się więcej o [tabelach replikowanych] i [tabelach rozproszonych].

## <a name="index-your-table"></a>Indeksowanie tabeli

Indeksowanie ułatwia szybkie odczytywanie tabel. W zależności od potrzeb można używać unikatowego zestawu technologii:

| Typ | Doskonałe rozwiązanie dla... | Należy uważać, jeśli...|
|:--- |:--- |:--- |
| Sterta | • Tabela przejściowa/tymczasowa<br></br>• Małe tabele z małymi wyszukiwaniami |• Każde wyszukiwanie skanuje pełną tabelę |
| Indeks klastrowany | • Tabele zawierające maksymalnie 100 milionów wierszy<br></br>• Duże tabele (ponad 100 milionów wierszy), w których często są używane tylko 1–2 kolumny |• Użycie w replikowanej tabeli<br></br>• Masz złożone zapytania obejmujące wiele operacje łączenia i grupowania<br></br>• Wprowadzasz aktualizacje indeksowanych kolumn: zajmuje to pamięć |
| Klastrowany indeks magazynu kolumn (CCI) (ustawienie domyślne) | • Duże tabele (ponad 100 milionów wierszy) | • Użycie w replikowanej tabeli<br></br>• Przeprowadzasz ogromne operacje aktualizacji w tabelach<br></br>• Występuje nadmierna aprowizacja tabeli: grupy wierszy nie obejmują różnych partycji i węzłów dystrybucji |

**Porady:**
* Oprócz indeksu klastrowanego można do kolumny intensywnie używanej do filtrowania dodać indeks nieklastrowany. 
* Zachowaj ostrożność przy zarządzaniu pamięcią tabeli przy użyciu indeksu CCI. Gdy ładujesz dane, chcesz, aby użytkownik (lub zapytanie) korzystał z zalet dużej klasy zasobów. Pamiętaj, aby unikać przycinania i tworzenia wielu małych skompresowanych grup wierszy.
* W przypadku drugiej generacji tabele indeksu CCI są buforowane lokalnie w węzłach obliczeniowych w celu zmaksymalizowania wydajności.
* W przypadku interfejsu CCI przyczyną niskiej wydajności może być słaba kompresja grup wierszy. W takim przypadku ponownie skompiluj lub zorganizuj indeks CCI. Chcesz, aby każda skompresowana grupa wierszy zawierała co najmniej 100 000 wierszy. Idealna liczba to 1 mln wierszy w grupie.
* Na podstawie rozmiaru i częstotliwości ładowania przyrostowego chcesz zautomatyzować reorganizację lub ponowną kompilację indeksów. Gruntowne porządki będą zawsze pomocne.
* Jeśli chcesz przyciąć grupę wierszy, podejdź do tego strategicznie. Jak duże są otwarte grupy wierszy? Ile danych planujesz załadować w ciągu nadchodzących dni?

Dowiedz się więcej o [indeksach].

## <a name="partitioning"></a>Partycjonowanie
Tabelę możesz partycjonować, gdy jest to duża tabela faktów (większa niż 1 miliard wierszy). W 99% przypadków klucz partycji powinien opierać się na dacie. Uważaj, aby nie partycjonować nadmiernie, szczególnie gdy korzystasz z klastrowanego indeksu magazynu kolumn.

Partycjonowanie może przynieść korzyści w przypadku tabel przejściowych, które wymagają procesu ELT. Ułatwia ono zarządzanie cyklem życia danych.
Uważaj, aby nadmiernie nie partycjonować danych, szczególnie w przypadku klastrowanego indeksu magazynu kolumn.

Dowiedz się więcej o [partycjach].

## <a name="incremental-load"></a>Ładowanie przyrostowe

Jeśli planujesz ładować dane przyrostowo, najpierw upewnij się, że przydzielasz większe klasy zasobów na potrzeby ładowania danych.  Jest to szczególnie ważne podczas ładowania do tabel z klastrowanymi indeksami magazynu kolumn.  Zobacz [klasy zasobów](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management) , aby uzyskać więcej szczegółów.  

Zalecamy korzystanie z aplikacji wielobase i ADF v2 w celu automatyzacji potoków usługi ELT w magazynie danych.

W przypadku dużej partii aktualizacji danych historycznych Rozważ użycie [CTAs](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) , aby napisać dane, które mają być przechowywane w tabeli, a nie za pomocą instrukcji INSERT, Update i DELETE.

## <a name="maintain-statistics"></a>Prowadzenie statystyk
 Do momentu, gdy funkcja autostatystyka jest ogólnie dostępna, wymagana jest ręczna konserwacja statystyk. Ważne jest aktualizowanie statystyk w miarę pojawiania się kolejnych *znaczących* zmian danych. Ułatwia to optymalizowanie planów zapytań. Jeśli okaże się, że obsługa wszystkich statystyk trwa zbyt długo, należy przemyśleć dokładnie wybór kolumn ze statystykami. 

Można również zdefiniować częstotliwość aktualizacji. Można na przykład codziennie aktualizować kolumny danych, w których mogą być dodawane nowe wartości. Największe korzyści można osiągnąć, prowadząc statystyki dla kolumn uczestniczących w sprzężeniach, kolumn używanych w ramach klauzuli WHERE i kolumn z klauzuli GROUP BY.

Dowiedz się więcej o [statystykach].

## <a name="resource-class"></a>Klasa zasobów
Grupy zasobów są używane jako sposób przydzielania pamięci do zapytań. Jeśli potrzeba większej ilości pamięci w celu zwiększenia szybkości zapytań lub ładowania, należy przydzielić wyższe klasy zasobów. Z drugiej strony użycie większych klas zasobów wpływa na współbieżność. Należy o tym pamiętać przed przeniesieniem wszystkich użytkowników do dużej klasy zasobów.

Jeśli zauważysz, że wykonywanie zapytań trwa zbyt długo, sprawdź, czy użytkownicy nie stosują uruchamiania w dużych klasach zasobów. Duże klasy zasobów używają wielu miejsc współbieżności. Może to powodować powstanie kolejki innych zasobów.

Na koniec przy użyciu Gen2 [puli SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)każda klasa zasobów otrzymuje 2,5 razy więcej pamięci niż Gen1.

Dowiedz się więcej, jak pracować z [klasami zasobów i współbieżnością].

## <a name="lower-your-cost"></a>Obniżanie kosztów
Kluczową cechą usługi Azure Synapse jest możliwość [zarządzania zasobami obliczeniowymi](sql-data-warehouse-manage-compute-overview.md). Pulę SQL można wstrzymać, gdy nie jest używana, co powoduje zatrzymanie rozliczeń zasobów obliczeniowych. Zasoby można skalować zgodnie ze swoimi wymaganiami dotyczącymi wydajności. W celu wstrzymania użyj witryny [Azure Portal](pause-and-resume-compute-portal.md) lub programu [PowerShell](pause-and-resume-compute-powershell.md). W celu skalowania użyj witryny [Azure Portal](quickstart-scale-compute-portal.md), programu [PowerShell](quickstart-scale-compute-powershell.md), języka [T-SQL](quickstart-scale-compute-tsql.md) lub [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Teraz możesz używać automatycznego skalowania w dowolnym momencie dzięki funkcji Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optymalizacja architektury pod kątem wydajności

Zalecamy rozważenie użycia bazy danych SQL Database i usługi Azure Analysis Services w architekturze gwiazdy. To rozwiązanie może spowodować rozdzielenie obciążenia między różnymi grupami użytkowników przy równoczesnym korzystaniu z zaawansowanych funkcji zabezpieczeń bazy danych SQL Database i usługi Azure Analysis Services. Jest to również sposób na zapewnienie użytkownikom nieograniczonej współbieżności.

Dowiedz się więcej [na temat typowych architektur, które wykorzystują usługę Azure Synapse](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Wdróż aplikację w jednym kliknięciem szprych w bazach danych SQL z puli SQL:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Szkic]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[ładowaniu danych]:design-elt-data-loading.md
[deeper guidance]:guidance-for-loading-data.md
[indeksach]:sql-data-warehouse-tables-index.md
[partycjach]:sql-data-warehouse-tables-partition.md
[statystykach]:sql-data-warehouse-tables-statistics.md
[klasami zasobów i współbieżnością]:resource-classes-for-workload-management.md
[tabelach replikowanych]:design-guidance-for-replicated-tables.md
[tabelach rozproszonych]:sql-data-warehouse-tables-distribute.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[migracji danych]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

[Azure Data Lake Storage]: ../data-factory/connector-azure-data-lake-store.md
[sys.dm_pdw_nodes_db_partition_stats]: /sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
