---
title: Limity zasobów — wystąpienie zarządzane
description: Ten artykuł zawiera omówienie limitów zasobów usługi Azure SQL Database dla wystąpień zarządzanych.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365391"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Omówienie limitów zasobów zarządzanych wystąpienia wystąpienia usługi Azure SQL Database

Ten artykuł zawiera omówienie właściwości technicznych i limitów zasobów dla wystąpienia zarządzanego usługi Azure SQL Database i zawiera informacje dotyczące sposobu żądania zwiększenia tych limitów.

> [!NOTE]
> Różnice w obsługiwanych funkcjach i instrukcjach T-SQL można znaleźć [w pomocy różnic y funkcji](sql-database-features.md) i obsługi instrukcji [T-SQL](sql-database-managed-instance-transact-sql-information.md). Aby uzyskać ogólne różnice między warstwami usług w pojedynczej bazie danych i wystąpieniu zarządzanym, zobacz [Porównanie warstwy usługi](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Charakterystyka generowania sprzętu

Wystąpienie zarządzane ma cechy i limity zasobów, które zależą od podstawowej infrastruktury i architektury. Wystąpienie zarządzanej usługi Azure SQL Database można wdrożyć na dwóch generacjach sprzętu: Gen4 i Gen5. Generacje sprzętu mają różne cechy, jak opisano w poniższej tabeli:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Sprzęt | Procesory Intel E5-2673 v3 (Haswell) 2,4 GHz, dołączony vCore SSD = 1 PP (rdzeń fizyczny) | Procesory Intel E5-2673 v4 (Broadwell) 2,3 GHz i Intel SP-8160 (Skylake), szybkie NVMe SSD, vCore=1 LP (hyper-thread) |
| Liczba rdzeni wirtualnych | 8, 16, 24 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Maksymalna ilość pamięci (stosunek pamięci do rdzenia) | 7 GB na r/r<br/>Dodaj więcej wirtualnych, aby uzyskać więcej pamięci. | 5,1 GB na r/r<br/>Dodaj więcej wirtualnych, aby uzyskać więcej pamięci. |
| Maksymalna pamięć OLTP w pamięci | Limit wystąpień: 1-1,5 GB na r/r| Limit wystąpień: 0,8 - 1,65 GB na vCore |
| Maksymalna ilość zarezerwowanego miejsca na wystąpienie |  Przeznaczenie ogólne: 8 TB<br/>Krytyczne dla biznesu: 1 TB | Przeznaczenie ogólne: 8 TB<br/> Krytyczne dla firmy 1 TB, 2 TB lub 4 TB w zależności od liczby rdzeni |

> [!IMPORTANT]
> - Sprzęt Gen4 jest wycofywany i nie jest już dostępny dla nowych wdrożeń. Wszystkie nowe wystąpienia zarządzane muszą być wdrożone na sprzęcie Gen5.
> - Rozważ [przeniesienie zarządzanych wystąpień na sprzęt gen 5,](sql-database-service-tiers-vcore.md) aby uzyskać szerszy zakres skalowalności vCore i magazynu, przyspieszonej sieci, najlepszej wydajności we/wy i minimalnego opóźnienia.

### <a name="in-memory-oltp-available-space"></a>Dostępne miejsce w pamięci OLTP 

Ilość miejsca OLTP w pamięci w warstwie usług [Krytyczne dla firmy](sql-database-service-tier-business-critical.md) zależy od liczby owo wirtualnych i generowania sprzętu. W poniższej tabeli wymieniono limity pamięci, które mogą być używane dla obiektów OLTP w pamięci.

| Przestrzeń OLTP w pamięci  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 rdzenie wirtualne  | 3,14 GB | |   
| 8 rdzeni wirtualnych  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Charakterystyka warstwy usług

Wystąpienie zarządzane ma dwie warstwy usług: [Ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md) i [Krytyczny dla firmy](sql-database-service-tier-business-critical.md). Te warstwy zapewniają [różne możliwości,](sql-database-service-tiers-general-purpose-business-critical.md)jak opisano w poniższej tabeli.

> [!Important]
> Warstwa usługi o znaczeniu krytycznym dla firmy zapewnia dodatkową wbudowaną kopię wystąpienia (repliki pomocniczej), która może być używana do obciążenia tylko do odczytu. Jeśli można oddzielić zapytania do odczytu i zapisu i tylko do odczytu/analityczne/raportowanie zapytań, otrzymujesz dwa razy wirtualne i pamięci w tej samej cenie. Replika pomocnicza może pozostawać w czasie kilku sekund za wystąpieniem podstawowym, więc jest przeznaczona do odciążania raportowania/obciążenia analitycznego, które nie wymaga dokładnego bieżącego stanu danych. W poniższej tabeli **kwerendy tylko** do odczytu są kwerendami, które są wykonywane w replice pomocniczej.

| **Funkcja** | **Cel ogólny** | **Krytyczne dla biznesu** |
| --- | --- | --- |
| Liczba rdzeni wirtualnych\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Ta sama liczba korów wirtualnych jest przeznaczona dla zapytań tylko do odczytu. |
| Maksymalna ilość pamięci | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore)<br/>Dodaj więcej wirtualnych, aby uzyskać więcej pamięci. | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore) dla zapytań odczytu i zapisu<br/>+ dodatkowe 20,4 GB - 408 GB (5,1 GB/vCore) dla zapytań tylko do odczytu.<br/>Dodaj więcej wirtualnych, aby uzyskać więcej pamięci. |
| Maksymalny rozmiar magazynu wystąpienia (zarezerwowany) | - 2 TB dla 4 vCorów (tylko Gen5)<br/>- 8 TB dla innych rozmiarów | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB na 4, 8, 16 vCores<br/>- 2 TB dla 24 vCores<br/>- 4 TB dla 32, 40, 64, 80 vCores |
| Maksymalny rozmiar bazy danych | Do aktualnie dostępnego rozmiaru wystąpienia (maks. 2 TB - 8 TB w zależności od liczby korów wirtualnych). | Do aktualnie dostępnego rozmiaru wystąpienia (maksymalnie 1 TB - 4 TB w zależności od liczby owo wirtualnych). |
| Maksymalny rozmiar bazy danych tempDB | Ograniczona do 24 GB/vCore (96 - 1920 GB) i obecnie dostępny rozmiar magazynu wystąpienia.<br/>Dodaj więcej vCores, aby uzyskać więcej miejsca tempdb.<br/> Rozmiar pliku dziennika jest ograniczony do 120 GB.| Do aktualnie dostępnego rozmiaru magazynu wystąpienia. |
| Maksymalna liczba baz danych na wystąpienie | 100, chyba że limit rozmiaru magazynu wystąpienia został osiągnięty. | 100, chyba że limit rozmiaru magazynu wystąpienia został osiągnięty. |
| Maksymalna liczba plików bazy danych na wystąpienie | Maksymalnie 280, chyba że rozmiar magazynu wystąpienia lub limit [miejsca alokacji magazynu w wersji Premium w wersji Premium](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) został osiągnięty. | 32 767 plików na bazę danych, chyba że osiągnięto limit rozmiaru magazynu wystąpienia. |
| Maksymalny rozmiar pliku danych | Ograniczona do aktualnie dostępnego rozmiaru magazynu wystąpień (maks. 2 TB - 8 TB) i [miejsca alokacji magazynu w wersji Premium platformy Azure.](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | Ograniczona do aktualnie dostępnego rozmiaru magazynu wystąpień (do 1 TB - 4 TB). |
| Maksymalny rozmiar pliku dziennika | Ograniczona do 2 TB i obecnie dostępny rozmiar magazynu wystąpienia. | Ograniczona do 2 TB i obecnie dostępny rozmiar magazynu wystąpienia. |
| Dane/Dziennik IOPS (przybliżony) | Do 30-40 K IOPS na wystąpienie*, 500 - 7500 na plik<br/>\*[Zwiększ rozmiar pliku, aby uzyskać więcej we/wy](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Dodaj więcej koperów wirtualnych, aby uzyskać lepszą wydajność we/wy. |
| Rejestrowanie limitu przepływności zapisu (na wystąpienie) | 3 MB/s na r/r<br/>Maksymalnie 22 MB/s | 4 MB/s na r/r<br/>Maksymalnie 48 MB/s |
| Przepustowość danych (przybliżona) | 100 - 250 MB/s na plik<br/>\*[Zwiększ rozmiar pliku, aby uzyskać lepszą wydajność we/wy](#file-io-characteristics-in-general-purpose-tier) | Nie ograniczając się. |
| Opóźnienie we/wy magazynu (przybliżone) | 5-10 ms | 1-2 ms |
| Oltp w pamięci | Nieobsługiwane | Dostępne, [rozmiar zależy od liczby vCore](#in-memory-oltp-available-space) |
| Maksymalna liczba sesji | 30000 | 30000 |
| [Repliki tylko do odczytu](sql-database-read-scale-out.md) | 0 | 1 (w cenie) |

> [!NOTE]
> - **Obecnie dostępny rozmiar magazynu wystąpienia** jest różnica między rozmiar wystąpienia zarezerwowanego i używanego miejsca do magazynowania.
> - Rozmiar pliku danych i dziennika w bazach danych użytkownika i systemu są uwzględniane w rozmiarze magazynu wystąpienia, który jest porównywany z limitem maksymalnego rozmiaru magazynu. Użyj widoku systemu <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files,</a> aby określić całkowitą ilość używanego miejsca przez bazy danych. Dzienniki błędów nie są zachowywane i nie są uwzględniane w rozmiarze. Kopie zapasowe nie są uwzględniane w rozmiarze magazynu.
> - Przepływność i we/wy w warstwie ogólnego przeznaczenia zależą również od [rozmiaru pliku,](#file-io-characteristics-in-general-purpose-tier) który nie jest jawnie ograniczony przez wystąpienie zarządzane.
> - Można utworzyć inną replikę czytelną w różnych regionach platformy Azure przy użyciu grup automatycznego trybu failover.
> - Maksymalna liczba wystąpień We/Wy zależą od układu pliku i dystrybucji obciążenia. Na przykład, jeśli utworzysz pliki o mocy 7 x 1 TB z maksymalną 5K IOPS i 7 małych plików (mniejszych niż 128 GB) z 500 IOPS każdy, można uzyskać 38500 IOPS na wystąpienie (7x5000 + 7x500), jeśli obciążenie może korzystać ze wszystkich plików. Należy zauważyć, że pewna ilość we/wy jest również używana do automatycznego tworzenia kopii zapasowych.

> [!NOTE]
> Więcej informacji na temat [limitów zasobów w pulach wystąpień zarządzanych można znaleźć w tym artykule](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Charakterystyka we/wy pliku w warstwie Ogólnego przeznaczenia

W warstwie usług ogólnego przeznaczenia każdy plik bazy danych otrzymuje dedykowane usługi We/Wy i przepływność, która zależy od rozmiaru pliku. Większe pliki są coraz więcej We/Wy i przepływności. Charakterystyki we/wy plików bazy danych są przedstawione w poniższej tabeli:

| Rozmiar pliku | >=0 i <=128 GiB | >128 i <=256 GiB | >256 i <= 512 GiB | >0,5 i <=1 TiB    | >1 i <=2 TiB    | >2 i <=4 TiB | >4 i <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| We/Wy na plik       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Przepływność na plik | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Jeśli zauważysz duże opóźnienie we/wy w niektórych plikach bazy danych lub zobaczysz, że usługi We/Wy/przepływność osiągają limit, możesz zwiększyć wydajność, [zwiększając rozmiar pliku](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Istnieją również limity na poziomie wystąpienia, takie jak maksymalna przepływność zapisu dziennika 22 MB/s, więc może nie być w stanie dotrzeć do pliku w całym pliku dziennika, ponieważ osiągasz limit przepływności wystąpienia.

## <a name="supported-regions"></a>Obsługiwane regiony

Wystąpienia zarządzane można tworzyć tylko w [obsługiwanych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który obecnie nie jest obsługiwany, można [wysłać żądanie pomocy technicznej za pośrednictwem portalu Azure](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Wystąpienie zarządzane obsługuje obecnie wdrażanie tylko w następujących typach subskrypcji:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dostawca usług w chmurze (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subskrypcje z miesięcznymi kredytami platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionalne ograniczenia zasobów

Obsługiwane typy subskrypcji może zawierać ograniczoną liczbę zasobów na region. Wystąpienie zarządzane ma dwa domyślne limity na region platformy Azure (które można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w witrynie Azure portal w](quota-increase-request.md) zależności od typu subskrypcji:

- **Limit**podsieci: Maksymalna liczba podsieci, w których wystąpienia zarządzane są wdrażane w jednym regionie.
- **Limit jednostek vCore**: Maksymalna liczba jednostek vCore, które można wdrożyć we wszystkich wystąpieniach w jednym regionie. Jeden procesor gp vCore używa jednej jednostki vCore, a jedna bc vCore zajmuje 4 jednostki vCore. Całkowita liczba wystąpień nie jest ograniczona, o ile mieści się w limicie jednostki vCore.

> [!Note]
> Te limity są ustawieniami domyślnymi, a nie ograniczeniami technicznymi. Limity można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w witrynie Azure portal,](quota-increase-request.md) jeśli potrzebujesz więcej zarządzanych wystąpień w bieżącym regionie. Alternatywnie można utworzyć nowe wystąpienia zarządzane w innym regionie platformy Azure bez wysyłania żądań pomocy technicznej.

W poniższej tabeli przedstawiono **domyślne limity regionalne** dla obsługiwanych typów subskrypcji (domyślne limity można rozszerzyć za pomocą żądania pomocy technicznej opisanego poniżej):

|Typ subskrypcji| Maksymalna liczba podsieci wystąpienia zarządzanego | Maksymalna liczba jednostek vCore* |
| :---| :--- | :--- |
|Pay-as-you-go|3|320|
|CSP |8 (15 w niektórych regionach**)|960 (1440 w niektórych regionach**)|
|Dev/Test płatności zgodnie z rzeczywistym poł.|3|320|
|Enterprise — tworzenie i testowanie|3|320|
|Ea|8 (15 w niektórych regionach**)|960 (1440 w niektórych regionach**)|
|Visual Studio Enterprise|2 |64|
|Platformy Visual Studio Professional i MSDN|2|32|

\*Podczas planowania wdrożeń należy wziąć pod uwagę, że warstwa usług o znaczeniu krytycznym dla firmy (BC) wymaga czterech (4) razy większej pojemności generacji niż warstwa usług ogólnego przeznaczenia (GP). Na przykład: 1 gp vCore = 1 jednostka vCore i 1 BC vCore = 4 jednostki vCore. Aby uprościć analizę zużycia względem domyślnych limitów, podsumuj jednostki vCore we wszystkich podsieciach w regionie, w którym są wdrażane wystąpienia zarządzane, i porównaj wyniki z limitami jednostek wystąpienia dla typu subskrypcji. **Maksymalna liczba jednostek vCore** ma zastosowanie do każdej subskrypcji w regionie. Nie ma limitu na poszczególne podsieci, z tą różnicą, że suma wszystkich owo wirtualnych wdrożonych w wielu podsieciach musi być niższa lub równa **maksymalnej liczbie jednostek vCore**.

\*\*Większe limity podsieci i elementów wirtualnych są dostępne w następujących regionach: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-Środkowe stany USA, Azja Południowo-Wschodnia, Wielka Brytania Południowa, Europa Zachodnia, Zachodnie stany USA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Żądanie zwiększenia przydziału dla wystąpienia zarządzanego SQL

Jeśli potrzebujesz więcej wystąpień zarządzanych w bieżących regionach, wyślij żądanie pomocy technicznej, aby rozszerzyć przydział przy użyciu witryny Azure portal. Aby uzyskać więcej informacji, zobacz [Żądanie zwiększenia przydziału dla usługi Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat wystąpienia zarządzanego, zobacz [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Aby uzyskać informacje o cenach, zobacz [cennik zarządzanych wystąpień bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [przewodnik szybki start](sql-database-managed-instance-get-started.md).
