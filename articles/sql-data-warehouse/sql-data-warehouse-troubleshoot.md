---
title: Rozwiązywanie problemów
description: Rozwiązywanie problemów Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/25/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b2a9a7b0b759f5853d83a4b1999887414fd5f430
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483203"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse rozwiązywania problemów
W tym artykule wymieniono typowe pytania dotyczące rozwiązywania problemów.

## <a name="connecting"></a>Się
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Nieudane logowanie użytkownika „NT AUTHORITY\ANONYMOUS LOGON”. (Microsoft SQL Server, błąd: 18456) | Ten błąd występuje, gdy użytkownik usługi AAD próbuje połączyć się z bazą danych master, ale nie ma użytkownika w tej bazie danych.  Aby rozwiązać ten problem, określ usługę SQL Data Warehouse, z którą chcesz się połączyć, lub dodaj użytkownika do bazy danych master.  Aby uzyskać więcej informacji, zobacz artykuł [Omówienie zabezpieczeń][Security overview] . |
| Podmiot zabezpieczeń serwera „nousername” nie jest w stanie uzyskać dostępu do bazy danych „master” w bieżącym kontekście zabezpieczeń. Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Logowanie użytkownika „nousername” nie powiodło się. (Microsoft SQL Server, błąd: 916) | Ten błąd występuje, gdy użytkownik usługi AAD próbuje połączyć się z bazą danych master, ale nie ma użytkownika w tej bazie danych.  Aby rozwiązać ten problem, określ usługę SQL Data Warehouse, z którą chcesz się połączyć, lub dodaj użytkownika do bazy danych master.  Aby uzyskać więcej informacji, zobacz artykuł [Omówienie zabezpieczeń][Security overview] . |
| Błąd CTAIP                                                  | Ten błąd może wystąpić, gdy została utworzona nazwa logowania w bazie danych Master programu SQL Server, ale nie w bazie danych SQL Data Warehouse.  Jeśli ten błąd wystąpi, zapoznaj się z artykułem [Omówienie zabezpieczeń][Security overview] .  W tym artykule wyjaśniono, jak utworzyć identyfikator logowania i użytkownika w bazie danych Master, a następnie jak utworzyć użytkownika w usłudze SQL Data Warehouse Database. |
| Zablokowane przez zaporę                                          | Bazy danych SQL platformy Azure są chronione przez zapory na poziomie serwera i bazy danych w celu zapewnienia, że tylko znane adresy IP mają dostęp do bazy danych. Zapory są domyślnie bezpieczne, co oznacza, że musisz jawnie włączyć i adres IP lub zakres adresów, aby można było nawiązać połączenie.  Aby skonfigurować zaporę w celu uzyskania dostępu, wykonaj kroki opisane w sekcji [Konfigurowanie dostępu do zapory serwera dla adresu IP klienta][Configure server firewall access for your client IP] w [instrukcje aprowizacji][Provisioning instructions]. |
| Nie można nawiązać połączenia z narzędziem lub sterownikiem                           | SQL Data Warehouse zalecamy używanie programu [SSMS][SSMS], [SSDT dla programu Visual Studio][SSDT for Visual Studio]lub [sqlcmd][sqlcmd] do wykonywania zapytań dotyczących danych. Aby uzyskać więcej informacji na temat sterowników i łączenia się z SQL Data Warehouse, zobacz [sterowniki Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] i [Połącz się z][Connect to Azure SQL Data Warehouse] artykułami Azure SQL Data Warehouse. |

## <a name="tools"></a>Narzędzia
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Brak użytkowników usługi AAD w Eksploratorze obiektów programu Visual Studio           | Jest to znany problem.  Aby obejść ten element, Wyświetl użytkowników w obszarze [sys. database_principals][sys.database_principals].  Aby dowiedzieć się więcej o używaniu Azure Active Directory z programem SQL Data Warehouse, zobacz temat [uwierzytelnianie Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] . |
| Ręczne wykonywanie skryptów, Używanie kreatora skryptów lub Nawiązywanie połączenia za pośrednictwem programu SSMS jest powolne, nie odpowiada ani nie produkuje błędów | Upewnij się, że utworzono użytkowników w bazie danych Master. W obszarze Opcje obsługi skryptów upewnij się również, że wersja aparatu jest ustawiona na wartość "Microsoft Azure SQL Data Warehouse Edition", a typ aparatu to "Microsoft Azure SQL Database". |
| Generowanie skryptów kończy się niepowodzeniem w programie SSMS                               | Generowanie skryptu dla SQL Data Warehouse kończy się niepowodzeniem, jeśli opcja "Generuj skrypt dla obiektów zależnych" ma wartość "true". Aby obejść ten krok, użytkownicy muszą ręcznie przejść do opcji narzędzia-> Options-> Eksplorator obiektów SQL Server-> generować skrypt dla opcji zależnych i ustawić na wartość false. |

## <a name="performance"></a>Wydajność
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Rozwiązywanie problemów z wydajnością zapytań                            | Jeśli próbujesz rozwiązać określone zapytanie, Zacznij od [uczenia się, jak monitorować zapytania][Learning how to monitor your queries]. |
| Problemy dotyczące przestrzeni TempDB | [Monitoruj](sql-data-warehouse-manage-monitor.md#monitor-tempdb) użycie miejsca w bazie danych tempdb.  Typowymi przyczynami braku wolnego miejsca w bazie danych TempDB są:<br>-Za mało zasobów przypisywanych do zapytania powodującego rozłożenie danych na TempDB.  Zobacz [zarządzanie obciążeniami](resource-classes-for-workload-management.md) <br>-Statystyki nie są obecne lub są nieaktualne, powodując nadmierne przenoszenie danych.  Szczegółowe informacje na temat tworzenia statystyk można znaleźć w temacie [Obsługa statystyk tabeli][Statistics] .<br>-Pamięć TempDB jest alokowana na poziom usług.  [Skalowanie SQL Data Warehouse][Scaling your SQL Data Warehouse] do wyższego ustawienia jednostek dwu powoduje przydzielenie większej ilości miejsca w bazie danych tempdb.|
| Niska wydajność zapytań i plany często wynikają z brakujących statystyk | Najbardziej typową przyczyną niskiej wydajności jest brak statystyk w tabelach.  Zobacz temat [konserwowanie statystyk tabeli][Statistics] , aby uzyskać szczegółowe informacje na temat tworzenia statystyk oraz tego, dlaczego mają one kluczowe znaczenie dla wydajności. |
| W kolejce niskie współbieżność/zapytania                             | Zrozumienie [zarządzania obciążeniem][Workload management] jest ważne, aby zrozumieć, jak zrównoważyć alokację pamięci za pomocą współbieżności. |
| Jak zaimplementować najlepsze rozwiązania                              | Najlepszym miejscem, aby dowiedzieć się, jak zwiększyć wydajność zapytań, jest [SQL Data Warehouse artykule dotyczącym najlepszych][SQL Data Warehouse best practices] rozwiązań. |
| Jak zwiększyć wydajność dzięki skalowaniu                      | Czasami rozwiązanie zwiększające wydajność to po prostu dodanie większej mocy obliczeniowej do zapytań przez [skalowanie SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Niska wydajność zapytań w wyniku niskiej jakości indeksu     | Czasami zapytania mogą spowalniać działanie z powodu [niskiej jakości indeksu magazynu kolumn][Poor columnstore index quality].  Zapoznaj się z tym artykułem, aby uzyskać więcej informacji oraz jak [ponownie skompilować indeksy w celu zwiększenia jakości segmentu][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Zarządzanie systemem
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nie można wykonać operacji, ponieważ serwer przekroczy dozwolony limit przydziału jednostek transakcji bazy danych wynoszący 45000. | Zmniejsz [jednostek dwu][DWU] bazy danych, którą próbujesz utworzyć, lub [Zażądaj zwiększenia limitu przydziału][request a quota increase]. |
| Badanie wykorzystania miejsca                              | Zobacz [rozmiary tabeli][Table sizes] , aby zrozumieć wykorzystanie miejsca w systemie. |
| Pomoc dotycząca zarządzania tabelami                                    | Zobacz artykuł [Omówienie tabeli][Overview] , aby uzyskać pomoc dotyczącą zarządzania tabelami.  Ten artykuł zawiera również linki do bardziej szczegółowych tematów, takich jak [typy danych tabeli][Data types], [dystrybuowanie tabeli][Distribute], [indeksowanie tabeli][Index], [partycjonowanie][Partition]tabeli, [Obsługa statystyk tabeli][Statistics] i [tabel tymczasowych][Temporary]. |
| Pasek postępu programu transparent Data Encryption (TDE) nie jest aktualizowany w Azure Portal | Stan TDE można wyświetlić za pomocą [programu PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Różnice między SQL Database
| Problem                                 | Rozwiązanie                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nieobsługiwane funkcje SQL Database     | Zobacz [Nieobsługiwane funkcje tabeli][Unsupported table features]. |
| Nieobsługiwane typy danych SQL Database   | Zobacz [nieobsługiwane typy danych][Unsupported data types].        |
| Ograniczenia dotyczące usuwania i aktualizacji         | Aby obejść [nieobsługiwaną aktualizację i składnię usuwania][Using CTAS to work around unsupported UPDATE and DELETE syntax], zobacz sekcję [obejścia aktualizacji][UPDATE workarounds], [usuwanie obejść][DELETE workarounds] i używanie CTAs. |
| Instrukcja MERGE nie jest obsługiwana      | Zobacz sekcję [scalanie rozwiązań][MERGE workarounds].                  |
| Ograniczenia procedury składowanej          | Zobacz [ograniczenia procedury składowanej][Stored procedure limitations] , aby poznać niektóre ograniczenia procedur składowanych. |
| UDF nie obsługują instrukcji SELECT | Jest to bieżące ograniczenie naszych UDF.  Zobacz [Tworzenie funkcji][CREATE FUNCTION] dla składni obsługiwanej przez nas. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać pomoc w znalezieniu rozwiązania problemu, poniżej przedstawiono inne zasoby, które można wypróbować.

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
[Wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
