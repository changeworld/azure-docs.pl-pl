---
title: Skalowanie zasobów elastycznej puli
description: Na tej stronie opisano skalowanie zasobów dla pul elastycznych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462602"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skalowanie zasobów puli elastycznej w bazie danych SQL usługi Azure

W tym artykule opisano sposób skalowania zasobów obliczeniowych i magazynowych dostępnych dla pul elastycznych i puli baz danych w usłudze Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Zmienianie zasobów obliczeniowych (corów wirtualnych lub jednostek DTU)

Po początkowym wybraniu liczby procesorów wirtualnych lub eDTU można dynamicznie skalować pulę elastyczną w górę lub w dół na podstawie rzeczywistego środowiska przy użyciu [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool) [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)lub interfejsu API [REST.](https://docs.microsoft.com/rest/api/sql/elasticpools/update)

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Wpływ zmiany warstwy usług lub przeskalowania rozmiaru obliczeń

Zmiana warstwy usług lub rozmiar obliczeń puli elastycznej następuje podobny wzorzec jak dla pojedynczych baz danych i głównie obejmuje usługi wykonywania następujących kroków:

1. Tworzenie nowego wystąpienia obliczeń dla puli elastycznej  

    Nowe wystąpienie obliczeń dla puli elastycznej jest tworzony z żądaną warstwą usług i rozmiar obliczeń. W przypadku niektórych kombinacji warstwy usług i zmian rozmiaru obliczeń replika każdej bazy danych musi zostać utworzona w nowym wystąpieniu obliczeniowym, które obejmuje kopiowanie danych i może silnie wpływać na ogólne opóźnienie. Niezależnie od tego bazy danych pozostają w trybie online podczas tego kroku, a połączenia nadal są kierowane do baz danych w oryginalnym wystąpieniu obliczeniowym.

2. Przełączanie routingu połączeń do nowego wystąpienia obliczeniowego

    Istniejące połączenia z bazami danych w oryginalnym wystąpieniu obliczeniowym są usuwane. Wszystkie nowe połączenia są ustanawiane do baz danych w nowym wystąpieniu obliczeń. W przypadku niektórych kombinacji warstwy usług i zmian rozmiaru obliczeniowego pliki bazy danych są odłączane i ponownie dołączane podczas przełączania.  Niezależnie od tego przełącznik może spowodować krótką przerwę w działaniu usługi, gdy bazy danych są ogólnie niedostępne przez mniej niż 30 sekund i często tylko przez kilka sekund. Jeśli istnieją długotrwałe transakcje uruchomione po przerwaniu połączeń, czas trwania tego kroku może potrwać dłużej w celu odzyskania przerwanych transakcji. [Przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md) może zmniejszyć wpływ przerywania długotrwałych transakcji.

> [!IMPORTANT]
> Żadne dane nie są tracone podczas dowolnego kroku w przepływie pracy.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Opóźnienie zmiany warstwy usług lub przeskalowania rozmiaru obliczeń

Szacowane opóźnienie, aby zmienić warstwę usług lub przeskalować rozmiar obliczeń pojedynczej bazy danych lub puli elastycznej jest parametryzowany w następujący sposób:

|Warstwa usług|Podstawowa pojedyncza baza danych,</br>Standard (S0-S1)|Podstawowy basen elastyczny,</br>Norma (S2-S12), </br>Hiperskala, </br>Pojedyncza baza danych ogólnego przeznaczenia lub pula elastyczna|Pojedyncza baza danych premium lub krytyczna dla firmy lub pula elastyczna|
|:---|:---|:---|:---|
|**Podstawowa pojedyncza baza danych standardowa</br> (S0-S1)**|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanej przestrzeni</br>&bull;&nbsp;Zazwyczaj mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|
|**Podstawowa pula elastyczna, </br>standardowa </br>(S2-S12), hiperskala, </br>pojedyncza baza danych ogólnego przeznaczenia lub pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanej przestrzeni</br>&bull;&nbsp;Zazwyczaj mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|
|**Pojedyncza baza danych premium lub krytyczna dla firmy lub pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych używanego w związku z kopiowaniem danych</br>&bull;&nbsp;Zazwyczaj mniej niż 1 minuta na GB miejsca|

> [!NOTE]
>
> - W przypadku zmiany warstwy usług lub przeksztego skalowania obliczeń dla puli elastycznej sumowanie miejsca używanego we wszystkich bazach danych w puli powinno być używane do obliczania oszacowania.
> - W przypadku przenoszenia bazy danych do/z puli elastycznej tylko miejsce używane przez bazę danych ma wpływ na opóźnienie, a nie na miejsce używane przez pulę elastyczną.
>
> [!TIP]
> Aby monitorować operacje w toku, zobacz: [Zarządzanie operacjami przy użyciu interfejsu API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), Zarządzanie [operacjami przy użyciu interfejsu wiersza polecenia ,](/cli/azure/sql/db/op)Monitorowanie operacji przy użyciu funkcji [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i tych dwóch poleceń programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Dodatkowe zagadnienia podczas zmiany warstwy usług lub zmiany rozmiaru obliczeń

- Podczas zmniejszania liczby procesorów wirtualnych lub eDTU dla puli elastycznej, używana przestrzeń puli musi być mniejsza niż maksymalny dozwolony rozmiar docelowej warstwy usług i eDTU puli.
- Podczas ponownego skalowania eDTU dla puli elastycznej, dodatkowy koszt magazynu ma zastosowanie, jeśli (1) maksymalny rozmiar magazynu puli jest obsługiwany przez pulę docelową i (2) maksymalny rozmiar magazynu przekracza uwzględniony rozmiar magazynu puli docelowej. Jeśli na przykład standardowa pula 100 eDTU o maksymalnym rozmiarze 100 GB jest przeliczona na pulę standardową 50 eDTU, obowiązuje dodatkowy koszt magazynowania, ponieważ pula docelowa obsługuje maksymalny rozmiar 100 GB, a jej ilość dołączona do magazynu wynosi tylko 50 GB. Tak więc dodatkowa ilość miejsca wynosi 100 GB – 50 GB = 50 GB. Aby uzyskać informacje na o cenach dodatkowego magazynu, zobacz [Cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość używanego miejsca jest mniejsza niż uwzględniona ilość magazynu, można uniknąć tego dodatkowego kosztu, zmniejszając maksymalny rozmiar bazy danych do uwzględnionej kwoty.

### <a name="billing-during-rescaling"></a>Rozliczenia podczas ponownego skalowania

Opłaty są naliczane za każdą godzinę istnienia bazy danych przy użyciu najwyższej warstwy usług + rozmiaru obliczeń, który został zastosowany w tej godzinie, niezależnie od użycia lub tego, czy baza danych była aktywna przez mniej niż godzinę. Na przykład jeśli utworzysz pojedynczą bazę danych i usuniesz ją pięć minut później, rachunek będzie odzwierciedlał opłatę za jedną godzinę bazy danych.

## <a name="change-elastic-pool-storage-size"></a>Zmiana rozmiaru elastycznego magazynu w puli

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazyn może być aprowizowana do maksymalnego limitu rozmiaru:

  - W przypadku przechowywania w warstwach usług standardowego lub ogólnego przeznaczenia zwiększ lub zmniejsz rozmiar w przyrostach o 10 GB
  - W przypadku pamięci masowej w warstwach usług o krytycznym znaczeniu dla klasy premium lub biznesowej zwiększ lub zmniejsz rozmiar w przyrostach o 250 GB
- Magazyn dla puli elastycznej można aprowizować przez zwiększenie lub zmniejszenie jego maksymalny rozmiar.
- Cena magazynu dla puli elastycznej jest kwota magazynu pomnożona przez cenę jednostkową magazynu warstwy usług. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, zobacz [cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupów oparty na UTU

- Cena eDTU dla elastycznej puli obejmuje pewną ilość miejsca do przechowywania bez dodatkowych kosztów. Dodatkowa pamięć masowa przekraczająca uwzględniona kwota może być aprowizowana dla dodatkowych kosztów do maksymalnego limitu rozmiaru w przyrostach od 250 GB do 1 TB, a następnie w przyrostach 256 GB powyżej 1 TB. Aby uzyskać uwzględniony zakres magazynowania i maksymalne limity rozmiaru, zobacz [Pula elastyczna: rozmiary magazynu i rozmiary obliczeń](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla puli elastycznej można aprowizować, zwiększając jego maksymalny rozmiar za pomocą [portalu Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [programu PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool) [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)lub interfejsu API [REST.](https://docs.microsoft.com/rest/api/sql/elasticpools/update)
- Cena dodatkowego miejsca do magazynowania dla puli elastycznej jest dodatkową ilością magazynu pomnożoną przez dodatkową cenę jednostkową magazynu w warstwie usługi. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, zobacz [cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne limity zasobów, zobacz [Limity zasobów oparte na polach vCore bazy danych SQL — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md) i [limity zasobów oparte na udu bazy danych SQL — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md).
