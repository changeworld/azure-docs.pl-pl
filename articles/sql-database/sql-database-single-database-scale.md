---
title: Skalowanie zasobów pojedynczej bazy danych
description: W tym artykule opisano sposób skalowania zasobów obliczeniowych i magazynowych dostępnych dla pojedynczej bazy danych w bazie danych SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061595"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalowanie zasobów pojedynczej bazy danych w bazie danych SQL usługi Azure

W tym artykule opisano sposób skalowania zasobów obliczeniowych i magazynowych dostępnych dla bazy danych SQL azure w warstwie obliczeniowej aprowizowanego. Alternatywnie [warstwa obliczeniowa bez użycia serwera](sql-database-serverless.md) zapewnia automatyczne skalowanie obliczeniowe i rachunki na sekundę dla obliczeń używanych.

Po początkowym wybraniu liczby procesorów wirtualnych lub jednostek DTU można dynamicznie skalować pojedynczą bazę danych w górę lub w dół na podstawie rzeczywistego doświadczenia przy użyciu [portalu Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)Portal , [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update)lub interfejsu API [REST](https://docs.microsoft.com/rest/api/sql/databases/update).

Poniższy film pokazuje dynamicznie zmieniającą warstwę usług i rozmiar obliczeń, aby zwiększyć dostępne procesory DTU dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="impact"></a>Wpływ

Zmiana warstwy usług lub rozmiaru obliczeń polega głównie na wykonaniu przez usługę następujących kroków:

1. Tworzenie nowego wystąpienia obliczeń dla bazy danych  

    Nowe wystąpienie obliczeń jest tworzony z żądaną warstwą usług i rozmiar obliczeń. W przypadku niektórych kombinacji warstwy usług i zmian rozmiaru obliczeń replika bazy danych musi zostać utworzona w nowym wystąpieniu obliczeniowym, które obejmuje kopiowanie danych i może mieć duży wpływ na ogólne opóźnienie. Niezależnie od tego baza danych pozostaje w trybie online podczas tego kroku, a połączenia nadal są kierowane do bazy danych w oryginalnym wystąpieniu obliczeniowym.

2. Przełączanie routingu połączeń do nowego wystąpienia obliczeniowego

    Istniejące połączenia z bazą danych w oryginalnym wystąpieniu obliczeniowym są usuwane. Wszystkie nowe połączenia są ustanawiane do bazy danych w nowym wystąpieniu obliczeń. W przypadku niektórych kombinacji warstwy usług i zmian rozmiaru obliczeniowego pliki bazy danych są odłączane i ponownie dołączane podczas przełączania.  Niezależnie od tego przełącznik może spowodować krótką przerwę w działaniu usługi, gdy baza danych jest ogólnie niedostępna przez mniej niż 30 sekund i często tylko przez kilka sekund. Jeśli istnieją długotrwałe transakcje uruchomione po przerwaniu połączeń, czas trwania tego kroku może potrwać dłużej w celu odzyskania przerwanych transakcji. [Przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md) może zmniejszyć wpływ przerywania długotrwałych transakcji.

> [!IMPORTANT]
> Żadne dane nie są tracone podczas dowolnego kroku w przepływie pracy. Upewnij się, że zaimplementowano niektóre [logiki ponawiania prób](sql-database-connectivity-issues.md) w aplikacjach i składnikach, które używają usługi Azure SQL Database, gdy warstwa usługi zostanie zmieniona.

## <a name="latency"></a>Opóźnienie 

Szacowane opóźnienie, aby zmienić warstwę usług lub przeskalować rozmiar obliczeń pojedynczej bazy danych lub puli elastycznej jest parametryzowany w następujący sposób:

|Warstwa usług|Podstawowa pojedyncza baza danych,</br>Standard (S0-S1)|Podstawowy basen elastyczny,</br>Norma (S2-S12), </br>Hiperskala, </br>Pojedyncza baza danych ogólnego przeznaczenia lub pula elastyczna|Pojedyncza baza danych premium lub krytyczna dla firmy lub pula elastyczna|
|:---|:---|:---|:---|
|**Podstawowa pojedyncza baza danych standardowa</br> (S0-S1)**|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanej przestrzeni</br>&bull;&nbsp;Zazwyczaj mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|
|**Podstawowa pula elastyczna, </br>standardowa </br>(S2-S12), hiperskala, </br>pojedyncza baza danych ogólnego przeznaczenia lub pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanej przestrzeni</br>&bull;&nbsp;Zazwyczaj mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|
|**Pojedyncza baza danych premium lub krytyczna dla firmy lub pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|

> [!TIP]
> Aby monitorować operacje w toku, zobacz: [Zarządzanie operacjami przy użyciu interfejsu API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), Zarządzanie [operacjami przy użyciu interfejsu wiersza polecenia ,](/cli/azure/sql/db/op)Monitorowanie operacji przy użyciu funkcji [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i tych dwóch poleceń programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Anulowanie zmian

Można anulować zmianę warstwy usługi lub operację ponownego skalowania obliczeń.

#### <a name="azure-portal"></a>Portal Azure

W bloku przeglądu bazy danych przejdź do **pozycji Powiadomienia** i kliknij kafelek wskazujący, że trwa operacja:

![Trwająca operacja](media/sql-database-single-database-scale/ongoing-operations.png)

Następnie kliknij przycisk **Anuluj tę operację**.

![Anulowanie trwającej operacji](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

W wierszu polecenia programu PowerShell ustaw polecenie `$resourceGroupName`, `$serverName`i `$databaseName`, a następnie uruchom następujące polecenie:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

- W przypadku uaktualniania do wyższej warstwy usług lub rozmiaru obliczeniowego maksymalny rozmiar bazy danych nie zwiększa się, chyba że jawnie określisz większy rozmiar (maxsize).
- Aby obniżyć poziom bazy danych, baza danych używane miejsce musi być mniejsza niż maksymalny dozwolony rozmiar warstwy usługi docelowej i rozmiar obliczeń.
- Podczas obniżania wersji **premium** do warstwy **Standardowa,** dodatkowy koszt magazynu ma zastosowanie, jeśli oba (1) maksymalny rozmiar bazy danych jest obsługiwany w docelowym rozmiarze obliczeń i (2) maksymalny rozmiar przekracza uwzględniony rozmiar magazynu docelowego rozmiaru obliczeń. Na przykład jeśli baza danych P1 o maksymalnym rozmiarze 500 GB jest pomniejszona o rozmiar do S3, obowiązuje dodatkowy koszt magazynowania, ponieważ S3 obsługuje maksymalny rozmiar 1 TB, a jego ilość magazynu jest tylko 250 GB. Tak więc dodatkowa ilość miejsca wynosi 500 GB – 250 GB = 250 GB. Aby uzyskać informacje na o cenach dodatkowego magazynu, zobacz [Cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość używanego miejsca jest mniejsza niż uwzględniona ilość magazynu, można uniknąć tego dodatkowego kosztu, zmniejszając maksymalny rozmiar bazy danych do uwzględnionej kwoty.
- Podczas uaktualniania bazy danych z [włączoną replikacją geograficzną](sql-database-geo-replication-portal.md) uaktualnij jej pomocnicze bazy danych do żądanej warstwy usług i rozmiar obliczeń przed uaktualnieniem podstawowej bazy danych (ogólne wskazówki dotyczące najlepszej wydajności). Podczas uaktualniania do innej wersji, jest wymagane, że pomocnicza baza danych jest uaktualniany najpierw.
- Podczas obniżania poziomu bazy danych z [włączoną replikacją geograficzną](sql-database-geo-replication-portal.md) należy obniżyć jego podstawowe bazy danych do żądanej warstwy usług i rozmiaru obliczeń przed obniżeniem poziomu pomocniczej bazy danych (ogólne wskazówki dotyczące najlepszej wydajności). Podczas obniżania wersji do innej wersji, jest wymagane, że podstawowa baza danych jest najpierw obniżona.
- Oferowane usługi przywracania różnią się w zależności do warstwy usługi. Jeśli przechodzisz na dół do warstwy **podstawowej,** istnieje niższy okres przechowywania kopii zapasowej. Zobacz [tworzenie kopii zapasowych bazy danych SQL platformy Azure](sql-database-automated-backups.md).
- Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="billing"></a>Rozliczenia 

Opłaty są naliczane za każdą godzinę istnienia bazy danych przy użyciu najwyższej warstwy usług + rozmiaru obliczeń, który został zastosowany w tej godzinie, niezależnie od użycia lub tego, czy baza danych była aktywna przez mniej niż godzinę. Na przykład jeśli utworzysz pojedynczą bazę danych i usuniesz ją pięć minut później, rachunek będzie odzwierciedlał opłatę za jedną godzinę bazy danych.

## <a name="change-storage-size"></a>Zmienianie rozmiaru magazynu

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazyn może być aprowizowana do maksymalnego limitu rozmiaru magazynu danych przy użyciu przyrostów 1 GB. Minimalna konfigurowalna pamięć masowa danych wynosi 1 GB. Zobacz strony dokumentacji limitu zasobów dla [pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [pul elastycznych dla maksymalnych limitów](sql-database-vcore-resource-limits-elastic-pools.md) rozmiaru magazynu danych w każdym celu usługi.
- Przechowywanie danych dla pojedynczej bazy danych można aprowizować, zwiększając lub zmniejszając jego maksymalny rozmiar za pomocą [portalu Azure,](https://portal.azure.com) [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update)lub interfejsu API [REST.](https://docs.microsoft.com/rest/api/sql/databases/update) Jeśli maksymalna wartość rozmiaru jest określona w bajtach, musi to być wielokrotność 1 GB (1073741824 bajtów).
- Ilość danych, które mogą być przechowywane w plikach danych bazy danych jest ograniczona przez skonfigurowany maksymalny rozmiar magazynu danych. Oprócz tego magazynu bazy danych SQL automatycznie przydziela 30% więcej miejsca do użycia w dzienniku transakcji.
- Baza danych SQL automatycznie przydziela 32 GB `tempdb` na rów wirtualny dla bazy danych. `tempdb`znajduje się na lokalnym magazynie SSD we wszystkich warstwach usług.
- Cena magazynu dla pojedynczej bazy danych lub puli elastycznej jest sumą ilości magazynu danych i magazynu dziennika transakcji pomnożone przez cenę jednostkową magazynu warstwy usług. Koszt `tempdb` jest wliczony w cenę. Aby uzyskać szczegółowe informacje na temat ceny magazynu, zobacz [cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupów oparty na UTU

- Cena jednostek DTU dla pojedynczej bazy danych zawiera pewną ilość miejsca do magazynowania bez dodatkowych kosztów. Dodatkowa pamięć masowa przekraczająca uwzględniona kwota może być aprowizowana dla dodatkowych kosztów do maksymalnego limitu rozmiaru w przyrostach od 250 GB do 1 TB, a następnie w przyrostach 256 GB powyżej 1 TB. Aby uzyskać uwzględnione kwoty magazynu i maksymalne limity rozmiaru, zobacz [Pojedyncza baza danych: Rozmiary magazynu i rozmiary obliczeń](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla pojedynczej bazy danych można aprowizować, zwiększając jej maksymalny rozmiar za pomocą portalu Azure, [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [programu PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update)lub interfejsu API [REST.](https://docs.microsoft.com/rest/api/sql/databases/update)
- Cena dodatkowego miejsca do magazynowania dla pojedynczej bazy danych jest dodatkową kwotą magazynu pomnożoną przez dodatkową cenę jednostkową magazynu w warstwie usługi. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, zobacz [cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="geo-replicated-database"></a>Replikowana geograficznie baza danych

Aby zmienić rozmiar bazy danych replikowanej pomocniczej bazy danych, zmień rozmiar podstawowej bazy danych. Ta zmiana zostanie następnie zreplikowana i zaimplementowana również w pomocniczej bazie danych. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Wiązania P11 i P15 o maksymalnym rozmiarze większym niż 1 TB

Ponad 1 TB pamięci masowej w warstwie Premium jest obecnie dostępna we wszystkich regionach z wyjątkiem: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regiony i us Government Central. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB. Następujące zagadnienia i ograniczenia dotyczą baz danych P11 i P15 o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli maksymalny rozmiar bazy danych P11 lub P15 został kiedykolwiek ustawiony na wartość większą niż 1 TB, można ją przywrócić lub skopiować tylko do bazy danych P11 lub P15.  Następnie bazy danych można przeskalować do innego rozmiaru obliczeń, pod warunkiem, że ilość miejsca przydzielonego w czasie operacji ponownego skalowania nie przekracza maksymalnych limitów rozmiaru nowego rozmiaru obliczeń.
- W przypadku aktywnych scenariuszy replikacji geograficznej:
  - Konfigurowanie relacji replikacji geograficznej: Jeśli podstawową bazą danych jest P11 lub P15, pomocnicze(-y) muszą być również P11 lub P15; mniejszy rozmiar obliczeń są odrzucane jako pomocnicze, ponieważ nie są w stanie obsługiwać więcej niż 1 TB.
  - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: Zmiana maksymalnego rozmiaru na więcej niż 1 TB w podstawowej bazie danych powoduje taką samą zmianę w pomocniczej bazie danych. Oba uaktualnienia muszą zakończyć się pomyślnie, aby zmiana w podstawowej została wniesiena. Obowiązują ograniczenia regionu dla opcji więcej niż 1 TB. Jeśli pomocniczy znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie jest uaktualniany.
- Korzystanie z usługi Import/Eksportuj do ładowania baz danych P11/P15 z więcej niż 1 TB nie jest obsługiwane. Program SqlPackage.exe służy do [importowania](sql-database-import.md) i [eksportowania](sql-database-export.md) danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne limity zasobów, zobacz [Limity zasobów oparte na polach vCore bazy danych SQL — pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów oparte na jednostce DTU bazy danych SQL — pule elastyczne](sql-database-dtu-resource-limits-single-databases.md).
