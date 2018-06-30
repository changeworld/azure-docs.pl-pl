---
title: Zasobów na podstawie jednostek dtu w warstwie bazy danych SQL Azure ogranicza pojedynczych baz danych | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe limity zasobów na podstawie jednostek DTU dla pojedynczych baz danych w bazie danych SQL Azure.
services: sql-database
author: sachinpMSFT
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: carlrab
ms.openlocfilehash: 2d6660e1064959f2d04424ae1c3e9bc668231c92
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131325"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych przy użyciu model kupna jednostek dtu w warstwie 

Ten artykuł zawiera limity szczegółowe zasobów dla puli elastycznej bazy danych SQL Azure przy użyciu model kupna jednostek dtu w warstwie.

Na podstawie jednostek dtu w warstwie zakupów modelu zasobów limitów dla pul elastycznych, zobacz [limity zasobów na podstawie jednostek dtu w warstwie - pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md). Limitów zasobów na podstawie vCore, zobacz [limity zasobów na podstawie vCore - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów na podstawie vCore - pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Pojedyncza baza danych: magazyn o rozmiarze i poziomy wydajności

Dla pojedynczej bazy danych w poniższej tabeli przedstawiono dostępne zasoby dla pojedynczej bazy danych na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość pamięci masowej dla pojedynczej bazy danych przy użyciu [portalu Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [interfejsu wiersza polecenia Azure](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), lub [Interfejsu API REST](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="basic-service-tier"></a>Warstwa Podstawowa usług
| **Poziom wydajności** | **Podstawowa** |
| :--- | --: |
| Maksymalna liczba jednostek DTU | 5 |
| Dołączony magazyn (GB) | 2 |
| Maksymalna liczba opcji magazynu (GB) | 2 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) |ND |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań) | 30 |
| Maksymalna liczba współbieżnych sesji | 300 |
|||

### <a name="standard-service-tier"></a>Warstwa Standardowa usług
| **Poziom wydajności** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 10 | 20 | 50 | 100 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji magazynu (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)| 60 | 90 | 120 | 200 |
| Maksymalna liczba współbieżnych sesji |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standardowa usługa warstwy (kontynuacja)
| **Poziom wydajności** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 200 | 400 | 800 | 1600 | 3000 |
| Dołączony magazyn (GB) | 250 | 250 | 250 | 250 | 250 |
| Maksymalna liczba opcji magazynu (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | ND | ND | ND | ND |ND |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)| 400 | 800 | 1600 | 3200 |6000 |
| Maksymalna liczba współbieżnych sesji |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Warstwa Premium usług 
| **Poziom wydajności** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maksymalna liczba jednostek DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Dołączony magazyn (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maksymalna liczba opcji magazynu (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maksymalna pojemność magazynu OLTP w pamięci (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maksymalna liczba współbieżnych sesji | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> Więcej niż 1 TB pamięci masowej w warstwie Premium jest dostępna we wszystkich regionach, z wyjątkiem następujących: zachodnie środkowe stany, Chiny Wschodnia, USDoDCentral, Niemcy środkowe, południowo-zachodni USDoDEast, USA wersji dla instytucji rządowych, Niemcy północno-wschodnie, Północna Chiny USGovIowa. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Pojedyncza baza danych: zmiana rozmiaru magazynu

- Cena jednostek DTU dla pojedynczej bazy danych zawiera pewne magazynu bez ponoszenia dodatkowych kosztów. Dodatkowe miejsce do magazynowania poza uwzględniona ilość można udostępnić dla dodatkowych kosztów w granicach maksymalnej długości w przyrostach 250 GB do 1 TB, a następnie w przyrostach 256 GB poza 1 TB. Dla kwoty uwzględnione magazynu i limity maksymalny rozmiar, zobacz [pojedynczej bazy danych: magazyn o rozmiarze i poziomy wydajności](#single-database-storage-sizes-and-performance-levels).
- Dodatkowy magazyn dla pojedynczej bazy danych można udostępnić przez odpowiednie zwiększenie jego maksymalny rozmiar przy użyciu [portalu Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Interfejs wiersza polecenia](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update).
- Cena dodatkowe miejsce do magazynowania dla pojedynczej bazy danych jest dodatkowe miejsce do magazynowania wielkość pomnożona przez cenie jednostkowej dodatkowe miejsce do magazynowania warstwy usług. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Pojedyncza baza danych: Zmień Dtu

Po wybraniu początkowo warstwy usług, poziom wydajności i wielkość magazynu, można skalować pojedynczej bazy danych w górę lub w dół dynamicznie oparte na rzeczywiste środowisko przy użyciu [portalu Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update). 

Poniższe wideo przedstawia dynamicznie zmieniające warstwę wydajności w celu zwiększenia dostępności jednostek Dtu dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Zmienienie warstwy usług i/lub poziomu wydajności bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowym poziomie wydajności, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Czas na przejście jest różny, ale jest mniej niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji transmitowane w chwili połączenia są wyłączone, czas na przejście może trwać dłużej. 

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład bazę danych 250 GB, która zmienia do, z lub do warstwy standardowych usług, powinno zakończyć się w ciągu sześciu godzin. W bazie danych sam rozmiar, który jest zmiana poziomów wydajności w ramach warstwy usług Premium skalowanie w pionie powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować w trakcie operacji, zobacz: [zarządzania operacjami przy użyciu interfejsu API REST SQL](/rest/api/sql/Operations/List), [zarządzania operacjami przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorować operacji za pomocą T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwa Polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Jeśli uaktualniasz do wyższego poziomu wydajności ani warstwy usługi, maksymalnego rozmiaru bazy danych nie zwiększa chyba że jawnie określ większy rozmiar (maxsize).
* Na starszą wersję bazy danych, bazy danych używane miejsce musi być mniejszy niż maksymalny dozwolony rozmiar docelowy warstwę i poziom wydajności usługi. 
* Podczas zmiany na starszą wersję z **Premium** do **standardowe** warstwy, kosztów dodatkowe miejsce do magazynowania w przypadku zarówno (1 maksymalny rozmiar bazy danych jest obsługiwany w docelowej poziom wydajności i (2 maksymalny rozmiar przekracza Magazyn dołączony ilość docelowy poziom wydajności. Na przykład jeśli baza danych P1 o maksymalnej długości 500 GB jest downsized do S3, następnie koszt dodatkowe miejsce do magazynowania ma zastosowanie od S3 obsługuje maksymalny rozmiar 500 GB, a jego wielkość magazynu dołączone jest tylko 250 GB. Tak dodatkowe miejsce do magazynowania jest 500 GB – 250 GB = 250 GB. Dla dodatkowego magazynu o cenach, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość miejsca jest mniejsza niż wielkość magazynu dołączone to z żadnymi dodatkowymi kosztami można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględniona ilość. 
* Podczas uaktualniania bazy danych o [— replikacja geograficzna](sql-database-geo-replication-portal.md) włączone, Uaktualnij swoje pomocniczej bazy danych do warstwy żądaną wydajność przed uaktualnieniem podstawowej bazy danych (ogólne wskazówki w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innej, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
* Podczas zmiany na starszą wersję bazy danych o [— replikacja geograficzna](sql-database-geo-replication-portal.md) włączone, obniżyć jego głównej bazy danych do warstwy żądaną wydajność przed przejściem dodatkowej bazy danych (ogólne wskazówki w celu uzyskania najlepszej wydajności). Podczas zmiany na starszą wersję do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
* Oferowane usługi przywracania różnią się w zależności do warstwy usług. Jeśli są powrót do subskrypcji **podstawowe** warstwy, brak krótszym okresie przechowywania kopii zapasowych — Zobacz [kopie zapasowe bazy danych SQL Azure](sql-database-automated-backups.md).
* Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Pojedyncza baza danych: ograniczenia P11 i P15, gdy maksymalny rozmiar większy niż 1 TB

Maksymalny rozmiar większy niż 1 TB P11 i P15 z replikacją bazy danych jest obsługiwane w następujących regionach: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada centralnej, Kanada Wschodnia, środkowe stany USA, Francja centralnej, Niemcy centralnej, Japonia Wschodnia, Japonia Zachodnia, centralnej Korei Północna środkowe stany USA, Europa Północna, Południowej środkowe stany USA, południowo Azja Wschodnia, Wielka Brytania Południowa, Wielka Brytania Zachodnia, East2 USA, zachodnie stany USA, Virginia wersji dla instytucji rządowych USA i Europa Zachodnia. Następujące zagadnienia i ograniczenia dotyczą P11 i P15 z replikacją baz danych o maksymalnym rozmiarze większa niż 1 TB:

- Jeśli wybierzesz maksymalny rozmiar większy niż 1 TB, podczas tworzenia bazy danych (przy użyciu wartość 4 TB lub 4096 GB), polecenia create kończy się niepowodzeniem z powodu błędu w przypadku bazy danych jest obsługiwana w nieobsługiwany region.
- Dla istniejących P11 P15 z replikacją baz danych i znajduje się w jednym z obsługiwanych regionów, możesz zwiększyć maksymalną magazynu poza 1 TB w przyrostach 256 GB do 4 TB. Aby sprawdzić, czy większy rozmiar jest obsługiwana w danym regionie, użyj [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkcji lub Sprawdź rozmiar bazy danych w portalu Azure. Uaktualnianie istniejącego P11 lub P15 z replikacją bazy danych można wykonać tylko przez głównego identyfikatora logowania poziomu serwera lub członków dbmanager: roli bazy danych. 
- Jeśli operacja uaktualnienia jest wykonywane w obsługiwanym regionie natychmiast zaktualizować konfigurację. Podczas procesu uaktualniania bazy danych pozostaje w trybie online. Nie można jednak korzystać pełna ilość miejsca w magazynie poza 1 TB pamięci masowej aż pliki bazy danych rzeczywistych zostały uaktualnione do nowy rozmiar maksymalny. Wymagany czas zależy od rozmiaru bazy danych trwa uaktualnianie. 
- Podczas tworzenia lub aktualizowania P11 lub P15 z replikacją bazy danych, można wybrać tylko między 1 TB i 4 TB maksymalny rozmiar w przyrostach 256 GB. Podczas tworzenia P11/P15, wstępnie wybrane jest domyślną opcją 1 TB pamięci masowej. W przypadku baz danych znajdujących się w jednym z obsługiwanych regionów może zwiększyć maksymalną magazynu na maksymalnie 4 TB dla nowego lub istniejącego pojedynczej bazy danych. Dla wszystkich innych regionów nie można zwiększyć maksymalny rozmiar w ponad 1 TB. Cena nie zmienia się po wybraniu 4 TB pamięci masowej uwzględnione.
- Jeśli maksymalny rozmiar bazy danych jest wartość większą niż 1 TB, następnie nie można zmienić do 1 TB, nawet jeśli rzeczywistego magazynu używanego znajduje się poniżej 1 TB. W związku z tym przypadku nie można obniżyć P11 lub P15 o maksymalnym rozmiarze większy niż 1 TB na wartość 1 TB P11 1 TB P15 lub obniżenia wydajności warstwy, takie jak P1 P6). To ograniczenie ma zastosowanie również do przywracania i kopiowania scenariusze, w tym punkcie w czasie, przywracanie geograficznie, długo-długoterminowego — kopia zapasowa przechowywania i kopii bazy danych. Po skonfigurowaniu bazy danych o maksymalnym rozmiarze większa niż 1 TB wszystkie operacje przywracania bazy danych musi być uruchamiane w P11/P15 o maksymalnym rozmiarze większa niż 1 TB.
- Aktywna replikacja geograficzna scenariuszach:
   - Definiowanie relacji — replikacja geograficzna: Jeśli podstawowa baza danych jest P11 lub P15 z replikacją, secondary(ies) musi być także P11 lub P15; niższe poziomy wydajności są odrzucane jako pomocnicze bazy danych, ponieważ nie może obsługiwać więcej niż 1 TB.
   - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: Zmiana maksymalnego rozmiaru na więcej niż 1 TB w głównej bazie danych wyzwala te same zmiany w pomocniczej bazie danych. Obu uaktualnień musi zakończyć się powodzeniem zmiany na serwerze podstawowym zaczęły obowiązywać. Mają zastosowanie ograniczenia region opcji więcej niż 1 TB. Pomocniczy znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie jest uaktualniany.
- Za pomocą usługi Import/Eksport ładowania P11/P15 z replikacją baz danych, z więcej niż 1 TB nie jest obsługiwane. Użyj SqlPackage.exe do [zaimportować](sql-database-import.md) i [wyeksportować](sql-database-export.md) danych.

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Informacje o Dtu a Edtu, zobacz [Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Informacje o limity rozmiaru bazy danych tempdb, zobacz https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
