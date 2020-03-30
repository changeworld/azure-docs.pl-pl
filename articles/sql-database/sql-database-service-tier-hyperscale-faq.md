---
title: Często zadawane pytania dotyczące hiperskali bazy danych SQL usługi Azure
description: Odpowiedzi na często zadawane pytania, które klienci zadają na temat bazy danych SQL platformy Azure w warstwie usługi hiperskali — często nazywanej bazą danych w hiperskali.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268628"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Często zadawane pytania dotyczące hiperskali bazy danych SQL usługi Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dla klientów rozważających bazę danych w warstwie usługi Azure SQL Database Hyperscale, określany jako tylko hiperskala w pozostałej części tego często zadawanych pytań. W tym artykule opisano scenariusze, które obsługuje hiperskala i funkcje, które są zgodne z hiperskali.

- To często zadawane pytania jest przeznaczone dla czytelników, którzy mają krótką wiedzę na temat warstwy usług hyperscale i chcą uzyskać odpowiedzi na ich konkretne pytania i wątpliwości.
- To często zadawane pytania nie ma być przewodnikiem ani odpowiedzią na pytania dotyczące korzystania z bazy danych w hiperskali. Aby zapoznać się z wprowadzeniem do hiperskali, zaleca się zapoznanie się z dokumentacją [bazy danych Azure SQL Database hyperscale.](sql-database-service-tier-hyperscale.md)

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-a-hyperscale-database"></a>Co to jest baza danych z hiperskali

Baza danych na dużą skalę to baza danych SQL platformy Azure w warstwie usług hiperskali, która jest obsługiwana przez technologię magazynu skalowość w poziomie hiperskali. Baza danych w hiperskali obsługuje do 100 TB danych i zapewnia wysoką przepływność i wydajność, a także szybkie skalowanie w celu dostosowania do wymagań dotyczących obciążenia. Skalowanie jest przezroczyste dla aplikacji — łączność, przetwarzanie zapytań itp.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jakie typy zasobów i modele zakupu obsługują hiperskali

Warstwa usługi hiperskali jest dostępna tylko dla pojedynczych baz danych przy użyciu modelu zakupów opartego na językach wirtualnych w usłudze Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Czym różni się warstwa usług hiperskali od warstwy usług ogólnego przeznaczenia i usługi o krytycznym znaczeniu dla firmy

Warstwy usług oparte na wynikach wirtualnych są zróżnicowane na podstawie dostępności bazy danych i typu magazynu, wydajności i maksymalnego rozmiaru, zgodnie z opisem w poniższej tabeli.

| | Typ zasobu | Ogólnego przeznaczenia |  Hiperskala | Krytyczne dla biznesu |
|:---:|:---:|:---:|:---:|:---:|
| **Najlepsze dla** |Wszystkie|Oferuje opcje zrównoważonego obliczeń i pamięci masowej zorientowane na budżet.|Większość obciążeń biznesowych. Automatyczne skalowanie pamięci masowej o rozmiarze do 100 TB, szybkie skalowanie w pionie i poziomie, szybkie przywracanie bazy danych.|Aplikacje OLTP z wysoką szybkością transakcji i niskim opóźnieniem we/wy. Oferuje najwyższą odporność na awarie i szybkie pracy awaryjnej przy użyciu wielu synchronicznie zaktualizowanych replik.|
|  **Typ zasobu** ||Pojedyncza baza danych / pula elastyczna / wystąpienie zarządzane | Pojedyncza baza danych | Pojedyncza baza danych / pula elastyczna / wystąpienie zarządzane |
| **Rozmiar obliczeń**|Pojedyncza baza danych / pula elastyczna * | Od 1 do 80 vCorów | Od 1 do 80 vCorów* | Od 1 do 80 vCorów |
| |Wystąpienie zarządzane | 8, 16, 24, 32, 40, 64, 80 vCores | Nie dotyczy | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Typ magazynu** | Wszystkie |Magazyn zdalny w wersji premium (na wystąpienie) | Magazyn oddzielony od pary z lokalną pamięcią podręczną SSD (na wystąpienie) | Superszybki lokalny magazyn dysków SSD (na wystąpienie) |
| **Rozmiar magazynu** | Pojedyncza baza danych / pula elastyczna *| 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Liczba operacji we/wy na sekundę** | Pojedyncza baza danych | 500 IOPS na r/r z maksymalną 7000 IOPS | Hiperskala to architektura wielopoziomowa z buforowania na wielu poziomach. Skuteczne we/wy będą zależeć od obciążenia. | 5000 IOPS z 200 000 maksymalnych we/wy|
| | Wystąpienie zarządzane | Zależy od rozmiaru pliku | Nie dotyczy | 1375 IOPS/vCore |
|**Dostępność**|Wszystkie|1 replika, brak skalowania w poziomie odczytu, brak lokalnej pamięci podręcznej | Wiele replik, maksymalnie 4 skalowanie odczytu w poziomie, częściowa pamięć podręczna lokalna | 3 repliki, 1 skalowa w poziomie odczytu, nadmiarowa strefa HA, pełna pamięć masowa lokalna |
|**Tworzenie kopii zapasowych**|Wszystkie|RA-GRS, 7-35 dni przechowywania (domyślnie 7 dni)| RA-GRS, 7-dniowa retencja, stałe odzyskiwanie punktu czasu (PITR) | RA-GRS, 7-35 dni przechowywania (domyślnie 7 dni) |

\*Pule elastyczne nie są obsługiwane w warstwie usługi Hiperskali

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kto powinien korzystać z warstwy usługi Hiperskala

Warstwa usług hyperscale jest przeznaczona dla klientów, którzy mają duże lokalne bazy danych programu SQL Server i chcą zmodernizować swoje aplikacje, przechodząc do chmury, lub dla klientów, którzy już korzystają z usługi Azure SQL Database i chcą znacznie rozszerzyć potencjału wzrostu bazy danych. Hiperskala jest również przeznaczona dla klientów, którzy szukają zarówno wysokiej wydajności, jak i wysokiej skalowalności. Dzięki hiperskali otrzymujesz:

- Rozmiar bazy danych do 100 TB
- Szybkie tworzenie kopii zapasowych bazy danych niezależnie od rozmiaru bazy danych (kopie zapasowe są oparte na migawkach magazynu)
- Szybkie przywracanie bazy danych niezależnie od rozmiaru bazy danych (przywracane są z migawek magazynu)
- Wyższa przepustowość dziennika niezależnie od rozmiaru bazy danych i liczby czeków wirtualnych
- Odczyt skalowano w poziomie przy użyciu jednej lub więcej replik tylko do odczytu, używanej do odciążania odczytu i jako tryb gotowości na gorąco.
- Szybkie skalowanie obliczeń w stałym czasie, aby być bardziej wydajnym, aby sprostać dużemu obciążeniu, a następnie skalować w dół, w stałym czasie. Jest to podobne do skalowania w górę iw dół między P6 i P11, na przykład, ale znacznie szybciej, ponieważ nie jest to rozmiar operacji danych.

### <a name="what-regions-currently-support-hyperscale"></a>Regiony obecnie obsługują hiperskali

Warstwa usługi Hiperskali jest obecnie dostępna w regionach wymienionych w obszarze [Omówienie hiperskali bazy danych SQL azure](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Czy można utworzyć wiele baz danych hiperskali na serwer logiczny

Tak. Aby uzyskać więcej informacji i limitów liczby baz danych w skali hiperskali na serwer logiczny, zobacz [Limity zasobów bazy danych SQL dla pojedynczych i puli baz danych na serwerze logicznym](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Jakie są charakterystyki wydajności bazy danych w hiperskali

Architektura hiperskali zapewnia wysoką wydajność i przepływność podczas obsługi dużych rozmiarów baz danych. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Jaka jest skalowalność bazy danych w hiperskali

Skala hiperskalowa zapewnia szybką skalowalność w zależności od zapotrzebowania na obciążenia.

- **Skalowanie w górę/w dół**

  Za pomocą hiperskali można skalować w górę podstawowy rozmiar obliczeń pod względem zasobów, takich jak procesor CPU i pamięć, a następnie skalować w dół, w stałym czasie. Ponieważ magazyn jest współużytkowane, skalowanie w górę i skalowanie w dół nie jest rozmiar operacji danych.  
- **Skalowanie wchylić/wyc./wyc.**

  Dzięki hiperskali można również uzyskać możliwość aprowizowania jednej lub więcej dodatkowych replik obliczeniowych, których można użyć do obsługi żądań odczytu. Oznacza to, że można użyć tych dodatkowych replik obliczeniowych jako replik tylko do odczytu, aby odciążyć obciążenie odczytu z podstawowych obliczeń. Oprócz tylko do odczytu repliki te służą również jako hot-standbys w przypadku pracy awaryjnej z podstawowego.

  Inicjowanie obsługi administracyjnej każdej z tych dodatkowych replik obliczeniowych można wykonać w stałym czasie i jest operacją online. Można połączyć się z tymi dodatkowymi replikami `ApplicationIntent` obliczeniowymi tylko `ReadOnly`do odczytu, ustawiając argument na ciągu połączenia na . Wszystkie połączenia z `ReadOnly` intencją aplikacji są automatycznie kierowane do jednej z dodatkowych replik obliczeniowych tylko do odczytu.

## <a name="deep-dive-questions"></a>Pytania deep dive

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Czy mogę mieszać hiperskali i pojedyncze bazy danych w jednym serwerze logicznym

Tak, można.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Czy hiperskala wymaga zmiany modelu programowania aplikacji

Nie, model programowania aplikacji pozostaje w stanie, w jakim jest. Ciąg połączenia jest używany w zwykły sposób i inne regularne sposoby interakcji z bazą danych hiperskali.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Jaki poziom izolacji transakcji jest domyślny w bazie danych hiperskali

W replice podstawowej domyślny poziom izolacji transakcji to RCSI (Odczyt izolacji migawki popełnione). W replikach pomocniczych skalowania w poziomie odczytu domyślnym poziomem izolacji jest migawka.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Czy mogę przenieść licencję lokalną lub IaaS SQL Server do hiperskali

Tak, [korzyść hybrydowa platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jest dostępna dla hiperskali. Każdy rdzeń programu SQL Server Standard może mapować na 1 rdzenie wirtualne w hiperskali. Każdy rdzeń programu SQL Server Enterprise może mapować na 4 rdzenie wirtualne w hiperskali. Nie potrzebujesz licencji SQL dla replik pomocniczych. Cena korzyści hybrydowej platformy Azure zostanie automatycznie zastosowana do replik skalowania w poziomie odczytu (pomocniczego).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Do jakiego rodzaju obciążeń jest zaprojektowana hiperskala

Skala hiperskali obsługuje wszystkie obciążenia programu SQL Server, ale jest zoptymalizowana przede wszystkim pod kątem oltp. Można również przynieść obciążeń hybrydowych (HTAP) i analitycznych (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Jak wybrać między usługą Azure SQL Data Warehouse a hiperskala bazy danych SQL Azure

Jeśli obecnie używasz zapytań analizy interaktywnej przy użyciu programu SQL Server jako magazynu danych, hiperskala jest doskonałym rozwiązaniem, ponieważ można obsługiwać małe i średnie magazyny danych (takie jak kilka TB do 100 TB) przy niższych kosztach i można migrować dane programu SQL Server i można migrować dane programu SQL Server obciążeń magazynowych do hiperskali przy minimalnych zmianach kodu T-SQL.

Jeśli korzystasz z analizy danych na dużą skalę ze złożonymi zapytaniami i stałymi szybkościami pozyskiwania wyższymi niż 100 MB/s lub przy użyciu magazynu danych równoległych (PDW), Teradata lub innych magazynów danych masowo równoległego przetwarzania (MPP), najlepszym rozwiązaniem może być magazyn danych SQL.
  
## <a name="hyperscale-compute-questions"></a>Pytania dotyczące obliczeń w hiperskali

### <a name="can-i-pause-my-compute-at-any-time"></a>Czy mogę wstrzymać obliczenia w dowolnym momencie

Nie w tej chwili można jednak skalować obliczenia i liczbę replik w dół, aby zmniejszyć koszty w godzinach poza szczytem.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Czy mogę aprowizować replikę obliczeniową z dodatkową pamięcią RAM dla mojego obciążenia wymagającego dużej ilości pamięci

Nie. Aby uzyskać więcej pamięci RAM, musisz uaktualnić do większego rozmiaru obliczeń. Aby uzyskać więcej informacji, zobacz [Magazyn hiperskali i rozmiary obliczeń](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Czy mogę aprowizować wiele replik obliczeniowych o różnych rozmiarach

Nie.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Ile replik skalowanych w poziomie odczytu jest obsługiwanych

Bazy danych w skali hiperskali są domyślnie tworzone przy jednej replice skalowanych w poziomie odczytu (dwie repliki, w tym podstawowa). Można skalować liczbę replik tylko do odczytu w zakresie od 0 do 4 przy użyciu [witryny Azure portal](https://portal.azure.com) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Aby uzyskać wysoką dostępność, muszę aprowizować dodatkowe repliki obliczeniowe

W bazach danych w hiperskali odporność danych jest dostępna na poziomie magazynu. Potrzebujesz tylko jednej repliki, aby zapewnić odporność. Gdy replika obliczeniowa jest wyłącza, nowa replika jest tworzona automatycznie bez utraty danych.

Jednak jeśli istnieje tylko jedna replika, może upłynąć trochę czasu, aby zbudować lokalną pamięć podręczną w nowej replice po przełączeniu w tryb failover. Podczas fazy przebudowy pamięci podręcznej baza danych pobiera dane bezpośrednio z serwerów stron, co powoduje większe opóźnienie magazynu i obniżoną wydajność zapytań.

W przypadku aplikacji o znaczeniu krytycznym, które wymagają wysokiej dostępności przy minimalnym wpływie pracy awaryjnej, należy aprowizować co najmniej 2 repliki obliczeniowe, w tym podstawową replikę obliczeniową. To jest konfiguracja domyślna. W ten sposób dostępna jest replika w trybie gotowości, która służy jako miejsce docelowe pracy awaryjnej.

## <a name="data-size-and-storage-questions"></a>Pytania dotyczące rozmiaru i przechowywania danych

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Jaki jest maksymalny rozmiar bazy danych obsługiwany w hiperskali

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Jaki jest rozmiar dziennika transakcji za pomocą funkcji Hiperskala

Dziennik transakcji z hiperskali jest praktycznie nieskończony. Nie musisz się martwić o wyczerpaniu miejsca w dzienniku w systemie, który ma wysoką przepustowość dziennika. Jednak szybkość generowania dziennika może być ograniczona dla ciągłego agresywnego zapisywania obciążeń. Najwyższa trwała generacja dziennika wynosi 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Czy `tempdb` moja skala w miarę wzrostu bazy danych

Baza `tempdb` danych znajduje się na lokalnym magazynie SSD i jest dopasowywalna proporcjonalnie do rozmiaru obliczeń, który aprowizujesz. Twój `tempdb` jest zoptymalizowany, aby zapewnić maksymalne korzyści wydajności. `tempdb`rozmiar nie jest konfigurowalny i jest zarządzany dla Ciebie.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Czy rozmiar mojej bazy danych automatycznie rośnie, czy też muszę zarządzać rozmiarem plików danych

Rozmiar bazy danych automatycznie rośnie w miarę wstawiania/pozyskiwania większej ilości danych.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Jaki jest najmniejszy rozmiar bazy danych, który umożliwia lub rozpoczyna

40 GB. Baza danych na dużą skalę jest tworzona z rozmiarem początkowym 10 GB. Następnie zaczyna rosnąć o 10 GB co 10 minut, aż osiągnie rozmiar 40 GB. Każdy z tych uchwytów 10 GB jest przydzielany na innym serwerze strony w celu zapewnienia większej liczby we/wy i wyższego równoległości we/wy. Z powodu tej optymalizacji, nawet jeśli wybierzesz rozmiar początkowej bazy danych mniejszy niż 40 GB, baza danych wzrośnie automatycznie do co najmniej 40 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>W jakich przyrostach powiększa się mój rozmiar bazy danych

Każdy plik danych powiększa się o 10 GB. Wiele plików danych może rosnąć w tym samym czasie.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Czy pamięć masowa jest w hiperskalowej

W hiperskali pliki danych są przechowywane w standardowym magazynie platformy Azure. Dane są w pełni buforowane na lokalnym magazynie SSD, na serwerach stron, które znajdują się w pobliżu replik obliczeniowych. Ponadto repliki obliczeniowe mają pamięć podręczną danych na lokalnym ssd i w pamięci, aby zmniejszyć częstotliwość pobierania danych z serwerów strony zdalnej.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Czy mogę zarządzać plikami lub grupami plików lub grupami plików za pomocą funkcji Hyperscale

Nie. Pliki danych są dodawane automatycznie. Typowe powody tworzenia dodatkowych grup plików nie mają zastosowania w architekturze magazynu hiperskali.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Czy mogę udostępnić twardy limit wzrostu danych dla mojej bazy danych

Nie.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Jak są rozmieszczone pliki danych za pomocą hiperskali

Pliki danych są kontrolowane przez serwery stron, z jednym serwerem strony na plik danych. Wraz ze wzrostem rozmiaru danych dodawane są pliki danych i skojarzone serwery stron.

### <a name="is-database-shrink-supported"></a>Czy obsługa zmniejszania bazy danych jest obsługiwana

Nie.

### <a name="is-data-compression-supported"></a>Czy kompresja danych jest obsługiwana

Tak, w tym kompresja wiersza, strony i magazynu kolumn.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Jeśli mam ogromną tabelę, czy moje dane tabeli są rozrzucone na wiele plików danych

Tak. Strony danych skojarzone z daną tabelą mogą trafić do wielu plików danych, które są częścią tej samej grupy plików. SQL Server używa [strategii proporcjonalnego wypełniania](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) do dystrybucji danych za pośrednictwem plików danych.

## <a name="data-migration-questions"></a>Pytania dotyczące migracji danych

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Czy mogę przenieść istniejące bazy danych SQL platformy Azure do warstwy usług hyperscale

Tak. Istniejące bazy danych SQL platformy Azure można przenieść do hiperskali. Jest to migracja jednokierunkowa. Nie można przenieść baz danych z hiperskali do innej warstwy usług. W przypadku weryfikacji koncepcji (POC) zaleca się wykonanie kopii bazy danych i migrację kopii do hiperskali.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Czy mogę przenieść bazy danych hiperskali do innych warstw usług

Nie. W tej chwili nie można przenieść bazy danych na skalę hiperskalową do innej warstwy usług.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Czy po przejściu do warstwy usług hiperskali tracę jakieś funkcje lub możliwości

Tak. Niektóre funkcje usługi Azure SQL Database nie są jeszcze obsługiwane w hiperskali, w tym, ale nie ograniczając się do długoterminowego przechowywania kopii zapasowych. Po migracji baz danych do hiperskali te funkcje przestają działać.  Oczekujemy, że te ograniczenia będą tymczasowe.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Czy mogę przenieść lokalną bazę danych programu SQL Server lub bazę danych programu SQL Server na maszynie wirtualnej w chmurze do hiperskali

Tak. Za pomocą wszystkich istniejących technologii migracji można przeprowadzić migrację do hiperskali, w tym replikacji transakcyjnej i innych technologii przenoszenia danych (kopiowanie zbiorcze, fabryka danych platformy Azure, usługi Azure Databricks, SSIS). Zobacz też [usługę migracji bazy danych platformy Azure](../dms/dms-overview.md), która obsługuje wiele scenariuszy migracji.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Co to jest mój przestój podczas migracji ze środowiska lokalnego lub środowiska maszyny wirtualnej do hiperskali i jak mogę go zminimalizować

Przestój migracji do hiperskali jest taki sam jak przestoje podczas migracji baz danych do innych warstw usług usługi Azure SQL Database. [Replikacja transakcyjna](replication-to-sql-database.md#data-migration-scenario
) służy do minimalizowania migracji przestojów dla baz danych o rozmiarze do kilku TB. W przypadku bardzo dużych baz danych (ponad 10 TB) można rozważyć migrację danych przy użyciu usługi ADF, Spark lub innych technologii przenoszenia danych.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Ile czasu zajęłoby wprowadzenie ilości danych X do hiperskali

Hiperskala jest w stanie zużywać 100 MB/s nowych/zmienionych danych, ale czas potrzebny na przeniesienie danych do baz danych SQL platformy Azure zależy również od dostępnej przepływności sieci, szybkości odczytu źródła i celu poziomu usługi bazy danych docelowej.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Czy mogę odczytać dane z magazynu obiektów blob i szybko załadować (jak Polybase w MAGAZYNIE DANYCH SQL)

Możesz mieć aplikację kliencką odczytu danych z usługi Azure Storage i załadować obciążenie danych do bazy danych w hiperskali (podobnie jak w przypadku dowolnej innej bazy danych SQL platformy Azure). Polybase nie jest obecnie obsługiwany w usłudze Azure SQL Database. Alternatywnie, aby zapewnić szybkie ładowanie, można użyć [usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)lub użyć zadania platformy Spark w [usłudze Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) ze [łącznikiem Spark dla języka SQL.](sql-database-spark-connector.md) Łącznik Platformy Spark do SQL obsługuje wstawianie zbiorcze.

Istnieje również możliwość zbiorczego odczytu danych z magazynu obiektów Blob platformy Azure przy użyciu magazynu BULK INSERT lub OPENROWSET: [Przykłady masowego dostępu do danych w usłudze Azure Blob Storage.](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)

Prosty model odzyskiwania lub rejestrowania zbiorczego nie jest obsługiwany w hiperskali. Pełny model odzyskiwania jest wymagane, aby zapewnić wysoką dostępność i punkt w czasie odzyskiwania. Jednak architektura dziennika hiperskali zapewnia lepszą szybkość pozyskiwania danych w porównaniu do innych warstw usług azure SQL Database.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Czy hiperskala umożliwia inicjowanie obsługi administracyjnej wielu węzłów do równoległego pozyskiwania dużych ilości danych

Nie. Hiperskala jest symetryczną architekturą wieloprze przetwarzania (SMP) i nie jest masowo równoległym przetwarzaniem (MPP) ani architekturą wielopanową. Można utworzyć tylko wiele replik, aby skalować w poziomie obciążeń tylko do odczytu.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Jaka jest najstarsza wersja programu SQL Server obsługiwana do migracji do hiperskali

Program SQL Server 2005. Aby uzyskać więcej informacji, zobacz [Migrowanie do pojedynczej bazy danych lub puli bazy danych](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Aby zapoznać się z problemami ze zgodnością, zobacz [Rozwiązywanie problemów ze zgodnością migracji bazy danych](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Czy hiperskala obsługuje migrację z innych źródeł danych, takich jak Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 i innych platform baz danych

Tak. [Usługa migracji bazy danych platformy Azure](../dms/dms-overview.md) obsługuje wiele scenariuszy migracji.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Pytania dotyczące ciągłości działania i odzyskiwania po awarii

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Jakie la są dostarczane dla bazy danych w hiperskali

Zobacz [umowy SLA dla usługi Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Dodatkowe pomocnicze repliki obliczeniowe zwiększają dostępność, do 99,99% dla bazy danych z co najmniej dwiema pomocniczymi replikami obliczeniowymi.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Czy kopie zapasowe bazy danych są zarządzane dla mnie przez usługę Azure SQL Database

Tak.

### <a name="how-often-are-the-database-backups-taken"></a>Jak często są podejmowane kopie zapasowe bazy danych

Nie ma tradycyjnych pełnych, różnicowych i dziennikowych kopii zapasowych dla baz danych hiperskali. Zamiast tego istnieją regularne migawki magazynu plików danych. Dziennik, który jest generowany jest po prostu zachowywane jako —jest dla skonfigurowany okres przechowywania, co pozwala przywrócić do dowolnego punktu w czasie w okresie przechowywania.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Czy funkcja hyperscale support point in time restore

Tak.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Co to jest cel punktu odzyskiwania (RPO)/czas odzyskiwania (RTO) do przywracania bazy danych w hiperskali

RPO wynosi 0 min. Cel RTO jest krótszy niż 10 minut, niezależnie od rozmiaru bazy danych. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Kopie zapasowe bazy danych wpływają na wydajność obliczeniową repliki podstawowej lub pomocniczej

Nie. Kopie zapasowe są zarządzane przez podsystem magazynu i wykorzystują migawki magazynu. Nie mają one wpływu na obciążenia użytkowników.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Czy można przeprowadzić przywracanie geograficzne za pomocą bazy danych w hiperskali

Tak. Przywracanie geograficzne jest w pełni obsługiwane. W przeciwieństwie do przywracania punktu w czasie, przywracanie geograficzne wymaga operacji rozmiaru danych. Pliki danych są kopiowane równolegle, więc czas trwania tej operacji zależy przede wszystkim od rozmiaru największego pliku w bazie danych, a nie od całkowitego rozmiaru bazy danych. Czas przywracania geograficznego będzie znacznie krótszy, jeśli baza danych zostanie przywrócona w regionie platformy Azure, który jest [sparowany](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) z regionem źródłowej bazy danych.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Czy mogę skonfigurować replikację geograficzną za pomocą bazy danych hiperskali

Nie w tej chwili.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Czy mogę wykonać kopię zapasową bazy danych hiperskali i przywrócić ją na serwerze lokalnym lub na programie SQL Server na maszynie Wirtualnej

Nie. Format magazynu dla baz danych w hiperskali różni się od dowolnej wydanej wersji programu SQL Server i nie kontrolujesz kopii zapasowych ani nie masz do nich dostępu. Aby wyjąć dane z bazy danych w hiperskali, można wyodrębnić dane przy użyciu dowolnej technologii przenoszenia danych, czyli usługi Azure Data Factory, Azure Databricks, SSIS itp.

## <a name="cross-feature-questions"></a>Pytania między funkcjami

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Czy po przejściu do warstwy usług hiperskali tracę jakieś funkcje lub możliwości

Tak. Niektóre funkcje usługi Azure SQL Database nie są obsługiwane w hiperskali, w tym, ale nie ograniczając się do długoterminowego przechowywania kopii zapasowych. Po migracji baz danych do hiperskali te funkcje przestają działać.

### <a name="will-polybase-work-with-hyperscale"></a>Czy Polybase będzie współpracować z hiperskali

Nie. Polybase nie jest obsługiwana w usłudze Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Czy hiperskala ma wsparcie dla języka R i Python

Nie w tej chwili.

### <a name="are-compute-nodes-containerized"></a>Są konteneryzowane węzłami obliczeniowymi

Nie. Procesy hiperskali są uruchamiane w węzłach [sieci szkieletowej usług](https://azure.microsoft.com/services/service-fabric/) (maszyn wirtualnych), a nie w kontenerach.

## <a name="performance-questions"></a>Pytania dotyczące wydajności

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Ile przepływności zapisu można wypchnąć w bazie danych hiperskali

Limit przepływności dziennika transakcji jest ustawiony na 100 MB/s dla dowolnego rozmiaru obliczeń hiperskali. Możliwość osiągnięcia tej szybkości zależy od wielu czynników, w tym, ale nie ograniczając się do typu obciążenia, konfiguracji klienta i posiadające wystarczającą pojemność obliczeniową w podstawowej repliki obliczeniowej do produkcji dziennika w tym tempie.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Ile IOPS mogę uzyskać na największych obliczeń

Czasy we/wy i opóźnienie we/wy będą się różnić w zależności od wzorców obciążenia. Jeśli dostępne dane są buforowane na replice obliczeniowej, zobaczysz podobną wydajność we/wy, jak w przypadku lokalnego identyfikatora SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Czy kopie zapasowe mają wpływ na moją przepustowość

Nie. Obliczeń jest oddzielony od warstwy magazynu. Eliminuje to wpływ tworzenia kopii zapasowych na wydajność.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Czy moja przepustowość ulega wpływowi podczas inicjowania obsługi administracyjnej dodatkowych replik obliczeniowych

Ponieważ magazyn jest współużytkowany i nie ma bezpośredniej replikacji fizycznej między replikami podstawowymi i pomocniczymi, przepływność repliki podstawowej nie będzie miała bezpośredniego wpływu na dodawanie replik pomocniczych. Możemy jednak ograniczyć ciągłe agresywne zapisywanie obciążenia na podstawowym, aby umożliwić log stosuje się na repliki pomocnicze i serwery stron, aby nadrobić zaległości, aby uniknąć niskiej wydajności odczytu w replikach pomocniczych.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Jak zdiagnozować i rozwiązać problemy z wydajnością w bazie danych hiperskali

W przypadku większości problemów z wydajnością, szczególnie tych, które nie są zakorzenione w wydajności magazynu, stosuje się typowe kroki diagnostyczne i rozwiązywanie problemów programu SQL Server. Aby zapoznać się z diagnostyką magazynu specyficzną dla hiperskali, zobacz [Diagnostyka rozwiązywania problemów z wydajnością programu SQL Hyperscale](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Pytania dotyczące skalowalności

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Jak długo trzeba czekać na skalowanie repliki obliczeniowej w górę i w dół

Skalowanie obliczeń w górę lub w dół powinno potrwać 5-10 minut, niezależnie od rozmiaru danych.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Czy moja baza danych jest w trybie offline, podczas gdy operacja skalowania w górę /w dół jest w toku

Nie. Skalowanie w górę iw dół będzie online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Czy powinienem spodziewać się spadku połączenia, gdy operacje skalowania są w toku

Skalowanie w górę lub w dół powoduje, że istniejące połączenia są usuwane, gdy praca awaryjna odbywa się na końcu operacji skalowania. Dodanie replik pomocniczych nie powoduje spadków połączeń.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Czy skalowanie replik obliczeniowych jest automatyczne lub wyzwalane przez użytkownika końcowego

Użytkowników końcowych. Nie automatyczne.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Czy rozmiar mojej `tempdb` bazy danych również rośnie w miarę skalowania obliczeń

Tak. Baza `tempdb` danych będzie skalowana automatycznie w miarę wzrostu obliczeń.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Czy mogę aprowizować wiele podstawowych replik obliczeniowych, takich jak system wielowymiełnowy, w którym wiele podstawowych głowic obliczeniowych może prowadzić wyższy poziom współbieżności

Nie. Tylko podstawowa replika obliczeniowa akceptuje żądania odczytu/zapisu. Pomocnicze repliki obliczeniowe akceptują tylko żądania tylko do odczytu.

## <a name="read-scale-out-questions"></a>Przeczytaj pytania skalowawczy w poziomie

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Ile pomocniczych replik obliczeniowych można aprowizować

Domyślnie tworzymy jedną replikę pomocniczą dla baz danych w hiperskali. Jeśli chcesz dostosować liczbę replik, możesz to zrobić za pomocą [witryny Azure portal](https://portal.azure.com) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Jak połączyć się z tymi pomocniczymi replikami obliczeniowymi

Można połączyć się z tymi dodatkowymi replikami `ApplicationIntent` obliczeniowymi tylko `ReadOnly`do odczytu, ustawiając argument na ciągu połączenia na . Wszystkie połączenia oznaczone `ReadOnly` za pomocą są automatycznie kierowane do jednej z dodatkowych replik obliczeniowych tylko do odczytu.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Jak sprawdzić poprawność, jeśli pomyślnie połączono mnie z pomocniczą repliką obliczeniową przy użyciu usługi SSMS lub innych narzędzi klienckich?

Można wykonać następującą kwerendę `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`T-SQL: .
Wynik jest, `READ_ONLY` jeśli są połączone z repliką `READ_WRITE` pomocniczą tylko do odczytu i jeśli są połączone z repliką podstawową. Należy zauważyć, że kontekst bazy danych musi być ustawiona `master` na nazwę bazy danych hiperskali, a nie do bazy danych.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Czy można utworzyć dedykowany punkt końcowy dla repliki skalowania w poziomie odczytu

Nie. Z replikami skalowanych w poziomie odczytu można łączyć tylko określając opcję `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Czy system wykonuje inteligentne równoważenie obciążenia obciążenia odczytu obciążenia

Nie. Nowe połączenie z intencją tylko do odczytu jest przekierowywane do dowolnej repliki skalowania w poziomie odczytu.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Czy można skalować repliki pomocnicze w górę/w dół niezależnie od repliki podstawowej

Nie. Pomocnicza replika obliczeniowa są również używane jako docelowych pracy awaryjnej wysokiej dostępności, więc muszą mieć taką samą konfigurację jak podstawowy, aby zapewnić oczekiwaną wydajność po pracy awaryjnej.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Czy mam `tempdb` inny rozmiar dla moich podstawowych obliczeń i dodatkowych pomocniczych replik obliczeniowych

Nie. Baza `tempdb` danych jest konfigurowana na podstawie inicjowania obsługi administracyjnej rozmiaru obliczeń, pomocnicze repliki obliczeń mają taki sam rozmiar jak obliczenia podstawowe.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Czy mogę dodawać indeksy i widoki w pomocniczych replikach obliczeniowych

Nie. Bazy danych w hiperskali mają udostępniony magazyn, co oznacza, że wszystkie repliki obliczeniowe widzą te same tabele, indeksy i widoki. Jeśli chcesz dodatkowe indeksy zoptymalizowane pod kątem odczytów w pomocniczych, należy dodać je na podstawowym.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Ile opóźnień będzie między replikami podstawowymi i pomocniczymi

Opóźnienie danych od czasu transakcji jest zatwierdzana na podstawowej do czasu, gdy jest widoczny na pomocnicze zależy od bieżącej szybkości generowania dziennika. Typowe opóźnienie danych jest w niskich milisekundach.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat warstwy usług hiperskali, zobacz [warstwa usług hiperskali](sql-database-service-tier-hyperscale.md).
