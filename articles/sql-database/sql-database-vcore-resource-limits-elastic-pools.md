---
title: Limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL platformy Azure — pul elastycznych | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis niektórych typowych limitów zasobów opartych na rdzeniach wirtualnych dla pul elastycznych usługi Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: af9d34b0c3b59cbf012d16de27bb4c60a809f619
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630965"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools"></a>Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pul elastycznych

Ten artykuł zawiera limity zasobów szczegółowe dla pul elastycznych usługi Azure SQL Database i bazy danych w puli przy użyciu modelu zakupu opartego na rdzeniach wirtualnych.

Oparte na jednostkach DTU limitów zakupu modelu zobacz [limity zasobów na podstawie jednostek DTU bazy danych SQL — pul elastycznych](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastyczna pula: magazyn o rozmiarze i poziomy wydajności

W przypadku pul elastycznych SQL Database w poniższej tabeli przedstawiono zasoby dostępne w poszczególnych usług warstwy i poziomu wydajności. Można ustawić warstwę usługi, poziom wydajności i wielkość magazynu przy użyciu [witryny Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [wiersza polecenia platformy Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), lub [interfejsu API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Limity zasobów pojedynczych baz danych w pulach elastycznych zazwyczaj są takie same jak dla pojedynczych baz danych poza pule, które ma taki sam poziom wydajności. Na przykład maksymalna współbieżnych procesów roboczych dla bazy danych GP_Gen4_1 to 200 pracowników. Dlatego maksymalny współbieżnych procesów roboczych dla bazy danych w puli GP_Gen4_1 jest również 200 pracowników. Uwaga: łączna liczba współbieżnych procesów roboczych w puli GP_Gen4_1 to 210.

### <a name="general-purpose-service-tier"></a>Warstwy usług w usłudze ogólnego przeznaczenia

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generacja 4
|Poziom wydajności|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|Maksymalny rozmiar danych (GB)|512|756|1536|2048|3584|4096|
|Maksymalny rozmiar dziennika|154|227|461|614|1075|1229|
|Size(DB) bazy danych TempDB|32|64|128|256|384|384|
|Docelowy operacji We/Wy (64 KB)|500|1000|2000|4000|7000|7000|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|210|420|840|1680|3360|5040|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba baz danych na pulę|100|200|500|500|500|500|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0.25, 0.5, 1|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Liczba replik|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|Usługa Premium Storage (zdalne)|
|Maksymalny rozmiar danych (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika|154|227|461|614|922|1229|1229|1229|
|Size(DB) bazy danych TempDB|64|128|256|384|384|384|384|384|
|Docelowy operacji We/Wy (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|5 – 7 ms (zapis)<br>5 – 10 ms (odczyt)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|210|420|840|1680|2520|3360|4200|8400
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba baz danych na pulę|100|200|500|500|500|500|500|500|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, o rozmiarze 0,5, 1, 2, 4, 8, 16, 24, 32|0, o rozmiarze 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, o rozmiarze 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Liczba replik|1|1|1|1|1|1|1|1|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|ND|ND|ND|ND|ND|ND|ND|ND|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

### <a name="business-critical-service-tier"></a>Warstwy usług krytycznych biznesowych

#### <a name="generation-4-compute-platform"></a>Platforma obliczeniowa generacja 4
|Poziom wydajności|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|4|4|4|4|4|4|
|Rdzenie wirtualne|1|2|4|8|16|24|
|Pamięć (GB)|7|14|28|56|112|168|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|1|2|4|8|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika|307|307|307|307|307|307|
|Size(DB) bazy danych TempDB|32|64|128|256|384|384|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|20000|40000|80000|120000|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|210|420|840|1680|3360|5040|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba baz danych na pulę|Nieobsługiwane|50|100|100|100|100|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|ND|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Liczba replik|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H: odczytu i zapisu|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Pojemność magazynu OLTP w pamięci (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|We/Wy, czas oczekiwania (w przybliżeniu)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|1 – 2 ms (zapis)<br>1 – 2 ms (odczyt)|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maksymalny rozmiar dziennika|307|307|307|307|614|1229|1229|1229|
|Size(DB) bazy danych TempDB|64|128|256|384|384|384|384|384|
|Docelowy operacji We/Wy (64 KB)|5000|10 000|20000|40000|60000|80000|100000|200000
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|210|420|840|1680|2520|3360|5040|8400|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba baz danych na pulę|ND|50|100|100|100|100|100|100|
|Min/max warianty pul elastycznych (rdzeń wirtualny) pozwalają na bazę danych|ND|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, o rozmiarze 0,5, 1, 2, 4, 8, 16, 24, 32|0, o rozmiarze 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, o rozmiarze 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Liczba replik|3|3|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględniony magazyn kopii zapasowych|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|1 X DB rozmiar|
|||

Jeśli wszystkie rdzenie wirtualne w puli elastycznej są zajęte, każda baza danych w puli otrzymuje taką samą ilość zasobów obliczeniowych do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwe udostępnianie zasobów puli elastycznej jest Oprócz zapewniania dowolnej ilości zasobów, w przeciwnym razie gwarantowanej dla każdej bazy danych, gdy minimalna liczba rdzeni wirtualnych na bazę danych jest ustawiona na wartość inną niż zero.

### <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych dla bazy danych w puli

W poniższej tabeli opisano właściwości dla bazy danych w puli.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba rdzeni na bazę danych |Maksymalna liczba rdzeni wirtualnych, które dowolnej bazy danych w puli mogą użyć, jeśli jest dostępna na podstawie użycia innych baz danych w puli. Maksymalna liczba rdzeni na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Ustaw maksymalny rdzeni wirtualnych na bazę danych jest wystarczająco wysoka, aby obsłużyć szczytowe użycia baz danych. Oczekiwany jest pewien stopień nadmiernego przydzielania, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych, w których nie wszystkie bazy danych jednocześnie osiągają szczytowe użycie.|
| Rdzenie min na bazę danych |Minimalna liczba rdzeni wirtualnych, który gwarantuje dowolnej bazy danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Rdzenie wirtualne min na bazę danych może być równa 0 i jest również wartością domyślną. Ta właściwość jest równa dowolne miejsce między 0 a użycie średni rdzeni wirtualnych na bazę danych. Iloczyn liczby baz danych w puli i rdzeni wirtualnych min na bazę danych nie może przekraczać rdzeni wirtualnych na pulę.|
| Maksymalny rozmiar magazynu na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Bazy danych w puli współużytkują magazyn puli przydzielone, więc rozmiar bazy danych może osiągnąć jest ograniczony do mniejszego z pozostałych puli magazynu i rozmiar bazy danych. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||
 
## <a name="next-steps"></a>Kolejne kroki

- Zobacz [— często zadawane pytania dla bazy danych SQL](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Zobacz [limity zasobów Omówienie usługi Azure SQL Database](sql-database-resource-limits.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
