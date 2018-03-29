---
title: Limity zasobów bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe limity zasobów bazy danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: fb9cb15e96c4098151d220c2bd47fbb7f299687c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="azure-sql-database-resource-limits"></a>Limitom zasobów bazy danych SQL Azure

> [!IMPORTANT]
> Limitów zasobów z wystąpienia zarządzane bazy danych SQL Azure, zobacz [zarządzane wystąpienia bazy danych SQL](sql-database-managed-instance.md#managed-instance-service-tier).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Pojedyncza baza danych: magazyn o rozmiarze i poziomy wydajności

Dla pojedynczej bazy danych w poniższej tabeli przedstawiono dostępne zasoby dla pojedynczej bazy danych na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość pamięci masowej dla pojedynczej bazy danych przy użyciu [portalu Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [języka Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), lub [interfejsu API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Pojedyncza baza danych: zmiana rozmiaru magazynu

- Cena jednostek DTU dla pojedynczej bazy danych zawiera pewne magazynu bez ponoszenia dodatkowych kosztów. Dodatkowe miejsce do magazynowania poza uwzględniona ilość można udostępnić dla dodatkowych kosztów w granicach maksymalnej długości w przyrostach 250 GB do 1 TB, a następnie w przyrostach 256 GB poza 1 TB. Dla kwoty uwzględnione magazynu i limity maksymalny rozmiar, zobacz [pojedynczej bazy danych: magazyn o rozmiarze i poziomy wydajności](#single-database-storage-sizes-and-performance-levels).
- Dodatkowy magazyn dla pojedynczej bazy danych można udostępnić przez odpowiednie zwiększenie jego maksymalny rozmiar przy użyciu [portalu Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Interfejs wiersza polecenia](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update).
- Cena dodatkowe miejsce do magazynowania dla pojedynczej bazy danych jest dodatkowe miejsce do magazynowania wielkość pomnożona przez cenie jednostkowej dodatkowe miejsce do magazynowania warstwy usług. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Pojedyncza baza danych: Zmień Dtu

Po wybraniu początkowo warstwy usług, poziom wydajności i wielkość magazynu, można skalować pojedynczej bazy danych w górę lub w dół dynamicznie oparte na rzeczywiste środowisko przy użyciu [portalu Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update). 

Poniższe wideo przedstawia dynamicznie zmieniające warstwę wydajności w celu zwiększenia dostępności jednostek Dtu dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Zmienienie warstwy usług i/lub poziomu wydajności bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowym poziomie wydajności, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Czas na przejście jest różny, ale zazwyczaj w sekcji 4 sekundy jest mniej niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji transmitowane w chwili połączenia są wyłączone, czas na przejście może trwać dłużej. 

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład bazę danych 250 GB, która zmienia do, z lub do warstwy standardowych usług, powinno zakończyć się w ciągu sześciu godzin. W bazie danych sam rozmiar, który jest zmiana poziomów wydajności w ramach warstwy usług Premium skalowanie w pionie powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować w trakcie operacji, zobacz: [zarządzania operacjami przy użyciu interfejsu API REST SQL](/rest/api/sql/Operations/List), [zarządzania operacjami przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorować operacji za pomocą T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwa Polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Jeśli uaktualniasz do wyższego poziomu wydajności ani warstwy usługi, maksymalnego rozmiaru bazy danych nie zwiększa chyba że jawnie określ większy rozmiar (maxsize).
* Na starszą wersję bazy danych, bazy danych używane miejsce musi być mniejszy niż maksymalny dozwolony rozmiar docelowy warstwę i poziom wydajności usługi. 
* Podczas zmiany na starszą wersję z **Premium** do **standardowe** warstwy, kosztów dodatkowe miejsce do magazynowania w przypadku zarówno (1 maksymalny rozmiar bazy danych jest obsługiwany w docelowej poziom wydajności i (2 maksymalny rozmiar przekracza Magazyn dołączony ilość docelowy poziom wydajności. Na przykład jeśli baza danych P1 o maksymalnej długości 500 GB jest downsized do S3, następnie koszt dodatkowe miejsce do magazynowania ma zastosowanie od S3 obsługuje maksymalny rozmiar 500 GB, a jego wielkość magazynu dołączone jest tylko 250 GB. Tak dodatkowe miejsce do magazynowania jest 500 GB – 250 GB = 250 GB. Dla dodatkowego magazynu o cenach, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość miejsca jest mniejsza niż wielkość magazynu dołączone to z żadnymi dodatkowymi kosztami można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględniona ilość. 
* Podczas uaktualniania bazy danych o [— replikacja geograficzna](sql-database-geo-replication-portal.md) włączone, Uaktualnij swoje pomocniczej bazy danych do warstwy żądaną wydajność przed uaktualnieniem podstawowej bazy danych (ogólne wskazówki). Podczas uaktualniania do innej, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
* Podczas zmiany na starszą wersję bazy danych o [— replikacja geograficzna](sql-database-geo-replication-portal.md) włączone, obniżyć jego głównej bazy danych do warstwy żądaną wydajność przed przejściem dodatkowej bazy danych (ogólne wskazówki). Podczas zmiany na starszą wersję do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
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

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pula elastyczna: magazyn o rozmiarze i poziomy wydajności

Dla puli elastycznej bazy danych SQL w poniższych tabelach przedstawiono zasoby dostępne na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość magazynu za pomocą [portalu Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [interfejsu wiersza polecenia Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), lub [interfejsu API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Ograniczenia zasobów pojedynczych baz danych w puli elastycznej zazwyczaj są takie same jak dla pojedynczych baz danych poza puli na podstawie Dtu i warstwy usług. Na przykład maksymalna pracowników równoczesnych S2 bazy danych jest 120 pracowników. Tak max równoczesnych procesów roboczych dla bazy danych w puli standardowej jest również 120 pracowników Jeśli maksymalna wartość DTU na bazę danych w puli jest 50 jednostek Dtu (co jest równoważne S2).

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Jeśli używane są wszystkie jednostki DTU puli elastycznej, każda baza danych w puli otrzymuje taką samą ilość zasobów do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwe udostępnianie zasobów puli elastycznej jest wykonywane oprócz zapewniania dowolnej ilości zasobów w przeciwnym razie gwarantowanej dla każdej bazy danych, gdy minimalna liczba jednostek DTU na bazę danych jest ustawiona na wartość różną od zera.

### <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych dla puli baz danych

W poniższej tabeli opisano właściwości puli baz danych.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba jednostek eDTU na bazę danych |Maksymalna liczba jednostek eDTU, z których może korzystać dowolna baza danych w puli, jeśli są dostępne na podstawie użycia innych baz danych w puli. Maksymalna liczba jednostek eDTU na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Należy ustawić odpowiednio wysoką maksymalną liczbę jednostek eDTU na bazę danych, aby obsłużyć szczytowe użycia baz danych. Oczekiwany jest pewien stopień nadmiernego przydzielania, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych, w których nie wszystkie bazy danych jednocześnie osiągają szczytowe użycie. Załóżmy na przykład, że użycie szczytowe na bazę danych wynosi 20 eDTU i tylko 20% ze 100 baz danych w puli osiąga szczytowe użycie w tym samym czasie. Jeśli maksymalna liczba jednostek eDTU na bazę danych wynosi 20 eDTU, zasadne jest nadmiarowe przydzielenie pięciokrotnie większej liczby eDTU dla puli i ustawienie liczby 400 jednostek eDTU na pulę. |
| Minimalna liczba jednostek eDTU na bazę danych |Minimalna liczba jednostek eDTU gwarantowana dla każdej bazy danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalna liczba jednostek eDTU na bazę danych może być ustawiona na 0 i jest to również wartość domyślna. Tę właściwość można ustawić na dowolną wartość między 0 a średnim użyciem jednostek eDTU na bazę danych. Iloczyn liczby baz danych w puli i minimalnej liczby jednostek eDTU na bazę danych nie może przekraczać liczby jednostek eDTU na pulę. Na przykład jeśli pula zawiera 20 baz danych, a minimalna liczba jednostek eDTU na bazę danych wynosi 10 eDTU, liczba jednostek eDTU na pulę musi wynosić co najmniej 200 eDTU. |
| Maksymalna liczba magazyn na bazę danych |Maksymalny rozmiar magazynu dla bazy danych w puli. Bazy danych w puli współużytkują magazyn puli, a więc magazyn bazy danych jest ograniczony do rozmiaru pozostałego magazynu puli lub maksymalnego rozmiaru magazynu na bazę danych (do mniejszej z tych wartości). Maksymalny rozmiar magazynu na bazę danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pula elastyczna: zmiana rozmiaru magazynu

- Cena eDTU dla elastycznej puli obejmuje określoną ilość magazynu bez ponoszenia dodatkowych kosztów. Dodatkowe miejsce do magazynowania poza uwzględniona ilość można udostępnić dla dodatkowych kosztów w granicach maksymalnej długości w przyrostach 250 GB do 1 TB, a następnie w przyrostach 256 GB poza 1 TB. Dla kwoty uwzględnione magazynu i limity maksymalny rozmiar, zobacz [puli elastycznej: magazyn o rozmiarze i poziomy wydajności](#elastic-pool-storage-sizes-and-performance-levels).
- Dodatkowe miejsce do magazynowania dla puli elastycznej można udostępnić przez odpowiednie zwiększenie jego maksymalny rozmiar przy użyciu [portalu Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [interfejsu API REST ](/rest/api/sql/elasticpools/update).
- Cena dodatkowe miejsce do magazynowania dla puli elastycznej jest dodatkowe miejsce do magazynowania wielkość pomnożona przez cenie jednostkowej dodatkowe miejsce do magazynowania warstwy usług. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Pula elastyczna: Zmień Edtu

Można zwiększyć lub zmniejszyć zasoby dostępne dla puli elastycznej oparte na potrzeby przy użyciu zasobów [portalu Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [ Interfejs API REST](/rest/api/sql/elasticpools/update).

- Kiedy ponowne skalowanie jednostek Edtu puli, połączenia z bazą danych krótko są usuwane. To samo, jak występuje, gdy ponowne skalowanie Dtu dla pojedynczej bazy danych (nie w puli). Aby uzyskać szczegółowe informacje na czas trwania i wpływu przerwanie połączenia dla bazy danych podczas operacji ponowne skalowanie, zobacz [ponowne skalowanie jednostek Dtu dla pojedynczej bazy danych](#single-database-change-storage-size). 
- Czas trwania, aby zmienić jednostek Edtu puli może zależeć od całkowitej ilości miejsca używanego przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, rescaling opóźnienia oblicza średnią 90 minut lub mniej na 100 GB. Na przykład jeśli całkowita ilość miejsca, używany przez wszystkie bazy danych w puli jest 200 GB, oczekiwany czas oczekiwania na ponowne skalowanie puli wynosi 3 godziny, a następnie lub mniej. W niektórych przypadkach w ramach warstwy standardowa lub Basic rescaling czas oczekiwania może być niezależnie od ilości miejsca używanego w obszarze pięć minut.
- Ogólnie rzecz biorąc, czas trwania, aby zmienić wartości min Edtu na bazę danych lub maksymalna liczba jednostek Edtu na bazę danych to pięć minut lub mniej.
- Gdy redukcję zatrudnienia jednostek Edtu puli miejsca puli musi być mniejszy niż maksymalny dozwolony rozmiar Edtu warstwy i puli usługi docelowej.
- Gdy ponowne skalowanie jednostek Edtu puli, kosztów dodatkowe miejsce do magazynowania w przypadku magazynu (1 maksymalny rozmiar puli jest obsługiwana przez docelową pulę i (2 maksymalny rozmiar magazynu przekracza wielkość magazynu dołączone docelową pulę. Na przykład jeśli 100 jednostek eDTU puli standardowej, maksymalny rozmiar 100 GB jest downsized do 50 eDTU puli standardowej, to koszt dodatkowe miejsce do magazynowania jest stosowany od docelową pulę obsługuje maksymalny rozmiar 100 GB, a jego wielkość magazynu dołączone jest tylko 50 GB. Tak dodatkowe miejsce do magazynowania jest 100 GB – 50 GB = 50 GB. Dla dodatkowego magazynu o cenach, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość miejsca jest mniejsza niż wielkość magazynu dołączone to z żadnymi dodatkowymi kosztami można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględniona ilość. 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Co to jest maksymalna liczba serwerów i baz danych?

| Maksimum | Wartość |
| :--- | :--- |
| Baz danych na serwerze | 5000 |
| Liczba serwerów na subskrypcję dla regionu | 20 |
|||

> [!IMPORTANT]
> Jak liczba baz danych zbliża się do limitu na serwerze, mogą wystąpić następujące czynności:
> <br> • Zwiększyć czas oczekiwania w wykonywanie zapytań bazy danych master.  Dotyczy to widoków dane statystyczne wykorzystania zasobów, takich jak sys.resource_stats.
> <br> • Zwiększenie opóźnienia podczas wykonywania operacji zarządzania i renderowania portalu punkt widzenia obejmujących wyliczanie baz danych na serwerze.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Co się stanie po osiągnięciu bazy danych i elastycznej puli zasobów limity?

### <a name="compute-dtus-and-edtus"></a>Obliczenia bazy danych (Dtu a Edtu)

Gdy wzrośnie wykorzystania obliczeń bazy danych (mierzoną Dtu a Edtu) opóźnienia zapytania zwiększa i może nawet limit czasu. W tych warunkach zapytania mogą być umieszczone w kolejce przez usługę i są pod warunkiem, że zwolnić zasobów do wykonania jako zasobu.
Gdy wystąpią wykorzystania wysokiej obliczeń, są następujące opcje środki zaradcze:

- Zwiększenie poziomu wydajności bazy danych lub puli elastycznej, aby zapewnić więcej Dtu lub Edtu bazy danych. Zobacz [pojedynczej bazy danych: Zmień Dtu](#single-database-change-dtus) i [puli elastycznej: Zmień Edtu](#elastic-pool-change-edtus).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Magazyn

Gdy miejsca w bazie danych używany przez nią miejsce osiągnie limit maksymalnego rozmiaru bazy danych wstawia i aktualizacji, które zwiększają rozmiar danych nie powiodło się i klienci odbierają [komunikat o błędzie](sql-database-develop-error-messages.md). Baza danych WYBIERA i USUWA nadal się powieść.

Gdy wystąpią wykorzystanie miejsca wysoka, są następujące opcje środki zaradcze:

- Zwiększyć maksymalny rozmiar bazy danych lub puli elastycznej lub zmień poziom wydajności, aby uzyskać więcej dołączone magazynu. Zobacz [limity zasobów bazy danych SQL](sql-database-resource-limits.md).
- W przypadku bazy danych w puli elastycznej następnie również bazy danych może zostać przeniesiona poza puli tak, aby jej miejsca do magazynowania nie są współużytkowane z innych baz danych.

### <a name="sessions-and-workers-requests"></a>Sesje i procesy robocze (liczba żądań) 

Maksymalna liczba jednoczesnych sesji i pracownicy są określane przez warstwę i poziom wydajności usługi (Dtu a Edtu).  Nowe żądania są odrzucane po osiągnięciu limitu sesji lub proces roboczy, a klienci odbierają komunikat o błędzie. Gdy liczbę połączeń dostępnych może być kontrolowane przez aplikację, liczbę równoczesnych procesów roboczych jest często przeszkodę oszacować i sterowanie nią. Jest to szczególnie istotne podczas okresów obciążenia szczytowego po osiągnięciu limitu zasobów bazy danych i pracowników, ustawianie z powodu dłużej uruchomione zapytania. 

Gdy wystąpią wysokie użycie sesji lub procesu roboczego, są następujące opcje środki zaradcze:
- Zwiększenie poziomu warstwy lub wydajności usługi bazy danych lub elastyczna pula. Zobacz [pojedynczej bazy danych: Zmienianie rozmiaru magazynu](#single-database-change-storage-size), [pojedynczej bazy danych: zmienić Dtu](#single-database-change-dtus), [puli elastycznej: Zmienianie rozmiaru magazynu](#elastic-pool-change-storage-size), i [puli elastycznej: Zmień Edtu ](#elastic-pool-change-edtus).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania, jeśli przyczyną zwiększone wykorzystanie wynika z rywalizacji o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje dotyczące warstwy usług, zobacz [warstw usług](sql-database-service-tiers.md).
- Aby uzyskać informacje o pojedynczych baz danych, zobacz [pojedyncze bazy danych zasobów](sql-database-resource-limits.md).
- Informacje o pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Informacje o Dtu a Edtu, zobacz [Dtu a Edtu](sql-database-what-is-a-dtu.md).
- Informacje o limity rozmiaru bazy danych tempdb, zobacz https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
