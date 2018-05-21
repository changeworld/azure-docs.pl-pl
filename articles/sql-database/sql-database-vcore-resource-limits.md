---
title: Azure SQL Database zasobów na podstawie vCore limity | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe limity zasobów na podstawie vCore bazy danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/15/2018
ms.author: carlrab
ms.openlocfilehash: d225af55a705d56a94bb0e8dcfcc938b64f3633a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Azure SQL Database vCore na podstawie zakupu limity modelu (wersja zapoznawcza)

> [!IMPORTANT]
> Na podstawie jednostek dtu w warstwie limitów zakupów modelu zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Pojedyncza baza danych: magazyn o rozmiarze i poziomy wydajności

Dla pojedynczej bazy danych w poniższej tabeli przedstawiono dostępne zasoby dla pojedynczej bazy danych na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość pamięci masowej dla pojedynczej bazy danych przy użyciu [portalu Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [języka Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), lub [interfejsu API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Warstwy usług celu ogólne

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generowania 4
|Poziom wydajności|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|3072|4096|4096|
|Maksymalny rozmiar dziennika|307|307|461|922|1229|1229|
|Bazy danych TempDB size(DB)|32|64|128|256|384|384|
|Docelowy IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|200|400|800|1600|3200|4800|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|000
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|48|80|
|Pamięć (GB)|11|22|44|88|132|176|264|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika|307|307|461|614|1229|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|200|400|800|1600|2400|3200|4800|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowa

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generowania 4
|Poziom wydajności|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|1|2|4|8|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika|307|307|307|307|307|307|
|Bazy danych TempDB size(DB)|32|64|128|256|384|384|
|Docelowy IOPS (64 KB)|5000|10 000|20000|40000|80000|120000|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|200|400|800|1600|3200|4800|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|3|3|3|3|3|3|
|Multi-AZ|Yes|Yes|Yes|Yes|Yes|Yes|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|48|80|
|Pamięć (GB)|11|22|44|88|132|176|264|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|1.571|3,142|6.284|15.768|25.252|37.936|68.104|131.64|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maksymalny rozmiar dziennika|307|307|307|307|614|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|5000|10 000|20000|40000|60000|80000|120000|200000
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|200|400|800|1600|2400|3200|4800|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

## <a name="single-database-change-storage-size"></a>Pojedyncza baza danych: zmiana rozmiaru magazynu

- Magazyn można udostępnić w granicach maksymalnej długości przy użyciu pojemności 1 GB. Magazyn danych można skonfigurować minimalną wynosi 5GB 
- Magazyn dla pojedynczej bazy danych można udostępnić, zwiększ lub zmniejsz jego rozmiar maksymalny przy użyciu [portalu Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update).
- Baza danych SQL automatycznie przydzieli 30% dodatkowy magazyn dla plików dziennika i 32GB na vCore dla bazy danych TempDB, ale nie może przekraczać 384GB. Bazy danych TempDB znajduje się na dołączonych dysków SSD w wszystkie warstwy usług.
- Cena przestrzeni dyskowej dla pojedynczej bazy danych to suma liczby danych dziennika i magazynu magazynu pomnożona przez cenie jednostkowej magazynu warstwy usług. Koszt bazy danych TempDB jest uwzględniony w cenie vCore. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Pojedyncza baza danych: Zmień vCores

Po wybraniu początkowo liczba vCores, można skalować pojedynczej bazy danych w górę lub w dół dynamicznie oparte na rzeczywiste środowisko przy użyciu [portalu Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [języka Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [programuPowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az_sql_db_update), lub [interfejsu API REST](/rest/api/sql/databases/update). 

Zmienienie warstwy usług i/lub poziomu wydajności bazy danych powoduje utworzenie repliki oryginalnej bazy danych na nowym poziomie wydajności, a następnie przełączenie połączeń do repliki. Podczas tego procesu nie zostaną utracone żadne dane, ale podczas przełączania do repliki połączenia do bazy danych są chwilowo wyłączane, dlatego niektóre bieżące transakcje mogą zostać wycofane. Czas na przejście jest różny, ale zazwyczaj w sekcji 4 sekundy jest mniej niż 30 sekund 99% czasu. W przypadku dużej liczby transakcji transmitowane w chwili połączenia są wyłączone, czas na przejście może trwać dłużej. 

Czas trwania całego procesu skalowania w górę zależy zarówno od rozmiaru, jak i warstwy usług bazy danych przed zmianą oraz po niej. Na przykład bazę danych 250 GB, która zmienia się z lub w ramach warstwy usług ogólnego przeznaczenia, powinno zakończyć się w ciągu sześciu godzin. W bazie danych sam rozmiar, który jest zmiana poziomów wydajności w warstwie usługi biznesowe krytyczne skalowanie w pionie powinno zakończyć się w ciągu trzech godzin.

> [!TIP]
> Aby monitorować w trakcie operacji, zobacz: [zarządzania operacjami przy użyciu interfejsu API REST SQL](/rest/api/sql/Operations/List), [zarządzania operacjami przy użyciu interfejsu wiersza polecenia](/cli/azure/sql/db/op), [monitorować operacji za pomocą T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i te dwa Polecenia programu PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) i [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Jeśli uaktualniasz do wyższego poziomu wydajności ani warstwy usługi, maksymalnego rozmiaru bazy danych nie zwiększa chyba że jawnie określ większy rozmiar (maxsize).
* Na starszą wersję bazy danych, bazy danych używane miejsce musi być mniejszy niż maksymalny dozwolony rozmiar docelowy warstwę i poziom wydajności usługi. 
* Podczas uaktualniania bazy danych o [— replikacja geograficzna](sql-database-geo-replication-portal.md) włączone, Uaktualnij swoje pomocniczej bazy danych do warstwy żądaną wydajność przed uaktualnieniem podstawowej bazy danych (ogólne wskazówki w celu uzyskania najlepszej wydajności). Podczas uaktualniania do innej, najpierw uaktualniania dodatkowej bazy danych jest wymagana.
* Podczas zmiany na starszą wersję bazy danych o [— replikacja geograficzna](sql-database-geo-replication-portal.md) włączone, obniżyć jego głównej bazy danych do warstwy żądaną wydajność przed przejściem dodatkowej bazy danych (ogólne wskazówki w celu uzyskania najlepszej wydajności). Podczas zmiany na starszą wersję do innej wersji, najpierw zmiany na starszą wersję podstawowej bazy danych jest wymagana.
* Nowe właściwości bazy danych są stosowane dopiero po zakończeniu wprowadzania zmian.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pula elastyczna: magazyn o rozmiarze i poziomy wydajności

Dla puli elastycznej bazy danych SQL w poniższych tabelach przedstawiono zasoby dostępne na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość magazynu za pomocą [portalu Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [interfejsu wiersza polecenia Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), lub [interfejsu API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Ograniczenia zasobów pojedynczych baz danych w puli elastycznej zazwyczaj są takie same jak dla pojedynczych baz danych poza pule, które ma na tym samym poziomie wydajności. Na przykład maksymalna pracowników równoczesnych GP_Gen4_1 bazy danych jest 200 pracowników. Tak max równoczesnych procesów roboczych dla bazy danych w puli GP_Gen4_1 jest również 200 pracowników. Uwaga: łączna liczba równoczesnych procesów roboczych w puli GP_Gen4_1 to 210.

### <a name="general-purpose-service-tier"></a>Warstwy usług celu ogólne

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generowania 4
|Poziom wydajności|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|
|Maksymalny rozmiar danych (GB)|512|756|1536|2048|3584|4096|
|Maksymalny rozmiar dziennika|154|227|461|614|1075|1229|
|Bazy danych TempDB size(DB)|32|64|128|256|384|384|
|Docelowy IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|210|420|840|1680|3360|5040|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba gęstość puli|100|200|500|500|500|500|
|Kliknij przycisk puli elastycznej min/max zatrzymuje|0, 0.25, 0.5, 1|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|48|80|
|Pamięć (GB)|11|22|44|88|132|176|264|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|
|Maksymalny rozmiar danych (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika|154|227|461|614|922|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|210|420|840|1680|2520|3360|5040|8400
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba gęstość puli|100|200|500|500|500|500|500|500|
|Kliknij przycisk puli elastycznej min/max zatrzymuje|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|Liczba replik|1|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowa

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generowania 4
|Poziom wydajności|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|1|2|4|8|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika|307|307|307|307|307|307|
|Bazy danych TempDB size(DB)|32|64|128|256|384|384|
|Docelowy IOPS (64 KB)|5000|10 000|20000|40000|80000|120000|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|210|420|840|1680|3360|5040|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba gęstość puli|ND|50|100|100|100|100|
|Kliknij przycisk puli elastycznej min/max zatrzymuje|ND|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Multi-AZ|Yes|Yes|Yes|Yes|Yes|Yes|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|48|80|
|Pamięć (GB)|11|22|44|88|132|176|264|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|1.571|3,142|6.284|15.768|25.252|37.936|68.104|131.64|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maksymalny rozmiar dziennika|307|307|307|307|614|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|5000|10 000|20000|40000|60000|80000|120000|200000
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|210|420|840|1680|2520|3360|5040|8400|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba gęstość puli|ND|50|100|100|100|100|100|100|
|Kliknij przycisk puli elastycznej min/max zatrzymuje|ND|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|Multi-AZ|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

Jeśli wszystkie vCores elastycznej puli są zajęte, każda baza danych w puli odbiera równa ilości zasobów obliczeniowych do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Udostępnianie sprawiedliwe przydzielanie zasobów dla elastycznej puli zasobów jest oprócz dowolnej liczby zasobów, w przeciwnym razie gwarancji dla każdej bazy danych, gdy minimalna vCore na bazę danych jest ustawiona na wartość inną niż zero.

### <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych dla puli baz danych

W poniższej tabeli opisano właściwości puli baz danych.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba vCores na bazę danych |Maksymalna liczba vCores, że wszystkie bazy danych w puli mogą użyć, jeśli dostępne na podstawie wykorzystania przez innych baz danych w puli. Maksymalna liczba vCores dla jednej bazy danych nie ma gwarancji zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Ustaw maksymalny vCores dla jednej bazy danych jest wystarczająco duża, aby obsłużyć szczytów wykorzystania bazy danych. Oczekiwany jest pewien stopień nadmiernego przydzielania, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych, w których nie wszystkie bazy danych jednocześnie osiągają szczytowe użycie.|
| VCores min na bazę danych |Minimalna liczba vCores, że wszystkie bazy danych w puli jest gwarantowana. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. VCores min na bazę danych może być równa 0, a także jest wartością domyślną. Ta właściwość ma ustawioną dowolnym od 0 do wykorzystania średni vCores na bazę danych. Produkt baz danych w puli i vCores min na bazę danych nie może przekraczać vCores dla każdej puli.|
| Maksymalna liczba magazyn na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Puli baz danych udostępnianie magazynu przydzielonego puli, dlatego rozmiar bazy danych może osiągnąć jest ograniczony do mniejsze pozostałej w puli magazynu i rozmiar bazy danych. Maksymalny rozmiar bazy danych odwołuje się do maksymalnego rozmiaru plików danych i nie obejmuje miejsca używane przez pliki dziennika. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pula elastyczna: zmiana rozmiaru magazynu

- Magazyn można udostępnić w granicach maksymalnej długości: 
 - Dla magazynu w warstwie standardowa Zwiększ lub Zmniejsz rozmiar w przyrostach 10 GB
 - Dla usługi Premium storage Zwiększ lub Zmniejsz rozmiar w przyrostach 250 GB
- Magazyn dla puli elastycznej można udostępnić, zwiększ lub zmniejsz jego rozmiar maksymalny przy użyciu [portalu Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [ Interfejs API REST](/rest/api/sql/elasticpools/update).
- Cena magazynu dla puli elastycznej jest wielkość magazynu pomnożona przez cenie jednostkowej magazynu warstwy usług. Aby uzyskać szczegółowe informacje na cenę dodatkowe miejsce do magazynowania, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Pula elastyczna: Zmień vCores

Można zwiększyć lub zmniejszyć poziom wydajności puli elastycznej oparte na potrzeby przy użyciu zasobów [portalu Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [interfejsu wiersza polecenia Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), lub [Interfejsu API REST](/rest/api/sql/elasticpools/update).

- Kiedy ponowne skalowanie vCores puli, połączenia z bazą danych krótko są usuwane. To samo, jak występuje, gdy ponowne skalowanie Dtu dla pojedynczej bazy danych (nie w puli). Aby uzyskać szczegółowe informacje na czas trwania i wpływu przerwanie połączenia dla bazy danych podczas operacji ponowne skalowanie, zobacz [ponowne skalowanie jednostek Dtu dla pojedynczej bazy danych](#single-database-change-storage-size). 
- Czas trwania, aby zmienić vCores puli może zależeć od całkowitej ilości miejsca używanego przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, rescaling opóźnienia oblicza średnią 90 minut lub mniej na 100 GB. Na przykład jeśli całkowita ilość miejsca, używany przez wszystkie bazy danych w puli jest 200 GB, oczekiwany czas oczekiwania na ponowne skalowanie puli wynosi 3 godziny, a następnie lub mniej. W niektórych przypadkach w ramach warstwy standardowa lub Basic rescaling czas oczekiwania może być niezależnie od ilości miejsca używanego w obszarze pięć minut.
- Ogólnie rzecz biorąc, czas trwania, aby zmienić vCores min na bazy danych lub max vCores dla jednej bazy danych to pięć minut lub mniej.
- Gdy redukcję zatrudnienia vCores puli miejsca puli musi być mniejszy niż maksymalny dozwolony rozmiar vCores warstwy i puli usługi docelowej.

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

### <a name="compute-vcores"></a>Obliczenia bazy danych (vCores)

Gdy wzrośnie wykorzystania obliczeń bazy danych (mierzonej przez użycie vCore), opóźnienia zapytania zwiększa i może nawet limit czasu. W tych warunkach zapytania mogą być umieszczone w kolejce przez usługę i są pod warunkiem, że zwolnić zasobów do wykonania jako zasobu.
Gdy wystąpią wykorzystania wysokiej obliczeń, są następujące opcje środki zaradcze:

- Zwiększenie poziomu wydajności bazy danych lub puli elastycznej, aby zapewnić więcej vCores bazy danych. Zobacz [pojedynczej bazy danych: Zmień cVcores](#single-database-change-vcores) i [puli elastycznej: Zmień vCores](#elastic-pool-change-vcores).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Magazyn

Gdy miejsca w bazie danych używany przez nią miejsce osiągnie limit maksymalnego rozmiaru bazy danych wstawia i aktualizacji, które zwiększają rozmiar danych nie powiodło się i klienci odbierają [komunikat o błędzie](sql-database-develop-error-messages.md). Baza danych WYBIERA i USUWA nadal się powieść.

Gdy wystąpią wykorzystanie miejsca wysoka, są następujące opcje środki zaradcze:

- Zwiększyć maksymalny rozmiar bazy danych lub puli elastycznej lub zmień poziom wydajności, aby zwiększyć pamięci masowej. Zobacz [limity zasobów na podstawie vCore bazy danych SQL](sql-database-vcore-resource-limits.md).
- W przypadku bazy danych w puli elastycznej następnie również bazy danych może zostać przeniesiona poza puli tak, aby jej miejsca do magazynowania nie są współużytkowane z innych baz danych.

### <a name="sessions-and-workers-requests"></a>Sesje i procesy robocze (liczba żądań) 

Maksymalna liczba sesji, jak i pracownicy są określane przez warstwę i poziom wydajności usługi. Nowe żądania są odrzucane po osiągnięciu limitu sesji lub proces roboczy, a klienci odbierają komunikat o błędzie. Gdy liczbę połączeń dostępnych może być kontrolowane przez aplikację, liczbę równoczesnych procesów roboczych jest często przeszkodę oszacować i sterowanie nią. Jest to szczególnie istotne podczas okresów obciążenia szczytowego po osiągnięciu limitu zasobów bazy danych i pracowników, ustawianie z powodu dłużej uruchomione zapytania. 

Gdy wystąpią wysokie użycie sesji lub procesu roboczego, są następujące opcje środki zaradcze:
- Zwiększenie poziomu warstwy lub wydajności usługi bazy danych lub elastyczna pula. Zobacz [pojedynczej bazy danych: Zmienianie rozmiaru magazynu](#single-database-change-storage-size), [pojedynczej bazy danych: Zmień vCores](#single-database-change-vcores), [puli elastycznej: Zmienianie rozmiaru magazynu](#elastic-pool-change-storage-size), i [puli elastycznej: Zmień vCores ](#elastic-pool-change-vcores).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania, jeśli przyczyną zwiększone wykorzystanie wynika z rywalizacji o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
