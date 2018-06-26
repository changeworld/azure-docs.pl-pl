---
title: Warstwy usługi bazy danych SQL Azure - jednostek dtu w warstwie | Dokumentacja firmy Microsoft
description: Więcej informacji na temat warstwy usługi dla pojedynczej i baz danych puli zapewnienie poziomy wydajności i rozmiaru magazynu.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: cf17ec616819da94678f2ae4f0f0ca283f99f629
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750428"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Wybieranie warstwy usług na podstawie jednostek dtu w warstwie, poziom wydajności i zasobów magazynowania 

Warstwy usług są zróżnicowane przez zakres poziomów wydajności o stałej ilości magazynu dołączone, ustalony okres przechowywania kopii zapasowych i ceny ustalonej. Wszystkie warstwy usług zapewniają elastyczność zmiany poziomów wydajności bez przestoju. Pojedyncze bazy danych i pul elastycznych są rozliczane co godzinę oparte na warstwę i poziom wydajności usługi.

> [!IMPORTANT]
> Wystąpienia zarządzane bazy danych SQL w publicznej wersji zapoznawczej nie obsługuje obecnie model kupna jednostek dtu w warstwie. Aby uzyskać więcej informacji, zobacz [wystąpienia zarządzane bazy danych SQL Azure](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Wybieranie warstwy na podstawie jednostek dtu w warstwie usług

Wybór warstwy usług zależy przede wszystkim ciągłość prowadzenia działalności biznesowej, magazynu i wymagania dotyczące wydajności.
||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: |--:| --:| --:| 
|Obciążenie docelowego|Projektowanie i produkcji|Projektowanie i produkcji|Projektowanie i produkcji||
|Umowa SLA dotycząca czasu dostępności|99,99%|99,99%|99,99%|N/d znajduje się w wersji zapoznawczej|
|Przechowywanie kopii zapasowych|7 dni|35 dni|35 dni|
|Procesor CPU|Małe|Niski, Średni, wysoki|Średni i wysoki|
|Wydajność We/Wy (przybliżony) |2.5 IOPS dla jednostek dtu w warstwie| 2.5 IOPS dla jednostek dtu w warstwie | 48 IOPS dla jednostek dtu w warstwie|
|We/Wy, czas oczekiwania (w przybliżeniu)|ms 5 (odczyt), 10 ms (Zapisz)|ms 5 (odczyt), 10 ms (Zapisz)|ms 2 (odczyt/zapis)|
|Indeksowanie magazynu kolumn |ND|S3 i nowsze|Obsługiwane|
|OLTP w pamięci|ND|ND|Obsługiwane|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Limity DTU i Magazyn pojedynczej bazy danych

Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu a Edtu, zobacz [co to są Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar magazynu | 2 GB | 1 TB | 4 TB  | 
| Maksymalna Dtu | 5 | 3000 | 4000 | |
||||||

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU puli elastycznej, magazynu i limity puli bazy danych

| | **Podstawowa** | **Standardowa** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar magazynu na bazy danych  | 2 GB | 1 TB | 1 TB | 
| Maksymalny rozmiar magazynu dla każdej puli | 156 GB | 4 TB | 4 TB | 
| Maksymalna liczba jednostek Edtu na bazę danych | 5 | 3000 | 4000 | 
| Maksymalna liczba jednostek Edtu na pulę | 1600 | 3000 | 4000 | 
| Maksymalna liczba baz danych dla każdej puli | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Więcej niż 1 TB pamięci masowej w warstwie Premium jest dostępna we wszystkich regionach, z wyjątkiem następujących: Wielka Brytania Północna, zachodnie środkowe stany, South2 Wielka Brytania, Chin Wschodnia, USDoDCentral, Niemcy środkowe, południowo-zachodni USDoDEast, USA wersji dla instytucji rządowych, nam wersji dla instytucji rządowych Południowa centralnej, Niemcy północno-wschodnie, Chiny Północno-wschodnie wersji dla instytucji rządowych Stanów Zjednoczonych. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i opcji rozmiar magazynu jest dostępna dla pojedynczych baz danych, zobacz [limity zasobów na podstawie jednostek dtu w warstwie bazy danych SQL dla pojedynczych baz danych](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Aby uzyskać więcej informacji o określonych poziomów wydajności i dostępne dla pul elastycznych opcji rozmiaru magazynu, zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).