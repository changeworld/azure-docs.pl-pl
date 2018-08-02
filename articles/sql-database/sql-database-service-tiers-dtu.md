---
title: Warstwy usługi SQL Database platformy Azure — jednostek DTU | Dokumentacja firmy Microsoft
description: Więcej informacji na temat warstw usług dla pojedynczej puli baz danych i do poziomów wydajności i rozmiaru magazynu.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411824"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Wybieranie warstwy usług oparte na jednostkach DTU, poziom wydajności i zasobów magazynu 

Warstwy usługi są zróżnicowane według szeroką gamę poziomów wydajności przy użyciu stałej ilość miejsca do magazynowania, ustalony okres przechowywania kopii zapasowych i stałej cenie. Wszystkie warstwy usługi oferują elastyczność zmiany poziomów wydajności bez przestojów. Pojedyncze bazy danych i pul elastycznych są rozliczane proporcjonalnie, godzinowo usługi warstwy i poziomu wydajności.

> [!IMPORTANT]
> Wystąpienie zarządzane SQL Database, obecnie w publicznej wersji zapoznawczej nie obsługuje model zakupu jednostek DTU. Aby uzyskać więcej informacji, zobacz [wystąpienia zarządzanego Azure SQL Database](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Wybieranie warstwy usług oparte na jednostkach DTU

Wybieranie warstwy usług zależy przede wszystkim ciągłości biznesowej, magazynu i wymagań dotyczących wydajności.
||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: |--:| --:| --:| 
|Docelowego obciążenia|Rozwoju i produkcji|Rozwoju i produkcji|Rozwoju i produkcji||
|Umowa SLA dotycząca czasu dostępności|99,99%|99,99%|99,99%|N/d w wersji zapoznawczej|
|Przechowywanie kopii zapasowych|7 dni|35 dni|35 dni|
|Procesor CPU|Małe|Niska, średnia, wysoka|Średni i wysoki|
|Przepustowość operacji We/Wy (w przybliżeniu) |2.5 operacje We/Wy na jednostkach DTU| 2.5 operacje We/Wy na jednostkach DTU | 48 operacje We/Wy na jednostkach DTU|
|We/Wy, czas oczekiwania (w przybliżeniu)|5 ms (odczyt), 10 ms (zapis)|5 ms (odczyt), 10 ms (zapis)|2 ms (odczyt/zapis)|
|Indeksowanie magazynu kolumn |ND|S3 i nowsze wersje|Obsługiwane|
|Przetwarzanie OLTP danych w pamięci|ND|ND|Obsługiwane|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Limity liczby jednostek DTU i magazynu pojedynczej bazy danych

Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu i Edtu, zobacz [co to są jednostki Dtu i Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar magazynu | 2 GB | 1 TB | 4 TB  | 
| Maksymalna liczba jednostek Dtu | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastyczna pula — eDTU, magazynu i limity baza danych w puli

| | **Podstawowa** | **Standardowa** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar magazynu na bazę danych  | 2 GB | 1 TB | 1 TB | 
| Maksymalny rozmiar magazynu na pulę | 156 GB | 4 TB | 4 TB | 
| Maksymalna liczba jednostek Edtu na bazę danych | 5 | 3000 | 4000 | 
| Maksymalna liczba jednostek Edtu na pulę | 1600 | 3000 | 4000 | 
| Maksymalna liczba baz danych na pulę | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępna we wszystkich regionach z wyjątkiem następujących: zachodnio-środkowe stany USA, Chiny wschodnie, USDoDCentral, Niemcy środkowe, Południowy Zachód klientów rządowych USA USDoDEast, USGov — Iowa, Niemcy północno-wschodnie, Chin północnych. W pozostałych regionach maksymalna wielkość pamięci w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne baza danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i opcje rozmiaru magazynu jest dostępne dla pojedynczych baz danych, zobacz [limity zasobów na podstawie jednostek DTU bazy danych SQL dla pojedynczych baz danych](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Szczegółowe informacje na temat określonych poziomów wydajności i opcji rozmiaru magazynu, dostępne dla elastycznych pul, [limity zasobów na podstawie jednostek DTU bazy danych SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
