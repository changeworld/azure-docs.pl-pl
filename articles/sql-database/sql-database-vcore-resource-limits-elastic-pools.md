---
title: Limity zasobów vCore - pule elastyczne
description: Na tej stronie opisano niektóre typowe limity zasobów vCore dla pul elastycznych w bazie danych SQL azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: b3c5594b8eef76dcb57903408dd1e77c96890eab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346262"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limity zasobów dla pul elastycznych przy użyciu modelu zakupu p-owa wirtualnego

Ten artykuł zawiera szczegółowe limity zasobów dla pul elastycznych bazy danych SQL azure i puli baz danych przy użyciu modelu zakupu pór wirtualnych.

Aby uzyskać limity modelu zakupu DTU, zobacz [Limity zasobów DTU bazy danych SQL — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie miejscem na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

Warstwę usługi, rozmiar obliczeń i ilość miejsca można ustawić za pomocą [portalu Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [programu PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases) [interfejsu wiersza polecenia platformy Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)lub interfejsu API [REST.](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases)

> [!IMPORTANT]
> Aby uzyskać wskazówki i zagadnienia dotyczące [skalowania, zobacz Skalowanie puli elastycznej](sql-database-elastic-pool-scale.md)

## <a name="general-purpose---provisioned-compute---gen4"></a>Przeznaczenie ogólne — aprowizowana moc obliczeniowa — Gen4

> [!IMPORTANT]
> Nowe bazy danych Gen4 nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 4 (część 1)

|Rozmiar obliczeń|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Maksymalna liczba DBs na pulę <sup>1</sup>|100|200|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|512|756|1536|1536|1536|2048|
|Maksymalny rozmiar dziennika|154|227|461|461|461|614|
|Maksymalny rozmiar danych tempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maksymalna stawka dziennika na pulę (MB/s)|4.7|9.4|14.1|18.8|23.4|28.1|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup> |210|420|630|840|1050|1260|
|Maksymalna liczba równoczesnych logowań na pulę <sup>3</sup> |210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Liczba replik|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 4 (część 2)

|Rozmiar obliczeń|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159.5|
|Maksymalna liczba DBs na pulę <sup>1</sup>|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|2048|2048|2048|2048|3584|4096|
|Maksymalny rozmiar dziennika (GB)|614|614|614|614|1075|1229|
|Maksymalny rozmiar danych tempDB (GB)|224|256|288|320|512|768|
|Typ magazynu|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maksymalna stawka dziennika na pulę (MB/s)|32.8|37.5|37.5|37.5|37.5|37.5|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna pula równoczesnych logowań (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Liczba replik|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).    

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

## <a name="general-purpose---provisioned-compute---gen5"></a>Przeznaczenie ogólne — aprowizowana moc obliczeniowa — Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 5 (część 1)

|Rozmiar obliczeń|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Maksymalna liczba DBs na pulę <sup>1</sup>|100|200|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|512|756|1536|1536|1536|2048|2048|
|Maksymalny rozmiar dziennika (GB)|154|227|461|461|461|614|614|
|Maksymalny rozmiar danych tempDB (GB)|64|128|192|256|320|384|448|
|Typ magazynu|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maksymalna stawka dziennika na pulę (MB/s)|9.4|18.8|28.1|37.5|37.5|37.5|37.5|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Liczba replik|1|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 5 (część 2)

|Rozmiar obliczeń|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maksymalna liczba DBs na pulę <sup>1</sup>|500|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|2048|3072|3072|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|614|922|922|922|1229|1229|1229|
|Maksymalny rozmiar danych tempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|Pamięć masowa premium (zdalna)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup> |6,400|7,200|8000|9600|12,800|16 000|32,000|
|Maksymalna stawka dziennika na pulę (MB/s)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Liczba replik|1|1|1|1|1|1|1|
|Wielu AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Przeznaczenie ogólne — aprowizowana moc obliczeniowa — seria Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Generowanie obliczeń serii Fsv2 (wersja zapoznawcza)

|Rozmiar obliczeń|GP_Fsv2_72|
|:--- | --: |
|Generowanie obliczeń|Seria Fsv2|
|Rejy wirtualne|72|
|Pamięć (GB)|136.2|
|Maksymalna liczba DBs na pulę <sup>1</sup>|500|
|Obsługa magazynu kolumn|Tak|
|Pamięć OLTP w pamięci (GB)|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|1024|
|Maksymalny rozmiar danych tempDB (GB)|333|
|Typ magazynu|Pamięć masowa premium (zdalna)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|16 000|
|Maksymalna stawka dziennika na pulę (MB/s)|37.5|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|3780|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|3780|
|Maksymalna liczba współbieżnych sesji|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0-72|
|Liczba replik|1|
|Wielu AZ|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

## <a name="business-critical---provisioned-compute---gen4"></a>Krytyczne dla firmy — obliczenia aprowizacji — Gen4

> [!IMPORTANT]
> Nowe bazy danych Gen4 nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Warstwa usług o krytycznym znaczeniu dla firmy: platforma obliczeniowa generacji 4 (część 1)

|Rozmiar obliczeń|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|2|3|4|5|6|
|Pamięć (GB)|14|21|28|35|42|
|Maksymalna liczba DBs na pulę <sup>1</sup>|50|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|2|3|4|5|6|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|
|Maksymalny rozmiar danych tempDB (GB)|64|96|128|160|192|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|9000|13,500|18 000|22,500|27,000|
|Maksymalna stawka dziennika na pulę (MB/s)|20|30|40|50|60|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Liczba replik|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Warstwa usług o krytycznym znaczeniu dla firmy: platforma obliczeniowa generacji 4 (część 2)

|Rozmiar obliczeń|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Rejy wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159.5|
|Maksymalna liczba DBs na pulę <sup>1</sup>|100|100|100|100|100|100|
|Obsługa magazynu kolumn|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Pamięć OLTP w pamięci (GB)|7|8|9.5|11|20|36|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych tempDB (GB)|224|256|288|320|512|768|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|31,500|36,000|40,500|45 000|72,000|96,000|
|Maksymalna stawka dziennika na pulę (MB/s)|70|80|80|80|80|80|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Liczba replik|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

## <a name="business-critical---provisioned-compute---gen5"></a>Krytyczne dla firmy — obliczenia aprowizacji — Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Warstwa usług o krytycznym znaczeniu dla firmy: platforma obliczeniowa generacji 5 (część 1)

|Rozmiar obliczeń|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|4|6|8|10|12|14|
|Pamięć (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|Maksymalna liczba DBs na pulę <sup>1</sup>|50|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|3.14|4.71|6.28|8.65|11.02|13.39|
|Maksymalny rozmiar danych (GB)|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|922|922|
|Maksymalny rozmiar danych tempDB (GB)|128|192|256|320|384|448|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|18 000|27,000|36,000|45 000|54,000|63,000|
|Maksymalna stawka dziennika na pulę (MB/s)|60|90|120|120|120|120|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Liczba replik|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Warstwa usług o krytycznym znaczeniu dla firmy: platforma obliczeniowa generacji 5 (część 2)

|Rozmiar obliczeń|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Rejy wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maksymalna liczba DBs na pulę <sup>1</sup>|100|100|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Pamięć OLTP w pamięci (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.68|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych tempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|Lokalny system SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|72,000|81,000|90 000|108,000|144,000|180,000|256,000|
|Maksymalna stawka dziennika na pulę (MB/s)|120|120|120|120|120|120|120|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych sesji|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Liczba replik|4|4|4|4|4|4|4|
|Wielu AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

## <a name="business-critical---provisioned-compute---m-series"></a>Krytyczne dla firmy — obliczenia aprowizacji — seria M

### <a name="m-series-compute-generation-preview"></a>Generowanie obliczeń serii M (wersja zapoznawcza)

|Rozmiar obliczeń|BC_M_128|
|:--- | --: |
|Generowanie obliczeń|Seria M|
|Rejy wirtualne|128|
|Pamięć (GB)|3767.1|
|Maksymalna liczba DBs na pulę <sup>1</sup>|100|
|Obsługa magazynu kolumn|Tak|
|Pamięć OLTP w pamięci (GB)|1768|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|2048|
|Maksymalny rozmiar danych tempDB (GB)|4096|
|Typ magazynu|Lokalny system SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (czytaj)|
|Maksymalna liczba IOPS danych na pulę <sup>2</sup>|200,000|
|Maksymalna stawka dziennika na pulę (MB/s)|333|
|Maksymalna liczba równoczesnych pracowników na pulę (żądania) <sup>3</sup>|13,440|
|Maksymalna liczba równoczesnych logowań na pulę (żądania) <sup>3</sup>|13,440|
|Maksymalna liczba współbieżnych sesji|30,000|
|Opcje minimalnych/maksymalnej puli elastycznej w wersji vCore na bazę danych|0-128|
|Liczba replik|4|
|Wielu AZ|Tak|
|Skalowanie w górę odczytu|Tak|
|Dołączone miejsce do przechowywania kopii zapasowych|1X rozmiar bazy danych|

<sup>1</sup> Zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md) dla dodatkowych zagadnień.

<sup>2</sup> Maksymalna wartość rozmiarów we/wy w zakresie od 8 KB do 64 KB. Rzeczywiste we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> Aby uzyskać maksymalną liczbę równoczesnych pracowników (żądań) dla dowolnej pojedynczej bazy danych, zobacz [Limity zasobów pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli pula elastyczna używa Gen5 i max vCore na bazę danych jest ustawiona na 2, a następnie maksymalna wartość równoczesnych pracowników wynosi 200.  Jeśli max vCore na bazę danych jest ustawiona na 0,5, a następnie maksymalna wartość równoczesnych pracowników wynosi 50, ponieważ w gen5 istnieje maksymalnie 100 równoczesnych pracowników na rów wirtualnych. W przypadku innych maksymalnych ustawień vCore na bazę danych, które są mniejsze niż 1 r.vCore lub mniej, liczba maksymalnych równoczesnych pracowników jest podobnie przeskalowana.

Jeśli wszystkie pulę wirtualną puli elastycznej są zajęte, każda baza danych w puli otrzymuje taką samą ilość zasobów obliczeniowych do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Uczciwość udostępniania zasobów puli elastycznej jest dodatkiem do dowolnej ilości zasobów gwarantowanych dla każdej bazy danych, gdy wartość vCore min na bazę danych jest ustawiona na wartość niezerową.

## <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych dla puli baz danych

W poniższej tabeli opisano właściwości puli baz danych.

> [!NOTE]
> Limity zasobów poszczególnych baz danych w pulach elastycznych są zazwyczaj takie same jak dla pojedynczych baz danych poza pulami, które mają ten sam rozmiar obliczeń. Na przykład maksymalna liczba równoczesnych pracowników dla GP_Gen4_1 bazy danych wynosi 200 pracowników. Tak więc maksymalna liczba równoczesnych pracowników bazy danych w puli GP_Gen4_1 jest również 200 pracowników. Należy zauważyć, że całkowita liczba równoczesnych pracowników w puli GP_Gen4_1 wynosi 210.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba wirtualnych ryp na bazę danych |Maksymalna liczba procesorów wirtualnych, które każda baza danych w puli może używać, jeśli są dostępne na podstawie wykorzystania przez inne bazy danych w puli. Maksymalna liczba wirtualnych na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Ustaw maksymalną liczbę procesorów wirtualnych na bazę danych wystarczająco wysoką, aby obsłużyć szczyty wykorzystania bazy danych. Pewien stopień nadmiernego zatwierdzania oczekuje się, ponieważ pula zazwyczaj zakłada wzorce użycia na gorąco i zimno dla baz danych, gdzie wszystkie bazy danych nie są jednocześnie szczytowe.|
| Min. rymy wirtualne na bazę danych |Minimalna liczba pole wirtualnych, które każda baza danych w puli jest gwarantowana. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalne pola wirtualne na bazę danych mogą być ustawione na 0, a także wartość domyślną. Ta właściwość jest ustawiona na dowolnym miejscu między 0 a średnim wykorzystaniem vCores na bazę danych. Produkt liczby baz danych w puli i min-rów wirtualnych na bazę danych nie może przekraczać kopii wirtualnych na pulę.|
| Maksymalna ilość miejsca na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Buforowane bazy danych współużytkuje przydzielony magazyn puli, więc rozmiar bazy danych może osiągnąć jest ograniczona do mniejszego magazynu pozostałej puli i rozmiaru bazy danych. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje miejsca używanego przez pliki dziennika. |
|||

## <a name="next-steps"></a>Następne kroki

- W przypadku limitów zasobów w ramach vCore dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu w ramach vCore](sql-database-vcore-resource-limits-single-databases.md)
- Aby uzyskać limity zasobów jednostek DTU dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-single-databases.md)
- Aby uzyskać limity zasobów DTU dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu usługi DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze bazy danych, zobacz [omówienie limitów zasobów na serwerze bazy danych SQL, aby](sql-database-resource-limits-database-server.md) uzyskać informacje o limitach na poziomie serwera i subskrypcji.
