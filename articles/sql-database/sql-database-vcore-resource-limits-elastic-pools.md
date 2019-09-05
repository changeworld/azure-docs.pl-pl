---
title: Azure SQL Database limitów zasobów opartych na rdzeń wirtualny — pule elastyczne | Microsoft Docs
description: Ta strona zawiera opis niektórych typowych limitów zasobów opartych na rdzeń wirtualny dla pul elastycznych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 2a5190cada0ca834cada4e02bfe7549dc43da4b8
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309514"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Limity zasobów dla pul elastycznych przy użyciu limitów modelu zakupu opartego na rdzeń wirtualny

Ten artykuł zawiera szczegółowe limity zasobów Azure SQL Database pul elastycznych i baz danych w puli przy użyciu modelu zakupu opartego na rdzeń wirtualny.

W przypadku ograniczeń modelu zakupu opartego na jednostkach DTU zobacz [SQL Database limity zasobów opartych na jednostkach DTU — pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

Możesz ustawić warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania przy użyciu [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [programu PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), interfejsu [wiersza polecenia platformy Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)lub [interfejsu API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie elastycznej puli](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Limity zasobów poszczególnych baz danych w pulach elastycznych są generalnie takie same jak w przypadku pojedynczych baz danych poza pulami o tym samym rozmiarze obliczeniowym. Na przykład maksymalna liczba współbieżnych procesów roboczych dla bazy danych GP_Gen4_1 to 200 procesów roboczych. W związku z tym Maksymalna liczba współbieżnych procesów roboczych dla bazy danych w puli GP_Gen4_1 to również 200 procesów roboczych. Należy pamiętać, że łączna liczba współbieżnych procesów roboczych w puli GP_Gen4_1 to 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Ogólnego przeznaczenia warstwa usługi: Rozmiary magazynu i rozmiary obliczeń

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Ogólnego przeznaczenia warstwa usługi: Platforma obliczeniowa 4 generacji (część 1)

|Rozmiar obliczeń|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Maksymalna liczba baz danych na pulę|100|200|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|512|756|756|1536|1536|1536|
|Maksymalny rozmiar dziennika|154|227|227|461|461|461|
|Rozmiar bazy danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|500|1000|1500|2000|2500|3000|
|Limity szybkości rejestrowania (MB/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) * |210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych logowań na pulę * |210|420|630|840|1050|1260|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0.25, 0.5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Ogólnego przeznaczenia warstwa usługi: Platforma obliczeniowa generacji 4 (część 2)

|Rozmiar obliczeń|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|168|
|Maksymalna liczba baz danych na pulę|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1536|2048|2048|2048|3584|4096|
|Maksymalny rozmiar dziennika (GB)|461|614|614|614|1075|1229|
|Rozmiar bazy danych TempDB (GB)|224|256|288|320|384|384|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|3500|4000|4500|5000|7000|7000|
|Limity szybkości rejestrowania (MB/s)|32,8125|37,5|37,5|37,5|37,5|37,5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych logowań w puli (żądania) *|1470|1680|1890|2100|3360|5040|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0.25, 0.5, 1...10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Ogólnego przeznaczenia warstwa usługi: Platforma obliczeniowa generacji 5 (część 1)

|Rozmiar obliczeń|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maksymalna liczba baz danych na pulę|100|200|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|512|756|756|1536|1536|1536|
|Maksymalny rozmiar dziennika (GB)|154|227|227|461|461|461|461|
|Rozmiar bazy danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Limity szybkości rejestrowania (MB/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|32,8125|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) *|210|420|630|840|1050|1260|1470|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Liczba replik|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Ogólnego przeznaczenia warstwa usługi: Platforma obliczeniowa generacji 5 (część 2)

|Rozmiar obliczeń|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|
|Rdzenie wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maksymalna liczba baz danych na pulę|500|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|2048|2048|3072|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|614|614|922|922|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Limity szybkości rejestrowania (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) *|1680|1890|2100|2520|3360|4200|8400|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Liczba replik|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Krytyczne dla działania firmy warstwa usługi: Rozmiary magazynu i rozmiary obliczeń

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Krytyczne dla działania firmy warstwa usługi: Platforma obliczeniowa 4 generacji (część 1)

|Rozmiar obliczeń|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Maksymalna liczba baz danych na pulę|Dla tego rozmiaru obliczeń są obsługiwane tylko pojedyncze baz danych|50|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|1|2|3|4|5|6|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|650|650|650|650|650|650|
|Maksymalny rozmiar dziennika (GB)|195|195|195|195|195|195|
|Rozmiar bazy danych TempDB (GB)|32|64|96|128|160|192|
|We/Wy, czas oczekiwania (w przybliżeniu)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|5000|10 000|15000|20000|25000|30000|
|Limity szybkości rejestrowania (MB/s)|10|20|30|40|50|60|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) *|210|420|630|840|1050|1260|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|ND|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Liczba replik|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Tak|
|Odczyt skalowalny w poziomie|Tak|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Krytyczne dla działania firmy warstwa usługi: Platforma obliczeniowa generacji 4 (część 2)

|Rozmiar obliczeń|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H/W|4|4|4|4|4|4|
|Rdzenie wirtualne|7|8|9|10|16|24|
|Pamięć (GB)|81,6|91,8|102|122,4|163,2|204|
|Maksymalna liczba baz danych na pulę|100|100|100|100|100|100|
|Obsługa magazynu kolumn|ND|ND|ND|ND|ND|ND|
|Magazyn OLTP w pamięci (GB)|7|8|9,5|11|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|650|650|650|650|1024|1024|
|Maksymalny rozmiar dziennika (GB)|195|195|195|195|307|307|
|Rozmiar bazy danych TempDB (GB)|224|256|288|320|384|384|
|We/Wy, czas oczekiwania (w przybliżeniu)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|35000|40000|45000|50000|80000|120000|
|Limity szybkości rejestrowania (MB/s)|70|80|80|80|80|80|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) *|1470|1680|1890|2100|3360|5040|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0.25, 0.5, 1...10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Liczba replik|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Tak|
|Odczyt skalowalny w poziomie|Tak|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Krytyczne dla działania firmy warstwa usługi: Platforma obliczeniowa generacji 5 (część 1)

|Rozmiar obliczeń|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maksymalna liczba baz danych na pulę|Dla tego rozmiaru obliczeń są obsługiwane tylko pojedyncze baz danych|50|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|307|461|461|922|922|
|Rozmiar bazy danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|5000|10 000|15000|20000|25000|30000|35000|
|Limity szybkości rejestrowania (MB/s)|15|30|45|60|75|90|105|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) *|210|420|630|840|1050|1260|1470|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|ND|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Liczba replik|4|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Tak|
|Odczyt skalowalny w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Krytyczne dla działania firmy warstwa usługi: Platforma obliczeniowa generacji 5 (część 2)

|Rozmiar obliczeń|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|
|Rdzenie wirtualne|16|18|20|24|32|40|80|
|Pamięć (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maksymalna liczba baz danych na pulę|100|100|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Magazyn OLTP w pamięci (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Liczba operacji we/wy docelowej (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Limity szybkości rejestrowania (MB/s)|120|120|120|120|120|120|120|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) *|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) *|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna dozwolona liczba sesji|30000|30000|30000|30000|30000|30000|30000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Liczba replik|4|4|4|4|4|4|4|
|Multi-AZ|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Odczyt skalowalny w poziomie|Tak|Yes|Yes|Yes|Yes|Yes|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna liczba współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych znajduje się w temacie [limity zasobów dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z usługi 5 rdzeń, a jej maksymalna rdzeń wirtualny na bazę danych to 2, Maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli maksymalna rdzeń wirtualny na bazę danych to 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny.  W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

Jeśli wszystkie rdzeni wirtualnych puli elastycznej są zajęte, każda baza danych w puli otrzymuje taką samą ilość zasobów obliczeniowych, aby przetwarzać zapytania. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwa współużytkowanie zasobów puli elastycznej jest uzupełnieniem dowolnej ilości zasobów, w przeciwnym razie zagwarantowane dla każdej bazy danych, gdy wartość rdzeń wirtualny min na bazę danych jest ustawiona na inną niż zero.

## <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych w puli

W poniższej tabeli opisano właściwości dla baz danych w puli.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba rdzeni wirtualnych na bazę danych |Maksymalna liczba rdzeni wirtualnych, które mogą być używane w dowolnej bazie danych w puli, jeśli są dostępne na podstawie użycia przez inne bazy danych w puli. Maksymalna rdzeni wirtualnych na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Wartość maksymalna rdzeni wirtualnych na bazę danych jest wystarczająco wysoka, aby obsługiwała szczyty w wykorzystaniu bazy danych. Oczekiwany jest jakiś stopień nadmiernego zatwierdzania, ponieważ w puli ogólnie przyjęto wzorce użycia gorącą i zimną dla baz danych, w których wszystkie bazy danych nie są jednocześnie szczytowe.|
| Minimalna rdzeni wirtualnych na bazę danych |Minimalna liczba rdzeni wirtualnych, jaką gwarantuje każda baza danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalna rdzeni wirtualnych na bazę danych może być ustawiona na 0 i jest również wartością domyślną. Ta właściwość jest ustawiona na dowolne miejsce między 0 a średnim wykorzystaniem rdzeni wirtualnych na bazę danych. Iloczyn liczby baz danych w puli i minimalnej rdzeni wirtualnych na bazę danych nie może przekroczyć rdzeni wirtualnych na pulę.|
| Maksymalny rozmiar magazynu na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Bazy danych w puli korzystają z przydzielonego magazynu puli, więc rozmiar bazy danych może dotrzeć do mniejszej ilości magazynu puli i rozmiaru bazy danych. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać limity zasobów rdzeń wirtualny dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md)
- W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na](sql-database-dtu-resource-limits-single-databases.md) JEDNOSTKAch DTU
- W przypadku limitów zasobów jednostek DTU dla pul elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu opartego na](sql-database-dtu-resource-limits-elastic-pools.md) JEDNOSTKAch DTU
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze bazy danych, zobacz [Omówienie limitów zasobów na serwerze SQL Database](sql-database-resource-limits-database-server.md) , aby uzyskać informacje na temat limitów na poziomie serwera i subskrypcji.
