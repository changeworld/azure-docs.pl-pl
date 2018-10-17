---
title: Skalowanie pojedynczej bazy danych zasobów — Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe, które muszą być dostępne dla pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: e00f043f99b9a57fad420c380a55789d73047e77
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352905"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalowanie pojedynczej bazy danych zasobów w usłudze Azure SQL Database

W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe, które muszą być dostępne dla pojedynczej bazy danych w usłudze Azure SQL Database.

## <a name="vcore-based-purchasing-model-change-storage-size"></a>modelu zakupu opartego na rdzeniach wirtualnych: zmiana rozmiaru magazynu

- Magazynu mogą być udostępniane do limitu maksymalnego rozmiaru przy użyciu przyrostami co 1 GB. Magazyn danych można skonfigurować minimalną to 5 GB
- Magazyn dla pojedynczej bazy danych mogą być udostępniane przez zwiększenie lub zmniejszenie jego rozmiar maksymalny za pomocą [witryny Azure portal](https://portal.azure.com), [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Wiersza polecenia platformy azure](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).
- Bazy danych SQL Database automatycznie przydziela 30% dodatkowego miejsca do magazynowania plików dziennika i 32GB na rdzeń wirtualny dla bazy danych TempDB, ale nie może przekraczać 384GB. Bazy danych TempDB jest umieszczony na dołączone dyski SSD we wszystkich warstwach usługi.
- Cena przestrzeni dyskowej dla pojedynczej bazy danych jest sumę kwot dane magazynu i dziennika magazynu oraz przez cenę jednostkową magazynu w warstwie usług. Koszt bazy danych TempDB jest uwzględniona w cenie — rdzeń wirtualny. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>modelu zakupu opartego na rdzeniach wirtualnych: zmiana zasobów obliczeniowych

Po początkowym wybraniu liczba rdzeni wirtualnych, należy można pojedynczej bazy danych w górę lub dół dynamicznie skalować na podstawie rzeczywistego użycia za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-server), [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [programuPowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).

Zmiana usługi warstwy i/lub obliczyć rozmiar bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowy rozmiar obliczeń, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Długość czasu na przejście zmienia się, ale zazwyczaj jest poniżej 4 sekund jest mniejsza niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji w locie w chwili połączenia są wyłączone, długość czasu na przejście może być dłuższy.

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład 250 GB bazy danych, zmiana do z lub w ramach warstwy usług ogólnego przeznaczenia, należy wykonać w ciągu 6 godzin. Dla bazy danych taki sam rozmiar, zmiana rozmiarów wystąpień obliczeniowych w ramach krytyczne dla działania firmy warstwy usługi skalowaniem w górę powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować działania w toku, zobacz: [zarządzania operacjami usługi przy użyciu interfejsu API REST programu SQL](https://docs.microsoft.com/rest/api/sql/operations/operations_list), [zarządzania operacjami usługi przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwie Polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- W przypadku uaktualniania do wersji na wyższą warstwę usługi lub obliczenia rozmiaru, maksymalnego rozmiaru bazy danych zwiększa, chyba że jawnie określisz o większym rozmiarze (maxsize).
- Na starszą wersję bazy danych, miejsca w bazie danych, używane musi być mniejszy niż maksymalny dozwolony rozmiar docelowej warstwy usług i rozmiaru obliczeń.
- Podczas uaktualniania bazy danych o [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, Uaktualnij pomocnicze bazy danych do żądanej usługi i obliczenia rozmiaru przed uaktualnieniem podstawowej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innego, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
- Przed obniżeniem bazę danych przy użyciu [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, obniżyć jej podstawowych baz danych do warstwy usług z żądaną i obliczenia rozmiaru przed przejściem do pomocniczej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Przed obniżeniem do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
- Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Model zakupu w oparciu o jednostki DTU: zmiana rozmiaru magazynu

- Cena jednostki DTU dla pojedynczej bazy danych obejmuje określoną ilość pamięci masowej bez ponoszenia dodatkowych kosztów. Dodatkowego magazynu ponad uwzględnioną kwotę można zaaprowizować za dodatkową opłatą maksymalnie limit maksymalnego rozmiaru, w przyrostach o rozmiarze 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB ponad 1 TB. Magazyn w pakiecie kwoty i limity maksymalnego rozmiaru, zobacz [pojedynczej bazy danych: magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla pojedynczej bazy danych mogą być udostępniane przez odpowiednie zwiększenie jego maksymalny rozmiar przy użyciu portalu Azure [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).
- Cena dodatkowego magazynu dla pojedynczej bazy danych jest mnożony przez cena jednostkowa dodatkowego magazynu w warstwie usług wielkość dodatkowego magazynu. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Model zakupu w oparciu o jednostki DTU: Zmień obliczenia zasobów (Dtu)

Po początkowym wybraniu warstwy usług, rozmiar obliczeń i ilość miejsca w magazynie, możesz skalować pojedynczą bazę danych w górę lub dół dynamicznie na podstawie rzeczywistego użycia w witrynie Azure portal, [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/databases_update).

Wideo pokazano, dynamicznie zmieniające się usługa warstwy i obliczenia rozmiaru w celu zwiększenia dostępna liczba jednostek Dtu dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Zmiana usługi warstwy i/lub obliczyć rozmiar bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowy rozmiar obliczeń, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Długość czasu na przejście zmienia się, ale jest mniej niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji w locie w chwili połączenia są wyłączone, długość czasu na przejście może być dłuższy.

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład 250 GB bazy danych, zmiana do, z lub do warstwy standardowa, należy wykonać w ciągu 6 godzin. Dla bazy danych taki sam rozmiar, zmiana rozmiarów wystąpień obliczeniowych w ramach warstwy Premium skalowanie w górę powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować działania w toku, zobacz: [zarządzania operacjami usługi przy użyciu interfejsu API REST programu SQL](https://docs.microsoft.com/rest/api/sql/operations/operations_list), [zarządzania operacjami usługi przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwie Polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

- W przypadku uaktualniania do wersji na wyższą warstwę usługi lub obliczenia rozmiaru, maksymalnego rozmiaru bazy danych zwiększa, chyba że jawnie określisz o większym rozmiarze (maxsize).
- Na starszą wersję bazy danych, miejsca w bazie danych, używane musi być mniejszy niż maksymalny dozwolony rozmiar docelowej warstwy usług i rozmiaru obliczeń.
- Przed obniżeniem z **Premium** do **standardowa** warstwy, koszty dodatkowego magazynu ma zastosowanie w przypadku, gdy (1) maksymalny rozmiar bazy danych jest obsługiwana w rozmiar docelowej obliczeń i (2) maksymalny rozmiar przekracza dołączonej wielkość magazynu w celu obliczenia rozmiaru. Na przykład, jeśli P1 baza danych o maksymalnej długości wynoszący 500 GB, jest downsized do S3, następnie magazyn dodatkowy koszt dotyczy ponieważ S3 obsługuje maksymalny rozmiar wynoszący 500 GB, a jego dostępnej ilości magazynu tylko 250 GB. Więc wielkość dodatkowego magazynu to 500 GB-250 GB = 250 GB. Cennik dodatkowego magazynu, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywistą ilość miejsca jest mniejsza niż wielkość magazynu w pakiecie, następnie to dodatkowych kosztów można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględnioną kwotę.
- Podczas uaktualniania bazy danych o [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, Uaktualnij pomocnicze bazy danych do żądanej usługi i obliczenia rozmiaru przed uaktualnieniem podstawowej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innego, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
- Przed obniżeniem bazę danych przy użyciu [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, obniżyć jej podstawowych baz danych do warstwy usług z żądaną i obliczenia rozmiaru przed przejściem do pomocniczej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Przed obniżeniem do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
- Oferowane usługi przywracania różnią się w zależności do warstwy usług. Jeśli to powrót do subskrypcji **podstawowe** warstwy, jest krótszym okresie przechowywania kopii zapasowych. Zobacz [kopie zapasowe bazy danych Azure SQL](sql-database-automated-backups.md).
- Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Model zakupu w oparciu o jednostki DTU: ograniczenia z P11 i P15 podczas maksymalny rozmiar większy niż 1 TB

Maksymalny rozmiar większy niż 1 TB dla bazy danych P11 i P15 jest obsługiwana w następujących regionach: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, środkowe stany USA, Francja środkowa, Niemcy środkowe, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa Północna środkowe stany USA, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo, stany USA 2, zachodnie stany USA, administracja USA — Wirginia i Europa Zachodnia. Następujące istotne zagadnienia i ograniczenia dotyczą P11 i P15 baz danych o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli wybierzesz maksymalny rozmiar większy niż 1 TB, podczas tworzenia bazy danych (za pomocą wartość 4 TB lub 4096 GB), polecenia create kończy się niepowodzeniem z powodu błędu w przypadku bazy danych są aprowizowane w nieobsługiwany region.
- W przypadku istniejących P11 i P15 baz danych znajdujących się w jednym z obsługiwanych regionów, możesz zwiększyć maksymalny rozmiar magazynu na ponad 1 TB z przyrostami 256 GB do 4 TB. Aby zobaczyć, czy większy rozmiar jest obsługiwana w Twoim regionie, użyj [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkcji lub Sprawdź rozmiar bazy danych w witrynie Azure portal. Uaktualnianie istniejącej P11 lub P15 bazy danych można wykonać tylko przez głównego identyfikatora logowania poziomu serwera lub członkowie roli bazy danych dbmanager.
- Jeśli operacja uaktualniania jest wykonywane w obsługiwanym regionie natychmiast zaktualizować konfigurację. Baza danych pozostaje w trybie online podczas procesu uaktualniania. Jednak nie możesz wykorzystywać cała kwota magazynu ponad 1 TB miejsca do magazynowania do czasu rzeczywistego wynoszącego zostali uaktualnieni do wersji nowy rozmiar maksymalny. Wymagany czas zależy od rozmiaru bazy danych, trwa uaktualnianie.
- Podczas tworzenia lub aktualizowania P11 lub P15 bazy danych, można wybrać tylko między maksymalny rozmiar 1 TB pojemności i 4 TB w przyrostach wynoszących 256 GB. Podczas tworzenia P11/P15, domyślna opcja magazynu o rozmiarze 1 TB jest wstępnie wybrane. W przypadku baz danych znajdujących się w jednym z obsługiwanych regionów można zwiększyć maksymalną magazynu do maksymalnie 4 TB dla nowej lub istniejącej pojedynczej bazy danych. Inne regiony nie można zwiększyć maksymalny rozmiar, powyżej 1 TB. Cena nie ulega zmianie, gdy wybierzesz 4 TB uwzględnionego magazynu.
- Jeśli maksymalny rozmiar bazy danych ma wartość większą niż 1 TB, następnie nie można zmienić do 1 TB, nawet jeśli rzeczywisty magazyn używany znajduje się poniżej 1 TB. W związku z tym nie można obniżyć P11 lub P15 o maksymalnym rozmiarze większym niż 1 TB do 1 TB P11 lub TB P15 1 lub niższych obliczenia rozmiaru, na przykład P1 P6). To ograniczenie dotyczy także przywracania i kopii scenariusze, w tym punkcie w czasie, przywracanie geograficzne długo-długoterminowa kopia zapasowa zachowywania i kopii bazy danych. Po skonfigurowaniu bazy danych o maksymalnym rozmiarze większym niż 1 TB, wszystkie operacje przywracania tej bazy danych muszą być uruchamiane w P11/P15 o maksymalnym rozmiarze większym niż 1 TB.
- Aktywna replikacja geograficzna scenariuszach:
  - Definiowanie relacji replikacji geograficznej: Jeśli podstawowa baza danych jest P11 lub P15, secondary(ies) również musi być P11 lub P15; niższe rozmiaru obliczeń są odrzucane jako pomocnicze bazy danych, ponieważ nie może obsługiwać więcej niż 1 TB.
  - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: Zmiana maksymalnego rozmiaru na więcej niż 1 TB w podstawowej bazie danych wyzwala tę samą zmianę w pomocniczej bazie danych. Obu uaktualnień musi zakończyć się powodzeniem, zmiany na serwerze podstawowym zaczęły obowiązywać. Region dla opcji więcej niż 1 TB ograniczenia. Jeśli pomocniczy znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie jest uaktualniany.
- Za pomocą usługi Import/Export dla obciążenia baz danych P11/P15, za pomocą więcej niż 1 TB nie jest obsługiwana. Użyj SqlPackage.exe do [zaimportować](sql-database-import.md) i [wyeksportować](sql-database-export.md) danych.

## <a name="next-steps"></a>Kolejne kroki

Ogólne limity zasobów, zobacz [limitów zasobów opartych na rdzeniach wirtualnych bazy danych SQL — pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów na podstawie jednostek DTU bazy danych SQL — pul elastycznych](sql-database-dtu-resource-limits-single-databases.md).
