---
title: Wszystkich tematów dotyczących usługi SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Spis wszystkich tematów dotyczących usługi Azure o nazwie SQL Data Warehouse, która istnieje na http://azure.microsoft.com/documentation/articles/, tytuł i opis.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678427"
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Wszystkie tematy dotyczące usługi Azure SQL Data Warehouse
W tym temacie wymieniono co temat, który bezpośrednio dotyczą **SQL Data Warehouse** usługi platformy Azure. Ta strona sieci Web dla słów kluczowych można wyszukiwać za pomocą **Ctrl + F**, aby znaleźć tematy odsetek bieżącego.

## <a name="new"></a>Nowa
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 1 |[Tworzenie kopii zapasowych SQL Data Warehouse](sql-data-warehouse-backups.md) |Więcej informacji na temat kopii zapasowych wbudowaną bazą danych SQL Data Warehouse, umożliwiające przywracanie Azure SQL Data Warehouse punkt przywracania lub inny region geograficzny. |

## <a name="updated-articles-sql-data-warehouse"></a>Zaktualizowano artykuły, Magazyn danych SQL
Ta sekcja zawiera artykuły, które zostały zaktualizowane, których zainstalowano aktualizację duży lub znaczące. Dla każdego artykułu zaktualizowane zostanie wyświetlony nierównej fragment tekstu dodano markdown. Artykuły zostały zaktualizowane w zakresie dat **2016-08-22** do **2016-10-05**.

| &nbsp; | Artykuł | Zaktualizowany tekst, fragment kodu | Zaktualizowane |
| ---:|:--- |:--- |:--- |
| 2 |[Ładowanie danych z magazynu obiektów blob platformy Azure do usługi SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/ — Śledzenie bajtów i plików wybierz r.command, s.request_id, r.status, count (różne input_name) jako nbr_files, Suma (s.bytes_processed) / 1024/1024 jako gb_processed z sys.dm_pdw_exec_requests r sprzężenia wewnętrznego sys.dm_pdw_dms_external_work s na r.request_id = s.request_id WHERE r. Etykieta = "CTAS: cso obciążenia. DimProduct "lub r. Etykieta = "CTAS: cso obciążenia. FactOnlineSales GROUP BY r.command, s.request_id, r.status ORDER BY nbr_files desc, gb_processed desc; |2016-09-07 |
| 3 |[Przywrócenie magazynu danych SQL](sql-data-warehouse-restore-database-overview.md) |** Można przywrócić hurtowni danych wstrzymania? ** Aby przywrócić magazynu danych, która jest wstrzymana, należy najpierw objąć ponownie do trybu online. Po powrocie do trybu online w magazynie danych masz siedem dni punktów przywracania do wyboru. ** Przywróć do regionu geograficznie nadmiarowego ** korzystania z magazynu geograficznie nadmiarowego można przywracać w magazynie danych do centrum danych sparowanego w innym regionie geograficznym. Magazyn danych został przywrócony z ostatniej kopii zapasowej codziennie. ** Przywrócić osi czasu ** można przywrócić bazę danych do dowolnego punktu przywracania w ciągu ostatnich siedmiu dni. Migawki Uruchom co cztery do ośmiu godzin i są dostępne na siedem dni. Gdy migawka jest starsza niż siedmiu dni, wygaśnie i jego punktu przywracania nie jest już dostępny. ** Przywrócić kosztów ** którego jest on rozliczany opłata magazynu dla magazynu danych przywróconych z szybkością Azure Premium Storage. Jeśli magazynu przywróconych danych zostanie wstrzymana, są naliczane opłaty za magazyn szybkością Azure Premium Storage. Zaletą wstrzymanie jest się, że nie jesteś opłat |2016-09-29 |

## <a name="get-started"></a>Rozpoczęcie pracy
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 4 |[Authentication to Azure SQL Data Warehouse](sql-data-warehouse-authentication.md) (Uwierzytelnianie w usłudze Azure SQL Data Warehouse) |Azure Active Directory (AAD) i SQL Server uwierzytelniania usługi Azure SQL Data Warehouse. |
| 5 |[Najlepsze praktyki dotyczące korzystania z usługi Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) |Zalecenia i najlepsze praktyki, których stosowanie zaleca się podczas tworzenia rozwiązań dla usługi Azure SQL Data Warehouse. Dzięki nim łatwiej jest odnieść sukces. |
| 6 |[Sterowniki dla magazynu danych Azure SQL](sql-data-warehouse-connection-strings.md) |Parametry połączenia i sterowniki dla magazynu danych SQL |
| 7 |[Połączyć się z magazynem danych Azure SQL](sql-data-warehouse-connect-overview.md) |Jak znaleźć nazwę serwera i parametry połączenia dla usługi Azure SQL Data Warehouse |
| 8 |[Analizowanie danych przy użyciu usługi Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |Używając usługi Azure Machine Learning, można utworzyć predykcyjny model uczenia maszynowego korzystający z danych przechowywanych w usłudze Azure SQL Data Warehouse. |
| 9 |[Zapytanie usługi Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |Tworzenie zapytań względem usługi Azure SQL Data Warehouse przy użyciu narzędzia wiersza polecenia sqlcmd. |
| 10 |[Utwórz bazę danych SQL Data Warehouse przy użyciu języka Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) |Dowiedz się, jak utworzyć bazę danych w usłudze Azure SQL Data Warehouse przy użyciu języka TSQL |
| 11 |[Tworzenie biletu pomocy technicznej dla usługi SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) |Tworzenie biletu pomocy technicznej w usłudze Azure SQL Data Warehouse. |
| 12 |[Ładowanie danych przy użyciu fabryki danych Azure](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |Dowiedz się, jak załadować dane przy użyciu usługi Azure Data Factory |
| 13 |[Ładowanie danych przy użyciu programu PolyBase w usłudze SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) |Dowiedz się, co to jest aparat PolyBase i jak z niego korzystać w scenariuszach dotyczących magazynów danych. |
| 14 |[Utwórz magazyn danych Azure SQL](sql-data-warehouse-get-started-provision.md) |Dowiedz się, jak utworzyć bazę danych w usłudze Azure SQL Data Warehouse w portalu Azure |
| 15 |[Tworzenie usługi SQL Data Warehouse przy użyciu programu PowerShell](sql-data-warehouse-get-started-provision-powershell.md) |Tworzenie bazy danych w usłudze SQL Data Warehouse za pomocą programu PowerShell |
| 16 |[Wizualizacja danych przy użyciu usługi Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) |Wizualizacja danych usługi SQL Data Warehouse przy użyciu usługi Power BI |
| 17 |[Tworzenie zapytań względem usługi Azure SQL Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |Tworzenie zapytań względem usługi SQL Data Warehouse przy użyciu programu Visual Studio. |

## <a name="develop"></a>Programowanie
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 18 |[Optymalizacja transakcji dla magazynu danych SQL](sql-data-warehouse-develop-best-practices-transactions.md) |Najlepszych rozwiązań o pisaniu aktualizacje wydajne transakcji w magazynie danych SQL Azure |
| 19 |[Zarządzanie współbieżności i obciążenia w usłudze SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) |Zrozumienie zarządzania współbieżności i obciążenia w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 20 |[Utwórz tabelę jako Select (CTAS) w magazynie danych SQL](sql-data-warehouse-develop-ctas.md) |Porady dotyczące programowania z Utwórz tabelę jako instrukcji select (CTAS) w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 21 |[Dynamiczne SQL w usłudze SQL Data Warehouse](sql-data-warehouse-develop-dynamic-sql.md) |Porady dotyczące używania dynamicznych SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 22 |[Grupuj według opcje w usłudze SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) |Wskazówki dotyczące implementowania grupy Opcje w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 23 |[Korzystanie z etykiet do dokumentu zapytania w usłudze SQL Data Warehouse](sql-data-warehouse-develop-label.md) |Porady dotyczące korzystania z etykiet do dokumentu zapytania w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 24 |[W przypadku usługi SQL Data Warehouse pętle](sql-data-warehouse-develop-loops.md) |Porady dotyczące języka Transact-SQL pętli i zastąpienie kursory w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 25 |[Procedury składowane w usłudze SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) |Wskazówki dotyczące implementowania procedur przechowywanych w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 26 |[Transakcje w usłudze SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) |Wskazówki dotyczące implementowania transakcji w magazynie danych SQL Azure związane z opracowywaniem rozwiązań. |
| 27 |[Schematy zdefiniowane przez użytkownika w usłudze SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) |Porady dotyczące używania schematów języka Transact-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 28 |[Przypisz zmiennych w usłudze SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) |Porady dotyczące przypisywania zmienne języka Transact-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 29 |[Widoki w usłudze SQL Data Warehouse](sql-data-warehouse-develop-views.md) |Porady dotyczące korzystania z widoków języka Transact-SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 30 |[Decyzje dotyczące projektu i technik programowania dla usługi SQL Data Warehouse](sql-data-warehouse-overview-develop.md) |Pojęcia dotyczące programowania, decyzji projektowych i zalecenia dotyczące technik programowania dla usługi SQL Data Warehouse. |

## <a name="manage"></a>Zarządzanie
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 31 |[Zarządzanie moc obliczeniową w usłudze Azure SQL Data Warehouse (omówienie)](sql-data-warehouse-manage-compute-overview.md) |Wydajność skalowania możliwości w usłudze Azure SQL Data Warehouse. Skalowanie w poziomie przez dostosowanie wartości dwu lub wstrzymywać i wznawiać zasobów obliczeniowych w celu ograniczenia kosztów. |
| 32 |[Zarządzanie moc obliczeniową w usłudze Azure SQL Data Warehouse (Azure portal)](sql-data-warehouse-manage-compute-portal.md) |Azure zadania portalu do zarządzania mocy obliczeniowej. Zasoby obliczeniowe skali przez dostosowanie wartości dwu. Wstrzymać lub wznowić zasobów obliczeniowych w celu ograniczenia kosztów. |
| 33 |[Zarządzanie moc obliczeniową w usłudze Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |Zadania programu PowerShell do zarządzania mocy obliczeniowej. Zasoby obliczeniowe skali przez dostosowanie wartości dwu. Wstrzymać lub wznowić zasobów obliczeniowych w celu ograniczenia kosztów. |
| 34 |[Zarządzanie energią obliczeniowych w magazynie danych SQL Azure (REST)](sql-data-warehouse-manage-compute-rest-api.md) |Zadania programu PowerShell do zarządzania mocy obliczeniowej. Zasoby obliczeniowe skali przez dostosowanie wartości dwu. Wstrzymać lub wznowić zasobów obliczeniowych w celu ograniczenia kosztów. |
| 35 |[Zarządzanie moc obliczeniową w usłudze Azure SQL Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |Na wydajność skalowania w poziomie przez dostosowanie wartości dwu zadań Transact-SQL (T-SQL). Zmniejszyć koszty przez skalowanie w trakcie godziny poza szczytem. |
| 36 |[Monitorowanie obciążenia przy użyciu widoków DMV](sql-data-warehouse-manage-monitor.md) |Dowiedz się, jak monitorować obciążenie przy użyciu widoków DMV. |
| 37 |[Zarządzanie bazami danych w magazynie danych SQL Azure](sql-data-warehouse-overview-manage.md) |Omówienie zarządzania bazami danych magazynu danych SQL. Obejmuje narzędzia do zarządzania, jednostek dwu i wydajność skalowania w poziomie, rozwiązywanie problemów z wydajność zapytań, ustanawiania zasad zabezpieczeń dobrej i przywracanie bazy danych z uszkodzenie danych lub regionalnej awarii. |
| 38 |[Monitor kwerend użytkowników w usłudze Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) |Omówienie zagadnienia dotyczące najlepszych rozwiązań i zadania monitorowania kwerend użytkowników w usłudze Azure SQL Data Warehouse |
| 39 |[Przywrócenie magazynu danych SQL](sql-data-warehouse-restore-database-overview.md) |Omówienie opcji przywracania bazy danych do przywrócenia bazy danych w magazynie danych SQL Azure. |
| 40 |[Przywróć magazyn danych Azure SQL (Portal)](sql-data-warehouse-restore-database-portal.md) |Azure portalu zadania przywracania usługi Azure SQL Data Warehouse. |
| 41 |[Przywróć magazyn danych Azure SQL (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |Zadania programu PowerShell dla usługi Azure SQL Data Warehouse przywracania. |
| 42 |[Przywróć magazyn danych Azure SQL (interfejsu API REST)](sql-data-warehouse-restore-database-rest-api.md) |Interfejs API REST zadania przywracania usługi Azure SQL Data Warehouse. |

## <a name="tables-and-indexes"></a>Tabele i indeksy
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 43 |[Typy danych w przypadku tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) |Wprowadzenie do typów danych w przypadku tabel Azure SQL Data Warehouse. |
| 44 |[Dystrybucja tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) |Wprowadzenie do korzystania z dystrybucji tabel w usłudze Azure SQL Data Warehouse. |
| 45 |[Indeksowanie tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-index.md) |Wprowadzenie do korzystania z tabeli indeksowanie w usłudze Azure SQL Data Warehouse. |
| 46 |[Przegląd tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-overview.md) |Wprowadzenie do tabel magazynu danych SQL Azure. |
| 47 |[Partycjonowanie tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-partition.md) |Wprowadzenie do partycjonowania tabeli w magazynie danych SQL Azure. |
| 48 |[Zarządzanie statystyk dotyczących tabel w usłudze SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) |Wprowadzenie do korzystania z statystyk dotyczących tabel w usłudze Azure SQL Data Warehouse. |
| 49 |[Tabele tymczasowe w usłudze SQL Data Warehouse](sql-data-warehouse-tables-temporary.md) |Wprowadzenie do tabel tymczasowych w usłudze Azure SQL Data Warehouse. |

## <a name="integrate"></a>Integracja
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 50 |[Użyj fabryki danych Azure z usługą Magazyn danych SQL](sql-data-warehouse-integrate-azure-data-factory.md) |Porady dotyczące przy użyciu fabryki danych Azure (ADF) w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 51 |[Użyj usługi Azure Machine Learning z usługą Magazyn danych SQL](sql-data-warehouse-integrate-azure-machine-learning.md) |Samouczek korzystania z usługi Azure Machine Learning z usługą Azure SQL Data Warehouse w celu opracowywania rozwiązań. |
| 52 |[Użyj usługi Azure Stream Analytics z usługą Magazyn danych SQL](sql-data-warehouse-integrate-azure-stream-analytics.md) |Porady dotyczące korzystania z usługi Azure Stream Analytics z usługą Magazyn danych SQL Azure związane z opracowywaniem rozwiązań. |
| 53 |[Użyj usługi Power BI z usługą Magazyn danych SQL](sql-data-warehouse-integrate-power-bi.md) |Porady dotyczące korzystania z usługi Power BI z usługą Magazyn danych SQL Azure związane z opracowywaniem rozwiązań. |
| 54 |[Korzystać z innych usług z usługą Magazyn danych SQL](sql-data-warehouse-overview-integrate.md) |Narzędzia i partnerom rozwiązania, które integrują się z usługą Magazyn danych SQL. |

## <a name="load"></a>Ładowanie
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 55 |[Ładowanie danych z magazynu obiektów blob platformy Azure do usługi Azure SQL Data Warehouse (fabryka danych Azure)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |Dowiedz się, jak załadować dane przy użyciu usługi Azure Data Factory |
| 56 |[Ładowanie danych z magazynu obiektów blob platformy Azure do usługi SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |Dowiedz się, jak używać programu PolyBase do ładowania danych z magazynu obiektów blob platformy Azure do usługi SQL Data Warehouse. Ładowanie kilku tabel z danych publicznej do schematu magazynu danych sprzedaży detalicznej Contoso. |
| 57 |[Ładowanie danych z programu SQL Server do usługi Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |Eksportowanie danych z programu SQL Server do plików prostych przy użyciu programu bcpw, importowanie danych do usługi Azure Blob Storage przy użyciu programu AZCopy oraz pozyskiwanie danych do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. |
| 58 |[Ładowanie danych z programu SQL Server do usługi Azure SQL Data Warehouse (pliki proste)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |Dane niewielkich rozmiarów można kopiować, używając programu bcp do eksportowania danych z programu SQL Server do plików prostych i importowania danych bezpośrednio do usługi Azure SQL Data Warehouse. |
| 59 |[Ładowanie danych z programu SQL Server do magazynu danych SQL Azure (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |Pokazuje, jak utworzyć pakiet SQL Server Integration Services (SSIS) do przenoszenia danych z różnych źródeł danych do usługi SQL Data Warehouse. |
| 60 |[Ładowanie danych przy użyciu programu PolyBase w usłudze SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) |Eksportowanie danych z programu SQL Server do plików prostych przy użyciu programu bcpw, importowanie danych do usługi Azure Blob Storage przy użyciu programu AZCopy oraz pozyskiwanie danych do usługi Azure SQL Data Warehouse przy użyciu programu PolyBase. |
| 61 |[Przewodnik dla przy użyciu programu PolyBase w usłudze SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) |Wskazówki i zalecenia dotyczące przy użyciu programu PolyBase w scenariuszach SQL Data Warehouse. |
| 62 |[Ładowanie przykładowych danych do usługi SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) |Ładowanie przykładowych danych do usługi SQL Data Warehouse |
| 63 |[Ładowanie danych za pomocą narzędzia BCP](sql-data-warehouse-load-with-bcp.md) |Dowiedz się, czym jest program bcp i jak z niego korzystać w scenariuszach dotyczących magazynów danych. |
| 64 |[Ładowanie danych do usługi Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) |Dowiedz się typowe scenariusze dotyczące danych ładowania do usługi SQL Data Warehouse. Obejmują one przy użyciu programu PolyBase, magazynu obiektów blob platformy Azure, plików prostych i wysyłania dysku. Można także użyć narzędzi innych firm. |

## <a name="migrate"></a>Migrate (Migracja)
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 65 |[Migrowanie kodu SQL do SQL Data Warehouse](sql-data-warehouse-migrate-code.md) |Wskazówki dotyczące migrowania kodu SQL Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 66 |[Migrowanie danych](sql-data-warehouse-migrate-data.md) |Wskazówki dotyczące migrowania danych Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 67 |[Narzędzie migracji magazynu danych (wersja zapoznawcza)](sql-data-warehouse-migrate-migration-utility.md) |Przeprowadź migrację do usługi SQL Data Warehouse. |
| 68 |[Migrowanie schemat do magazynu danych SQL](sql-data-warehouse-migrate-schema.md) |Wskazówki dotyczące migrowania schemat Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |
| 69 |[Migrowanie rozwiązania do usługi SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) |Wskazówki dotyczące migracji za rozwiązania do platformy Azure SQL Data Warehouse. |

## <a name="partners"></a>Partnerzy
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 70 |[Partnerów analizy biznesowej usługi SQL Data Warehouse](sql-data-warehouse-partner-business-intelligence.md) |Lista partnerów analizy biznesowej innych firm z rozwiązaniami, które obsługują usługi SQL Data Warehouse. |
| 71 |[Partnerzy Integracja danych magazynu danych SQL](sql-data-warehouse-partner-data-integration.md) |Listę innych firm partnerom rozwiązań integracji danych, które obsługują usługi Azure SQL Data Warehouse. |
| 72 |[Partnerzy zarządzania danych magazynu danych SQL](sql-data-warehouse-partner-data-management.md) |Lista partnerów zarządzania innych producentów danych z rozwiązaniami, które obsługują usługi SQL Data Warehouse. |

## <a name="reference"></a>Informacje ogólne
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 73 |[Tematy odwołań dla usługi SQL Data Warehouse](sql-data-warehouse-overview-reference.md) |Odwołanie łączy zawartości dla usługi SQL Data Warehouse. |
| 74 |[Polecenia cmdlet programu PowerShell i interfejsów API REST dla usługi SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) |Znajdź top poleceń cmdlet programu PowerShell dla usługi Azure SQL Data Warehouse, łącznie ze sposobem wstrzymywanie i wznawianie bazy danych. |
| 75 |[Elementy języka](sql-data-warehouse-reference-tsql-language-elements.md) |Lista linków do zawartości referencyjnej dla elementy języka Transact-SQL, które są używane dla usługi SQL Data Warehouse. |
| 76 |[Tematy dotyczące języka Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) |Łącza do zawartości referencyjnej dla tematy języka Transact-SQL używane przez usługi SQL Data Warehouse. |
| 77 |[Widoki systemowe](sql-data-warehouse-reference-tsql-system-views.md) |Łącza do systemu widokach zawartości dla usługi SQL Data Warehouse. |

## <a name="security"></a>Bezpieczeństwo
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 78 |[Obsługa klientów niższych poziomów usługi SQL Data Warehouse — inspekcji i dynamicznego maskowania danych](sql-data-warehouse-auditing-downlevel-clients.md) |Więcej informacji na temat obsługi klientów niższych poziomów usługi SQL Data Warehouse danych inspekcji |
| 79 |[Inspekcja w magazynie danych Azure SQL](sql-data-warehouse-auditing-overview.md) |Wprowadzenie do inspekcji w usłudze Azure SQL Data Warehouse |
| 80 |[Rozpoczynanie pracy z przezroczystym danych szyfrowania (funkcji TDE) w magazynie danych SQL](sql-data-warehouse-encryption-tde.md) |Przezroczystego szyfrowania danych (funkcji TDE) w magazynie danych SQL |
| 81 |[Rozpoczynanie pracy z przezroczystym danych szyfrowania (funkcji TDE)](sql-data-warehouse-encryption-tde-tsql.md) |Przezroczystego szyfrowania danych (funkcji TDE) w magazynie danych SQL (T-SQL) |
| 82 |[Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) |Porady dotyczące zabezpieczenia bazy danych w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań. |

## <a name="miscellaneous"></a>Różne postanowienia
| &nbsp; | Stanowisko | Opis |
| ---:|:--- |:--- |
| 83 |[Instalacja programu Visual Studio i narzędzi SSDT dla usługi SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) |Instalacja programu Visual Studio i narzędzi SQL Server Data Tools (SSDT) dla usługi Azure SQL Data Warehouse |
| 84 |[Migracja do szczegóły magazynu w warstwie Premium](sql-data-warehouse-migrate-to-premium-storage.md) |Instrukcje dotyczące migrowania istniejącego magazynu danych SQL magazyn w warstwie premium |
| 85 |[Rozpoczynanie pracy z wykrywanie zagrożeń](sql-data-warehouse-security-threat-detection.md) |Jak rozpocząć pracę z wykrywanie zagrożeń |
| 86 |[Limity pojemności magazynu danych SQL](sql-data-warehouse-service-capacity-limits.md) |Maksymalne wartości dla połączeń, baz danych, tabel i zapytań dotyczących usługi SQL Data Warehouse. |
| 87 |[Rozwiązywanie problemów z usługą Magazyn danych Azure SQL](sql-data-warehouse-troubleshoot.md) |Rozwiązywanie problemów z usługą Magazyn danych Azure SQL. |

