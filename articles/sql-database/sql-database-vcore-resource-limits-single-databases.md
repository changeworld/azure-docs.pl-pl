---
title: Azure SQL Database limity zasobów opartych na rdzeń wirtualny — pojedyncza baza danych | Microsoft Docs
description: Ta strona zawiera opis niektórych typowych limitów zasobów opartych na rdzeń wirtualny dla pojedynczej bazy danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/21/2019
ms.openlocfilehash: 108e31ebff5910c139bcaf83675cc8161c2a96f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693364"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na rdzeń wirtualny

Ten artykuł zawiera szczegółowe limity zasobów Azure SQL Database pojedynczych baz danych przy użyciu modelu zakupu opartego na rdzeń wirtualny.

W przypadku ograniczeń modelu zakupu opartego na jednostkach DTU dla pojedynczych baz danych na serwerze SQL Database, zobacz [Omówienie limitów zasobów na serwerze SQL Database](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](sql-database-file-space-management.md).

Możesz ustawić warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania dla pojedynczej bazy danych, korzystając z [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), interfejsu [wiersza polecenia platformy Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)lub [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie pojedynczej bazy danych](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-for-provisioned-compute"></a>Ogólnego przeznaczenia warstwa usługi dla obliczeń zainicjowanych

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="gen4-compute-generation-part-1"></a>Generowanie obliczeń obliczenia (część 1)

|Rozmiar obliczeń|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Typ usługi Storage|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|500|1000|1500|2000|2500|3000|
|Maksymalny współczynnik rejestrowania (MB/s)|3,75|7,5|11,25|15|18,75|22,5|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|168|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|3072|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|922|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|384|384|
|Typ usługi Storage|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)
|Maksymalna liczba operacji we/wy danych (64 KB)|3500|4000|4500|5000|8000|12000|
|Maksymalny współczynnik rejestrowania (MB/s)|26,25|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ usługi Storage|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Maksymalny współczynnik rejestrowania (MB/s)|3,75|7,5|11,25|15|18,75|22,5|26,25|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|ND|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ usługi Storage|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|8000|9000|10 000|12000|16000|20000|40000|
|Maksymalny współczynnik rejestrowania (MB/s)|30|30|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|ND|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

## <a name="general-purpose-service-tier-for-serverless-compute"></a>Ogólnego przeznaczenia warstwy usług dla obliczeń bezserwerowych

[Warstwa obliczeń bezserwerowych](sql-database-serverless.md) jest w wersji zapoznawczej.

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Min — maks. rdzeni wirtualnych|0,5-1|0,5-2|0,5 – 4|0,75-6|1.0 — 8|
|Min — maks. pamięć (GB)|2.02-3|2.05-6|2.10-12|2.25 – 18|3,00-24|
|Minimalne opóźnienie autopauzy (minuty)|60|60|60|60|60|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|512|1024|1024|1024|1536|
|Maksymalny rozmiar dziennika (GB)|154|307|307|307|461|
|Maksymalny rozmiar danych TempDB (GB)|32|64|128|192|256|
|Typ usługi Storage|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|500|1000|2000|3000|4000|
|Maksymalny współczynnik rejestrowania (MB/s)|2.5|5,6|10|15|20|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|75|150|300|450|600|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|
|Wiele-AZ|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Min — maks. rdzeni wirtualnych|1,25-10|1.50 — 12|1,75-14|2,00-16|
|Min — maks. pamięć (GB)|3,75-30|4.50 – 36|5,25-42|6.00-48|
|Minimalne opóźnienie autopauzy (minuty)|60|60|60|60|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|
|Maksymalny rozmiar danych (GB)|1536|1536|1536|3072|
|Maksymalny rozmiar dziennika (GB)|461|461|461|922|
|Maksymalny rozmiar danych TempDB (GB)|320|384|448|512|
|Typ usługi Storage|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|5000|6000|7000|8000|
|Maksymalny współczynnik rejestrowania (MB/s)|20|20|20|20|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|750|900|1050|1200|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|
|Wiele-AZ|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

## <a name="business-critical-service-tier-for-provisioned-compute"></a>Krytyczne dla działania firmy warstwa usługi dla obliczeń zainicjowanych

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="gen4-compute-generation-part-1"></a>Generowanie obliczeń obliczenia (część 1)

|Rozmiar obliczeń|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|1|2|3|4|5|6|
|Typ usługi Storage|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|5000|10 000|15000|20000|25000|30000|
|Maksymalny współczynnik rejestrowania (MB/s)|8|16|24|32|40|48|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Odczyt skalowalny w poziomie|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|168|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|7|8|9,5|11|20|36|
|Typ usługi Storage|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|384|384|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|35000|40000|45000|50000|80000|120000|
|Maksymalny współczynnik rejestrowania (MB/s)|56|64|64|64|64|64|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych logowań (żądania)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Odczyt skalowalny w poziomie|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen5-compute-compute-part-1"></a>5 rdzeń obliczeń obliczeniowych (część 1)

|Rozmiar obliczeń|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ usługi Storage|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Maksymalny współczynnik rejestrowania (MB/s)|12|24|36|48|60|72|84|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Odczyt skalowalny w poziomie|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|384|384|384|384|384|384|384|
|Typ usługi Storage|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Maksymalny współczynnik rejestrowania (MB/s)|96|96|96|96|96|96|96|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych logowań|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Odczyt skalowalny w poziomie|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

## <a name="hyperscale-service-tier-for-provisioned-compute"></a>Warstwa usługi do skalowania dla zasobów obliczeniowych

### <a name="gen5-compute-generation"></a>Generowanie obliczeń 5 rdzeń

|Poziom wydajności|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generowanie obliczeń|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|5\. generacji|
|Rdzeni wirtualnych|2|4|8|16|24|32|40|80|
|Pamięć (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Zmienia|3\. pamięć|3\. pamięć|3\. pamięć|3\. pamięć|3\. pamięć|3\. pamięć|3\. pamięć|3\. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Maksymalny rozmiar danych TempDB (GB)|64|128|256|384|384|384|384|384|
|Typ usługi Storage| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) | [Uwaga 1](#notes) |
|Maksymalna liczba operacji we/wy danych (64 KB)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Opóźnienie we/wy (przybliżone)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|[Uwaga 3](#notes)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|800|1600|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Wiele-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Odczyt skalowalny w poziomie|Tak|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

#### <a name="notes"></a>Uwagi

**Uwaga 1**: funkcja do skalowania jest architekturą wielowarstwową z oddzielnymi składnikami obliczeniowymi i magazynowanymi: [skalowanie warstwy usług](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Uwaga 2**: skalowanie wielowarstwowej architektury ma buforowanie na wielu poziomach. Efektywne operacje we/wy będą zależeć od obciążenia.

**Uwaga 3**: opóźnienie to 1-2 ms dla danych w pamięci podręcznej opartej na dyskach SSD RBPEX na replikach obliczeń, które buforują większość używanych stron danych. Wyższe opóźnienie dla danych pobieranych z serwerów stronicowania.

## <a name="next-steps"></a>Następne kroki

- W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na](sql-database-dtu-resource-limits-single-databases.md) JEDNOSTKAch DTU
- Aby uzyskać limity zasobów rdzeń wirtualny dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu opartego na rdzeń wirtualny](sql-database-vcore-resource-limits-elastic-pools.md)
- W przypadku limitów zasobów jednostek DTU dla pul elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu opartego na](sql-database-dtu-resource-limits-elastic-pools.md) JEDNOSTKAch DTU
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze bazy danych, zobacz [Omówienie limitów zasobów na serwerze SQL Database](sql-database-resource-limits-database-server.md) , aby uzyskać informacje na temat limitów na poziomie serwera i subskrypcji.
