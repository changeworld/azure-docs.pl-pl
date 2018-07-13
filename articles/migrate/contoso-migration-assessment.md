---
title: Ocena obciążeń lokalnych pod kątem migracji firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ocenia ich maszyn lokalnych do migracji na platformę Azure za pomocą usługi Azure Migration i migrację bazy danych
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006570"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migracja Contoso: ocena obciążeń lokalnych pod kątem migracji na platformę Azure

W tym artykule pokazano, jak Contoso ocenia swojej aplikacji SmartHotel w środowisku lokalnym, w ramach przygotowania do jego migracji na platformę Azure.

Ten dokument jest trzeci odcinek serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje, a szereg scenariuszy wdrażania, które ilustrują sposób konfigurowania infrastruktury migracji, ocenić przydatność, korzystając z zasobów lokalnych do migracji, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększanie się stopnia skomplikowania i będziemy dodawać dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji. | Dostępne
Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure  | Pokazuje, jak firmy Contoso jest wykonywany oceny aplikacji SmartHotel dwuwarstwowej lokalnych działających w oprogramowaniu firmy VMware. Contoso ocenia maszynach wirtualnych usługi app przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | W tym artykule.
[Artykuł 4: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak firmy Contoso jest wykonywany migracji lift-and-shift na platformie Azure dla lokalnych aplikacji SmartHotel. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzanego SQL, przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację aplikacji SmartHotel maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i programu SQL Server zawsze włączona grupa dostępności](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do klastra programu SQL Server chronione przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest lift-and-shift migracji aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu funkcji odzyskiwania lokacji i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzacja aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne
[Artykuł 11: Refaktoryzacja TFS w usłudze VSTS](contoso-migration-tfs-vsts.md) | Pokazuje, jak Contoso migruje ich lokalnego wdrożenia Team Foundation Server (TFS), migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure. | Dostępne
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompilować aplikację na platformie Azure](contoso-migration-rebuild.md) | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | Dostępne


## <a name="overview"></a>Przegląd

Firma Contoso rozważa migrację na platformę Azure, chce uzyskać ocenę techniczną i finansową zorientować się, czy swoich lokalnych obciążeń są odpowiednie na potrzeby migracji do chmury. W szczególności zespół Contoso chce ocenić zgodność maszyn i baz danych do migracji oraz oszacować wydajność i koszty związane z hostowaniem zasobów na platformie Azure.

Turystyczną i lepiej zrozumieć technologii, więc chcesz ocenić dwa swoje aplikacje w środowisku lokalnym podsumowane w poniższej tabeli. Należy pamiętać, że ich ocenianych scenariusze migracji tej aplikacji rehost i Refaktoryzacja dla migracji. Dowiedz się więcej o rehosting i refaktoryzacji w [Omówienie migracji Contoso](contoso-migration-overview.md).

**Nazwa aplikacji** | **Platforma** | **Warstwy aplikacji** | **Szczegóły**
--- | --- | --- | ---
SmartHotel<br/><br/> Służy do zarządzania wymaganiami podróż firmy Contoso | Systemem Windows przy użyciu bazy danych programu SQL Server | Dwuwarstwowej aplikacji za pomocą frontonu platformy ASP.NET witryny sieci Web na jednej maszyny Wirtualnej (WEBVM) i program SQL Server uruchomiony na innej maszynie Wirtualnej (SQLVM) | Maszyny wirtualne są VMware, uruchomiona na hoście ESXi zarządzanym przez serwer vCenter.<br/><br/> Przykładowa aplikacja można pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Contoso usługi app Service Desk | W systemie Linux/Apache, za pomocą języka PHP MySQL (światła). | Dwuwarstwowej aplikacji za pomocą frontonu witryny sieci Web PHP na jednej maszynie Wirtualnej (OSTICKETWEB) i uruchomiona w innej maszyny Wirtualnej (OSTICKETMYSQL) z bazy danych MySQL | Aplikacja jest używana przez klienta usługi aplikacji do śledzenia problemów dla pracowników wewnętrznych i zewnętrznych klientów.<br/><br/> Przykładowa aplikacja można pobrać z [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Bieżącej architektury


Poniżej przedstawiono diagram przedstawiający bieżącej infrastruktury lokalnej firmy Contoso.

![Architektura firmy Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Firma Contoso ma jednego głównego centrum danych znajdujące się w mieście z nowego Jorku w wschodnich Stanach Zjednoczonych.
- Mają trzy dodatkowe gałęzie lokalne na terenie Stanów Zjednoczonych.
- Główne centrum danych jest połączony z Internetem za pomocą połączenia ethernet metro fiber (500 MB/s).
- Każda gałąź jest lokalnie połączony z Internetem, przy użyciu połączeń klasy biznesowej, przy użyciu tuneli IPSec sieci VPN, wróć do głównego centrum danych. Dzięki temu ich całej sieci, aby trwale połączony oraz optymalizuje łączności z Internetem.
- Główne centrum danych jest w pełni zwirtualizowany z oprogramowaniem VMware. Mają one dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez program vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami i serwery DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Kontrolery domeny w lokalnej gałęzi, uruchom na serwerach fizycznych.





## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, co firma chce osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie i w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla ich deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT musi być w reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: w miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.

## <a name="assessment-goals"></a>Cele oceny

Zespół chmury firmy Contoso ma przypięte dół cele dotyczące ich oceny migracji:

- Po migracji aplikacji na platformie Azure mają te same możliwości wydajności, jak obecnie w środowisku programu VMWare w środowisku lokalnym.  Przejście do chmury nie oznacza, że wydajność aplikacji jest mniej istotny.
- Firma Contoso potrzebuje do zrozumienia zgodność swojej aplikacji i baz danych z wymaganiami platformy Azure, a także ich opcje hostingu platformy Azure.
- Należy zminimalizować administracji bazą danych firmy Contoso, po aplikacje zostały przeniesione do chmury.  
- Firma Contoso chce zrozumienia nie tylko opcje migracji, ale także koszty związane z infrastrukturą, po przeniesieniu do chmury.

## <a name="assessment-tools"></a>Narzędzia do oceny
Contoso korzysta z narzędzi firmy Microsoft do oceny. Te narzędzia dostosowanie ich celów oraz dostarczają informacje, które są im potrzebne.

**Technologia** | **Opis** | **Koszty**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Użyjemy ich DMA do oceny i wykrycia problemów ze zgodnością, które mogą mieć wpływ na ich funkcje bazy danych na platformie Azure. Program DMA ocenia równoważność funkcji między źródłami SQL i elementy docelowe i zaleca wydajności i niezawodności. | Narzędzie to można pobrać bezpłatnie.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso będą używać tej usługi do oceny maszyn wirtualnych VMware. Ocenia ona kwalifikowanie się maszyn do migracji na platformę Azure. Oprócz tego przedstawia szacunki dotyczące rozmiarów i kosztów maszyn uruchamianych na platformie Azure.  | Brak aktualnie może być 2018 Brak opłat za korzystanie z tej usługi.
[Mapa usługi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Usługa Azure Migrate przedstawia zależności między maszynami, które chcesz migrować, za pomocą rozwiązania Service Map, |  które jest częścią usługi Azure Log Analytics. Obecnie można go używać bezpłatnie przez 180 dni.

W tym scenariuszu firma Contoso pobiera i uruchamia program DMA do oceny lokalnej bazy danych programu SQL Server dla swoich aplikacji turystycznej. Używają usługi Azure migrate z mapowaniem zależności, aby ocenić maszyny wirtualne aplikacji przed migracją na platformę Azure.



## <a name="assessment-architecture"></a>Architekturę do oceny


![architekturę do oceny migracji](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Firma Contoso ma nazwę fikcyjne reprezentujących typowe przedsiębiorstwo.
- Firma Contoso ma lokalne centrum danych (**contoso-datacenter**), z kontrolerami domeny w środowisku lokalnym (CONTOSODC1, CONTOSODC2).
- Maszyny wirtualne VMware znajdują się na VMware ESXI hostach z systemem w wersji 6.5. Hosty: **contosohost1**, **contosohost2**
- Środowisko VMware jest zarządzane przez serwer vCenter 6.5 (**venter**uruchomiony na maszynie Wirtualnej.
- SmartHotel aplikacji turystycznej:
    - Aplikacja jest warstwowego na dwie maszyny wirtualne VMware, **WEBVM** i **SQLVM**.
    - Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com**.
    - Maszyny wirtualne działają w systemie Windows Server 2008 R2 Datacenter z dodatkiem SP1.
- Środowisko VMware jest zarządzane przez program vCenter Server (**vcenter.contoso.com**) uruchomiony na maszynie wirtualnej.
- OSTicket usługi aplikacji działu:
    - Aplikacja jest warstwowa między dwiema maszynami wirtualnymi, **OSTICKETWEB** i **OSTICKETMYSQL**.
    - Maszyny wirtualne są uruchomione na serwerze Linux Ubuntu 16.04-LTS.
    - Maszyna wirtualna OSTICKETWEB jest uruchomiona, Apache 2 oraz środowisko PHP 7.0.
    - Maszyna wirtualna OSTICKETMYSQL działa MySQL 5.7.22.

![Architektura](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono Contoso (i) wymaga oceny:

- Dostęp współautora lub właściciela subskrypcji platformy Azure lub grupy zasobów w subskrypcji platformy Azure.
- Lokalny program vCenter Server w wersji 5.5, 6.0 lub 6.5.
- Konto tylko do odczytu w programie vCenter Server lub uprawnienia do utworzenia takiego konta.
- Uprawnienia do utworzenia maszyny wirtualnej w programie vCenter Server przy użyciu szablonu .OVA.
- Co najmniej jeden host ESXi w wersji 5.0 lub nowszy.
- Co najmniej dwie lokalne maszyny wirtualne VMware, w tym jedna z uruchomioną bazą danych programu SQL Server.
- Uprawnienia do instalowania agentów usługi Azure Migrate na każdej maszynie Wirtualnej.
- Maszyny wirtualne powinny mieć bezpośrednie połączenie z Internetem.
        – Możesz ograniczyć dostęp do Internetu do [wymaganych adresów URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Jeśli maszyn bez łączności internetowej [bramy pakietu OMS](../log-analytics/log-analytics-oms-gateway.md) konieczne jest zainstalowanie na nich.
- Nazwa FQDN maszyny wirtualnej z uruchomionym wystąpieniem programu SQL Server, używana do oceny bazy danych.
- Zapora systemu Windows uruchomiona na maszynie wirtualnej programu SQL Server powinna zezwalać na połączenia zewnętrzne na porcie TCP 1433 (domyślnym), aby umożliwić nawiązanie połączenia przez program DMA.


## <a name="assessment-overview"></a>Przegląd oceny

Oto jak Contoso zamierza wykonać oceny:


> [!div class="checklist"]
> * **Krok 1: Pobierz i zainstaluj program DMA**: Przygotujemy program DMA do oceny lokalnej bazy danych programu SQL Server.
> * **Krok 2: Ocena bazy danych za pomocą programu DMA**: uruchamianie i analizowanie oceny bazy danych.
> * **Krok 3: Przygotowywanie oceny maszyn wirtualnych za pomocą usługi Azure Migrate**: ustawienia lokalne konta i dopasujemy VMware.
> * **Krok 4. odnajdywanie lokalnych maszyn wirtualnych za pomocą usługi Azure Migrate**: Tworzenie usługi Azure Migrate maszyna wirtualna modułu zbierającego. Następnie uruchom moduł zbierający w celu odnalezienia maszyn wirtualnych do oceny.
> * **Krok 5: Przygotowywanie analizy zależności za pomocą usługi Azure Migrate**: Zainstaluj usługę Azure Migrate agentów na maszynach wirtualnych, dzięki czemu użytkownicy widzą mapowanie zależności między maszynami wirtualnymi.
> * **Krok 6: Oceny maszyn wirtualnych za pomocą usługi Azure Migrate**: Sprawdź zależności, grupy maszyn wirtualnych i uruchomimy ocenę. Gdy ocena będzie gotowa, ich analizować w ramach przygotowania do migracji.


## <a name="step-1-download-and-install-the-dma"></a>Krok 1: Pobieranie i instalowanie programu DMA

1. Contoso — pliki do pobrania program DMA z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Asystent można zainstalować na dowolnym komputerze mającym połączenie z wystąpieniem serwera SQL. Nie musisz go uruchamiać na maszynie programu SQL Server.
    - Nie można uruchomić na maszynie hosta programu SQL Server.
2. Działają one pobrany plik Instalatora (DownloadMigrationAssistant.msi), aby rozpocząć instalację.
3. Na **Zakończ** strony, wybierają **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Krok 2: Uruchamianie i analizowanie oceny bazy danych dla SmartHotel

Contoso można teraz uruchomić ocenę, aby przeanalizować ich na lokalnym serwerze SQL Server dla aplikacji SmartHotel.

1. W Asystencie migracji bazy danych kliknij przycisk **New**, wybierz opcję **oceny**i podaj nazwę projektu — oceny **SmartHotel**.
2. Wybierają **typ serwera źródła** jako **programu SQL Server na maszynach wirtualnych Azure**.

    ![Wybierz źródło](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      Aktualnie program DMA nie obsługuje oceny migracji na wystąpienie zarządzane SQL. Obejść ten problem firma Contoso używa programu SQL Server na maszynie Wirtualnej platformy Azure jako tymczasowego obiektu docelowego dla oceny.

3. W **Wybierz docelową wersję**, wybierają program SQL Server 2017 w wersji docelowej. Należy wybrać tę opcję, ponieważ jest to wersja używane przez wystąpienie zarządzane usługi SQL.
4. Należy wybrać, aby znaleźć informacje o zgodności oraz nowe funkcje:
    - **Problemy ze zgodnością** należy pamiętać, zmiany, które mogą uniemożliwić migrację lub które wymagają drobnych korekt przed migracją. Przechowuje informuje o funkcjach aktualnie w użyciu, które zostały wycofane. Problemy są uporządkowane według poziomu zgodności.
    - **Rekomendowane nowe funkcje** informacje o nowych funkcjach w docelowej platformy SQL Server, który może służyć do bazy danych po zakończeniu migracji. Są one uporządkowane według wydajności, zabezpieczeń i magazynu.

    ![Wybieranie obiektu docelowego](./media/contoso-migration-assessment/dma-assessment-2.png)

2. W **nawiązywanie połączenia z serwerem**, użytkownik podał nazwę maszyny Wirtualnej uruchomionej bazy danych i poświadczenia dostępu do niego. Należy włączyć **certyfikat serwera zaufania** się upewnić, że będą oni mogli uzyskać do programu SQL Server. A następnie kliknięciu **Connect**.

    ![Wybieranie obiektu docelowego](./media/contoso-migration-assessment/dma-assessment-3.png)

3. W **Dodaj źródło**, co zwiększa bazy danych chcą oceny, a następnie kliknij przycisk **dalej** Aby uruchomić ocenę.
4. Ocena jest tworzony.

    ![Tworzenie oceny](./media/contoso-migration-assessment/dma-assessment-4.png)

5. W **wyników przeglądu**, ich można zobaczyć wyniki oceny.


### <a name="analyze-the-database-assessment"></a>Analizowanie oceny bazy danych

Wyniki są wyświetlane, gdy są one dostępne. Jeśli ich rozwiązywanie problemów dotyczących muszą oni kliknij **Uruchom ponownie ocenę** ponownie uruchomić ocenę.

1. W **problemy ze zgodnością** raportu, sprawdź wszystkie problemy na każdym poziomie zgodności. Poziomy zgodności są mapowane na wersje programu SQL Server w następujący sposób:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problemy ze zgodnością](./media/contoso-migration-assessment/dma-assessment-5.png)

2. W **Rekomendowaną** zgłosić, Contoso, można wyświetlić funkcje wydajności, zabezpieczeń i magazynu, jakie po migracji. Różne funkcje, zaleca się w tym przetwarzanie OLTP danych w pamięci i magazynu kolumn, Stretch Database, Always Encrypted, dynamiczne maskowanie danych i przezroczystego szyfrowania danych (TDE).

    ![Zalecenia dotyczące funkcji](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Zaleca się Contoso [włącza funkcję TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) dla wszystkich programu SQL Server baz danych, jest to jeszcze ważniejsze w przypadku baz danych w chmurze. Funkcja TDE powinna być włączona dopiero po zakończeniu migracji. Jeśli funkcja TDE jest już włączony, należy przenieść certyfikatu lub klucza asymetrycznego z główną bazą danych serwera docelowego. [Dowiedz się więcej](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Mogą one eksportować oceny w formacie JSON lub CSV.

Należy zwrócić uwagę na to, że jeśli uruchamiasz ocenę w większej skali można:

- Uruchamiać wiele ocen jednocześnie i wyświetlić stan ocen, otwierając **wszystkich ocen** strony.
- [Konsolidować oceny w bazie danych programu SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Konsolidować oceny w raporcie usługi Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Krok 3: Przygotowywanie oceny maszyn wirtualnych za pomocą usługi Azure Migrate

Contoso potrzebuje Utwórz konto VMware, używanego przez usługę Azure Migrate spowoduje automatyczne odnajdywanie maszyn wirtualnych do oceny, weryfikowanie uprawnień do utworzenia maszyny Wirtualnej, zwróć uwagę na porty, które muszą być otwarte i ustaw ustawienia poziomu statystyk.

### <a name="set-up-a-vmware-account"></a>Konfigurowanie konta VMware

 Odnajdywanie maszyny Wirtualnej wymaga konta tylko do odczytu w programie vCenter z następującymi właściwościami:

- Typ użytkownika: co najmniej użytkownik tylko do odczytu.
- Uprawnienia: obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu.
- Szczegóły: użytkownik przypisany na poziomie centrum danych z dostępem do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

### <a name="verify-permissions-to-create-a-vm"></a>Weryfikowanie uprawnień do utworzenia maszyny wirtualnej

Contoso — sprawdza, czy ma uprawnienia do tworzenia maszyny Wirtualnej, importując plik w. OVA format. [Dowiedz się więcej](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Weryfikowanie portów

Ocena Contoso używa mapowanie zależności. Ta funkcja wymaga zainstalowania na maszynach wirtualnych, którą chcesz ocenić agenta. Agent musi być w stanie połączyć się z platformy Azure przez port TCP 443 na każdej maszynie Wirtualnej. [Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) o wymaganiach dotyczących połączenia.


### <a name="set-statistics-settings"></a>Ustawianie statystyk

Przed rozpoczęciem wdrażania, Contoso należy ustawić ustawienia statystyk programu vCenter Server na poziom 3. Należy pamiętać, że:

- Po ustawieniu poziomu, musisz poczekać co najmniej dzień, zanim uruchomisz ocenę. W przeciwnym razie może ona nie działać zgodnie z oczekiwaniami.
- Jeśli zostanie ustawiony poziom wyższy niż 3, ocena będzie działać w następujący sposób:
    - Nie będą zbierane dane wydajności dysków ani sieci.
    - W przypadku magazynu usługa Azure Migrate zaleca standardowy dysk na platformie Azure, o takim samym rozmiarze co dysk lokalny.
    - W przypadku sieci zaleca się utworzenie karty sieciowej na platformie Azure dla każdej lokalnej karty sieciowej.
    - W przypadku obliczeń usługa Azure Migrate uwzględnia liczbę rdzeni oraz rozmiar pamięci i zaleca utworzenie maszyny wirtualnej platformy Azure o takiej samej konfiguracji. Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.
- [Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) o rozmiarach na poziomie 3.

One Ustaw poziom w następujący sposób:

1. W kliencie internetowym vSphere one Otwórz wystąpienie programu vCenter server.
2. W **Zarządzaj** > **ustawienia** > **ogólne**, klikając **Edytuj**.
3. W **statystyki**, ustawiają statystyki ustawienia poziomu **Level 3**.

    ![Poziom statystyki programu vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Krok 4: Odnajdywanie maszyn wirtualnych

Do odnalezienia maszyn wirtualnych, Contoso utworzy projekt usługi Azure Migrate. Ich pobieranie i konfigurowanie maszyny Wirtualnej modułu zbierającego oraz uruchom moduł zbierający, aby odnaleźć swoich lokalnych maszyn wirtualnych.

### <a name="create-a-project"></a>Tworzenie projektu

1. W [witryny Azure portal](https://portal.azure.com), wyszukiwania **usługi Azure Migrate**i Utwórz projekt (ContosoMigration).
2. Określ nazwę projektu, a subskrypcja platformy Azure i Utwórz nową grupę zasobów platformy Azure, **ContosoFailoverRG**. Należy pamiętać, że:

    - Projekt usługi Azure Migrate można utworzyć tylko w regionie Zachodnio-środkowe stany USA lub Wschodnie stany USA.
    - Migrację możesz zaplanować dla dowolnej lokalizacji docelowej.
    - Lokalizacja projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Ta maszyna wirtualna służy do odnajdowania lokalnych maszyn wirtualnych VMware i wysyłania dotyczących ich metadanych do usługi Azure Migrate. Aby skonfigurować urządzenie modułu zbierającego, pliki do pobrania firmy Contoso. OVA szablonu, a następnie importuje go do lokalnego serwera vCenter do utworzenia maszyny Wirtualnej.

1. W projekcie usługi Azure Migrate > **wprowadzenie** > **Odnajdź i oceń** > **odnajdź maszyny**, będą oni mogli pobrać. Plik szablonu OVA.
2. Skopiuj ich identyfikator i klucz projektu. Są one wymagane do skonfigurowania modułu zbierającego.

    ![Pobieranie pliku OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Przed wdrożeniem maszyny Wirtualnej, Contoso sprawdza, czy. Plik OVA jest bezpieczne.

1. Na komputerze, do którego pobrano plik ich Otwórz okno wiersza polecenia administratora.
2. One uruchom następujące polecenie, aby wygenerować skrót pliku ova:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Wygenerowany skrót powinien odpowiadać następującym ustawieniom (wersja 1.0.9.12)

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>Tworzenie urządzenia modułu zbierającego

Teraz Contoso można zaimportować pobrany plik na serwer vcenter i aprowizuj serwer konfiguracji maszyny Wirtualnej.

1. W konsoli klienta vSphere kliknij pozycję **pliku** > **wdrażania szablonu OVF**.

    ![Wdrażanie pliku OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. W Kreatorze wdrażania szablonu OVF > **źródła**, określają lokalizację. Plik OVA.
3. W **nazwę i lokalizację**, określają przyjazną nazwę maszyny Wirtualnej modułu zbierającego i lokalizacji magazynu, w którym będzie hostowana maszyna wirtualna. Określają one również host lub klaster, na którym będzie działać urządzenie modułu zbierającego.
5. W **magazynu**, określają lokalizację magazynu, a następnie w **Format dysku**, jak mają być aprowizacji magazynu.
7. W **mapowanie sieci**, określają sieci, z którą połączy się maszyna wirtualna modułu zbierającego. Sieć musi mieć połączenie z Internetem w celu wysyłania metadanych do platformy Azure.
8. Przejrzyj ustawienia i wybierz **Włącz po wdrożeniu**> **Zakończ**. Po utworzeniu urządzenia pojawi się komunikat potwierdzający pomyślne zakończenie operacji.

### <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Teraz uruchom moduł zbierający w celu odnalezienia maszyn wirtualnych. Należy pamiętać, że moduł zbierający aktualnie obsługuje tylko "Angielski (Stany Zjednoczone)" jako język systemu operacyjnego i język interfejsu modułu zbierającego.

1. W przypadku klienta vSphere konsoli > **Otwórz konsolę**, określają, języka, strefy czasowej i preferencje hasła dla maszyny Wirtualnej modułu zbierającego.
2. Na pulpicie kliknij przycisk **uruchom moduł zbierający** skrótów.

    ![Skrót do modułu zbierającego](./media/contoso-migration-assessment/collector-shortcut.png)

4. W usłudze Azure Migrate Collector > **Skonfiguruj wymagania wstępne**, zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
5. Moduł zbierający sprawdzi, czy maszyna wirtualna ma dostęp do Internetu, czy czas jest zsynchronizowany, i czy działa usługa modułu zbierającego (instalowana domyślnie na maszynie Wirtualnej). Ponadto moduł zbierający instaluje interfejs PowerCLI programu VMWare.

    > [!NOTE]
    > Zakłada się, że maszyna wirtualna ma bezpośredni dostęp do Internetu, bez serwera proxy.

    ![Weryfikowanie wymagań wstępnych](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. W **Określ szczegóły serwera vCenter**określają nazwę (FQDN) lub adres IP serwera vCenter i poświadczenia tylko do odczytu używane do odnajdywania.
7. Wybierają zakres odnajdowania maszyn wirtualnych. Moduł zbierający odnajdzie tylko maszyny wirtualne we wskazanym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Zakres nie powinien zawierać więcej niż 1500 maszyn wirtualnych.

    ![Nawiązywanie połączenia z serwerem vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. W **określ projekt migracji**, określają, usługa Azure Migrate identyfikator projektu i klucz, który został skopiowany z portalu. Można uzyskać je ponownie w projekcie **Przegląd** strony > **odnajdź maszyny**.  

    ![Nawiązywanie połączenia z usługą Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. W **Wyświetl postęp zbierania** Contoso może monitorować proces odnajdowania i sprawdź, że metadane zbierane z maszyn wirtualnych znajduje się w zakresie. Moduł zbierający informuje o szacowanym czasie odnajdowania.

    ![Zbieranie w toku](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu zbierania, Contoso sprawdza, czy maszyny wirtualne są wyświetlane w portalu.

1. W projekcie usługi Azure Migrate > **Zarządzaj** > **maszyn**, sprawdź, czy są wyświetlane maszyn wirtualnych, które miały zostać odnalezione.

    ![Odnalezione maszyny](./media/contoso-migration-assessment/discovery-complete.png)

3. Zwróć uwagę na to, że maszyny nie mają zainstalowanych agentów usługi Azure Migrate. Firma Contoso potrzebuje na zainstalowanie ich w zależności do wyświetlenia.

    ![Odnalezione maszyny](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Krok 5: Przygotowywanie analizy zależności

Aby wyświetlić zależności między maszynami wirtualnymi, które Contoso chce uzyskać dostęp, Pobierz i zainstaluj agentów na maszynach wirtualnych aplikacji. Contoso dzieje na wszystkich maszynach wirtualnych dla swoich aplikacji, systemów Windows i Linux.

### <a name="take-a-snapshot"></a>Tworzenie migawki

Przed zmodyfikowaniem je, wykonując migawkę przed zainstalowaniem agentów są one przechowywać kopię maszyny Wirtualnej.

![Migawka maszyny](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

1. Na **maszyn** stronie one wybierz maszynę, a następnie **wymaga instalacji** w **zależności** kolumny.
2. Na **odnajdź maszyny** strony są wykonaj następujące czynności:
    - Pobierz agenta MMA i zależności dla każdej maszyny Wirtualnej Windows
    - Pobierz agenta MMA i zależności dla każdej maszyny Wirtualnej systemu Linux
3. Skopiuj one teraz identyfikator obszaru roboczego i klucz. Są one potrzebne podczas instalowania programu MMA.

    ![Pobieranie agenta](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Zainstaluj agentów na maszynach wirtualnych Windows

Działają one instalacji na każdej maszynie Wirtualnej.

#### <a name="install-the-mma-on-windows-vms"></a>Zainstaluj program MMA na maszynach wirtualnych Windows

1. One kliknij dwukrotnie pobranego agenta.
2. W **Folder docelowy**, ich pozostaw domyślny folder instalacji > **dalej**.
2. W **opcje instalacji agenta**, wybierają **Połącz agenta z usługą Azure Log Analytics** > **dalej**.

    ![Instalacja programu MMA](./media/contoso-migration-assessment/mma-install.png)

5. W **usługi Azure Log Analytics**, ich Wklej identyfikator obszaru roboczego i klucz, który został skopiowany z portalu.

    ![Instalacja programu MMA](./media/contoso-migration-assessment/mma-install2.png)

6. W **gotowy do instalacji**, teraz można zainstalować programu MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Zainstaluj agenta zależności na maszynach wirtualnych Windows

1. One kliknij dwukrotnie pobranego agenta zależności.
2. One zaakceptuj postanowienia licencyjne i poczekaj na zakończenie instalacji.

    ![Agent zależności](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Instalowanie agentów na maszynach wirtualnych z systemem Linux

Działają one instalacji na każdej maszynie Wirtualnej.

#### <a name="install-the-mma-on-linux-vms"></a>Zainstaluj program MMA na maszynach wirtualnych z systemem Linux

1. Instalacji w każdej maszyny Wirtualnej przy użyciu biblioteki ctypes python: **"sudo" polecenia apt-get zainstalowania języka python ctypeslib**.
2. Powinno być ono uruchomione polecenie, aby zainstalować agenta MMA jako użytkownik główny.  Przestanie głównego uruchom następujące polecenie i wprowadź hasło główne: **"sudo" -i**.
3. Po instalacji agenta MMA.
    - Wstaw swoje poprawny identyfikator i klucz obszaru do polecenia.
    - Polecenia są dla 64-bitowych.
    - **Identyfikator obszaru roboczego** i **klucza podstawowego** można znaleźć w portalu pakietu OMS > **ustawienia**w **połączone źródła** kartę.
    - Uruchom następujące polecenia, aby pobrać agenta pakietu OMS, należy zweryfikować sumy kontrolnej i instalacja/dołączanie agenta.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instalowanie agenta zależności na maszynach wirtualnych z systemem Linux

Po zainstalowaniu programu MMA Contoso można zainstalować agenta zależności na maszynach wirtualnych z systemem Linux.

1. Agent zależności jest instalowany na komputerach z systemem Linux przy użyciu Linux64.bin InstallDependencyAgent, skrypt powłoki, za pomocą samorozpakowujący się plik binarny. Można uruchomić plik przy użyciu sh lub dodać uprawnienia do wykonywania do pliku sam.

2. Jako katalog główny instalacji agenta zależności systemu Linux:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Krok 6: Uruchamianie i analizowanie oceny maszyny Wirtualnej

Firma Contoso może teraz Sprawdź zależności maszyny i Utwórz grupę. Następnie uruchom ocenę grupy.

### <a name="verify-dependencies-and-create-a-group"></a>Sprawdzanie zależności i tworzenie grupy


1. Dla maszyn do analizowania, kliknij przycisk **wyświetlanie zależności**.

    ![Wyświetlanie zależności maszyn](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. W przypadku maszyny SQLVM mapa zależności zawiera następujące informacje:

    - Grupy procesów/procesy z aktywnymi połączeniami sieciowymi uruchomione na maszynie SQLVM w określonym przedziale czasu (domyślnie przez godzinę)
    - Połączenia przychodzące TCP (klienta) do wszystkich zależnych maszyn i połączenia wychodzące TCP (serwera) z wszystkich zależnych maszyn.
    - Maszyny zależne z zainstalowanymi agentami usługi Azure Migrate są wyświetlane jako osobne pola
    - W przypadku maszyn bez zainstalowanych agentów są wyświetlane informacje o porcie i adresie IP.

3. W przypadku maszyn z zainstalowanym agentem (WEBVM) ich kliknij pole maszyny, aby wyświetlić więcej informacji, w tym nazwę FQDN, system operacyjny i adres MAC.

    ![Wyświetlanie zależności grupowych](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Teraz wybierz maszyny wirtualne można dodać do grupy (SQLVM i WEBVM).  Mogą one użyj kombinacji klawisza CTRL i kliknij, aby wybrać wiele maszyn wirtualnych.
5. Polecenie **Utwórz grupę**, a następnie określ nazwę (smarthotelapp).

> [!NOTE]
    > Aby wyświetlić bardziej szczegółowe zależności, możesz rozszerzyć zakres czasu. Możesz wybrać przedział lub datę początkową i datę końcową.


### <a name="run-an-assessment"></a>Uruchamianie oceny


1. Na **grup** strony, otwórz grupę (smarthotelapp) i kliknij przycisk **Utwórz ocenę**.

    ![Tworzenie oceny](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Ocena pojawi się na stronie **Zarządzaj** > **Oceny**.

Contoso używane z domyślnych ustawień oceny, ale można dostosować ustawienia. [Dowiedz się więcej](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analizowanie oceny maszyny wirtualnej

Ocena usługi Azure Migrate zawiera informacje dotyczące zgodności z lokalnych maszyn wirtualnych na platformie Azure, sugerowanych rozmiarów maszyny wirtualnej platformy Azure oraz szacowanych miesięcznych kosztów platformy Azure.

![Raport z oceny](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

![Wyświetlanie oceny](./media/contoso-migration-assessment/assessment-display.png)

Ocena pobiera ocena zaufania od 1 gwiazdki do 5 gwiazdek (1 gwiazdka to najniższa i 5 gwiazdek — najwyższa).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Pomaga to oszacować niezawodność zaleceń dotyczących rozmiarów, udostępnianych przez usługę Azure Migrate.
- Ocena zaufania jest przydatna podczas ustalania *ustalania rozmiaru na podstawie wydajności* zgodnie z usługi Azure Migrate może nie mieć wystarczającej liczby punktów danych, do ustalenia rozmiaru na podstawie użycia. W przypadku *ustalania rozmiaru jako lokalnego* ocena zaufania to zawsze 5 gwiazdek, ponieważ usługa Azure Migrate ma wszystkie punkty danych, których potrzebuje do ustalenia rozmiaru maszyny wirtualnej.
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


## <a name="clean-up-after-assessment"></a>Wyczyść zasoby po ukończeniu oceny

- Po zakończeniu oceny Contoso zachowuje urządzenia usługi Azure Migration dla przyszłych ocen.
- Wyłącz ich maszyn wirtualnych VMware. One będzie ponownego uruchomienia po ich oceny dodatkowych maszyn wirtualnych.
- Oni będzie na bieżąco projektu migracji firmy Contoso na platformie Azure.  Trwa wdrażanie w grupie zasobów ContosoFailoverRG w regionie wschodnie stany USA Azure.
-  Maszyna wirtualna modułu zbierającego ma licencji 180-dniowej wersji ewaluacyjnej. Jeśli upłynie limit, ich musisz Pobierz i skonfiguruj moduł zbierający ponownie.


## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu firma Contoso ocenie swojej bazy danych aplikacji SmartHotel, przy użyciu narzędzia DMA i lokalnych maszyn wirtualnych przy użyciu usługi Azure Migrate. Następnie sprawdzono ich ocen w celu upewnienia się, że lokalne zasoby są gotowe do migracji na platformę Azure.

## <a name="next-steps"></a>Kolejne kroki

W następnym artykule w tej serii Contoso rehostowaniu jego SmartHotel aplikacji na platformie Azure przy użyciu migracji lift-and-shift. Contoso migruje frontonu WEBVM dla aplikacji przy użyciu usługi Azure Site Recovery i aplikacji bazy danych do wystąpienia zarządzanego Azure SQL, przy użyciu usługi migracji bazy danych. [Rozpoczynanie pracy](contoso-migration-rehost-vm-sql-managed-instance.md) z tym wdrożeniem.
