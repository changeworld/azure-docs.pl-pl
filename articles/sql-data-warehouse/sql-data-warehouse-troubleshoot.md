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
ms.openlocfilehash: ce57c48e568e840f3a651a5530f3fba6c0be60b7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721051"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse rozwiązywania problemów
W tym artykule wymieniono typowe pytania dotyczące rozwiązywania problemów.

## <a name="connecting"></a>Się
| Problem                                                        | Rozdzielczość                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Nieudane logowanie użytkownika „NT AUTHORITY\ANONYMOUS LOGON”. (Microsoft SQL Server, błąd: 18456) | Ten błąd występuje, gdy użytkownik usługi AAD próbuje połączyć się z bazą danych master, ale nie ma użytkownika w tej bazie danych.  Aby rozwiązać ten problem, określ usługę SQL Data Warehouse, z którą chcesz się połączyć, lub dodaj użytkownika do bazy danych master.  Aby uzyskać więcej informacji, zobacz artykuł [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md) . |
| Podmiot zabezpieczeń serwera „nousername” nie jest w stanie uzyskać dostępu do bazy danych „master” w bieżącym kontekście zabezpieczeń. Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Logowanie użytkownika „nousername” nie powiodło się. (Microsoft SQL Server, błąd: 916) | Ten błąd występuje, gdy użytkownik usługi AAD próbuje połączyć się z bazą danych master, ale nie ma użytkownika w tej bazie danych.  Aby rozwiązać ten problem, określ usługę SQL Data Warehouse, z którą chcesz się połączyć, lub dodaj użytkownika do bazy danych master.  Aby uzyskać więcej informacji, zobacz artykuł [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md) . |
| Błąd CTAIP                                                  | Ten błąd może wystąpić, gdy została utworzona nazwa logowania w bazie danych Master programu SQL Server, ale nie w bazie danych SQL Data Warehouse.  Jeśli ten błąd wystąpi, zapoznaj się z artykułem [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md) .  W tym artykule wyjaśniono, jak utworzyć identyfikator logowania i użytkownika w bazie danych Master, a następnie jak utworzyć użytkownika w usłudze SQL Data Warehouse Database. |
| Zablokowane przez zaporę                                          | Bazy danych SQL platformy Azure są chronione przez zapory na poziomie serwera i bazy danych w celu zapewnienia, że tylko znane adresy IP mają dostęp do bazy danych. Zapory są domyślnie bezpieczne, co oznacza, że musisz jawnie włączyć i adres IP lub zakres adresów, aby można było nawiązać połączenie.  Aby skonfigurować zaporę w celu uzyskania dostępu, wykonaj kroki opisane w sekcji [Konfigurowanie dostępu do zapory serwera dla adresu IP klienta](sql-data-warehouse-get-started-provision.md) w [instrukcje aprowizacji](sql-data-warehouse-get-started-provision.md). |
| Nie można nawiązać połączenia z narzędziem lub sterownikiem                           | SQL Data Warehouse zalecamy używanie programu [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT dla programu Visual Studio](sql-data-warehouse-install-visual-studio.md)lub [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) do wykonywania zapytań dotyczących danych. Aby uzyskać więcej informacji na temat sterowników i łączenia się z SQL Data Warehouse, zobacz [sterowniki Azure SQL Data Warehouse](sql-data-warehouse-connection-strings.md) i [Połącz się z](sql-data-warehouse-connect-overview.md) artykułami Azure SQL Data Warehouse. |

## <a name="tools"></a>narzędzia
| Problem                                                        | Rozdzielczość                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Brak użytkowników usługi AAD w Eksploratorze obiektów programu Visual Studio           | Jest to znany problem.  Aby obejść ten element, Wyświetl użytkowników w obszarze [sys. database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Aby dowiedzieć się więcej o używaniu Azure Active Directory z programem SQL Data Warehouse, zobacz temat [uwierzytelnianie Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) . |
| Ręczne wykonywanie skryptów, Używanie kreatora skryptów lub Nawiązywanie połączenia za pośrednictwem programu SSMS jest powolne, nie odpowiada ani nie produkuje błędów | Upewnij się, że utworzono użytkowników w bazie danych Master. W obszarze Opcje obsługi skryptów upewnij się również, że wersja aparatu jest ustawiona na wartość "Microsoft Azure SQL Data Warehouse Edition", a typ aparatu to "Microsoft Azure SQL Database". |
| Generowanie skryptów kończy się niepowodzeniem w programie SSMS                               | Generowanie skryptu dla SQL Data Warehouse kończy się niepowodzeniem, jeśli opcja "Generuj skrypt dla obiektów zależnych" ma wartość "true". Aby obejść ten krok, użytkownicy muszą ręcznie przejść do **opcji narzędzia-> Options-> Eksplorator obiektów SQL Server-> generować skrypt dla opcji zależnych i ustawić na wartość false** . |

## <a name="performance"></a>Wydajność
| Problem                                                        | Rozdzielczość                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Rozwiązywanie problemów z wydajnością zapytań                            | Jeśli próbujesz rozwiązać określone zapytanie, Zacznij od [uczenia się, jak monitorować zapytania](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemy dotyczące przestrzeni TempDB | [Monitoruj](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-tempdb) użycie miejsca w bazie danych tempdb.  Typowymi przyczynami braku wolnego miejsca w bazie danych TempDB są:<br>-Za mało zasobów przypisywanych do zapytania powodującego rozłożenie danych na TempDB.  Zobacz [zarządzanie obciążeniami](resource-classes-for-workload-management.md) <br>-Statystyki nie są obecne lub są nieaktualne, powodując nadmierne przenoszenie danych.  Szczegółowe informacje na temat tworzenia statystyk można znaleźć w temacie [Obsługa statystyk tabeli](sql-data-warehouse-tables-statistics.md) .<br>-Pamięć TempDB jest alokowana na poziom usług.  [Skalowanie SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scaling-compute) do wyższego ustawienia jednostek dwu powoduje przydzielenie większej ilości miejsca w bazie danych tempdb.|
| Niska wydajność zapytań i plany często wynikają z brakujących statystyk | Najbardziej typową przyczyną niskiej wydajności jest brak statystyk w tabelach.  Zobacz temat [konserwowanie statystyk tabeli](sql-data-warehouse-tables-statistics.md) , aby uzyskać szczegółowe informacje na temat tworzenia statystyk oraz tego, dlaczego mają one kluczowe znaczenie dla wydajności. |
| W kolejce niskie współbieżność/zapytania                             | Zrozumienie [zarządzania obciążeniem](resource-classes-for-workload-management.md) jest ważne, aby zrozumieć, jak zrównoważyć alokację pamięci za pomocą współbieżności. |
| Jak zaimplementować najlepsze rozwiązania                              | Najlepszym miejscem, aby dowiedzieć się, jak zwiększyć wydajność zapytań, jest [SQL Data Warehouse artykule dotyczącym najlepszych](sql-data-warehouse-best-practices.md) rozwiązań. |
| Jak zwiększyć wydajność dzięki skalowaniu                      | Czasami rozwiązanie zwiększające wydajność to po prostu dodanie większej mocy obliczeniowej do zapytań przez [skalowanie SQL Data Warehouse](sql-data-warehouse-manage-compute-overview.md). |
| Niska wydajność zapytań w wyniku niskiej jakości indeksu     | Czasami zapytania mogą spowalniać działanie z powodu [niskiej jakości indeksu magazynu kolumn](../sql-data-warehouse/sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Zapoznaj się z tym artykułem, aby uzyskać więcej informacji oraz jak [ponownie skompilować indeksy w celu zwiększenia jakości segmentu](../sql-data-warehouse/sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Zarządzanie systemem
| Problem                                                        | Rozdzielczość                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nie można wykonać operacji, ponieważ serwer przekroczy dozwolony limit przydziału jednostek transakcji bazy danych wynoszący 45000. | Zmniejsz [jednostek dwu](what-is-a-data-warehouse-unit-dwu-cdwu.md) bazy danych, którą próbujesz utworzyć, lub [Zażądaj zwiększenia limitu przydziału](sql-data-warehouse-get-started-create-support-ticket.md). |
| Badanie wykorzystania miejsca                              | Zobacz [rozmiary tabeli]( ../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) , aby zrozumieć wykorzystanie miejsca w systemie. |
| Pomoc dotycząca zarządzania tabelami                                    | Zobacz artykuł [Omówienie tabeli] [przegląd], aby uzyskać pomoc dotyczącą zarządzania tabelami.  Ten artykuł zawiera również linki do bardziej szczegółowych tematów, takich jak [typy danych tabeli](sql-data-warehouse-tables-data-types.md), [dystrybuowanie tabeli](sql-data-warehouse-tables-distribute.md), [indeksowanie tabeli](sql-data-warehouse-tables-index.md), [partycjonowanie](sql-data-warehouse-tables-partition.md)tabeli, [Obsługa statystyk tabeli](sql-data-warehouse-tables-statistics.md) i [tabel tymczasowych](sql-data-warehouse-tables-temporary.md). |
| Pasek postępu programu transparent Data Encryption (TDE) nie jest aktualizowany w Azure Portal | Stan TDE można wyświetlić za pomocą [programu PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Różnice między SQL Database
| Problem                                 | Rozdzielczość                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nieobsługiwane funkcje SQL Database     | Zobacz [Nieobsługiwane funkcje tabeli](../sql-data-warehouse/sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nieobsługiwane typy danych SQL Database   | Zobacz [nieobsługiwane typy danych](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Ograniczenia dotyczące usuwania i aktualizacji         | Aby obejść [nieobsługiwaną aktualizację i składnię usuwania](sql-data-warehouse-develop-ctas.md), zobacz sekcję [obejścia aktualizacji](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [usuwanie obejść](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) i używanie CTAs. |
| Instrukcja MERGE nie jest obsługiwana      | Zobacz sekcję [scalanie rozwiązań](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Ograniczenia procedury składowanej          | Zobacz [ograniczenia procedury składowanej](../sql-data-warehouse/sql-data-warehouse-develop-stored-procedures.md#limitations) , aby poznać niektóre ograniczenia procedur składowanych. |
| UDF nie obsługują instrukcji SELECT | Jest to bieżące ograniczenie naszych UDF.  Zobacz [Tworzenie funkcji](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) dla składni obsługiwanej przez nas. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać pomoc w znalezieniu rozwiązania problemu, poniżej przedstawiono inne zasoby, które można wypróbować.

* [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)