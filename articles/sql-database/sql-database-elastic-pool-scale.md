---
title: Skalowanie elastycznej puli zasobów — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Na tej stronie opisano skalowania zasobów dla pul elastycznych w bazie danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 0d15382f413485ccc287bac945b3c88eb748a9f6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313318"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Pula elastyczna zasoby są skalowane w bazie danych SQL Azure

W tym artykule opisano sposób skalowania dostępnych puli baz danych i elastyczne pule zasobów obliczeniowych i przestrzeni dyskowej w bazie danych SQL Azure. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>na podstawie vCore model kupna: Zmień rozmiar magazynu puli elastycznej

- Magazyn można udostępnić w granicach maksymalnej długości: 
 - Dla magazynu w warstwie standardowa Zwiększ lub Zmniejsz rozmiar w przyrostach 10 GB
 - Dla usługi Premium storage Zwiększ lub Zmniejsz rozmiar w przyrostach 250 GB
- Magazyn dla puli elastycznej można udostępnić, zwiększ lub zmniejsz jego rozmiar maksymalny.
- Cena magazynu dla puli elastycznej jest wielkość magazynu pomnożona przez cenie jednostkowej magazynu warstwy usług. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>na podstawie vCore model kupna: Zmień elastyczną pulę obliczeniowe zasobów (vCores)

Można zwiększyć lub zmniejszyć poziom wydajności puli elastycznej oparte na potrzeby przy użyciu zasobów [portalu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [Interfejsu API REST](/rest/api/sql/elasticpools/update).

- Kiedy ponowne skalowanie vCores puli, połączenia z bazą danych krótko są usuwane. To samo, jak występuje, gdy ponowne skalowanie Dtu dla pojedynczej bazy danych (nie w puli). Aby uzyskać szczegółowe informacje na czas trwania i wpływu przerwanie połączenia dla bazy danych podczas operacji ponowne skalowanie, zobacz [ponowne skalowanie jednostek Dtu dla pojedynczej bazy danych](#single-database-change-storage-size). 
- Czas trwania, aby zmienić vCores puli może zależeć od całkowitej ilości miejsca używanego przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, rescaling opóźnienia oblicza średnią 90 minut lub mniej na 100 GB. Na przykład jeśli całkowita ilość miejsca, używany przez wszystkie bazy danych w puli jest 200 GB, oczekiwany czas oczekiwania na ponowne skalowanie puli wynosi 3 godziny, a następnie lub mniej. W niektórych przypadkach w ramach warstwy standardowa lub Basic rescaling czas oczekiwania może być niezależnie od ilości miejsca używanego w obszarze pięć minut.
- Ogólnie rzecz biorąc, czas trwania, aby zmienić vCores min na bazy danych lub max vCores dla jednej bazy danych to pięć minut lub mniej.
- Gdy redukcję zatrudnienia vCores puli miejsca puli musi być mniejszy niż maksymalny dozwolony rozmiar vCores warstwy i puli usługi docelowej.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Na podstawie jednostek dtu w warstwie model kupna: Zmień rozmiar magazynu puli elastycznej

- Cena eDTU dla elastycznej puli obejmuje określoną ilość magazynu bez ponoszenia dodatkowych kosztów. Dodatkowe miejsce do magazynowania poza uwzględniona ilość można udostępnić dla dodatkowych kosztów w granicach maksymalnej długości w przyrostach 250 GB do 1 TB, a następnie w przyrostach 256 GB poza 1 TB. Dla kwoty uwzględnione magazynu i limity maksymalny rozmiar, zobacz [puli elastycznej: magazyn o rozmiarze i poziomy wydajności](#elastic-pool-storage-sizes-and-performance-levels).
- Dodatkowe miejsce do magazynowania dla puli elastycznej można udostępnić przez odpowiednie zwiększenie jego maksymalny rozmiar przy użyciu [portalu Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [interfejsu API REST ](/rest/api/sql/elasticpools/update).
- Cena dodatkowe miejsce do magazynowania dla puli elastycznej jest dodatkowe miejsce do magazynowania wielkość pomnożona przez cenie jednostkowej dodatkowe miejsce do magazynowania warstwy usług. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Na podstawie jednostek dtu w warstwie model kupna: Zmień elastyczną pulę obliczeniowe zasobów (Edtu)

Można zwiększyć lub zmniejszyć zasoby dostępne dla puli elastycznej oparte na potrzeby przy użyciu zasobów [portalu Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [ Interfejs API REST](/rest/api/sql/elasticpools/update).

- Kiedy ponowne skalowanie jednostek Edtu puli, połączenia z bazą danych krótko są usuwane. To samo, jak występuje, gdy ponowne skalowanie Dtu dla pojedynczej bazy danych (nie w puli). Aby uzyskać szczegółowe informacje na czas trwania i wpływu przerwanie połączenia dla bazy danych podczas operacji ponowne skalowanie, zobacz [ponowne skalowanie jednostek Dtu dla pojedynczej bazy danych](#single-database-change-storage-size). 
- Czas trwania, aby zmienić jednostek Edtu puli może zależeć od całkowitej ilości miejsca używanego przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, rescaling opóźnienia oblicza średnią 90 minut lub mniej na 100 GB. Na przykład jeśli całkowita ilość miejsca, używany przez wszystkie bazy danych w puli jest 200 GB, oczekiwany czas oczekiwania na ponowne skalowanie puli wynosi 3 godziny, a następnie lub mniej. W niektórych przypadkach w ramach warstwy standardowa lub Basic rescaling czas oczekiwania może być niezależnie od ilości miejsca używanego w obszarze pięć minut.
- Ogólnie rzecz biorąc, czas trwania, aby zmienić wartości min Edtu na bazę danych lub maksymalna liczba jednostek Edtu na bazę danych to pięć minut lub mniej.
- Gdy redukcję zatrudnienia jednostek Edtu puli miejsca puli musi być mniejszy niż maksymalny dozwolony rozmiar Edtu warstwy i puli usługi docelowej.
- Gdy ponowne skalowanie jednostek Edtu puli, kosztów dodatkowe miejsce do magazynowania w przypadku magazynu (1 maksymalny rozmiar puli jest obsługiwana przez docelową pulę i (2 maksymalny rozmiar magazynu przekracza wielkość magazynu dołączone docelową pulę. Na przykład jeśli 100 jednostek eDTU puli standardowej, maksymalny rozmiar 100 GB jest downsized do 50 eDTU puli standardowej, to koszt dodatkowe miejsce do magazynowania jest stosowany od docelową pulę obsługuje maksymalny rozmiar 100 GB, a jego wielkość magazynu dołączone jest tylko 50 GB. Tak dodatkowe miejsce do magazynowania jest 100 GB – 50 GB = 50 GB. Dla dodatkowego magazynu o cenach, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość miejsca jest mniejsza niż wielkość magazynu dołączone to z żadnymi dodatkowymi kosztami można uniknąć przez ograniczenie maksymalnego rozmiaru bazy danych do uwzględniona ilość. 

## <a name="next-steps"></a>Kolejne kroki

Ogólny limitów zasobów, zobacz [limity zasobów na podstawie vCore bazy danych SQL — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md) i [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów - pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md).
