---
title: Skalowanie elastycznej puli zasobów — Azure SQL Database | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis skalowanie zasobów dla pul elastycznych usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: f73fc58abfa6cde4133bd56858b7f26bf0c3d4a3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204811"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skalowanie elastycznej puli zasobów w usłudze Azure SQL Database

W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe, które muszą być dostępne dla elastycznych pul i baz danych w puli usługi Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Zasoby obliczeniowe zmiany (rdzeni wirtualnych lub jednostek Dtu)

Po początkowym wybraniu liczbę rdzeni wirtualnych lub jednostek Edtu, należy można elastycznej puli w górę lub dół dynamicznie skalować na podstawie rzeczywistego użycia za pomocą [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [wiersza polecenia platformy Azure ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Wpływ zmiany rozmiaru obliczeń warstwy lub ponowne skalowanie usługi

Zmiana usługi warstwy lub obliczenia rozmiaru puli elastycznej jest zgodna ze wzorcem podobnie jak w przypadku pojedynczych baz danych i głównie wiąże się usługi, wykonując następujące czynności:

1. Tworzenie nowego wystąpienia obliczeniowe dla puli elastycznej  

    Nowe wystąpienie obliczeniowe dla puli elastycznej jest tworzony z warstwy żądanej usługi a rozmiar obliczeń. Dla niektórych kombinacji warstwę usługi i zmiany rozmiaru obliczeń replik każdej bazy danych należy utworzyć nowe wystąpienie obliczeniowe, które polega na kopiowaniu danych i zdecydowanie mogą mieć wpływ na ogólny czas oczekiwania. Niezależnie od tego baz danych pozostanie w trybie online w tym kroku i połączeń w dalszym ciągu nastąpi przekierowanie do baz danych w oryginalnym wystąpieniu obliczeń.

2. Przełącz routing połączeń do nowego wystąpienia obliczeniowego

    Istniejące połączenia z bazami danych w oryginalnym wystąpieniu obliczeniowych są porzucane. Wszystkie nowe połączenia są określane w bazach danych w nowe wystąpienie obliczeniowe. Niektóre kombinacje warstwy usług i zmiany rozmiaru obliczeń pliki bazy danych są odłączone i ponownie dołączyć podczas przełączania.  Niezależnie od tego przełącznik może spowodować przerwa w świadczeniu usługi krótki, jeśli bazy danych nie są dostępne, zazwyczaj mniej niż 30 sekund i często tylko kilka sekund. W przypadku długotrwałych transakcji uruchomiony podczas połączenia zostaną przerwane, czas trwania tego kroku może trwać dłużej, w celu odzyskania przerwane transakcje. [Przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md) zmniejszyć wpływ w przerywania długotrwałych transakcji.

> [!IMPORTANT]
> Żadne dane nie zostaną utracone podczas dowolny krok w przepływie pracy.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Opóźnienie zmiany rozmiaru obliczeń warstwy lub ponowne skalowanie usługi

Szacowany czas oczekiwania, aby zmienić warstwę usługi lub zmienić rozmiar obliczeń pojedynczą bazę danych lub elastycznej puli jest sparametryzowane w następujący sposób:

|Warstwa usług|Podstawowe pojedynczej bazy danych</br>Standardowa (S0 — S1)|Elastyczna pula podstawowa</br>Standard (S2-S12) </br>W Hiperskali </br>Ogólne pojedynczą bazę danych przeznaczenia lub puli elastycznej|Premium lub krytyczne dla działania firmy pojedynczą bazę danych lub puli elastycznej|
|:---|:---|:---|:---|
|**Podstawowe pojedynczej bazy danych,</br> standardowa (S0 — S1)**|&bull; &nbsp;Niezależnie od miejsca używanego w stałym czasie opóźnienia</br>&bull; &nbsp;Zwykle, mniej niż 5 minut|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|
|**Podstawowych pul elastycznych, </br>Standard (S2 S12), </br>w Hiperskali </br>ogólnego przeznaczenia pojedynczej bazy danych lub puli elastycznej**|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Niezależnie od miejsca używanego w stałym czasie opóźnienia</br>&bull; &nbsp;Zwykle, mniej niż 5 minut|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|
|**Premium lub krytyczne dla działania firmy pojedynczą bazę danych lub puli elastycznej**|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|&bull; &nbsp;Opóźnienie proporcjonalne do miejsca w bazie danych stosowany ze względu na kopiowanie danych</br>&bull; &nbsp;Zwykle, mniej niż minutę za GB miejsca|

> [!NOTE]
>
> - W przypadku zmiana warstwy usługi lub ponowne skalowanie obliczeń dla puli elastycznej, suma miejsca używanego we wszystkich bazach danych w puli powinno być używane do obliczania szacowania.
> - W przypadku przenoszenia bazy danych z puli elastycznej, tylko przestrzeni używanej przez bazy danych ma wpływ na opóźnienia, a nie miejsca z puli elastycznej.
>
> [!TIP]
> Aby monitorować działania w toku, zobacz: [Zarządzanie operacjami za pomocą interfejsu API REST programu SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [zarządzania operacjami usługi przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwa polecenia programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [Stop AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Dodatkowe zagadnienia podczas zmiany usługi warstwy lub podczas ponownego skalowania rozmiaru obliczeń

- Gdy zmniejszenie rozmiaru rdzeni wirtualnych lub jednostek Edtu dla puli elastycznej, miejsca puli musi być mniejszy niż maksymalny dozwolony rozmiar docelowej usługi warstwy i puli jednostek Edtu.
- Gdy podczas ponownego skalowania rdzeni wirtualnych lub jednostek Edtu dla puli elastycznej, koszty dodatkowego magazynu w przypadku (1) maksymalny rozmiar magazynu puli jest obsługiwana przez docelową pulę, a (2) maksymalny rozmiar magazynu przekracza wielkość magazynu w pakiecie docelową pulę. Na przykład, jeśli 100 jednostek eDTU puli standardowej, maksymalny rozmiar 100 GB, jest downsized do 50 jednostek eDTU puli standardowej, następnie magazyn dodatkowy koszt dotyczy ponieważ puli docelowej obsługuje maksymalny rozmiar 100 GB, a jego dostępnej ilości magazynu jest tylko 50 GB. Więc wielkość dodatkowego magazynu to 100 GB – 50 GB = 50 GB. Cennik dodatkowego magazynu, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywistą ilość miejsca jest mniejsza niż wielkość magazynu w pakiecie, następnie to dodatkowych kosztów można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględnioną kwotę.

### <a name="billing-during-rescaling"></a>Rozliczenia podczas podczas ponownego skalowania

Opłaty naliczane są za każdą godzinę istnienia bazy danych przy użyciu najwyższej warstwy usługi i obliczenia rozmiaru zastosowany w ciągu tej godziny, niezależnie od użycia lub tego, czy baza danych była Aktywna krócej niż godzinę. Po utworzeniu pojedynczej bazy danych i usuniesz ją 5 minut później rachunku odzwierciedla za godzinę korzystania z jednej bazy danych.

## <a name="change-elastic-pool-storage-size"></a>Zmień rozmiar magazynu puli elastycznej

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazyn można aprowizować maksymalnie limit maksymalnego rozmiaru:

  - W przypadku usługi storage w warstwach usług standardowa lub ogólnego przeznaczenia należy zwiększyć lub zmniejszyć rozmiar w przyrostach co 10 GB
  - Dla magazynu w warstwie premium lub krytyczne dla warstwy usług, zwiększania lub zmniejszania rozmiaru w partiach po 250 GB
- Magazyn dla elastycznej puli mogą być udostępniane przez zwiększenie lub zmniejszenie jego rozmiar maksymalny.
- Cena przestrzeni dyskowej dla puli elastycznej jest wielkość magazynu, oraz przez cenę jednostkową magazynu w warstwie usług. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupu w oparciu o jednostki DTU

- Cena jednostki eDTU dla puli elastycznej obejmuje określoną ilość pamięci masowej bez ponoszenia dodatkowych kosztów. Dodatkowego magazynu ponad uwzględnioną kwotę można zaaprowizować za dodatkową opłatą maksymalnie limit maksymalnego rozmiaru, w przyrostach o rozmiarze 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB ponad 1 TB. Magazyn w pakiecie kwoty i limity maksymalnego rozmiaru, zobacz [puli elastycznej: magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla elastycznej puli mogą być udostępniane przez odpowiednie zwiększenie jego maksymalnego rozmiaru za pomocą [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), lub [interfejsu API REST ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Cena dodatkowego magazynu dla puli elastycznej jest mnożony przez cena jednostkowa dodatkowego magazynu w warstwie usług wielkość dodatkowego magazynu. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Kolejne kroki

Ogólne limity zasobów, zobacz [limitów zasobów opartych na rdzeniach wirtualnych bazy danych SQL — pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md) i [limity zasobów na podstawie jednostek DTU bazy danych SQL — pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md).
