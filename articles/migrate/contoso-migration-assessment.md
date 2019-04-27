---
title: Ocena obciążeń lokalnych pod kątem migracji firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ocenia jego maszyn lokalnych do migracji na platformę Azure za pomocą usługi Azure Migrate i Data Migration Assistant.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: raynew
ms.openlocfilehash: 4739308d301291bf88e8ae547ba85f9648339c4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681104"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migracja Contoso: Ocena obciążeń lokalnych pod kątem migracji do platformy Azure

W tym artykule Contoso ocenia jego aplikacji rozwiązania SmartHotel360 lokalnych do migracji na platformę Azure.

Ten artykuł jest częścią serii, która dokumentuje, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje i scenariusze wdrożenia są szczegółowo, które ilustrują sposób konfigurowania infrastruktury migracji, ocenić przydatność, korzystając z zasobów lokalnych do migracji, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększanie się stopnia skomplikowania. Artykuły zostaną dodane do serii wraz z upływem czasu.

Artykuł | Szczegóły | Stan
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów w serii. | Dostępne
Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | W tym artykule
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Jej zmigrowaniu aplikacji frontonu, za pomocą usługi Azure Site Recovery. Baza danych aplikacji jest migrowana do bazy danych wystąpienia zarządzanego Azure SQL przy użyciu usługi Azure Database Migration Service. | Dostępne
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360 użyciu usługi Site Recovery do migracji maszyn wirtualnych aplikacji i Database Migration Service migrację bazy danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zostanie ukończona lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso wykonuje migrację swoich aplikacji rozwiązania SmartHotel360 na aplikację internetową platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych. | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzuj Team Foundation Server w usługach Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne


## <a name="overview"></a>Omówienie

Jak Contoso uzna, migracji na platformę Azure, firma chce uzyskać ocenę techniczną i finansową ustalić, czy swoich lokalnych obciążeń są odpowiednie na potrzeby migracji do chmury. W szczególności zespół Contoso chce ocenić zgodność maszyn i baz danych do migracji. Chce oszacować wydajność i koszty związane z zasobami firmy Contoso na platformie Azure.

Aby rozpocząć pracę i lepiej zrozumieć technologiami, Contoso ocenia dwa swoje lokalne aplikacje, podsumowane w poniższej tabeli. Firma ocenia scenariusze migracji tej aplikacji rehost i Refaktoryzacja dla migracji. Dowiedz się więcej o rehosting i refaktoryzacji w [Omówienie migracji Contoso](contoso-migration-overview.md).

Nazwa aplikacji | Platforma | Warstwy aplikacji | Szczegóły
--- | --- | --- | ---
SmartHotel360<br/><br/> (służy do zarządzania wymaganiami podróż firmy Contoso) | Działa na Windows przy użyciu bazy danych programu SQL Server | Dwuwarstwowej aplikacji. Witryny sieci Web frontonu platformy ASP.NET działa na jednej maszynie Wirtualnej (**WEBVM**) i programu SQL Server jest uruchamiany na inną maszynę Wirtualną (**SQLVM**). | Maszyny wirtualne są VMware, uruchomiona na hoście ESXi zarządzanego przez program vCenter Server.<br/><br/> Możesz pobrać przykładową aplikację z [GitHub](https://github.com/Microsoft/SmartHotel360).
osTicket<br/><br/> (Aplikacji działu usług firmy Contoso) | Uruchamiany w systemie Linux/Apache, za pomocą języka PHP MySQL (LAMP) | Dwuwarstwowej aplikacji. Fronton witryny sieci Web PHP działa na jednej maszynie Wirtualnej (**OSTICKETWEB**) i bazy danych MySQL jest uruchamiany na inną maszynę Wirtualną (**OSTICKETMYSQL**). | Aplikacja jest używana przez klienta usługi aplikacji do śledzenia problemów dla pracowników wewnętrznych i zewnętrznych klientów.<br/><br/> Możesz pobrać próbkę z [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Bieżącej architektury

Ten diagram przedstawia bieżącej infrastruktury lokalnej firmy Contoso:

![Bieżący architektura firmy Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Firma Contoso ma jednego głównego centrum danych. Centrum danych znajduje się w mieście z nowego Jorku w wschodnich Stanach Zjednoczonych.
- Firma Contoso ma trzy dodatkowe gałęzie lokalne na terenie Stanów Zjednoczonych.
- Główne centrum danych jest połączony z Internetem za pomocą fiber połączenia Metro Ethernet (500 MB/s).
- Każdej gałęzi jest połączony z Internetem lokalnie przy użyciu klasy biznesowej połączeń przy użyciu tuneli IPsec sieci VPN, wróć do głównego centrum danych. Ustawienia umożliwia całej sieci firmy Contoso do podłączenia trwale i optymalizuje łączności z Internetem.
- Główne centrum danych jest w pełni zwirtualizowany z oprogramowaniem VMware. Firma Contoso ma dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez program vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami. Firma Contoso używa serwerów DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Kontrolery domeny w lokalnej gałęzi, uruchom na serwerach fizycznych.

## <a name="business-drivers"></a>Czynniki biznesowe

Firmy Contoso IT zespół kierowniczy pracował ściśle zintegrowana z partnerami biznesowymi firmy, aby zrozumieć, co firma chce osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie. Co w efekcie zwiększa wykorzystanie w systemach lokalnych i infrastruktury w firmie.
- **Zwiększenie wydajności**: Firma Contoso potrzebuje usunąć niepotrzebne procedur i usprawniać procesy dla jego deweloperów i użytkowników. Potrzeby biznesowe IT do szybkiego i do nie odpadów czas i pieniądze, więc firma może szybciej na wymagania klientów.
- **Zwiększenie elastyczności**:  Contoso IT trzeba zwiększyć szybkość reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany, które występują w witrynie marketplace termin firmy im odnosić sukcesy w globalnej gospodarki reagować. IT w firmie Contoso nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: Jak pomyślnie rozwoju działalności firmy Contoso IT, należy podać systemów, które można rozwijać w tym samym tempie.

## <a name="assessment-goals"></a>Cele oceny

Zespół chmury Contoso zidentyfikowała cele dotyczące jej oceny migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, które aplikacje mają obecnie w środowisku VMWare lokalne firmy Contoso. Przejście do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Firma Contoso potrzebuje do zrozumienia zgodność swojej aplikacji i baz danych z wymaganiami platformy Azure. Firmy Contoso musi zrozumieć jej opcje hostingu platformy Azure.
- Po przeniesieniu aplikacji w chmurze, należy zminimalizować administracji bazą danych firmy Contoso.  
- Firma Contoso chce zrozumienia nie tylko jego opcje migracji, ale także koszty związane z infrastrukturą, po przeniesieniu do chmury.

## <a name="assessment-tools"></a>Narzędzia oceny

Firma Contoso używa narzędzi firmy Microsoft, jej oceny migracji. Narzędzia dostosowanie do celów firmy oraz Contoso powinien podać wszystkie informacje, których potrzebuje.

Technologia | Opis | Koszty
--- | --- | ---
[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Firma Contoso używa Data Migration Assistant do oceny i wykrycia problemów ze zgodnością, które mogłyby wpłynąć na jego działanie bazy danych na platformie Azure. Data Migration Assistant ocenia równoważność funkcji między źródłami SQL i elementy docelowe. Zaleca się wydajności i niezawodności. | Data Migration Assistant to narzędzie bezpłatna, udostępniona do pobrania.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Firma Contoso używa usługi Azure Migrate, aby ocenić jego maszyn wirtualnych VMware. Usługa Azure Migrate ocenia przydatność migracji maszyn. Oferuje ona oszacowania rozmiarów i kosztów, co działających na platformie Azure.  | Od maja 2018 r. Usługa Azure Migrate jest bezpłatna usługa.
[Mapa usługi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Usługa Azure Migrate rozwiązania Service map przedstawia zależności między maszynami, które firma chce przeprowadzić migrację. | Mapa usługi jest częścią dzienniki usługi Azure Monitor. Obecnie firmy Contoso, mogą używać rozwiązania Service Map przez 180 dni bez ponoszenia opłat.

W tym scenariuszu firma Contoso pobiera i uruchamia Data Migration Assistant do oceny lokalnej bazy danych programu SQL Server do swojej aplikacji turystycznej. Firma Contoso używa usługi Azure Migrate z mapowaniem zależności do oceny, maszyny wirtualne aplikacji przed migracją na platformę Azure.

## <a name="assessment-architecture"></a>Architekturę do oceny

![architekturę do oceny migracji](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Firma Contoso ma fikcyjne nazwę, która reprezentuje typowe przedsiębiorstwo.
- Firma Contoso ma lokalne centrum danych (**contoso-datacenter**) i rozwiązań lokalnych kontrolerów domeny (**CONTOSODC1**, **CONTOSODC2**).
- Maszyny wirtualne VMware znajdują się na hostach VMware ESXi z systemem w wersji 6.5 (**contosohost1**, **contosohost2**).
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**uruchomiony na maszynie Wirtualnej).
- Aplikacja podróży rozwiązania SmartHotel360 ma następującą charakterystykę:
    - Aplikacja jest warstwowa między dwiema maszynami wirtualnymi VMware (**WEBVM** i **SQLVM**).
    - Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com**.
    - Maszyny wirtualne działają w systemie Windows Server 2008 R2 Datacenter z dodatkiem SP1.
- Środowisko VMware jest zarządzane przez program vCenter Server (**vcenter.contoso.com**) uruchomiony na maszynie wirtualnej.
- Aplikacja technicznej osTicket usługi:
    - Aplikacja jest warstwowa między dwiema maszynami wirtualnymi (**OSTICKETWEB** i **OSTICKETMYSQL**).
    - Maszyny wirtualne działają na serwer z systemem Linux Ubuntu 16.04-LTS.
    - **OSTICKETWEB** działa Apache 2 oraz środowisko PHP 7.0.
    - **OSTICKETMYSQL** działa MySQL 5.7.22.

## <a name="prerequisites"></a>Wymagania wstępne

Contoso i inni użytkownicy muszą spełniać następujące wymagania wstępne dla oceny:

- Uprawnienia właściciela lub współautora do subskrypcji platformy Azure lub dla grupy zasobów w subskrypcji platformy Azure.
- Wystąpienie serwera vCenter lokalnej wersji 6.5, 6.0 lub 5.5.
- Konto tylko do odczytu w programie vCenter Server lub uprawnienia, aby go utworzyć.
- Uprawnienia do tworzenia maszyny Wirtualnej w wystąpieniu programu vCenter Server przy użyciu szablonu .ova.
- Co najmniej jeden host ESXi w wersji 5.5 lub nowszej.
- Co najmniej dwie lokalne maszyny wirtualne VMware, w tym jedna z uruchomioną bazą danych programu SQL Server.
- Uprawnienia do instalowania agentów usługi Azure Migrate na każdej maszynie Wirtualnej.
- Maszyny wirtualne powinny mieć bezpośrednie połączenie z Internetem.  
    - Można ograniczyć dostęp do Internetu [wymaganych adresów URL](https://docs.microsoft.com/azure/migrate/concepts-collector).  
    - Jeśli Twoje maszyny wirtualne nie ma łączności z Internetem, Azure [brama usługi Log Analytics](../azure-monitor/platform/gateway.md) należy zainstalować na nich i agenta ruch kierowany przez nią.
- Nazwa FQDN maszyny wirtualnej z uruchomionym wystąpieniem programu SQL Server, używana do oceny bazy danych.
- Zapora Windows uruchomione na maszynie Wirtualnej programu SQL Server powinna zezwalać na połączenia zewnętrzne na porcie TCP 1433 (domyślnym). Ta konfiguracja umożliwia Data Migration Assistant połączyć.

## <a name="assessment-overview"></a>Przegląd oceny

Poniżej przedstawiono, jak Contoso wykonuje oceny:

> [!div class="checklist"]
> * **Krok 1. Pobierz i zainstaluj Data Migration Assistant**: Contoso przygotowuje Data Migration Assistant dla oceny lokalnej bazy danych programu SQL Server.
> * **Krok 2. Ocena bazy danych przy użyciu Data Migration Assistant**: Firmy Contoso jest uruchamiany i analizowanie oceny bazy danych.
> * **Krok 3. Przygotowywanie oceny maszyn wirtualnych przy użyciu usługi Azure Migrate**: Contoso konfiguruje lokalnych kont i dostosowanie ustawień programu VMware.
> * **Krok 4. Odnajdywanie lokalnych maszyn wirtualnych przy użyciu usługi Azure Migrate**: Contoso tworzy usługę Azure Migrate maszyna wirtualna modułu zbierającego. Następnie firmy Contoso jest uruchamiany moduł zbierający w celu odnalezienia maszyn wirtualnych do oceny.
> * **Krok 5. Przygotowywanie analizy zależności za pomocą usługi Azure Migrate**: Firma Contoso instaluje agentów usługi Azure Migrate na maszynach wirtualnych, dzięki czemu firma może zobaczyć mapowanie zależności między maszynami wirtualnymi.
> * **Krok 6: Oceny maszyn wirtualnych przy użyciu usługi Azure Migrate**: Contoso sprawdza zależności, grupy maszyn wirtualnych i uruchamia oceny. Gdy ocena będzie gotowa, Contoso analizuje oceny w ramach przygotowania do migracji.

## <a name="step-1-download-and-install-data-migration-assistant"></a>Krok 1: Pobierz i zainstaluj Asystenta migracji danych

1. Contoso — pliki do pobrania Data Migration Assistant z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Data Migration Assistant można zainstalować na dowolnym komputerze mającym połączenie z wystąpieniem programu SQL Server. Contoso — nie trzeba go uruchamiać na komputerze z serwerem SQL.
    - Data Migration Assistant nie należy uruchamiać na maszynie hosta programu SQL Server.
2. Contoso uruchamia pobrany plik Instalatora (DownloadMigrationAssistant.msi), aby rozpocząć instalację.
3. Na **Zakończ** stronie Contoso wybiera **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>Krok 2: Uruchamianie i analizowanie oceny bazy danych dla rozwiązania SmartHotel360

Contoso można teraz uruchomić ocenę, aby przeanalizować jej bazy danych SQL Server w środowisku lokalnym aplikacji rozwiązania SmartHotel360.

1. W Data Migration Assistant wybiera Contoso **New** > **oceny**i umożliwia nadanie oceny nazwy projektu.
2. Dla **typ serwera źródła**, wybiera Contoso **programu SQL Server na maszynach wirtualnych Azure**.

    ![Data Migration Assistant — wybierz źródło](./media/contoso-migration-assessment/dma-assessment-1.png)

   > [!NOTE]
   >    Obecnie usługa Data Migration Assistant nie obsługuje oceny migracji do wystąpienia zarządzanego bazy danych SQL Azure. Jako obejście, firma Contoso używa programu SQL Server na Maszynie wirtualnej platformy Azure jako tymczasowego obiektu docelowego dla oceny.

3. W **Wybierz docelową wersję**, Contoso wybiera programu SQL Server 2017 w wersji docelowej. Firma Contoso potrzebuje można wybrać tę wersję, ponieważ jest to wersja, który jest używany przez wystąpienie zarządzane usługi SQL Database.
4. Contoso wybiera raportów, aby pomóc ją znaleźć informacje o zgodności oraz nowe funkcje:
   - **Problemy ze zgodnością** należy pamiętać, zmiany, które mogą uniemożliwić migrację lub które wymagają drobnych korekt przed migracją. Ten raport zapewnia Contoso poinformowany o funkcjach aktualnie w użyciu, które są przestarzałe. Problemy są uporządkowane według poziomu zgodności.
   - **Rekomendowane nowe funkcje** informacje o nowych funkcjach w docelowej platformy SQL Server, który może służyć do bazy danych po zakończeniu migracji. Nowych rekomendowanych funkcji są zorganizowane w nagłówki **wydajności**, **zabezpieczeń**, i **magazynu**.

     ![Data Migration Assistant — problemy ze zgodnością i nowe funkcje](./media/contoso-migration-assessment/dma-assessment-2.png)

2. W **nawiązywanie połączenia z serwerem**, Contoso Wstawia nazwę maszyny Wirtualnej, która jest uruchomiona, bazy danych oraz poświadczenia, aby uzyskać do niego dostęp. Wybiera contoso **certyfikat serwera zaufania** się upewnić, że maszyna wirtualna może dostęp do serwera SQL. Następnie wybiera Contoso **Connect**.

    ![Data Migration Assistant — połączenie z serwerem](./media/contoso-migration-assessment/dma-assessment-3.png)

3. W **Dodaj źródło**, Contoso dodaje chce ocenić bazy danych, a następnie wybiera **dalej** Aby uruchomić ocenę.
4. Ocena jest tworzony.

    ![Data Migration Assistant — Utwórz ocenę](./media/contoso-migration-assessment/dma-assessment-4.png)

5. W **wyników przeglądu**, Contoso wyświetla wyniki oceny.

### <a name="analyze-the-database-assessment"></a>Analizowanie oceny bazy danych

Wyniki są wyświetlane, gdy są one dostępne. Jeśli Contoso rozwiązuje problemy, należy wybrać **Uruchom ponownie ocenę** ponownie uruchomić ocenę.

1. W **problemy ze zgodnością** zgłosić, Contoso sprawdza, czy wszystkie problemy na każdym poziomie zgodności. Poziomy zgodności są mapowane na wersje programu SQL Server w następujący sposób:

   - 100: SQL Server 2008/Azure SQL Database
   - 110: SQL Server 2012/Azure SQL Database
   - 120: SQL Server 2014/Azure SQL Database
   - 130: SQL Server 2016/Azure SQL Database
   - 140: SQL Server 2017/Azure SQL Database

     ![Data Migration Assistant — raport problemy ze zgodnością](./media/contoso-migration-assessment/dma-assessment-5.png)

2. W **Rekomendowaną** zgłosić, Contoso widoków wydajności, zabezpieczeń i magazynu funkcji, jakie po migracji. Różne funkcje, zaleca się w tym OLTP w pamięci indeksów magazynu kolumn, Stretch Database, Always Encrypted, dynamiczne maskowanie danych i niewidoczne szyfrowanie danych.

    ![Data Migration Assistant — raport zalecenia dotyczące funkcji](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Firmy Contoso powinien [Włączanie technologii transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) dla wszystkich baz danych programu SQL Server. Jest to jeszcze bardziej istotne, gdy baza danych znajduje się w chmurze niż gdy hostowanych lokalnie. Przezroczyste szyfrowanie danych powinno być włączone tylko po zakończeniu migracji. Jeśli przezroczyste szyfrowanie danych jest już włączony, Contoso przenieść certyfikatu lub klucza asymetrycznego z główną bazą danych serwera docelowego. Dowiedz się, jak [przenieść bazę danych chronione przez szyfrowanie danych do innego wystąpienia programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Contoso można wyeksportować oceny w formacie JSON lub CSV.

> [!NOTE]
> Aby na dużą skalę oceny:
> - Uruchamiać wiele ocen jednocześnie i wyświetlać stan oceny na **wszystkich ocen** strony.
> - Konsolidować oceny w [bazy danych programu SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017).
> - Konsolidować oceny w [raportu usługi Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>Krok 3: Przygotowywanie oceny maszyn wirtualnych przy użyciu usługi Azure Migrate

Firma Contoso potrzebuje do utworzenia konta VMware, usługa Azure Migrate można użyć do automatycznego wykrywania maszyn wirtualnych do oceny, sprawdź uprawnienia, aby utworzyć Maszynę wirtualną, zwróć uwagę na porty, które muszą być otwarte i ustaw ustawienia poziomu statystyk.

### <a name="set-up-a-vmware-account"></a>Konfigurowanie konta VMware

Odnajdywanie maszyny Wirtualnej wymaga konta tylko do odczytu w programie vCenter Server, który ma następujące właściwości:

- **Typ użytkownika**: Co najmniej użytkownik tylko do odczytu.
- **Uprawnienia**: Obiekt centrum danych, można wybrać **Propagacja do obiektu podrzędnego** pola wyboru. Aby uzyskać **roli**, wybierz opcję **tylko do odczytu**.
- **Szczegóły**: Użytkownik został przypisany na poziomie centrum danych, dostęp do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).

### <a name="verify-permissions-to-create-a-vm"></a>Weryfikowanie uprawnień do utworzenia maszyny wirtualnej

Sprawdza firmy Contoso, ma uprawnienia do tworzenia maszyny Wirtualnej, importując plik w formacie .ova. Dowiedz się, jak [utworzenie i przypisanie roli z uprawnieniami](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Weryfikowanie portów

Ocena Contoso używa mapowanie zależności. Mapowanie zależności wymaga agenta do zainstalowania na maszynach wirtualnych, które zostaną ocenione. Agent musi być możliwe nawiązanie połączenia na platformie Azure przez port TCP 443 na każdej maszynie Wirtualnej. Dowiedz się więcej o [wymagań dotyczących połączenia](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).

### <a name="set-statistics-settings"></a>Ustawianie statystyk

Przed rozpoczęciem wdrażania Contoso go ustawić ustawienia statystyk programu vCenter Server na poziom 3.

> [!NOTE]
> - Po ustawieniu poziomu, Contoso musi poczekać co najmniej jeden dzień przed uruchomieniem oceny. W przeciwnym razie oceny może nie działać zgodnie z oczekiwaniami.
> - Jeśli zostanie ustawiony poziom wyższy niż 3, ocena działa, ale:
>    - Nie zostaną zebrane dane dotyczące wydajności dysków ani sieci.
>    - W przypadku magazynu usługa Azure Migrate zaleca standardowy dysk na platformie Azure, o takim samym rozmiarze co dysk lokalny.
>    - W przypadku sieci dla każdej karty sieciowej w środowisku lokalnym karty sieciowej jest zalecane na platformie Azure.
>    - Dla środowiska obliczeniowego usługa Azure Migrate analizuje rdzeni maszyn wirtualnych i rozmiaru pamięci i zaleca Maszynie wirtualnej platformy Azure z taką samą konfiguracją. Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.
> - Aby uzyskać więcej informacji na temat zmiany rozmiaru przy użyciu poziomu 3 zobacz [zmiany rozmiaru](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

Aby ustawić poziom:

1. W kliencie internetowym vSphere firmy Contoso otwiera wystąpienie programu vCenter Server.
2. Wybiera contoso **Zarządzaj** > **ustawienia** > **ogólne** > **Edytuj**.
3. W **statystyki**, Contoso ustawia dane statystyczne ustawienia poziomu **Level 3**.

    ![poziomu statystyk serwera vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>Krok 4: Odnajdywanie maszyn wirtualnych

Do odnalezienia maszyn wirtualnych, Contoso utworzy projekt usługi Azure Migrate. Contoso pobiera i ustawia maszyna wirtualna modułu zbierającego. Następnie firmy Contoso jest uruchamiany moduł zbierający Aby odnaleźć swoich lokalnych maszyn wirtualnych.

### <a name="create-a-project"></a>Tworzenie projektu

1. W [witryny Azure portal](https://portal.azure.com), wyszukuje Contoso **usługi Azure Migrate**. Następnie Contoso tworzy projekt.
2. Określa nazwę projektu, Contoso (**ContosoMigration**) i subskrypcję platformy Azure. Tworzy nową grupę zasobów platformy Azure (**ContosoFailoverRG**).
    > [!NOTE]
    > - Projekt usługi Azure Migrate można utworzyć tylko w zachodnio-środkowe stany USA lub w regionie wschodnie stany USA.
    > - Migrację możesz zaplanować dla dowolnej lokalizacji docelowej.
    > - Lokalizacja projektu jest używany tylko do przechowywania metadanych, które są zbierane z maszyn wirtualnych w środowisku lokalnym.

    ![Azure migracji — Tworzenie projektu migracji](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę Wirtualną, nazywaną *urządzenia modułu zbierającego*. Maszyna wirtualna umożliwia odnalezienie lokalnych maszyn wirtualnych z programu VMware i wysyła metadane dotyczące maszyn wirtualnych do usługi Azure Migrate. Aby skonfigurować urządzenie modułu zbierającego, Contoso pobiera szablon OVA i następnie importuje go do wystąpienia serwera vCenter w środowisku lokalnym, aby utworzyć maszynę Wirtualną.

1. W projekcie usługi Azure Migrate wybiera Contoso **wprowadzenie** > **Odnajdź i oceń** > **odnajdź maszyny**. Contoso pobiera plik szablonu OVA.
2. Contoso kopiuje identyfikator i klucz projektu. Projekt i identyfikator są wymagane do skonfigurowania modułu zbierającego.

    ![Usługa Azure Migrate — pobieranie pliku OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Przed wdrożeniem maszyny Wirtualnej, Contoso sprawdza, czy plik OVA bezpiecznego:

1. Na komputerze, na którym plik został pobrany Contoso, zostanie otwarte okno wiersza polecenia administratora.
2. Contoso uruchamia następujące polecenie, aby wygenerować skrót pliku OVA:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **Przykład**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Wygenerowany skrót powinien odpowiadać wartości skrótów na liście [tutaj](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#continuous-discovery).

### <a name="create-the-collector-appliance"></a>Tworzenie urządzenia modułu zbierającego

Teraz firmy Contoso, można zaimportować pobrany plik z wystąpieniem programu vCenter Server i aprowizować urządzenia modułu zbierającego maszyny Wirtualnej:

1. W konsoli klienta vSphere wybiera Contoso **pliku** > **wdrażania szablonu OVF**.

    ![Klient sieci Web — szablon OVF wdrażanie vSphere](./media/contoso-migration-assessment/vcenter-wizard.png)

2. W Kreatorze wdrażania szablonu OVF wybiera Contoso **źródła**, a następnie określa lokalizację pliku OVA.
3. W **nazwę i lokalizację**, Contoso Określa nazwę wyświetlaną dla maszyny Wirtualnej modułu zbierającego. Następnie wybiera lokalizację magazynu, w której będzie hostem maszyny Wirtualnej. Contoso określa również, host lub klaster, na którym ma działać urządzenie modułu zbierającego.
4. W **magazynu**, Contoso Określa lokalizację magazynu. W **Format dysku**, Contoso wybiera, jaki chce aprowizacji magazynu.
5. W **mapowanie sieci**, Contoso określa sieci, w której chcesz połączyć maszyny Wirtualnej modułu zbierającego. Sieć musi mieć łączność z Internetem w celu wysyłania metadanych do platformy Azure.
6. Contoso przeglądy ustawienia, a następnie wybiera **Włącz po wdrożeniu** > **Zakończ**. Po utworzeniu urządzenia, zostanie wyświetlony komunikat potwierdzający pomyślne zakończenie.

### <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Teraz Contoso jest uruchamiany moduł zbierający w celu odnalezienia maszyn wirtualnych. Aktualnie moduł zbierający obsługuje obecnie tylko **angielski (Stany Zjednoczone)** jako język systemu operacyjnego i język interfejsu modułu zbierającego.

1. W konsoli klienta vSphere wybiera Contoso **Otwórz konsolę**. Contoso określa język, strefa czasowa i hasło preferencje dotyczące maszyny Wirtualnej modułu zbierającego.
2. Na komputerze stacjonarnym, wybiera Contoso **uruchom moduł zbierający** skrótów.

    ![konsoli klienta vSphere — skrót do modułu zbierającego](./media/contoso-migration-assessment/collector-shortcut.png)

3. W usłudze Azure Migrate Collector wybiera Contoso **Skonfiguruj wymagania wstępne**. Contoso akceptuje postanowienia licencyjne i odczytuje informacje dotyczące innych firm.
4. Moduł zbierający sprawdzi, czy maszyna wirtualna ma dostęp do Internetu, czy jest zsynchronizowany czas oraz czy Usługa modułu zbierającego jest uruchomiona. (Usługa modułu zbierającego jest instalowany domyślnie na maszynie Wirtualnej). Firma Contoso instaluje również interfejs PowerCLI programu VMware.

    > [!NOTE]
    > Zakłada się, że maszyna wirtualna ma bezpośredni dostęp do Internetu bez użycia serwera proxy.

    ![Usługa Azure Migrate modułu zbierającego — Sprawdź wymagania wstępne](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. W **Określ szczegóły serwera vCenter**poświadczeń tylko do odczytu używane na potrzeby odnajdywania i Contoso wprowadza nazwę (FQDN) lub adres IP wystąpienia serwera vCenter.
6. Contoso wybiera zakres odnajdowania maszyn wirtualnych. Moduł zbierający może odnajdywać tylko te maszyny wirtualne, które znajdują się w określonym zakresie. Można ustawić zakresu do określonego folderu, w centrum danych lub w klastrze. Zakres nie powinien zawierać więcej niż 1500 maszyn wirtualnych.

    ![Określanie szczegółów programu vCenter Server](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. W **określ projekt migracji**, firmy Contoso przechodzi z usługi Azure Migrate identyfikator i klucz projektu, które zostały skopiowane z portalu. Aby uzyskać identyfikator i klucz projektu, przejść do projektu Contoso **Przegląd** strony > **odnajdź maszyny**.  

    ![Określanie projektu migracji](./media/contoso-migration-assessment/collector-connect-azure.png)

8. W **Wyświetl postęp zbierania**, Contoso można monitorować proces odnajdowania i sprawdzić, że metadane zbierane z maszyn wirtualnych znajduje się w zakresie. Moduł zbierający informuje o szacowanym czasie odnajdowania.

    ![Wyświetl postęp zbierania](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu zbierania Contoso sprawdza, czy maszyny wirtualne są wyświetlane w portalu:

1. W projekcie usługi Azure Migrate wybiera Contoso **Zarządzaj** > **maszyn**. Contoso sprawdza, czy maszyny wirtualne, które chce, aby dowiedzieć się, są wyświetlane.

    ![Usługa Azure Migrate - odnalezione maszyny](./media/contoso-migration-assessment/discovery-complete.png)

2. Obecnie maszyny nie mają zainstalowanych agentów usługi Azure Migrate. Firmy Contoso jest zainstalowanie agentów, aby wyświetlić zależności.

    ![Usługa Azure Migrate — wymagana jest instalacja agenta](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>Krok 5. Przygotowywanie analizy zależności

Aby wyświetlić zależności między maszynami wirtualnymi, które chce ocenić, Contoso pobiera i instaluje agentów na maszynach wirtualnych aplikacji. Firma Contoso instaluje agentów na wszystkich maszynach wirtualnych w przypadku aplikacji, zarówno dla Windows i Linux.

### <a name="take-a-snapshot"></a>Tworzenie migawki

Aby zachować kopię maszyn wirtualnych przed ich zmodyfikowaniem, Contoso tworzy migawkę przed zainstalowanych agentów.

![Migawka maszyny](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

1. W **maszyn**, Contoso wybiera maszyny. W **zależności** wybiera Contoso kolumnie **wymaga instalacji**.
2. W **odnajdź maszyny** okienku Contoso:
    - Pobieranie programu Microsoft Monitoring Agent (MMA) i agenta zależności dla poszczególnych maszyn wirtualnych Windows.
    - Pobiera MMA i agenta zależności dla każdej maszyny Wirtualnej systemu Linux.
3. Contoso kopiuje klucz i identyfikator obszaru roboczego. Firma Contoso potrzebuje klucz i identyfikator obszaru roboczego podczas instalacji programu MMA.

    ![Pobieranie agenta](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Zainstaluj agentów na maszynach wirtualnych Windows

Contoso uruchamia instalację na każdej maszynie Wirtualnej.

#### <a name="install-the-mma-on-windows-vms"></a>Zainstaluj program MMA na maszynach wirtualnych Windows

1. Contoso kliknie dwukrotnie pobranego agenta.
2. W **Folder docelowy**, Contoso utrzymuje domyślny folder instalacji, a następnie wybiera **dalej**.
3. W **opcje instalacji agenta**, wybiera Contoso **Połącz agenta z usługą Azure Log Analytics** > **dalej**.

    ![Microsoft Monitoring Agent instalacji — opcje instalacji agenta](./media/contoso-migration-assessment/mma-install.png)

4. W **usługi Azure Log Analytics**, Contoso wkleja identyfikator obszaru roboczego i klucz, który go skopiowane z portalu.

    ![Microsoft Monitoring Agent Setup - usługi Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. W **gotowy do instalacji**, firma Contoso instaluje programu MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Zainstaluj agenta zależności na maszynach wirtualnych Windows

1. Contoso kliknie dwukrotnie pobranego agenta zależności.
2. Contoso akceptuje postanowienia licencyjne i czeka na zakończenie instalacji.

    ![Konfigurowanie agenta zależności — instalowanie](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Instalowanie agentów na maszynach wirtualnych z systemem Linux

Contoso uruchamia instalację na każdej maszynie Wirtualnej.

#### <a name="install-the-mma-on-linux-vms"></a>Zainstaluj program MMA na maszynach wirtualnych z systemem Linux

1. Firma Contoso instaluje biblioteki ctypes Python na każdej maszynie Wirtualnej za pomocą następującego polecenia:

    `sudo apt-get install python-ctypeslib`
2. Firmy Contoso, musisz uruchomić polecenie do zainstalowania agenta MMA jako użytkownik główny. Stają się głównym, Contoso uruchamia następujące polecenie, a następnie wprowadź hasło główne:

    `sudo -i`
3. Firma Contoso instaluje programu MMA:
   - Firmy Contoso przechodzi klucz i identyfikator obszaru roboczego w poleceniu.
   - Polecenia są dla 64-bitowych.
   - Identyfikator obszaru roboczego i klucz podstawowy znajdują się w obszarze roboczym usługi Log Analytics w witrynie Azure portal. Wybierz **ustawienia**, a następnie wybierz pozycję **połączone źródła** kartę.
   - Uruchom następujące polecenia, aby pobrać agenta usługi Log Analytics, weryfikacja sumy kontrolnej i zainstalować i dołączyć agenta:

     ```
     wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
     ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instalowanie agenta zależności na maszynach wirtualnych z systemem Linux

Po zainstalowaniu programu MMA firma Contoso instaluje agenta zależności na maszynach wirtualnych systemu Linux:

1. Dependency Agent jest zainstalowany na komputerach z systemem Linux przy użyciu Linux64.bin InstallDependencyAgent, skrypt powłoki, który ma samorozpakowujący się plik binarny. Dodaje plik za pomocą sh lub go uruchamia contoso uprawnienia do wykonywania do sam plik.

2. Firma Contoso instaluje agenta zależności systemu Linux jako użytkownik główny:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Krok 6: Uruchamianie i analizowanie oceny maszyny Wirtualnej

Firma Contoso może teraz Sprawdź zależności maszyny i Utwórz grupę. Następnie uruchamia ocenę grupy.

### <a name="verify-dependencies-and-create-a-group"></a>Sprawdzanie zależności i tworzenie grupy

1. Aby określić, które maszyny do analizowania, wybiera Contoso **wyświetlanie zależności**.

    ![Usługa Azure Migrate — wyświetlanie zależności maszyn](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Dla maszyny SQLVM Mapa zależności zawiera następujące informacje:

    - Grupa procesów lub procesy, które mają aktywnymi połączeniami sieciowymi uruchomione na maszynie SQLVM w określonym przedziale czasu (domyślnie przez godzinę,).
    - Połączenia przychodzące TCP (klienta) do wszystkich zależnych maszyn i połączenia wychodzące TCP (serwera) z wszystkich zależnych maszyn.
    - Zależnych maszyn, które mają zainstalowanych agentów usługi Azure Migrate są wyświetlane jako oddzielne pola.
    - Port i adres IP umożliwia wyświetlenie maszyn, które nie mają zainstalowanych agentów.

3. W przypadku maszyn z zainstalowanym agentem (WEBVM) firmy Contoso zaznacza pole maszyny, aby wyświetlić więcej informacji. Informacje obejmują nazwę FQDN, system operacyjny i adres MAC.

    ![Usługa Azure Migrate — wyświetlanie zależności grupowych](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Contoso wybiera maszyny wirtualne, które można dodać do grupy (SQLVM i WEBVM). Firma Contoso używa kombinacji Ctrl + kliknięcie, aby wybrać wiele maszyn wirtualnych.
5. Wybiera contoso **Utwórz grupę**, a następnie wprowadza imię i nazwisko (**smarthotelapp**).

    > [!NOTE]
    > Aby wyświetlić bardziej szczegółowe zależności, możesz rozszerzyć zakres czasu. Można wybrać przedział lub wybierz daty rozpoczęcia i zakończenia.

### <a name="run-an-assessment"></a>Uruchamianie oceny

1. W **grup**, Contoso otworzy grupę (**smarthotelapp**), a następnie wybiera **Utwórz ocenę**.

    ![Azure migracji — tworzenie oceny](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Aby wyświetlić ocenę, wybiera Contoso **Zarządzaj** > **ocen**.

Firma Contoso używa domyślnych ustawień oceny, ale możesz [dostosować ustawienia](how-to-modify-assessment.md).

### <a name="analyze-the-vm-assessment"></a>Analizowanie oceny maszyny wirtualnej

Ocena usługi Azure Migrate zawiera informacje dotyczące zgodności lokalnych z platformą Azure, sugerowanych rozmiarów maszyny wirtualnej platformy Azure oraz szacowanych miesięcznych kosztów platformy Azure.

![Usługa Azure Migrate — raport z oceny](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

![Usługa Azure Migrate — wyświetlanie oceny](./media/contoso-migration-assessment/assessment-display.png)

Ocena jest ocena zaufania od 1 gwiazdki do 5 gwiazdek (1 gwiazdka to najniższa i 5 gwiazdek jest najwyższa).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych, które są wymagane do obliczenia oceny.
- Pomaga oszacować niezawodność zaleceń dotyczących rozmiarów, które są dostarczane przez usługę Azure Migrate.
- Ocena zaufania jest przydatna podczas ustalania *ustalania rozmiaru na podstawie wydajności*. Usługa Azure Migrate może nie mieć wystarczającej liczby punktów danych na potrzeby ustalania rozmiaru na podstawie użycia. Aby uzyskać *jako lokalne* zmiany rozmiaru, ocena zaufania to zawsze 5 gwiazdek, ponieważ usługa Azure Migrate ma wszystkie punkty danych, należy go rozmiaru maszyny Wirtualnej.
- W zależności od wartości procentowej dostępnych punktów danych ocenę zaufania dla oceny określa:

   Dostępność punktów danych | Ocena zaufania
   --- | ---
   0%–20% | 1 gwiazdki
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdek
   61%–80% | 4 gwiazdek
   81%–100% | 5 gwiazdek

#### <a name="verify-azure-readiness"></a>Sprawdź gotowość na platformę Azure

![Usługa Azure Migrate — Ocena gotowości](./media/contoso-migration-assessment/azure-readiness.png)  

Raport z oceny zawiera informacje, które są podsumowane w tabeli. Aby wyświetlić ustalania rozmiaru na podstawie wydajności, usługa Azure Migrate potrzebuje następujących informacji. Nie można zebrać informacje, ocena rozmiarów może nie być dokładne.

- Dane użycia procesora CPU i pamięci.
- Liczba operacji we/wy na sekundę i przepływność dla każdego dysku podłączonego do maszyny wirtualnej.
- Informacje o ruchu wchodzącym/wychodzącym dla każdej karty sieciowej podłączonej do maszyny wirtualnej.

Ustawienie | Oznaczenie | Szczegóły
--- | --- | ---
**Gotowość maszyny wirtualnej platformy Azure** | Wskazuje, czy maszyna wirtualna jest gotowa do migracji. | Możliwe stany:</br><br/>– Gotowa na platformę Azure<br/><br/>– Gotowa warunkowo <br/><br/>– Niegotowa na platformę Azure<br/><br/>– Nieznana gotowość<br/><br/> Jeśli maszyna wirtualna nie jest gotowa, usługa Azure Migrate zawiera pewne kroki korygowania.
**Rozmiar maszyny wirtualnej platformy Azure** | Gotowe w przypadku maszyn wirtualnych usługi Azure Migrate oferuje zalecenie dotyczące rozmiaru maszyny Wirtualnej platformy Azure. | Zalecenia te zależą od właściwości oceny:<br/><br/>– Jeśli wybrano opcję ustalania rozmiaru na podstawie wydajności, uwzględniana jest historia wydajności maszyn wirtualnych.<br/><br/>— Jeśli używasz *jako lokalne*, ustalanie rozmiaru zależy od rozmiaru maszyny Wirtualnej w środowisku lokalnym i dane użycia nie będą używane.
**Sugerowane narzędzie** | Ponieważ maszyn zainstalowano agentów, usługa Azure Migrate analizuje procesy, które są uruchomione na maszynie. Wskazuje ona, czy maszyna to maszyna bazy danych.
**Informacje o maszynie wirtualnej** | Raport zawiera ustawienia dla maszyny Wirtualnej w środowisku lokalnym, w tym system operacyjny, typ rozruchu i informacje o dysku i pamięci masowej.

#### <a name="review-monthly-cost-estimates"></a>Przegląd szacowanego kosztu miesięcznego

Ten widok przedstawia łączny koszt zasobów obliczeniowych i magazynowych uruchomionych maszyn wirtualnych na platformie Azure. Pokazuje także szczegóły dla poszczególnych maszyn.

![Usługa Azure Migrate — koszty platformy Azure](./media/contoso-migration-assessment/azure-costs.png)

- Koszty są szacowane przy użyciu zaleceń dotyczących rozmiarów maszyny.
- Szacowany miesięczny koszt zasobów obliczeniowych i magazynowych jest agregowany dla wszystkich maszyn wirtualnych w grupie.

## <a name="clean-up-after-assessment"></a>Wyczyść zasoby po ukończeniu oceny

- Po zakończeniu oceny, Contoso zachowuje urządzenia usługi Azure Migrate do użycia w przyszłości ocen.
- Contoso powoduje wyłączenie maszyny Wirtualnej VMware. Contoso użyje go ponownie podczas ocenia dodatkowych maszyn wirtualnych.
- Przechowuje contoso **migracji Contoso** projekt na platformie Azure. Projekt jest obecnie wdrożona w **ContosoFailoverRG** grupy zasobów w regionie wschodnie stany USA Azure.
-  Maszyna wirtualna modułu zbierającego ma licencję 180-dniowej wersji ewaluacyjnej. Jeśli upłynie limit, Contoso należy pobrać modułu zbierającego i skonfiguruj go ponownie.

## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu firma Contoso ocenia swoją bazę danych aplikacji rozwiązania SmartHotel360 za pomocą narzędzia oceny migracji danych. Ocenia ona lokalnych maszyn wirtualnych, za pomocą usługi Azure Migrate. Contoso przegląda ocen, aby upewnić się, że lokalne zasoby są gotowe do migracji na platformę Azure.

## <a name="next-steps"></a>Kolejne kroki

W następnym artykule z tej serii Contoso rehostowaniu swojej aplikacji rozwiązania SmartHotel360 na platformie Azure przy użyciu funkcji migracji lift-and-shift. Contoso migruje frontonu WEBVM dla aplikacji za pomocą usługi Azure Site Recovery. Do wystąpienia usługi Azure SQL Database Managed migracją bazy danych aplikacji przy użyciu usługi migracji bazy danych. [Rozpoczynanie pracy](contoso-migration-rehost-vm-sql-managed-instance.md) z tym wdrożeniem.
