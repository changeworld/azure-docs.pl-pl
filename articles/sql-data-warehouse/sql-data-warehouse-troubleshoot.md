---
title: Rozwiązywanie problemów z usługą Magazyn danych Azure SQL | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługą Magazyn danych Azure SQL.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 27445eb754a5e985485db101c9d0fe1ba8aa2451
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Rozwiązywanie problemów z usługą Magazyn danych Azure SQL
Ten temat zawiera listę często zadawane pytania dotyczące rozwiązywania problemów.

## <a name="connecting"></a>Łączenie
| Problem | Rozwiązanie |
|:--- |:--- |
| Logowanie użytkownika "NT\LOGOWANIE" nie powiodło się. (Program Microsoft SQL Server, błąd: 18456) |Ten błąd występuje, gdy użytkownik AAD próbuje połączyć się z główną bazą danych, ale nie ma użytkownika głównego.  Aby rozwiązać ten problem, albo określić magazyn danych SQL, aby połączyć się w chwili połączenia lub dodać użytkownika do bazy danych master.  Zobacz [Omówienie zabezpieczeń] [ Security overview] artykułu, aby uzyskać więcej informacji. |
| Serwer główny "MyUserName" nie jest możliwość dostępu do bazy danych "master" w bieżącym kontekście zabezpieczeń. Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Logowanie użytkownika "MyUserName" nie powiodło się. (Program Microsoft SQL Server, błąd: 916) |Ten błąd występuje, gdy użytkownik AAD próbuje połączyć się z główną bazą danych, ale nie ma użytkownika głównego.  Aby rozwiązać ten problem, albo określić magazyn danych SQL, aby połączyć się w chwili połączenia lub dodać użytkownika do bazy danych master.  Zobacz [Omówienie zabezpieczeń] [ Security overview] artykułu, aby uzyskać więcej informacji. |
| Błąd CTAIP |Ten błąd może wystąpić po utworzeniu nazwy logowania bazy danych master serwera SQL, ale nie znajduje się w bazie danych magazynu danych SQL.  Jeśli ten błąd wystąpi, Przyjrzyjmy się [Omówienie zabezpieczeń] [ Security overview] artykułu.  W tym artykule opisano, jak utworzyć identyfikator logowania i użytkownika na wzorcu, a następnie utworzyć użytkownika w bazie danych usługi SQL Data Warehouse. |
| Blokowane przez zaporę |Bazy danych SQL Azure są chronione przez zapory poziomu serwera i bazy danych do zapewnienia tylko znane adresy IP, które mają dostęp do bazy danych. Zapory są zabezpieczone przez domyślną, co oznacza, że musisz jawnie włączyć oraz adres IP lub zakres adresów, zanim będzie można połączyć.  Aby skonfigurować zaporę tak, aby uzyskać dostęp, postępuj zgodnie z instrukcjami [konfigurowania serwera zapory dostępu do sieci IP klienta] [ Configure server firewall access for your client IP] w [inicjowania obsługi administracyjnej instrukcje] [Provisioning instructions]. |
| Nie można połączyć z narzędziem lub sterownika |Usługa SQL Data Warehouse zaleca użycie [SSMS][SSMS], [narzędzi SSDT dla programu Visual Studio][SSDT for Visual Studio], lub [sqlcmd] [ sqlcmd] zgromadzonych danych. Więcej szczegółów na sterowniki i nawiązywania połączenia z usługi SQL Data Warehouse, zobacz [sterowniki dla usługi Azure SQL Data Warehouse] [ Drivers for Azure SQL Data Warehouse] i [nawiązywanie połączenia z usługą Magazyn danych SQL Azure] [ Connect to Azure SQL Data Warehouse] artykułów. |

## <a name="tools"></a>Narzędzia
| Problem | Rozwiązanie |
|:--- |:--- |
| Eksplorator obiektów programu Visual Studio nie ma użytkowników usługi AAD |Jest to znany problem.  Jako obejście, wyświetlania informacji o użytkownikach w [sys.database_principals][sys.database_principals].  Zobacz [uwierzytelniania usługi Azure SQL Data Warehouse] [ Authentication to Azure SQL Data Warehouse] Aby dowiedzieć się więcej o korzystaniu z usługi Azure Active Directory z usługą Magazyn danych SQL. |
|Podręcznik obsługi skryptów, za pomocą Kreatora skryptów lub łączących się za pomocą narzędzia SSMS jest powolne, zawieszone lub tworzenie błędów| Upewnij się, że użytkownicy zostały utworzone w bazie danych master. W oknie dialogowym Opcje obsługi skryptów również upewnij się, że wersji aparatu jest ustawiony jako "Microsoft Azure SQL Data magazynu Edition" i "Baza danych SQL Microsoft Azure" jest typ aparatu.|

## <a name="performance"></a>Wydajność
| Problem | Rozwiązanie |
|:--- |:--- |
| Rozwiązywanie problemów z wydajnością zapytania |Jeśli chcesz rozwiązać określonego zapytania, Rozpocznij od [dowiedzieć, jak monitorować zapytań][Learning how to monitor your queries]. |
| Zapytanie niską wydajność i planów często wynika ma statystyk |Najczęstszą przyczyną niskiej wydajności jest brak statystyk dotyczących tabel.  Zobacz [utrzymania statystyk tabeli] [ Statistics] szczegółowe informacje o sposobie tworzenia statystyk i dlaczego są krytyczne znaczenie dla wydajności. |
| Współbieżność niski / zapytań w kolejce |Opis [zarządzania obciążenia] [ Workload management] jest ważne, aby zrozumieć, jak Równoważenie alokacji pamięci z współbieżności. |
| Jak zaimplementować najlepsze rozwiązania |Najlepiej rozpocząć informacje o sposobach poprawiać wydajność zapytań [najlepsze rozwiązania w zakresie usługi SQL Data Warehouse] [ SQL Data Warehouse best practices] artykułu. |
| Jak poprawić wydajność w przypadku skalowania |Czasami rozwiązanie do poprawy wydajności, to po prostu Dodaj więcej mocy do zapytań przez obliczeniowej [skalowania SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Wydajność zapytań niską wyniku indeksu słabą jakość |Sytuacje kwerend może to spowolnić z powodu [jakości indeksu magazynu kolumn niską][Poor columnstore index quality].  Ten artykuł, aby uzyskać więcej informacji i sposobu [Odbuduj indeksy, aby poprawić jakość segmentu][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Zarządzanie systemem
| Problem | Rozwiązanie |
|:--- |:--- |
| Msg 40847: Nie można wykonać operacji, ponieważ serwer przekroczyłby dozwolony przydział jednostki transakcji bazy danych z 45000. |Obniż [DWU] [ DWU] podjęto próbę utworzenia bazy danych lub [zażądać zwiększenia limitu przydziału][request a quota increase]. |
| Badanie wykorzystania miejsca |Zobacz [tabeli rozmiary] [ Table sizes] zrozumienie wykorzystanie miejsca systemu. |
| Pomóc w zarządzaniu tabel |Zobacz [omówienie tabeli] [ Overview] artykułu, aby uzyskać pomoc dotyczącą zarządzania tabel.  Ten artykuł zawiera także linki do bardziej szczegółowych tematów, takich jak [typów danych tabeli][Data types], [Dystrybucja tabeli][Distribute], [Indeksowania tabeli][Index], [partycjonowania tabeli][Partition], [utrzymania statystyk tabeli] [ Statistics] i [tabel tymczasowych][Temporary]. |
|Pasek postępu danych przezroczystego szyfrowania (funkcji TDE) nie jest aktualizowana w portalu Azure|Można wyświetlić stan funkcji TDE za pośrednictwem [powershell](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption).|

## <a name="polybase"></a>Program Polybase
| Problem | Rozwiązanie |
|:--- |:--- |
| Obciążenia zakończy się niepowodzeniem z powodu dużych wierszy |Obsługa dużych wiersza nie jest obecnie dostępna dla programu Polybase.  Oznacza to, że jeśli tabela zawiera VARCHAR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX), tabel zewnętrznych nie można załadować danych.  Podczas ładowania dużych wierszy jest obecnie obsługiwane tylko za pośrednictwem usługi fabryka danych Azure (za pomocą narzędzia BCP), usługi Azure Stream Analytics, SSIS, BCP lub klasy .NET SQLBulkCopy. Obsługa PolyBase dla dużych wierszy zostanie dodana w przyszłej wersji. |
| obciążenia BCP tabeli z typem danych MAX kończy się niepowodzeniem |Jest to znany problem, który wymaga VARCHAR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX) można umieścić na koniec tabeli w niektórych scenariuszach.  Spróbuj przenieść maksymalna liczba kolumn na koniec tabeli. |

## <a name="differences-from-sql-database"></a>Różnice z bazy danych SQL
| Problem | Rozwiązanie |
|:--- |:--- |
| Nieobsługiwane funkcje bazy danych SQL |Zobacz [nieobsługiwane funkcje tabeli][Unsupported table features]. |
| Nieobsługiwane typy danych bazy danych SQL |Zobacz [nieobsługiwane typy danych][Unsupported data types]. |
| Usuń i ograniczenia aktualizacji |Zobacz [obejścia aktualizacji][UPDATE workarounds], [obejścia DELETE] [ DELETE workarounds] i [przy użyciu CTAS w celu obejścia nieobsługiwany aktualizacji i Usuń składni][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Instrukcji MERGE nie jest obsługiwane. |Zobacz [obejścia scalania][MERGE workarounds]. |
| Procedura składowana ograniczenia |Zobacz [przechowywane procedury ograniczenia] [ Stored procedure limitations] do zrozumienia niektórych ograniczeń procedur składowanych. |
| Funkcje UDF nie obsługują instrukcji "SELECT" |Jest to aktualne ograniczenie naszych funkcji UDF.  Zobacz [CREATE FUNCTION] [ CREATE FUNCTION] obsługujemy składni. |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać pomoc w znalezieniu rozwiązania problemu Oto inne zasoby, które można wypróbować.

* [Blogi]
* [Żądania funkcji]
* [Wideo]
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
[Distribute]:/sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
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
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
