---
title: Zasób oparty na jednostkach DTU bazy danych SQL platformy Azure ogranicza pojedynczych baz danych | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis niektórych typowych limitów zasobów na podstawie jednostek DTU dla pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: standalone-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: ef16b0ebf41225ed820e7289132d0f556226a969
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965379"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU

Ten artykuł zawiera limity zasobów szczegółowe dla pojedynczych baz danych Azure SQL Database przy użyciu modelu zakupu opartego na jednostkach DTU.

Aby uzyskać oparty na jednostkach DTU zakupu model zasobu limity dla pul elastycznych, zobacz [limity zasobów oparty na jednostkach DTU - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). Limity zasobów opartych na rdzeniach wirtualnych, zobacz [limity zasobów opartych na rdzeniach wirtualnych - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). Aby uzyskać więcej informacji na temat różnych modeli zakupu zobacz [zakupu warstwy usługi i modele](sql-database-service-tiers.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Pojedyncza baza danych: Magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych

W poniższych tabelach Pokaż zasoby dostępne dla pojedynczej bazy danych w poszczególnych warstwach usług i obliczenia rozmiaru. Można ustawić warstwę usługi, rozmiar obliczeń i ilość miejsca w magazynie dla pojedynczej bazy danych za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [języka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Interfejs wiersza polecenia Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), lub [interfejsu API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

### <a name="basic-service-tier"></a>Warstwa Podstawowa usług

| **Obliczenia rozmiaru** | **Podstawowa** |
| :--- | --: |
| Maksymalna liczba jednostek DTU | 5 |
| Dołączony magazyn (GB) | 2 |
| Maksymalna liczba opcje magazynu (GB) | 2 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) |ND |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) | 30 |
| Maksymalna liczba współbieżnych sesji | 300 |
|||

### <a name="standard-service-tier"></a>Warstwa Standardowa usług

| **Obliczenia rozmiaru** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maksymalna liczba jednostek DTU | 10 | 20 | 50 | 100 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcje magazynu (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych sesji |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Warstwy usług w warstwie standardowa (ciąg dalszy)

| **Obliczenia rozmiaru** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 200 | 400 | 800 | 1600 | 3000 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcje magazynu (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |ND |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych sesji |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Warstwa Premium usług

| **Obliczenia rozmiaru** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dołączony magazyn (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maksymalna liczba opcje magazynu (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maksymalna liczba współbieżnych sesji | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach poza następującymi: Chiny wschodnie, Chiny Północne, Niemcy środkowe, Niemcy północno-wschodnie, Północne Zjednoczone Królestwo, południowe Zjednoczone Królestwo, US dod — środkowe, dod — wschodnie stany USA, środkowe dla instytucji rządowych USA i zachodnie środkowe stany USA.  W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Pojedyncza baza danych: Zmień rozmiar magazynu

- Cena jednostki DTU dla pojedynczej bazy danych obejmuje określoną ilość pamięci masowej bez ponoszenia dodatkowych kosztów. Dodatkowego magazynu ponad uwzględnioną kwotę można zaaprowizować za dodatkową opłatą maksymalnie limit maksymalnego rozmiaru, w przyrostach o rozmiarze 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB ponad 1 TB. Magazyn w pakiecie kwoty i limity maksymalnego rozmiaru, zobacz [pojedynczej bazy danych: Magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych](#single-database-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla pojedynczej bazy danych mogą być udostępniane przez odpowiednie zwiększenie jego maksymalnego rozmiaru za pomocą [witryny Azure portal](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-storage-size), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [platformy Azure Interfejs wiersza polecenia](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena dodatkowego magazynu dla pojedynczej bazy danych jest mnożony przez cena jednostkowa dodatkowego magazynu w warstwie usług wielkość dodatkowego magazynu. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Pojedyncza baza danych: Zmiana liczby jednostek Dtu

Po początkowym wybraniu warstwy usług, rozmiar obliczeń i ilość miejsca w magazynie, użytkownik może pojedynczej bazy danych w górę lub dół dynamicznie skalować na podstawie rzeczywistej wydajności przy użyciu [witryny Azure portal](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [ Program PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

Wideo pokazano, dynamicznie zmieniające się usługa warstwy i obliczenia rozmiaru w celu zwiększenia dostępna liczba jednostek Dtu dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Zmiana usługi warstwy i/lub obliczyć rozmiar bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowy rozmiar obliczeń, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Długość czasu na przejście zmienia się, ale jest mniej niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji w locie w chwili połączenia są wyłączone, długość czasu na przejście może być dłuższy.

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład 250 GB bazy danych, zmiana do, z lub do warstwy standardowa, należy wykonać w ciągu 6 godzin. Dla bazy danych taki sam rozmiar, zmiana rozmiarów wystąpień obliczeniowych w ramach warstwy Premium skalowanie w górę powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować działania w toku, zobacz: [Zarządzanie operacjami za pomocą interfejsu API REST programu SQL](https://docs.microsoft.com/rest/api/sql/databaseoperations/listbydatabase
), [zarządzania operacjami usługi przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwa polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- W przypadku uaktualniania do wersji na wyższą warstwę usługi lub obliczenia rozmiaru, maksymalnego rozmiaru bazy danych zwiększa, chyba że jawnie określisz o większym rozmiarze (maxsize).
- Na starszą wersję bazy danych, miejsca w bazie danych, używane musi być mniejszy niż maksymalny dozwolony rozmiar docelowej warstwy usług i rozmiaru obliczeń.
- Przed obniżeniem z **Premium** do **standardowa** warstwy, koszty dodatkowego magazynu ma zastosowanie w przypadku, gdy (1) maksymalny rozmiar bazy danych jest obsługiwana w rozmiar docelowej obliczeń i (2) maksymalny rozmiar przekracza dołączonej wielkość magazynu w celu obliczenia rozmiaru. Na przykład, jeśli P1 baza danych o maksymalnej długości wynoszący 500 GB, jest downsized do S3, następnie magazyn dodatkowy koszt dotyczy ponieważ S3 obsługuje maksymalny rozmiar wynoszący 500 GB, a jego dostępnej ilości magazynu tylko 250 GB. Więc wielkość dodatkowego magazynu to 500 GB-250 GB = 250 GB. Cennik dodatkowego magazynu, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywistą ilość miejsca jest mniejsza niż wielkość magazynu w pakiecie, następnie to dodatkowych kosztów można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględnioną kwotę.
- Podczas uaktualniania bazy danych o [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, Uaktualnij pomocnicze bazy danych do żądanej usługi i obliczenia rozmiaru przed uaktualnieniem podstawowej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innego, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
- Przed obniżeniem bazę danych przy użyciu [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, obniżyć jej podstawowych baz danych do warstwy usług z żądaną i obliczenia rozmiaru przed przejściem do pomocniczej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Przed obniżeniem do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
- Oferowane usługi przywracania różnią się w zależności do warstwy usług. Jeśli to powrót do subskrypcji **podstawowe** warstwy, jest krótszym okresie przechowywania kopii zapasowych. Zobacz [kopie zapasowe bazy danych Azure SQL](sql-database-automated-backups.md).
- Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Pojedyncza baza danych: Ograniczenia P11 i P15 podczas maksymalny rozmiar większy niż 1 TB

Następujące istotne zagadnienia i ograniczenia dotyczą P11 i P15 baz danych o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli wybierzesz maksymalny rozmiar większy niż 1 TB, podczas tworzenia bazy danych (za pomocą wartość 4 TB lub 4096 GB), polecenia create kończy się niepowodzeniem z powodu błędu w przypadku bazy danych są aprowizowane w nieobsługiwany region.
- W przypadku istniejących P11 i P15 baz danych znajdujących się w jednym z obsługiwanych regionów, możesz zwiększyć maksymalny rozmiar magazynu na ponad 1 TB z przyrostami 256 GB do 4 TB. Aby zobaczyć, czy większy rozmiar jest obsługiwana w Twoim regionie, użyj [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkcji lub Sprawdź rozmiar bazy danych w witrynie Azure portal. Uaktualnianie istniejącej P11 lub P15 bazy danych można wykonać tylko przez głównego identyfikatora logowania poziomu serwera lub członkowie roli bazy danych dbmanager.
- Jeśli operacja uaktualniania jest wykonywane w obsługiwanym regionie natychmiast zaktualizować konfigurację. Baza danych pozostaje w trybie online podczas procesu uaktualniania. Jednak nie możesz wykorzystywać cała kwota magazynu ponad 1 TB miejsca do magazynowania do czasu rzeczywistego wynoszącego zostali uaktualnieni do wersji nowy rozmiar maksymalny. Wymagany czas zależy od rozmiaru bazy danych, trwa uaktualnianie.
- Podczas tworzenia lub aktualizowania P11 lub P15 bazy danych, można wybrać tylko między maksymalny rozmiar 1 TB pojemności i 4 TB w przyrostach wynoszących 256 GB. Podczas tworzenia P11/P15, domyślna opcja magazynu o rozmiarze 1 TB jest wstępnie wybrane. W przypadku baz danych znajdujących się w jednym z obsługiwanych regionów można zwiększyć maksymalną magazynu do maksymalnie 4 TB dla nowej lub istniejącej pojedynczej bazy danych. Inne regiony nie można zwiększyć maksymalny rozmiar, powyżej 1 TB. Cena nie ulega zmianie, gdy wybierzesz 4 TB uwzględnionego magazynu.
- Jeśli maksymalny rozmiar bazy danych ma wartość większą niż 1 TB, następnie nie można zmienić do 1 TB, nawet jeśli rzeczywisty magazyn używany znajduje się poniżej 1 TB. W związku z tym nie można obniżyć P11 lub P15 o maksymalnym rozmiarze większym niż 1 TB do 1 TB P11 lub TB P15 1 lub niższych obliczenia rozmiaru, na przykład P1 P6). To ograniczenie dotyczy także przywracania i kopii scenariusze, w tym punkcie w czasie, przywracanie geograficzne długo-długoterminowa kopia zapasowa zachowywania i kopii bazy danych. Po skonfigurowaniu bazy danych o maksymalnym rozmiarze większym niż 1 TB, wszystkie operacje przywracania tej bazy danych muszą być uruchamiane w P11/P15 o maksymalnym rozmiarze większym niż 1 TB.
- Aktywna replikacja geograficzna scenariuszach:
  - Definiowanie relacji replikacji geograficznej: Jeśli podstawowa baza danych jest P11 lub P15, secondary(ies) również musi być P11 lub P15; niższe rozmiarów wystąpień obliczeniowych są odrzucane jako pomocnicze bazy danych, ponieważ nie może obsługiwać więcej niż 1 TB.
  - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: Zmiana maksymalnego rozmiaru na więcej niż 1 TB w podstawowej bazie danych wyzwala tę samą zmianę w pomocniczej bazie danych. Obu uaktualnień musi zakończyć się powodzeniem, zmiany na serwerze podstawowym zaczęły obowiązywać. Region dla opcji więcej niż 1 TB ograniczenia. Jeśli pomocniczy znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie jest uaktualniany.
- Za pomocą usługi Import/Export dla obciążenia baz danych P11/P15, za pomocą więcej niż 1 TB nie jest obsługiwana. Użyj SqlPackage.exe do [zaimportować](sql-database-import.md) i [wyeksportować](sql-database-export.md) danych.

## <a name="next-steps"></a>Kolejne kroki

- Rdzeń wirtualny limitów zasobów dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na rdzeniach wirtualnych](sql-database-vcore-resource-limits-single-databases.md)
- Limity zasobów (rdzeń wirtualny) dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na rdzeniach wirtualnych](sql-database-vcore-resource-limits-elastic-pools.md)
- Aby uzyskać limites zasobów jednostek DTU dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Limity zasobów dla wystąpienia zarządzanego, można zobaczyć [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacji na temat limitów zasobów na serwerze bazy danych, zobacz [Przegląd limity zasobów na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.