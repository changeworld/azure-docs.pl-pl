---
title: Skalowanie pojedynczej bazy danych zasobów — Azure SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe, które muszą być dostępne dla pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 471ded9cd94623929630155f1a3c613bf00576a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331846"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalowanie pojedynczej bazy danych zasobów w usłudze Azure SQL Database

W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe, które muszą być dostępne dla pojedynczej bazy danych w usłudze Azure SQL Database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

## <a name="change-compute-size-vcores-or-dtus"></a>Zmiana rozmiaru obliczeń (rdzeni wirtualnych lub jednostek Dtu)

Po początkowym wybraniu liczbę rdzeni wirtualnych lub jednostek Dtu, należy można pojedynczej bazy danych w górę lub dół dynamicznie skalować na podstawie rzeczywistego użycia za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ Program PowerShell](/powershell/module/az.sql/set-azsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/update).

Wideo pokazano, dynamicznie zmieniające się usługa warstwy i obliczenia rozmiaru w celu zwiększenia dostępna liczba jednostek Dtu dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Wpływ zmiany rozmiaru obliczeń warstwy lub ponowne skalowanie usługi

Zmiana usługi warstwy lub obliczeniowe rozmiar pojedynczej bazy danych obejmuje głównie usługi, wykonując następujące czynności:

1. Tworzenie nowego wystąpienia obliczeniowe dla bazy danych  

    Nowe wystąpienie obliczeniowe dla bazy danych jest tworzony z warstwy żądanej usługi a rozmiar obliczeń. Dla niektórych kombinacji warstwę usługi i zmiany rozmiaru obliczeń repliki bazy danych należy utworzyć nowe wystąpienie obliczeniowe, które polega na kopiowaniu danych i zdecydowanie mogą mieć wpływ na ogólny czas oczekiwania. Niezależnie od tego baza danych pozostaje w trybie online w tym kroku i połączeń w dalszym ciągu nastąpi przekierowanie do bazy danych w oryginalnym wystąpieniu obliczeń.

2. Przełącz routing połączeń do nowego wystąpienia obliczeniowego

    Istniejące połączenia z bazą danych w oryginalnym wystąpieniu obliczeniowych są porzucane. Wszystkie nowe połączenia są określane w bazie danych w nowe wystąpienie obliczeniowe. Niektóre kombinacje warstwy usług i zmiany rozmiaru obliczeń pliki bazy danych są odłączone i ponownie dołączyć podczas przełączania.  Niezależnie od tego przełącznik może spowodować przerwa w świadczeniu usługi krótki, gdy baza danych jest niedostępne, zazwyczaj mniej niż 30 sekund i często tylko kilka sekund. W przypadku długotrwałych transakcji uruchomiony podczas połączenia zostaną przerwane, czas trwania tego kroku może trwać dłużej, w celu odzyskania przerwane transakcje. [Przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md) zmniejszyć wpływ w przerywania długotrwałych transakcji.

> [!IMPORTANT]
> Żadne dane nie zostaną utracone podczas dowolny krok w przepływie pracy.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Opóźnienie zmiany rozmiaru obliczeń warstwy lub ponowne skalowanie usługi

Czas oczekiwania, aby zmienić warstwę usługi lub zmienić rozmiar obliczeń pojedynczą bazę danych lub elastycznej puli jest sparametryzowane w następujący sposób:

|Warstwa usług|Podstawowe pojedynczej bazy danych</br>Standardowa (S0 — S1)|Elastyczna pula podstawowa</br>Standard (S2-S12) </br>W Hiperskali </br>Ogólne pojedynczą bazę danych przeznaczenia lub puli elastycznej|Premium lub krytyczne dla działania firmy pojedynczą bazę danych lub puli elastycznej|
|:---|:---|:---|:---|
|**Podstawowe pojedynczej bazy danych,</br> standardowa (S0 — S1)**|&bull; &nbsp;Niezależnie od miejsca używanego w stałym czasie opóźnienia</br>&bull; &nbsp;Zwykle, mniej niż 5 minut|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|
|**Podstawowych pul elastycznych, </br>Standard (S2 S12), </br>w Hiperskali </br>ogólnego przeznaczenia pojedynczej bazy danych lub puli elastycznej**|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Niezależnie od miejsca używanego w stałym czasie opóźnienia</br>&bull; &nbsp;Zwykle, mniej niż 5 minut|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|
|**Premium lub krytyczne dla działania firmy pojedynczą bazę danych lub puli elastycznej**|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|

> [!TIP]
> Aby monitorować działania w toku, zobacz: [Zarządzanie operacjami za pomocą interfejsu API REST programu SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [zarządzania operacjami usługi przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwa polecenia programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [Stop AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Anulowanie zmiany warstwy usługi lub obliczeń podczas ponownego skalowania operacji

Warstwy usług, zmienić lub obliczeń podczas ponownego skalowania operacji można anulować.

#### <a name="azure-portal"></a>Azure Portal

W bloku Przegląd bazy danych, przejdź do **powiadomienia** i kliknij Kafelek wskazujący jest wykonywana operacja:

![Trwającą operacją](media/sql-database-single-database-scale/ongoing-operations.png)

Następnie kliknij przycisk oznaczony **Anuluj tę operację**.

![Anuluj trwającą operację.](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

Z wiersza polecenia programu PowerShell, należy ustawić `$ResourceGroupName`, `$ServerName`, i `$DatabaseName`, a następnie uruchom następujące polecenie:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Dodatkowe zagadnienia podczas zmiany usługi warstwy lub podczas ponownego skalowania rozmiaru obliczeń

- W przypadku uaktualniania do wersji na wyższą warstwę usługi lub obliczenia rozmiaru, maksymalnego rozmiaru bazy danych zwiększa, chyba że jawnie określisz o większym rozmiarze (maxsize).
- Na starszą wersję bazy danych, miejsca w bazie danych, używane musi być mniejszy niż maksymalny dozwolony rozmiar docelowej warstwy usług i rozmiaru obliczeń.
- Przed obniżeniem z **Premium** do **standardowa** warstwy, koszty dodatkowego magazynu ma zastosowanie w przypadku, gdy (1) maksymalny rozmiar bazy danych jest obsługiwana w rozmiar docelowej obliczeń i (2) maksymalny rozmiar przekracza dołączonej wielkość magazynu w celu obliczenia rozmiaru. Na przykład, jeśli P1 baza danych o maksymalnej długości wynoszący 500 GB, jest downsized do S3, następnie magazyn dodatkowy koszt dotyczy ponieważ S3 obsługuje maksymalny rozmiar wynoszący 500 GB, a jego dostępnej ilości magazynu tylko 250 GB. Więc wielkość dodatkowego magazynu to 500 GB-250 GB = 250 GB. Cennik dodatkowego magazynu, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywistą ilość miejsca jest mniejsza niż wielkość magazynu w pakiecie, następnie to dodatkowych kosztów można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględnioną kwotę.
- Podczas uaktualniania bazy danych o [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, Uaktualnij pomocnicze bazy danych do żądanej usługi i obliczenia rozmiaru przed uaktualnieniem podstawowej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innego, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
- Przed obniżeniem bazę danych przy użyciu [geografickou replikaci](sql-database-geo-replication-portal.md) włączona, obniżyć jej podstawowych baz danych do warstwy usług z żądaną i obliczenia rozmiaru przed przejściem do pomocniczej bazy danych (ogólne wytyczne w celu uzyskania najlepszej wydajności). Przed obniżeniem do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
- Oferowane usługi przywracania różnią się w zależności do warstwy usługi. Jeśli to powrót do subskrypcji **podstawowe** warstwy, jest krótszym okresie przechowywania kopii zapasowych. Zobacz [kopie zapasowe bazy danych Azure SQL](sql-database-automated-backups.md).
- Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

### <a name="billing-during-compute-rescaling"></a>Rozliczenia podczas obliczeń podczas ponownego skalowania.

Opłaty naliczane są za każdą godzinę istnienia bazy danych przy użyciu najwyższej warstwy usługi i obliczenia rozmiaru zastosowany w ciągu tej godziny, niezależnie od użycia lub tego, czy baza danych była Aktywna krócej niż godzinę. Po utworzeniu pojedynczej bazy danych i usuniesz ją 5 minut później rachunku odzwierciedla za godzinę korzystania z jednej bazy danych.

## <a name="change-storage-size"></a>Zmień rozmiar magazynu

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazynu mogą być udostępniane do limitu maksymalnego rozmiaru przy użyciu przyrostami co 1 GB. Magazyn danych można skonfigurować minimalną to 5 GB
- Magazyn dla pojedynczej bazy danych mogą być udostępniane przez zwiększenie lub zmniejszenie jego rozmiar maksymalny za pomocą [witryny Azure portal](https://portal.azure.com), [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Wiersza polecenia platformy azure](/cli/azure/sql/db#az-sql-db-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Bazy danych SQL Database automatycznie przydziela 30% dodatkowego miejsca do magazynowania plików dziennika i 32GB na rdzeń wirtualny dla bazy danych TempDB, ale nie może przekraczać 384GB. Bazy danych TempDB jest umieszczony na dołączone dyski SSD we wszystkich warstwach usługi.
- Cena przestrzeni dyskowej dla pojedynczej bazy danych jest sumę kwot dane magazynu i dziennika magazynu oraz przez cenę jednostkową magazynu w warstwie usług. Koszt bazy danych TempDB jest uwzględniona w cenie — rdzeń wirtualny. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupu w oparciu o jednostki DTU

- Cena jednostki DTU dla pojedynczej bazy danych obejmuje określoną ilość pamięci masowej bez ponoszenia dodatkowych kosztów. Dodatkowego magazynu ponad uwzględnioną kwotę można zaaprowizować za dodatkową opłatą maksymalnie limit maksymalnego rozmiaru, w przyrostach o rozmiarze 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB ponad 1 TB. Magazyn w pakiecie kwoty i limity maksymalnego rozmiaru, zobacz [pojedynczej bazy danych: Magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla pojedynczej bazy danych mogą być udostępniane przez odpowiednie zwiększenie jego maksymalny rozmiar przy użyciu portalu Azure [języka Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Cena dodatkowego magazynu dla pojedynczej bazy danych jest mnożony przez cena jednostkowa dodatkowego magazynu w warstwie usług wielkość dodatkowego magazynu. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Ograniczenia P11 i P15, gdy jest to maksymalny rozmiar większy niż 1 TB

Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach poza następującymi: Chiny Wschodnie, Chiny Północne, Niemcy Środkowe, Niemcy Północno-Wschodnie, Zachodnio-środkowe stany USA, regiony US DoD i Instytucje rządowe dla środkowych stanów USA. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB. Następujące istotne zagadnienia i ograniczenia dotyczą P11 i P15 baz danych o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli maksymalny rozmiar bazy danych P11 lub P15 nigdy nie ustawiono na wartość większą niż 1 TB, następnie można go jedynie przywrócić lub kopiowane do P11 lub P15 bazy danych.  Później bazy danych może być przeskalowywany w ten sposób rozmiarowi obliczeniowej podana ilość miejsca przydzielonego w czasie działania podczas ponownego skalowania nie przekracza maksymalnego rozmiaru limitów nowy rozmiar obliczeń.
- Aktywna replikacja geograficzna scenariuszach:
  - Definiowanie relacji replikacji geograficznej: Jeśli podstawowa baza danych jest P11 lub P15, secondary(ies) również musi być P11 lub P15; niższe rozmiaru obliczeń są odrzucane jako pomocnicze bazy danych, ponieważ nie może obsługiwać więcej niż 1 TB.
  - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: Zmiana maksymalnego rozmiaru na więcej niż 1 TB w podstawowej bazie danych wyzwala tę samą zmianę w pomocniczej bazie danych. Obu uaktualnień musi zakończyć się powodzeniem, zmiany na serwerze podstawowym zaczęły obowiązywać. Region dla opcji więcej niż 1 TB ograniczenia. Jeśli pomocniczy znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie jest uaktualniany.
- Za pomocą usługi Import/Export dla obciążenia baz danych P11/P15, za pomocą więcej niż 1 TB nie jest obsługiwana. Użyj SqlPackage.exe do [zaimportować](sql-database-import.md) i [wyeksportować](sql-database-export.md) danych.

## <a name="next-steps"></a>Kolejne kroki

Ogólne limity zasobów, zobacz [limitów zasobów opartych na rdzeniach wirtualnych bazy danych SQL — pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów na podstawie jednostek DTU bazy danych SQL — pul elastycznych](sql-database-dtu-resource-limits-single-databases.md).
