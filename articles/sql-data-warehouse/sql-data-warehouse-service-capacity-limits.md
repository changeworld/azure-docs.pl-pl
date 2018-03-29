---
title: Limity pojemności magazynu danych SQL | Dokumentacja firmy Microsoft
description: Maksymalne wartości dla połączeń, baz danych, tabel i zapytań dotyczących usługi SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 03/27/2018
ms.author: kevin;barbkess
ms.openlocfilehash: fa7d8a9880ff97f30dc583d792e39aa914ea5435
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limity pojemności magazynu danych SQL
Poniższe tabele zawierają wartości maksymalne dozwolone dla poszczególnych składników usługi Azure SQL Data Warehouse.

## <a name="workload-management"></a>Zarządzanie obciążeniami
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| [Jednostki magazynu danych (DWU)][Data Warehouse Units (DWU)] |Jednostka DWU Max jednego magazynu danych SQL | Zoptymalizowana pod kątem elastyczność [warstwę wydajności](performance-tiers.md): DW6000<br></br>Zoptymalizowana pod kątem obliczeń [warstwę wydajności](performance-tiers.md): DW30000c |
| [Jednostki magazynu danych (DWU)][Data Warehouse Units (DWU)] |Domyślne jednostek dtu w warstwie na serwer |54,000<br></br>Domyślnie każdy serwer SQL (na przykład myserver.database.windows.net) ma limit przydziału jednostek DTU równa 54 000, dzięki czemu do DW6000c. Ten limit przydziału jest po prostu limitem bezpieczeństwa. Może spowodować zwiększenie limitu przydziału przez [tworzenie biletu pomocy technicznej] [ creating a support ticket] i wybierając *przydziału* jako typ żądania.  Do obliczenia z jednostek dtu w warstwie wymaga, należy pomnożyć 7.5 przez łączną potrzebne DWU lub należy pomnożyć 9.0, przez całkowitą cDWU, potrzebne. Na przykład:<br></br>DW6000 x w wersji 7.5 = 45,000 Dtu<br></br>DW600c x 9.0 = równa 54 000 jednostek Dtu.<br></br>Twoje bieżące użycie jednostek DTU z opcji programu SQL server można wyświetlić w portalu. Limit przydziału jednostek DTU obejmuje zarówno wstrzymane, jak i niewstrzymane bazy danych. |
| Połączenie z bazą danych |Otwieranie sesji jednoczesnych |1024<br/><br/>Każdy z 1024 aktywnych sesji przesyłania żądań do bazy danych magazynu danych SQL, w tym samym czasie. Uwaga: istnieją ograniczenia liczby zapytań, które mogą być wykonywane jednocześnie. Po przekroczeniu limitu współbieżności, żądanie przechodzi w stan kolejki wewnętrznej, gdzie oczekuje na przetworzenie. |
| Połączenie z bazą danych |Maksymalna ilość pamięci dla przygotowanych instrukcji |20 MB |
| [Zarządzanie obciążenia][Workload management] |Maksymalna liczba jednoczesnych kwerend |32<br/><br/> Domyślnie usługi SQL Data Warehouse można wykonywać maksymalnie 32 zapytania jednoczesne i kolejek pozostałych zapytań.<br/><br/>Liczba równoczesnych zapytań można descrease, gdy użytkownicy są przypisane do wyższych klas zasobów lub gdy magazyn danych SQL ma mniejszy [usługi poziom](performance-tiers.md#service-levels). Niektóre kwerendy, takie jak zapytania DMV zawsze mogą być uruchamiane. |
| [tempdb][Tempdb] |Maksymalna GB |399 GB na DW100. W związku z tym w DWU1000, bazy danych tempdb jest dopasowywany do 3,99 TB |

## <a name="database-objects"></a>Obiekty bazy danych
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Database (Baza danych) |Maks. rozmiar |240 TB skompresowane na dysku<br/><br/>Ta przestrzeń jest niezależna od miejsca w bazie danych tempdb lub dziennika, a w związku z tym to miejsce jest przydzielane trwałych tabelach.  Kompresja klastrowanego magazynu kolumn szacuje się na 5 X.  Kompresja ta umożliwia wzrostu około 1 bazy PB, gdy wszystkie tabele są klastrowanego magazynu kolumn (domyślny typ tabeli). |
| Tabela |Maks. rozmiar |60 TB skompresowane na dysku |
| Tabela |Tabel na bazę danych |10 000 |
| Tabela |Kolumn w tabeli |1024 kolumn |
| Tabela |Liczba bajtów na kolumny |Zależne od kolumny [— typ danych][data type].  Limit wynosi 8000 dla typów danych char, 4000 dla nvarchar lub 2 GB dla typów danych MAX. |
| Tabela |Liczba bajtów na wiersz, zdefiniowanego rozmiaru |8060 bajtów<br/><br/>Liczba bajtów na wiersz jest obliczana tak samo jak dla programu SQL Server w kompresji strony. Jak SQL Server magazynu danych SQL obsługuje magazynu przepełnienia wiersza, który umożliwia **kolumn o zmiennej długości** do zostać przeniesiony poza wiersz. Jeśli wierszy o zmiennej długości są przenoszone poza wiersz, tylko bajtów 24 główny jest przechowywany w głównym rekordzie. Aby uzyskać więcej informacji, zobacz [przepełnienia wiersza danych przekraczających rozmiarze 8 KB][Row-Overflow Data Exceeding 8 KB]. |
| Tabela |Partycje tabeli |15,000<br/><br/>O wysokiej wydajności, firma Microsoft zaleca, minimalizując liczbę partycji muszą podczas przerywania obsługi wymagań biznesowych. Miarę zwiększania się liczby partycji, obciążenie dla operacji języka definicji danych (DDL) i manipulowania języka DML (Data) rozwoju i powoduje, że niższej wydajności. |
| Tabela |Liczba znaków na wartość graniczna partycji. |4000 |
| Indeks |Indeksy klastrowane nie na tabelę. |50<br/><br/>Dotyczy tylko tabele magazynu wierszy. |
| Indeks |Indeksy klastrowane na tabelę. |1<br><br/>Dotyczy zarówno magazynu wierszy, jak i magazynu kolumn tabeli. |
| Indeks |Rozmiar klucza indeksu. |900 bajtów.<br/><br/>Dotyczy tylko indeksów magazynu wierszy.<br/><br/>Jeśli istniejące dane w kolumnach nie przekracza 900 bajtów podczas tworzenia indeksu można utworzyć indeksy w kolumnach varchar o maksymalnym rozmiarze więcej niż 900 bajtów. Jednak później WSTAWIĆ lub akcji aktualizacji dla kolumn, które powodują łączny rozmiar przekracza 900 bajtów zakończy się niepowodzeniem. |
| Indeks |Kolumny klucza w indeksie. |16<br/><br/>Dotyczy tylko indeksów magazynu wierszy. Klastrowane indeksy magazynu kolumn zawiera wszystkich kolumn. |
| Statystyki |Rozmiar wartości w kolumnie łączna. |900 bajtów. |
| Statystyki |Kolumny dla każdego obiektu statystyk. |32 |
| Statystyki |Statystyki tworzone dla kolumn w tabeli. |30,000 |
| Procedury składowane |Maksymalnej liczby poziomów zagnieżdżenia. |8 |
| Widok |Kolumn w widoku |1,024 |

## <a name="loads"></a>Obciążenia
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Program Polybase obciążenia |MB na wiersz |1<br/><br/>Program Polybase ładuje tylko do wierszy, które są mniejsze niż 1 MB, a nie można załadować VARCHAR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Zapytania
| Kategoria | Opis | Maksimum |
|:--- |:--- |:--- |
| Zapytanie |W kolejce kwerend w tabelach użytkownika. |1000 |
| Zapytanie |Zapytania jednoczesne na widoki systemu. |100 |
| Zapytanie |Zapytania w kolejce na widoki systemu |1000 |
| Zapytanie |Maksymalna parametrów |2098 |
| Batch |Maksymalny rozmiar |65,536*4096 |
| Wybierz wyniki |Kolumn w wierszu |4096<br/><br/>Nigdy nie może mieć więcej niż 4096 kolumn na wiersz, w wyniku wybierz. Nie ma żadnej gwarancji, że zawsze może mieć 4096. Jeśli plan zapytania wymaga tabeli tymczasowej, 1024 kolumn dla tabeli maksymalną mogą być stosowane. |
| WYBIERZ |Podzapytania zagnieżdżonych |32<br/><br/>Program może nigdy nie więcej niż 32 zagnieżdżonych podzapytań w instrukcji SELECT. Nie ma żadnej gwarancji, że zawsze może mieć 32. Na przykład sprzężenia można wprowadzać podzapytania do planu zapytania. Liczba podzapytania ograniczeniem również dostępnej pamięci. |
| WYBIERZ |Kolumn w SPRZĘŻENIU |1024 kolumn<br/><br/>Program może nigdy nie więcej niż 1024 kolumny sprzężenia. Nie ma żadnej gwarancji, że zawsze może mieć 1024. Jeśli plan sprzężenia wymaga tabeli tymczasowej o więcej kolumn niż wynik sprzężenia, 1024 limit stosuje się do tabeli tymczasowej. |
| WYBIERZ |Bajty na grupy według kolumn. |8060<br/><br/>Kolumn w klauzuli GROUP BY może zawierać maksymalnie 8060 bajtów. |
| WYBIERZ |Bajtów w kolejności według kolumn |8060 bajtów<br/><br/>Kolumn w klauzuli ORDER BY może zawierać maksymalnie 8060 bajtów |
| Identyfikatory na instrukcję |Liczba identyfikatorów przywoływany |65,535<br/><br/>Usługa SQL Data Warehouse ogranicza liczbę identyfikatorów, które mogą znajdować się w jednym wyrażeniu zapytania. Przekraczające ten numer powoduje błąd programu SQL Server 8632. Aby uzyskać więcej informacji, zobacz [błąd wewnętrzny: Osiągnięto limit usług wyrażeń][Internal error: An expression services limit has been reached]. |
| Literały ciągu | Liczba literałów ciągu w instrukcji | 20,000 <br/><br/>Usługa SQL Data Warehouse limites liczba stałe typu string, które mogą znajdować się w jednym wyrażeniu zapytania. Przekraczające ten numer powoduje błąd programu SQL Server 8632. Aby uzyskać więcej informacji, zobacz [błąd wewnętrzny: Osiągnięto limit usług wyrażeń][Internal error: An expression services limit has been reached]. |

## <a name="metadata"></a>Metadane
| Widok systemu | Maksymalna liczba wierszy |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Całkowita liczba pracowników DMS dla ostatniego 1000 żądań SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Całkowita liczba kroków najnowszych żądań SQL 1000, które są przechowywane w sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Najnowsze 1000 SQL żądania, które są przechowywane w sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacje, zobacz [Omówienie usługi SQL Data Warehouse][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./resource-classes-for-workload-management.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
