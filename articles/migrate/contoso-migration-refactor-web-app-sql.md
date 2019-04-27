---
title: Refaktoryzuj aplikacji firmy Contoso przy użyciu funkcji migracji do usługi Azure Web App i Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso rehostowaniu aplikację w środowisku lokalnym przy użyciu funkcji migracji ją z bazą danych aplikacji sieci Web platformy Azure i usługi Azure SQL Server.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 271e18d370068e0445f183af0c694b19f0da22f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60672969"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migracja Contoso: Refaktoryzacja lokalnych aplikacji z bazą danych Azure SQL i aplikacji sieci Web platformy Azure

W tym artykule przedstawiono, jak Contoso refactors ich aplikacji rozwiązania SmartHotel360 na platformie Azure. Fronton aplikacji maszyny Wirtualnej oni migrować do usługi Azure Web App i aplikacji bazy danych do usługi Azure SQL database.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serię artykułów i przykładowe aplikacje, używanych przez firmę Microsoft. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | W tym artykule opisano, jak Contoso przygotowuje jej w środowisku lokalnym i infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych](contoso-migration-assessment.md)  | Pokazuje, jak firmy Contoso jest wykonywany oceny aplikacji SmartHotel dwuwarstwowej lokalnych działających w oprogramowaniu firmy VMware. Contoso ocenia maszynach wirtualnych usługi app przy użyciu [usługi Azure Migrate](migrate-overview.md) usługi bazy danych oraz aplikacji programu SQL Server za pomocą [Asystenta migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i wystąpienie zarządzane SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla aplikacji SmartHotel. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i bazy danych aplikacji do wystąpienia zarządzanego SQL, przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migrację aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i programu SQL Server zawsze włączona grupa dostępności](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji, maszyny wirtualne i usługi migracji bazy danych do migracji bazy danych aplikacji do klastra programu SQL Server chronione przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak firmy Contoso jest lift-and-shift migracji aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, przy użyciu Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i serwer Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu funkcji odzyskiwania lokacji i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
Artykuł 9: Refaktoryzowanie aplikacji w usługach Azure Web App i Azure SQL Database | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | W tym artykule
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux do aplikacji sieci Web platformy Azure i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne
[Artykuł 11: Refaktoryzuj TFS na usługom DevOps platformy Azure](contoso-migration-tfs-vsts.md) | Pokazuje, jak Contoso migruje ich lokalnego wdrożenia Team Foundation Server (TFS) przy użyciu funkcji migracji do usługi Azure DevOps usług na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji na kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | Dostępne
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne

W tym artykule Contoso migruje Windows dwuwarstwowy. Aplikacja rozwiązania SmartHotel360 NET działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji, znajduje się jako "open source" i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Rozwija się firmy Contoso, i istnieje nacisk na systemach lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Firmy Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**:  Contoso IT trzeba zwiększyć szybkość reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: W miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Koszty**: Firma Contoso chce, aby zminimalizować koszty licencjonowania.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele zostały użyte w celu ustalenia najlepszej metody migracji.

**Wymagania** | **Szczegóły**
--- | --- 
**Aplikacja** | Aplikacji na platformie Azure będą obowiązywać tak ważne, ponieważ jest już dziś.<br/><br/> Powinien on te same możliwości wydajności, jak obecnie w środowisku programu VMWare<br/><br/> Zespół nie chcesz inwestować w aplikacji. Na razie Administratorzy zostanie przesunięty aplikacji bezpieczne w chmurze.<br/><br/> Zespół chce polegająca na wyłączeniu obsługi systemu Windows Server 2008 R2, na którym aktualnie jest uruchomiona aplikacja.<br/><br/> Zespół chce również na nowoczesnej platformie PaaS bazy danych, minimalizuje potrzebę zarządzania odbiegać od programu SQL Server 2008 R2.<br/><br/> Firma Contoso ma wykorzystywać swoich inwestycji w Licencjonowanie programu SQL Server i pakietem Software Assurance, jeśli jest to możliwe.<br/><br/> Ponadto firma Contoso chce ograniczyć pojedynczym punktem awarii w warstwie sieci web.
**Ograniczenia** | Aplikacja składa się z aplikacji platformy ASP.NET i usługi WCF uruchomionych na tej samej maszyny Wirtualnej. Firma chce podzielić to na dwie aplikacje internetowe przy użyciu usługi Azure App Service. 
**Azure** | Firma Contoso chce, aby przenieść aplikację na platformę Azure, ale nie ma zostać uruchomiony na maszynach wirtualnych. Firma Contoso chce korzystać z usług PaaS platformy Azure w przypadku usługi sieci web i danych. 
**DevOps** | Firma Contoso chce przenieść do modelu metodyki DevOps, używając DevOps platformy Azure dla swoich kompilacji i wydawania potoków.

## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół celami i wymaganiami, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, które będą używane do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- Aplikację lokalną rozwiązania SmartHotel360 są rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


### <a name="proposed-solution"></a>Proponowane rozwiązanie

- W warstwie bazy danych aplikacji firmy Contoso w porównaniu z programu SQL Server przy użyciu usługi Azure SQL Database [w tym artykule](https://docs.microsoft.com/azure/sql-database/sql-database-features). Contoso zdecydowano o wybraniu usługi Azure SQL Database z kilku powodów:
    - Usługa Azure SQL Database to zarządzana usługa relacyjnej bazy danych. System ten zapewnia przewidywalną wydajność na różnych poziomach usługi, przy bliskich zeru nakładach na administrację. Korzyści obejmują dynamiczną skalowalność bez przestojów, wbudowany mechanizm inteligentnej optymalizacji i globalną skalowalność i dostępność.
    - Contoso mogą korzystać z uproszczonego Data Migration Assistant (DMA) do oceny i migracji lokalnej bazy danych do bazy danych SQL Azure.
    - Z pakietem Software Assurance Contoso mogą wymieniać istniejących licencji do korzystania z taryf rabatowych w usłudze SQL Database za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server. Może to zapewnić oszczędności nawet do 30%.
    - SQL Database oferuje pewną liczbę funkcji zabezpieczeń, takich jak zawsze zaszyfrowane dane dynamiczne maskowanie i wykrywanie zagrożeń zabezpieczeń poziomu wierszy.
- W przypadku warstwy aplikacji sieci web firmy Contoso scrumowy zdecydował się korzystać z usługi Azure App Service. Ta usługa PaaS umożliwia, aby wdrożyć aplikację za pomocą tylko kilku zmian w konfiguracji. Contoso wprowadzić zmiany, przy użyciu programu Visual Studio i wdrażanie dwóch aplikacji sieci web. Jeden dla witryny sieci Web i jeden dla usługi WCF.
- Aby spełnić wymagania dla potoku metodyki DevOps, Contoso został wybrany za pomocą DevOps platformy Azure do zarządzania kodu źródłowego (SCM) repozytoria Git. Zautomatyzowane kompilacje i wydania będzie służyć do tworzenia kodu, a następnie wdrożyć ją w usłudze Azure Web Apps.
  
### <a name="solution-review"></a>Przegląd rozwiązania
Contoso ocenia swoich konstrukcjach proponowanych przez zestawiania listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | Kod aplikacji rozwiązania SmartHotel360 uniknąć konieczności można zmienić dla migracji na platformę Azure.<br/><br/> Contoso mogą korzystać z inwestycji w pakiet Software Assurance dla programu SQL Server i Windows Server za pomocą korzyści użycia hybrydowego platformy Azure.<br/><br/> Po zakończeniu migracji systemu Windows Server 2008 R2 nie musi być obsługiwana. [Dowiedz się więcej](https://support.microsoft.com/lifecycle).<br/><br/> Contoso można skonfigurować warstwa sieci web aplikacji z wieloma wystąpieniami tak, aby nie jest już pojedynczym punktem awarii.<br/><br/> Baza danych nie będzie już zależny od przestarzałej programu SQL Server 2008 R2.<br/><br/> Usługa SQL Database obsługuje wymagania techniczne. Contoso oceny lokalnej bazy danych, używając Asystenta migracji bazy danych i wykrył, że jest zgodny.<br/><br/> Usługa SQL Database ma wbudowaną odporność na uszkodzenia, który Contoso trzeba skonfigurować. Zapewnia to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | Usługi Azure App Services obsługuje tylko jedno wdrożenie aplikacji dla każdej aplikacji sieci Web. Oznacza to, że dwie aplikacje sieci Web musi być aprowizowana (jeden dla witryny sieci Web) i jeden dla usługi WCF.<br/><br/> Jeśli firma Contoso używa programu Data Migration Assistant zamiast usługi migracji danych do migracji ich bazy danych, nie będzie mieć gotowy do migracji infrastruktury baz danych na dużą skalę. Contoso będą musieli tworzyć innego regionu w celu zapewnienia pracy awaryjnej, jeśli region podstawowy jest niedostępny.

## <a name="proposed-architecture"></a>Proponowana architektury

![Architektura scenariusza](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Proces migracji

1. Contoso ustanowienie wystąpienia usługi Azure SQL i bazy danych rozwiązania SmartHotel360 jest migrowana do niego.
2. Contoso aprowizuje i konfiguruje aplikacje sieci Web, a następnie wdraża aplikacji rozwiązania SmartHotel360.

    ![Proces migracji](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso użyje DMA do oceny i wykrycia problemów ze zgodnością, które mogą mieć wpływ na ich funkcje bazy danych na platformie Azure. Program DMA ocenia równoważność funkcji między źródłami SQL i elementy docelowe i zaleca wydajności i niezawodności. | Narzędzie to można pobrać bezpłatnie.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentna, w pełni zarządzana usługa relacyjnej w chmurze bazy danych. | Koszt na podstawie funkcji, przepływności i rozmiaru. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Usługa Azure App Services — aplikacje sieci Web](https://docs.microsoft.com/azure/app-service/overview) | Tworzenie zaawansowanych aplikacji chmurowych przy użyciu w pełni zarządzana platforma | Koszt oparte na czas trwania rozmiar, lokalizację i użycia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Usługa Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Zapewnia ciągłą integrację i potok ciągłego wdrażania (CI/CD) do tworzenia aplikacji. Potok rozpoczyna się od repozytorium Git na potrzeby zarządzania kodu aplikacji, system kompilacji na potrzeby tworzenia pakietów i innych artefaktów kompilacji i system zarządzania wydaniami, aby wdrożyć zmiany w deweloperów, testerów i środowisk produkcyjnych. 

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej przedstawiono wymagania firmy Contoso do uruchomienia tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Contoso tworzone subskrypcji podczas wczesnych artykułu. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.


## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1. Aprowizowanie wystąpienia bazy danych SQL na platformie Azure**: Firmy Contoso inicjuje wystąpienie serwera SQL na platformie Azure. Po aplikacji witryny sieci Web jest Migrowanie na platformę Azure, aplikacji sieci web WCF service będzie wskazywać tego wystąpienia.
> * **Krok 2. Migracja bazy danych za pomocą programu DMA**: Contoso przeprowadza migrację bazy danych aplikacji przy użyciu Asystenta migracji bazy danych.
> * **Krok 3. Aprowizowanie aplikacji sieci Web**: Firmy Contoso Inicjuje obsługę dwóch aplikacji sieci web.
> * **Krok 4. Konfigurowanie usługi Azure DevOps**: Contoso tworzy nowy projekt DevOps platformy Azure, a następnie importuje repozytorium Git.
> * **Krok 5. Konfigurowanie parametrów połączenia**: Contoso konfiguruje parametry połączenia, aby aplikacji sieci web warstwy sieci web, aplikacji sieci web usługi WCF i wystąpieniem serwera SQL może komunikować się.
> * **Krok 6: Skonfiguruj kompilację i potoki wersji**: W ostatnim kroku Contoso ustawia kompilacji i wydania potoków do utworzenia aplikacji i wdraża je do dwóch oddzielnych aplikacji sieci Web platformy Azure.


## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Aprowizowanie bazy danych Azure SQL

1. Administratorzy firmy Contoso zaznacz, aby utworzyć bazę danych SQL na platformie Azure. 

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


## <a name="step-2-migrate-the-database-with-dma"></a>Krok 2: Migracja bazy danych za pomocą programu DMA

Administratorzy firmy Contoso, zostaną zmigrowane bazy rozwiązanie SmartHotel360 przy użyciu narzędzia DMA.

### <a name="install-dma"></a>Instalowanie programu DMA

1. Będą oni mogli pobrać narzędzia z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) do maszyny Wirtualnej w środowisku lokalnym SQL Server (**SQLVM**).
2. Instalatora (DownloadMigrationAssistant.msi), są one uruchomione na maszynie Wirtualnej.
3. Na **Zakończ** strony, wybierają **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

### <a name="migrate-the-database-with-dma"></a>Migracja bazy danych za pomocą programu DMA

1. W narzędziu DMA, tworzą nowy projekt (**SmartHotelDB**) i wybierz **migracji** 
2. Wybierają typ serwera źródłowego jako **programu SQL Server**, jak i obiektem docelowym jako **usługi Azure SQL Database**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Informacje dotyczące migracji, co zwiększa **SQLVM** co serwer źródłowy i **SmartHotel.Registration** bazy danych. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Otrzyma błąd, który wydaje się być skojarzony z uwierzytelnianiem. Jednak po przeanalizowaniu, problem polega na kropki (.) w nazwie bazy danych. Jako obejście decyzję o udostępnianie nowej bazy danych SQL przy użyciu nazwy **rejestracji SmartHotel**, aby rozwiązać ten problem. Po uruchomieniu narzędzia DMA ponownie, są one możliwość wyboru **SmartHotel rejestracji**i kontynuować pracę w kreatorze.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. W **zaznacz obiekty**, wybierz tabele bazy danych i wygenerować skryptu SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Po utworzeniu usługi DMS skryptu, klikając **Wdróż schemat**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. Program DMA potwierdza, że wdrożenie zakończyło się pomyślnie.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Po rozpoczęciu migracji.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Po zakończeniu migracji, Contoso Administratorzy mogą sprawdzić, czy bazy danych jest uruchomiony w wystąpieniu programu Azure SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Usunięcie dodatkowych bazy danych SQL **SmartHotel.Registration** w witrynie Azure portal.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Krok 3: Aprowizowanie aplikacji sieci Web

Z bazą danych migracji Administratorzy Contoso obecnie uaktywnienie dwóch aplikacji sieci web.

1. Wybierają **aplikacji sieci Web** w portalu.

    ![Aplikacja internetowa](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Zapewniają one nazwę aplikacji (**SHWEB EUS2**), systemem Windows i umieść go anulować produkcyjnej grupie zasobów **ContosoRG**. Tworzą nową usługę app service i planu.

    ![Aplikacja internetowa](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Po aprowizacji aplikacji sieci web powtarzają proces tworzenia aplikacji sieci web dla usługi WCF (**SHWCF EUS2**)

    ![Aplikacja internetowa](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Po tych czynności przejdź na adres aplikacji w celu sprawdzenia, czy zostały one pomyślnie utworzony.


## <a name="step-4-set-up-azure-devops"></a>Krok 4: Konfigurowanie usług Azure DevOps


Firma Contoso potrzebuje do tworzenia infrastruktury DevOps i potoki pod kątem aplikacji.  Aby to zrobić, Administratorzy Contoso Utwórz nowy projekt DevOps, zaimportuj kod, ustaw kompilację i potoki wersji.

1. W ramach konta Contoso DevOps platformy Azure tworzą nowy projekt (**ContosoSmartHotelRefactor**) i wybierz **Git** kontroli wersji.

   ![Nowy projekt](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. Zaimportowali repozytorium Git, zawierający kod aplikacji. Jest on [publicznego repozytorium](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) i można go pobrać.

    ![Pobieranie kodu aplikacji](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. Po zaimportowaniu kod będzie łączenie programu Visual Studio do repozytorium i klonowanie kodu za pomocą programu Team Explorer.

    ![Połącz się z projektem](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. Po repozytorium zostanie sklonowany maszyny dewelopera, ich otworzyć plik rozwiązania dla aplikacji. Usługi wcf i aplikacji sieci web każda z nich ma oddzielić projektu w pliku.

    ![Plik rozwiązania](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>Krok 5. Konfigurowanie parametrów połączenia

Administratorzy firmy Contoso, musisz upewnić się, aplikacje sieci web i bazy danych mogą wszystkie komunikować się. Aby to zrobić, mogą konfigurować parametry połączenia w kodzie, jak i w aplikacjach sieci web.

1. W aplikacji sieci web dla usługi WCF (**SHWCF EUS2**) > **ustawienia** > **ustawienia aplikacji**, dodają nowe parametry połączenia o nazwie  **DefaultConnection**.
2. Parametry połączenia są pobierane z **rejestracji SmartHotel** bazy danych i powinien być zaktualizowany o poprawne poświadczenia.

    ![Parametry połączenia](media/contoso-migration-refactor-web-app-sql/string1.png)

3. W programie Visual Studio otworzą **SmartHotel.Registration.wcf** projektu z pliku rozwiązania. **ConnectionStrings** sekcja pliku web.config dla usługi WCF SmartHotel.Registration.Wcf powinny być aktualizowane przy użyciu parametrów połączenia.

     ![Parametry połączenia](media/contoso-migration-refactor-web-app-sql/string2.png)

4. **Klienta** sekcja pliku web.config dla SmartHotel.Registration.Web należy je zmienić i wskazać do nowej lokalizacji usługi WCF. To jest adres URL aplikacji sieci web WCF hostingu punktu końcowego usługi.

    ![Parametry połączenia](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. Po zmiany w kodzie, Administratorzy muszą zatwierdzić zmiany. Za pomocą programu Team Explorer w programie Visual Studio, one Zatwierdź i Synchronizuj.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>Krok 6: Konfigurowanie kompilacji i wydawania potoków w DevOps platformy Azure

Administratorzy firmy Contoso teraz skonfigurować DevOps platformy Azure, aby wykonać kompilację i procesu tworzenia wersji.

1. W metodyce DevOps platformy Azure, kliknij przycisk **kompilowania i wydawania** > **nowy potok**.

    ![Nowy potok](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. Wybierają **Azure repozytoriów Git** i odpowiedniego repozytorium.

    ![Git i repozytorium](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. W **wybierz szablon**, wybierają szablon platformy ASP.NET dla kompilacji.

     ![Szablon platformy ASP.NET](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. Nazwa **ContosoSmartHotelRefactor — ASP.NET — ciągła Integracja** jest używany dla kompilacji. Polecenie **Zapisz k & Olejką**.

     ![Zapisz i kolejki](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. Dotyczącego pierwsza kompilacja. Kliknięciu numer kompilacji, aby obejrzeć ten proces. Po zakończeniu ich Zobacz opinie proces i kliknij przycisk **artefaktów** Aby przejrzeć wyniki kompilacji.

    ![Przegląd](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. Folder **porzucić** zawiera wyniki kompilacji.

   - Pliki zip dwa są pakiety zawierające aplikacje.
   - Te pliki są używane w potoku wydań we wdrożeniach w usłudze Azure Web Apps

     ![Artefakt](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. Polecenie **wersji** > **+ nowy potok**.

    ![Nowy potok](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Wybierają Szablon wdrożenia usługi Azure App Service.

    ![Szablon usługi Azure App Service](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. Ich nazwy potoku tworzenia wersji **ContosoSmartHotel360Refactor**i określ nazwę aplikacji sieci web WCF (SHWCF EUS2) **etapu** nazwy.

    ![Środowisko](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. W obszarze etapy, kliknij przycisk **zadanie 1, 1 zadanie** Aby skonfigurować wdrożenie usługi WCF.

    ![Wdrażanie usługi WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. Sprawdź, subskrypcja jest zaznaczone i autoryzowane i wybierz **nazwa usługi App service**.

     ![Wybierz usługi app service](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. W potoku > **artefaktów**, wybierają **+ Dodaj artefakt**, a następnie wybierz pozycję tworzenia za pomocą **ContosoSmarthotel360Refactor** potoku.

     ![Kompilacja](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. Kliknięciu błyskawicy zaznaczono artefaktu., aby włączyć wyzwalacz ciągłego wdrażania.

     ![Błyskawicy](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. Wyzwalacz ciągłego wdrażania, powinien być ustawiony na **włączone**.

    ![Ciągłe wdrażanie włączone](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. Teraz, ich przenieść z powrotem do zadania etap 1 I zadań i kliknij przycisk **wdrożenia usługi Azure App Service**.

    ![Wdrażanie usługi app service](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. W **zaznacz plik lub folder**, mogą zlokalizować **SmartHotel.Registration.Wcf.zip** pliku, który został tworzenie podczas kompilacji i kliknij przycisk **Zapisz**.

    ![Zapisz WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. Polecenie **potoku** > **etapów** **+ Dodaj**, aby dodać środowiska na potrzeby **SHWEB EUS2**. Użytkownik wybrać inne wdrożenie w usłudze Azure App Service.

    ![Dodanie środowiska](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. One Powtórz te czynności, aby opublikować aplikację sieci web (**SmartHotel.Registration.Web.zip**) pliku w aplikacji sieci web poprawne.

    ![Publikowanie aplikacji sieci web](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. Po zapisaniu, potok wydania zostaną wyświetlone w następujący sposób.

     ![Podsumowanie potok wydania](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. Ich przenieść z powrotem do **kompilacji**i kliknij przycisk **wyzwalaczy** > **Włącz ciągłą integrację**. Dzięki temu potoku, gdy zmiany zostaną zatwierdzone w kodzie, a pełne kompilowanie i wydawanie występuje.

    ![Włącz ciągłą integrację](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. Polecenie **Zapisz k & Olejką** uruchomić pełny potok. Nowa kompilacja jest wyzwalana, który z kolei tworzy pierwsze wydanie aplikacji w usłudze Azure App Service.

    ![Zapisz potoku](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Administratorzy firmy Contoso można postępuj zgodnie z kompilacji i wydania procesie potoku z DevOps platformy Azure. Po zakończeniu kompilacji, rozpocznie się wydania.

    ![Kompilowania i wydawania aplikacji](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. Po ukończeniu potoku obie witryny zostały wdrożone, a aplikacja będzie się i działa w trybie online.

    ![Zakończ wydania](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

W tym momencie aplikacji pomyślnej migracji na platformę Azure.



## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji firma Contoso potrzebuje do wykonania tych kroków czyszczenia:  

- Usuń lokalne maszyny wirtualne z magazynu programu vCenter.
- Usuń maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizuj wewnętrzną dokumentację, aby wyświetlić nowe lokalizacje dla aplikacji rozwiązania SmartHotel360. Pokaż bazy danych jako uruchomiona w bazie danych Azure SQL i frontonu jako uruchomiona w dwóch aplikacjach sieci web.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.


## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

- Firma Contoso potrzebuje w celu zapewnienia, że ich nowych **rejestracji SmartHotel** bazy danych jest bezpieczna. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- W szczególności Contoso, należy zaktualizować aplikacje sieci web do używania protokołu SSL przy użyciu certyfikatów.

### <a name="backups"></a>Tworzenie kopii zapasowych

- Firma Contoso potrzebuje zapoznać się z wymaganiami kopii zapasowej usługi Azure SQL Database. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso również potrzebuje dowiedzieć się więcej o zarządzaniu kopie zapasowe bazy danych SQL i przywrócenie. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) o automatycznych kopii zapasowych.
- Firmy Contoso powinien rozważyć grupy trybu failover, aby zapewnić rozwiązania regionalnej pracy awaryjnej dla bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Firma Contoso potrzebuje do zaleca się wdrożenie aplikacji sieci Web w głównym wschodnie stany USA 2 i środkowe stany USA region dla odporności na błędy. Firma Contoso może skonfigurować usługi Traffic Manager, aby upewnić się, pracy awaryjnej w przypadku regionalnej awarii.

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu wszystkich zasobów firmy Contoso, należy przypisać tagi platformy Azure na podstawie ich [planowania infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Wszystkie licencjonowania jest wbudowana w koszt usługi PaaS, które zużywa firmy Contoso. Ta ilość zostanie odjęta z umowy EA.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso zaprojektowane od nowa w aplikacji rozwiązania SmartHotel360 na platformie Azure przy użyciu funkcji migracji aplikacji frontonu maszyn wirtualnych do dwóch aplikacji sieci Web platformy Azure. Baza danych aplikacji została poddana migracji do usługi Azure SQL database.






