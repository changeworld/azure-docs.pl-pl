---
title: Usługi Azure SQL Database o dużej skali — często zadawane pytania | Dokumentacja firmy Microsoft
description: Poproś odpowiedzi na typowe pytania klientów dotyczące usługi Azure SQL database w warstwie usług na dużą skalę — często nazywane bazę danych na dużą skalę.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: 7746d7256add185be0c67123edf63ea09b6b05a4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111836"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Często zadawane pytania dotyczące bazy danych SQL Azure na dużą skalę

Ten artykuł zawiera odpowiedzi na często zadawane pytania dla klientów rozważających bazę danych w warstwie usług Azure SQL Database na dużą skalę, często nazywane bazę danych na dużą skalę (obecnie dostępna w publicznej wersji zapoznawczej). W tym artykule opisano scenariusze, które obsługuje w Hiperskali i ogólnie rzecz biorąc usług dla wielu funkcji są zgodne z bazy danych SQL na dużą skalę.

- Często zadawane pytania jest przeznaczony dla osób, którzy krótki opis warstwy usługi w Hiperskali i chcą mieć ich konkretne pytania i odpowiedzi na problemy.
- Często zadawane pytania nie powinno być — przewodnik lub odpowiedzi na pytania dotyczące sposobu korzystania z bazy danych SQL Database na dużą skalę. W tym firma Microsoft zaleca, możesz odwołać się do [bazy danych SQL Azure na dużą skalę](sql-database-service-tier-hyperscale.md) dokumentacji.

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-a-hyperscale-database"></a>Co to jest bazą danych na dużą skalę

Baza danych na dużą skalę jest usługi Azure SQL database w warstwie usługi w Hiperskali, która jest wspierana przez technologii magazynowania skalowalnego w poziomie na dużą skalę. Bazę danych na dużą skalę obsługuje do 100 TB danych i zapewnia wysoką przepływność i wydajności, a także szybkie skalowanie w celu dostosowania do wymagań dotyczących obciążenia. Skalowanie jest niewidoczny dla aplikacji — łączność, przetwarzanie zapytań i tak dalej, pracował jak każdej innej bazy danych SQL.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Jakie typy zasobów i modele zakupu obsługiwane na dużą skalę

Warstwy usługi w Hiperskali jest dostępna tylko dla pojedynczych baz danych przy użyciu modelu zakupu opartego na rdzeniach wirtualnych w usłudze Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Czym różni się w warstwie usług na dużą skalę z warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy

Usługi oparte na rdzeniach wirtualnych warstwy różnią się głównie na podstawie dostępności, typ magazynu i operacje We/Wy.

- Warstwy usług ogólnego przeznaczenia jest odpowiednie dla większości obciążeń biznesowych, oferując zestaw o zrównoważonym obciążeniu opcji obliczeniowych i magazynu, w której czas opóźnienia lub pracy awaryjnej we/wy nie są priorytet.
- Warstwy usług na dużą skalę jest zoptymalizowany pod kątem obciążeń dużych baz danych.
- Krytyczne dla działania firmy warstwy usług jest odpowiednia dla obciążeń biznesowych, gdzie ma najwyższy priorytet, czas oczekiwania operacji We/Wy.

| | Typ zasobu | Ogólne zastosowanie |  Hiperskala | Krytyczne dla działania firmy |
|:---|:---:|:---:|:---:|:---:|
| **Najlepsze dla** |Wszyscy|  Większości obciążeń biznesowych. Oferty budżetu zorientowane na obiekty o zrównoważonym obciążeniu opcji obliczeniowych i magazynu. | Aplikacji przetwarzających dane z wymagań w zakresie pojemności dużych ilości danych i możliwości automatycznego skalowania magazynu i Skaluj obliczenia płynnie. | Przetwarzanie OLTP danych aplikacji za pomocą dużo transakcji i najniższym opóźnieniu operacji We/Wy. Oferuje najwyższą odporność na awarie, za pomocą kilku izolowany replik.|
|  **Typ zasobu** ||Pojedynczą bazę danych / elastycznej puli / wystąpienia zarządzanego | Pojedyncza baza danych | Pojedynczą bazę danych / elastycznej puli / wystąpienia zarządzanego |
| **Obliczenia rozmiaru**|Pojedynczą bazę danych / elastycznej puli * | 1 do 80 rdzeni wirtualnych | 1 do 80 rdzeni wirtualnych * | 1 do 80 rdzeni wirtualnych |
| |Wystąpienie zarządzane | 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | ND | 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| **Typ magazynu** | Wszyscy |Magazyn zdalny — wersja Premium (na wystąpienie) | Produkcyjnej magazynu z lokalnego dysku SSD pamięci podręcznej (na wystąpienie) | Superszybkiego lokalny magazyn SSD (na wystąpienie) |
| **Rozmiar magazynu** | Pojedynczą bazę danych / elastycznej puli | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | ND | 32 GB – 4 TB |
| **Przepustowość operacji We/Wy** | Pojedynczy bazę danych ** | 500 operacji We/Wy na rdzeniach wirtualnych za pomocą 7000 maksymalna liczba IOPS | Jeszcze nieznane | 5000 operacji We/Wy z 200 000 maksymalna liczba IOPS|
| | Wystąpienie zarządzane | Zależy od rozmiaru pliku | ND | Wystąpienie zarządzane: Zależy od rozmiaru pliku|
|**Dostępność**|Wszyscy|1 repliki, brak skalę odczytywania, nie lokalnej pamięci podręcznej | Wiele replik maksymalnie 15 skalę odczytywania, częściowe lokalnej pamięci podręcznej | 3 repliki, 1 HA skalę odczytywania, strefowo nadmiarowe pełnej lokalnej pamięci podręcznej |
|**Tworzenie kopii zapasowych**|Wszyscy|RA-GRS, 7 – 35 dni (domyślnie co 7 dni)| RA-GRS, 7 – 35 dni (7 dni domyślnie), stałym czasie in0time punktu odzyskiwania (Odzyskiwanie) | RA-GRS, 7 – 35 dni (domyślnie co 7 dni) |

\* Pule elastyczne nie są obsługiwane w przypadku warstwy usług na dużą skalę

### <a name="who-should-use-the-hyperscale-service-tier"></a>Kto powinien używać warstwy usług na dużą skalę

Warstwy usług na dużą skalę jest przeznaczone głównie dla klientów, którzy dużych lokalnych baz danych z programu SQL Server i chcesz modernizuj swoje aplikacje dzięki przeniesieniu do chmury lub dla klientów, którzy są już za pomocą usługi Azure SQL Database i chcesz znacznie rozszerzyć możliwości wzrostu bazy danych. Na dużą skalę, również jest przeznaczony dla klientów, którzy wyszukiwania o wysokiej wydajności i wysokiej skalowalności. Za pomocą w Hiperskali zapewnia następujące korzyści:

- Obsługa do 100 TB, rozmiar bazy danych
- Kopie zapasowe szybkie bazy danych niezależnie od rozmiaru bazy danych (kopie zapasowe są oparte na migawki plików)
- Przywracanie szybkie baz danych niezależnie od rozmiaru bazy danych (Przywracanie pochodzą z pliku migawki)
- Większą przepływność dziennika skutkuje czas zatwierdzenia transakcji szybko niezależnie od rozmiaru bazy danych
- Odczyt skalowania w poziomie do co najmniej jeden węzeł tylko do odczytu, odciążania obciążenia odczytu i spełniają gorąca.
- Szybkie skalowanie w górę mocy obliczeniowej, w czasie stała się bardziej zaawansowanych, aby pomieścić duże obciążenia, a następnie Skaluj w dół w stałym czasie. Jest to podobne do skalowania w górę i w dół między P6 do P11, na przykład, ale znacznie szybciej, ponieważ jest nie większy niż operacji danych.

### <a name="what-regions-currently-support-hyperscale"></a>Jakie regiony obsługują obecnie na dużą skalę

Na dużą skalę jest obecnie dostępna dla pojedynczych baz danych w następujących regionach:  US1 Zachodnia, zachodnie stany USA 2, wschodnie US1, środkowe stany USA, Europa Zachodnia, Europa Północna, Azja południowo-wschodnia, Japonia Wschodnia, Korea środkowa, Australia Południowo-Wschodnia i Australia Wschodnia.

### <a name="can-i-create-multiple-hyperscale-databases-per-sql-database-server"></a>Można tworzyć wiele Hiperskali baz danych na serwerze bazy danych SQL

Tak. Aby uzyskać więcej informacji i limity liczby baz danych na dużą skalę w jednej bazy danych programu SQL server, zobacz [limity zasobów bazy danych SQL Database dla pojedynczych i puli baz danych na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md).

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>Co to są Charakterystyka wydajności bazy danych na dużą skalę

Architektura bazy danych SQL na dużą skalę zapewnia wysoką wydajność i przepływność obsługując rozmiary dużych baz danych. Profil wydajności dokładne i właściwości nie jest dostępna w publicznej wersji zapoznawczej.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Co to jest skalowalność bazy danych na dużą skalę

Bazy danych SQL na dużą skalę oferuje szybką skalowalność, zależnie od potrzeb Twojego obciążenia.

- **Skalowanie w górę/w dół**

  W Hiperskali możesz skalować w górę rozmiar głównej obliczeniowych pod względem zasobów, takich jak procesor CPU, pamięć i następnie Skaluj w dół w stałym czasie. Ponieważ Magazyn jest udostępniany, skalowanie w górę i skalowania w dół nie jest rozmiar operacji danych.  
- **Skalowanie na wejściu/wyjściu**

  Za pomocą na dużą skalę możesz także uzyskać możliwość inicjowania obsługi co najmniej jeden węzeł dodatkowe zasoby obliczeniowe, które służą do obsługi żądań odczytu. Oznacza to, że tych dodatkowych węzłów obliczeniowych można używać jako tylko do odczytu węzły odciążania odczytu obciążenie z podstawowej obliczeń. Dodatkowo tylko do odczytu, te węzły również służyć jako stałej gotowości użytkownika w przypadku awarii za pośrednictwem z serwera podstawowego.

  Inicjowanie obsługi administracyjnej każdego z tych dodatkowe zasoby obliczeniowe węzłów może odbywać się w stałym czasie i jest operacji w trybie online. Możesz nawiązać węzły te dodatkowe zasoby obliczeniowe tylko do odczytu, ustawiając `ApplicationIntent` argumentu w ciągu połączenia w celu `read_only`. Wszystkie połączenia oznaczone `read-only` są automatycznie kierowane do jednego z węzłów dodatkowe zasoby obliczeniowe tylko do odczytu.

## <a name="deep-dive-questions"></a>Szczegółowe omówienie pytania

### <a name="can-i-mix-hyperscale-and-single-databases-a-my-sql-database-server"></a>Czy można mieszać w Hiperskali i pojedynczych baz danych Mój serwer bazy danych SQL

Tak, możesz.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Czy na dużą skalę wymaga mojej aplikacji modelu programowania, aby zmienić

Nie, to pozostanie on modelu programowania aplikacji. Umożliwia parametrów połączenia w zwykły sposób i regularnego inne tryby interakcji z bazą danych Azure SQL.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Poziomy izolacji transakcji mają być domyślny w bazie danych SQL Database na dużą skalę

W węźle podstawowym poziom izolacji transakcji jest RCSI (izolacja migawki zatwierdzone odczytu). Skalowanie odczytu dodatkowych węzłów migawki jest poziom izolacji.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Czy mogę przenieść Moje wdrożenia lokalne czy licencji IaaS programu SQL Server do bazy danych SQL na dużą skalę

Tak, [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) jest dostępna dla na dużą skalę. Co program SQL Server Standard podstawowej można zamapować na 1 rdzeni na dużą skalę. Każdy rdzeń programu SQL Server Enterprise można mapować do 4 rdzeni na dużą skalę. Nie potrzebujesz licencji SQL dla replik pomocniczych. Cena korzyści użycia hybrydowego platformy Azure zostaną automatycznie zastosowane do skali odczytu replik (pomocniczy).

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Jakiego rodzaju obciążenia bazy danych SQL na dużą skalę zaprojektowano pod kątem

Baza danych SQL na dużą skalę obsługuje wszystkich obciążeń programu SQL Server, ale przede wszystkim jest on zoptymalizowany pod kątem OLTP. Przenoszenie hybrydowych i analityczna (składnicy danych) obsługiwać obciążenia.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Jak można wybrać jedną Azure SQL Data Warehouse i bazy danych SQL na dużą skalę

Jeśli jesteś obecnie uruchamiania zapytań interaktywnych analiz przy użyciu programu SQL Server jako magazyn danych SQL Database na dużą skalę jest świetnym rozwiązaniem, ponieważ może obsługiwać magazyny danych stosunkowo mały (na przykład kilka TB aż zawierać dziesiątki TB) przy niskich kosztach i można migrować dane  Obciążenie magazynu na bazę danych SQL na dużą skalę bez zmian w kodzie języka T-SQL.

Jeśli jesteś prowadzą analizy danych na dużą skalę za pomocą złożonych kwerend i przy użyciu magazynu danych równoległych (PDW), Teradata lub innych wysoce równoległe procesora (MPP)) hurtowni danych, usługa SQL Data Warehouse może być najlepszym wyborem.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Pytania dotyczące obliczeń bazy danych SQL na dużą skalę

### <a name="can-i-pause-my-compute-at-any-time"></a>Czy można wstrzymać Moje obliczeń w dowolnym momencie

Nie.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Czy mogę uaktywnić obliczeniowe przy użyciu dodatkowej pamięci RAM dla mojego obciążenia intensywnie korzystających z pamięci

Nie. Aby uzyskać więcej pamięci RAM, musisz uaktualnić do wyższej rozmiaru obliczeń. 4. generacji sprzętu oferuje więcej pamięci RAM, w porównaniu do 5. generacji sprzętu. Aby uzyskać więcej informacji, zobacz [rozmiarów magazynu i mocy obliczeniowej w Hiperskali](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-preview-for-provisioned-compute-tier).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Czy mogę uaktywnić wielu węzłach obliczeniowych o różnych rozmiarach

Nie.

### <a name="how-many-read-scale-replicas-are-supported"></a>Jak wiele replik skalę odczytywania, które są obsługiwane.

W publicznej wersji zapoznawczej baz danych w Hiperskali są domyślnie tworzone przy użyciu jednej z replik skalę odczytywania (dwie repliki w sumie). Jeśli chcesz dodać lub usunąć skali odczytu replik, zgłoś żądanie pomocy technicznej w witrynie Azure portal.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Wysoką dostępność mogę muszą aprowizację dodatkowych węzłów obliczeniowych

W bazach danych na dużą skalę wysoką dostępność znajduje się na poziomie magazynu. Wystarczy tylko jedną replikę w celu zapewnienia wysokiej dostępności. Replika obliczeniowych nie działa, nowej repliki jest tworzona automatycznie bez utraty danych.

Jednakże jeśli istnieje tylko jedna replika, może potrwać trochę czasu kompilacji lokalnej pamięci podręcznej w nowej repliki po włączeniu trybu failover. W fazie ponownej kompilacji pamięci podręcznej bazy danych pobiera dane bezpośrednio z serwerów strony, co spowoduje pogorszenie wydajności operacji We/Wy i zapytań.

W przypadku aplikacji o kluczowym znaczeniu, które wymagają wysokiej dostępności należy udostępnić co najmniej 2 węzłów obliczeniowych, w tym węźle obliczeniowym głównej (ustawienie domyślne). W ten sposób dostępne w przypadku przejścia w tryb failover jest stałej gotowości.

## <a name="data-size-and-storage-questions"></a>Pytania dotyczące rozmiaru i magazynu danych

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Co to jest obsługiwany przy użyciu bazy danych SQL na dużą skalę rozmiar maksymalny bazy danych

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Co to jest rozmiaru dziennika transakcji o na dużą skalę

Dziennik transakcji z Hiperskali jest praktycznie nieskończone. Nie trzeba już martwić się o brakiem miejsca w dzienniku w systemie, w którym dziennika o wysokiej przepustowości. Jednak szybkości generowania rekordów dziennika może być ograniczona dla ciągłej agresywne obciążeń. Szczytowe i średnia Zaloguj generacji współczynnik nie jest jeszcze znany (nadal w wersji zapoznawczej).

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Mojej tymczasowej bazy danych Skaluj w miarę wzrostu bazy danych

Twoje `tempdb` baza danych znajduje się na lokalny magazyn SSD i jest skonfigurowany na podstawie rozmiaru obliczeń, który możesz aprowizować. Twoje `tempdb` jest zoptymalizowane pod kątem i rozmieszczony w celu zapewnienia maksymalnej wydajności korzyści. `tempdb` Rozmiar nie jest konfigurowalne i zarządzanie odbywa się przez podsystem magazynowania.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Jest rozmiar bazy danych automatycznie zwiększać lub trzeba zarządzać rozmiarem plików danych

Rozmiar bazy danych automatycznie rozszerza się, jak możesz insert/pozyskiwania większej ilości danych.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Co to jest najmniejszy rozmiar bazy danych, która obsługuje lub zaczynające się od bazy danych SQL na dużą skalę

5 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>Jakie wielokrotność rozmiar bazy danych zwiększać

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Jest magazyn w Hiperskali bazy danych SQL, lokalnym lub zdalnym

W Hiperskali pliki danych są przechowywane w usłudze Azure standard storage. Dane są silnie buforowane na lokalny magazyn SSD na maszynach blisko węzłów obliczeniowych. Ponadto węzły obliczeniowe mają pamięci podręcznej na lokalne dyski SSD i w pamięci (puli buforów i tak dalej), zmniejszyć częstotliwość pobierania danych z węzłów zdalnego.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Czy mogę zarządzać lub zdefiniować plików lub grup z na dużą skalę

Nie
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Czy mogę uaktywnić twardych dzienny limit wzrostu ilości danych dla bazy danych

Nie

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Jak są pliki danych rozmieszczony z bazy danych SQL na dużą skalę

Pliki danych są kontrolowane przez serwery na stronie. Wzroście rozmiaru danych plików danych i węzły serwera skojarzonej strony zostaną dodane.

### <a name="is-database-shrink-supported"></a>Zmniejszania bazy danych jest obsługiwana

Nie

### <a name="is-database-compression-supported"></a>Kompresja bazy danych jest obsługiwana

Yes

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Jeśli mam ogromna tabeli, Moje dane tabeli Pobierz rozłożyć wielu plików danych

Tak. Strony danych skojarzone z danej tabeli mogą znaleźć się w wielu plików danych, które są częścią tej samej grupie plików. Program SQL Server używa [strategii wypełniania proporcjonalnego](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) dystrybuowania danych nad plikami danych.

## <a name="data-migration-questions"></a>Pytania dotyczące migracji danych

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Warstwy usług na dużą skalę można przenieść mojej istniejącej bazy danych Azure SQL

Tak. Można przenieść istniejących baz danych Azure SQL na dużą skalę. W publicznej wersji zapoznawczej jest to jednokierunkowe migracji. Nie można przenieść bazy danych w Hiperskali, do innej warstwy usług. Firma Microsoft zaleca, Utwórz kopię produkcyjnych bazach danych i migrację do w Hiperskali dla weryfikacji koncepcji (weryfikacji koncepcji).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Czy można przenosić Moje bazy danych na dużą skalę, do innych wersji

Nie. W publicznej wersji zapoznawczej nie można przenieść bazę danych na dużą skalę do innej warstwy usług.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Po zakończeniu migracji do warstwy usługi w Hiperskali utracić wszystkie funkcje lub możliwości

Tak. Długoterminowe kopie zapasowe przechowywane w bazie danych SQL Azure nie jest obsługiwana w Hiperskali w publicznej wersji zapoznawczej. Po przeprowadzeniu migracji bazy danych na dużą skalę, ta funkcja przestaje działać.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Czy mogę przenieść moją bazę danych programu SQL Server w środowisku lokalnym lub maszynie wirtualnej bazy danych programu SQL Server na dużą skalę

Tak. Można użyć wszystkich istniejących technologii migracji przeprowadzić migrację do na dużą skalę, łącznie z pliku BACPAC replikacji transakcyjnej, trwa ładowanie danych logicznych. Zobacz też [Azure Database Migration Service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Co to jest Moja przestój podczas migracji ze środowiska lokalnego lub w środowisku maszyny wirtualnej o dużej skali i jak mogę je zminimalizować

Czas przestoju, jest taka sama jak przestój podczas migracji baz danych do pojedynczej bazy danych w usłudze Azure SQL Database. Możesz użyć [replikacji transakcyjnej](replication-to-sql-database.md#data-migration-scenario
) aby zminimalizować przestoje migracji baz danych do kilku TB, rozmiar. Dla bardzo dużych baz danych (ponad 10 TB), można rozważyć, aby przeprowadzić migrację danych za pomocą usługi ADF, Spark lub inne technologie przenoszenia danych.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Ile czasu będzie je Wypełnij w X ilość danych do bazy danych SQL na dużą skalę

Nie są jeszcze znane (nadal w wersji zapoznawczej)

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Czy mogę odczytywać dane z magazynu obiektów blob i szybkie obciążenia (np. programu Polybase i SQL Data Warehouse)

Można odczytać danych z usługi Azure Storage i załadować ładowania danych do bazy danych na dużą skalę (podobnie można zrobić za pomocą regularnego pojedynczej bazy danych). Program Polybase nie jest obecnie obsługiwane w usłudze Azure SQL Database. Możesz zrobić przy użyciu technologii Polybase [usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) lub uruchamianie zadania Spark w [usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) z [łącznika usługi Spark SQL](sql-database-spark-connector.md). Łącznik platformy Spark SQL obsługuje wstawiania zbiorczego.

Odzyskiwania prostego lub modelu rejestrowanie zbiorcze nie jest obsługiwana w Hiperskali. Model odzyskiwania pełnego jest wymagany w celu zapewnienia wysokiej dostępności. Jednak w Hiperskali zapewnia lepsze pozyskiwanie szybkości w porównaniu do pojedynczej bazy danych ze względu na nowej architekturze dziennika.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Baza danych SQL na dużą skalę zezwala na inicjowanie obsługi administracyjnej wielu węzłów do wprowadzania dużych ilości danych

Nie. Baza danych SQL na dużą skalę jest architektura SMP i nie asymetrycznego przetwarzanie wieloprocesorowe lub wielu wzorców architektury. Można utworzyć tylko wiele replik w celu skalowania obciążeń tylko do odczytu.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Co to jest najstarsze programu SQL Server wersji będą migracji obsługi bazy danych SQL na dużą skalę z

SQL Server 2005. Aby uzyskać więcej informacji, zobacz [Przeprowadź migrację do pojedynczej bazy danych lub baza danych w puli](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). W przypadku problemów ze zgodnością, zobacz [Rozwiązywanie problemów ze zgodnością migracji bazy danych](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Baza danych SQL na dużą skalę obsługuje migrację z innych źródeł danych, takich jak Aurora, MySQL, Oracle, DB2 i innych platform bazy danych

Tak. Pochodzących z różnych źródeł danych innych niż SQL Server wymaga migracji logiczne. Możesz użyć [Azure Database Migration Service](../dms/dms-overview.md) logiczne migracji.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Pytania biznesowe ciągłość działalności biznesowej i odzyskiwanie po awarii odzyskiwania

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Umowy SLA podano dla bazy danych na dużą skalę

Ogólnie rzecz biorąc umowy SLA nie jest dostępna w publicznej wersji zapoznawczej. Jednak w Hiperskali zapewnia takiego samego poziomu wysokiej dostępności przy użyciu bieżących ofert bazy danych SQL. Zobacz [SLA](https://azure.microsoft.com/support/legal/sla/).

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Kopie zapasowe bazy danych odbywa się automatycznie przez usługę Azure SQL Database

Yes

### <a name="how-often-are-the-database-backups-taken"></a>Jak często wykonywane kopie zapasowe bazy danych

Nie istnieją żadne tradycyjnych pełnej, różnicowej i kopii zapasowych dzienników dla baz danych SQL Database na dużą skalę. Zamiast tego są regularnie migawki plików danych i dziennika, który jest generowany po prostu są zachowywane, jest okres przechowywania skonfigurowany lub dostępne dla Ciebie.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Obsługa bazy danych SQL na dużą skalę punktu przywracania do określonego

Yes

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Jaki jest cel punktu odzyskiwania (RPO) / cel czasu odzyskiwania (RTO) z kopii zapasowej/przywracania w Hiperskali bazy danych SQL

Cel punktu odzyskiwania jest 0 min. Cel czasu odzyskiwania ma mniej niż 10 minut, niezależnie od rozmiaru bazy danych. Jednak w publicznej wersji zapoznawczej, możesz napotkać dłuższy czas przywracania.

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Wykonaj kopie zapasowe dużych baz danych mogą wpłynąć na wydajność obliczeń Moje podstawowe

Nie. Kopie zapasowe są zarządzane przez podsystem magazynowania i korzystać z migawki pliku. Nie mieć wpływ na obciążenie użytkownikami na serwerze podstawowym.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Czy można wykonać przywracanie geograficzne z bazą danych SQL Database na dużą skalę

Nie, nie w okresie publicznej wersji zapoznawczej.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Czy można skonfigurować replikację geograficzną w Hiperskali bazy danych SQL Database

Nie, nie w okresie publicznej wersji zapoznawczej.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Moje węzłów obliczeniowych dodatkowej stopień replikacją geograficzną za pomocą bazy danych SQL na dużą skalę

Nie, nie w okresie publicznej wersji zapoznawczej.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Czy mogę wykonaj kopię zapasową bazy danych SQL Database na dużą skalę i przywrócić je do mojego lokalnego serwera lub programu SQL Server na maszynie Wirtualnej

Nie. Format magazynu dla baz danych na dużą skalę różni się od tradycyjnych programu SQL Server, a nie kontrolować kopii zapasowych lub uzyskać do nich dostęp. Aby można było dane z bazy danych SQL Database o dużej skali, korzystać z niej eksportu lub używanie skryptów oraz BCP.

## <a name="cross-feature-questions"></a>Wiele funkcji, pytania

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Po zakończeniu migracji do warstwy usługi w Hiperskali utracić wszystkie funkcje lub możliwości

Tak. Długoterminowe kopie zapasowe przechowywane w bazie danych SQL Azure nie jest obsługiwana w Hiperskali w publicznej wersji zapoznawczej. Po przeprowadzeniu migracji bazy danych na dużą skalę, ta funkcja przestaje działać.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Polybase będzie pracować z bazy danych SQL na dużą skalę

Nie. Program Polybase nie jest obsługiwane w usłudze Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Zasoby obliczeniowe ma obsługi języków R i python

Nie. Języki R i Python, nie są obsługiwane w usłudze Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Są w węzłach obliczeniowych kontenerowych nimi

Nie. Baza danych znajduje się w obliczeniowych maszyn wirtualnych, a nie w kontenerze.

## <a name="performance-questions"></a>Pytania dotyczące wydajności

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Jakiej przepływności można wypchnąć na największą obliczeń bazy danych SQL na dużą skalę

Nie są jeszcze znane (nadal w wersji zapoznawczej)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Ile operacji We/Wy jest zgłaszany w usłudze obliczeniowej największej bazy danych SQL na dużą skalę

Nie są jeszcze znane (nadal w wersji zapoznawczej)

### <a name="does-my-throughput-get-affected-by-backups"></a>Moje przepływność Pobierz wpływ tworzenia kopii zapasowych

Nie. Obliczeń jest całkowicie niezależna od warstwy magazynu, aby uniknąć wpływu na obliczeń.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Moje przepływności Pobierz dotyczy problem, jak aprowizować dodatkowych węzłów obliczeniowych

Ponieważ Magazyn jest udostępniana i nie ma bezpośredniego fizycznych replikacji wykonywane między węzłami obliczeniowymi podstawowego i pomocniczego, technicznie rzecz biorąc, przepływność w węźle podstawowym dotyczy dodawania węzłów skalę odczytywania. Jednak może ograniczać ciągłego obciążenia agresywne do Zezwalaj na logowanie się na dodatkowych węzłach, a serwery na stronie nadążyć i uniknąć zły wydajność odczytu dodatkowych węzłów.

## <a name="scalability-questions"></a>Pytania dotyczące skalowalności

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Jak długo będą potrzebne do skalowania węzłów obliczeniowych w górę i w dół

Kilka minut

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Jest Moja baza danych w trybie offline podczas skalowania w górę/dół operacja jest w toku

Nie. Skalowanie w górę i w dół będą w trybie online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Należy oczekiwać upuszczania połączenia w przypadku operacji skalowania w toku

Skalowanie w górę lub w dół powoduje istniejące połączenia Trwa porzucanie sytuacji trybu failover do węzła obliczeniowego o rozmiarze docelowego. Dodawanie odczytu repliki, nie powoduje połączenia spadnie.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Jest skalowanie w górę i w dół węzły obliczeniowe, automatyczne lub przez użytkownika końcowego wywołać operację

Użytkownik końcowy. Nie są wykonywane automatycznie.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Jest mój `tempb` również rozwijać jak moc obliczeniowa jest skalowana w

Tak. Tymczasowej bazy danych spowoduje automatyczne skalowanie w górę wraz ze wzrostem natężenia zasoby obliczeniowe.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Czy mogę uaktywnić wiele oblicza podstawowego, takich jak system wielu wzorców, gdzie wielu obliczeń głównej głowy może zapewnić wyższy poziom równoczesności

Nie. Tylko do węzła obliczeniowego głównej akceptuje żądania odczytu/zapisu. Węzły obliczeniowe dodatkowej akceptować tylko żądania tylko do odczytu.

## <a name="read-scale-questions"></a>Pytania dotyczące skalowania odczytu

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Ilu węzłów pomocnicza usługa obliczeniowa może aprowizować

W publicznej wersji zapoznawczej możemy utworzyć 2 repliki baz danych na dużą skalę, domyślnie. Jeśli chcesz dopasować liczby replik określa liczbę, zgłoś żądanie pomocy technicznej w witrynie Azure portal.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Jak nawiązać te węzły pomocnicza usługa obliczeniowa

Możesz nawiązać węzły te dodatkowe zasoby obliczeniowe tylko do odczytu, ustawiając `ApplicationIntent` argumentu w ciągu połączenia w celu `read_only`. Wszystkie połączenia oznaczone `read-only` są automatycznie kierowane do jednego z węzłów dodatkowe zasoby obliczeniowe tylko do odczytu.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Można tworzyć dedykowanych punktu końcowego dla repliki skali odczytu

Nie. W publicznej wersji zapoznawczej, możesz łączyć tylko z skali odczytu repliki, określając `ApplicationIntent=ReadOnly`.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>System robi równoważenia obciążenia inteligentne odczytu obciążenia

Nie. W wersji zapoznawczej odczytu tylko obciążenia jest skierowana do losowych repliki skalę odczytywania.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Można czy mogę skalować w górę/dół węzły pomocnicza usługa obliczeniowa, niezależnie od głównej zasoby obliczeniowe

Nie, nie w okresie publicznej wersji zapoznawczej.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Uzyskać innej tymczasowej bazy danych zmiany rozmiaru Moje podstawowe zasobów obliczeniowych i Moje dodatkowych dodatkowych węzłów obliczeniowych

Nie. Twoje `tempdb` jest konfigurowane w oparciu o aprowizacji rozmiar obliczeń, w publicznej wersji zapoznawczej, węzły pomocnicza usługa obliczeniowa to taki sam rozmiar jak podstawowe zasoby obliczeniowe.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Czy mogę dodać węzłów obliczeniowych w indeksach i widoki na mojej maszynie pomocniczej

Nie. Bazy danych na dużą skalę udostępnionego magazynu, co oznacza, że wszystkie zasoby obliczeniowe węzłów, zobacz ten sam tabel, indeksy i widoków. Jeśli chcesz, aby dodatkowe indeksy zoptymalizowane pod kątem operacji odczytu na serwerze pomocniczym — należy dodać je na serwerze podstawowym najpierw.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Ile opóźnienia ma należeć do zakresu od węzła obliczeniowego podstawowego i pomocniczego

Od chwili, gdy transakcja zostaje zatwierdzona na serwerze podstawowym, w zależności od szybkości generowania rekordów dziennika jego może być natychmiastowe lub w milisekundach niski.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat warstwy usług na dużą skalę, zobacz [warstwę usługi na dużą skalę (wersja zapoznawcza)](sql-database-service-tier-hyperscale.md).
