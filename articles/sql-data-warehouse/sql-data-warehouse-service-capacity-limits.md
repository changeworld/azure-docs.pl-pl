---
title: Limity pojemności — Azure SQL Data Warehouse | Microsoft Docs
description: Maksymalne wartości dozwolone dla różnych składników Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4443f94df9095da3a7ec0e9694b8089033c8d177
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686436"
---
# <a name="sql-data-warehouse-capacity-limits"></a>SQL Data Warehouse limity pojemności
Maksymalne wartości dozwolone dla różnych składników Azure SQL Data Warehouse.

## <a name="workload-management"></a>Zarządzanie obciążeniami
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| [Jednostki magazynu danych (jednostek dwu)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Maksymalna jednostek dwu dla pojedynczego SQL Data Warehouse | Gen1: DW6000<br></br>Gen2: DW30000c |
| [Jednostki magazynu danych (jednostek dwu)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |Domyślna wartość DTU na serwer |54 000<br></br>Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma limit przydziału jednostek DTU 54 000, który umożliwia do 9 DW6000c. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Możesz zwiększyć limit przydziału, [tworząc bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i wybierając pozycję *przydział* jako typ żądania.  Aby obliczyć zapotrzebowanie na jednostki DTU, pomnóż 7,5 przez łączną potrzebną jednostek dwu lub pomnożenie 9,0 przez łączną cDWU potrzebną. Na przykład:<br></br>DW6000 x 7,5 = 45 000 DTU<br></br>DW6000c x 9,0 = 54 000 DTU.<br></br>Bieżące użycie jednostek DTU można wyświetlić z poziomu opcji programu SQL Server w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. |
| Połączenie z bazą danych |Maksymalna liczba otwartych sesji współbieżnych |1024<br/><br/>Liczba równoczesnych otwartych sesji różni się w zależności od wybranej jednostek dwu. DWU600c i nowsze obsługują maksymalnie 1024 otwartych sesji. DWU500c i poniżej, obsługują maksymalny limit współbieżnych sesji otwierania wynoszący 512. Uwaga Istnieją limity liczby zapytań, które mogą być wykonywane współbieżnie. Po przekroczeniu limitu współbieżności żądanie przechodzi do kolejki wewnętrznej, w której czeka na przetworzenie. |
| Połączenie z bazą danych |Maksymalna ilość pamięci dla przygotowanych instrukcji |20 MB |
| [Zarządzanie obciążeniami](resource-classes-for-workload-management.md) |Maksymalna liczba współbieżnych zapytań |128<br/><br/> SQL Data Warehouse może wykonać maksymalnie 128 współbieżnych zapytań i kolejki pozostałych zapytań.<br/><br/>Liczba współbieżnych zapytań może się zmniejszyć, gdy użytkownicy są przypisani do wyższych klas zasobów lub gdy SQL Data Warehouse ma niższe ustawienie [jednostki magazynu danych](memory-and-concurrency-limits.md) . Niektóre zapytania, takie jak zapytania DMV, są zawsze dozwolone do uruchomienia i nie wpływają na współbieżny limit zapytań. Aby uzyskać więcej informacji na temat jednoczesnego wykonywania zapytań, zobacz artykuł [maksymalne wartości współbieżności](memory-and-concurrency-limits.md#concurrency-maximums) . |
| [tempdb](sql-data-warehouse-tables-temporary.md) |Maksymalna ilość GB |399 GB na DW100. W związku z tym w DWU1000 baza danych tempdb ma rozmiar 3,99 TB. |

## <a name="database-objects"></a>obiekty bazy danych
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Database (Baza danych) |Maksymalny rozmiar | Gen1:240 TB skompresowane na dysku. Ta przestrzeń jest niezależna od bazy danych tempdb lub miejsca w dzienniku, dlatego to miejsce jest przeznaczone do trwałych tabel.  Szacuje się, że kompresja magazynu kolumn jest w pięciokrotną.  Ta kompresja pozwala na zwiększenie rozmiaru bazy danych do około 1 PB, gdy wszystkie tabele są klastrowane z magazynu kolumn (domyślny typ tabeli). <br/><br/> Gen2:240TB dla magazynu wierszy i nieograniczony magazyn dla tabel magazynu kolumn |
| Tabela |Maksymalny rozmiar |60 TB skompresowane na dysku |
| Tabela |Tabele na bazę danych | 100 000 |
| Tabela |Kolumny na tabelę |1024 kolumn |
| Tabela |Bajtów na kolumnę |Zależne od [typu danych](sql-data-warehouse-tables-data-types.md)kolumny. Limit wynosi 8000 dla typów danych char, 4000 dla nvarchar lub 2 GB w przypadku MAKSYMALNYch typów danych. |
| Tabela |Bajty na wiersz, zdefiniowany rozmiar |8060 bajtów<br/><br/>Liczba bajtów na wiersz jest obliczana w taki sam sposób, jak w przypadku SQL Server z kompresją strony. Podobnie jak SQL Server, SQL Data Warehouse obsługuje magazyn przepełnienia wierszy, co umożliwia wypchnięcie **kolumn o zmiennej długości** poza wierszem. Gdy wiersze o zmiennej długości są wypychane poza wierszem, tylko 24-bajtowy katalog główny jest przechowywany w rekordzie głównym. Aby uzyskać więcej informacji, zobacz [dane przepełnienia wierszy przekraczające 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Partycje na tabelę |15 000<br/><br/>W celu zapewnienia wysokiej wydajności zalecamy zminimalizowanie liczby potrzebnych partycji przy zachowaniu wymagań firmy. Wraz ze wzrostem liczby partycji, obciążenie dla operacji języka definicji danych (DDL) i języka manipulowania danymi (DML) zwiększa się i powoduje wolniejszą wydajność. |
| Tabela |Liczba znaków na wartość graniczną partycji. |4000 |
| Indeks |Indeksy nieklastrowane na tabelę. |50<br/><br/>Dotyczy tylko tabel magazynu wierszy. |
| Indeks |Indeksy klastrowane na tabelę. |1<br><br/>Dotyczy zarówno magazynu wierszy, jak i tabel magazynu kolumn. |
| Indeks |Rozmiar klucza indeksu. |900 bajtów.<br/><br/>Dotyczy tylko indeksów magazynu wierszy.<br/><br/>Można utworzyć indeksy kolumn varchar o maksymalnym rozmiarze większym niż 900 bajtów, jeśli podczas tworzenia indeksu istniejące dane w kolumnach nie przekraczają 900 bajtów. Jednak późniejsze operacje wstawiania lub aktualizowania w kolumnach, które powodują, że całkowity rozmiar przekracza 900 bajtów będzie kończyć się niepowodzeniem. |
| Indeks |Kolumny kluczy na indeks. |16<br/><br/>Dotyczy tylko indeksów magazynu wierszy. Klastrowane indeksy magazynu kolumn obejmują wszystkie kolumny. |
| Statystyka |Rozmiar połączonych wartości kolumn. |900 bajtów. |
| Statystyka |Kolumny na obiekt statystyk. |32 |
| Statystyka |Statystyka utworzona dla kolumn na tabelę. |30 000 |
| Procedury składowane |Maksymalne poziomy zagnieżdżenia. |8 |
| Wyświetl |Kolumny na widok |1 024 |

## <a name="loads"></a>Powoduje
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Obciążenia wielopodstawowe |MB na wiersz |1<br/><br/>Liczba wierszy ładowania bazy jest mniejsza niż 1 MB. Ładowanie typów danych obiektów LOB do tabel z klastrowanym indeksem magazynu kolumn (WIK) nie jest obsługiwane.<br/><br/> |

## <a name="queries"></a>Zapytania
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Zapytanie |Zakolejkowane zapytania w tabelach użytkownika. |1000 |
| Zapytanie |Współbieżne zapytania w widokach systemu. |100 |
| Zapytanie |Zakolejkowane zapytania w widokach systemu |1000 |
| Zapytanie |Parametry maksymalne |2098 |
| Partia zadań |Maksymalny rozmiar |65536 * 4096 |
| Wybierz wyniki |Kolumny na wiersz |4096<br/><br/>Nie można nigdy mieć więcej niż 4096 kolumn na wiersz w wyniku SELECT. Nie ma gwarancji, że zawsze możesz mieć 4096. Jeśli plan zapytania wymaga tabeli tymczasowej, może zostać zastosowana Maksymalna liczba kolumn 1024 na tabelę. |
| SELECT |Zagnieżdżone podzapytania |32<br/><br/>W instrukcji SELECT nigdy nie można korzystać z więcej niż 32 zagnieżdżonych podzapytań. Nie ma gwarancji, że zawsze możesz mieć 32. Na przykład SPRZĘŻENIe może wprowadzić podzapytanie do planu zapytania. Liczbę podzapytań można również ograniczyć przez dostępną pamięć. |
| SELECT |Kolumny na SPRZĘŻENIe |1024 kolumn<br/><br/>W SPRZĘŻENIu nigdy nie można mieć więcej niż 1024 kolumn. Nie ma gwarancji, że zawsze możesz mieć 1024. Jeśli plan SPRZĘŻENIa wymaga tabeli tymczasowej zawierającej więcej kolumn niż wynik SPRZĘŻENIa, limit 1024 dotyczy tabeli tymczasowej. |
| SELECT |Bajtów na grupę według kolumn. |8060<br/><br/>Kolumny w klauzuli GROUP BY mogą mieć maksymalnie 8060 bajtów. |
| SELECT |Liczba bajtów na kolejność według kolumn |8060 bajtów<br/><br/>Kolumny w klauzuli ORDER BY mogą mieć maksymalnie 8060 bajtów. |
| Identyfikatory na instrukcję |Liczba przywoływanych identyfikatorów |65 535<br/><br/>SQL Data Warehouse ogranicza liczbę identyfikatorów, które mogą być zawarte w pojedynczym wyrażeniu zapytania. Przekroczenie tej liczby spowoduje SQL Server błędu 8632. Aby uzyskać więcej informacji, zobacz [błąd wewnętrzny: osiągnięto limit usług Expression Services](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literały ciągu | Liczba literałów ciągu w instrukcji | 20 000 <br/><br/>SQL Data Warehouse ogranicza liczbę stałych ciągów w jednym wyrażeniu zapytania. Przekroczenie tej liczby spowoduje SQL Server błędu 8632.|

## <a name="metadata"></a>Metadane
| Widok systemu | Maksymalna liczba wierszy |
|:--- |:--- |
| sys. DM _pdw_component_health_alerts |10 000 |
| sys. DM _pdw_dms_cores |100 |
| sys. DM _pdw_dms_workers |Łączna liczba procesów roboczych usługi DMS dla najnowszych żądań SQL 1000. |
| sys. DM _pdw_errors |10 000 |
| sys. DM _pdw_exec_requests |10 000 |
| sys. DM _pdw_exec_sessions |10 000 |
| sys. DM _pdw_request_steps |Łączna liczba kroków dla ostatnich 1000 żądań SQL, które są przechowywane w pliku sys. DM _pdw_exec_requests. |
| sys. DM _pdw_os_event_logs |10 000 |
| sys. DM _pdw_sql_requests |Najnowsze 1000 żądań SQL, które są przechowywane w pliku sys. DM _pdw_exec_requests. |

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z zaleceniami dotyczącymi używania SQL Data Warehouse, zobacz [Arkusz Ściągawka](cheat-sheet.md).
