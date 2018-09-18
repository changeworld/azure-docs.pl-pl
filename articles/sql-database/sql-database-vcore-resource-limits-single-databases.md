---
title: Limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL platformy Azure — pojedynczej bazy danych | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis niektórych typowych limitów zasobów opartych na rdzeniach wirtualnych, pojedynczej bazy danych w usłudze Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: ca6d36a4f06865b630e869623752bc5d488b6251
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732944"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych

Ten artykuł zawiera limity zasobów szczegółowe dla pojedynczych baz danych Azure SQL Database przy użyciu modelu zakupu opartego na rdzeniach wirtualnych.

Oparte na jednostkach DTU limitów zakupu modelu zobacz [limity zasobów na podstawie jednostek DTU bazy danych SQL](sql-database-dtu-resource-limits.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).


## <a name="single-database-storage-sizes-and-compute-sizes"></a>Pojedyncza baza danych: magazyn o rozmiarze i rozmiarów wystąpień obliczeniowych

Dla pojedynczych baz danych w poniższych tabelach Pokaż zasoby dostępne dla pojedynczej bazy danych w poszczególnych warstwach usług i obliczenia rozmiaru. Można ustawić warstwę usługi, rozmiar obliczeń i ilość miejsca w magazynie dla pojedynczej bazy danych za pomocą [witryny Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [języka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [ Interfejs wiersza polecenia Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases), lub [interfejsu API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Warstwy usług w usłudze ogólnego przeznaczenia

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generacja 4
|Obliczenia rozmiaru|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|3072|4096|4096|
|Maksymalny rozmiar dziennika (GB)|307|307|461|922|1229|1229|
|Rozmiar bazy danych TempDB (GB)|32|64|128|256|384|384|
|Docelowy operacji We/Wy (64 KB)|500|1000|2000|4000|7000|7000|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|800|1600|3200|4800|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|000
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Obliczenia rozmiaru|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|307|307|461|614|1229|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|64|128|256|384|384|384|384|384|
|Docelowy operacji We/Wy (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|800|1600|2400|3200|4000|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|1|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowych

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generacja 4
|Obliczenia rozmiaru|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|1|2|4|8|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Rozmiar bazy danych TempDB (GB)|32|64|128|256|384|384|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|20000|40000|80000|120000|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|800|1600|3200|4800|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Obliczenia rozmiaru|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|614|1229|1229|1229|
|Rozmiar bazy danych TempDB (GB)|64|128|256|384|384|384|384|384|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|20000|40000|60000|80000|100000|200000
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|800|1600|2400|3200|4000|8000|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Liczba replik|3|3|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [— często zadawane pytania dla bazy danych SQL](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Zobacz [limity zasobów Omówienie usługi Azure SQL Database](sql-database-resource-limits.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
