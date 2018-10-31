---
title: Skalowanie elastycznej puli zasobów — Azure SQL Database | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis skalowanie zasobów dla pul elastycznych usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: cd9886f11685397cbfb82f88bb0b37c8ccc41b67
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240175"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skalowanie elastycznej puli zasobów w usłudze Azure SQL Database

W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe, które muszą być dostępne dla elastycznych pul i baz danych w puli usługi Azure SQL Database.

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>modelu zakupu opartego na rdzeniach wirtualnych: Zmień rozmiar magazynu puli elastycznej

- Magazyn można aprowizować maksymalnie limit maksymalnego rozmiaru:

  - Dla magazynu w warstwie standardowa należy zwiększyć lub zmniejszyć rozmiar w przyrostach co 10 GB
  - Dla usługi Premium storage należy zwiększyć lub zmniejszyć rozmiar w partiach po 250 GB
- Magazyn dla elastycznej puli mogą być udostępniane przez zwiększenie lub zmniejszenie jego rozmiar maksymalny.
- Cena przestrzeni dyskowej dla puli elastycznej jest wielkość magazynu, oraz przez cenę jednostkową magazynu w warstwie usług. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>modelu zakupu opartego na rdzeniach wirtualnych: zasoby (rdzenie wirtualne) obliczeniowe zmiany puli elastycznej

Można zwiększyć lub zmniejszyć rozmiar obliczeń do puli elastycznej oparte na zasób, używając wymaga [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

- Kiedy podczas ponownego skalowania rdzeni wirtualnych w puli elastycznej, połączenia z bazą danych krótko są usuwane. To zachowanie jest takie samo zachowanie, które występuje, gdy podczas ponownego skalowania liczby jednostek Dtu dla pojedynczej bazy danych. Aby uzyskać szczegółowe informacje dotyczące czasu trwania i wpływ przerwanie połączenia dla bazy danych podczas wykonywania operacji podczas ponownego skalowania, zobacz [Zmień obliczenia zasobów (Dtu)](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- Czas trwania, aby zmienić rdzeni wirtualnych w puli może zależeć od całkowitej ilości miejsca do magazynowania używane przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, opóźnienie podczas ponownego skalowania uśrednia 90 minut na każde 100 GB. Na przykład jeżeli całkowita ilość miejsca jest używany przez wszystkie bazy danych w puli wynosi 200 GB, oczekiwany czas oczekiwania na ponowne skalowanie puli jest 3 godziny lub mniej. W niektórych przypadkach w ramach warstwy Standard lub Basic opóźnienia podczas ponownego skalowania może być mniej niż pięć minut niezależnie od ilości miejsca.
- Ogólnie rzecz biorąc, czas trwania, aby zmienić rdzeni wirtualnych min na bazie danych lub maksymalna liczba rdzeni wirtualnych na bazę danych to pięć minut lub mniej.
- Gdy zmniejszenie rozmiaru puli rdzeni wirtualnych, przestrzeni puli musi być mniejszy niż maksymalny dozwolony rozmiar docelowej usługi warstwy i puli rdzeni wirtualnych.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Model zakupu w oparciu o jednostki DTU: Zmień rozmiar magazynu puli elastycznej

- Cena jednostki eDTU dla puli elastycznej obejmuje określoną ilość pamięci masowej bez ponoszenia dodatkowych kosztów. Dodatkowego magazynu ponad uwzględnioną kwotę można zaaprowizować za dodatkową opłatą maksymalnie limit maksymalnego rozmiaru, w przyrostach o rozmiarze 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB ponad 1 TB. Magazyn w pakiecie kwoty i limity maksymalnego rozmiaru, zobacz [puli elastycznej: magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla elastycznej puli mogą być udostępniane przez odpowiednie zwiększenie jego maksymalnego rozmiaru za pomocą [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), lub [interfejsu API REST ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Cena dodatkowego magazynu dla puli elastycznej jest mnożony przez cena jednostkowa dodatkowego magazynu w warstwie usług wielkość dodatkowego magazynu. Szczegółowe informacje na temat cena magazynu dodatkowego, [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Model zakupu w oparciu o jednostki DTU: zasoby (Edtu) obliczeniowe zmiany puli elastycznej

Można zwiększyć lub zmniejszyć zasoby dostępne dla elastycznej puli w oparciu o zasobu wymaga za pomocą [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

- Kiedy podczas ponownego skalowania jednostki Edtu puli, połączenia z bazą danych krótko są usuwane. To zachowanie jest takie samo zachowanie, które występuje, gdy podczas ponownego skalowania liczby jednostek Dtu dla pojedynczej bazy danych. Aby uzyskać szczegółowe informacje dotyczące czasu trwania i wpływ przerwanie połączenia dla bazy danych podczas wykonywania operacji podczas ponownego skalowania, zobacz [Zmień obliczenia zasobów (Dtu)](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus).
- Czas trwania, aby zmienić liczbę jednostek Edtu puli mogą zależeć od całkowitej ilości miejsca do magazynowania używane przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, opóźnienie podczas ponownego skalowania uśrednia 90 minut na każde 100 GB. Na przykład jeżeli całkowita ilość miejsca jest używany przez wszystkie bazy danych w puli wynosi 200 GB, oczekiwany czas oczekiwania na ponowne skalowanie puli jest 3 godziny lub mniej. W niektórych przypadkach w ramach warstwy Standard lub Basic opóźnienia podczas ponownego skalowania może być mniej niż pięć minut niezależnie od ilości miejsca.
- Ogólnie rzecz biorąc, czas trwania, aby zmienić minimalnej liczby jednostek Edtu na bazę danych lub maksymalnej liczby jednostek Edtu na bazę danych to pięć minut lub mniej.
- Gdy zmniejszenie rozmiaru jednostek Edtu dla puli elastycznej, miejsca puli musi być mniejszy niż maksymalny dozwolony rozmiar docelowej usługi warstwy i puli jednostek Edtu.
- Gdy podczas ponownego skalowania jednostek Edtu dla puli elastycznej, koszty dodatkowego magazynu w przypadku (1) maksymalny rozmiar magazynu puli jest obsługiwana przez docelową pulę, a (2) maksymalny rozmiar magazynu przekracza wielkość magazynu w pakiecie docelową pulę. Na przykład, jeśli 100 jednostek eDTU puli standardowej, maksymalny rozmiar 100 GB, jest downsized do 50 jednostek eDTU puli standardowej, następnie magazyn dodatkowy koszt dotyczy ponieważ puli docelowej obsługuje maksymalny rozmiar 100 GB, a jego dostępnej ilości magazynu jest tylko 50 GB. Więc wielkość dodatkowego magazynu to 100 GB – 50 GB = 50 GB. Cennik dodatkowego magazynu, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywistą ilość miejsca jest mniejsza niż wielkość magazynu w pakiecie, następnie to dodatkowych kosztów można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględnioną kwotę.

## <a name="next-steps"></a>Kolejne kroki

Ogólne limity zasobów, zobacz [limitów zasobów opartych na rdzeniach wirtualnych bazy danych SQL — pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md) i [limity zasobów na podstawie jednostek DTU bazy danych SQL — pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md).
