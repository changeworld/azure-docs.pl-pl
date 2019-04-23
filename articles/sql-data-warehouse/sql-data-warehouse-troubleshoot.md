---
title: Rozwiązywanie problemów z usługi Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: dc78fbc93d625b39379e07f240eef7fbad10d194
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003857"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Rozwiązywanie problemów z usługi Azure SQL Data Warehouse
W tym artykule wymieniono typowe pytania dotyczące rozwiązywania problemów.

## <a name="connecting"></a>Łączenie
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Nie można zalogować użytkownika "NT\LOGOWANIE". (Microsoft SQL Server, Error: 18456) | Ten błąd występuje, gdy użytkownika usługi AAD, próbuje połączyć się z główną bazą danych, ale nie ma użytkownika głównego.  Aby rozwiązać ten problem, należy albo określić magazyn danych SQL, które chcesz połączyć się z chwili połączenia lub dodać użytkownika do bazy danych master.  Zobacz [Przegląd zabezpieczeń] [ Security overview] artykuł, aby uzyskać więcej informacji. |
| Serwer nie jest możliwość dostępu do bazy danych "master" w bieżącym kontekście zabezpieczeń podmiotu zabezpieczeń "Moja_nazwa_użytkownika". Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Nie można zalogować użytkownika "Moja_nazwa_użytkownika". (Microsoft SQL Server, Error: 916) | Ten błąd występuje, gdy użytkownika usługi AAD, próbuje połączyć się z główną bazą danych, ale nie ma użytkownika głównego.  Aby rozwiązać ten problem, należy albo określić magazyn danych SQL, które chcesz połączyć się z chwili połączenia lub dodać użytkownika do bazy danych master.  Zobacz [Przegląd zabezpieczeń] [ Security overview] artykuł, aby uzyskać więcej informacji. |
| Błąd CTAIP                                                  | Ten błąd może wystąpić po utworzeniu nazwy logowania na główna baza danych SQL, ale nie w bazie danych SQL Data Warehouse.  Jeśli wystąpi ten błąd, Przyjrzyj się [Przegląd zabezpieczeń] [ Security overview] artykułu.  W tym artykule wyjaśniono, jak utworzyć identyfikator logowania i użytkownika na wzorzec, a następnie utworzyć użytkownika w bazie danych SQL Data Warehouse. |
| Blokowane przez zaporę                                          | Baz danych SQL Azure są chronione przez zapory poziomu serwera i bazy danych zapewniające tylko znane adresy IP, które mają dostęp do bazy danych. Zapory są zabezpieczone przez domyślne, co oznacza, że musisz jawnie włączyć i adres IP lub zakres adresów, zanim będzie można połączyć.  Aby skonfigurować zaporę w taki sposób, aby uzyskać dostęp, wykonaj kroki opisane w [skonfigurować dostęp do zapory serwera na Twój adres IP klienta] [ Configure server firewall access for your client IP] w [aprowizacji instrukcje] [Provisioning instructions]. |
| Nie można nawiązać połączenia przy użyciu narzędzia lub sterownika                           | Usługa SQL Data Warehouse zaleca używanie [SSMS][SSMS], [SSDT dla programu Visual Studio][SSDT for Visual Studio], lub [sqlcmd] [ sqlcmd] wykonywać zapytania o swoje dane. Aby uzyskać więcej informacji na temat sterowników i łączenie z usługą SQL Data Warehouse, zobacz [sterowniki dla usługi Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] i [nawiązywanie połączenia z usługi Azure SQL Data Warehouse] [ Connect to Azure SQL Data Warehouse] artykułów. |

## <a name="tools"></a>Narzędzia
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Brak użytkowników usługi AAD w Eksploratorze obiektów programu Visual Studio           | Jest to znany problem.  Jako obejście, Wyświetl użytkowników w [sys.database_principals][sys.database_principals].  Zobacz [uwierzytelniania usługi Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] Aby dowiedzieć się więcej o korzystaniu z usługi Azure Active Directory z usługą SQL Data Warehouse. |
| Podręcznik obsługi skryptów, za pomocą Kreatora skryptów lub łączących się za pomocą programu SSMS jest powolne, nie odpowiada lub produkcji błędy | Upewnij się, że użytkownicy zostały utworzone w bazie danych master. W oknie dialogowym Opcje obsługi skryptów również upewnij się, że wersja silnika jest ustawiana jako "Microsoft Azure SQL Data Warehouse w wersji" i typ aparatu to "Microsoft Azure SQL Database". |
| Generowanie skryptów zakończy się niepowodzeniem w programie SSMS                             | Generowanie skryptu dla usługi SQL data warehouse zakończy się niepowodzeniem, jeśli opcja "Generuj skrypt dla obiektów zależnych" opcja jest ustawiona na wartość "True". Jako obejście, użytkownicy muszą ręcznie przejdź do pozycji Narzędzia -> Opcje -> Eksplorator obiektów SQL Server -> Generuj skrypt dla opcji zależnych, a wartość false |

## <a name="performance"></a>Wydajność
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Rozwiązywanie problemów z wydajnością zapytań                            | Jeśli próbujesz rozwiązywać określone zapytanie, skorzystaj z [jak monitorować zapytań][Learning how to monitor your queries]. |
| Zapytanie niską wydajność i plany często są wynikiem ma statystyk | Najczęstszą przyczyną niskiej wydajności jest brak statystyki tabel.  Zobacz [utrzymywanie statystyki tabeli] [ Statistics] szczegółowe informacje dotyczące sposobu tworzenia statystyk i dlaczego są one mają kluczowe znaczenie dla wydajności. |
| Niski współbieżności / zapytań w kolejce                             | Opis [Zarządzanie obciążeniami] [ Workload management] jest ważne, aby zrozumieć, jak zrównoważyć alokacji pamięci za pomocą współbieżności. |
| Jak wdrożyć najlepsze rozwiązania                              | Najlepszym miejscem do rozpoczęcia informacje dotyczące sposobów, aby poprawić wydajność zapytań jest [najlepsze rozwiązania SQL Data Warehouse] [ SQL Data Warehouse best practices] artykułu. |
| Jak poprawić wydajność, ze skalowaniem                      | Czasami rozwiązania do zwiększania wydajności jest po prostu Dodaj coraz większej mocy obliczeniowej możliwości zapytań przez [skalowania usługi SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Zapytania niską wydajność indeksu niskiej jakości     | Sytuacje zapytania może spowolnić z powodu [jakości indeksu magazynu kolumn niską][Poor columnstore index quality].  Ten artykuł, aby uzyskać więcej informacji i sposobie [Odbuduj indeksy, aby poprawić jakość segmentu][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Zarządzanie systemem
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nie można wykonać operacji, ponieważ serwer przekroczyłby dozwolony przydział jednostki transakcji bazy danych wynoszący 45000. | Obniż [DWU] [ DWU] próby utworzenia bazy danych lub [zażądać zwiększenia limitu przydziału][request a quota increase]. |
| Badanie wykorzystania miejsca                              | Zobacz [tabeli rozmiary] [ Table sizes] do zrozumienia wykorzystania miejsca systemu. |
| Pomoc w zarządzaniu tabel                                    | Zobacz [Omówienie tabel] [ Overview] artykuł, aby uzyskać pomoc w zarządzaniu tabel.  Ten artykuł zawiera także łącza do bardziej szczegółowych tematów, takich jak [typy danych w tabelach][Data types], [Dystrybucja tabeli][Distribute], [Indeksowania tabeli][Index], [partycjonowania tabeli][Partition], [utrzymywanie statystyki tabeli] [ Statistics] i [tabele tymczasowe][Temporary]. |
| Pasek postępu usługi technologii transparent data encryption (TDE) nie aktualizuje się w witrynie Azure Portal | Można wyświetlić stan TDE za pośrednictwem [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="polybase"></a>Program Polybase
| Problem                                           | Rozwiązanie                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Obciążenia kończy się niepowodzeniem z powodu dużych wierszy                | Obsługa duży wiersz nie jest obecnie dostępny dla programu Polybase.  Oznacza to, że jeśli tabela zawiera, VARCHAR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX), tabele zewnętrzne nie można załadować danych.  Trwa ładowanie dużych wierszy jest obecnie obsługiwane tylko za pośrednictwem usługi Azure Data Factory (przy użyciu narzędzia BCP), Azure Stream Analytics, SSIS, BCP lub klasa .NET SQLBulkCopy. Program PolyBase obsługę dużych wierszy zostanie dodana w przyszłej wersji. |
| kończy się niepowodzeniem obciążenia BCP tabeli z typem danych maksymalna | Istnieje znany problem, który wymaga, że umieszczone na końcu tabeli w niektórych scenariuszach VARCHAR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX).  Spróbuj przenieść maksymalna liczba kolumn na koniec tabeli. |

## <a name="differences-from-sql-database"></a>Różnice z bazy danych SQL
| Problem                                 | Rozwiązanie                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nieobsługiwane funkcje bazy danych SQL     | Zobacz [nieobsługiwane funkcje tabeli][Unsupported table features]. |
| Nieobsługiwane typy danych SQL Database   | Zobacz [nieobsługiwane typy danych][Unsupported data types].        |
| Usuń i ograniczenia dotyczące aktualizacji         | Zobacz [obejścia aktualizacji][UPDATE workarounds], [obejścia DELETE] [ DELETE workarounds] i [za pomocą instrukcji CTAS w celu obejścia nieobsługiwany aktualizacji i Usuń składni][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Instrukcja MERGE nie jest obsługiwana.      | Zobacz [obejścia scalania][MERGE workarounds].                  |
| Ograniczenia dotyczące procedury składowanej          | Zobacz [przechowywane procedury ograniczenia] [ Stored procedure limitations] Aby poznać niektóre ograniczenia procedur składowanych. |
| Funkcje zdefiniowane przez użytkownika nie obsługują instrukcji "SELECT" | To aktualne ograniczenie nasze funkcje zdefiniowane przez użytkownika.  Zobacz [CREATE FUNCTION] [ CREATE FUNCTION] składnię, firma Microsoft obsługuje. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać pomoc w znalezieniu rozwiązania problemu poniżej przedstawiono niektóre zasoby, które można wypróbować.

* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu CAT]
* [Tworzenie biletu pomocy technicznej]
* [Forum MSDN]
* [Forum Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Tworzenie biletu pomocy technicznej]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
