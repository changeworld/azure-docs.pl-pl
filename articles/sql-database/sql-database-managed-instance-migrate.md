---
title: Migrowanie z programu SQL Server do wystąpienia zarządzanego
description: Dowiedz się, jak przeprowadzić migrację bazy danych z wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208944"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database

W tym artykule dowiesz się o metodach migracji wystąpienia wersji programu SQL Server 2005 lub nowszej do [wystąpienia zarządzanego usługi Azure SQL Database.](sql-database-managed-instance.md) Aby uzyskać informacje dotyczące migracji do pojedynczej bazy danych lub puli elastycznej, zobacz [Migrowanie do pojedynczej lub puli bazy danych](sql-database-cloud-migrate.md). Aby uzyskać informacje dotyczące migracji z innych platform, zobacz [Przewodnik migracji bazy danych platformy Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Jeśli chcesz szybko uruchomić i wypróbować wystąpienie zarządzane, możesz przejść do [przewodnika Szybki start](sql-database-managed-instance-quickstart-guide.md) zamiast tej strony. 

Na wysokim poziomie proces migracji bazy danych wygląda następująco:

![proces migracji](./media/sql-database-managed-instance-migration/migration-process.png)

- [Oceń zgodność wystąpienia zarządzanego,](#assess-managed-instance-compatibility) w którym należy upewnić się, że nie ma żadnych problemów z blokowaniem, które mogą uniemożliwić migracje.
  - Ten krok obejmuje również tworzenie [planu bazowego wydajności](#create-performance-baseline) w celu określenia użycia zasobów w źródłowym wystąpieniu programu SQL Server. Ten krok jest potrzebny, jeśli chcesz o wdrożyć poprawnie wielkości wystąpienia zarządzanego i sprawdzić, czy wydajność po migracji nie są naruszone.
- [Wybieranie opcji łączności aplikacji](sql-database-managed-instance-connect-app.md)
- [Wdrażanie w przypadku zarządzanych o optymalnym rozmiarze,](#deploy-to-an-optimally-sized-managed-instance) w którym wybierzesz parametry techniczne (liczba owo wirtualnych, ilość pamięci) i warstwę wydajności (krytyczne dla firmy, ogólnego przeznaczenia) wystąpienia zarządzanego.
- [Wybierz metodę migracji i migruj w](#select-migration-method-and-migrate) miejscu migracji baz danych przy użyciu migracji w trybie offline (natywna kopia zapasowa/przywracanie, importowanie/eksportowanie bazy danych) lub migracji online (usługa migracji danych, replikacja transakcyjna).
- [Monitoruj aplikacje,](#monitor-applications) aby upewnić się, że oczekiwano wydajności.

> [!NOTE]
> Aby przeprowadzić migrację pojedynczej bazy danych do pojedynczej bazy danych lub puli [elastycznej, zobacz Migrowanie bazy danych programu SQL Server do bazy danych SQL Database](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Ocena zgodności wystąpienia zarządzanego

Najpierw należy określić, czy wystąpienie zarządzane jest zgodne z wymaganiami bazy danych aplikacji. Opcja wdrażania wystąpienia zarządzanego została zaprojektowana w celu zapewnienia łatwej migracji windy i zmiany dla większości istniejących aplikacji korzystających z programu SQL Server lokalnie lub na maszynach wirtualnych. Jednak czasami może wymagać funkcji lub możliwości, które nie są jeszcze obsługiwane, a koszt wdrożenia obejścia są zbyt wysokie.

Asystent [migracji danych (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) służy do wykrywania potencjalnych problemów ze zgodnością wpływających na funkcjonalność bazy danych w usłudze Azure SQL Database. DMA nie obsługuje jeszcze wystąpienia zarządzanego jako miejsca docelowego migracji, ale zaleca się uruchomienie oceny względem usługi Azure SQL Database i dokładne przejrzenie listy zgłoszonych problemów z parzystością funkcji i zgodnością z dokumentacją produktu. Zobacz [funkcje usługi Azure SQL Database,](sql-database-features.md) aby sprawdzić, czy istnieją pewne zgłoszone problemy z blokowaniem, które nie są blokerami w wystąpieniu zarządzanym, ponieważ większość problemów z blokowaniem uniemożliwiających migrację do usługi Azure SQL Database została usunięta za pomocą wystąpienia zarządzanego. Na przykład funkcje, takie jak kwerendy między bazami danych, transakcje między bazami danych w tym samym wystąpieniu, połączony serwer z innymi źródłami SQL, CLR, globalne tabele tymczasowe, widoki na poziomie wystąpienia, Service Broker i tym podobne są dostępne w wystąpieniach zarządzanych.

Jeśli istnieją zgłaszane problemy z blokowaniem, które nie zostały usunięte z opcją wdrażania wystąpienia zarządzanego, może być konieczne rozważenie alternatywnej opcji, takiej jak [SQL Server na maszynach wirtualnych platformy Azure.](https://azure.microsoft.com/services/virtual-machines/sql-server/) Oto kilka przykładów:

- Jeśli potrzebujesz bezpośredniego dostępu do systemu operacyjnego lub systemu plików, na przykład zainstalować agentów innych firm lub niestandardowych na tej samej maszynie wirtualnej z programem SQL Server.
- Jeśli masz ścisłe zależności od funkcji, które nadal nie są obsługiwane, takich jak FileStream / FileTable, PolyBase i transakcji między wystąpieniami.
- Jeśli absolutnie trzeba pozostać w określonej wersji programu SQL Server (2012, na przykład).
- Jeśli wymagania obliczeniowe są znacznie niższe, że oferuje wystąpienie zarządzane (na przykład jeden rów wirtualny), a konsolidacja bazy danych jest nie do przyjęcia.

Jeśli wszystkie zidentyfikowane blokady migracji i kontynuowanie migracji do wystąpienia zarządzanego, należy pamiętać, że niektóre zmiany mogą mieć wpływ na wydajność obciążenia:
- Obowiązkowy model pełnego odzyskiwania i regularny harmonogram automatycznych kopii zapasowych mogą mieć wpływ na wydajność obciążenia lub akcji konserwacji/ETL, jeśli okresowo używano modelu prostego/rejestrowanego zbiorczo lub zatrzymywane kopie zapasowe na żądanie.
- Różne konfiguracje poziomu serwera lub bazy danych, takie jak flagi śledzenia lub poziomy zgodności
- Nowe funkcje, których używasz, takie jak szyfrowanie przezroczystej bazy danych (TDE) lub grupy automatycznego trybu failover mogą mieć wpływ na użycie procesora CPU i we/wy.

Dostępność wystąpienia zarządzanego gwarantuje dostępność na 99,99%, nawet w scenariuszach krytycznych, więc nie można wyłączyć obciążenia powodowego przez te funkcje. Aby uzyskać więcej informacji, zobacz [główne przyczyny, które mogą powodować inną wydajność na programie SQL Server i wystąpieniu zarządzanym](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Tworzenie planu bazowego wydajności

Jeśli trzeba porównać wydajność obciążenia w wystąpieniu zarządzanym z oryginalnego obciążenia uruchomionego na programie SQL Server, należy utworzyć linię bazową wydajności, która będzie używana do porównania. 

Linia bazowa wydajności to zestaw parametrów, takich jak średnie/maksymalne użycie procesora CPU, średnie/maksymalne opóźnienie we/wy dysku, przepływność, we/wy, średnia/maksymalna długość życia strony, średni maksymalny rozmiar tempdb. Po migracji należy mieć podobne lub nawet lepsze parametry, dlatego ważne jest, aby mierzyć i rejestrować wartości bazowe dla tych parametrów. Oprócz parametrów systemowych należy wybrać zestaw kwerend reprezentatywnych lub najważniejszych zapytań w obciążeniu i zmierzyć minimalny/średni/maksymalny czas trwania, użycie procesora CPU dla wybranych zapytań. Te wartości umożliwiają porównanie wydajności obciążenia uruchomionego w wystąpieniu zarządzanym z oryginalnymi wartościami w źródle programu SQL Server.

Niektóre parametry, które należy zmierzyć w wystąpieniu programu SQL Server są: 
- [Monitoruj użycie procesora CPU w wystąpieniu programu SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) i rejestruj średnie i maksymalne użycie procesora CPU.
- [Monitoruj użycie pamięci w wystąpieniu programu SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) i określ ilość pamięci używanej przez różne składniki, takie jak pula buforów, pamięć podręczna planu, pula magazynu kolumn, [OLTP w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)itp. Ponadto należy znaleźć średnie i szczytowe wartości licznika wydajności pamięci długość życia strony.
- Monitorowanie użycia we/wy dysku w źródłowym wystąpieniu programu SQL Server przy użyciu [liczników](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) widoku dm_io_virtual_file_stats lub [wydajności](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorowanie wydajności obciążenia i zapytań lub wystąpienia programu SQL Server przez badanie widoków zarządzania dynamicznego lub magazynu zapytań w przypadku migracji z wersji programu SQL Server 2016+. Identyfikowanie średniego czasu trwania i użycia procesora CPU najważniejszych zapytań w obciążeniu, aby porównać je z zapytaniami, które są uruchomione w wystąpieniu zarządzanym.

> [!Note]
> Jeśli zauważysz jakikolwiek problem z obciążeniem na sql server, takie jak wysokie użycie procesora CPU, stałe ciśnienie pamięci, tempdb lub parametryzacja problemów, należy spróbować rozwiązać je w wystąpieniu źródłowego programu SQL Server przed podjęciem linii bazowej i migracji. Migracja znać problemy do dowolnego nowego systemu migh spowodować nieoczekiwane wyniki i unieważnić wszelkie porównania wydajności.

W wyniku tego działania należy mieć udokumentowane średnie i szczytowe wartości użycia procesora CPU, pamięci i we/wy w systemie źródłowym, a także średni i maksymalny czas trwania i użycie procesora CPU dominujących i najbardziej krytycznych zapytań w obciążeniu. Te wartości należy użyć później, aby porównać wydajność obciążenia w wystąpieniu zarządzanym z wydajnością wyjściową obciążenia na źródłowym programie SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Wdrażanie w przypadku wystąpienia zarządzanego o optymalnym rozmiarze

Wystąpienie zarządzane jest dostosowane do obciążeń lokalnych, które planują przenieść się do chmury. Wprowadza nowy [model zakupów,](sql-database-service-tiers-vcore.md) który zapewnia większą elastyczność w wyborze odpowiedniego poziomu zasobów dla obciążeń. W świecie lokalnym prawdopodobnie jesteś przyzwyczajony do rozmiaru tych obciążeń przy użyciu rdzeni fizycznych i przepustowości we/wy. Model zakupu dla wystąpienia zarządzanego jest oparty na rdzeniach wirtualnych lub "rdzeniach wirtualnych", z dodatkową pamięcią masową i we/wy dostępnymi oddzielnie. Model vCore jest prostszy sposób, aby zrozumieć wymagania obliczeniowe w chmurze w porównaniu z tym, co używasz lokalnie dzisiaj. Ten nowy model umożliwia odpowiedni rozmiar środowiska docelowego w chmurze. W tym miejscu opisano kilka ogólnych wskazówek, które mogą pomóc w wyborze odpowiedniej warstwy i charakterystyki usługi:
- Na podstawie użycia procesora CPU linii bazowej można aprowizować wystąpienie zarządzane, które odpowiada liczbie rdzeni używanych w programie SQL Server, mając na uwadze, że charakterystyki procesora CPU mogą wymagać skalowania w celu dopasowania [charakterystyki maszyny Wirtualnej, w której jest zainstalowane wystąpienie zarządzane](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Na podstawie użycia pamięci bazowej wybierz [warstwę usługi, która ma odpowiednią pamięć](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). Ilość pamięci nie może być wybrana bezpośrednio, więc należy wybrać wystąpienie zarządzane z ilością kopii wirtualnych, która ma pasującą pamięć (na przykład 5.1 GB/vCore w gen5). 
- Na podstawie punktu odniesienia dla linii bazowej podsystemu plików wybierz między ogólnego przeznaczenia (opóźnienie większe niż 5 ms) i warstwy usług o znaczeniu krytycznym dla firmy (opóźnienie mniejsze niż 3 ms).
- Na podstawie przepływności linii bazowej wstępnie przydzielić rozmiar plików danych lub dziennika, aby uzyskać oczekiwaną wydajność we/wy.

Możesz wybrać zasoby obliczeniowe i magazynowe w czasie wdrażania, a następnie zmienić je później bez wprowadzania przestojów dla aplikacji za pomocą [portalu Azure:](sql-database-scale-resources.md)

![rozmiar wystąpienia zarządzanego](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Aby dowiedzieć się, jak utworzyć infrastrukturę sieci wirtualnej i wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> Ważne jest, aby zachować docelową sieci wirtualnej i podsieci zawsze zgodnie z wymaganiami sieci [wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Wszelkie niezgodności można uniemożliwić tworzenie nowych wystąpień lub przy użyciu tych, które zostały już utworzone. Dowiedz się więcej o [tworzeniu nowych](sql-database-managed-instance-create-vnet-subnet.md) i [konfigurowaniu istniejących](sql-database-managed-instance-configure-vnet-subnet.md) sieci.

## <a name="select-migration-method-and-migrate"></a>Wybierz metodę migracji i migruj

Opcja wdrażania wystąpienia zarządzanego jest przeznaczona dla scenariuszy użytkowników wymagających masowej migracji bazy danych z implementacji bazy danych lokalnych lub IaaS. Są one optymalnym wyborem, gdy trzeba podnieść i przesunąć tylnej części aplikacji, które regularnie używają poziomu wystąpienia i / lub funkcji cross-database. Jeśli jest to twój scenariusz, można przenieść całe wystąpienie do odpowiedniego środowiska na platformie Azure bez konieczności ponownego egoistycznego enia aplikacji.

Aby przenieść wystąpienia SQL, należy dokładnie zaplanować:

- Migracja wszystkich baz danych, które muszą być kolokowane (te uruchomione w tym samym wystąpieniu)
- Migracja obiektów na poziomie wystąpienia, od których zależy aplikacja, w tym logowania, poświadczenia, zadania i operatory agenta SQL oraz wyzwalacze na poziomie serwera.

Wystąpienie zarządzane jest usługą zarządzaną, która umożliwia delegowanie niektórych regularnych działań DBA do platformy, ponieważ są one wbudowane. W związku z tym niektóre dane na poziomie wystąpienia nie muszą być migrowane, takie jak zadania konserwacji dla regularnych kopii zapasowych lub zawsze włączone konfiguracji, jak [wysoka dostępność](sql-database-high-availability.md) jest wbudowana.

Wystąpienie zarządzane obsługuje następujące opcje migracji bazy danych (obecnie są to jedyne obsługiwane metody migracji):

- Usługa migracji bazy danych platformy Azure — migracja z niemal zerowym przestojem,
- Native `RESTORE DATABASE FROM URL` - używa natywnych kopii zapasowych z programu SQL Server i wymaga pewnego przestoju.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Usługa migracji bazy danych Azure (DMS)](../dms/dms-overview.md) to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowej migracji z wielu źródeł bazy danych do platform Azure Data przy minimalnym przestoju. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących baz danych innych firm i programu SQL Server na platformę Azure. Opcje wdrażania w publicznej wersji zapoznawczej obejmują bazy danych w bazie danych SQL Database platformy Azure i bazy danych programu SQL Server na maszynie wirtualnej platformy Azure. DMS jest zalecaną metodą migracji dla obciążeń przedsiębiorstwa.

Jeśli korzystasz z usług SQL Server Integration Services (SSIS) na komputerze SQL Server lokalnie, dms nie obsługuje jeszcze migracji katalogu SSIS (SSISDB), który przechowuje pakiety SSIS, ale można aprowizować środowisko uruchomieniowe integracji platformy Azure-SSIS (IR) w usłudze Azure Data Factory (ADF), który utworzy nowy bazę danych SSISDB w wystąpieniu zarządzanym, a następnie możesz ponownie rozmieszczać pakiety, zobacz Tworzenie usługi [Azure-SSIS IR IR w ADF.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

Aby dowiedzieć się więcej o tym scenariuszu i krokach konfiguracji dla usługi DMS, zobacz [Migrowanie lokalnej bazy danych do wystąpienia zarządzanego przy użyciu usługi DMS.](../dms/tutorial-sql-server-to-managed-instance.md)  

### <a name="native-restore-from-url"></a>Natywne polecenie RESTORE z adresu URL

Przywracanie natywnych kopii zapasowych (.bak plików) pobranych z programu SQL Server lokalnie lub [programu SQL Server na maszynach wirtualnych,](https://azure.microsoft.com/services/virtual-machines/sql-server/)dostępnych w [usłudze Azure Storage](https://azure.microsoft.com/services/storage/), jest jedną z kluczowych możliwości opcji wdrażania wystąpienia zarządzanego, która umożliwia szybką i łatwą migrację bazy danych w trybie offline.

Poniższy diagram zawiera ogólny przegląd procesu:

![przepływ migracji](./media/sql-database-managed-instance-migration/migration-flow.png)

Poniższa tabela zawiera więcej informacji dotyczących metod, których można użyć w zależności od używanej wersji programu SQL Server:

|Krok|Aparat SQL i wersja|Metoda tworzenia kopii zapasowych / przywracania|
|---|---|---|
|Umieszczanie kopii zapasowej w usłudze Azure Storage|Poprzednia funkcja SQL 2012 SP1 CU2|Przekazywanie pliku bak bezpośrednio do magazynu platformy Azure|
||2012 SP1 CU2 - 2016|Bezpośrednia kopia zapasowa przy użyciu przestarzałej składni [z poświadczeniami](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 r. i powyżej|Bezpośrednie tworzenie kopii zapasowych przy użyciu [poświadczeń sygnatury dostępu Współdziel](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Przywracanie z magazynu platformy Azure do wystąpienia zarządzanego|[PRZYWRÓĆ Z ADRESU URL ZA POMOCĄ POŚWIADCZENIA SYGNATURY DOSTĘPU WSPÓŁDZIELONEGO](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Podczas migracji bazy danych chronionej przez [przezroczyste szyfrowanie danych](transparent-data-encryption-azure-sql.md) do wystąpienia zarządzanego przy użyciu opcji przywracania macierzystego odpowiedni certyfikat z lokalnego lub programu IaaS SQL Server musi zostać zmigrowany przed przywróceniem bazy danych. Aby uzyskać szczegółowe kroki, zobacz [Migrowanie certyfikatu TDE do wystąpienia zarządzanego](sql-database-managed-instance-migrate-tde-certificate.md)
> - Przywracanie systemowych baz danych nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywanych w bazach danych wzorca lub msdb), zaleca się ich skryptowanie i uruchamianie skryptów T-SQL w wystąpieniu docelowym.

Aby uzyskać przewodnik Szybki start pokazujący, jak przywrócić kopię zapasową bazy danych do wystąpienia zarządzanego przy użyciu poświadczeń Sygnatury dostępu Współdzielonego, zobacz [Przywracanie z kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorowanie aplikacji

Po zakończeniu migracji do wystąpienia zarządzanego należy śledzić zachowanie aplikacji i wydajność obciążenia. Proces ten obejmuje następujące działania:
- [Porównaj wydajność obciążenia uruchomionego w wystąpieniu zarządzanym](#compare-performance-with-the-baseline) z [linią bazową wydajności utworzoną na źródłowym programie SQL Server](#create-performance-baseline).
- Stale [monitoruj wydajność obciążenia,](#monitor-performance) aby zidentyfikować potencjalne problemy i ulepszenia.

### <a name="compare-performance-with-the-baseline"></a>Porównanie wydajności z linią bazową

Pierwsze działanie, które należy podjąć natychmiast po pomyślnej migracji jest porównanie wydajności obciążenia z wydajnością obciążenia linii bazowej. Celem tego działania jest potwierdzenie, że wydajność obciążenia w wystąpieniu zarządzanym spełnia Twoje potrzeby. 

Migracja bazy danych do wystąpienia zarządzanego zachowuje ustawienia bazy danych i jej oryginalny poziom zgodności w większości przypadków. Oryginalne ustawienia są zachowywane w miarę możliwości w celu zmniejszenia ryzyka niektórych degradacji wydajności w porównaniu do źródła SQL Server. Jeśli poziom zgodności bazy danych użytkowników był 100 lub wyższy przed migracją, pozostaje taki sam po migracji. Jeśli poziom zgodności bazy danych użytkowników był 90 przed migracją, w uaktualnionej bazie danych poziom zgodności jest ustawiony na 100, co jest najniższym obsługiwanym poziomem zgodności w wystąpieniu zarządzanym. Poziom zgodności baz danych systemu wynosi 140. Ponieważ migracja do wystąpienia zarządzanego faktycznie migruje do najnowszej wersji aparatu bazy danych programu SQL Server, należy pamiętać, że należy ponownie przetestować wydajność obciążenia, aby uniknąć niektórych zaskakujących problemów z wydajnością.

Jako warunek wstępny upewnij się, że wykonano następujące działania:
- Wyrównaj ustawienia wystąpienia zarządzanego z ustawieniami ze źródłowego wystąpienia programu SQL Server, badając różne wystąpienia, bazy danych, ustawienia temdb i konfiguracje. Upewnij się, że nie zmieniono ustawień, takich jak poziomy zgodności lub szyfrowanie przed uruchomieniem pierwszego porównania wydajności, lub zaakceptuj ryzyko, że niektóre z nowych funkcji, które zostały włączone, mogą mieć wpływ na niektóre zapytania. Aby zmniejszyć ryzyko migracji, zmień poziom zgodności bazy danych tylko po monitorowaniu wydajności.
- Zaimplementuj [wskazówki dotyczące najlepszych rozwiązań dotyczących magazynu dla ogólnego przeznaczenia,](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) takie jak wstępne przydzielanie rozmiaru plików w celu uzyskania lepszej wydajności.
- Dowiedz się więcej o [kluczowych różnicach w środowisku, które mogą powodować różnice w wydajności między wystąpieniem zarządzanym a programem SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) i identyfikowanie zagrożeń, które mogą mieć wpływ na wydajność.
- Upewnij się, że w włączonym Magazynie zapytań i automatycznym dostrajaniu w wystąpieniu zarządzanym. Te funkcje umożliwiają pomiar wydajności obciążenia i automatyczne rozwiązywanie potencjalnych problemów z wydajnością. Dowiedz się, jak używać Query Store jako optymalnego narzędzia do uzyskiwania informacji o wydajności obciążenia przed i po zmianie poziomu zgodności bazy danych, jak wyjaśniono w [obszarze Zachowaj stabilność wydajności podczas uaktualniania do nowszej wersji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Po przygotowaniu środowiska, które jest porównywalne w miarę możliwości ze środowiskiem lokalnym, można rozpocząć uruchamianie obciążenia i zmierzyć wydajność. Proces pomiaru powinien obejmować te same parametry, które zostały zmierzone [podczas tworzenia wydajności bazowej miar obciążenia na źródłowym](#create-performance-baseline)programie SQL Server .
W rezultacie należy porównać parametry wydajności z linią bazową i zidentyfikować różnice krytyczne.

> [!NOTE]
> W wielu przypadkach nie będzie można uzyskać dokładnie pasujące wydajności na wystąpienie zarządzane i SQL Server. Wystąpienie zarządzane jest aparatem bazy danych programu SQL Server, ale konfiguracja infrastruktury i wysokiej dostępności w wystąpieniu zarządzanym może wprowadzić pewną różnicę. Można oczekiwać, że niektóre kwerendy będą szybsze, podczas gdy inne mogą być wolniejsze. Celem porównania jest sprawdzenie, czy wydajność obciążenia w wystąpieniu zarządzanym odpowiada wydajności na programie SQL Server (średnio) i zidentyfikowanie, czy są jakieś krytyczne zapytania o wydajności, które nie pasują do oryginalnej wydajności.

Wynik porównania wydajności może być:
- Wydajność obciążenia wystąpienia zarządzanego jest wyrównana lub lepsza niż wydajność obciążenia na programie SQL Server. W takim przypadku pomyślnie potwierdzono, że migracja zakończyła się pomyślnie.
- Większość parametrów wydajności i zapytań w obciążeniu pracy działa poprawnie, z pewnymi wyjątkami o obniżonej wydajności. W takim przypadku należy określić różnice i ich znaczenie. Jeśli istnieją niektóre ważne zapytania o obniżonej wydajności, należy zbadać są podstawowe plany SQL zmienione lub kwerendy są uderzanie niektórych limitów zasobów. Łagodzenie w tym przypadku może być zastosowanie niektórych wskazówek dotyczących zapytań krytycznych (na przykład zmieniony poziom zgodności, starszy estymator kardynalności) bezpośrednio lub za pomocą przewodników planu, odbudować lub utworzyć statystyki i indeksy, które mogą mieć wpływ na plany. 
- Większość zapytań jest wolniejsza w przypadku wystąpienia zarządzanego w porównaniu ze źródłowym programem SQL Server. W takim przypadku spróbuj zidentyfikować główne przyczyny różnicy, takie jak [osiągnięcie pewnego limitu zasobów,]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) takich jak limity we/wy, limit pamięci, limit szybkości dziennika wystąpień itp. Jeśli nie ma żadnych limitów zasobów, które mogą powodować różnicę, spróbuj zmienić poziom zgodności bazy danych lub zmienić ustawienia bazy danych, takie jak oszacowanie starszej kardynalności i ponownie uruchomić test. Przejrzyj zalecenia dostarczone przez widoki wystąpienia zarządzanego lub magazynu zapytań, aby zidentyfikować kwerendy, które cofnęła wydajność.

> [!IMPORTANT]
> Wystąpienie zarządzane ma wbudowaną funkcję automatycznej korekcji planu, która jest domyślnie włączona. Ta funkcja zapewnia, że kwerendy, które działały poprawnie w wklejanie nie ulegną degradacji w przyszłości. Upewnij się, że ta funkcja jest włączona i że obciążenie zostało wykonane wystarczająco długo ze starymi ustawieniami przed zmianą nowych ustawień, aby włączyć wystąpienie zarządzane, aby dowiedzieć się więcej o wydajności i planach linii bazowej.

Należy dokonać zmiany parametrów lub uaktualnić warstwy usług, aby zbiegać się do optymalnej konfiguracji, dopóki nie uzyskasz wydajności obciążenia, która odpowiada Twoim potrzebom.

### <a name="monitor-performance"></a>Monitorowanie wydajności

Wystąpienie zarządzane udostępnia wiele zaawansowanych narzędzi do monitorowania i rozwiązywania problemów i należy ich używać do monitorowania wydajności wystąpienia. Niektóre z parametrów, które trzeba by monitorować są:
- Użycie procesora CPU w wystąpieniu w celu określenia, czy liczba rdzeni wirtualnych, które zostały zainicjowane jest odpowiednie dopasowanie dla obciążenia.
- Długość życia strony w wystąpieniu zarządzanym w celu [określenia, czy potrzebujesz dodatkowej pamięci.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)
- Statystyki oczekiwania, `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` takie jak lub które powiedzą, czy masz problemy z we/wy magazynu, zwłaszcza w warstwie ogólnego przeznaczenia, gdzie może być konieczne wstępne przydzielenie plików, aby uzyskać lepszą wydajność we/wy.

## <a name="leverage-advanced-paas-features"></a>Wykorzystaj zaawansowane funkcje PaaS

Gdy jesteś na w pełni zarządzanej platformie i zweryfikowano, że wydajność obciążenia są zgodne z wydajnością obciążenia programu SQL Server, skorzystaj z zalet, które są dostarczane automatycznie w ramach usługi bazy danych SQL. 

Nawet jeśli nie wprowadziliśmy pewnych zmian w wystąpieniu zarządzanym podczas migracji, istnieje duże prawdopodobieństwo, że włączysz niektóre nowe funkcje podczas obsługi wystąpienia, aby skorzystać z najnowszych ulepszeń aparatu bazy danych. Niektóre zmiany są włączone tylko po [zmianie poziomu zgodności bazy danych.](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)


Na przykład nie trzeba tworzyć kopii zapasowych w wystąpieniu zarządzanym — usługa wykonuje kopie zapasowe dla Ciebie automatycznie. Nie musisz już martwić się o planowanie, wykonywanie i zarządzanie kopiami zapasowymi. Wystąpienie zarządzane zapewnia możliwość przywrócenia do dowolnego punktu w czasie w tym okresie przechowywania przy użyciu [punktu w czasie odzyskiwania (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Ponadto nie trzeba się martwić o konfigurowanie wysokiej dostępności, ponieważ jest wbudowana [wysoka dostępność.](sql-database-high-availability.md)

Aby zwiększyć bezpieczeństwo, należy rozważyć użycie [usługi Azure Active Directory Authentication](sql-database-security-overview.md), [Inspekcja,](sql-database-managed-instance-auditing.md) [wykrywanie zagrożeń,](sql-database-advanced-data-security.md) [zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)i [dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Oprócz zaawansowanych funkcji zarządzania i zabezpieczeń, wystąpienie zarządzane udostępnia zestaw zaawansowanych narzędzi, które mogą pomóc [w monitorowaniu i dostrojeniu obciążenia.](sql-database-monitor-tune-overview.md) [Analiza SQL platformy Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) umożliwia monitorowanie dużego zestawu wystąpień zarządzanych i centralizowanie monitorowania dużej liczby wystąpień i baz danych. [Automatyczne dostrajanie](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) w wystąpieniu zarządzanym stale monitoruje wydajność statystyk wykonania planu SQL i automatycznie naprawia zidentyfikowane problemy z wydajnością.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o wystąpieniach zarządzanych, zobacz [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
- Aby zapoznać się z samouczkiem zawierającym przywracanie z kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby uzyskać samouczek przedstawiający migrację przy użyciu dms, zobacz [Migrowanie lokalnej bazy danych do wystąpienia zarządzanego przy użyciu DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
