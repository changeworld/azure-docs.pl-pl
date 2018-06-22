---
title: Usługa Azure SQL Database - vCore | Dokumentacja firmy Microsoft
description: Na podstawie vCore model kupna (wersja zapoznawcza) umożliwia niezależnie skalowania zasobów obliczeniowych i magazynu, zgodne lokalnymi wydajności i zoptymalizowania cen.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: bfa32796b40033a13d1ced9f8431bd19492e6498
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309583"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Wybieranie warstwy usług vCore, obliczeniowych, pamięci, magazynu i zasobów we/wy

Warstwy usług są zróżnicowane przez zakres poziomów wydajności, projekt wysokiej dostępności, izolacji błędów, typów pamięci masowej i zakres we/wy. Klienta należy skonfigurować osobno wymagane okres przechowywania i przechowywania kopii zapasowych. Z modelem vCore pojedyncze bazy danych oraz pul elastycznych kwalifikują się do się do 30 procent oszczędności za pomocą [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Poniższa tabela pomoże Ci poznać różnice między tymi dwoma warstwami:

||**Ogólnego przeznaczenia**|**Biznesowe krytyczne**|
|---|---|---|
|Najlepsze dla|Większości obciążeń biznesowych. Oferty budżecie obiektowe zrównoważony i skalowalnych zasobów obliczeniowych i magazynu opcje.|Aplikacje biznesowe z wysokimi wymaganiami w zakresie operacji wejścia/wyjścia. Oferuje najwyższą odporność na awarie, korzystając z kilku izolowanych replik.|
|Wystąpienia obliczeniowe|vCore 1 do 80, generowanie 4 i 5 generacji |vCore 1 do 80, generowanie 4 i 5 generacji|
|Memory (Pamięć)|7 GB na podstawowe |7 GB na podstawowe |
|Magazyn|Magazyn zdalny Premium, 5 GB – 4 TB|Lokalny magazyn dysków SSD, 5 GB – 4 TB|
|Wydajność We/Wy (przybliżony)|500 IOPS na vCore z 7000 maksymalna liczba IOPS|5000 IOPS na podstawowe z 200000 maksymalna liczba IOPS|
|Dostępność|1 repliki, bez skalowania odczytu|repliki 3, 1 [skali odczytu](sql-database-read-scale-out.md), strefy HA geograficznie nadmiarowy|
|Tworzenie kopii zapasowych|RA-GRS, 7-35 dni (domyślnie co 7 dni)|RA-GRS, 7-35 dni (domyślnie co 7 dni) *|
|W pamięci|ND|Obsługiwane|
|||

\* Podczas udostępniania wersji zapoznawczej okres przechowywania kopii zapasowych nie konfiguruje się i zostanie rozwiązany do 7 dni.

> [!IMPORTANT]
> Jeśli potrzebujesz mniejszej niż jeden vCore pojemności obliczeń, użyj model kupna jednostek dtu w warstwie.

Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania. 

## <a name="storage-considerations"></a>Zagadnienia dotyczące magazynu

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

## <a name="backups-and-storage"></a>Kopie zapasowe i magazynu

Magazyn kopii zapasowych bazy danych jest przydzielony do obsługi punktu w czasie przywracania (PITR) i długi okres przechowywania STYLEM możliwościach bazy danych SQL. Ta pamięć masowa jest przydzielony osobno dla każdej bazy danych i rozliczane jako dwa osobne na bazę danych opłat. 

- **PITR**: jedna baza danych, kopie zapasowe są kopiowane do magazynu RA-GRS są automatycznie. Rozmiar magazynu zwiększa dynamicznie w miarę tworzenia nowych kopii zapasowych.  Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Użycia magazynu zależy szybkość zmian w bazie danych i okresu przechowywania. Można skonfigurować okres przechowywania osobne dla każdej bazy danych od 7 do 35 dni. Kwota minimalna magazynu równy 1 x rozmiar danych jest dostarczany bez dodatkowych opłat. Dla baz danych ta wartość jest wystarczająca do przechowywania kopii zapasowych 7 dni.
- **Od lewej do prawej**: baza danych SQL udostępnia opcję konfigurowania długoterminowego przechowywania tworzenia pełnych kopii zapasowych maksymalnie 10 lat. Jeśli od lewej do prawej jest włączona, kopie zapasowe te są przechowywane w magazynie RA-GRS automatycznie, ale można kontrolować, jak często kopie zapasowe są kopiowane. Aby spełnić wymagania zgodności różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczne i roczne. Ta konfiguracja określi, ile miejsca do magazynowania, które będą używane do tworzenia kopii zapasowych od lewej do prawej. Kalkulator cen od lewej do prawej służy do szacowania kosztów magazynowania od lewej do prawej. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Na podstawie vCore zakupów modelu (wersja zapoznawcza), należy wymienić istniejących licencji dla rabaty na korzystanie z bazy danych SQL [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Tego świadczenia usługi Azure umożliwia, przy użyciu lokalnej licencji programu SQL Server można zapisać do 30% w bazie danych SQL Azure przy użyciu lokalnej licencji programu SQL Server z Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migracja pojedynczej bazy danych — replikacja geograficzna łącza

Migrowania jednostek dtu w warstwie na podstawie modelu na podstawie vCore model jest podobny do uaktualnienia lub zmiany na starszą wersję — replikacja geograficzna relacje między bazami danych standardowa i Premium. Nie wymaga się, że przerywa — replikacja geograficzna, ale użytkownik musi być zgodna z regułami sekwencjonowania. Podczas uaktualniania, należy najpierw uaktualnić dodatkowej bazy danych, a następnie Uaktualnij serwera podstawowego. Podczas zmiany na starszą wersję, odwrócić kolejność: należy najpierw obniżyć podstawowej bazy danych i starszą wersję pomocniczą. 

Używając — replikacja geograficzna między dwoma pule elastyczne, zalecane jest, możesz określić jedną pulę jako podstawowy, a drugi — lokacji dodatkowej. W takim przypadku Migrowanie pule elastyczne należy używać te same wskazówki.  Jednak jest technicznie możliwe, że puli elastycznej zawiera podstawowe i pomocnicze bazy danych jest. W takim przypadku poprawnie migracji należy należy traktować puli z większe użycie jako "primary" i odpowiednio postępuj zgodnie z regułami sekwencjonowania.  

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

## <a name="migration-of-failover-groups"></a>Migracja grup trybu failover 

Migracja grup trybu failover z wieloma bazami danych wymaga migracji podstawowych i pomocniczych baz danych. Podczas tego procesu mają zastosowanie tego samego zagadnienia i reguły sekwencjonowania. Po baz danych są konwertowane na podstawie vCore modelu, trybu failover grupy będą obowiązywać z tymi samymi ustawieniami zasad. 

## <a name="creation-of-a-geo-replication-secondary"></a>Tworzenie pomocniczej — replikacja geograficzna

Można utworzyć tylko przy użyciu tej samej warstwie usługi jako podstawowej dodatkowej geograficzna. Baza danych o szybkości generowania rekordów dziennika wysoka zaleca się czy pomocniczy został utworzony z tym samym poziomie wydajności jako podstawowy. W przypadku tworzenia dodatkowej geograficzna w puli elastycznej dla jednej podstawowej bazy danych, zaleca się czy puli ma `maxVCore` ustawienie odpowiada poziom wydajności podstawowej bazy danych. W przypadku tworzenia dodatkowej geograficzna w puli elastycznej dla podstawowego w innej puli elastycznej, zalecane jest pule mają taki sam `maxVCore` ustawienia

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Konwertowanie na podstawie jednostek dtu w warstwie bazy danych do bazy danych na podstawie vCore przy użyciu kopii bazy danych.

Wszystkie bazy danych z poziomu wydajności na podstawie jednostek dtu w warstwie można skopiować do bazy danych o wydajności na podstawie vCore poziomu bez ograniczeń lub specjalne sekwencjonowania tak długo, jak poziom wydajności docelowy obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Jest to spowodowane kopii bazy danych tworzy migawkę danych na godzinę rozpoczęcia operacji kopiowania i nie wykonuje synchronizację danych między serwerem źródłowym a docelowym. 

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i opcje rozmiaru magazynu dostępne dla pojedynczej bazy danych, zobacz [limity zasobów na podstawie vCore bazy danych SQL dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- Szczegółowe informacje dotyczące określonych poziomów wydajności i magazynowania zawiera rozmiar dostępnych dla pul elastycznych [limity zasobów na podstawie vCore bazy danych SQL dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).