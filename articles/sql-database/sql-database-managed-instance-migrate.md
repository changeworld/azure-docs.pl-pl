---
title: Migracja bazy danych z wystąpienia programu SQL Server do usługi Azure SQL Database — wystąpienie zarządzane | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację bazy danych z wystąpienia programu SQL Server do usługi Azure SQL Database — wystąpienie zarządzane.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 9fe6ab797eaa325ad802702e95f5a0e5b8e4fef4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070427"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migracja wystąpienia programu SQL Server do usługi Azure SQL Database, wystąpienia zarządzanego

W tym artykule dowiesz się o metodach migracji programu SQL Server 2005 lub nowszych wersji do [wystąpienie zarządzane usługi Azure SQL Database](sql-database-managed-instance.md). Aby uzyskać informacje na temat migracji do pojedynczej bazy danych lub elastycznej puli, zobacz [migracja do bazy danych pojedyncze lub zbiorcze](sql-database-cloud-migrate.md). Migracja informacji o migracji z innych platform, zobacz [Przewodnik po migracji bazy danych Azure](https://datamigration.microsoft.com/).

Na wysokim poziomie proces migracji bazy danych wygląda następująco:

![Proces migracji](./media/sql-database-managed-instance-migration/migration-process.png)

- [Ocena zgodności wystąpienia zarządzanego](#assess-managed-instance-compatibility)
- [Wybierz opcję połączenie aplikacji](sql-database-managed-instance-connect-app.md)
- [Wdrażanie w optymalnym rozmiarze wystąpienia zarządzanego](#deploy-to-an-optimally-sized-managed-instance)
- [Wybierz metodę migracji, a następnie przeprowadzić migrację](#select-migration-method-and-migrate)
- [Monitorowanie aplikacji](#monitor-applications)

> [!NOTE]
> Aby przeprowadzić migrację poszczególnych baz danych do pojedynczej bazy danych lub elastycznej puli, zobacz [migracji bazy danych programu SQL Server do usługi Azure SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Ocena zgodności wystąpienia zarządzanego

Najpierw Ustal, czy zarządzane wystąpienia jest zgodne z wymaganiami bazy danych aplikacji. Opcji wdrożenia wystąpienia zarządzanego jest zaprojektowany w celu zapewnienia łatwego lift- and -shift migracji dla większości istniejących aplikacji, które używają programu SQL Server w środowisku lokalnym lub na maszynach wirtualnych. Jednak czasami może być wymagane funkcje lub możliwości, które nie są jeszcze obsługiwane i kosztów wdrożenia obejście tego problemu jest zbyt wysokie.

Użyj [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) do wykrywania potencjalnych problemy ze zgodnością mogących mieć wpływ na funkcjonalność bazy danych w usłudze Azure SQL Database. Program DMA nie obsługuje jeszcze obsługi zarządzane wystąpienia jako lokalizację docelową migracji, ale zalecane do uruchamiania oceny w bazie danych Azure SQL i dokładnie przejrzyj listę zgłoszonych potrafiło i problemy ze zgodnością z dokumentacji produktu. Zobacz [funkcji usługi Azure SQL Database](sql-database-features.md) Aby sprawdzić, czy istnieją niektóre zgłoszone problemy z blokowaniem, które nie blokują w wystąpieniu zarządzanym, ponieważ większość problemów z blokowaniem, co uniemożliwia migracji do usługi Azure SQL Database zostały usunięte z zarządzane wystąpienie. Wystąpienie, funkcji, takich jak zapytania wielu baz danych, transakcje między bazami danych w ramach tego samego wystąpienia, połączonego serwera na inne SQL źródeł, CLR, globalne tabele tymczasowe widoków poziomu wystąpienia, Usługa Service Broker i podobne dostępnych w wystąpieniach zarządzanych.

W przypadku niektórych zgłaszane problemy z blokowaniem, które nie są usuwane przy użyciu opcji wdrożenia wystąpienia zarządzanego, może być konieczne należy wziąć pod uwagę alternatywnych opcji, takich jak [programu SQL Server na maszynach wirtualnych Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Oto kilka przykładów:

- W razie potrzeby bezpośredniego dostępu do systemu operacyjnego lub systemu plików, na przykład instalowanie innych firm lub niestandardowych agentów na tej samej maszyny wirtualnej z programem SQL Server.
- W przypadku ścisłej zależności od funkcji, które nadal nie są obsługiwane, takich jak typu FileStream / FileTable, PolyBase i transakcje dla wielu wystąpień.
- Jeśli bezwzględnie konieczne pozostać na określonej wersji programu SQL Server (2012, na przykład).
- Jeśli wymagania dotyczące obliczeń są znacznie niższe oferuje tego wystąpienia zarządzanego (jeden rdzeń wirtualny, na przykład) i konsolidacji bazy danych nie jest dopuszczalne opcji.

Jeśli program został rozwiązany, wszystkie zidentyfikowane migracja blockers i kontynuowanie migracji do wystąpienia zarządzanego, należy pamiętać, że niektóre zmiany mogą mieć wpływ na wydajność przetwarzania obciążenia:
- Model odzyskiwania pełnego obowiązkowe i regularnego automatycznych harmonogram tworzenia kopii zapasowych mogą mieć wpływ na wydajność obciążeń lub operacje konserwacji/ETL okresowo używane modelu prostego/niepełnym dziennikiem lub zatrzymywać kopii zapasowych na żądanie.
- Różnych konfiguracji poziomu serwera lub bazy danych, takich jak poziomy zgodności lub flag śledzenia
- Nowe funkcje, które są używane, takich jak przezroczyste szyfrowanie bazy danych (TDE) lub automatyczny tryb failover grupy mogą mieć wpływ na użycie procesora CPU i we/wy.

Zarządzany wystąpienia gwarancji 99,99% dostępności nawet w scenariuszach krytycznych, więc obciążenie spowodowane przez te funkcje nie może być wyłączony. Aby uzyskać więcej informacji, zobacz [głównych przyczyn, które mogłyby powodować wydajności programu SQL Server i wystąpienia zarządzanego](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Tworzenie linii bazowej wydajności

Jeśli potrzebujesz porównać wydajność przetwarzania obciążenia w wystąpieniu zarządzanym przy użyciu oryginalnego obciążenia uruchomione na serwerze SQL, należy utworzyć punkt odniesienia wydajności, która będzie służyć do porównania. Parametry, które będziesz potrzebować do mierzenia w ramach wystąpienia programu SQL Server, należą: 
- [Monitoruj użycie procesora CPU w ramach wystąpienia programu SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) i rejestrowania na Średni i szczytowego użycia procesora CPU.
- [Monitorowanie użycia pamięci w ramach wystąpienia programu SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) i określić ilość pamięci używanej przez różne składniki, takie jak pula buforów plan pamięci podręcznej, pula magazynu kolumn [OLTP w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)itp. Ponadto powinien znajdować się wartości średnia i szczytowe użycie licznika wydajności pamięci oczekiwanej długości życia strony.
- Monitorowanie użycia operacji We/Wy dysku przy użyciu wystąpienia programu SQL Server źródła [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) widoku lub [liczniki wydajności](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitoruj obciążenie i zwiększyć wydajność zapytań lub wystąpienia programu SQL Server, sprawdzając dynamicznych widoków zarządzania lub Query Store, w przypadku migracji z wersji programu SQL Server 2016 +. Zidentyfikuj Średni czas trwania i użycie procesora CPU najważniejsze zapytania w obciążenia, aby porównać je za pomocą zapytań, które są uruchomione na wystąpieniu zarządzanym.

> [!Note]
> Jeśli zauważysz wszelkie problemy związane z swoje obciążenia w programie SQL Server, np. wysokie użycie procesora CPU, wykorzystanie pamięci stałej, problemy z bazy danych tempdb lub parametrization należy próbować je rozwiązać wystąpieniu programu SQL Server źródła przed przełączeniem linii bazowej i migracji. Migrowanie wiedzieć, problemy, aby wszystkie nowe migh system spowodować nieoczekiwane wyniki i unieważnić każde porównanie wydajności.

Wyniku tego działania zostanie powinna mieć udokumentowane średnia i wartości szczytowe użycie procesora CPU, pamięci i we/wy w systemie źródła oraz średnią i maksymalny czas trwania i użycie procesora CPU dominującego i najważniejszych kwerend w obciążenia. Te wartości należy użyć później, aby porównać wydajność przetwarzania obciążenia w wystąpieniu zarządzanym przy użyciu punkt odniesienia wydajności obciążeń w źródle programu SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Wdrażanie na optymalny rozmiar wystąpienia zarządzanego

Wystąpienie zarządzane jest przeznaczony dla lokalnych obciążeń, które planuje przenieść do chmury. Wprowadza [nowy model zakupu](sql-database-service-tiers-vcore.md) , zapewnia większą elastyczność przy wyborze odpowiedniego poziomu zasobów dla obciążeń. W środowisku lokalnym masz prawdopodobnie przyzwyczajeni do ustalania rozmiaru te obciążenia za pomocą rdzeni fizycznych i przepustowość we/wy. Model zakupu dla wystąpienia zarządzanego jest na podstawie rdzeni wirtualnych lub "rdzeni wirtualnych," przy użyciu dodatkowego miejsca do magazynowania i we/wy dostępne oddzielnie. Model rdzenia wirtualnego jest prostszy sposób, aby poznać wymagania dotyczące obliczeń w chmurze, a nie za środowiska lokalnego już dziś. Ten nowy model umożliwia utworzenie odpowiedniego rozmiaru docelowego środowiska w chmurze. Ogólne wskazówki, które mogą być pomocne, aby wybrać warstwę odpowiednią usługę i właściwości są opisane poniżej:
- [Monitoruj użycie procesora CPU w ramach wystąpienia programu SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) i wyboru ile mocy zasilania obecnie używasz (przy użyciu dynamicznych widoków zarządzania, SQL Server Management Studio lub innych narzędzi do monitorowania). Możesz aprowizować wystąpienie zarządzane, która jest zgodna z liczbą rdzeni, które są używane w programie SQL Server, mając na uwadze, który może być konieczne można skalować do dopasowania właściwości procesora CPU [charakterystykami maszyny Wirtualnej, w którym zainstalowano wystąpienie zarządzane usługi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics).
- Sprawdź ilość dostępnej pamięci w ramach wystąpienia programu SQL Server, a następnie wybierz [warstwę usługi, która ma pasujące pamięci](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics). Przydaje się do mierzenia przewidywanej strony trwałości wystąpieniu programu SQL Server w celu określenia [potrzebujesz dodatkowej pamięci](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Zmierz opóźnienia operacji We/Wy podsystemu pliku dokonać wyboru między warstw usług ogólnego przeznaczenia i krytyczne dla działania firmy.

Możesz wybrać obliczeniowych i zasobów magazynowania we wdrożeniu czasu, a następnie zmienić je później bez przerwy w działaniu swojej aplikacji za pomocą [witryny Azure portal](sql-database-scale-resources.md):

![ustalanie rozmiaru wystąpienia zarządzanego](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Aby dowiedzieć się, jak utworzyć infrastrukturę sieci wirtualnej i wystąpienia zarządzanego, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Ważne jest, aby zachować swoje docelowej sieci wirtualnej i podsieci zawsze zgodnie z [wymagania dotyczące sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Wszelkie niezgodności mogą uniemożliwić tworzenie nowych wystąpień lub używanie tych systemów, które zostały już utworzone. Dowiedz się więcej o [tworzenie nowych](sql-database-managed-instance-create-vnet-subnet.md) i [konfigurowania istniejących](sql-database-managed-instance-configure-vnet-subnet.md) sieci.

## <a name="select-migration-method-and-migrate"></a>Wybierz metodę migracji, a następnie przeprowadzić migrację

Wystąpienie zarządzane opcji cele użytkownika scenariusze wdrażania wymagających migracji pamięci masowej bazy danych ze środowiska lokalnego lub IaaS bazy danych implementacji. Są one optymalnym wyborem, gdy trzeba lift- and -shift zaplecza aplikacji, które regularnie Użyj poziomu wystąpienia i / lub funkcje między bazami danych. W przypadku tego scenariusza, całe wystąpienie można przenieść do odpowiedniego środowiska na platformie Azure bez konieczności ponownego projektowania Twojej aplikacji.

Aby przenieść wystąpienia programu SQL, musisz starannie zaplanować:

- Migracja wszystkich baz danych, które muszą być wspólnie rozmieszczonych (tymi uruchomionych na tym samym wystąpieniu)
- Migracja obiektów na poziomie wystąpienia, które zależy aplikacja, tym logowania, poświadczenia, operatorów i zadań agenta SQL i wyzwalaczy na poziomie serwera.

Wystąpienie zarządzane to zarządzana usługa, która umożliwia delegowanie niektórych działań DBA regularne do platformy, jak są wbudowane. W związku z tym, niektórych danych na poziomie wystąpienia nie należy migrować, np. zadań konserwacyjnych regularnie Twórz kopie zapasowe lub konfiguracji zawsze włączonej jako [wysokiej dostępności](sql-database-high-availability.md) jest wbudowany.

Wystąpienie zarządzane obsługuje następujące opcje migracji bazy danych (obecnie są to tylko migracja obsługiwanych metod):

- Azure Database Migration Service — migracji prawie bez przestojów
- Natywne `RESTORE DATABASE FROM URL` - używa natywne kopie zapasowe z programu SQL Server i wymaga pewnych przestojów.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Usługi Azure Database Migration Service (DMS)](../dms/dms-overview.md) to w pełni zarządzana usługa ustalono, aby umożliwić bezproblemowe migracje z wielu źródłowych baz danych do platformy danych Azure przy minimalnych przestojach. Ta usługa usprawnia zadania wymagane do przenoszenia istniejących innych firm i baz danych programu SQL Server na platformie Azure. Opcje wdrożenia w publicznej wersji zapoznawczej obejmują bazy danych w bazach danych Azure SQL Database i programu SQL Server w maszynie wirtualnej platformy Azure. Usługa DMS jest zalecaną metodą migracji obciążenia przedsiębiorstwa.

Jeśli używasz programu SQL Server Integration Services (SSIS) na program SQL Server w środowisku lokalnym, usługa DMS nie obsługuje jeszcze Migrowanie wykazu usług SSIS (SSISDB), która przechowuje pakietów usług SSIS, ale mogą aprowizować środowiska Azure-SSIS Integration Runtime (IR) w usłudze Azure Data Factory (ADF), których będzie Tworzenie nowej bazy danych SSISDB w zarządzanym wystąpieniu i można następnie wdrożyć ponownie pakietów, zobacz [tworzenie środowiska Azure-SSIS IR w usłudze ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Aby dowiedzieć się więcej na temat tego scenariusza i konfiguracji kroki przez usługę DMS, zobacz [migracji lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Natywne przywracania z adresu URL

Przywróć natywnego wykonywania kopii zapasowych (pliki z rozszerzeniem bak) pobrane z lokalnego programu SQL Server lub [programu SQL Server na maszynach wirtualnych](https://azure.microsoft.com/services/virtual-machines/sql-server/), która jest dostępna na [usługi Azure Storage](https://azure.microsoft.com/services/storage/), jest jednym z kluczowych możliwości wdrożenia wystąpienia zarządzanego opcja, która umożliwia szybkie i łatwe w trybie offline bazy danych migracji.

Na poniższym diagramie przedstawiono ogólny przegląd procesu:

![procedury migracji](./media/sql-database-managed-instance-migration/migration-flow.png)

Poniższa tabela zawiera więcej informacji dotyczących metod, których można używać w zależności od wersji programu SQL Server dla źródła, które są uruchomione:

|Krok|Aparat SQL i wersji|Tworzenie kopii zapasowej / Przywracanie — metoda|
|---|---|---|
|Umieść tworzenie kopii zapasowych w usłudze Azure Storage|Pakietu CU2 wcześniejsze SQL 2012 z dodatkiem SP1|Przekaż plik bak bezpośrednio do usługi Azure storage|
||PAKIETU W CU2 2012 Z DODATKIEM SP1 — 2016|Bezpośrednie kopii zapasowej przy użyciu przestarzałe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) składni|
||2016 i nowsze wersje|Przy użyciu kopii zapasowej bezpośredniego [przy użyciu POŚWIADCZEŃ sygnatury dostępu Współdzielonego](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Przywróć z usługi Azure storage do wystąpienia zarządzanego|[Przywróć z adresu URL przy użyciu POŚWIADCZEŃ sygnatury dostępu Współdzielonego](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Podczas migracji bazy danych chronionych przez [funkcji Transparent Data Encryption](transparent-data-encryption-azure-sql.md) do wystąpienia zarządzanego przy użyciu opcji przywracania natywnych, odpowiedni certyfikat z wdrożenia lokalne czy IaaS programu SQL Server muszą zostać zmigrowane przed bazy danych Przywracanie. Aby uzyskać szczegółowe instrukcje, zobacz [certyfikatu TDE migracji do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md)
> - Przywracanie bazy danych systemu nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywane w bazach danych master i msdb), zaleca się ich skryptu i uruchamianie skryptów T-SQL w wystąpieniu docelowym.

Aby uzyskać szybki start przedstawiający sposób przywracania kopii zapasowej bazy danych do wystąpienia zarządzanego przy użyciu poświadczeń sygnatury dostępu Współdzielonego, zobacz [Przywracanie z kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorowanie aplikacji

Po zakończeniu migracji do wystąpienia zarządzanego, możesz śledzić zachowanie aplikacji oraz wydajność przetwarzania obciążenia. Ta procedura obejmuje następujące działania:
- [Porównaj wydajność obciążenia uruchomione na wystąpieniu zarządzanym](#compare-performance-with-the-baseline) z [odniesienia wydajności, który został utworzony w źródle programu SQL Server](#create-performance-baseline).
- Stale [monitorować wydajność przetwarzania obciążenia](#monitor-performance) do identyfikowania potencjalnych problemów i poprawy jakości obsługi.

### <a name="compare-performance-with-the-baseline"></a>Porównanie wydajności z linią bazową

Pierwsze działanie, który trzeba wykonać bezpośrednio po pomyślnej migracji jest porównanie wydajności obciążenia przy użyciu punkt odniesienia wydajności obciążeń. Celem tego działania jest upewnij się, że na wydajność obciążeń w wystąpieniu zarządzanemu spełnia Twoje wymagania. 

Migracja bazy danych do wystąpienia zarządzanego przechowuje ustawienia bazy danych i jej oryginalnego poziom zgodności w większości przypadków. Pierwotne ustawienia zostaną zachowane, jeśli jest to możliwe, aby zmniejszyć ryzyko niektóre jakości wydajność w porównaniu ze źródłem programu SQL Server. Jeśli poziom zgodności bazy danych użytkownika był 100 lub wyższym przed migracją, pozostaje taki sam po migracji. Jeśli poziom zgodności bazy danych użytkownika 90 przed migracją w bazie danych uaktualniony poziom zgodności jest ustawiony na 100, czyli poziom najniższą obsługiwany zgodność w wystąpieniu zarządzanym. Poziom zgodności bazy danych systemu jest 140. Ponieważ migracji do wystąpienia zarządzanego faktycznie przeprowadza migrację do najnowszej wersji programu SQL Server Database Engine, należy pamiętać, trzeba ponownie testowanie wydajności obciążenia, aby uniknąć niektórych Zaskakujące problemów z wydajnością.

Jako warunek wstępny upewnij się, że zostały wykonane następujące działania:
- Dopasuj ustawienia w wystąpieniu zarządzanym przy użyciu ustawień z wystąpieniem programu SQL Server, badając różne wystąpienia, bazy danych, temdb ustawień i konfiguracji. Upewnij się, że nie zmieniono ustawień, takich jak poziomy zgodności lub szyfrowania przed uruchomieniem pierwszego porównanie wydajności lub zaakceptować ryzyko, że niektóre nowe funkcje, które zostały włączone mogą mieć wpływ na niektóre zapytania. Aby zmniejszyć ryzyko migracji, należy zmienić poziom zgodności bazy danych dopiero po monitorowania wydajności.
- Implementowanie [magazynu najlepsze rozwiązania przyjęte ogólnego przeznaczenia](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) takich jak wstępnie przydzielanie rozmiar plików w celu uzyskania lepszej wydajności.
- Dowiedz się więcej o [klucza różnice środowiska, które mogłyby spowodować różnice w wydajności między wystąpienia zarządzanego i programu SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) i Identyfikuj zagrożenia, które mogłyby wpłynąć na wydajność.
- Upewnij się, zachowasz włączone Query Store i dostrajania automatycznego wystąpienia zarządzanego. Te funkcje umożliwiają mierzenie wydajności obciążeń i automatycznie rozwiązać potencjalne problemy z wydajnością. Naucz się używać Query Store jako optymalne narzędzie do pobierania informacji o wydajności obciążeń przed i po zmianie poziomu zgodności bazy danych, jak wyjaśniono w [zachować wydajność stabilność podczas uaktualnienia do nowszej wersji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Po przygotowaniu środowiska, który jest porównywalny jak najszerzej ze środowiskiem w środowisku lokalnym, można rozpocząć uruchamianie obciążenia i zmierzyć wydajność. Proces miary powinny zawierać te same parametry, które możesz mierzony [podczas tworzenia punkt odniesienia wydajności miar obciążenie w źródle programu SQL Server](#create-performance-baseline).
W wyniku należy porównać parametrów wydajności z linią bazową i zidentyfikować różnice krytyczne.

> [!NOTE]
> W wielu przypadkach może nie mieć możliwość dokładnie pasujących wydajności dotyczące wystąpienia zarządzanego i programu SQL Server. Wystąpienie zarządzane jest aparatem bazy danych programu SQL Server, ale infrastruktury i konfiguracji o wysokiej dostępności w wystąpieniu zarządzanym może powodować pewne różnice. Można by oczekiwać kilka zapytań będzie szybsze innych może być wolniejsze. Celem porównania jest Sprawdź, czy wydajność obciążeń w wystąpieniu zarządzanym odpowiada wydajności w programie SQL Server (w średni), a następnie Zidentyfikuj istnieją wszystkie zapytania mają krytyczne znaczenie wydajności, które nie odpowiadają oryginalnej wydajność.

Wynik porównania wydajności może być:
- Wydajność obciążeń w wystąpieniu zarządzanym jest wyrównany lub lepsze, wydajność obciążenia w programie SQL Server. W tym przypadku zostały pomyślnie potwierdza, że migracja zakończyła się powodzeniem.
- Większość parametrów wydajności i zapytania w dobrym stanie, z pewnymi wyjątkami za pomocą pogorszenie wydajności pracy obciążenia. W takim przypadku należy zidentyfikować różnice i ich znaczenie. Jeśli istnieją pewne ważne zapytania z pogorszenie wydajności, należy zbadać bazowego planach SQL są zmieniane lub zapytania są już je osiągnęli pewne ograniczenia zasobów. Środki zaradcze można w takim przypadku można zastosować wskazówki zapytania mają krytyczne znaczenie (na przykład poziom zgodności zmienione, narzędzia szacunkowej oceny kardynalności starszej wersji) albo bezpośrednio lub za pomocą przewodniki planu odbudować lub utworzyć statystyki i indeksy, które mogłyby wpłynąć na plany. 
- Większość zapytań jest wolniejsze na wystąpieniu zarządzanym, w porównaniu ze źródłem programu SQL Server. W takim przypadku spróbuj zidentyfikować głównych przyczyn różnica takich jak [osiągnięcia limitu niektórych zasobów]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) , takie jak limity operacji We/Wy, limit pamięci, ograniczanie liczby wywołań dziennika wystąpienia itp. Jeśli nie ma ograniczeń zasobów, które może spowodować, że różnica, spróbuj zmienić poziom zgodności bazy danych lub zmiany ustawień bazy danych, takich jak szacunkowa ocena kardynalności starszej wersji i ponownie uruchom test. Przejrzyj zalecenia podane przy użyciu funkcji widoków Managed Instance lub Query Store identyfikować zapytania, które uwzględniona wydajności.

> [!IMPORTANT]
> Wystąpienie zarządzane jest wbudowana funkcja automatycznego poprawiania planu, który jest domyślnie włączona. Ta funkcja pozwala zagwarantować, że zapytania, które działały prawidłowo w Wklej czy zmniejsza w przyszłości. Upewnij się, że ta funkcja jest włączona oraz czy wykonali obciążeń wystarczająco długi, za pomocą stare ustawienia przed zmianą nowe ustawienia w celu umożliwienia wystąpienia zarządzanego dowiedzieć się więcej o bazowej wydajności i planach.

Zmiana parametrów lub Uaktualnij warstwy usług zapewniające zbiegają się do optymalną konfigurację, dopóki nie zostanie wyświetlony na wydajność obciążeń, który odpowiada Twoim potrzebom.

### <a name="monitor-performance"></a>Monitorowanie wydajności

Gdy są w pełni zarządzana platforma i upewnieniu się, że obciążenie wydajności te same możesz wydajność obciążeń programu SQL Server, należy podjąć korzyści, które są dostarczane automatycznie w ramach usługi SQL Database. 

Nawet wtedy, gdy nie wprowadzasz pewne zmiany w wystąpieniu zarządzanym podczas migracji, istnieje wysokie prawdopodobieństwo, które spowoduje włączenie niektóre z nowych funkcji, gdy pracujesz wystąpienie z zalet najnowszych ulepszeń aparatu bazy danych. Niektóre zmiany są włączone tylko po [poziom zgodności bazy danych został zmieniony](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


Na przykład nie trzeba tworzyć kopie zapasowe w wystąpieniu zarządzanym — usługa wykonuje kopie zapasowe dla Ciebie automatycznie. Już nie musisz martwić o planowania, wykonywania i zarządzanie kopiami zapasowymi. Wystąpienie zarządzane zapewnia możliwość przywracania do dowolnego punktu w czasie w ramach tego przechowywania okresu przy użyciu [punktu w czasie odzyskiwania (Odzyskiwanie)](sql-database-recovery-using-backups.md#point-in-time-restore). Ponadto nie trzeba już martwić się o Konfigurowanie wysokiej dostępności jako [wysokiej dostępności](sql-database-high-availability.md) jest wbudowany.

Aby wzmocnić zabezpieczenia, należy rozważyć użycie [uwierzytelniania usługi Azure Active Directory](sql-database-security-overview.md), [inspekcji](sql-database-managed-instance-auditing.md), [wykrywanie zagrożeń](sql-database-advanced-data-security.md), [zabezpieczenia](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), i [dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Oprócz Zaawansowane zarządzanie i funkcje zabezpieczeń, wystąpienie zarządzane zapewnia zestaw zaawansowanych narzędzi, które mogą ułatwić [monitorowania i dostrajania obciążenia](sql-database-monitor-tune-overview.md). [Usługa Azure SQL analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) pozwala na monitorowanie szerokiej gamy wystąpienia zarządzane przez usługę i scentralizowane monitorowanie dużą liczbą wystąpień i baz danych. [Automatyczne dostrajanie](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) w wystąpieniu zarządzanym stale monitorować wydajność statystyk wykonywania planu SQL i automatycznie rozwiązać problemy z wydajnością zidentyfikowane.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje o wystąpieniach zarządzanych, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Aby uzyskać samouczek, który obejmuje Przywracanie z kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Samouczek przedstawiający podczas migracji przy użyciu usługi DMS zobacz [migracji lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
