---
title: Usługa Azure SQL Database — rdzeń wirtualny | Dokumentacja firmy Microsoft
description: Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: d18076486704d5f03acd2253650762c3bd24b0af
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091496"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Wybieranie warstwy usług (rdzeń wirtualny), obliczeniowych, pamięci, magazynu i zasoby we/wy

Warstwy usługi są zróżnicowane według szeroką gamę poziomów wydajności, projekt wysokiej dostępności, odporności izolacji, typów pamięci masowej i zakres operacji We/Wy. Klienta należy skonfigurować osobno wymagane okres przechowywania i przechowywania kopii zapasowych. Za pomocą modelu rdzenia wirtualnego pojedyncze bazy danych i pul elastycznych kwalifikują się do 30 procent oszczędności w przypadku [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Poniższa tabela pomoże Ci zrozumieć różnice między tymi dwoma warstwami:

||**Ogólnego przeznaczenia**|**Krytyczne dla działania**|
|---|---|---|
|Najlepsze dla|Większości obciążeń biznesowych. Oferty budżetu, aby poznać podstawy zrównoważonych oraz skalowalnych opcji obliczeniowych i magazynu.|Aplikacje biznesowe z wysokimi wymaganiami w zakresie operacji wejścia/wyjścia. Oferuje najwyższą odporność na awarie, korzystając z kilku izolowanych replik.|
|Wystąpienia obliczeniowe|1 do 80 (rdzeń wirtualny), 4. generacji i 5. generacji |1 do 80 (rdzeń wirtualny), 4. generacji i 5. generacji|
|Memory (Pamięć)|4. generacji: 7 GB na rdzeń<br>5. generacji: 5.5 GB na rdzeń | 4. generacji: 7 GB na rdzeń<br>5. generacji: 5.5 GB na rdzeń |
|Magazyn|Magazyn zdalny w warstwie Premium, 5 GB – 4 TB|Lokalny magazyn SSD, 5 GB – 4 TB|
|Przepustowość operacji We/Wy (w przybliżeniu)|500 operacji We/Wy na rdzeniach wirtualnych za pomocą 7000 maksymalna liczba IOPS|5000 operacji We/Wy na każdy rdzeń za pomocą 200000 maksymalna liczba IOPS|
|Dostępność|1 repliki, bez skalowania odczytu|3 repliki, 1 [skalę odczytywania](sql-database-read-scale-out.md), strefa nadmiarowe wysokiej dostępności|
|Tworzenie kopii zapasowych|RA-GRS, 7 – 35 dni (domyślnie co 7 dni)|RA-GRS, 7 – 35 dni (domyślnie co 7 dni)|
|W pamięci|ND|Obsługiwane|
|||

> [!IMPORTANT]
> Jeśli potrzebujesz mniejszej niż jeden rdzeń wirtualny mocy obliczeniowej, przy użyciu modelu zakupu opartego na jednostkach DTU.

Zobacz [— często zadawane pytania dla bazy danych SQL](sql-database-faq.md) odpowiedzi na często zadawane pytania. 

## <a name="storage-considerations"></a>Zagadnienia dotyczące magazynu

Rozważ następujące źródła:
- Przydzielenia pamięci, która jest używana przez pliki danych (MDF) i plików (LDF) dziennika.
- Każdy poziom wydajności obsługuje maksymalny rozmiar bazy danych, za pomocą domyślny maksymalny rozmiar 32 GB.
- Po skonfigurowaniu rozmiar wymaganej bazy danych (MDF o rozmiarze), 30% dodatkowego miejsca do magazynowania jest automatycznie dodawany do obsługi LDF
- Można wybrać dowolnego rozmiaru bazy danych między 10 GB i maksymalnej obsługiwanej
 - Dla magazynu w warstwie standardowa należy zwiększyć lub zmniejszyć rozmiar w przyrostach co 10 GB
 - Dla usługi Premium storage należy zwiększyć lub zmniejszyć rozmiar w partiach po 250 GB
- W przypadku warstwy usług ogólnego przeznaczenia `tempdb` używa dołączone dyski SSD i ten koszt przechowywania jest uwzględniona w cenie — rdzeń wirtualny.
- W warstwie krytyczne dla działalności `tempdb` udziałów dołączonych dysków SSD przy użyciu pliki MDF i LDF i kosztach magazynowania w bazie danych tempDB jest uwzględniona w cenie — rdzeń wirtualny.

> [!IMPORTANT]
> Opłaty są naliczane za całkowita ilość miejsca, które są przydzielone dla plików MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

## <a name="backups-and-storage"></a>Tworzenie kopii zapasowych i magazynu

Magazyn kopii zapasowych bazy danych jest przydzielany do obsługi punktu w czasie przywracania (Odzyskiwanie) i długi okres przechowywania (LTR) możliwościach usługi SQL Database. Ten magazyn jest przydzielany osobno dla każdej bazy danych i rozliczane jako dwie osobne bazy danych opłaty. 

- **Odzyskiwanie**: poszczególnych baz danych, kopie zapasowe są kopiowane do magazynu RA-GRS są wykonywane automatycznie. Rozmiar magazynu zwiększa dynamicznie w miarę tworzenia nowych kopii zapasowych.  Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Użycie magazynu zależy od szybkości zmian bazy danych i okres przechowywania. Można skonfigurować okres przechowywania osobne dla każdej bazy danych od 7 do 35 dni. Minimalna wielkość magazynu równy 1 x rozmiaru danych znajduje się bez dodatkowych opłat. Większość baz danych ta wartość jest wystarczający, aby przechowywać kopie zapasowe z 7 dni.
- **Od lewej do prawej**: usługa SQL Database oferuje opcję Konfigurowanie długoterminowego przechowywania pełnych kopii zapasowych dla maksymalnie 10 lat. Jeśli od lewej do prawej jest włączona, te kopie zapasowe są przechowywane w magazynach RA-GRS automatycznie, ale można kontrolować, jak często kopie zapasowe są kopiowane. Aby spełnić wymagania zgodności w różnych, możesz wybrać różnych okresów przechowywania dla kopii zapasowych co tydzień, miesięczny lub roczny. Ta konfiguracja określi, ile pamięci masowej, będzie używany do tworzenia kopii zapasowych LTR. Aby oszacować koszt magazynu od lewej do prawej, można użyć Kalkulator cen od lewej do prawej. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Oparty na rdzeniach wirtualnych model zakupu mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na temat korzystania z bazy danych SQL [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Ta korzyść platformy Azure pozwala na używanie licencji programu SQL Server w środowisku lokalnym można zapisać do 30% w usłudze Azure SQL Database przy użyciu lokalnych licencji programu SQL Server z pakietem Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migracja pojedynczej bazy danych za pomocą łącza replikacji geograficznej

Migrowanie do modelu opartego na jednostkach DTU do modelu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję relacje replikacji geograficznej między bazami danych w warstwach standardowa i Premium. Nie jest wymagane, czy zakończenie replikacji geograficznej, ale użytkownik musi być zgodna z zasadami sekwencjonowania. Podczas uaktualniania, należy najpierw uaktualnić pomocnicze bazy danych, a następnie Uaktualnij podstawowy. Przed obniżeniem, odwrócić kolejność: należy najpierw obniżyć podstawowej bazy danych, a następnie obniżenia poziomu pomocniczej. 

Korzystając z replikacją geograficzną między dwie pule elastyczne, zaleca się wyznaczyć jedną pulę jako podstawowy, a druga — jako pomocnicza. W takim przypadku Migrowanie pul elastycznych, należy użyć te same wskazówki.  Jednak jest technicznie jest to możliwe, że pula elastyczna zawiera podstawowych i pomocniczych baz danych. W tym przypadku prawidłowo migracji możesz należy traktować puli z lepszego wykorzystania jako "primary" i postępuj zgodnie z zasadami sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące scenariuszy migracji: 

|Bieżąca warstwa usługi|Docelowej warstwy usług|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa (Standard)|Zastosowania ogólne|Boczna|Można migrować w dowolnej kolejności, ale musisz upewnić się, ustalanie rozmiaru odpowiedniego — rdzeń wirtualny *|
|Premium|Krytyczne dla działania firmy|Boczna|Można migrować w dowolnej kolejności, ale musisz upewnić się, ustalanie rozmiaru odpowiedniego — rdzeń wirtualny *|
|Standardowa (Standard)|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
|Krytyczne dla działania firmy|Standardowa (Standard)|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Premium|Zastosowania ogólne|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Premium|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
|Krytyczne dla działania firmy|Zastosowania ogólne|Zmień na starszą wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
||||

\* Każdy 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny, a każdy 125 jednostek DTU w warstwie Premium co najmniej 1 rdzeń wirtualny

## <a name="migration-of-failover-groups"></a>Migracja grupy trybu failover 

Migracja grup trybu failover z wieloma bazami danych wymaga migracji poszczególnych baz danych podstawowych i pomocniczych. Podczas tego procesu rozważenia tych samych czynników i sekwencjonowania zasady są stosowane. Po przekonwertowaniu baz danych do modelu opartego na rdzeniach wirtualnych, grupy trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad. 

## <a name="creation-of-a-geo-replication-secondary"></a>Tworzenie pomocniczej replikacji geograficznej

Można utworzyć tylko przy użyciu tej samej warstwy usługi jako podstawowy pomocniczej geograficznej. Baza danych o szybkości generowania rekordów dziennika wysoka zalecane jest, czy pomocniczy został utworzony na tym samym poziomie wydajności jako podstawowy. Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla pojedynczej podstawowej bazy danych, zaleca się że pula ma `maxVCore` ustawienia jest zgodny z poziomem wydajności podstawowej bazy danych. Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla podstawowego w innej puli elastycznej, zalecane pule mają taką samą `maxVCore` ustawienia

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Przy użyciu kopii bazy danych do przekonwertowania bazy danych oparty na jednostkach DTU na bazę danych oparty na rdzeniach wirtualnych.

Można skopiować dowolną bazę danych z poziomem wydajności oparty na jednostkach DTU do bazy danych o wydajności oparty na rdzeniach wirtualnych poziomu bez ograniczeń lub specjalne, sekwencjonowanie tak długo, jak poziom wydajności miejsca docelowego obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Jest to spowodowane kopii bazy danych tworzy migawkę danych od godziny rozpoczęcia operacji kopiowania i nie wykonuje synchronizację danych między źródłowym a docelowym. 

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje o określonych poziomów wydajności i dostępności dla pojedynczej bazy danych opcje rozmiaru magazynu, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- Szczegółowe informacje dotyczące określonych poziomów wydajności i magazynowania dostępnych dla pul elastycznych opcji rozmiaru zawiera [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
