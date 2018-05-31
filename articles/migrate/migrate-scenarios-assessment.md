---
title: Ocenianie gotowości obciążeń lokalnych do migracji na platformę Azure za pomocą programu DMA i usługi Azure Migrate | Microsoft Docs
description: Dowiedz się, jak przy użyciu programu Data Migration Assistant (DMA) i usługi Azure Migrate przygotować platformę Azure do migracji maszyn lokalnych.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 0d8ef36e001aaf417b84efaf99a992fd64f01b6f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366345"
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>Scenariusz 1. Ocena obciążeń lokalnych pod kątem migracji na platformę Azure

Firma Contoso rozważa migrację na platformę Azure. W związku z tym chce uzyskać ocenę techniczną i finansową dotyczącą gotowości obciążeń lokalnych do migracji do chmury. W szczególności firma ta chce ocenić zgodność maszyn i baz danych pod kątem migracji oraz oszacować wydajność i koszty związane z hostowaniem zasobów na platformie Azure.

Aby zrobić pierwszy krok i zapoznać się z nowymi technologiami, firma Contoso zamierza ocenić i zmigrować małą lokalną aplikację turystyczną. Jest to aplikacja dwuwarstwowa, obejmująca aplikację internetową uruchomioną na jednej maszynie wirtualnej oraz bazę danych programu SQL Server, hostowaną na drugiej maszynie wirtualnej. Aplikacja jest wdrożona w oprogramowaniu VMware, a środowisko jest zarządzane za pomocą programu vCenter Server. Ocena zostanie przeprowadzona przy użyciu programu Data Migration Assistant (DMA) i usługi Azure Migrate. Jeśli chcesz wypróbować ten scenariusz przy użyciu tej ilustracyjnej aplikacji turystycznej, możesz pobrać ją z serwisu [github](https://github.com/Microsoft/SmartHotel360).

**Technologia** | **Opis** | **Koszty**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Program DMA wykrywa problemy ze zgodnością, które mogą mieć wpływ na funkcjonalność bazy danych na platformie Azure. Ponadto ocenia on równoważność funkcji między obiektem źródłowym i obiektem docelowym programu SQL Server oraz udostępnia zalecenia mające na celu zwiększenie wydajności i niezawodności w środowisku docelowym. | Narzędzie to można pobrać bezpłatnie.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Usługa ta ułatwia ocenę maszyn lokalnych pod kątem migracji na platformę Azure. Ocenia ona kwalifikowanie się maszyn do migracji na platformę Azure. Oprócz tego przedstawia szacunki dotyczące rozmiarów i kosztów maszyn uruchamianych na platformie Azure. Aktualnie usługa Azure Migrate może ocenić gotowość lokalnych maszyn wirtualnych VMware do migracji na platformę Azure. | Obecnie (kwiecień 2018 r) z usługi tej można korzystać bezpłatnie.
[Mapa usługi](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Usługa Azure Migrate przedstawia zależności między maszynami, które chcesz migrować, za pomocą rozwiązania Service Map, |  które jest częścią usługi Azure Log Analytics. Obecnie można go używać bezpłatnie przez 180 dni.

W tym scenariuszu pobierzemy i uruchomimy program DMA w celu oceny lokalnej bazy danych programu SQL Server dla aplikacji turystycznej. Za pomocą usługi Azure Migrate z mapowaniem zależności ocenimy maszyny wirtualne aplikacji przed ich zmigrowaniem na platformę Azure.

> [!NOTE]
> W tym scenariuszu obiektem docelowym oceny dla bazy danych będzie „program SQL Server na maszynie wirtualnej platformy Azure”. Jednak w kolejnym artykule przeprowadzimy migrację do wystąpienia zarządzanego Azure SQL. Zrobimy tak, ponieważ aktualnie program DMA nie obsługuje oceny obiektu docelowego wystąpienia zarządzanego Azure SQL.

## <a name="architecture"></a>Architektura


![architekturę do oceny migracji](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

W tym scenariuszu:
- Firma Contoso to fikcyjna nazwa reprezentująca typową organizację przedsiębiorstwa. Firma Contoso chce ocenić dwuwarstwową, lokalną aplikację turystyczną, a następnie przeprowadzić jej migrację.
- Firma Contoso ma lokalne centrum danych (**contoso-datacenter**) i lokalny kontroler domeny (**contosodc1**).
- Wewnętrzna aplikacja turystyczna jest podzielona na warstwy między dwie maszyny wirtualne — **WEBVM** i **SQLVM** — i znajduje się na hoście VMware ESXi **contosohost1.contoso.com**.
- Środowisko VMware jest zarządzane przez program vCenter Server (**vcenter.contoso.com**) uruchomiony na maszynie wirtualnej.




## <a name="prerequisites"></a>Wymagania wstępne

Oto, czego potrzebujesz, aby wdrożyć ten scenariusz:

- Lokalny program vCenter Server w wersji 5.5, 6.0 lub 6.5.
- Konto tylko do odczytu w programie vCenter Server lub uprawnienia do utworzenia takiego konta.
- Uprawnienia do utworzenia maszyny wirtualnej w programie vCenter Server przy użyciu szablonu .OVA.
- Co najmniej jeden host ESXi w wersji 5.0 lub nowszy.
- Co najmniej dwie lokalne maszyny wirtualne VMware, w tym jedna z uruchomioną bazą danych programu SQL Server.
- Musisz mieć uprawnienia do zainstalowania agentów usługi Azure Migrate na każdej maszynie wirtualnej.
- Maszyny wirtualne powinny mieć bezpośrednie połączenie z Internetem.
        – Możesz ograniczyć dostęp do Internetu do [wymaganych adresów URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        – W przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować na nich [bramę OMS](../log-analytics/log-analytics-oms-gateway.md).
- Nazwa FQDN maszyny wirtualnej z uruchomionym wystąpieniem programu SQL Server, używana do oceny bazy danych.
- Zapora systemu Windows uruchomiona na maszynie wirtualnej programu SQL Server powinna zezwalać na połączenia zewnętrzne na porcie TCP 1433 (domyślnym), aby umożliwić nawiązanie połączenia przez program DMA.


## <a name="scenario-overview"></a>Omówienie scenariusza

Oto, co chcemy zrobić:


> [!div class="checklist"]
> * **Krok 1. Przygotowywanie platformy Azure**. Potrzebna jest tylko subskrypcja platformy Azure.
> * **Krok 2. Pobieranie i instalowanie programu DMA**. Przygotujemy program DMA do oceny lokalnej bazy danych programu SQL Server.
> * **Krok 3. Ocena bazy danych**. Uruchomimy i przeanalizujemy ocenę bazy danych.
> * **Krok 4. Przygotowywanie oceny maszyn wirtualnych za pomocą usługi Azure Migrate**. Skonfigurujemy lokalne konta i dopasujemy ustawienia oprogramowania VMware.
> * **Krok 5. Odnajdywanie lokalnych maszyn wirtualnych**. Utworzymy maszynę wirtualną z modułem zbierającym usługi Azure Migrate. Następnie uruchomimy moduł zbierający w celu odnalezienia maszyn wirtualnych do oceny.
> * **Krok 6. Przygotowywanie analizy zależności**. Zainstalujemy agentów usługi Azure Migrate na maszynach wirtualnych, aby uwidocznić mapowanie zależności między maszynami wirtualnymi.
> * **Krok 7. Ocena maszyn wirtualnych**. Sprawdzimy zależności, pogrupujemy maszyny wirtualne i uruchomimy ocenę. Gdy ocena będzie gotowa, przeanalizujemy ją w ramach przygotowań do migracji.


## <a name="step-1-prepare-azure"></a>Krok 1. Przygotowywanie platformy Azure

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

- Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.
- Jeśli używasz istniejącej subskrypcji i nie jesteś jej administratorem, musisz skontaktować się z administratorem w celu uzyskania uprawnień właściciela lub współautora subskrypcji albo grupy zasobów używanej w tym scenariuszu.


## <a name="step-2-download-and-install-the-dma"></a>Krok 2. Pobieranie i instalowanie programu DMA

1. Pobierz program DMA z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=53595).
    - Asystenta możesz zainstalować na dowolnej maszynie, która może łączyć się z wystąpieniem SQL. Nie musisz go uruchamiać na maszynie programu SQL Server.
    - Nie należy go uruchamiać na maszynie hosta programu SQL Server.
2. Kliknij dwukrotnie pobrany plik instalatora (DownloadMigrationAssistant.msi), aby rozpocząć instalację.
3. Na stronie **Finish** (Zakończenie) upewnij się, że opcja **Launch Microsoft Data Migration Assistant** (Uruchom program Microsoft Data Migration Assistant) jest zaznaczona, a następnie kliknij przycisk **Finish** (Zakończ).

## <a name="step-3-run-and-analyze-the-database-assessment"></a>Krok 3. Uruchamianie i analizowanie oceny bazy danych

Uruchom ocenę, aby przeanalizować źródłowe wystąpienie programu SQL Server względem określonego obiektu docelowego.

1. W obszarze **New** (Nowy) wybierz pozycję **Assessment** (Ocena) i wpisz nazwę projektu oceny.
2. W obszarze **Source server type** (Typ serwera źródłowego) wybierz pozycję **SQL Server**. W obszarze **Target server type** (Typ serwera docelowego) wybierz pozycję **SQL Server on Azure Virtual Machines** (Program SQL Server na maszynach wirtualnych platformy Azure).

    ![Wybierz źródło](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      Aktualnie program DMA nie obsługuje oceny migracji na wystąpienie zarządzane SQL. Aby obejść ten problem, używamy programu SQL Server na maszynie wirtualnej platformy Azure jako tymczasowego obiektu docelowego dla oceny.

1.  W obszarze **Select Target Version** (Wybieranie wersji obiektu docelowego) wskaż docelową wersję programu SQL Server, który chcesz uruchomić na platformie Azure, i określ zakres odnajdywania w ocenie:
    - Opcja **Compatibility Issues** (Problemy ze zgodnością) informuje o zmianach, które mogą uniemożliwić migrację lub które wymagają drobnych korekt przed migracją. Informuje ona również, które z obecnie używanych funkcji zostały wycofane. Problemy są uporządkowane według poziomu zgodności.
    - Opcja **New features' recommendation** (Zalecenia dotyczące nowych funkcji) informuje o nowych funkcjach docelowej platformy programu SQL Server, które można wykorzystać w bazie danych po zakończeniu migracji. Są one uporządkowane według wydajności, zabezpieczeń i magazynu.

    ![Wybieranie obiektu docelowego](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. W obszarze **Connect to a server** (Łączenie z serwerem), określ nazwę maszyny z uruchomionym wystąpieniem programu SQL Server, typ uwierzytelniania i szczegóły połączenia. Następnie kliknij przycisk **Connect** (Połącz).

    ![Wybieranie obiektu docelowego](./media/migrate-scenarios-assessment/dma-assessment-3.png)

3. W obszarze **Add source** (Dodawanie źródła) wybierz bazę danych, którą chcesz ocenić, a następnie kliknij przycisk **Add** (Dodaj).
4. Zostanie utworzona ocena o podanej nazwie.

    ![Tworzenie oceny](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  Kliknij przycisk **Next** (Dalej), aby uruchomić ocenę.
6. W obszarze **Review Results** (Przegląd wyników) zobaczysz wyniki włączonych testów w ramach oceny.


### <a name="analyze-the-database-assessment"></a>Analizowanie oceny bazy danych

Gdy wyniki staną się dostępne, od razu pojawią się w programie Assistant.

1. W raporcie **problemów ze zgodnością** sprawdź, czy w bazie danych występują problemy na poszczególnych poziomach zgodności, oraz dowiedz się, jak je rozwiązać. Poziomy zgodności są mapowane na wersje programu SQL Server w następujący sposób:
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problemy ze zgodnością](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. W raporcie **zaleceń dotyczących funkcji** sprawdź, jakie funkcje wydajności, zabezpieczeń i magazynu powinny zostać skonfigurowane po zakończeniu migracji. Zalecenia obejmują różne funkcje, w tym przetwarzanie OLTP danych w pamięci i magazyn kolumn, dynamiczne maskowanie danych, Stretch Database, Always Encrypted i Transparent Data Encryption.

    ![Zalecenia dotyczące funkcji](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. Po rozwiązaniu problemów kliknij pozycję **Restart Assessment** (Uruchom ponownie ocenę), aby ponownie uruchomić ocenę.
4. Kliknij pozycję **Export report** (Eksportuj raport), aby wyeksportować raport w formacie JSON lub CSV.

Jeśli uruchamiasz ocenę w większej skali:

- Możesz uruchamiać wiele ocen jednocześnie i wyświetlać ich stany na stronie **All Assessments** (Wszystkie oceny).
- Możesz [konsolidować oceny w bazie danych programu SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- Możesz też [konsolidować oceny w raporcie usługi Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>Krok 4. Przygotowywanie oceny maszyn wirtualnych za pomocą usługi Azure Migrate

Utwórz konto VMware używane przez usługę Azure Migrate do automatycznego odnajdywanie maszyn wirtualnych, które mają być oceniane. Sprawdź, czy masz uprawnienia do tworzenia maszyny wirtualnej, ustaw poziom statystyk i zwróć uwagę na porty, które muszą być otwarte.

### <a name="set-up-a-vmware-account"></a>Konfigurowanie konta VMware

 Musisz mieć konto tylko do odczytu w programie vCenter. Jeśli go nie masz, utwórz konto VMware z poniższymi właściwościami:

- Typ użytkownika: co najmniej użytkownik tylko do odczytu.
- Uprawnienia: obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu.
- Szczegóły: użytkownik przypisany na poziomie centrum danych z dostępem do wszystkich obiektów w centrum danych.
- Aby ograniczyć dostęp, przypisz rolę **Bez dostępu** z obiektem **Propagacja do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).

### <a name="verify-permissions-to-create-a-vm"></a>Weryfikowanie uprawnień do utworzenia maszyny wirtualnej

Sprawdź, czy masz uprawnienia do utworzenia maszyny wirtualnej, importując plik w formacie .OVA. [Dowiedz się więcej](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Weryfikowanie portów

W tym scenariuszu skonfigurujemy mapowanie zależności. Ta funkcja wymaga zainstalowania agenta na ocenianych maszynach wirtualnych. Agent ten musi mieć możliwość połączenia się z platformą Azure przez port TCP 443 na każdej maszynie wirtualnej. [Dowiedz się więcej](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) o wymaganiach dotyczących połączenia.


### <a name="set-statistics-settings"></a>Ustawianie statystyk

Przed rozpoczęciem wdrażania określ poziom 3 w ustawieniach statystyk programu vCenter Server. Należy pamiętać, że:
- Po ustawieniu poziomu musisz poczekać co najmniej dzień, zanim uruchomisz ocenę. W przeciwnym razie może ona nie działać zgodnie z oczekiwaniami.
- Jeśli zostanie ustawiony poziom wyższy niż 3, ocena będzie działać w następujący sposób:
    - Nie będą zbierane dane wydajności dysków ani sieci.
    - W przypadku magazynu usługa Azure Migrate zaleca standardowy dysk na platformie Azure, o takim samym rozmiarze co dysk lokalny.
    - W przypadku sieci zaleca się utworzenie karty sieciowej na platformie Azure dla każdej lokalnej karty sieciowej.
    - W przypadku obliczeń usługa Azure Migrate uwzględnia liczbę rdzeni oraz rozmiar pamięci i zaleca utworzenie maszyny wirtualnej platformy Azure o takiej samej konfiguracji. Jeśli występuje wiele kwalifikujących się rozmiarów maszyn wirtualnych platformy Azure, zalecany jest rozmiar, który generuje najniższy koszt.


[Dowiedz się więcej](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) o rozmiarach na poziomie 3.

Ustaw poziom w następujący sposób:

1. W kliencie internetowym vSphere otwórz wystąpienie programu vCenter Server.
2. Wybierz kartę **Zarządzanie**, a następnie w obszarze **Ustawienia** kliknij pozycję **Ogólne**.
3. Kliknij pozycję **Edytuj**, a następnie w obszarze **Statystyka** wybierz ustawienie **Poziom 3**.

    ![Poziom statystyki programu vCenter](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>Krok 5. Odnajdywanie maszyn wirtualnych

Utwórz projekt usługi Azure Migrate, a następnie pobierz i skonfiguruj maszynę wirtualną z modułem zbierającym. Następnie uruchom moduł zbierający, aby odnaleźć maszyny wirtualne.

### <a name="create-a-project"></a>Tworzenie projektu

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Utwórz zasób**.
2. Wyszukaj **Azure Migrate**. Wybierz pozycję **Azure Migrate** w wynikach wyszukiwania, a następnie kliknij pozycję **Utwórz**.
3. Podaj nazwę projektu i wybierz subskrypcję platformy Azure.
4. Utwórz nową grupę zasobów.
5. Wskaż lokalizację projektu, a następnie kliknij pozycję **Utwórz**.

    - Projekt usługi Azure Migrate można utworzyć tylko w regionie Zachodnio-środkowe stany USA lub Wschodnie stany USA.
    - Migrację możesz zaplanować dla dowolnej lokalizacji docelowej.
    - Lokalizacja projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)




### <a name="download-the-collector-appliance"></a>Pobieranie urządzenia modułu zbierającego

Usługa Azure Migrate tworzy lokalną maszynę wirtualną, nazywaną modułem zbierającym. Ta maszyna wirtualna służy do odnajdowania lokalnych maszyn wirtualnych VMware i wysyłania dotyczących ich metadanych do usługi Azure Migrate. Aby skonfigurować urządzenie modułu zbierającego, należy pobrać plik OVA i zaimportować go na lokalny serwer vCenter w celu utworzenia maszyny wirtualnej.

1. W projekcie usługi Azure Migrate kliknij pozycję **Wprowadzenie** > **Odnajdź i oceń** > **Odnajdź maszyny**.
2. W obszarze **Odnajdź maszyny** kliknij pozycję **Pobierz**, aby pobrać plik OVA.
3. W obszarze **Skopiuj poświadczenia projektu** skopiuj identyfikator projektu i klucz. Będą potrzebne do skonfigurowania modułu zbierającego.

    ![Pobieranie pliku OVA](./media/migrate-scenarios-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Weryfikowanie urządzenia modułu zbierającego

Przed wdrożeniem pliku OVA sprawdź, czy jest on bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Wygenerowany skrót powinien odpowiadać następującym ustawieniom (wersja 1.0.9.8)

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff


### <a name="create-the-collector-appliance"></a>Tworzenie urządzenia modułu zbierającego

Zaimportuj pobrany plik na serwer vCenter.

1. W konsoli klienta vSphere kliknij pozycję **File** (Plik)  >  **Deploy OVF Template** (Wdróż szablon OVF).

    ![Wdrażanie pliku OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png)

2. W kreatorze wdrażania szablonu OVF na stronie **Source** (Źródło) określ lokalizację pliku OVA i kliknij pozycję **Next** (Dalej).
3. W obszarze **OVF Template Details** (Szczegóły szablonu OVF) kliknij pozycję **Next** (Dalej). W obszarze **End User License Agreement** (Umowa licencyjna użytkownika oprogramowania) kliknij przycisk **Accept** (Akceptuj), aby zaakceptować umowę, a następnie kliknij przycisk **Next** (Dalej).
4. W obszarze **Name and Location** (Nazwa i lokalizacja) wprowadź przyjazną nazwę maszyny wirtualnej modułu zbierającego oraz wskaż lokalizację magazynu, w którym będzie hostowana maszyna wirtualna, a następnie kliknij przycisk **Next** (Dalej). Wskaż host lub klaster, na którym będzie działać urządzenie modułu zbierającego.
5. W obszarze **Storage** (Magazyn) określ miejsce przechowywania plików urządzenia, a następnie kliknij przycisk **Next** (Dalej).
6. W obszarze **Disk Format** (Format dysku) określ sposób aprowizacji magazynu.
7. W obszarze **Network Mapping** (Mapowanie sieci) określ sieć, z którą będzie się łączyć maszyna wirtualna modułu zbierającego. Sieć musi mieć połączenie z Internetem w celu wysyłania metadanych do platformy Azure.
8. W obszarze **Ready to Complete** (Gotowe do ukończenia) przejrzyj ustawienia, zaznacz opcję **Power on after deployment** (Włącz po wdrożeniu), a następnie kliknij przycisk **Zakończ**.

Po utworzeniu urządzenia pojawi się komunikat potwierdzający pomyślne zakończenie operacji.

### <a name="run-the-collector-to-discover-vms"></a>Uruchamianie modułu zbierającego w celu odnalezienia maszyn wirtualnych

Aktualnie moduł zbierający obsługuje wyłącznie opcję „Angielski (Stany Zjednoczone)” jako język systemu operacyjnego i język interfejsu modułu zbierającego.

1. W konsoli klienta vSphere kliknij maszynę wirtualną prawym przyciskiem myszy, a następnie kliknij pozycję **Open Console** (Otwórz konsolę).
2. Określ preferencje dotyczące języka, strefy czasowej i hasła dla urządzenia.
3. Na pulpicie kliknij skrót **Run collector** (Uruchom moduł zbierający).

    ![Skrót do modułu zbierającego](./media/migrate-scenarios-assessment/collector-shortcut.png)

4. W usłudze Azure Migrate Collector otwórz obszar **Set up prerequisites** (Skonfiguruj elementy wymagane wstępnie).
    - Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - Moduł zbierający sprawdza, czy maszyna wirtualna ma dostęp do Internetu, czy jest zsynchronizowany czas oraz czy jest uruchomiona usługa modułu zbierającego (instalowana domyślnie na maszynie wirtualnej). Ponadto moduł zbierający instaluje interfejs wiersza polecenia PowerCLI VMWare.

    > [!NOTE]
    > Zakłada się, że maszyna wirtualna ma bezpośredni dostęp do Internetu, bez serwera proxy.

    ![Weryfikowanie wymagań wstępnych](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)


5. W obszarze **Specify vCenter Server details** (Określ szczegóły serwera vCenter) wykonaj następujące czynności:
    - Określ nazwę FQDN lub adres IP serwera vCenter.
    - W polach **User name** (Nazwa użytkownika) i **Password** (Hasło) wprowadź poświadczenia konta tylko do odczytu, którego moduł zbierający ma użyć do odnalezienia maszyn wirtualnych na serwerze vCenter.
    - W obszarze **Select scope** (Zakres wybierania) wybierz zakres odnajdowania maszyn wirtualnych. Moduł zbierający odnajdzie tylko maszyny wirtualne we wskazanym zakresie. Zakresem może być określony folder, centrum danych albo klaster. Zakres nie powinien zawierać więcej niż 1500 maszyn wirtualnych.

    ![Nawiązywanie połączenia z serwerem vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. W obszarze **Specify migration project** (Określ projekt migracji) podaj identyfikator i klucz projektu usługi Azure Migrate, skopiowane z portalu. Jeśli nie zostały skopiowane, otwórz witrynę Azure Portal na maszynie wirtualnej modułu zbierającego. Na stronie **Omówienie** projektu kliknij polecenie **Odnajdź maszyny**, a następnie skopiuj wartości.  

    ![Nawiązywanie połączenia z usługą Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. Na stronie **View collection progress** (Wyświetlanie postępu zbierania) możesz monitorować proces odnajdowania i sprawdzić, czy metadane zbierane z maszyn wirtualnych odpowiadają wymaganemu zakresowi. Moduł zbierający informuje o szacowanym czasie odnajdowania.

    ![Zbieranie w toku](./media/migrate-scenarios-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu zbierania sprawdź, czy maszyny wirtualne są widoczne w portalu.

1. W projekcie usługi Azure Migrate kliknij pozycję **Zarządzaj** > **Maszyny**.
2. Sprawdź, czy maszyny wirtualne, które miały zostać odnalezione, są widoczne na stronie.

    ![Odnalezione maszyny](./media/migrate-scenarios-assessment/discovery-complete.png)

3. Zwróć uwagę na to, że maszyny nie mają zainstalowanych agentów usługi Azure Migrate. Należy ich zainstalować, aby wyświetlić zależności.

    ![Odnalezione maszyny](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>Krok 6. Przygotowywanie analizy zależności

Aby wyświetlić zależności między maszynami wirtualnymi, które chcemy ocenić, musimy pobrać i zainstalować agentów na maszynach wirtualnych aplikacji internetowych — WEBVM i SQLVM.

### <a name="take-a-snapshot"></a>Tworzenie migawki

Jeśli przed zmodyfikowaniem maszyny wirtualnej chcesz utworzyć jej kopię, utwórz migawkę przed zainstalowaniem agentów.

![Migawka maszyny](./media/migrate-scenarios-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej

1.  W projekcie usługi Azure Migrate na stronie **Maszyny** wybierz maszynę i kliknij pozycję **Wymaga instalacji** w kolumnie **Zależności**.
2.  Na stronie **Odnajdywanie maszyn** dla każdej maszyny wirtualnej pobierz i zainstaluj agenta Microsoft Monitoring Agent (MMA) i agenta zależności.
3.  Skopiuj identyfikator i klucz obszaru roboczego. Będą one potrzebne po zainstalowaniu programu MMA.

    ![Pobieranie agenta](./media/migrate-scenarios-assessment/download-agents.png)



#### <a name="install-the-mma"></a>Instalowanie programu MMA

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3. W obszarze **Folder docelowy** pozostaw domyślny folder instalacji i kliknij przycisk **Dalej**.
4. W obszarze **Opcje instalacji agenta** wybierz pozycję **Połącz agenta z usługą Azure Log Analytics** > **Dalej**.

    ![Instalacja programu MMA](./media/migrate-scenarios-assessment/mma-install.png)
5. W obszarze **Azure Log Analytics** wklej identyfikator i klucz obszaru roboczego skopiowane z portalu. Kliknij przycisk **Dalej**.
    ![Instalacja programu MMA](./media/migrate-scenarios-assessment/mma-install2.png)

6. W obszarze **Gotowe do instalacji** zainstaluj program MMA.



#### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności

1.  Kliknij dwukrotnie pobranego agenta zależności.
2.  Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3.  W obszarze **Instalowanie** poczekaj na ukończenie instalacji. Następnie kliknij przycisk **Next** (Dalej).

    ![Agent zależności](./media/migrate-scenarios-assessment/dependency-agent.png)



## <a name="step-7-run-and-analyze-the-vm-assessment"></a>Krok 7. Uruchamianie i analizowanie oceny maszyny wirtualnej

Sprawdź zależności maszyny, a następnie utwórz grupę. Uruchom ocenę.

### <a name="verify-dependencies-and-create-a-group"></a>Sprawdzanie zależności i tworzenie grupy

1.  Na stronie **Maszyny** kliknij pozycję **Wyświetl zależności** dla maszyn wirtualnych, które chcesz przeanalizować.

    ![Wyświetlanie zależności maszyn](./media/migrate-scenarios-assessment/view-machine-dependencies.png)

2. W przypadku maszyny SQLVM mapa zależności zawiera następujące informacje:

    - Grupy procesów/procesy z aktywnymi połączeniami sieciowymi uruchomione na maszynie SQLVM w określonym przedziale czasu (domyślnie przez godzinę)
    - Połączenia przychodzące TCP (klienta) do wszystkich zależnych maszyn i połączenia wychodzące TCP (serwera) z wszystkich zależnych maszyn.
    - Maszyny zależne z zainstalowanymi agentami usługi Azure Migrate są wyświetlane jako osobne pola
    - W przypadku maszyn bez zainstalowanych agentów są wyświetlane informacje o porcie i adresie IP.

 3. Aby wyświetlić dodatkowe informacje o maszynie z zainstalowanym agentem (WEBVM), takie jak nazwa FQDN, system operacyjny i adres MAC, kliknij pole maszyny.

    ![Wyświetlanie zależności grupowych](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. Wybierz maszyny wirtualne, które chcesz dodać do grupy (SQLVM i WEBVM).  Aby wybrać wiele maszyn wirtualnych, przytrzymaj naciśnięty klawisz CTRL i klikaj poszczególne maszyny.
5. Kliknij przycisk **Utwórz grupę** i podaj nazwę (smarthotelapp).

> [!NOTE]
    > Aby wyświetlić bardziej szczegółowe zależności, możesz rozszerzyć zakres czasu. Możesz wybrać przedział lub datę początkową i datę końcową.


### <a name="run-an-assessment"></a>Uruchamianie oceny


1. Na stronie **Grupy**  otwórz grupę (smarthotelapp).
2. Kliknij pozycję **Utwórz ocenę**.

    ![Tworzenie oceny](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. Ocena pojawi się na stronie **Zarządzaj** > **Oceny**.


### <a name="modify-assessment-settings"></a>Modyfikowanie ustawień oceny

Ten samouczek korzysta z domyślnych ustawień oceny, ale możesz je dostosować w następujący sposób:

1. Na stronie **Oceny** projektu migracji wybierz ocenę i kliknij pozycję **Edytuj właściwości**.
2. Zmodyfikuj właściwości zgodnie z poniższą tabelą:

    **Ustawienie** | **Szczegóły** | **Domyślne**
    --- | --- | ---
    **Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację. | Brak wartości domyślnej.
    **Nadmiarowość magazynu** | Typ nadmiarowości magazynu, który będzie używany przez maszyny wirtualne platformy Azure po zakończeniu migracji. | Wartość domyślna to [Magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md). Usługa Azure Migrate obsługuje tylko oceny oparte na dyskach zarządzanych, a dyski zarządzane obsługują tylko magazyn LRS, dlatego obecnie dostępna jest tylko opcja magazynu LRS.
    **Kryterium rozmiaru** | Kryterium do użycia przez usługę Azure Migrate w celu określenia odpowiedniego rozmiaru dla maszyn wirtualnych platformy Azure. Rozmiar maszyn wirtualnych możesz ustalać *na podstawie wydajności* lub rozmiaru *maszyn lokalnych* bez uwzględniania historii wydajności. | Opcja domyślna to określanie rozmiaru na podstawie wydajności.
    **Historia wydajności** | Przedział czasu uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*. | Wartość domyślna to jeden dzień.
    **Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*.  | Domyślnie jest to 95. percentyl.
    **Warstwa cenowa** | Możesz określić [warstwę cenową (Podstawowa/Standardowa)](../virtual-machines/windows/sizes-general.md) dla docelowych maszyn wirtualnych platformy Azure. Na przykład jeśli planujesz migrację środowiska produkcyjnego, rozważ warstwę Standardowa, która udostępnia maszyny wirtualne o małych opóźnieniach, lecz które mogą kosztować więcej. Z drugiej strony w przypadku środowiska deweloperskiego i testowego rozważ warstwę Podstawowa, która udostępnia maszyny wirtualnych o większych opóźnieniach, lecz przy niższych kosztach. | Domyślnie jest używana warstwa [Standardowa](../virtual-machines/windows/sizes-general.md).
    **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. | Ustawienie domyślne to 1,3x.
    **Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której przeprowadzono rejestrację. | Ustawienie domyślne to [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Waluta** | Twoja waluta rozliczeniowa. | Wartość domyślna to USD.
    **Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure. | Ustawienie domyślne to 0%.
    **Korzyść użycia hybrydowego platformy Azure** | Określ, jeśli masz pakiet Software Assurance i kwalifikujesz się do otrzymania [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.

3. Kliknij przycisk **Zapisz**, aby zaktualizować ustawienia oceny.


### <a name="analyze-the-vm-assessment"></a>Analizowanie oceny maszyny wirtualnej

Ocena usługi Azure Migrate zawiera informacje dotyczące zgodności lokalnych maszyn wirtualnych z platformą Azure, sugerowanych rozmiarów maszyn wirtualnych platformy Azure oraz szacowanych miesięcznych kosztów platformy Azure.

![Raport z oceny](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

![Wyświetlanie oceny](./media/migrate-scenarios-assessment/assessment-display.png)

Ocena jest skojarzona z oceną zaufania obejmującą zakres od 1 gwiazdki do 5 gwiazdek (1 gwiazdka to najniższa ocena, 5 gwiazdek — najwyższa).
- Ocena zaufania jest przypisana do oceny na podstawie dostępności punktów danych potrzebnych do obliczenia oceny.
- Pomaga to oszacować niezawodność zaleceń dotyczących rozmiarów, udostępnianych przez usługę Azure Migrate.
- Ocena zaufania jest przydatna w przypadku *ustalania rozmiaru na podstawie wydajności*, ponieważ usługa Azure Migrate może nie mieć liczby punktów danych wystarczających do określenia rozmiaru na podstawie użycia. W przypadku *ustalania rozmiaru jako lokalnego* ocena zaufania to zawsze 5 gwiazdek, ponieważ usługa Azure Migrate ma wszystkie punkty danych, których potrzebuje do ustalenia rozmiaru maszyny wirtualnej.
- W zależności od wartości procentowej dostępnych punktów danych ocenę zaufania dla oceny określa:

   **Dostępność punktów danych** | **Ocenę zaufania**
   --- | ---
   0%–20% | 1 gwiazdka
   21%–40% | 2 gwiazdki
   41%–60% | 3 gwiazdki
   61%–80% | 4 gwiazdki
   81%–100% | 5 gwiazdek

#### <a name="verify-readiness"></a>Sprawdzanie gotowości

![Ocena gotowości](./media/migrate-scenarios-assessment/azure-readiness.png)  

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

![Ocena gotowości](./media/migrate-scenarios-assessment/azure-costs.png)

- Koszty są szacowane na podstawie zalecanego rozmiaru maszyny.
- Szacowany miesięczny koszt zasobów obliczeniowych i magazynowych jest agregowany dla wszystkich maszyn wirtualnych w grupie.


## <a name="conclusion"></a>Podsumowanie

W tym scenariuszu zostały wykonane następujące kroki:

> [!div class="checklist"]
> * Ocena lokalnej bazy danych za pomocą narzędzia DMA.
> * Ocena lokalnych maszyn wirtualnych za pomocą usługi Azure Migrate z uwzględnieniem mapowania zależności.
> * Przegląd ocen w celu upewnienia się, że lokalne zasoby są gotowe do migracji na platformę Azure.

## <a name="next-steps"></a>Następne kroki

W następnym scenariuszu przeprowadzimy migrację lokalnych maszyn wirtualnych i bazy danych na platformę Azure, korzystając z [metody migracji „lift-and-shift”](migrate-scenarios-lift-and-shift.md).
