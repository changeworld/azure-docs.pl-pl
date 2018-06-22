---
title: Limity zasobów na podstawie vCore bazy danych SQL Azure - pule elastyczne | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe zasobów na podstawie vCore limity pul elastycznych w bazie danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 01f213c7cf5f6be3ef84601a50bb4455422faf22
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309918"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools-preview"></a>Azure SQL Database vCore na podstawie zakupu modelu limity pul elastycznych (wersja zapoznawcza)

Ten artykuł zawiera limity szczegółowe zasobów dla puli elastycznej bazy danych SQL Azure i puli baz danych przy użyciu model kupna vCore.

Na podstawie jednostek dtu w warstwie limitów zakupów modelu zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów - pule elastyczne](sql-database-dtu-resource-limits-elastic-pools.md).


## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pula elastyczna: magazyn o rozmiarze i poziomy wydajności

Dla puli elastycznej bazy danych SQL w poniższych tabelach przedstawiono zasoby dostępne na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość magazynu za pomocą [portalu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [interfejsu wiersza polecenia Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), lub [interfejsu API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

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
|Poziom wydajności|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generowanie H/W|5|5|5|5|5|5|5|5|
|Rdzenie wirtualne|2|4|8|16|24|32|40|80|
|Pamięć (GB)|11|22|44|88|132|176|220|440|
|Obsługa magazynu kolumn|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Magazyn OLTP w pamięci (GB)|ND|ND|ND|ND|ND|ND|ND|ND|
|Typ magazynu|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|Magazyn w warstwie Premium (zdalnego)|
|Maksymalny rozmiar danych (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika|154|227|461|614|922|1229|1229|1229|
|Bazy danych TempDB size(DB)|64|128|256|384|384|384|384|384|
|Docelowy IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|ms 5-7 (Zapisz)<br>5 – 10 ms (odczyt)|
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|210|420|840|1680|2520|3360|4200|8400
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba gęstość puli|100|200|500|500|500|500|500|500|
|Kliknij przycisk puli elastycznej min/max zatrzymuje|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
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
|Liczba replik|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|
|Przeczytaj skalowalnego w poziomie|Yes|Yes|Yes|Yes|Yes|Yes|
|Uwzględnione magazynu kopii zapasowej|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|Rozmiar bazy danych X 1|
|||

#### <a name="generation-5-compute-platform"></a>Platforma obliczeniowa generowania 5
|Poziom wydajności|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
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
|Maksymalna liczba równoczesnych procesów roboczych (liczba żądań)|210|420|840|1680|2520|3360|5040|8400|
|Maksymalny dopuszczalny sesji|30000|30000|30000|30000|30000|30000|30000|30000|
|Maksymalna liczba gęstość puli|ND|50|100|100|100|100|100|100|
|Kliknij przycisk puli elastycznej min/max zatrzymuje|ND|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Liczba replik|3|3|3|3|3|3|3|3|
|Multi-AZ|ND|ND|ND|ND|ND|ND|ND|ND|
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
 
## <a name="next-steps"></a>Kolejne kroki

- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
