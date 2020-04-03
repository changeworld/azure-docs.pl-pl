---
title: Rozwiązywanie problemów
description: Rozwiązywanie problemów z usługą Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 47ee6f7627602732800949bcb9701045fcbff1a8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583177"
---
# <a name="troubleshooting-synapse-sql-pool-in-azure-synapse-analytics"></a>Rozwiązywanie problemów z pulą SQL synapse w usłudze Azure Synapse Analytics

W tym artykule wymieniono typowe pytania dotyczące rozwiązywania problemów.

## <a name="connecting"></a>Łączenie
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Nieudane logowanie użytkownika „NT AUTHORITY\ANONYMOUS LOGON”. (Microsoft SQL Server, błąd: 18456) | Ten błąd występuje, gdy użytkownik usługi Azure AD próbuje połączyć się z główną bazą danych, ale nie ma użytkownika we wzorcu.  Aby rozwiązać ten problem, należy określić pulę SQL, z którą chcesz się połączyć w czasie połączenia, albo dodać użytkownika do głównej bazy danych.  Zobacz [omówienie zabezpieczeń, aby](sql-data-warehouse-overview-manage-security.md) uzyskać więcej informacji. |
| Podmiot zabezpieczeń serwera „nousername” nie jest w stanie uzyskać dostępu do bazy danych „master” w bieżącym kontekście zabezpieczeń. Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Logowanie użytkownika „nousername” nie powiodło się. (Microsoft SQL Server, błąd: 916) | Ten błąd występuje, gdy użytkownik usługi Azure AD próbuje połączyć się z główną bazą danych, ale nie ma użytkownika we wzorcu.  Aby rozwiązać ten problem, należy określić pulę SQL, z którą chcesz się połączyć w czasie połączenia, albo dodać użytkownika do głównej bazy danych.  Zobacz [omówienie zabezpieczeń, aby](sql-data-warehouse-overview-manage-security.md) uzyskać więcej informacji. |
| Błąd CTAIP                                                  | Ten błąd może wystąpić, gdy login został utworzony w głównej bazie danych serwera SQL, ale nie w bazie danych SQL.  Jeśli wystąpi ten błąd, zapoznaj się z [omówieniem zabezpieczeń.](sql-data-warehouse-overview-manage-security.md)  W tym artykule wyjaśniono, jak utworzyć login i użytkownika na wzorcu, a następnie jak utworzyć użytkownika w bazie danych SQL. |
| Zablokowane przez zaporę sieciową                                          | Pule SQL są chronione przez zapory, aby upewnić się, że tylko znane adresy IP mają dostęp do bazy danych. Zapory są domyślnie bezpieczne, co oznacza, że przed nawiązaniem połączenia należy jawnie włączyć adres IP lub zakres adresów.  Aby skonfigurować zaporę dostępu, wykonaj czynności opisane w temacie [Konfigurowanie dostępu do zapory serwera dla adresu IP klienta](create-data-warehouse-portal.md) w [instrukcjach inicjowania obsługi administracyjnej](create-data-warehouse-portal.md). |
| Nie można połączyć się z narzędziem lub sterownikiem                           | Puli SQL Synapse zaleca użycie [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15), [SSDT dla programu Visual Studio](sql-data-warehouse-install-visual-studio.md)lub [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) do badania danych. Aby uzyskać więcej informacji na temat sterowników i łączenia się z platformą Azure Synapse, zobacz [Sterowniki dla platformy Azure Synapse](sql-data-warehouse-connection-strings.md) i połącz się z artykułami [usługi Azure Synapse.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>narzędzia
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| W Eksploratorze obiektów programu Visual Studio brakuje użytkowników usługi Azure AD           | Jest to znany problem.  Aby obejść ten problem, wyświetl użytkowników w [pliku sys.database_principals](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver15).  Zobacz [Uwierzytelnianie w usłudze Azure Synapse,](sql-data-warehouse-authentication.md) aby dowiedzieć się więcej o korzystaniu z usługi Azure Active Directory z pulą SQL Synapse. |
| Ręczne wykonywanie skryptów, korzystanie z kreatora skryptów lub łączenie się za pośrednictwem usługi SSMS jest powolne, nie odpowiada lub powoduje błędy | Upewnij się, że użytkownicy zostali stworzeni w głównej bazie danych. W opcjach skryptów upewnij się również, że wersja aparatu jest ustawiona jako "Microsoft Azure SQL Data Warehouse Edition", a typ aparatu to "Microsoft Azure SQL Database". |
| Generowanie skryptów kończy się niepowodzeniem w ssms                               | Generowanie skryptu dla puli SQL Synapse kończy się niepowodzeniem, jeśli opcja "Generowanie skryptu dla obiektów zależnych" jest ustawiona na "True". Aby obejść ten problem, użytkownicy muszą ręcznie przejść do **narzędzia -> Options ->SQL Server Object Explorer -> Generuj skrypt dla opcji zależnych i ustaw wartość false** |

## <a name="performance"></a>Wydajność
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Rozwiązywanie problemów z wydajnością kwerend                            | Jeśli próbujesz rozwiązać określone zapytanie, zacznij od [nauka monitorowania zapytań](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemy z przestrzenią TempDB | Monitorowanie wykorzystania miejsca [w pliku TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Typowe przyczyny wyczerpania miejsca w centrum danych TempDB to:<br>- Za mało zasobów przydzielonych do kwerendy powodując wyciek danych do bazy danych TempDB.  Zobacz [Zarządzanie obciążeniami](resource-classes-for-workload-management.md) <br>- Brakuje statystyk lub nieaktualne powodujące nadmierne przenoszenie danych.  Zobacz [Utrzymywanie statystyk tabeli,](sql-data-warehouse-tables-statistics.md) aby uzyskać szczegółowe informacje na temat tworzenia statystyk<br>- Przestrzeń TempDB jest przydzielana na poziom usługi.  [Skalowanie puli SQL](sql-data-warehouse-manage-compute-overview.md#scaling-compute) do wyższego ustawienia DWU przydziela więcej miejsca tempdb.|
| Niska wydajność zapytań i plany często są wynikiem brakujących statystyk | Najczęstszą przyczyną słabej wydajności jest brak statystyk na tabelach.  Zobacz [Obsługa statystyk tabeli,](sql-data-warehouse-tables-statistics.md) aby uzyskać szczegółowe informacje na temat tworzenia statystyk i powodów, dla których mają one kluczowe znaczenie dla wydajności. |
| Niska współbieżność / kwerendy w kolejce                             | Zrozumienie [zarządzania obciążeniem](resource-classes-for-workload-management.md) jest ważne, aby zrozumieć, jak zrównoważyć alokacji pamięci z współbieżności. |
| Jak zaimplementować najlepsze rozwiązania                              | Najlepszym miejscem, aby rozpocząć, aby dowiedzieć się, jak poprawić wydajność kwerendy jest [Synapse SQL puli najlepszych praktyk](sql-data-warehouse-best-practices.md) artykułu. |
| Jak zwiększyć wydajność dzięki skalowaniu                      | Czasami rozwiązaniem poprawy wydajności jest po prostu dodać więcej mocy obliczeniowej do zapytań przez [skalowanie puli SQL](sql-data-warehouse-manage-compute-overview.md). |
| Niska wydajność zapytań w wyniku niskiej jakości indeksu     | Czasami kwerendy mogą spowolnić z powodu [słabej jakości indeksu magazynu kolumn.](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)  Zobacz ten artykuł, aby uzyskać więcej informacji i jak [odbudować indeksy w celu poprawy jakości segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Zarządzanie systemem
| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nie można wykonać operacji, ponieważ serwer przekracza dozwolony przydział jednostki transakcji bazy danych 45000. | Zmniejsz dwu [bazy](what-is-a-data-warehouse-unit-dwu-cdwu.md) danych, którą próbujesz utworzyć, lub [zażądaj zwiększenia przydziału](sql-data-warehouse-get-started-create-support-ticket.md). |
| Badanie wykorzystania przestrzeni kosmicznej                              | Zobacz [Rozmiary tabel,]( ../../sql-data-warehouse/sql-data-warehouse-tables-overview.md#table-size-queries) aby zrozumieć wykorzystanie miejsca w systemie. |
| Pomoc dotycząca zarządzania tabelami                                    | Zobacz artykuł [omówienie tabeli, aby](sql-data-warehouse-tables-overview.md) uzyskać pomoc dotyczącą zarządzania tabelami.  Ten artykuł zawiera również łącza do bardziej szczegółowych tematów, takich jak [typy danych tabeli,](sql-data-warehouse-tables-data-types.md) [Dystrybucja tabeli,](sql-data-warehouse-tables-distribute.md) [Indeksowanie tabeli,](sql-data-warehouse-tables-index.md) [Partycjonowanie tabeli,](sql-data-warehouse-tables-partition.md) [Utrzymywanie statystyk tabeli](sql-data-warehouse-tables-statistics.md) i [Tabele tymczasowe](sql-data-warehouse-tables-temporary.md). |
| Pasek postępu przezroczystego szyfrowania danych (TDE) nie jest aktualizowany w witrynie Azure portal | Stan TDE można wyświetlić za pomocą [programu PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Różnice w bazie danych SQL
| Problem                                 | Rozwiązanie                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nieobsługiwały funkcje bazy danych SQL     | Zobacz [Nieobsługiwałem funkcji tabeli](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nieobsługiwały typy danych bazy danych SQL   | Zobacz [Nieobsługiwały typy danych](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Ograniczenia usuwania i aktualizacji         | Zobacz [update workarounds](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [DELETE workarounds](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) and [Using CTAS to work around unsupported UPDATE and DELETE syntax](sql-data-warehouse-develop-ctas.md). |
| Instrukcja MERGE nie jest obsługiwana      | Zobacz [obejścia scalania](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Ograniczenia procedury składowanej          | Zobacz [Ograniczenia procedury składowanej,](sql-data-warehouse-develop-stored-procedures.md#limitations) aby zrozumieć niektóre ograniczenia procedur składowanych. |
| Pliki UDF nie obsługują instrukcji SELECT | Jest to aktualne ograniczenie naszych UDF.  Zobacz [TWORZENIE FUNKCJI](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse?view=aps-pdw-2016-au7) dla składni, którą obsługujemy. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej pomocy w znalezieniu rozwiązania problemu, oto kilka innych zasobów, które możesz wypróbować.

* [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse)
* [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
