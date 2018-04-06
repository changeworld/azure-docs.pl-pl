---
title: Usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat warstwy usługi dla pojedynczej i baz danych puli zapewnienie poziomy wydajności i rozmiaru magazynu.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: a4474aec212084006becd02f317dabae6e731d98
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Co to są warstwach usług bazy danych SQL Azure?

[Baza danych SQL Azure](sql-database-technical-overview.md) oferuje dwa modele zakupów dla zasobów obliczeniowych, magazynu i zasobów we/wy: model kupna DTU i na podstawie vCore model kupna (wersja zapoznawcza). Następujących tabel i wykresów porównania i kontrastu te dwa modele zakupów.

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

### <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Wybieranie warstwy usług w model kupna jednostek dtu w warstwie

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

### <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Wydajność limity rozmiaru poziomu i magazynu w model kupna jednostek dtu w warstwie

Poziomy wydajności są wyrażane jako liczba jednostek DTU (Database Transaction Unit) dla pojedynczych baz danych oraz jako liczba jednostek eDTU (elastic Database Transaction Unit) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu a Edtu, zobacz [co to są Dtu a Edtu](sql-database-what-is-a-dtu.md)?

#### <a name="single-databases"></a>Pojedyncze bazy danych

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar * | 2 GB | 1 TB | 4 TB  | 
| Maksymalna Dtu | 5 | 3000 | 4000 | |
||||||

Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i opcji rozmiar magazynu jest dostępna dla pojedynczych baz danych, zobacz [limity zasobów na podstawie jednostek dtu w warstwie bazy danych SQL dla pojedynczych baz danych](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

#### <a name="elastic-pools"></a>Pule elastyczne

| | **Podstawowa** | **Standardowa** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maksymalny rozmiar magazynu na bazie danych *  | 2 GB | 1 TB | 1 TB | 
| Maksymalny rozmiar magazynu na puli * | 156 GB | 4 TB | 4 TB | 
| Maksymalna liczba jednostek Edtu na bazę danych | 5 | 3000 | 4000 | 
| Maksymalna liczba jednostek Edtu na pulę | 1600 | 3000 | 4000 | 
| Maksymalna liczba baz danych dla każdej puli | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Magazyn o rozmiarze większym niż ilość miejsca do magazynowania są dostępne w wersji zapoznawczej dodatkowych kosztów za dodatkową opłatą. Szczegóły można znaleźć w [cenniku usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* W warstwie Premium ponad 1 TB miejsca do magazynowania jest obecnie dostępny w następujących regionach: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Środkowe stany USA, Francja Środkowa, Niemcy Środkowe, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Wschodnie stany USA 2, Zachodnie stany USA, Administracja USA — Wirginia i Europa Zachodnia. Więcej informacji można znaleźć na stronie [bieżących ograniczeń poziomów P11–P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Aby uzyskać więcej informacji o określonych poziomów wydajności i dostępne dla pul elastycznych opcji rozmiaru magazynu, zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

## <a name="vcore-based-purchasing-model-preview"></a>na podstawie vCore model kupna (wersja zapoznawcza)

Wirtualne podstawowe reprezentuje Procesora logicznego oferowany z opcją wybór między generacje sprzętu. Na podstawie vCore model kupna (wersja zapoznawcza) zapewnia elastyczność, przezroczystość zużycia pojedynczego zasobu, formantowi i łatwe do tłumaczenia lokalne wymagania obciążenia w chmurze. Ten model umożliwia skalowanie możliwości obliczeniowych, pamięci oraz Magazyn ustalane na podstawie jego potrzeb obciążenia. W oparciu vCore model kupna klientów można wybrać ogólnego przeznaczenia i warstwy krytycznych usług biznesowych (wersja zapoznawcza) dla obu [baz danych z jednym](sql-database-single-database-resources.md) i [pule elastyczne](sql-database-elastic-pool.md). 

Warstwy usług są zróżnicowane przez zakres poziomów wydajności, projekt wysokiej dostępności, izolacji błędów, typów pamięci masowej i zakres we/wy. Klienta należy skonfigurować osobno wymagane okres przechowywania i przechowywania kopii zapasowych. Gdy używany jest vCore model, pojedynczych baz danych i pul elastycznych kwalifikują się do się do 30 procent oszczędności za pomocą [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

W podstawie vCore zakupów modelu klientów płatności dla:
- Obliczeniowe (warstwy usług + liczba vCores + generowania sprzętu) *
- Typ i ilość miejsca w magazynie danych i dziennika 
- Liczba operacji We-Wy **
- Kopia zapasowa magazynu (RA-GRS) ** 

\* W początkowej publicznej wersji zapoznawczej procesorów logicznych 4 Gen są oparte na Intel E5-2673 v3 procesorów 2,4 GHz (Haswell)

\*\* Podczas udostępniania wersji zapoznawczej mogą 7 dni i IOs

> [!IMPORTANT]
> Obliczeniowe, IOs, danych i przechowywanie dziennika są naliczane na bazę danych lub puli elastycznej. Magazyn kopii zapasowych jest pobierana dla każdej bazy danych. Szczegóły wystąpienia zarządzane opłat można znaleźć [wystąpienia zarządzane bazy danych SQL Azure](sql-database-managed-instance.md).

### <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Wybieranie warstwy usług, obliczeniowych, pamięci, magazynu i zasobów we/wy

Konwertowanie na podstawie vCore model kupna umożliwia niezależnie skalowania zasobów obliczeniowych i magazynu, zgodne lokalnymi wydajności i zoptymalizowania cen. Jeśli bazy danych lub puli elastycznej zużywa ponad 300 Konwersja jednostek dtu w warstwie vCore może zmniejszyć koszt. Możesz przekonwertować przy użyciu interfejsu API wyboru lub przy użyciu portalu Azure, bez przestojów. Jednak konwersja nie jest wymagana. Jeśli model kupna jednostek dtu w warstwie spełnia wymagania biznesowe i wydajności, na których, można nadal używać. Jeśli zdecydujesz się do konwersji z modelu jednostek dtu w warstwie vCore modelu, należy wybrać poziom wydajności przy użyciu następujących zasadą: każdego 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 vCore, a każdy 125 jednostek DTU w warstwie Premium vCore co najmniej 1.

Poniższa tabela pomoże Ci poznać różnice między tymi dwoma warstwami:

||**Ogólnego przeznaczenia**|**Biznesowe krytyczne**|
|---|---|---|
|Najlepsze dla|Większości obciążeń biznesowych. Oferty budżecie obiektowe zrównoważony i skalowalnych zasobów obliczeniowych i magazynu opcje.|Aplikacje biznesowe z wysokimi wymaganiami w zakresie operacji wejścia/wyjścia. Oferuje najwyższą odporność na awarie, korzystając z kilku izolowanych replik.|
|Wystąpienia obliczeniowe|vCore 1-16|vCore 1-16|
|Memory (Pamięć)|7 GB na podstawowe |7 GB na podstawowe |
|Magazyn|Magazyn zdalny Premium, 5 GB – 4 TB|Lokalny magazyn dysków SSD, 5 GB – 1 TB|
|Wydajność We/Wy (przybliżony)|500 IOPS na vCore z 7500 maksymalna liczba IOPS|5000 IOPS na podstawowe|
|Dostępność|1 repliki, bez skalowania odczytu|repliki 3, 1 [skali odczytu](sql-database-read-scale-out.md), strefy HA geograficznie nadmiarowy|
|Tworzenie kopii zapasowych|RA-GRS, 7-35 dni (domyślnie co 7 dni)|RA-GRS, 7-35 dni (domyślnie co 7 dni) *|
|W pamięci|ND|Obsługiwane|
|||

\* Podczas udostępniania wersji zapoznawczej okres przechowywania kopii zapasowych nie konfiguruje się i zostanie rozwiązany do 7 dni.

> [!IMPORTANT]
> Jeśli potrzebujesz mniejszej niż jeden vCore pojemności obliczeń, użyj model kupna jednostek dtu w warstwie.

Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i opcje rozmiaru magazynu dostępne dla pojedynczej bazy danych, zobacz [limity zasobów na podstawie vCore bazy danych SQL dla pojedynczych baz danych](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels) i elastyczne pule można znaleźć [bazy danych SQL limity pul elastycznych zasobów na podstawie vCore](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania. 

### <a name="storage-considerations"></a>Zagadnienia dotyczące magazynu

Rozważ następujące źródła:
- Magazyn przydzielony jest używany przez pliki danych (MDF) i pliki dziennika (LDF) plików.
- Każdy poziom wydajności obsługuje maksymalny rozmiar bazy danych, z domyślny maksymalny rozmiar 32 GB.
- Po skonfigurowaniu rozmiar wymaganej bazy danych (rozmiar MDF) 30% dodatkowego miejsca do magazynowania jest automatycznie dodawany do obsługi LDF
- Można wybrać dowolnego rozmiaru bazy danych między 10 GB, a maksymalna obsługiwana wartość
 - Dla magazynu w warstwie standardowa Zwiększ lub Zmniejsz rozmiar w przyrostach 10 GB
 - Dla usługi Premium storage Zwiększ lub Zmniejsz rozmiar w przyrostach 250 GB
- W warstwie usług ogólnego przeznaczenia `tempdb` używa dołączonych dysków SSD i kosztów magazynowania jest uwzględniony w cenie vCore.
- W warstwie usługi biznesowe krytyczne `tempdb` dołączonych dysków SSD z plików MDF i LDF i koszt przechowywania bazy danych tempDB jest uwzględniony w cenie vCore udziałów.

> [!IMPORTANT]
> Naliczane są opłaty za całkowita ilość miejsca przydzielonego na plików MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [widoku sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

### <a name="backups-and-storage"></a>Kopie zapasowe i magazynu

Magazyn kopii zapasowych bazy danych jest przydzielony do obsługi punktu w czasie przywracania (PITR) i długi okres przechowywania STYLEM możliwościach bazy danych SQL. Ta pamięć masowa jest przydzielony osobno dla każdej bazy danych i rozliczane jako dwa osobne na opłat baz danych. 

- **PITR**: jedna baza danych, kopie zapasowe są kopiowane do magazynu RA-GRS są automatycznie. Rozmiar magazynu zwiększa dynamicznie w miarę tworzenia nowych kopii zapasowych.  Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Użycia magazynu zależy szybkość zmian w bazie danych i okresu przechowywania. Można skonfigurować okres przechowywania osobne dla każdej bazy danych od 7 do 35 dni. Kwota minimalna magazynu równy 1 x rozmiar danych jest dostarczany bez dodatkowych opłat. Dla baz danych ta wartość jest wystarczająca do przechowywania kopii zapasowych 7 dni.
- **Od lewej do prawej**: baza danych SQL udostępnia opcję konfigurowania długoterminowego przechowywania tworzenia pełnych kopii zapasowych maksymalnie 10 lat. Jeśli od lewej do prawej jest włączona, kopie zapasowe te są przechowywane w magazynie RA-GRS automatycznie, ale można kontrolować, jak często kopie zapasowe są kopiowane. Aby spełnić wymagania zgodności różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczne i roczne. Ta konfiguracja określi, ile miejsca do magazynowania, które będą używane do tworzenia kopii zapasowych od lewej do prawej. Kalkulator cen od lewej do prawej służy do szacowania kosztów magazynowania od lewej do prawej. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

### <a name="azure-hybrid-use-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W oparciu vCore model kupna należy wymienić istniejących licencji dla rabaty na korzystanie z bazy danych SQL [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Tego świadczenia usługi Azure umożliwia, przy użyciu lokalnej licencji programu SQL Server można zapisać do 30% w bazie danych SQL Azure przy użyciu lokalnej licencji programu SQL Server z Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

#### <a name="migration-of-single-databases-with-geo-replication-links"></a>Migracja pojedynczej bazy danych — replikacja geograficzna łącza

Migrowania jednostek dtu w warstwie na podstawie modelu na podstawie vCore model jest podobny do uaktualnienia lub zmiany na starszą wersję — replikacja geograficzna relacje między bazami danych standardowa i Premium. Nie wymaga się, że przerywa — replikacja geograficzna, ale użytkownik musi być zgodna z regułami sekwencjonowania. Podczas uaktualniania, należy najpierw uaktualnić dodatkowej bazy danych, a następnie Uaktualnij serwera podstawowego. Podczas zmiany na starszą wersję, odwrócić kolejność: należy najpierw obniżyć podstawowej bazy danych i starszą wersję pomocniczą. 

Korzystając z replikacja geograficzna między dwoma pule elastyczne, zdecydowanie zaleca się wyznaczyć jedną pulę jako podstawowy, a drugi — lokacji dodatkowej. W takim przypadku Migrowanie pule elastyczne należy używać te same wskazówki.  Jednak jest technicznie możliwe, że puli elastycznej zawiera podstawowe i pomocnicze bazy danych jest. W takim przypadku poprawnie migracji należy należy traktować puli z większe użycie jako "primary" i odpowiednio postępuj zgodnie z regułami sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące scenariuszy migracji: 

|Bieżącej warstwy usług|Warstwy usług docelowych|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa (Standard)|Zastosowania ogólne|Penetracja|Można migrować w dowolnej kolejności, ale konieczne jest zapewnienie vCore odpowiednie zmiany rozmiaru *|
|Premium|Krytyczne dla działania firmy|Penetracja|Można migrować w dowolnej kolejności, ale konieczne jest zapewnienie odpowiedniej vCore zmiany rozmiaru *|
|Standardowa (Standard)|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację dodatkowej najpierw|
|Krytyczne dla działania firmy|Standardowa (Standard)|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Premium|Zastosowania ogólne|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Premium|Uaktualnienie|Należy przeprowadzić migrację dodatkowej najpierw|
|Krytyczne dla działania firmy|Zastosowania ogólne|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację dodatkowej najpierw|
||||

\* Każdy 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 vCore, a każdy 125 jednostek DTU w warstwie Premium vCore co najmniej 1

#### <a name="migration-of-failover-groups"></a>Migracja grup trybu failover 

Migracja grup trybu failover z wieloma bazami danych wymaga migracji podstawowych i pomocniczych baz danych. Podczas tego procesu mają zastosowanie tego samego zagadnienia i reguły sekwencjonowania. Po baz danych są konwertowane na podstawie vCore modelu, trybu failover grupy będą obowiązywać z tymi samymi ustawieniami zasad. 

#### <a name="creation-of-a-geo-replication-secondary"></a>Tworzenie pomocniczej — replikacja geograficzna

Można utworzyć tylko przy użyciu tej samej warstwie usługi jako podstawowej dodatkowej geograficzna. W przypadku bazy danych o szybkości generowania rekordów dziennika wysokiej stanowczo zalecane utworzony pomocniczej z tym samym poziomie wydajności jako podstawowy. W przypadku tworzenia dodatkowej geograficzna w puli elastycznej dla jednej podstawowej bazy danych, zdecydowanie zaleca się czy puli ma `maxVCore` ustawienie odpowiada poziom wydajności podstawowej bazy danych. W przypadku tworzenia dodatkowej geograficzna w puli elastycznej dla podstawowego w innej puli elastycznej, zdecydowanie zaleca się pule mają taki sam `maxVCore` ustawienia

#### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Konwertowanie na podstawie jednostek dtu w warstwie bazy danych do bazy danych na podstawie vCore przy użyciu kopii bazy danych.

Wszystkie bazy danych z poziomu wydajności na podstawie jednostek dtu w warstwie można skopiować do bazy danych o wydajności na podstawie vCore poziomu bez ograniczeń lub specjalne sekwencjonowania tak długo, jak poziom wydajności docelowy obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Jest to spowodowane kopii bazy danych tworzy migawkę danych na godzinę rozpoczęcia operacji kopiowania i nie wykonuje synchronizację danych między serwerem źródłowym a docelowym. 

## <a name="next-steps"></a>Kolejne kroki

- Szczegółowe informacje o określonych poziomów wydajności i dostępnych wyborów rozmiar magazynu, zobacz [zasobów jednostek dtu w warstwie bazy danych SQL na podstawie limitów](sql-database-dtu-resource-limits.md) i [limity zasobów na podstawie vCore bazy danych SQL](sql-database-vcore-resource-limits.md).
- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Dowiedz się więcej o [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md)
