---
title: Refaktoryzuj aplikacji firmy Contoso, migrując je do grupy dostępności maszyn wirtualnych platformy Azure i programu SQL Server AlwaysOn | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehost aplikację w środowisku lokalnym przy użyciu funkcji migracji do kontenera usługi Azure Web App i bazy danych serwera SQL Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: aeed26512be6cad5c22cdbb0ca19b1574049c0d4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872270"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migracja Contoso: Refaktoryzacja lokalnych aplikacji z bazą danych Azure SQL i aplikacji sieci Web platformy Azure

W tym artykule przedstawiono, jak Contoso refactors ich SmartHotel aplikacji na platformie Azure. Fronton aplikacji maszyny Wirtualnej oni migrować do usługi Azure Web App i aplikacji bazy danych do usługi Azure SQL database.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania, a dodamy dodatkowe artykuły, wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny aplikacji SmartHotel dwuwarstwowej lokalnych działających w oprogramowaniu firmy VMware. Contoso ocenia maszynach wirtualnych usługi app przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla aplikacji SmartHotel. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzanego SQL, przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i SQL Server zawsze włączona grupa dostępności](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do klastra programu SQL Server chronione przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest lift-and-shift migracji aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu funkcji odzyskiwania lokacji i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
Artykuł 9: Refaktoryzacja aplikację z bazą danych Azure SQL i aplikacji sieci Web platformy Azure | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | W tym artykule
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux do aplikacji sieci Web platformy Azure i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne


W tym artykule Contoso migruje Windows dwuwarstwowy. Aplikacja NET SmartHotel działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji, znajduje się jako "open source" i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie i istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT musi być w reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: w miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Koszty**: Contoso chce zminimalizować koszty licencjonowania.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele zostały użyte w celu ustalenia najlepszej metody migracji.

**Wymagania** | **Szczegóły**
--- | --- 
**Aplikacja** | Aplikacji na platformie Azure będą obowiązywać tak ważne, ponieważ jest już dziś.<br/><br/> Powinien on te same możliwości wydajności, jak obecnie w środowisku programu VMWare<br/><br/> One nie chcesz inwestować w aplikacji. Teraz po prostu chcą bezpiecznie przenieść je do chmury.<br/><br/> Firma chce polegająca na wyłączeniu obsługi systemu Windows Server 2008 R2, na którym aktualnie jest uruchomiona aplikacja.<br/><br/> Firma chce przesuwania kursora od programu SQL Server 2008 R2 na nowoczesnej platformie PaaS bazy danych, minimalizuje potrzebę zarządzania.<br/><br/> Firma Contoso ma wykorzystanie inwestycji w Licencjonowanie programu SQL Server i pakietem Software Assurance, jeśli jest to możliwe.<br/><br/> Ponadto chcesz eliminowanie pojedynczym punktem awarii w warstwie sieci web firmy Contoso.
**Ograniczenia** | Aplikacja składa się z aplikacji platformy ASP.NET i usługi WCF uruchomionych na tej samej maszyny Wirtualnej. Firma chce podzielić to na dwie aplikacje internetowe przy użyciu usługi Azure App Service. 
**Azure** | Firma chce przenieść aplikację na platformę Azure, ale nie chcesz go uruchamiać na maszynach wirtualnych. Firma chce korzystać z usług PaaS platformy Azure w przypadku usługi sieci web i danych. 

## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół swoje cele i wymagania, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, które będą używały do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- SmartHotel aplikacji w środowisku lokalnym są rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


### <a name="proposed-solution"></a>Proponowane rozwiązanie

- W warstwie bazy danych aplikacji firmy Contoso w porównaniu z programu SQL Server przy użyciu usługi Azure SQL Database [w tym artykule](https://docs.microsoft.com/azure/sql-database/sql-database-features). Zespół postanowił do korzystania z usługi Azure SQL Database z kilku powodów:
    - Usługa Azure SQL Database to zarządzana usługa relacyjnej bazy danych. System ten zapewnia przewidywalną wydajność na różnych poziomach usługi, przy bliskich zeru nakładach na administrację. Korzyści obejmują dynamiczną skalowalność bez przestojów, wbudowany mechanizm inteligentnej optymalizacji i globalną skalowalność i dostępność.
    - Mogą one używać uproszczonej Data Migration Assistant (DMA) do oceny i migracji lokalnej bazy danych do bazy danych SQL Azure.
    - Z pakietem Software Assurance mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych w usłudze SQL Database za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server. Może to zapewnić oszczędności nawet do 30%.
    - SQL Database oferuje pewną liczbę funkcji zabezpieczeń, takich jak zawsze zaszyfrowane dane dynamiczne maskowanie i wykrywanie zagrożeń zabezpieczeń poziomu wierszy.
- W przypadku warstwy aplikacji sieci web zostały zdecydowali się na użycie usługi Azure App Service. Ta usługa PaaS umożliwia, aby wdrożyć aplikację za pomocą tylko kilku zmian w konfiguracji. One wprowadzić zmiany, przy użyciu programu Visual Studio i wdrażanie dwóch aplikacji sieci web. Jeden dla witryny sieci Web i jeden dla usługi WCF.
  
### <a name="solution-review"></a>Przegląd rozwiązania
Contoso ocenia swoich konstrukcjach proponowanych przez zestawiania listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | SmartHotel kod aplikacji nie będzie konieczne można zmienić dla migracji na platformę Azure.<br/><br/> Mogą one używać swoich inwestycji związanych z pakietem Software Assurance dla programu SQL Server i Windows Server za pomocą korzyści użycia hybrydowego platformy Azure.<br/><br/> Po zakończeniu migracji one już należy do obsługi systemu Windows Server 2008 R2. [Dowiedz się więcej](https://support.microsoft.com/lifecycle).<br/><br/> Warstwa sieci web aplikacji z wieloma wystąpieniami można skonfigurować tak, aby nie jest już pojedynczym punktem awarii.<br/><br/> Współpracownicy będą nie jest już zależny od przestarzałej programu SQL Server 2008 R2.<br/><br/> Usługa SQL Database obsługuje wymagania techniczne firmy Contoso. Ich użycia w celu oceny lokalnej bazy danych, używając Asystenta migracji bazy danych i wykrył, że jest zgodny.<br/><br/> Usługa SQL Database ma wbudowaną odporność na uszkodzenia, który Contoso trzeba skonfigurować. Zapewnia to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | Usługi Azure App Services obsługuje tylko jedno wdrożenie aplikacji dla każdej aplikacji sieci Web. Oznacza to, że dwie aplikacje sieci Web musi być aprowizowana (jeden dla witryny sieci Web) i jeden dla usługi WCF.<br/><br/> Jeśli korzystają z programu Data Migration Assistant zamiast usługi migracji danych do migracji ich bazy danych, Contoso, nie będziesz mieć gotowy do migracji infrastruktury baz danych na dużą skalę. One należy tworzyć innego regionu w celu zapewnienia pracy awaryjnej, jeśli region podstawowy jest niedostępny.

## <a name="proposed-architecture"></a>Proponowana architektury

![Architektura scenariusza](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Proces migracji

1. Contoso aprowizowanie wystąpienia usługi Azure SQL i migrację bazy danych SmartHotel do niego.
2. Ich obsługi administracyjnej i konfigurowanie aplikacji sieci Web i wdrażanie aplikacji SmartHotel do nich.

    ![Proces migracji](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Użyjemy ich DMA do oceny i wykrycia problemów ze zgodnością, które mogą mieć wpływ na ich funkcje bazy danych na platformie Azure. Program DMA ocenia równoważność funkcji między źródłami SQL i elementy docelowe i zaleca wydajności i niezawodności. | Narzędzie to można pobrać bezpłatnie.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentna, w pełni zarządzana usługa relacyjnej w chmurze bazy danych. | Koszt na podstawie funkcji, przepływności i rozmiaru. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Usługa Azure App Services — aplikacje sieci Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Tworzenie zaawansowanych aplikacji chmurowych przy użyciu w pełni zarządzana platforma | Koszt oparte na czas trwania rozmiar, lokalizację i użycia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Wymagania wstępne

Oto, co Ty (i firmy Contoso) potrzebne do uruchomienia tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1: Aprowizowanie wystąpienia bazy danych SQL na platformie Azure**: Contoso Inicjuje obsługę wystąpienie serwera SQL na platformie Azure. Po aplikacji witryny sieci Web jest Migrowanie na platformę Azure, aplikacji sieci web WCF service będzie wskazywać tego wystąpienia.
> * **Krok 2: Migrację bazy danych za pomocą programu DMA**: migrowania bazy danych aplikacji przy użyciu Asystenta migracji bazy danych.
> * **Krok 3: Aprowizowanie Web Apps**: udostępnia dwóch aplikacji sieci web.
> * **Krok 4: Konfigurowanie parametrów połączenia**: Konfigurowanie parametrów połączenia, umożliwiając aplikacji sieci web warstwy sieci web, aplikacji sieci web usługi WCF i wystąpieniem serwera SQL może komunikować się.
> * **Krok 5. Publikowanie aplikacji sieci web**: Contoso jako ostatni krok publikuje aplikacje na platformie Azure.


## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Aprowizowanie bazy danych Azure SQL

1. Należy wybrać, aby utworzyć bazę danych SQL na platformie Azure. 

    ![Aprowizacja SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Określają nazwę bazy danych w celu dopasowania bazy danych uruchomione na maszynie Wirtualnej w środowisku lokalnym (**SmartHotel.Registration**). W grupie zasobów ContosoRG mogą umieścić bazę danych. To jest grupa zasobów, których używają zasobów w środowisku produkcyjnym na platformie Azure.

    ![Aprowizacja SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Konfigurowania nowego wystąpienia programu SQL Server (**eus2-sql-smarthotel**) w regionie podstawowym.

    ![Aprowizacja SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Ustawiają warstwę cenową, aby dopasować ich serwer i bazę danych. I należy wybrać, aby oszczędzać pieniądze dzięki korzyści użycia hybrydowego platformy Azure, ponieważ mają one już licencję na program SQL Server.
5. W przypadku ustalania rozmiaru Użyj oparte na v-Core zakupu, a Ustaw limity dla ich oczekiwanego wymagań.

    ![Aprowizacja SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Następnie utwórz wystąpienie bazy danych.

    ![Aprowizacja SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. Po utworzeniu wystąpienia będzie otworzyć bazy danych i zanotuj szczegóły, które są im potrzebne podczas korzystania z pomocy migracji bazy danych do migracji.

    ![Aprowizacja SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Potrzebujesz dodatkowej pomocy?**

- [Uzyskaj Pomoc](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) aprowizacji bazy danych SQL.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) v-Core limity zasobów.


## <a name="step-2-migrate-the-database-with-dma"></a>Krok 2: Migrację bazy danych za pomocą programu DMA

Contoso zostaną zmigrowane SmartHotel bazy danych przy użyciu narzędzia DMA.

### <a name="install-dma"></a>Instalowanie programu DMA

1. Będą oni mogli pobrać narzędzia z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) do maszyny Wirtualnej w środowisku lokalnym SQL Server (**SQLVM**).
2. Instalatora (DownloadMigrationAssistant.msi), są one uruchomione na maszynie Wirtualnej.
3. Na **Zakończ** strony, wybierają **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

### <a name="migrate-the-database-with-dma"></a>Migracja bazy danych za pomocą programu DMA

1. W narzędziu DMA Utwórz nowy projekt (**SmartHotelDB**) i wybierz **migracji** 
2. Wybierają typ serwera źródłowego jako **programu SQL Server**, jak i obiektem docelowym jako **usługi Azure SQL Database**. 

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Informacje dotyczące migracji, co zwiększa **SQLVM** co serwer źródłowy i **SmartHotel.Registration** bazy danych. 

     ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Otrzyma błąd, który wydaje się być skojarzony z uwierzytelnianiem. Jednak po przeanalizowaniu, problem polega na kropki (.) w nazwie bazy danych. Jako obejście decyzję o udostępnianie nowej bazy danych SQL przy użyciu nazwy **rejestracji SmartHotel**, aby rozwiązać ten problem. Po uruchomieniu narzędzia DMA ponownie, są one możliwość wyboru **SmartHotel rejestracji**i kontynuować pracę w kreatorze.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. W **zaznacz obiekty**, wybierz tabele bazy danych i wygenerować skryptu SQL.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Po utworzeniu usługi DMS skryptu, klikając **Wdróż schemat**.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. Program DMA potwierdza, że wdrożenie zakończyło się pomyślnie.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Po rozpoczęciu migracji.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Po zakończeniu migracji, Contoso można sprawdzić, czy baza danych jest uruchomiony w wystąpieniu programu Azure SQL.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Usunięcie dodatkowych bazy danych SQL **SmartHotel.Registration** w witrynie Azure portal.

    ![PROGRAM DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Krok 3: Aprowizowanie Web Apps

Z bazą danych migracji firma Contoso może teraz aprowizowanie że dwie aplikacje sieci web.

1. Wybierają **aplikacji sieci Web** w portalu.

    ![Aplikacja internetowa](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Zapewniają one nazwę aplikacji (**SHWEB EUS2**), systemem Windows i umieść go anulować produkcyjnej grupie zasobów **ContosoRG**. Tworzą nową usługę app service i planu.

    ![Aplikacja internetowa](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Po aprowizacji aplikacji sieci web powtarzają proces tworzenia aplikacji sieci web dla usługi WCF (**SHWCF EUS2**)

    ![Aplikacja internetowa](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Po tych czynności przejdź na adres aplikacji w celu sprawdzenia, czy zostały one pomyślnie utworzony.

## <a name="step-4-configure-connection-strings"></a>Krok 4: Konfigurowanie parametrów połączenia

Firma Contoso potrzebuje upewnić się, że aplikacje sieci web i bazy danych mogą wszystkie się komunikować. Aby to zrobić, mogą konfigurować parametry połączenia w kodzie, jak i w aplikacjach sieci web.

1. W aplikacji sieci web dla usługi WCF (**SHWCF EUS2**) > **ustawienia** > **ustawienia aplikacji**, dodają nowe parametry połączenia o nazwie  **DefaultConnection**.
2. Parametry połączenia są pobierane z **rejestracji SmartHotel** bazy danych i powinien być zaktualizowany o poprawne poświadczenia.

    ![Parametry połączenia](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Firmy Contoso w programie Visual Studio otwiera się z plikiem rozwiązania z **rozwiązania SmartHotel360--rezerwacji — aplikacje wewnętrzne** folderu. **ConnectionStrings** sekcja pliku web.config dla usługi WCF SmartHotel.Registration.Wcf powinny być aktualizowane przy użyciu parametrów połączenia.

     ![Parametry połączenia](media/contoso-migration-refactor-web-app-sql/string2.png)

4. **Klienta** sekcja pliku web.config dla SmartHotel.Registration.Web należy je zmienić i wskazać do nowej lokalizacji usługi WCF. To jest adres URL aplikacji sieci web WCF hostingu punktu końcowego usługi.

    ![Parametry połączenia](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>Krok 5. Publikowanie aplikacji sieci web

W ostatnim kroku Contoso publikuje aplikacji sieci web na platformie Azure.

1. W programie Visual Studio, one kliknij prawym przyciskiem myszy projekt SmartHotel.REgistration.Wcf > **Publikuj**.

    ![Publikowanie](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. Uruchamiają publikowania.

    ![Publikowanie](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. Wybierają istniejącej usługi App Service, ponieważ już utworzonych aplikacji sieci web.

    ![Publikowanie](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. Po wybraniu aplikacji WCF, program Visual Studio wdroży go.

    ![Publikowanie](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. Są następnie powtórz proces, aby opublikować aplikację sieci web - SmartHotel.Registration.Web.

    ![Publikowanie](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


W tym momencie aplikacji pomyślnej migracji na platformę Azure.

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji firma Contoso potrzebuje do wykonania tych kroków czyszczenia:  

- Usuń lokalne maszyny wirtualne z magazynu programu vCenter.
- Usuń maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizuj wewnętrzną dokumentację, aby wyświetlić nowe lokalizacje dla aplikacji SmartHotel. Pokaż bazy danych jako uruchomiona w bazie danych Azure SQL i frontonu jako uruchomiona w dwóch aplikacjach sieci web.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.


## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

- Firmy Contoso, musisz upewnić się, że ich nowych **rejestracji SmartHotel** bazy danych jest bezpieczna. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- W szczególności należy zaktualizowanie aplikacji sieci web do używania protokołu SSL przy użyciu certyfikatów.

### <a name="backups"></a>Tworzenie kopii zapasowych

- Firma Contoso potrzebuje zapoznać się z wymaganiami kopii zapasowej usługi Azure SQL Database. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Powinni rozważyć Implementowanie grupy trybu failover, aby zapewnić rozwiązania regionalnej pracy awaryjnej dla bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso muszą wziąć pod uwagę wdrażanie aplikacji sieci Web w głównym wschodnie stany USA 2 i środkowe stany USA region, aby zapewnić elastyczność. One może skonfigurować usługi Traffic Manager, aby upewnić się, pracy awaryjnej w przypadku regionalnej awarii.

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu wszystkich zasobów firmy Contoso, należy przypisać tagi platformy Azure na podstawie ich [planowania infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Wszystkie licencjonowania jest wbudowana w koszt usługi PaaS, które zużywa firmy Contoso. Ta ilość zostanie odjęta z umowy EA.
1. Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso zaprojektowane od nowa aplikacja SmartHotel na platformie Azure przy użyciu funkcji migracji aplikacji frontonu maszyn wirtualnych do dwóch aplikacji sieci Web platformy Azure. Baza danych aplikacji one migracji do usługi Azure SQL database.






