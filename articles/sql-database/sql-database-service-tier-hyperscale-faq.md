---
title: Azure SQL Database często zadawane pytania dotyczące skalowania | Microsoft Docs
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure SQL Database w warstwie usług skalowania — zwykle nazywanej bazą danych w skali.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 8c35877c7de2fa89a8fe7a94c11787814183df9e
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162251"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Często zadawane pytania dotyczące baz danych usługi Azure SQL Database

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące klientów rozważających bazę danych w Azure SQL Databaseej warstwie usług, która jest często nazywana bazą danych w ramach skalowania. W tym artykule opisano scenariusze, które są obsługiwane przez funkcję preskalowania, a usługi międzyusługowe są zgodne z ogólnym skalowaniem SQL Database.

- Często zadawane pytania są przeznaczone dla czytelników, którzy mają zwięzłe zrozumienie warstwy usługi w ramach skalowania i poszukują określonych pytań i odpowiedzi na nie.
- Te często zadawane pytania nie są przeznaczone do Guidebook lub odpowiedzi na pytania dotyczące korzystania z bazy danych SQL Database. W tym celu zalecamy zaodwoływanie się do dokumentacji dotyczącej [skalowania Azure SQL Database](sql-database-service-tier-hyperscale.md) .

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-a-hyperscale-database"></a>Co to jest baza danych ze skalą

Baza danych wieloskali to baza danych Azure SQL Database w warstwie usług w ramach skalowania, która jest obsługiwana przez technologię magazynu skalowalnego w poziomie. Baza danych wieloskalowania obsługuje do 100 TB danych i zapewnia wysoką przepływność i wydajność, a także szybkie skalowanie w celu dostosowania do wymagań dotyczących obciążenia. Skalowanie jest przezroczyste dla aplikacji — łączności, przetwarzania zapytań i tak dalej, jak w przypadku każdej innej bazy danych SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jakie typy zasobów i modele zakupów obsługują skalowanie

Warstwa usługi do skalowania jest dostępna tylko dla pojedynczych baz danych korzystających z modelu zakupu opartego na rdzeń wirtualny w Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Jak warstwa usługi dla skalowania różni się od Ogólnego przeznaczenia i Krytyczne dla działania firmy warstw usług

Warstwy usług oparte na rdzeń wirtualny są szczególnie zróżnicowane w zależności od dostępności, typu magazynu i liczby operacji we/wy na sekundę.

- Warstwa usługi Ogólnego przeznaczenia jest odpowiednia dla większości obciążeń firmowych, oferując zrównoważony zestaw opcji obliczeniowych i magazynu, w przypadku których opóźnienie operacji we/wy lub czas pracy awaryjnej nie są priorytetem.
- Warstwa usługi do skalowania jest zoptymalizowana pod kątem bardzo dużych obciążeń związanych z bazami danych.
- Warstwa usługi Krytyczne dla działania firmy jest odpowiednia dla obciążeń firmowych, w których opóźnienie operacji we/wy jest priorytetem.

| | Typ zasobu | Ogólne zastosowanie |  Hiperskala | Krytyczne dla działania firmy |
|:---:|:---:|:---:|:---:|:---:|
| **Najlepsze dla** |Wszyscy|  Większość obciążeń firmowych. Oferuje zorientowane na budżety Opcje obliczeniowe i magazynowe. | Aplikacje danych o dużych wymaganiach dotyczących pojemności danych oraz możliwość płynnego skalowania magazynu i skalowania w poziomie. | Aplikacje OLTP o dużej szybkości transakcji i najniższym opóźnieniu we/wy. Oferuje największą odporność na błędy przy użyciu kilku izolowanych replik.|
|  **Typ zasobu** ||Pojedyncza baza danych/Pula elastyczna/wystąpienie zarządzane | Pojedyncza baza danych | Pojedyncza baza danych/Pula elastyczna/wystąpienie zarządzane |
| **Rozmiar obliczeń**|Pojedyncza baza danych/Pula elastyczna * | od 1 do 80 rdzeni wirtualnych | od 1 do 80 rdzeni wirtualnych * | od 1 do 80 rdzeni wirtualnych |
| |Wystąpienie zarządzane | 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | ND | 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| **Typ magazynu** | Wszyscy |Magazyn zdalny w warstwie Premium (na wystąpienie) | Niepołączony magazyn z lokalną pamięcią podręczną dysków SSD (na wystąpienie) | Lokalny magazyn SSD o wysokiej szybkości (na wystąpienie) |
| **Rozmiar magazynu** | Pojedyncza baza danych/Pula elastyczna | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | ND | 32 GB – 4 TB |
| **Przepływność we/wy** | Pojedyncza baza danych * * | 500 operacji we/wy na sekundę z 7000 maksymalną liczbą IOPS | Skalowanie jest architekturą wielowarstwową z buforowaniem na wielu poziomach. Efektywne operacje we/wy będą zależeć od obciążenia. | 5000 operacji we/wy z maksymalną liczbą IOPS 200 000|
| | Wystąpienie zarządzane | Zależy od rozmiaru pliku | ND | Wystąpienie zarządzane: Zależy od rozmiaru pliku|
|**Dostępność**|Wszyscy|1 replika, brak skali do odczytu, brak lokalnej pamięci podręcznej | Wiele replik, do 4 częściowej lokalnej pamięci podręcznej | 3 repliki, 1 Skala odczytu, strefa nadmiarowa HA, pełna lokalna pamięć podręczna |
|**Kopii zapasowych**|Wszyscy|RA-GRS, 7-35 dni (domyślnie 7 dni)| RA-GRS, 7 dni, stałe odzyskiwanie do czasu w czasie (kopie) | RA-GRS, 7-35 dni (domyślnie 7 dni) |

\*Pule elastyczne nie są obsługiwane w warstwie usługi w ramach skalowania

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kto powinien korzystać z warstwy usługi do skalowania

Warstwa usługi do skalowania jest przeznaczona głównie dla klientów, którzy mają duże SQL Server lokalne bazy danych i chcą przeprowadzić modernizację swoich aplikacji, przechodząc do chmury lub dla klientów, którzy już używają Azure SQL Database i chcą znacząco rozwijać potencjalny wzrost rozmiaru bazy danych. Skalowanie jest również przeznaczone dla klientów, którzy poszukują zarówno wysokiej wydajności, jak i wysokiej skalowalności. Dzięki funkcji skalowania uzyskasz następujące korzyści:

- Obsługa nawet 100 TB rozmiaru bazy danych
- Szybkie kopie zapasowe bazy danych niezależnie od rozmiaru bazy danych (kopie zapasowe są oparte na migawkach plików)
- Szybka baza danych jest przywracana niezależnie od rozmiaru bazy danych (przywraca z migawek plików)
- Wyższe przepływność dziennika powoduje szybkie zatwierdzanie transakcji, niezależnie od rozmiaru bazy danych
- Odczytaj skalę do co najmniej jednego węzła tylko do odczytu w celu odciążenia obciążenia odczytu i rezerwy na gorąco.
- Szybkie skalowanie w górę obliczeń, w stałym czasie, do bardziej wydajnego, aby zapewnić duże obciążenie, a następnie skalować w dół w stałym czasie. Jest to podobne do skalowania w górę i w dół między P6 do P11, na przykład, ale znacznie szybciej, ponieważ nie jest to rozmiar operacji na danych.

### <a name="what-regions-currently-support-hyperscale"></a>Które regiony obsługują teraz skalowanie

Warstwa wieloskalowania Azure SQL Database jest obecnie dostępna w regionach wymienionych w sekcji [Azure SQL Database preskalowanie](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Czy można utworzyć wiele baz danych na jednym serwerze logicznym

Tak. Aby uzyskać więcej informacji i limitów dotyczących liczby baz danych w skali na serwerze logicznym, zobacz [SQL Database limitów zasobów dla jednej i puli baz danych na serwerze logicznym](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Jakie są charakterystyki wydajności bazy danych w ramach skalowania

Architektura skalowania SQL Database zapewnia wysoką wydajność i przepływność, a jednocześnie obsługuje duże rozmiary baz danych. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Co to jest skalowalność bazy danych w ramach skalowania

Funkcja wieloskalowania SQL Database zapewnia szybką skalowalność na podstawie zapotrzebowania na obciążenie.

- **Skalowanie w górę/w dół**

  Dzięki funkcji wieloskalowania można skalować podstawowy rozmiar obliczeń w ramach zasobów, takich jak procesor CPU, pamięć, a następnie skalowanie w dół w stałym czasie. Ponieważ magazyn jest współużytkowany, skalowanie w górę i skalowanie w dół nie jest rozmiarem operacji na danych.  
- **Skalowanie w górę/w dół**

  Dzięki funkcji skalowania można również udostępniać jeden lub więcej dodatkowych węzłów obliczeniowych, których można użyć do obsługi żądań odczytu. Oznacza to, że można użyć tych dodatkowych węzłów obliczeniowych jako węzłów tylko do odczytu w celu odciążenia obciążenia odczytu od podstawowego obliczenia. Oprócz tylko do odczytu węzły te również pełnią rolę "gorąca" w przypadku przejścia w tryb failover z poziomu podstawowego.

  Inicjowanie obsługi każdego z tych dodatkowych węzłów obliczeniowych może odbywać się w stałym czasie i jest operacją online. Można połączyć się z tymi dodatkowymi węzłami obliczeniowymi tylko do odczytu `ApplicationIntent` przez ustawienie argumentu parametrów połączenia na `readonly`. Wszystkie połączenia oznaczone za `readonly` pomocą są automatycznie kierowane do jednego z dodatkowych węzłów obliczeniowych tylko do odczytu.

## <a name="deep-dive-questions"></a>Głębokie pytania szczegółowe

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Czy mogę mieszać pojedyncze bazy danych na jednym serwerze logicznym

Tak, możesz.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Czy funkcja skalowania wymaga zmiany modelu programowania aplikacji

Nie, model programowania aplikacji pozostaje w takiej postaci. Parametry połączenia są używane w zwykły sposób, a inne standardowe tryby do współpracy z usługą Azure SQL Database.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Jakie poziomy izolacji transakcji mają być domyślne w SQL Databaseej bazie danych

W węźle podstawowym poziom izolacji transakcji to RCSI (izolacja zatwierdzoną migawką). Na poziomie izolacji węzła pomocniczego skali odczytu jest to migawka.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Czy można włączyć SQL Server licencję lokalną lub IaaS do SQL Database

Tak, [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jest dostępny do skalowania. Każdy SQL Server Standard rdzeń można mapować na 1 rdzeni wirtualnych w skali. Każdy SQL Server Enterprise rdzeń można zamapować na 4 rdzeni wirtualnych. Nie potrzebujesz licencji SQL dla replik pomocniczych. Korzyść użycia hybrydowego platformy Azure cena zostanie automatycznie zastosowana do replik odczytu (pomocniczych).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Jakiego rodzaju obciążenia są SQL Database w ramach skalowania przeznaczonego dla

Funkcja wieloskalowania SQL Database obsługuje wszystkie obciążenia SQL Server, ale jest przede wszystkim zoptymalizowane pod kątem OLTP. Można również wprowadzać obciążenia hybrydowe (HTAP) i analityczne (składnicy danych).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Jak mogę wybrać między Azure SQL Data Warehouse i skalowaniem SQL Database

Jeśli obecnie uruchamiasz interakcyjne zapytania analityczne przy użyciu SQL Server jako magazynu danych, SQL Database to świetna opcja, ponieważ można hostować stosunkowo małe magazyny danych (np. kilka TB do dziesiątkach TB) przy niższych kosztach i przeprowadzić migrację danych w ramach Arehouse obciążenie w celu SQL Database skalowania bez zmian w kodzie T-SQL.

W przypadku korzystania z analizy danych na dużą skalę z złożonymi zapytaniami i używaniem usług Parallel Data Warehouse (PDW), Teradata lub innych magazynów danych (MPP), SQL Data Warehouse mogą być najlepszym wyborem.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database na pytania dotyczące obliczeń w ramach skalowania

### <a name="can-i-pause-my-compute-at-any-time"></a>Czy mogę wstrzymywać obliczenia w dowolnym momencie

W tej chwili można jednak skalować obliczenia i liczbę replik w dół, aby zmniejszyć koszty w czasie poza szczytem.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Czy można zarezerwować obliczenia z dodatkową ilością pamięci RAM na potrzeby obciążeń intensywnie korzystających z pamięci

Nie. Aby uzyskać więcej pamięci RAM, należy przeprowadzić uaktualnienie do wyższego rozmiaru. Aby uzyskać więcej informacji, zobacz temat [skalowanie magazynu i rozmiarów obliczeniowych](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Czy mogę zainicjować obsługę wielu węzłów obliczeniowych o różnych rozmiarach

Nie.

### <a name="how-many-read-scale-replicas-are-supported"></a>Ile replik skali odczytu jest obsługiwanych

Bazy danych ze skalowaniem są tworzone domyślnie z jedną repliką w trybie odczytu (łącznie). Liczbę replik tylko do odczytu można skalować między 0 a 4 przy użyciu [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) lub [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Aby zapewnić wysoką dostępność, należy zastanowić się nad dodatkowymi węzłami obliczeniowymi

W bazach danych w ramach skalowania odporność jest zapewniana na poziomie magazynu. Wymagana jest tylko jedna replika w celu zapewnienia odporności. Gdy replika obliczeniowa nie działa, Nowa replika jest tworzona automatycznie bez utraty danych.

Jeśli jednak istnieje tylko jedna replika, może upłynąć trochę czasu, aby utworzyć lokalną pamięć podręczną w nowej replice po przejściu w tryb failover. W fazie odbudowywania pamięci podręcznej baza danych pobiera dane bezpośrednio z serwerów stronicowania, co spowodowało spadek wydajności operacji we/wy na sekundę.

W przypadku aplikacji o kluczowym znaczeniu, które wymagają wysokiej dostępności, należy udostępnić co najmniej 2 węzły obliczeniowe, w tym podstawowy węzeł obliczeniowy (domyślnie). W przypadku przejścia w tryb failover w przypadku pracy awaryjnej jest dostępny stan aktywny.

## <a name="data-size-and-storage-questions"></a>Rozmiar danych i pytania dotyczące magazynu

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Jaki jest maksymalny rozmiar bazy danych obsługiwany przez SQL Database

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaki jest rozmiar dziennika transakcji ze skalą

Dziennik transakcji ze skalą jest praktycznie nieskończony. Nie trzeba martwić się o uruchamianie miejsca w dzienniku w systemie, który ma wysoką przepływność dzienników. Jednak szybkość generowania dzienników może być ograniczona dla ciągłego agresywnych obciążeń. Szczytowa szybkość generowania dzienników wynosi około 100 MB/s.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Czy moja Tymczasowa baza danych działa w miarę zwiększania rozmiaru

`tempdb` Baza danych znajduje się w lokalnym magazynie dysków SSD i jest konfigurowana na podstawie wymaganego rozmiaru obliczeń. Twoje `tempdb` działanie jest zoptymalizowane i ustanawiane w celu zapewnienia maksymalnej wydajności. Nie `tempdb` można skonfigurować tego rozmiaru i jest on zarządzany przez system podrzędny magazynu.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Czy rozmiar bazy danych jest automatycznie zwiększany, czy muszę zarządzać rozmiarem plików danych

Rozmiar bazy danych jest automatycznie zwiększany podczas wstawiania/pozyskiwania większej ilości danych.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Jaki jest najmniejszy rozmiar bazy danych, który SQL Database funkcja preskalowania obsługuje lub zaczyna się od

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>W jaki sposób zwiększa się rozmiar bazy danych

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Jest magazynem w SQL Database funkcji skalowania lokalnego lub zdalnego

W obszarze skalowanie pliki danych są przechowywane w usłudze Azure Storage w warstwie Standardowa. Dane są intensywnie buforowane na lokalnym magazynie dysków SSD na maszynach blisko węzłów obliczeniowych. Ponadto węzły obliczeniowe mają pamięć podręczną na lokalnym dysku SSD i w pamięci (w puli buforów itd.), aby zmniejszyć częstotliwość pobierania danych z węzłów zdalnych.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Czy mogę zarządzać plikami lub grupami plików lub ich definiować przy użyciu funkcji skalowania

Nie
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Czy mogę zainicjować twarde zakończenie wzrostu ilości danych dla mojej bazy danych

Nie

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Jak pliki danych są ustanawiane przy użyciu SQL Database

Pliki danych są kontrolowane przez serwery stronicowania. W miarę zwiększania rozmiaru danych dodawane są pliki danych i skojarzone węzły serwera stronicowania.

### <a name="is-database-shrink-supported"></a>Czy zmniejszenie rozmiaru bazy danych jest obsługiwane

Nie

### <a name="is-database-compression-supported"></a>Czy kompresja bazy danych jest obsługiwana

Tak

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Jeśli mam ogromną tabelę, dane tabeli są rozłożone na wiele plików danych

Tak. Strony danych skojarzone z daną tabelą mogą kończyć się wieloma plikami danych, które są częścią tej samej grupy plików. SQL Server używa [strategii wypełniania proporcjonalnego](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) do dystrybuowania danych za pośrednictwem plików danych.

## <a name="data-migration-questions"></a>Pytania dotyczące migracji danych

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Czy mogę przenieść istniejące bazy danych Azure SQL Database do warstwy usługi w ramach skalowania

Tak. Istniejące bazy danych Azure SQL można przenieść do skalowania. Jest to jednokierunkowa migracja. Nie można przenieść baz danych ze skalowania do innej warstwy usług. Zalecamy wykonanie kopii produkcyjnych baz danych i migrację do funkcji wieloskalowania w celu sprawdzenia koncepcji (POCs).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Czy mogę przenieść bazy danych mojego skalowania do innych wersji

Nie. W tej chwili nie można przenieść bazy danych w ramach skalowania do innej warstwy usług.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Czy po migracji do warstwy usługi w ramach skalowania

Tak. Niektóre funkcje Azure SQL Database nie są jeszcze obsługiwane w ramach skalowania, w tym, ale nie ograniczone długoterminowe kopie zapasowe. Po przeprowadzeniu migracji baz danych do skalowania te funkcje przestaną działać.  Oczekujemy, że te ograniczenia będą tymczasowe.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Czy mogę przenieść lokalną bazę danych SQL Server lub moją SQL Serverą bazę danych maszyny wirtualnej do skalowania

Tak. Możesz użyć wszystkich istniejących technologii migracji, aby przeprowadzić migrację do skalowania, w tym BACPAC, replikację transakcyjną, ładowanie danych logicznych. Zobacz również [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Jaki jest mój przestój podczas migracji ze środowiska lokalnego lub maszyny wirtualnej do skalowania i jak można go zminimalizować

Przestój jest taki sam jak czas przestoju podczas migrowania baz danych do pojedynczej bazy danych w programie Azure SQL Database. Za pomocą [replikacji](replication-to-sql-database.md#data-migration-scenario
) transakcyjnej można zminimalizować czas przestoju dla baz danych o rozmiarze do kilku TB. W przypadku bardzo dużej bazy danych (10 + TB) można rozważyć Migrowanie danych przy użyciu funkcji ADF, Spark lub innych technologii przenoszenia danych.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Ile czasu zajmie potrzeba przełączenia wartości X do SQL Database

Funkcja skalowania może zużywać 100 MB/s nowych/zmienionych danych.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Czy mogę odczytywać dane z usługi BLOB Storage i wykonywać szybkie ładowanie (takie jak baza danych i SQL Data Warehouse)

Możesz odczytywać dane z usługi Azure Storage i ładować obciążenia danych do bazy danych w formie wieloskali (podobnie jak w przypadku zwykłej pojedynczej bazy danych). Baza Base nie jest obecnie obsługiwana w Azure SQL Database. Bazę danych można wykonać przy użyciu [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) lub uruchamiania zadania Spark w [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) za pomocą [łącznika Spark dla SQL](sql-database-spark-connector.md). Łącznik platformy Spark do bazy danych SQL obsługuje wstawianie zbiorcze.

Proste odzyskiwanie lub model rejestrowania zbiorczego nie jest obsługiwany w ramach skalowania. Model odzyskiwania pełnego jest wymagany w celu zapewnienia wysokiej dostępności. Jednak funkcja przedskalowania zapewnia lepszą szybkość pozyskiwania danych w porównaniu do pojedynczej bazy danych Azure SQL Database ze względu na nową architekturę dzienników.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Czy funkcja skalowania SQL Database umożliwia obsługę wielu węzłów na potrzeby pozyskiwania dużych ilości danych

Nie. Skalowanie SQL Database jest architekturą SMP i nie jest to asymetryczne przetwarzanie wieloskładnikowe ani architektura wielu wzorców. Można utworzyć tylko wiele replik w celu skalowania obciążeń tylko do odczytu.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Co to jest najstarsza wersja SQL Server, SQL Database migracji z obsługą skalowania z

SQL Server 2005. Aby uzyskać więcej informacji, zobacz [Migrowanie do pojedynczej bazy danych lub bazy danych w puli](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Aby poznać problemy ze zgodnością, zobacz [Rozwiązywanie problemów ze zgodnością migracji bazy danych](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>SQL Database migrację pomocy technicznej w ramach skalowania z innych źródeł danych, takich jak Aurora, MySQL, Oracle, DB2 i inne platformy baz danych

Tak. Pochodzące z różnych źródeł danych innych niż SQL Server wymaga migracji logicznej. Do migracji logicznej można użyć [Azure Database Migration Service](../dms/dms-overview.md) .

## <a name="business-continuity-and-disaster-recovery-questions"></a>Ciągłość działania i odzyskiwanie po awarii — pytania

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Jakie umowy SLA są udostępniane dla bazy danych w ramach aplikacji do skalowania

Dzięki domyślnemu podstawowemu i 1 pomocniczemu zapasowi jest dostępna umowa SLA na 99,95% czasu.  W przypadku większej liczby replik umowa SLA jest równa 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Są kopiami zapasowymi bazy danych zarządzanymi dla mnie przez usługę Azure SQL Database

Tak

### <a name="how-often-are-the-database-backups-taken"></a>Jak często wykonywane są kopie zapasowe bazy danych

Nie ma tradycyjnych pełnych, różnicowych i dzienników kopii zapasowych dla SQL Database baz danych. Zamiast tego istnieją regularne migawki plików danych, które zostały wygenerowane, są po prostu zachowywane jako przeznaczone dla skonfigurowanego lub dostępnego okresu przechowywania.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Czy SQL Database w czasie przywracania punktu obsługi funkcji preskalowania

Tak

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Jaki jest cel punktu odzyskiwania (RPO)/Recovery cel (RTO) z kopią zapasową/przywracaniem w SQL Databasem skalowaniu

Cel punktu odzyskiwania to 0 min. Cel RTO jest krótszy niż 10 minut, niezależnie od rozmiaru bazy danych. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Czy kopie zapasowe dużych baz danych wpływają na wydajność obliczeń na serwerze podstawowym

Nie. Kopie zapasowe są zarządzane przez podsystem magazynowania i wykorzystują migawki plików. Nie mają one wpływu na obciążenie użytkownika na serwerze podstawowym.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Czy można wykonać przywracanie geograficzne za pomocą bazy danych SQL Database

Tak.  Przywracanie geograficzne jest w pełni obsługiwane.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Czy można skonfigurować replikację geograficzną za pomocą bazy danych SQL Database

Nie w tej chwili.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Czy moje pomocnicze węzły obliczeniowe uzyskują replikację geograficzną z SQL Databasem skalowaniu

Nie w tej chwili.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Czy można utworzyć kopię zapasową bazy danych SQL Database i przywrócić ją do serwera lokalnego lub SQL Server na maszynie wirtualnej

Nie. Format magazynu dla baz danych w ramach skalowania jest inny niż tradycyjny SQL Server i nie kontrolujesz kopii zapasowych ani nie masz do nich dostępu. Aby wypróbować dane z SQL Databasej bazy danych w ramach skalowania, Użyj usługi Export lub skryptu i narzędzia BCP.

## <a name="cross-feature-questions"></a>Pytania dotyczące funkcji krzyżowych

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Czy po migracji do warstwy usługi w ramach skalowania

Tak. Niektóre funkcje Azure SQL Database nie są obsługiwane w ramach skalowania, w tym między innymi długoterminowe kopie zapasowe przechowywania danych. Po przeprowadzeniu migracji baz danych do skalowania te funkcje przestaną działać.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Czy baza będzie bazować na SQL Database

Nie. Baza Base nie jest obsługiwana w Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Czy obliczenia są obsługiwane dla języków R i Python

Nie. Język R i Python nie są obsługiwane w Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Czy węzły obliczeniowe są kontenerami

Nie. Twoja baza danych znajduje się na maszynie wirtualnej obliczeniowej, a nie w kontenerze.

## <a name="performance-questions"></a>Pytania dotyczące wydajności

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Jak dużo przepływności można wypchnąć na największe SQL Database skalowanie w górę

Zaobserwowano spójną 100 MB/s zmian danych (generowanie danych dziennika transakcji)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Ile operacji we/wy na największe skalowanie SQL Database

Liczba operacji we/wy i czas oczekiwania na sekundę różnią się w zależności od wzorców obciążenia.  Jeśli wymagane jest uzyskanie dostępu do danych w pamięci podręcznej obliczeniowej, będzie to te same wzorce we/wy co lokalny dysk SSD.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Czy na moją przepływność wpływają kopie zapasowe

Nie. Obliczenia są oddzielone od warstwy magazynu, aby uniknąć wpływu na obliczenia.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Czy moja przepływność jest narażona na dostęp w przypadku udostępniania dodatkowych węzłów obliczeniowych

Ponieważ magazyn jest współużytkowany i nie ma żadnej bezpośredniej replikacji fizycznej między podstawowym i pomocniczym węzłem obliczeniowym, technicznie nie wpłynie to na przepływność w węźle podstawowym. Można jednak ograniczyć ciągłe agresywne obciążenie, aby umożliwić wykonywanie dzienników na węzłach pomocniczych i serwerach stronicowania, aby uniknąć złej wydajności odczytu w węzłach pomocniczych.

## <a name="scalability-questions"></a>Pytania dotyczące skalowalności

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Jak długo trwa skalowanie w górę i w dół węzła obliczeniowego

Skalowanie obliczeniowe w górę lub w dół powinno trwać 5-10 minut niezależnie od rozmiaru danych.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Czy moja baza danych jest w trybie offline, gdy trwa skalowanie w górę/w dół

Nie. Skalowanie w górę i w dół będzie przełączane w tryb online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Czy należy oczekiwać, że połączenie jest porzucane, gdy operacje skalowania są w toku

Skalowanie w górę lub w dół powoduje, że istniejące połączenia są usuwane w przypadku przejścia w tryb failover do węzła obliczeniowego z rozmiarem docelowym. Dodanie replik odczytu nie powoduje porzucania połączeń.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Skalowanie w górę i w dół węzłów obliczeniowych automatyczne lub wyzwalane przez użytkownika końcowego

Użytkownik końcowy. Nie automatycznie.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Czy my `tempb` rośnie także, gdy obliczenia są skalowane

Tak. Tymczasowa baza danych zostanie przeskalowana automatycznie w miarę wzrostu obliczeń.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Czy można zainicjować obsługę wielu podstawowych węzłów obliczeniowych, takich jak system z wieloma wzorcami, gdzie wiele podstawowych głowic obliczeniowych może mieć wyższy poziom współbieżności

Nie. Tylko podstawowy węzeł obliczeniowy akceptuje żądania odczytu/zapisu. Dodatkowe węzły obliczeniowe akceptują tylko żądania tylko do odczytu.

## <a name="read-scale-questions"></a>Przeczytaj pytania dotyczące skalowania

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Ile pomocniczych węzłów obliczeniowych można udostępnić

Domyślnie tworzymy 2 repliki dla baz danych. Jeśli chcesz dostosować liczbę replik, możesz to zrobić za pomocą [Azure Portal](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Jak mogę połączyć się z tymi dodatkowymi węzłami obliczeniowymi

Można połączyć się z tymi dodatkowymi węzłami obliczeniowymi tylko do odczytu `ApplicationIntent` przez ustawienie argumentu parametrów połączenia na `readonly`. Wszystkie połączenia oznaczone za `readonly` pomocą są automatycznie kierowane do jednego z dodatkowych węzłów obliczeniowych tylko do odczytu.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-node-using-ssms--other-client-tools"></a>Jak mogę sprawdzić, czy pomyślnie nawiązano połączenie z dodatkowym węzłem obliczeniowym przy użyciu narzędzia SSMS/other Client Tools?

Poniższe zapytanie T-SQL można wykonać za pomocą narzędzia SSMS/other Client Tools: `SELECT DATABASEPROPERTYEX ( '<database_name>' , 'updateability' )`.
Wynikiem tego jest `READ_ONLY` to, że połączenie wskazuje węzeł pomocniczy tylko do odczytu lub `READ_WRITE` Jeśli połączenie wskazuje węzeł podstawowy.

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Czy można utworzyć dedykowany punkt końcowy dla repliki w skali odczytu

Nie. Można łączyć się tylko z repliką skali odczytu, `ApplicationIntent=ReadOnly`określając.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Czy system ma inteligentne Równoważenie obciążenia podczas odczytu obciążenia

Nie. Obciążenie tylko do odczytu jest przekierowywane do losowej repliki w skali odczytu.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Czy można skalować w górę/w dół pomocnicze węzły obliczeniowe niezależnie od podstawowych obliczeń

Nie. Pomocnicze węzły obliczeniowe są również używane na potrzeby wysokiej dostępności, dlatego muszą być takie same jak w przypadku trybu failover.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Czy mogę uzyskać różne rozmiary tymczasowej bazy danych dla moich podstawowych obliczeń i dodatkowych pomocniczych węzłów obliczeniowych

Nie. Konfiguracja `tempdb` jest zależna od rozmiaru obliczeń, ale pomocnicze węzły obliczeniowe mają taki sam rozmiar jak podstawowe obliczenia.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Czy mogę dodawać indeksy i widoki w dodatkowych węzłach obliczeniowych

Nie. Bazy danych w ramach skalowania mają magazyn udostępniony, co oznacza, że wszystkie węzły obliczeniowe zobaczą te same tabele, indeksy i widoki. Jeśli chcesz, aby dodatkowe indeksy były zoptymalizowane pod kątem odczytu na serwerze pomocniczym — należy najpierw dodać je na serwerze podstawowym.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Jak dużo opóźnić między podstawowym i pomocniczym węzłem obliczeniowym

Od momentu, gdy transakcja jest zatwierdzana na poziomie podstawowym, w zależności od szybkości generowania dziennika, może być chwilowo lub w niskiej wartości milisekund.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat warstwy usługi do skalowania, zobacz sekcję [skalowanie warstwy usług](sql-database-service-tier-hyperscale.md).
