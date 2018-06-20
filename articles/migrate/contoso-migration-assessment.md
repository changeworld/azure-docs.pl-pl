---
title: Oceny obciążeń lokalnych do migracji firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ocenia ich maszyn lokalnych do migracji do usługi Azure z migracji bazy danych i migracji Azure
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: fb987c95afc0f77386f4f78c44f3c6825f86ee43
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232497"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migracja Contoso: oceny obciążeń lokalnych do migracji do usługi Azure

W tym artykule opisano, jak Contoso ocenia jego lokalnymi SmartHotel aplikacji w ramach przygotowania do migracji jej na platformie Azure.

Ten dokument jest innej serii artykuły, które dokumentów, jak fikcyjnej firmy Contoso migruje jej zasobów lokalnych do chmury Microsoft Azure. Seria zawiera również ogólne informacje, oraz szereg scenariuszy wdrażania, które przedstawiają sposób konfigurowania infrastruktury migracji, oceny przydatności zasobów lokalnych do migracji, a następnie uruchom różnych rodzajów migracji. Scenariusze zwiększa się złożoność i które będą dodawane dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
Artykuł 3: Oceny zasobów lokalnych (w tym artykule)  | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji frontonu maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi migrację do zarządzanego wystąpienie serwera SQL. | Dostępne
[Artykuł 5: Rehost na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migracji aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Site Recovery ich użyć do migracji aplikacji maszyny wirtualne i usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Rehost aplikacji systemu Linux, aby maszyny wirtualne platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso migruje osService aplikacji systemu Linux przy użyciu usługi Azure Site Recovery.
[Artykuł 8: Rehost aplikację systemu Linux, aby maszyny wirtualne platformy Azure i serwerem MySQL Azure](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso przeprowadzanie migracji aplikacji Linux osService, przy użyciu usługi Site Recovery dla maszyny Wirtualnej migracji i MySQL Workbench do migracji (na wystąpienie serwera Azure MySQL. | Dostępne


## <a name="overview"></a>Przegląd

Jak uznają migracji na platformie Azure, firma Contoso chce uruchamiać technicznych i finansowych oceny Aby dowiedzieć się, czy jego obciążeń lokalnych nadają się do migracji do chmury. W szczególności zespołu Contoso chcesz ocenić zgodność maszyny i bazy danych do migracji, a Szacowanie zdolności produkcyjnych i koszty związane z zasobami na platformie Azure.

Aby uzyskać ich mokro stopy i Poznaj lepiej technologii, masz zamiar oceny dwa swoje aplikacje lokalne, podsumowane w poniższej tabeli. Należy pamiętać, że ich jest oceny scenariusze migracji tego rehost i zrefaktoryzuj aplikacje do migracji. Dowiedz się więcej o rehosting i refaktoryzacji w [Omówienie migracji Contoso](contoso-migration-overview.md).

**Nazwa aplikacji** | **Platformy** | **Warstwy aplikacji** | **Szczegóły**
--- | --- | --- | ---
SmartHotel<br/><br/> Służy do zarządzania wymaganiami podróży firmy Contoso | Uruchamianie w systemie Windows z bazy danych programu SQL Server | Dwuwarstwowej aplikacji z witryną internetową serwera sieci Web platformy ASP.NET działających na jednej maszyny Wirtualnej (WEBVM) i SQL Server uruchomiony na innym maszyny Wirtualnej (SQLVM) | Maszyny wirtualne są VMware uruchomiona na hoście ESXi zarządzany przez serwer vCenter.<br/><br/> Przykładowa aplikacja można pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Usługi contoso technicznej aplikacji | Uruchomiony na systemie Linux/Apache za pomocą języka PHP MySQL (światła). | Dwuwarstwowej aplikacji z frontonu witryny PHP w jednej maszyny Wirtualnej (OSTICKETWEB) i bazy danych MySQL przeprowadzana na innej maszyny Wirtualnej (OSTICKETMYSQL) | Aplikacja jest używany przez klienta aplikacji usługi do śledzenia problemów dla pracowników wewnętrznych i zewnętrznych klientów.<br/><br/> Przykładowa aplikacja można pobrać z [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Architektura bieżącego


Poniżej przedstawiono diagram przedstawiający bieżącej infrastruktury lokalnej firmy Contoso.

![Architektura firmy Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Firma Contoso ma jeden głównego centrum danych znajduje się w mieście z nowego Jorku w Wschodniej Stanów Zjednoczonych.
- Mają one trzy dodatkowe gałęzi lokalnej w Stanach Zjednoczonych.
- Głównego centrum danych jest połączony z Internetem za pomocą połączenia ethernet metro fiber (500 MB/s).
- Każda gałąź jest lokalnie połączony z Internetem przy użyciu połączenia klasy biznesowych, z tuneli protokołu IPSec sieci VPN do głównego centrum danych. Dzięki temu ich całej sieci trwale zostać nawiązane połączenie oraz optymalizuje łączności z Internetem.
- Głównego centrum danych jest w pełni zwirtualizowanych z programu VMware. Mają one dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami i serwery DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Na kontrolerach domeny w lokalnej gałęzi działa na serwerach fizycznych.





## <a name="business-drivers"></a>Czynniki biznesowe

Kadry kierowniczej IT ściśle działał z ich partnerów biznesowych, aby zrozumieć firmy chce osiągnięcie przy tej migracji:

- **Adres rozwoju firmy**: rośnie Contoso i w związku z tym istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawnić procesy ich deweloperzy i użytkownicy.  Potrzeb biznesowych IT szybkie i czas nie odpadkami lub pieniędzy, w związku z tym dostarczania szybciej na wymagania dotyczące klienta.
- **Zwiększyć elastyczność**: Contoso IT musi być bardziej odpowiednie do potrzeb firmy. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby włączyć Powodzenie w globalnej gospodarka reakcji.  Nie może pobrać w taki sposób, lub stają się okienek biznesowych.
- **Skala**: jak pomyślnie rozwojem działalności IT firmy Contoso musi dostarczyć systemów, które są w stanie rośnie w tempie tego samego.

## <a name="assessment-goals"></a>Cele oceny

Zespół chmury Contoso ma przypięty dół cele dotyczące ich oceny migracji:

- Po migracji aplikacji na platformie Azure powinien mają te same możliwości wydajności, jak obecnie w środowisku VMWare lokalnymi.  Przenoszeniu do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Contoso należy zrozumieć zgodność aplikacji i baz danych z wymaganiami platformy Azure, a także ich opcje hostingu na platformie Azure.
- Firmy Contoso administracyjnej bazy danych należy zminimalizować po aplikacje zostały przeniesione do chmury.  
- Firma Contoso chce zrozumienia nie tylko opcje migracji, ale także koszty związane z infrastrukturą, gdy zostanie przeniesiona do chmury.

## <a name="assessment-tools"></a>Narzędzia do oceny
Firma Contoso używa narzędzia Microsoft do oceny. Te narzędzia są wyrównane z ich cele i należy zapewnić użytkownikom informacje, które są im potrzebne.

**Technologia** | **Opis** | **Koszty**
--- | --- | ---
[Asystent migracji bazy danych (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Będzie przez nich używana DMA do oceny i wykrywania problemów ze zgodnością, które mogą mieć wpływ na ich funkcji bazy danych na platformie Azure. DMA ocenia parzystość funkcji platformy SQL źródeł i obiektów docelowych i zaleca ulepszenia wydajności i niezawodności. | Narzędzie to można pobrać bezpłatnie.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso będą używać tej usługi do oceny ich maszyny wirtualne VMware. Ocenia ona kwalifikowanie się maszyn do migracji na platformę Azure. Oprócz tego przedstawia szacunki dotyczące rozmiarów i kosztów maszyn uruchamianych na platformie Azure.  | Brak aktualnie 2018 może bezpłatnie korzystać z tej usługi.
[Mapa usługi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Usługa Azure Migrate przedstawia zależności między maszynami, które chcesz migrować, za pomocą rozwiązania Service Map, |  które jest częścią usługi Azure Log Analytics. Obecnie można go używać bezpłatnie przez 180 dni.

W tym scenariuszu Contoso pobiera i uruchamia DMA do oceny lokalną bazą danych programu SQL Server dla aplikacji podróży. Używają Azure migracji z mapowaniem zależności do oceny aplikacji maszyn wirtualnych, przed migracją do systemu Azure.



## <a name="assessment-architecture"></a>Architektura oceny


![architekturę do oceny migracji](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Firma Contoso używa nazwy fikcyjne, reprezentujący organizacji typowego przedsiębiorstwa. 
- Firma Contoso ma lokalnego centrum danych (**centrum danych firmy contoso**), z kontrolerami domeny lokalnej (CONTOSODC1, CONTOSODC2).
- Maszyny wirtualne VMware znajdują się na VMware ESXI hostach z systemem w wersji 6.5. Hosty: **contosohost1**, **contosohost2**
- Zarządza w środowisku programu VMware vCenter server 6.5 (**venter**uruchomionego na maszynie Wirtualnej.
- Aplikacja podróży SmartHotel:
    - Aplikacja jest warstwowa w dwóch maszyn wirtualnych VMware **WEBVM** i **SQLVM**.
    - Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com**.
    - Maszyny wirtualne są uruchomione systemu Windows Server 2008 R2 Datacenter z dodatkiem SP1.
- Środowisko VMware jest zarządzane przez program vCenter Server (**vcenter.contoso.com**) uruchomiony na maszynie wirtualnej.
- OSTicket obsługi technicznej aplikacji:
    - Aplikacja jest warstwowa w dwóch maszyn wirtualnych, **OSTICKETWEB** i **OSTICKETMYSQL**.
    - Maszyny wirtualne są uruchomione na Ubuntu Linux Server 16.04-LTS.
    - Maszyna wirtualna OSTICKETWEB działa Apache 2 i PHP w wersji 7.0.
    - Maszyna wirtualna OSTICKETMYSQL działa MySQL 5.7.22.

![Architektura](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Wymagania wstępne

Oto Contoso (a) wymaga oceny:

- Dostęp właścicielem lub współautorem subskrypcji platformy Azure lub dla grupy zasobów w subskrypcji Azure.
- Lokalny program vCenter Server w wersji 5.5, 6.0 lub 6.5.
- Konto tylko do odczytu w programie vCenter Server lub uprawnienia do utworzenia takiego konta.
- Uprawnienia do utworzenia maszyny wirtualnej w programie vCenter Server przy użyciu szablonu .OVA.
- Co najmniej jeden host ESXi w wersji 5.0 lub nowszy.
- Co najmniej dwie lokalne maszyny wirtualne VMware, w tym jedna z uruchomioną bazą danych programu SQL Server.
- Uprawnienia do instalowania agentów Azure migracji na każdej maszynie Wirtualnej.
- Maszyny wirtualne powinny mieć bezpośrednie połączenie z Internetem.
        – Możesz ograniczyć dostęp do Internetu do [wymaganych adresów URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Jeśli maszyn bez łączności z Internetem, [bramy OMS](../log-analytics/log-analytics-oms-gateway.md) należy zainstalować na nich.
- Nazwa FQDN maszyny wirtualnej z uruchomionym wystąpieniem programu SQL Server, używana do oceny bazy danych.
- Zapora systemu Windows uruchomiona na maszynie wirtualnej programu SQL Server powinna zezwalać na połączenia zewnętrzne na porcie TCP 1433 (domyślnym), aby umożliwić nawiązanie połączenia przez program DMA.


## <a name="assessment-overview"></a>Omówienie oceny

Oto jak Contoso będzie przeprowadzenie oceny:


> [!div class="checklist"]
> * **Krok 1: Pobierz i zainstaluj DMA**: przygotowanie DMA oceny lokalną bazą danych programu SQL Server.
> * **Krok 2: Ocena bazy danych o DMA**: uruchamiać i analizować oceny bazy danych.
> * **Krok 3: Przygotowanie do oceny maszyny Wirtualnej z migracji Azure**: Konfigurowanie lokalnych kont i dostrajał VMware ustawienia.
> * **Krok 4: Odnajdywanie maszyn wirtualnych lokalnie z migracji Azure**: Utwórz moduł zbierający Azure migracji maszyny Wirtualnej. Następnie uruchom moduł zbierający do odnajdywanie maszyn wirtualnych w celu oceny.
> * **Krok 5: Przygotowanie do analizy zależności z migracji Azure**: migracji Azure zainstalować agentów na maszynach wirtualnych, dzięki czemu będą mogli wyświetlać mapowania zależności między maszynami wirtualnymi.
> * **Krok 6: Oceny maszyn wirtualnych o migracji Azure**: Sprawdź zależności, grupowanie maszyn wirtualnych i uruchamianie oceny. Po oceny jest gotowy, ich analizować w ramach przygotowania do migracji.


## <a name="step-1-download-and-install-the-dma"></a>Krok 1: Pobierz i zainstaluj DMA

1. Contoso pobiera DMA z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Asystent można zainstalować na dowolnym komputerze, który może połączyć się z wystąpieniem programu SQL. Nie musisz go uruchamiać na maszynie programu SQL Server.
    - Nie można uruchomić na komputerze hosta programu SQL Server.
2. Uruchomieniu pliku pobranego Instalatora (DownloadMigrationAssistant.msi), aby rozpocząć instalację.
3. Na **Zakończ** strony, wybierają **uruchomić Asystenta firmy Microsoft danych migracji** przed zakończeniem pracy kreatora.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Krok 2: Uruchom i analizować oceny bazy danych dla SmartHotel

Contoso można teraz uruchomić ocenę do analizowania ich lokalnego serwera SQL dla aplikacji SmartHotel.

1. W bazie danych migracji Asystencie, kliknij przycisk **nowy**, wybierz pozycję **oceny**i nadaj nazwę projektu - oceny **SmartHotel**.
2. Wybierają **typ serwera źródła** jako **programu SQL Server na maszynach wirtualnych Azure**. 

    ![Wybierz źródło](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Aktualnie program DMA nie obsługuje oceny migracji na wystąpienie zarządzane SQL. Jako obejście Contoso używa programu SQL Server na maszynie Wirtualnej Azure jako element docelowy domniemana oceny.

3. W **Wybierz docelową wersję**, wybierają 2017 serwera SQL w wersji docelowej. Należy wybrać tę opcję, ponieważ jest to wersja używanego przez zarządzane wystąpienia serwera SQL.
4. Wybierają informacji dotyczących zgodności i nowe funkcje:
    - **Problemy ze zgodnością** należy zwrócić uwagę zmiany, która może spowodować uszkodzenie migracji lub które wymagają mniejsze zmiany przed migracją. Zapewnia informuje o funkcjach obecnie w użyciu, które zostały wycofane. Problemy są uporządkowane według poziomu zgodności.
    - **Nowe funkcje zalecenie** zawiera informacje dotyczące nowych funkcji w docelowej platformy programu SQL Server, który może służyć do bazy danych po zakończeniu migracji. Są one uporządkowane według wydajności, zabezpieczeń i magazynu.

    ![Wybieranie obiektu docelowego](./media/contoso-migration-assessment/dma-assessment-2.png)

2. W **Połącz z serwerem**, nazwę maszyny Wirtualnej uruchomionej bazy danych i poświadczeń dostępu do niego. Należy włączyć **zaufania certyfikatów serwera** się upewnić, że ich może uzyskać dostęp do programu SQL Server. Następnie użytkownik klika polecenie **Connect**.

    ![Wybieranie obiektu docelowego](./media/contoso-migration-assessment/dma-assessment-3.png)

3. W **źródła Dodaj**, co zwiększa bazy danych, którego chce ocenić, a następnie kliknij przycisk **dalej** uruchomić oceny.
4. Utworzono oceny.
    
    ![Tworzenie oceny](./media/contoso-migration-assessment/dma-assessment-4.png)

5. W **wyników przeglądu**, będą mogli wyświetlać wyniki oceny.


### <a name="analyze-the-database-assessment"></a>Analizowanie oceny bazy danych

Wyniki są wyświetlane, gdy są one dostępne. Jeżeli ich rozwiązywanie problemów muszą kliknij **ponowne uruchomienie oceny** ponowne uruchomienie oceny.

1. W **problemy ze zgodnością** raport, sprawdź wszystkie problemy na każdym poziomie zgodności. Poziomy zgodności są mapowane na wersje programu SQL Server w następujący sposób:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problemy ze zgodnością](./media/contoso-migration-assessment/dma-assessment-5.png)

2. W **funkcji zalecenia** raport Contoso można wyświetlić funkcje wydajności, zabezpieczeń i magazynowania, które oceny zaleca się po migracji. Zaleca się różne funkcje, w tym OLTP w pamięci i magazynu kolumn, bazy danych Stretch, zawsze zaszyfrowane, dynamicznego maskowania danych i funkcji przezroczystego szyfrowania danych (TDE).

    ![Zalecenia dotyczące funkcji](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Zalecamy Contoso [umożliwia funkcji TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) dla wszystkich programu SQL Server baz danych jest to jeszcze bardziej krytyczne w przypadku baz danych w chmurze. Funkcji TDE powinna być włączona tylko po migracji. Jeśli już włączono funkcji TDE, należy przenieść certyfikatu lub klucza asymetrycznego do głównej bazy danych serwera docelowego. [Dowiedz się więcej](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Ich można wyeksportować oceny w formacie JSON lub CSV.

Należy pamiętać, że jeśli korzystasz z większą oceny skali można:

- Równoczesne uruchamianie wielu ocen i wyświetlić stan oceny przez otwarcie **wszystkie oceny** strony.
- [Konsolidacja oceny do bazy danych programu SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Konsolidacja oceny do raportu usługi Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Krok 3: Przygotowanie do oceny maszyny Wirtualnej z platformy Azure migracji

Contoso musi utworzyć konto VMware, który migracji Azure będzie używany do automatyczne odnajdywanie maszyn wirtualnych w celu oceny, sprawdź uprawnienia do tworzenia maszyn wirtualnych, należy pamiętać, porty, które muszą być otwarte i Ustaw statystyki ustawienia poziom.

### <a name="set-up-a-vmware-account"></a>Konfigurowanie konta VMware

 Odnajdywanie maszyn wirtualnych wymaga konta tylko do odczytu w programie vCenter o następujących właściwościach: 

- Typ użytkownika: co najmniej użytkownik tylko do odczytu.
- Uprawnienia: obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu.
- Szczegóły: użytkownik przypisany na poziomie centrum danych z dostępem do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

### <a name="verify-permissions-to-create-a-vm"></a>Weryfikowanie uprawnień do utworzenia maszyny wirtualnej

Contoso Sprawdź, czy ma uprawnienia do tworzenia maszyny Wirtualnej przez zaimportowanie pliku w. Format komórek jajowych. [Dowiedz się więcej](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Weryfikowanie portów

Ocena Contoso używa mapowania zależności. Ta funkcja wymaga zainstalowanego na maszynach wirtualnych chcesz ocenić agenta. Agent musi mieć możliwość połączenia z platformą Azure z portu TCP 443 na każdej maszynie Wirtualnej. [Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) o wymaganiach dotyczących połączenia.


### <a name="set-statistics-settings"></a>Ustawianie statystyk

Przed rozpoczęciem wdrożenia Contoso ustawiony poziom 3 ustawienia statystyki dla serwera vCenter. Należy pamiętać, że:

- Po ustawieniu poziomu, trzeba poczekać co najmniej dzień przed uruchomieniem oceny. W przeciwnym razie może ona nie działać zgodnie z oczekiwaniami.
- Jeśli zostanie ustawiony poziom wyższy niż 3, ocena będzie działać w następujący sposób:
    - Nie będą zbierane dane wydajności dysków ani sieci.
    - W przypadku magazynu usługa Azure Migrate zaleca standardowy dysk na platformie Azure, o takim samym rozmiarze co dysk lokalny.
    - W przypadku sieci zaleca się utworzenie karty sieciowej na platformie Azure dla każdej lokalnej karty sieciowej.
    - W przypadku obliczeń usługa Azure Migrate uwzględnia liczbę rdzeni oraz rozmiar pamięci i zaleca utworzenie maszyny wirtualnej platformy Azure o takiej samej konfiguracji. Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) o rozmiarach na poziomie 3.

Poziom ich ustawić w następujący sposób:

1. VSphere klienta sieci Web ich Otwórz wystąpienie programu vCenter server.
2. W **Zarządzaj** > **ustawienia** > **ogólne**, użytkownik klika polecenie **Edytuj**.
3. W **statystyki**, ich ustawienie statystyki poziomu **poziom 3**.

    ![Poziom statystyki programu vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Krok 4: Odnajdywanie maszyn wirtualnych

Odnajdywanie maszyn wirtualnych, Contoso tworzy projekt platformy Azure migracji. Ich pobrać i skonfigurować moduł zbierający maszyny Wirtualnej i uruchomić moduł zbierający do odnajdywanie swoich lokalnych maszyn wirtualnych.

### <a name="create-a-project"></a>Tworzenie projektu

1. W [portalu Azure](https://portal.azure.com), wyszukiwania **migracji Azure**i Utwórz projekt (ContosoMigration).
2. Określ nazwę projektu subskrypcji platformy Azure i utworzyć nową grupę zasobów platformy Azure, **ContosoFailoverRG**. Należy pamiętać, że:

    - Projekt usługi Azure Migrate można utworzyć tylko w regionie Zachodnio-środkowe stany USA lub Wschodnie stany USA.
    - Migrację możesz zaplanować dla dowolnej lokalizacji docelowej.
    - Lokalizacja projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Ta maszyna wirtualna służy do odnajdowania lokalnych maszyn wirtualnych VMware i wysyłania dotyczących ich metadanych do usługi Azure Migrate. Aby skonfigurować urządzenia modułu zbierającego, pliki do pobrania firmy Contoso. Szablon komórek jajowych i importuje go do lokalnego serwera vCenter do utworzenia maszyny Wirtualnej.

1. W projekcie migracji Azure > **wprowadzenie** > **odnajdź & oceny** > **odnajdywanie maszyn**, będą oni mogli pobrać. Plik szablonu komórki jajowe.
2. Kopiowania projektu identyfikator i klucz. Są one potrzebne do skonfigurowania modułu zbierającego.

    ![Pobieranie pliku OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Przed wdrożeniem maszyny Wirtualnej, Contoso sprawdza, czy. Komórki jajowe plik jest bezpieczny.

1. Na komputerze, na którym plik pobrany ich Otwórz okno polecenia administratora.
2. One uruchom następujące polecenie, aby wygenerować skrótu dla komórek jajowych:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Wygenerowany skrót powinien odpowiadać następującym ustawieniom (wersja 1.0.9.7)

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c


### <a name="create-the-collector-appliance"></a>Tworzenie urządzenia modułu zbierającego

Teraz Contoso można zaimportować pobrany plik do programu vCenter Server i aprowizuj serwer konfiguracji maszyny Wirtualnej.

1. W konsoli klienta vSphere, kliknij przycisk **pliku** > **wdrażanie szablonu OVF**.

    ![Wdrażanie pliku OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. W Kreatorze wdrażania szablonu OVF > **źródła**, określają lokalizację. Plik komórek jajowych.
3. W **nazwy i lokalizacji**, określa przyjazną nazwę dla modułu zbierającego maszyny Wirtualnej, a lokalizacja magazynu, w którym będzie hostowana maszyna wirtualna. Określają również hosta lub klastra, na którym uruchomiona jest moduł zbierający urządzenia.
5. W **magazynu**, określają lokalizację magazynu i **Format dysku**, w jaki sposób chcą, aby udostępnić magazyn.
7. W **mapowanie sieci**, określają sieci, z którą połączy się modułu zbierającego maszyny Wirtualnej. Sieć musi mieć połączenie z Internetem w celu wysyłania metadanych do platformy Azure.
8. Przejrzyj ustawienia i wybierz **włączają po wdrożeniu**> **Zakończ**. Po utworzeniu urządzenia pojawi się komunikat potwierdzający pomyślne zakończenie operacji.

### <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Teraz uruchom moduł zbierający do odnajdywanie maszyn wirtualnych. Należy pamiętać, że moduł zbierający aktualnie obsługuje tylko "Angielski (Stany Zjednoczone)" jako język systemu operacyjnego i język interfejsu modułu zbierającego.

1. W vSphere Client konsoli > **Otwórz konsolę**, określa język, strefa czasowa i preferencje hasła dla modułu zbierającego maszyny Wirtualnej.
2. Na pulpicie kliknij przycisk **uruchom moduł zbierający** skrótów.

    ![Skrót do modułu zbierającego](./media/contoso-migration-assessment/collector-shortcut.png)

4. W module zbierającym migracji Azure > **konfigurowanie wymagań wstępnych**, zaakceptuj postanowienia licencyjne i odczytać informacji o innych firm.
5. Moduł zbierający sprawdza, czy maszyna wirtualna ma dostęp do Internetu, czy godzina jest zsynchronizowana, i czy działa usługa modułu zbierającego (jest instalowany domyślnie na maszynie Wirtualnej). Instaluje VMWare PowerCLI.

    > [!NOTE]
    > Zakłada się, że maszyna wirtualna ma bezpośredni dostęp do Internetu, bez serwera proxy.

    ![Weryfikowanie wymagań wstępnych](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. W **Określ szczegóły serwera vCenter**, określa nazwę (FQDN) lub adres IP serwera vCenter, i tylko do odczytu poświadczenia używane do odnajdywania.
7. Wybierają zakres odnajdywania maszyny Wirtualnej. Moduł zbierający odnajdzie tylko maszyny wirtualne we wskazanym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Zakres nie powinien zawierać więcej niż 1500 maszyn wirtualnych.

    ![Nawiązywanie połączenia z serwerem vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. W **Określ migrację**, określa identyfikator projektu migracji Azure i klucz, który został skopiowany z portalu. Możesz uzyskać je ponownie w projekcie **omówienie** strony > **odnajdywanie maszyn**.  

    ![Nawiązywanie połączenia z usługą Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. W **wyświetlić postęp kolekcji** Contoso można monitorować odnajdywania i sprawdź, że metadane zbierane z maszyn wirtualnych znajduje się w zakresie. Moduł zbierający informuje o szacowanym czasie odnajdowania.

    ![Zbieranie w toku](./media/contoso-migration-assessment/collector-collection-process.png) 



### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu zbierania Contoso sprawdza, czy maszyny wirtualne są wyświetlane w portalu.

1. W projekcie migracji Azure > **Zarządzaj** > **maszyny**, sprawdź, czy ma zostać przeprowadzone odnajdywanie maszyn wirtualnych są widoczne.

    ![Odnalezione maszyny](./media/contoso-migration-assessment/discovery-complete.png)

3. Zwróć uwagę na to, że maszyny nie mają zainstalowanych agentów usługi Azure Migrate. Firmy Contoso, należy zainstalować je w zależności do wyświetlenia.

    ![Odnalezione maszyny](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Krok 5: Przygotowanie do analizy zależności

Aby wyświetlić zależności między maszynami wirtualnymi, które Contoso chcesz uzyskać dostęp, Pobierz i zainstaluj agentów w aplikacji maszyn wirtualnych. Contoso dzieje się tak na wszystkie maszyny wirtualne na swoje aplikacje w systemach Windows i Linux.

### <a name="take-a-snapshot"></a>Tworzenie migawki

Przed zmodyfikowaniem je, wykonując migawek, zanim agenci są zainstalowani one przechowywać kopię maszyny Wirtualnej.

![Migawka maszyny](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

1. Na **maszyny** strony, wybierają maszyny, a następnie **wymaga instalacji** w **zależności** kolumny.
2. Na **odnajdywanie maszyn** strony one wykonaj następujące czynności:
    - Pobierz agenta MMA i zależności dla każdej maszyny Wirtualnej systemu Windows
    - Pobierz agenta MMA i zależności dla każdej maszyny Wirtualnej systemu Linux
3. Teraz one skopiować identyfikator i klucz. Muszą one podczas instalowania MMA.

    ![Pobieranie agenta](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Instalowanie agentów na maszynach wirtualnych systemu Windows

Instalacji są uruchamiane w każdej maszyny Wirtualnej.

#### <a name="install-the-mma-on-windows-vms"></a>Zainstaluj MMA na maszynach wirtualnych systemu Windows

1. One kliknij dwukrotnie pobrany agenta.
2. W **Folder docelowy**, prowadzą domyślny folder instalacji > **dalej**.
2. W **opcje instalacji agenta**, wybierają **Połącz agenta z usługą Analiza dzienników Azure** > **dalej**.

    ![Instalacja programu MMA](./media/contoso-migration-assessment/mma-install.png)
    
5. W **Azure Log Analytics**, ich Wklej identyfikator i klucz, który został skopiowany z portalu. 

    ![Instalacja programu MMA](./media/contoso-migration-assessment/mma-install2.png)

6. W **gotowy do instalacji**, mogą teraz instalować MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Zainstaluj agenta zależności na maszynach wirtualnych systemu Windows

1. One kliknij dwukrotnie pobrany agenta zależności.
2. Zaakceptuj postanowienia licencyjne, a poczekaj na zakończenie instalacji.

    ![Agent zależności](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Instalowanie agentów na maszynach wirtualnych systemu Linux

Instalacji są uruchamiane w każdej maszyny Wirtualnej.

#### <a name="install-the-mma-on-linux-vms"></a>Zainstaluj MMA na maszynach wirtualnych systemu Linux

1. Biblioteka języka python ctypes instalacji na poszczególnych maszynę Wirtualną przy użyciu: **sudo stanie get instalacji języka python ctypeslib**.
2. Powinno być ono uruchomione polecenie, aby zainstalować agenta MMA jako katalogu głównego.  Wybrany jako główny uruchom następujące polecenia i wprowadź hasła głównego: **sudo -i**.
3. Teraz instalacji MMA agent.
    - Włóż poprawny identyfikator i klucz do polecenia.
    - Polecenia są dla 64-bitowej.
    - **Identyfikator obszaru roboczego** i **klucza podstawowego** można znaleźć w portalu OMS > **ustawienia**w **połączonych źródeł** kartę.
    - Uruchom następujące polecenia, aby pobrać agent pakietu OMS, sprawdź poprawność sumy kontrolnej i zainstaluj/dołączyć agenta.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```
 


#### <a name="install-the-dependency-agent-on-linux-vms"></a>Zainstaluj agenta zależności na maszynach wirtualnych systemu Linux

Po zainstalowaniu MMA agenta zależności Contoso można zainstalować na maszynach wirtualnych systemu Linux.

1. Dependency Agent jest zainstalowany na komputery z systemem Linux przy użyciu Linux64.bin InstallDependencyAgent, skrypt powłoki z samowyodrębniający plikiem binarnym. Można uruchomić go za pomocą sh lub Dodaj uprawnienia w samym pliku do wykonywania.

2. Jako katalog główny instalacji agenta systemu Linux zależności:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Krok 6: Uruchamiać i analizować oceny maszyny Wirtualnej

Contoso można teraz zweryfikować zależności maszyny i Utwórz grupę. Następnie uruchom oceny grupy.

### <a name="verify-dependencies-and-create-a-group"></a>Sprawdź zależności, a następnie utwórz grupę


1. Dla maszyn do analizy, kliknij przycisk **zależności widoku**.

    ![Wyświetlanie zależności maszyn](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. W przypadku maszyny SQLVM mapa zależności zawiera następujące informacje:

    - Grupy procesów/procesy z aktywnymi połączeniami sieciowymi uruchomione na maszynie SQLVM w określonym przedziale czasu (domyślnie przez godzinę)
    - Połączenia przychodzące TCP (klienta) do wszystkich zależnych maszyn i połączenia wychodzące TCP (serwera) z wszystkich zależnych maszyn.
    - Maszyny zależne z zainstalowanymi agentami usługi Azure Migrate są wyświetlane jako osobne pola
    - W przypadku maszyn bez zainstalowanych agentów są wyświetlane informacje o porcie i adresie IP.

3. W przypadku komputerów z zainstalowanym agentem (WEBVM) ich kliknij pole maszyny, aby wyświetlić więcej informacji, w tym nazwę FQDN, systemu operacyjnego i adres MAC.

    ![Wyświetlanie zależności grupowych](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Teraz wybrać maszyny wirtualne można dodać do grupy (SQLVM i WEBVM).  One użyj kombinacji klawisza CTRL + kliknij, aby wybrać wiele maszyn wirtualnych.
5. Użytkownik klika polecenie **Utwórz grupę**i określ nazwę (smarthotelapp).

> [!NOTE]
    > Aby wyświetlić bardziej szczegółowe zależności, możesz rozszerzyć zakres czasu. Możesz wybrać przedział lub datę początkową i datę końcową.


### <a name="run-an-assessment"></a>Uruchamianie oceny


1. Na **grup** strony, otwórz grupę (smarthotelapp) i kliknij przycisk **utworzyć oceny**.

    ![Tworzenie oceny](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Ocena pojawi się na stronie **Zarządzaj** > **Oceny**.

Contoso używane domyślne ustawienia oceny, ale można dostosować ustawienia. [Dowiedz się więcej](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analizowanie oceny maszyny wirtualnej

Ocena Azure migracji zawiera informacje o zgodności maszyn wirtualnych lokalnie na platformie Azure, sugerowane doboru wielkości dla maszyny Wirtualnej platformy Azure i miesięczne Azure koszty.

![Raport z oceny](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

![Wyświetlanie oceny](./media/contoso-migration-assessment/assessment-display.png)

Ocenę pobiera przedziałem zaufania z gwiazdką 1 do 5 gwiazdkę (1 gwiazdka być najniższy i 5 Gwiazda jest najwyższa).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Pomaga to oszacować niezawodność zaleceń dotyczących rozmiarów, udostępnianych przez usługę Azure Migrate.
- Zaufanie jest przydatne podczas wykonywania *wydajności na podstawie rozmiaru* jako migracji Azure może nie mieć za mało punktów danych w celu wykorzystania na podstawie zmiany rozmiaru. W przypadku *ustalania rozmiaru jako lokalnego* ocena zaufania to zawsze 5 gwiazdek, ponieważ usługa Azure Migrate ma wszystkie punkty danych, których potrzebuje do ustalenia rozmiaru maszyny wirtualnej.
- W zależności od wartości procentowej dostępnych punktów danych ocenę zaufania dla oceny określa:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

#### <a name="verify-readiness"></a>Sprawdzanie gotowości

![Ocena gotowości](./media/contoso-migration-assessment/azure-readiness.png)  

Raport z oceny zawiera podsumowane informacje w tabeli. Do wyświetlenia treści dotyczącej ustalania rozmiaru na podstawie wydajności usługa Azure Migrate potrzebuje następujących informacji. Jeśli nie można zebrać tych informacji, ocena rozmiarów może być niedokładna.

- Dane użycia procesora CPU i pamięci.
- Liczba operacji we/wy na sekundę i przepływność dla każdego dysku podłączonego do maszyny wirtualnej.
- Informacje o ruchu wchodzącym/wychodzącym dla każdej karty sieciowej podłączonej do maszyny wirtualnej.


**Ustawienie** | **Wskazanie** | **Szczegóły**
--- | --- | ---
**Gotowość maszyny wirtualnej platformy Azure** | Wskazuje, czy maszyna wirtualna jest gotowa do migracji | Możliwe stany:</br><br/>– Gotowa na platformę Azure<br/><br/>– Gotowa warunkowo <br/><br/>– Niegotowa na platformę Azure<br/><br/>– Nieznana gotowość<br/><br/> Jeśli maszyna wirtualna nie jest gotowa, pojawią się instrukcje rozwiązania tego problemu.
**Rozmiar maszyny wirtualnej platformy Azure** | W przypadku gotowych maszyn wirtualnych widać informację z zalecanym rozmiarem maszyny wirtualnej platformy Azure. | Zalecenia te zależą od właściwości oceny:<br/><br/>– Jeśli wybrano opcję ustalania rozmiaru na podstawie wydajności, uwzględniana jest historia wydajności maszyn wirtualnych.<br/><br/>– Jeśli wybrano opcję „jak lokalne”, rozmiar jest ustalany na podstawie rozmiaru lokalnej maszyny wirtualnej, a dane dotyczące użycia nie są uwzględniane.
**Sugerowane narzędzie** | Ponieważ na maszynach zainstalowano agentów, usługa Azure Migrate analizuje procesy uruchomione na maszynie i sprawdza, czy dana maszyna to maszyna bazy danych.
**Informacje o maszynie wirtualnej** | Raport zawiera ustawienia lokalnej maszyny wirtualnej, w tym system operacyjny, typ rozruchu oraz informacje o dysku i magazynie.


#### <a name="review-monthly-cost-estimates"></a>Przegląd szacowanego kosztu miesięcznego

Ten widok przedstawia łączne koszty zasobów obliczeniowych i magazynowych w przypadku korzystania z maszyn wirtualnych na platformie Azure oraz szczegóły dla poszczególnych maszyn.

![Ocena gotowości](./media/contoso-migration-assessment/azure-costs.png)

- Koszty są szacowane na podstawie zalecanego rozmiaru maszyny.
- Szacowany miesięczny koszt zasobów obliczeniowych i magazynowych jest agregowany dla wszystkich maszyn wirtualnych w grupie.


## <a name="clean-up-after-assessment"></a>Czyszczenie po ocenie

- Po zakończeniu oceny Contoso zachowuje urządzenia Azure migracji dla przyszłych oceny.
- Wyłącz one maszyny Wirtualnej VMware. One będzie uruchomić ją ponownie po ich oceny kolejnych maszyn wirtualnych.
- Będzie prowadzą Contoso migrację na platformie Azure.  Obecnie jest wdrożona w grupie zasobów ContosoFailoverRG Wschodnia nam Azure obszaru.
-  Maszyna wirtualna modułu zbierającego ma licencji 180-dniowa wersja ewaluacyjna. Po przekroczeniu tego limitu program muszą pobrać i skonfigurować moduł zbierający ponownie.


## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu Contoso ocenie swojej bazy danych aplikacji SmartHotel, korzystając z narzędzia DMA i lokalnych maszyn wirtualnych przy użyciu usługi Azure migracji. Następnie przeglądane ich oceny w celu upewnienia się, że zasoby lokalne są gotowe do migracji do usługi Azure.

## <a name="next-steps"></a>Kolejne kroki

W artykule w tej serii Contoso rehostowaniu jego SmartHotel aplikacji na platformie Azure z migracją przyrostu shift. Contoso migruje frontonu WEBVM aplikacji przy użyciu usługi Azure Site Recovery i bazy danych aplikacji do usługi Azure SQL zarządzane wystąpienia, korzystanie z usługi migracji bazy danych. [Rozpoczynanie pracy](contoso-migration-rehost-vm-sql-managed-instance.md) z tego wdrożenia.
