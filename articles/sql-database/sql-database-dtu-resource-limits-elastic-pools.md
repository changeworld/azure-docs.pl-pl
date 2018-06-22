---
title: Zasobów na podstawie jednostek dtu w warstwie bazy danych SQL Azure ogranicza pule elastyczne | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe limity zasobów na podstawie jednostek DTU dla pul elastycznych w bazie danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: adf0cd8fb8511b01d2c8e773444b0af128102ad1
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309869"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-based-purchasing-model"></a>Zasoby limity pul elastycznych, używając model kupna jednostek dtu w warstwie 

Ten artykuł zawiera limity szczegółowe zasobów dla puli elastycznej bazy danych SQL Azure i puli baz danych przy użyciu model kupna jednostek dtu w warstwie. 

Na podstawie jednostek dtu w warstwie zakupów modelu zasobów limitów dla pojedynczych baz danych, zobacz [limity zasobów na podstawie jednostek dtu w warstwie - pojedynczych baz danych](sql-database-vcore-resource-limits-elastic-pools.md). Limitów zasobów na podstawie vCore, zobacz [limity zasobów na podstawie vCore - pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów na podstawie vCore - pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pula elastyczna: magazyn o rozmiarze i poziomy wydajności

Dla puli elastycznej bazy danych SQL w poniższych tabelach przedstawiono zasoby dostępne na każdym poziomie wydajności i warstwę usług. Można ustawić warstwy usług, poziom wydajności i wielkość magazynu za pomocą [portalu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [interfejsu wiersza polecenia Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), lub [interfejsu API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Ograniczenia zasobów pojedynczych baz danych w puli elastycznej zazwyczaj są takie same jak dla pojedynczych baz danych poza puli na podstawie Dtu i warstwy usług. Na przykład maksymalna pracowników równoczesnych S2 bazy danych jest 120 pracowników. Tak max równoczesnych procesów roboczych dla bazy danych w puli standardowej jest również 120 pracowników Jeśli maksymalna wartość DTU na bazę danych w puli jest 50 jednostek Dtu (co jest równoważne S2).

### <a name="basic-elastic-pool-limits"></a>Limity podstawowych pul elastycznych

| Jednostki eDTU na pulę | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Magazyn dołączony na pulę (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maksymalna liczba opcji magazynu dla każdej puli (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maksymalna liczba OLTP w pamięci magazynu dla każdej puli (GB) | ND | ND | ND | ND | ND | ND | ND | ND |
| Maksymalna liczba baz danych na pulę | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Min wyborów jednostek Edtu na bazę danych | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Maksymalna liczba opcji jednostek Edtu na bazę danych | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limity standardowych pul elastycznych

| Jednostki eDTU na pulę | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Magazyn dołączony na pulę (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maksymalna liczba opcji magazynu dla każdej puli (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Maksymalna liczba OLTP w pamięci magazynu dla każdej puli (GB) | ND | ND | ND | ND | ND | ND | 
| Maksymalna liczba baz danych na pulę | 100 | 200 | 500 | 500 | 500 | 500 | 
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min wyborów jednostek Edtu na bazę danych | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maksymalna liczba opcji jednostek Edtu na bazę danych | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maksymalny rozmiar magazynu na bazę danych (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limity standardowych pul elastycznych (ciąg dalszy) 

| Jednostki eDTU na pulę | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Magazyn dołączony na pulę (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maksymalna liczba opcji magazynu dla każdej puli (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maksymalna liczba OLTP w pamięci magazynu dla każdej puli (GB) | ND | ND | ND | ND | ND | 
| Maksymalna liczba baz danych na pulę | 500 | 500 | 500 | 500 | 500 | 
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min wyborów jednostek Edtu na bazę danych | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maksymalna liczba opcji jednostek Edtu na bazę danych | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Maksymalna liczba opcji magazynu dla jednej bazy danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limity pul elastycznych Premium

| Jednostki eDTU na pulę | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Magazyn dołączony na pulę (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maksymalna liczba opcji magazynu dla każdej puli (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maksymalna liczba OLTP w pamięci magazynu dla każdej puli (GB) | 1 | 2 | 4 | 10 | 12 | 
| Maksymalna liczba baz danych na pulę | 50 | 100 | 100 | 100 | 100 | 
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 200 | 400 | 800 | 1600 | 2400 | 
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalna liczba jednostek eDTU na bazę danych | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Maksymalna liczba jednostek eDTU na bazę danych | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limity pul elastycznych Premium (ciąg dalszy) 

| Jednostki eDTU na pulę | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Magazyn dołączony na pulę (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maksymalna liczba opcji magazynu dla każdej puli (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maksymalna liczba OLTP w pamięci magazynu dla każdej puli (GB) | 16 | 20 | 24 | 28 | 32 |
| Maksymalna liczba baz danych na pulę | 100 | 100 | 100 | 100 | 100 | 
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maksymalna liczba współbieżnych sesji na pulę | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min wyborów jednostek Edtu na bazę danych | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maksymalna liczba opcji jednostek Edtu na bazę danych | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> Więcej niż 1 TB pamięci masowej w warstwie Premium jest dostępna we wszystkich regionach, z wyjątkiem następujących: Wielka Brytania Północna, zachodnie środkowe stany, South2 Wielka Brytania, Chin Wschodnia, USDoDCentral, Niemcy środkowe, południowo-zachodni USDoDEast, USA wersji dla instytucji rządowych, nam wersji dla instytucji rządowych Południowa centralnej, Niemcy północno-wschodnie, Chiny Północno-wschodnie wersji dla instytucji rządowych Stanów Zjednoczonych. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

Jeśli używane są wszystkie jednostki DTU puli elastycznej, każda baza danych w puli otrzymuje taką samą ilość zasobów do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwe udostępnianie zasobów puli elastycznej jest wykonywane oprócz zapewniania dowolnej ilości zasobów w przeciwnym razie gwarantowanej dla każdej bazy danych, gdy minimalna liczba jednostek DTU na bazę danych jest ustawiona na wartość różną od zera.

### <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych dla puli baz danych

W poniższej tabeli opisano właściwości puli baz danych.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba jednostek eDTU na bazę danych |Maksymalna liczba jednostek eDTU, z których może korzystać dowolna baza danych w puli, jeśli są dostępne na podstawie użycia innych baz danych w puli. Maksymalna liczba jednostek eDTU na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Należy ustawić odpowiednio wysoką maksymalną liczbę jednostek eDTU na bazę danych, aby obsłużyć szczytowe użycia baz danych. Oczekiwany jest pewien stopień nadmiernego przydzielania, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych, w których nie wszystkie bazy danych jednocześnie osiągają szczytowe użycie. Załóżmy na przykład, że użycie szczytowe na bazę danych wynosi 20 eDTU i tylko 20% ze 100 baz danych w puli osiąga szczytowe użycie w tym samym czasie. Jeśli maksymalna liczba jednostek eDTU na bazę danych wynosi 20 eDTU, zasadne jest nadmiarowe przydzielenie pięciokrotnie większej liczby eDTU dla puli i ustawienie liczby 400 jednostek eDTU na pulę. |
| Minimalna liczba jednostek eDTU na bazę danych |Minimalna liczba jednostek eDTU gwarantowana dla każdej bazy danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalna liczba jednostek eDTU na bazę danych może być ustawiona na 0 i jest to również wartość domyślna. Tę właściwość można ustawić na dowolną wartość między 0 a średnim użyciem jednostek eDTU na bazę danych. Iloczyn liczby baz danych w puli i minimalnej liczby jednostek eDTU na bazę danych nie może przekraczać liczby jednostek eDTU na pulę. Na przykład jeśli pula zawiera 20 baz danych, a minimalna liczba jednostek eDTU na bazę danych wynosi 10 eDTU, liczba jednostek eDTU na pulę musi wynosić co najmniej 200 eDTU. |
| Maksymalna liczba magazyn na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Jednak puli baz danych udostępnianie przydzielone puli magazynu. Nawet jeśli maksymalna całkowita ilość miejsca *na bazę danych* jest ustawiona na większą niż całkowita ilość miejsca dostępne *miejsca puli*, całkowita ilość miejsca faktycznie używana przez wszystkie bazy danych nie będzie mogła przekroczyć limit dostępnej puli. Maksymalny rozmiar bazy danych odwołuje się do maksymalnego rozmiaru plików danych i nie obejmuje miejsca używane przez pliki dziennika. |
|||
 


## <a name="next-steps"></a>Kolejne kroki

- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Informacje o Dtu a Edtu, zobacz [Dtu a Edtu](sql-database-what-is-a-dtu.md).
- Informacje o limity rozmiaru bazy danych tempdb, zobacz https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
