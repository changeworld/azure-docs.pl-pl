---
title: Limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL platformy Azure — pojedynczej bazy danych | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis niektórych typowych limitów zasobów opartych na rdzeniach wirtualnych, pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/22/2019
ms.openlocfilehash: 7dcbf2733ff23b0454208dbc789599cd372278ec
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507836"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych

Ten artykuł zawiera limity zasobów szczegółowe dla pojedynczych baz danych Azure SQL Database przy użyciu modelu zakupu opartego na rdzeniach wirtualnych.

Oparte na jednostkach DTU zakupu modelu limitów dla pojedynczych baz danych na serwerze bazy danych SQL, zobacz [Przegląd zasobów limity na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

Można ustawić warstwę usługi, rozmiar obliczeń i ilość miejsca w magazynie dla pojedynczej bazy danych za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [języka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Interfejs wiersza polecenia Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), lub [interfejsu API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Skalowanie, wskazówki i uwagi, zobacz [skalowanie pojedynczej bazy danych](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Warstwy usług ogólnego przeznaczenia: Magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generacja 4 (część 1)

|Obliczenia rozmiaru|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1536|1536|1536|
|Maksymalny rozmiar dziennika (GB)|307|307|307|461|461|461|
|Rozmiar bazy danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|500|1000|1500|2000|2500|3000|
|Limity szybkości dziennika (MB/s)|3.75|7.5|11.25|15|18.75|22.5|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generacja 4 (część 2)

|Obliczenia rozmiaru|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|168|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1536|3072|3072|3072|4096|4096|
|Maksymalny rozmiar dziennika (GB)|461|922|922|922|1229|1229|
|Rozmiar bazy danych TempDB (GB)|224|256|288|320|384|384|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)
|Docelowy operacji We/Wy (64 KB)|3500|4000|4500|5000|7000|7000|
|Limity szybkości dziennika (MB/s)|26.25|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generowania 5 (część 1)

|Obliczenia rozmiaru|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|307|461|461|461|461|
|Rozmiar bazy danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Limity szybkości dziennika (MB/s)|3.75|7.5|11.25|15|18.75|22.5|26.25|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generowania 5 (część 2)

|Obliczenia rozmiaru|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|Rdzenie wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Limity szybkości dziennika (MB/s)|30|30|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="serverless-compute-tier"></a>Warstwa bezserwerowych usług obliczeniowych

[Warstwy bezserwerowe środowisko obliczeniowe](sql-database-serverless.md) jest w publicznej wersji zapoznawczej i jest tylko w przypadku modelu pojedynczych baz danych przy użyciu zakup (rdzeń wirtualny).

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5

|Obliczenia rozmiaru|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|
|Minimum maksimum rdzeni wirtualnych|0.5-1|0.5-2|0.5-4|
|Min — maksymalny rozmiar pamięci (GB)|2.02-3|2.05-6|2.10-12|
|Wstrzymaj automatycznie min. opóźnienie (godziny)|6|6|6|
|Obsługa magazynu kolumn|Tak|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|512|1024|1024|
|Maksymalny rozmiar dziennika (GB)|12|24|48|
|Rozmiar bazy danych TempDB (GB)|32|64|128|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|250|500|1000|
|Limity szybkości dziennika (MB/s)|2.5|5.6|10|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|75|150|300|
|Maksymalny dopuszczalny sesji|30000|30000|30000|
|Liczba replik|1|1|1|
|Multi-AZ|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Warstwy usług krytycznych firm zainicjowanych zasobów obliczeniowych w warstwie

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generacja 4 (część 1)

|Obliczenia rozmiaru|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|1|2|3|4|5|6|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|650|650|650|650|650|650|
|Maksymalny rozmiar dziennika (GB)|195|195|195|195|195|195|
|Rozmiar bazy danych TempDB (GB)|32|64|96|128|160|192|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|15000|20000|25000|30000|
|Limity szybkości dziennika (MB/s)|8|16|24|32|40|48|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Tak|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generacja 4 (część 2)

|Obliczenia rozmiaru|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|168|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|7|8|9.5|11|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|650|650|650|650|1024|1024|
|Maksymalny rozmiar dziennika (GB)|195|195|195|195|307|307|
|Rozmiar bazy danych TempDB (GB)|224|256|288|320|384|384|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|35000|40000|45000|50000|80000|120000|
|Limity szybkości dziennika (MB/s)|56|64|64|64|64|64|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych logowań (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Tak|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generowania 5 (część 1)

|Obliczenia rozmiaru|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|307|461|461|922|922|
|Rozmiar bazy danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Limity szybkości dziennika (MB/s)|12|24|36|48|60|72|84|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|1400|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generowania 5 (część 2)

|Obliczenia rozmiaru|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|Rdzenie wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Limity szybkości dziennika (MB/s)|96|96|96|96|96|96|96|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych logowań|1600|1800|2000|2400|3200|4000|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

## <a name="hyperscale-service-tier"></a>Warstwa usługi Hiperskala

### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5

|Poziom wydajności|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|10.2|20.4|40.8|81.6|122.4|163.2|204|408|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Rozmiar bazy danych TempDB (GB)|64|128|256|384|384|384|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Docelowy operacji We/Wy (64 KB)|Określone|Określone|Określone|Określone|Określone|Określone|Określone|Określone|
|We/Wy, czas oczekiwania (w przybliżeniu)|Określone|Określone|Określone|Określone|Określone|Określone|Określone|Określone|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|800|1600|2400|3200|4000|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|2|2|2|2|2|2|2|2|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych |7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Kolejne kroki

- Limity zasobów jednostek DTU dla pojedynczej bazy danych, można zobaczyć [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-single-databases.md)
- Limity zasobów (rdzeń wirtualny) dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na rdzeniach wirtualnych](sql-database-vcore-resource-limits-elastic-pools.md)
- Limity zasobów jednostek DTU dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Limity zasobów dla wystąpienia zarządzanego, można zobaczyć [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacji na temat limitów zasobów na serwerze bazy danych, zobacz [Przegląd limity zasobów na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.