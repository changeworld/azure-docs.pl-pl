---
title: Usługa Azure SQL Database — jednostek dtu w warstwie | Dokumentacja firmy Microsoft
description: Więcej informacji na temat warstwy usługi dla pojedynczej i baz danych puli zapewnienie poziomy wydajności i rozmiaru magazynu.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648653"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Na podstawie jednostek dtu w warstwie model kupna bazy danych SQL Azure 


[Baza danych SQL Azure](sql-database-technical-overview.md) oferuje dwa modele zakupów dla zasobów obliczeniowych, magazynu i zasobów we/wy: model kupna DTU i na podstawie vCore model kupna (wersja zapoznawcza). Następujących tabel i wykresów porównania i kontrastu te dwa modele zakupów.

> [!IMPORTANT]
> Na podstawie vCore model kupna (wersja zapoznawcza), zobacz [na podstawie vCore model kupna](sql-database-service-tiers-vcore.md)


|**Model kupna**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Jednostek dtu w warstwie na podstawie modelu|Ten model jest oparty na powiązane miary obliczeniowej, magazynu i zasobów we/wy. Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu a Edtu, zobacz [co to są Dtu a Edtu](sql-database-what-is-a-dtu.md)?|Najlepsze dla klientów, którzy zasobów proste, wstępnie skonfigurowane opcje.| 
|vCore na podstawie modelu|Ten model umożliwia niezależnie skalowanie zasobów obliczeniowych i magazynu. Umożliwia on również używać korzyści hybrydowe platformy Azure dla programu SQL Server w celu uzyskania oszczędności.|Najlepsze dla klientów, którzy wartość elastyczność, kontroli i przejrzystości.|
||||  

![model cenowy](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>Model kupna na podstawie jednostek dtu w warstwie

Jednostki przepływności bazy danych (DTU) reprezentuje mieszanych pomiarach procesora CPU, pamięci, odczytuje i zapisuje. Model kupna jednostek dtu w warstwie oferuje zestaw wstępnie skonfigurowane pakietów zasobów obliczeniowych i uwzględniony magazynu na dysku różne poziomy wydajności aplikacji. Klienci, którzy preferowane prostotę wstępnie skonfigurowane pakietu i płatności w stałej kwocie każdego miesiąca, może się okazać modelu na podstawie jednostek dtu w warstwie bardziej odpowiednie do potrzeb. W podstawie jednostek dtu w warstwie model kupna, klienci mogą wybrać między **podstawowe**, **standardowe**, i **Premium** warstw usług dla obu [pojedynczybazdanych](sql-database-single-database-resources.md) i [pule elastyczne](sql-database-elastic-pool.md). Warstwy usług są zróżnicowane przez zakres poziomów wydajności o stałej ilości magazynu dołączone, ustalony okres przechowywania kopii zapasowych i ceny ustalonej. Wszystkie warstwy usług zapewniają elastyczność zmiany poziomów wydajności bez przestoju. Pojedyncze bazy danych i pul elastycznych są rozliczane co godzinę oparte na warstwę i poziom wydajności usługi.

> [!IMPORTANT]
> Wystąpienia zarządzane bazy danych SQL w publicznej wersji zapoznawczej nie obsługuje obecnie model kupna jednostek dtu w warstwie. Aby uzyskać więcej informacji, zobacz [wystąpienia zarządzane bazy danych SQL Azure](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Wybieranie warstwy usług w model kupna jednostek dtu w warstwie

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Wydajność limity rozmiaru poziomu i magazynu w model kupna jednostek dtu w warstwie

Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu a Edtu, zobacz [co to są Dtu a Edtu](sql-database-what-is-a-dtu.md)?

### <a name="single-databases"></a>Pojedyncze bazy danych

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar * | 2 GB | 1 TB | 4 TB  | 
| Maksymalna Dtu | 5 | 3000 | 4000 | |
||||||

Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i opcji rozmiar magazynu jest dostępna dla pojedynczych baz danych, zobacz [limity zasobów na podstawie jednostek dtu w warstwie bazy danych SQL dla pojedynczych baz danych](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

### <a name="elastic-pools"></a>Pule elastyczne

| | **Podstawowa** | **Standardowa** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar magazynu na bazie danych *  | 2 GB | 1 TB | 1 TB | 
| Maksymalny rozmiar magazynu na puli * | 156 GB | 4 TB | 4 TB | 
| Maksymalna liczba jednostek Edtu na bazę danych | 5 | 3000 | 4000 | 
| Maksymalna liczba jednostek Edtu na pulę | 1600 | 3000 | 4000 | 
| Maksymalna liczba baz danych dla każdej puli | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> - Rozmiary magazynu większe niż uwzględniona ilość miejsca do magazynowania są dostępne w wersji zapoznawczej i za dodatkową opłatą. Szczegóły można znaleźć w [cenniku usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
> - Więcej niż 1 TB pamięci masowej w warstwie Premium jest dostępna we wszystkich regionach, z wyjątkiem następujących: Wielka Brytania Północna, zachodnie środkowe stany, South2 Wielka Brytania, Chiny Wschodnia, USDoDCentral, Niemcy środkowe, południowo-zachodni USDoDEast, USA wersji dla instytucji rządowych, nam wersji dla instytucji rządowych Południowa centralnej, Niemcy północno-wschodnie, Północna Chin, nam wersji dla instytucji rządowych Wschodnia. Zaplanowane jest zwiększenie dostępności. W pozostałych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
Aby uzyskać więcej informacji o określonych poziomów wydajności i dostępne dla pul elastycznych opcji rozmiaru magazynu, zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>Kolejne kroki

- Szczegółowe informacje o określonych poziomów wydajności i dostępnych wyborów rozmiar magazynu, zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits.md) i [limity zasobów na podstawie vCore bazy danych SQL](sql-database-vcore-resource-limits.md).
- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Dowiedz się więcej o [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md)
