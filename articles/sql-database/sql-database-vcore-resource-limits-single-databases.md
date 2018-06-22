---
title: Limity zasobów na podstawie vCore bazy danych SQL Azure - pojedynczej bazy danych | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe limity zasobów na podstawie vCore dla pojedynczej bazy danych w bazie danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 1a14e1a7c50f458067491a8605a0518056ac0aa8
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309897"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database-preview"></a>Azure SQL Database vCore na podstawie zakupu modelu limity dla pojedynczej bazy danych (wersja zapoznawcza)

Ten artykuł zawiera limity szczegółowe zasobów dla pojedynczej bazy danych usługi Azure SQL Database przy użyciu model kupna vCore.

Na podstawie jednostek dtu w warstwie limitów zakupów modelu zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Pojedyncza baza danych: magazyn o rozmiarze i poziomy wydajności

Dla pojedynczej bazy danych w poniższej tabeli przedstawiono dostępne zasoby dla pojedynczej bazy danych na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość pamięci masowej dla pojedynczej bazy danych przy użyciu [portalu Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [języka Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), lub [interfejsu API REST](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

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
|Poziom wydajności|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika|307|307|461|614|1229|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|200|400|800|1600|2400|3200|4000|8000|
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
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|ms 1 i 2 (Zapisz)<br>ms 1 i 2 (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maksymalny rozmiar dziennika|307|307|307|307|614|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|5000|10 000|20000|40000|60000|80000|100000|200000
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|200|400|800|1600|2400|3200|4000|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|3|3|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
