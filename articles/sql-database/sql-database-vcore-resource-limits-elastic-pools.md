---
title: Limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL platformy Azure — pul elastycznych | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis niektórych typowych limitów zasobów opartych na rdzeniach wirtualnych dla pul elastycznych usługi Azure SQL Database.
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
ms.date: 05/23/2019
ms.openlocfilehash: 98bd70d9f6eb70cb7848dfa74e19c78e55a34991
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240353"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Limity zasobów dla pul elastycznych za pomocą ograniczeń oparty na rdzeniach wirtualnych model zakupu

Ten artykuł zawiera limity zasobów szczegółowe dla pul elastycznych usługi Azure SQL Database i bazy danych w puli przy użyciu modelu zakupu opartego na rdzeniach wirtualnych.

Oparte na jednostkach DTU limitów zakupu modelu zobacz [limity zasobów na podstawie jednostek DTU bazy danych SQL — pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

Można ustawić warstwę usługi, rozmiar obliczeń i magazynu przy użyciu kwota [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [wiersza polecenia platformy Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), lub [interfejsu API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Skalowanie, wskazówki i uwagi, zobacz [skalowanie elastycznej puli](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Limity zasobów pojedynczych baz danych w pulach elastycznych są ogólnie takie same jak dla pojedynczych baz danych poza pule, które ma taką samą obliczenia rozmiaru. Na przykład maksymalna współbieżnych procesów roboczych dla bazy danych GP_Gen4_1 to 200 pracowników. Dlatego maksymalny współbieżnych procesów roboczych dla bazy danych w puli GP_Gen4_1 jest również 200 pracowników. Uwaga: łączna liczba współbieżnych procesów roboczych w puli GP_Gen4_1 to 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Warstwy usług ogólnego przeznaczenia: Magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generacja 4 (część 1)

|Obliczenia rozmiaru|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|rdzenie wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Maksymalna liczba baz danych na pulę|100|200|500|500|500|500|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|512|756|756|1536|1536|1536|
|Maksymalny rozmiar dziennika|154|227|227|461|461|461|
|Rozmiar bazy danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|500|1000|1500|2000|2500|3000|
|Limity szybkości dziennika (MB/s)|4.6875|9.375|14.0625|18.75|23.4375|28.125|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) * |210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych logowań na pulę * |210|420|630|840|1050|1260|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generacja 4 (część 2)

|Obliczenia rozmiaru|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|rdzenie wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|168|
|Maksymalna liczba baz danych na pulę|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1536|2048|2048|2048|3584|4096|
|Maksymalny rozmiar dziennika (GB)|461|614|614|614|1075|1229|
|Rozmiar bazy danych TempDB (GB)|224|256|288|320|384|384|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|3500|4000|4500|5000|7000|7000|
|Limity szybkości dziennika (MB/s)|32.8125|37.5|37.5|37.5|37.5|37.5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych logowań puli (żądań) *|1470|1680|1890|2100|3360|5040|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generowania 5 (część 1)

|Obliczenia rozmiaru|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|rdzenie wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Maksymalna liczba baz danych na pulę|100|200|500|500|500|500|500|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|512|756|756|1536|1536|1536|
|Maksymalny rozmiar dziennika (GB)|154|227|227|461|461|461|461|
|Rozmiar bazy danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Limity szybkości dziennika (MB/s)|4.6875|9.375|14.0625|18.75|23.4375|28.125|32.8125|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych logowań na pulę (żądań) *|210|420|630|840|1050|1260|1470|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Liczba replik|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Warstwy usług ogólnego przeznaczenia: Platforma obliczeniowa generowania 5 (część 2)

|Obliczenia rozmiaru|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|rdzenie wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Maksymalna liczba baz danych na pulę|500|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|2048|2048|3072|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|614|614|922|922|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Limity szybkości dziennika (MB/s)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|1680|1890|2100|2520|33600|4200|8400|
|Maksymalna liczba współbieżnych logowań na pulę (żądań) *|1680|1890|2100|2520|33600|4200|8400|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Liczba replik|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Warstwy usług krytycznych biznesowe: Magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generacja 4 (część 1)

|Obliczenia rozmiaru|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|rdzenie wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Maksymalna liczba baz danych na pulę|Tylko jednej bazy danych są obsługiwane w przypadku tego rozmiaru obliczeń|50|100|100|100|100|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|1|2|3|4|5|6|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|650|650|650|650|650|650|
|Maksymalny rozmiar dziennika (GB)|195|195|195|195|195|195|
|Rozmiar bazy danych TempDB (GB)|32|64|96|128|160|192|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|15000|20000|25000|30000|
|Limity szybkości dziennika (MB/s)|10|20|30|40|50|60|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych logowań na pulę (żądań) *|210|420|630|840|1050|1260|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|ND|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Liczba replik|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Tak|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generacja 4 (część 2)

|Obliczenia rozmiaru|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|rdzenie wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|81.6|91.8|102|122.4|163.2|204|
|Maksymalna liczba baz danych na pulę|100|100|100|100|100|100|
|Obsługa magazynu kolumn|ND|ND|ND|ND|ND|ND|
|Pojemność magazynu OLTP w pamięci (GB)|7|8|9.5|11|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|650|650|650|650|1024|1024|
|Maksymalny rozmiar dziennika (GB)|195|195|195|195|307|307|
|Rozmiar bazy danych TempDB (GB)|224|256|288|320|384|384|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|35000|40000|45000|50000|80000|120000|
|Limity szybkości dziennika (MB/s)|70|80|80|80|80|80|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych logowań na pulę (żądań) *|1470|1680|1890|2100|3360|5040|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Liczba replik|4|4|4|4|4|4|
|Multi-AZ|Yes|Yes|Yes|Yes|Yes|Tak|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generowania 5 (część 1)

|Obliczenia rozmiaru|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|rdzenie wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Maksymalna liczba baz danych na pulę|Tylko jednej bazy danych są obsługiwane w przypadku tego rozmiaru obliczeń|50|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|307|461|461|922|922|
|Rozmiar bazy danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|15000|20000|25000|30000|35000|
|Limity szybkości dziennika (MB/s)|15|30|45|60|75|90|105|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych logowań na pulę (żądań) *|210|420|630|840|1050|1260|1470|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|ND|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Liczba replik|4|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Yes|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Warstwy usług krytycznych biznesowe: Platforma obliczeniowa generowania 5 (część 2)

|Obliczenia rozmiaru|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|rdzenie wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Maksymalna liczba baz danych na pulę|100|100|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Pojemność magazynu OLTP w pamięci (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Docelowy operacji We/Wy (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Limity szybkości dziennika (MB/s)|120|120|120|120|120|120|120|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądań) *|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych logowań na pulę (żądań) *|1680|1890|2100|2520|3360|4200|8400|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Liczba replik|4|4|4|4|4|4|4|
|Multi-AZ|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Przeczytaj skalowalnego w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|

\* Aby uzyskać maksymalną współbieżnych procesów roboczych (żądań) dla dowolnej poszczególne bazy danych, zobacz [pojedynczy limity zasobów bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład jeśli 5. generacji i jego max (rdzeń wirtualny) na bazę danych korzysta z puli elastycznej jest 2, maksymalna współbieżnych procesów roboczych to 200.  Jeśli maksymalny (rdzeń wirtualny) na bazę danych wynosi 0,5, następnie max współbieżnych procesów roboczych jest 50, ponieważ na 5. generacji są maksymalnie 100 współbieżnych procesów roboczych na rdzeniach wirtualnych.  Inne ustawienia max (rdzeń wirtualny) na bazę danych, które są mniej 1 rdzeń wirtualny lub mniejszą liczbę maksymalną współbieżnych procesów roboczych jest podobnie przeskalowywany w ten sposób.

Jeśli wszystkie rdzenie wirtualne w puli elastycznej są zajęte, każda baza danych w puli otrzymuje taką samą ilość zasobów obliczeniowych do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwe udostępnianie zasobów puli elastycznej jest Oprócz zapewniania dowolnej ilości zasobów, w przeciwnym razie gwarantowanej dla każdej bazy danych, gdy minimalna liczba rdzeni wirtualnych na bazę danych jest ustawiona na wartość inną niż zero.

## <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych dla bazy danych w puli

W poniższej tabeli opisano właściwości dla bazy danych w puli.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba rdzeni na bazę danych |Maksymalna liczba rdzeni wirtualnych, które dowolnej bazy danych w puli mogą użyć, jeśli jest dostępna na podstawie użycia innych baz danych w puli. Maksymalna liczba rdzeni na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Ustaw maksymalny rdzeni wirtualnych na bazę danych jest wystarczająco wysoka, aby obsłużyć szczytowe użycia baz danych. Pewien stopień nadmiernego zatwierdzania oczekuje się, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych gdzie wszystkich baz danych są nie jednocześnie osiągają szczytowe użycie.|
| Rdzenie min na bazę danych |Minimalna liczba rdzeni wirtualnych, który gwarantuje dowolnej bazy danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Rdzenie wirtualne min na bazę danych może być równa 0 i jest również wartością domyślną. Ta właściwość jest równa dowolne miejsce między 0 a użycie średni rdzeni wirtualnych na bazę danych. Iloczyn liczby baz danych w puli i rdzeni wirtualnych min na bazę danych nie może przekraczać rdzeni wirtualnych na pulę.|
| Maksymalny rozmiar magazynu na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Bazy danych w puli współużytkują magazyn puli przydzielone, więc rozmiar bazy danych może osiągnąć jest ograniczony do mniejszego z pozostałych puli magazynu i rozmiar bazy danych. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||

## <a name="next-steps"></a>Kolejne kroki

- Rdzeń wirtualny limitów zasobów dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na rdzeniach wirtualnych](sql-database-vcore-resource-limits-single-databases.md)
- Limity zasobów jednostek DTU dla pojedynczej bazy danych, można zobaczyć [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-single-databases.md)
- Limity zasobów jednostek DTU dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na jednostkach DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Limity zasobów dla wystąpienia zarządzanego, można zobaczyć [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacji na temat limitów zasobów na serwerze bazy danych, zobacz [Przegląd limity zasobów na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.
