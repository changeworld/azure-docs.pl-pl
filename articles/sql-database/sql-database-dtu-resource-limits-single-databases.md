---
title: Zasób oparty na jednostkach DTU bazy danych SQL platformy Azure ogranicza pojedynczych baz danych | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis niektórych typowych limitów zasobów na podstawie jednostek DTU dla pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: effb09cfc68961065ad0b4e4be52255bcd1fe4e0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414171"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU 

Ten artykuł zawiera limity zasobów szczegółowe dla pul elastycznych usługi Azure SQL Database przy użyciu modelu zakupu opartego na jednostkach DTU.

Aby uzyskać oparty na jednostkach DTU zakupu model zasobu limity dla pul elastycznych, zobacz [limity zasobów oparty na jednostkach DTU - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). Limity zasobów opartych na rdzeniach wirtualnych, zobacz [limity zasobów opartych na rdzeniach wirtualnych - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Pojedyncza baza danych: magazyn o rozmiarze i poziomy wydajności

Dla pojedynczych baz danych w poniższych tabelach przedstawiono zasoby dostępne dla pojedynczej bazy danych w poszczególnych usług warstwy i poziomu wydajności. Można ustawić warstwę usługi, poziom wydajności i ilość miejsca w magazynie dla pojedynczej bazy danych za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [wiersza polecenia platformy Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases), lub [Interfejsu API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Warstwa Podstawowa usług
| **Poziom wydajności** | **Podstawowa** |
| :--- | --: |
| Maksymalna liczba jednostek DTU | 5 |
| Dołączony magazyn (GB) | 2 |
| Maksymalna liczba opcje magazynu (GB) | 2 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) |ND |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) | 30 |
| Maksymalna liczba współbieżnych sesji | 300 |
|||

### <a name="standard-service-tier"></a>Warstwa Standardowa usług
| **Poziom wydajności** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 10 | 20 | 50 | 100 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcje magazynu (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych sesji |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Warstwy usług w warstwie standardowa (ciąg dalszy)
| **Poziom wydajności** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 200 | 400 | 800 | 1600 | 3000 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcje magazynu (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |ND |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych sesji |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Warstwa Premium usług 
| **Poziom wydajności** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dołączony magazyn (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maksymalna liczba opcje magazynu (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maksymalna liczba współbieżnych sesji | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępna we wszystkich regionach z wyjątkiem następujących: zachodnio-środkowe stany USA, Chiny wschodnie, USDoDCentral, Niemcy środkowe, Południowy Zachód Gov USDoDEast, USA, Niemcy północno-wschodnie, USGovIowa, Chin północnych. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Pojedyncza baza danych: zmiana rozmiaru magazynu

- Cena jednostki DTU dla pojedynczej bazy danych obejmuje określoną ilość pamięci masowej bez ponoszenia dodatkowych kosztów. Dodatkowego magazynu ponad uwzględnioną kwotę można zaaprowizować za dodatkową opłatą maksymalnie limit maksymalnego rozmiaru, w przyrostach o rozmiarze 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB ponad 1 TB. Magazyn w pakiecie kwoty i limity maksymalnego rozmiaru, zobacz [pojedynczej bazy danych: magazyn o rozmiarze i poziomy wydajności](#single-database-storage-sizes-and-performance-levels).
- Dodatkowy magazyn dla pojedynczej bazy danych mogą być udostępniane przez odpowiednie zwiększenie jego maksymalnego rozmiaru za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [platformy Azure Interfejs wiersza polecenia](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update).
- Cena dodatkowego magazynu dla pojedynczej bazy danych jest mnożony przez cena jednostkowa dodatkowego magazynu w warstwie usług wielkość dodatkowego magazynu. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Pojedyncza baza danych: zmiana liczby jednostek Dtu

Po początkowym wybraniu warstwy usług, poziom wydajności i ilość miejsca w magazynie, użytkownik może pojedynczej bazy danych w górę lub dół dynamicznie skalować na podstawie rzeczywistej wydajności przy użyciu [witryny Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update). 

W poniższym wideo pokazano, dynamicznie zmieniające się warstwy wydajności, aby zwiększyć dostępna liczba jednostek Dtu dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Zmienienie warstwy usług i/lub poziomu wydajności bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowym poziomie wydajności, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Długość czasu na przejście zmienia się, ale jest mniej niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji w locie w chwili połączenia są wyłączone, długość czasu na przejście może być dłuższy. 

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład 250 GB bazy danych, zmiana do, z lub do warstwy standardowa, należy wykonać w ciągu 6 godzin. Dla bazy danych taki sam rozmiar, który zmiana poziomów wydajności w ramach warstwy Premium skalowanie w górę powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować działania w toku, zobacz: [zarządzania operacjami usługi przy użyciu interfejsu API REST programu SQL](/rest/api/sql/Operations/List), [zarządzania operacjami usługi przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwie Polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Jeśli uaktualniasz na wyższy poziom wydajności lub Warstwa usługi maksymalnego rozmiaru bazy danych nie zwiększa chyba że jawnie określisz o większym rozmiarze (maxsize).
* W przypadku obniżania, miejsca w bazie danych, używane musi być mniejszy niż maksymalny dozwolony rozmiar docelowej usługi warstwy i poziomu wydajności. 
* Przed obniżeniem z **Premium** do **standardowa** warstwy, koszty dodatkowego magazynu ma zastosowanie w przypadku, gdy (1) maksymalny rozmiar bazy danych jest obsługiwana w poziom wydajności miejsca docelowego i (2) maksymalny rozmiar przekracza wielkość magazynu w pakiecie poziom wydajności miejsca docelowego. Na przykład, jeśli P1 baza danych o maksymalnej długości wynoszący 500 GB, jest downsized do S3, następnie magazyn dodatkowy koszt dotyczy ponieważ S3 obsługuje maksymalny rozmiar wynoszący 500 GB, a jego dostępnej ilości magazynu tylko 250 GB. Więc wielkość dodatkowego magazynu to 500 GB-250 GB = 250 GB. Cennik dodatkowego magazynu, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywistą ilość miejsca jest mniejsza niż wielkość magazynu w pakiecie, następnie to dodatkowych kosztów można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględnioną kwotę. 
* Podczas uaktualniania bazy danych o [geografickou replikaci](sql-database-geo-replication-portal.md) włączone, należy uaktualnić pomocnicze bazy danych do odpowiedniej warstwy wydajności przed uaktualnieniem podstawowej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innego, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
* Przed obniżeniem bazę danych przy użyciu [geografickou replikaci](sql-database-geo-replication-portal.md) włączone, obniżyć jej podstawowych baz danych do odpowiedniej warstwy wydajności przed przejściem do pomocniczej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Przed obniżeniem do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
* Oferowane usługi przywracania różnią się w zależności do warstwy usług. Jeśli są powrót do subskrypcji **podstawowe** warstwy jest krótszym okresie przechowywania kopii zapasowych — Zobacz [kopie zapasowe bazy danych SQL Azure](sql-database-automated-backups.md).
* Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Pojedyncza baza danych: ograniczenia P11 i P15 podczas maksymalny rozmiar większy niż 1 TB

Maksymalny rozmiar większy niż 1 TB dla bazy danych P11 i P15 jest obsługiwana w następujących regionach: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, środkowe stany USA, Francja środkowa, Niemcy środkowe, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa Północna środkowe stany USA, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo, stany USA 2, zachodnie stany USA, administracja USA — Wirginia i Europa Zachodnia. Następujące istotne zagadnienia i ograniczenia dotyczą P11 i P15 baz danych o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli wybierzesz maksymalny rozmiar większy niż 1 TB, podczas tworzenia bazy danych (za pomocą wartość 4 TB lub 4096 GB), polecenia create kończy się niepowodzeniem z powodu błędu w przypadku bazy danych są aprowizowane w nieobsługiwany region.
- W przypadku istniejących P11 i P15 baz danych znajdujących się w jednym z obsługiwanych regionów, możesz zwiększyć maksymalny rozmiar magazynu na ponad 1 TB z przyrostami 256 GB do 4 TB. Aby zobaczyć, czy większy rozmiar jest obsługiwana w Twoim regionie, użyj [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkcji lub Sprawdź rozmiar bazy danych w witrynie Azure portal. Uaktualnianie istniejącej P11 lub P15 bazy danych można wykonać tylko przez głównego identyfikatora logowania poziomu serwera lub członkowie roli bazy danych dbmanager. 
- Jeśli operacja uaktualniania jest wykonywane w obsługiwanym regionie natychmiast zaktualizować konfigurację. Baza danych pozostaje w trybie online podczas procesu uaktualniania. Jednak nie możesz wykorzystywać cała kwota magazynu ponad 1 TB miejsca do magazynowania do czasu rzeczywistego wynoszącego zostali uaktualnieni do wersji nowy rozmiar maksymalny. Wymagany czas zależy od rozmiaru bazy danych, trwa uaktualnianie. 
- Podczas tworzenia lub aktualizowania P11 lub P15 bazy danych, można wybrać tylko między maksymalny rozmiar 1 TB pojemności i 4 TB w przyrostach wynoszących 256 GB. Podczas tworzenia P11/P15, domyślna opcja magazynu o rozmiarze 1 TB jest wstępnie wybrane. W przypadku baz danych znajdujących się w jednym z obsługiwanych regionów można zwiększyć maksymalną magazynu do maksymalnie 4 TB dla nowej lub istniejącej pojedynczej bazy danych. Inne regiony nie można zwiększyć maksymalny rozmiar, powyżej 1 TB. Cena nie ulega zmianie, gdy wybierzesz 4 TB uwzględnionego magazynu.
- Jeśli maksymalny rozmiar bazy danych ma wartość większą niż 1 TB, następnie nie można zmienić do 1 TB, nawet jeśli rzeczywisty magazyn używany znajduje się poniżej 1 TB. W związku z tym, nie można obniżyć P11 lub P15 o maksymalnym rozmiarze większym niż 1 TB na wartość 1 TB P11, 1 TB P15 lub obniżenia wydajności warstwy, takich jak P1 P6). To ograniczenie dotyczy także przywracania i kopii scenariusze, w tym punkcie w czasie, przywracanie geograficzne długo-długoterminowa kopia zapasowa zachowywania i kopii bazy danych. Po skonfigurowaniu bazy danych o maksymalnym rozmiarze większym niż 1 TB, wszystkie operacje przywracania tej bazy danych muszą być uruchamiane w P11/P15 o maksymalnym rozmiarze większym niż 1 TB.
- Aktywna replikacja geograficzna scenariuszach:
   - Definiowanie relacji replikacji geograficznej: Jeśli podstawowa baza danych jest P11 lub P15, secondary(ies) również musi być P11 lub P15; niższych warstwach wydajności zostały odrzucone jako pomocnicze bazy danych, ponieważ nie może obsługiwać więcej niż 1 TB.
   - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: Zmiana maksymalnego rozmiaru na więcej niż 1 TB w podstawowej bazie danych wyzwala tę samą zmianę w pomocniczej bazie danych. Obu uaktualnień musi zakończyć się powodzeniem, zmiany na serwerze podstawowym zaczęły obowiązywać. Region dla opcji więcej niż 1 TB ograniczenia. Jeśli pomocniczy znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie jest uaktualniany.
- Za pomocą usługi Import/Export dla obciążenia baz danych P11/P15, za pomocą więcej niż 1 TB nie jest obsługiwana. Użyj SqlPackage.exe do [zaimportować](sql-database-import.md) i [wyeksportować](sql-database-export.md) danych.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [— często zadawane pytania dla bazy danych SQL](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacji na temat jednostek Dtu i Edtu, zobacz [jednostek Dtu i Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Aby uzyskać informacje na temat limitów rozmiarów bazy danych tempdb, zobacz https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
