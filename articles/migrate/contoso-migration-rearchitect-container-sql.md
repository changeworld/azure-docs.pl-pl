---
title: Przekształcanie aplikacji firmy Contoso w usłudze Azure container i usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ponowne Ustalanie architektury aplikacji w kontenerach Windows Azure i usługi Azure SQL Database.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: cb984bcbe79b69c0614579d66a3b853cd38a7e12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690257"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migracja Contoso: Ponowne Ustalanie architektury aplikacji w środowisku lokalnym, do kontenera platformy Azure i usługi Azure SQL Database

W tym artykule pokazano, jak Contoso migruje i przekształcanie ich aplikacji rozwiązania SmartHotel360 na platformie Azure. Contoso migruje maszyny Wirtualnej frontonu aplikacji kontenera usługi Windows Azure i bazy danych aplikacji z bazą danych Azure SQL.

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso jest migrowana zasobów lokalnych do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodatkowe artykuły, zostanie dodany wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne 
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zakończeniu migracji lift-and-shift aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure, za pomocą usługi Azure Site Recovery | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługi Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso aplikacji osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery i bazy danych aplikacji jest migrowana do wystąpienia serwera usługi Azure MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso aplikacji rozwiązania SmartHotel360 jest migrowana do aplikacji sieci Web platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych | Dostępne
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w usłudze Azure Web Apps i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne 
[Artykuł 11: Refaktoryzuj TFS na usługom DevOps platformy Azure](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
Artykuł 12: Ponowne Ustalanie architektury aplikacji na kontenerów platformy Azure i usługi Azure SQL Database | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | W tym artykule
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne 
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne

W tym artykule Contoso migruje dwuwarstwowej WPF Windows, aplikacji rozwiązania SmartHotel360 forms XAML działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji, znajduje się jako "open source" i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Contoso IT zespół kierowniczy ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie, a w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Firmy Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**:  Contoso IT trzeba zwiększyć szybkość reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: W miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Koszty**: Firma Contoso chce, aby zminimalizować koszty licencjonowania.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele zostały użyte w celu ustalenia najlepszej metody migracji.

**Cele** | **Szczegóły**
--- | --- 
**Systemowe aplikacji** | Aplikacji na platformie Azure będą obowiązywać tak ważne, ponieważ jest już dziś.<br/><br/> Powinien on te same możliwości wydajności, jak obecnie w środowisku programu VMWare<br/><br/> Firma Contoso chce polegająca na wyłączeniu obsługi systemu Windows Server 2008 R2, na którym aplikacja obecnie działa i zgadzasz się inwestować w aplikacji.<br/><br/> Firma Contoso chce przesuwania kursora od programu SQL Server 2008 R2 na nowoczesnej platformie PaaS bazy danych, minimalizuje potrzebę zarządzania.<br/><br/> Firma Contoso ma wykorzystywać swoich inwestycji w Licencjonowanie programu SQL Server i pakietem Software Assurance, jeśli jest to możliwe.<br/><br/> Firma Contoso chce mieć możliwość skalowania w górę do warstwy sieci web aplikacji.
**Ograniczenia** | Aplikacja składa się z aplikacji platformy ASP.NET i usługi WCF uruchomionych na tej samej maszyny Wirtualnej. Firma Contoso chce podzielić to na dwie aplikacje internetowe przy użyciu usługi Azure App Service. 
**Systemowe platformy Azure** | Firma Contoso chce przenieść aplikację na platformę Azure, a następnie uruchom go w kontenerze, aby rozszerzyć życia aplikacji. Go nie chce zaczynać od całkowicie od nowa, aby wdrożyć aplikację na platformie Azure. 
**DevOps** | Firma Contoso chce przenieść do modelu DevOps za pomocą usługom DevOps platformy Azure dla kodu kompilacji i potoku wydania.

## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół celami i wymaganiami, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, używających Contoso do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- Aplikację lokalną rozwiązania SmartHotel360 są rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


### <a name="proposed-architecture"></a>Proponowana architektury

- W warstwie bazy danych aplikacji firmy Contoso w porównaniu z programu SQL Server przy użyciu usługi Azure SQL Database [w tym artykule](https://docs.microsoft.com/azure/sql-database/sql-database-features). Zdecydował się go z usługą Azure SQL Database dla kilka możliwych przyczyn:
    - Usługa Azure SQL Database to zarządzana usługa relacyjnej bazy danych. System ten zapewnia przewidywalną wydajność na różnych poziomach usługi, przy bliskich zeru nakładach na administrację. Korzyści obejmują dynamiczną skalowalność bez przestojów, wbudowany mechanizm inteligentnej optymalizacji i globalną skalowalność i dostępność.
    - Contoso korzysta z uproszczonego Data Migration Assistant (DMA) do oceny i migracji lokalnej bazy danych do bazy danych SQL Azure.
    - Z pakietem Software Assurance Contoso mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych w usłudze SQL Database za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server. Może to zapewnić oszczędności nawet do 30%.
    - SQL Database oferuje pewną liczbę funkcji zabezpieczeń, takich jak zawsze zaszyfrowane dane dynamiczne maskowanie i wykrywanie zagrożeń zabezpieczeń poziomu wierszy.
- W przypadku warstwy aplikacji sieci web firmy Contoso podjęła decyzję przekonwertować go do kontenera Windows przy użyciu usługom DevOps platformy Azure.
    - Contoso — wdrażanie aplikacji przy użyciu usługi Azure Service Fabric i Ściągnij obraz kontenera Windows z usługi Azure Container Registry (ACR).
    - Prototyp do rozszerzania możliwości aplikacji o analizę tonacji zostaną zaimplementowane jako innej usługi w usłudze Service Fabric podłączone do usługi Cosmos DB.  Spowoduje to odczytu informacji z Tweetów i wyświetlane w aplikacji.
- Do zaimplementowania potoku metodyki DevOps, Contoso użyje DevOps platformy Azure do zarządzania kodem źródłowym (SCM), korzystając z repozytoriów Git.  Zautomatyzowane kompilacje i wydania będzie służyć do tworzenia kodu, a następnie wdrożyć ją do usługi Azure Container Registry i usługi Azure Service Fabric.

    ![Architektura scenariusza](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Przegląd rozwiązania
Contoso ocenia proponowane projektu poprzez umieszczenie razem listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | Kod aplikacji rozwiązania SmartHotel360 będą musieli zmienić pod kątem migracji do usługi Azure Service Fabric. Jednak nakład pracy jest minimalny, za pomocą narzędzi zestawu SDK usługi Service Fabric dla zmian.<br/><br/> Wraz z przejściem do usługi Service Fabric firma Contoso może rozpocząć tworzenie mikrousług, aby dodać do aplikacji, szybko wraz z upływem czasu, bez ryzyka do oryginalnej bazy kodu.<br/><br/> Kontenery Windows oferuje te same korzyści w postaci kontenerów, ogólnie rzecz biorąc. Zwiększają elastyczność, przenoszenia i kontroli.<br/><br/> Contoso mogą korzystać z jej inwestycji w pakiet Software Assurance dla programu SQL Server i Windows Server za pomocą korzyści użycia hybrydowego platformy Azure.<br/><br/> Po zakończeniu migracji nie jest już należy do obsługi systemu Windows Server 2008 R2. [Dowiedz się więcej](https://support.microsoft.com/lifecycle).<br/><br/> Contoso można skonfigurować warstwa sieci web aplikacji z wieloma wystąpieniami tak, aby nie jest już pojedynczym punktem awarii.<br/><br/> Nie będzie zależny od przestarzałej programu SQL Server 2008 R2.<br/><br/> Usługa SQL Database obsługuje wymagania techniczne firmy Contoso. Administratorzy firmy Contoso oceny lokalnej bazy danych, używając Asystenta migracji bazy danych i można go odnaleźć zgodne.<br/><br/> Usługa SQL Database ma wbudowaną odporność na uszkodzenia, nie trzeba skonfigurować Contoso. Zapewnia to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | Kontenery są bardziej skomplikowane niż inne opcje migracji. Nauki kontenerów może to stanowić problem "contoso".  Wprowadzają nowe stopień złożoności, który zawiera wiele wartości, pomimo krzywej.<br/><br/> Zespół operacyjny w firmie Contoso należy podnieść do zrozumienia i pomocy technicznej platformy Azure, kontenery i mikrousługi aplikacji.<br/><br/> Jeśli firma Contoso używa programu Data Migration Assistant zamiast usługi migracji danych do migracji bazy danych, nie będzie mieć gotowy do migracji infrastruktury baz danych na dużą skalę.



### <a name="migration-process"></a>Proces migracji

1. Contoso aprowizuje klaster Azure usługi Service fabric dla Windows.
2. Ustanowienie wystąpienia usługi Azure SQL i bazy danych rozwiązania SmartHotel360 jest migrowana do niego.
3. Contoso konwertuje maszyna wirtualna w warstwie sieci Web do kontenera platformy Docker za pomocą narzędzi zestawu SDK usługi Service Fabric.
4. Łączy klaster usługi Service fabric i usługi ACR i wdraża aplikację za pomocą usługi Azure service fabric.

    ![Proces migracji](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Ocenia i wykrywa problemy ze zgodnością, które mogą mieć wpływ na funkcjonalność bazy danych na platformie Azure. Program DMA ocenia równoważność funkcji między źródłami SQL i elementy docelowe i zaleca wydajności i niezawodności. | Narzędzie to można pobrać bezpłatnie.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Zapewnia inteligentną i w pełni zarządzana usługa relacyjnej w chmurze bazy danych. | Koszt na podstawie funkcji, przepływności i rozmiaru. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Magazyny dowolnych typów obrazów wdrożeń kontenerów. | Koszt na podstawie funkcji, magazynu i czas trwania użycia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Kompilacje i obsługiwanie aplikacji zawsze włączone, skalowalne i rozproszone rozwiązanie | Koszt na podstawie rozmiaru, lokalizacji i czasu trwania węzłów obliczeniowych. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/service-fabric/).
[Usługa Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Zapewnia ciągłą integrację i potok ciągłego wdrażania (CI/CD) do tworzenia aplikacji. Potok rozpoczyna się od repozytorium Git na potrzeby zarządzania kodu aplikacji, system kompilacji na potrzeby tworzenia pakietów i innych artefaktów kompilacji i system zarządzania wydaniami, aby wdrożyć zmiany w deweloperów, testerów i środowisk produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

Oto, czego firma Contoso potrzebuje do uruchomienia tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Contoso utworzyć subskrypcje wcześniej w tym artykule. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso uprzednio skonfigurowane infrastruktury platformy Azure.
**Wymagania wstępne dla deweloperów** | Firma Contoso potrzebuje następujących narzędzi na stacji roboczej dewelopera:<br/><br/> - [Visual Studio 2017 Community Edition: W wersji 15.5](https://www.visualstudio.com/)<br/><br/> — Włączony pakiet roboczy .NET.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [V zestawu SDK usługi Service Fabric w wersji 3.0 lub nowszej](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (system Windows 10) lub Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) skonfigurowane do korzystania z kontenerów Windows.



## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak firmy Contoso jest wykonywany migracji:

> [!div class="checklist"]
> * **Krok 1. Aprowizowanie wystąpienia bazy danych SQL na platformie Azure**: Firmy Contoso inicjuje wystąpienie serwera SQL na platformie Azure. Po frontonu sieci web, które maszyna wirtualna jest migrowana do usługi Azure container wystąpienia kontenera za pomocą aplikacji frontonu sieci web będzie wskazywać tej bazy danych.
> * **Krok 2. Utwórz rejestr Azure Container Registry (ACR)**: Firmy Contoso Inicjuje obsługę rejestr kontenerów przedsiębiorstwa na potrzeby obrazów kontenerów platformy docker.
> * **Krok 3. Aprowizowanie usługi Azure Service Fabric**: Aprowizuje klaster usługi Service Fabric.
> * **Krok 4. Zarządzanie certyfikatami sieci szkieletowej usługi**: Contoso konfiguruje certyfikaty usługi Azure DevOps dostępu do usług w klastrze.
> * **Krok 5. Migracja bazy danych za pomocą programu DMA**: Jej zmigrowaniu bazy danych aplikacji przy użyciu Asystenta migracji bazy danych.
> * **Krok 6: Konfigurowanie usługi DevOps platformy Azure**: Contoso Konfiguruje nowy projekt w usłudze Azure Services DevOps i importuje kodu do repozytorium Git.
> * **Krok 7: Aplikację można przekonwertować**: Contoso konwertuje kontenera za pomocą narzędzi DevOps platformy Azure i zestawu SDK aplikacji.
> * **Krok 8: Konfigurowanie kompilacji i wydania**: Firma Contoso instaluje potoki kompilacji i wydań, tworzenie i publikowanie aplikacji w usłudze ACR i klaster usługi Service Fabric.
> * **Krok 9: Rozszerzanie aplikacji**: Gdy aplikacji jest publiczny, Contoso rozszerza je, aby móc korzystać z możliwości platformy Azure i je opublikuje go na platformie Azure przy użyciu potoku.



## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Aprowizowanie bazy danych Azure SQL

Administratorzy firmy Contoso aprowizować usługi Azure SQL database.

1. Wybierz, aby utworzyć **bazy danych SQL** na platformie Azure. 

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Określają nazwę bazy danych w celu dopasowania bazy danych uruchomione na maszynie Wirtualnej w środowisku lokalnym (**SmartHotel.Registration**). W grupie zasobów ContosoRG mogą umieścić bazę danych. To jest grupa zasobów, których używają zasobów w środowisku produkcyjnym na platformie Azure.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Konfigurowania nowego wystąpienia programu SQL Server (**eus2-sql-smarthotel**) w regionie podstawowym.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Ustawiają warstwy cenowej, odpowiednio do potrzeb serwera i bazy danych. I należy wybrać, aby oszczędzać pieniądze dzięki korzyści użycia hybrydowego platformy Azure, ponieważ mają one już licencję na program SQL Server.
5. W przypadku ustalania rozmiaru Użyj oparte na v-Core zakupu i Ustaw limity dla oczekiwanego wymagania.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Następnie utwórz wystąpienie bazy danych.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Po utworzeniu wystąpienia będzie otworzyć bazy danych i zanotuj szczegóły, które są im potrzebne podczas korzystania z pomocy migracji bazy danych do migracji.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Potrzebujesz dodatkowej pomocy?**

- [Uzyskaj Pomoc](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) aprowizacji bazy danych SQL.
- [Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) v-Core limity zasobów.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Krok 2: Tworzenie rejestru Azure container Registry i aprowizowanie usługi Azure Container

Kontenera platformy Azure zostanie utworzona wyeksportowane pliki z maszyny Wirtualnej w sieci Web. Kontener są przechowywane w usłudze Azure Container Registry (ACR).


1. Administratorzy firmy Contoso Tworzenie rejestru kontenerów w witrynie Azure portal.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Zapewniają one nazwę rejestru (**contosoacreus2**) i umieścić go w regionie podstawowym w grupie zasobów, które używają swoich zasobów infrastruktury. Włącz dostęp dla użytkowników, administrator i ustaw go jako SKU typu premium, dzięki czemu mogą korzystać z replikacji geograficznej.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Krok 3: Aprowizowanie usługi Azure Service Fabric

Kontener rozwiązania SmartHotel360 zostanie uruchomiony w klastrze usługi Azure Service Fabric. Administratorzy firmy Contoso Utwórz klaster usługi Service Fabric w następujący sposób:

1. Utwórz zasób usługi Service Fabric w portalu Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. W **podstawy**, zapewniają unikatową nazwę usługi Katalogowej dla klastra i poświadczenia na potrzeby uzyskiwania dostępu do lokalnej maszyny Wirtualnej. Zasób ich umieszczenia w grupie zasobów w środowisku produkcyjnym (**ContosoRG**) w regionie wschodnie stany USA 2 podstawowym.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. W **konfiguracja typu węzła**, ich dane wejściowe, nazwa typu węzła, ustawienia trwałości, rozmiar maszyny Wirtualnej i punktów końcowych aplikacji.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. W **Tworzenie magazynu kluczy**, tworzą nowy magazyn kluczy w swojej grupie zasobów infrastruktury do przechowywania certyfikatu.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. W **zasady dostępu** umożliwiają dostęp do maszyn wirtualnych do wdrożenia usługi key vault.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Określają nazwę certyfikatu.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Na stronie podsumowania kopiują łącze, które jest używane do pobierania certyfikatu. Muszą one tutaj, aby nawiązać połączenie z klastra usługi Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Po pomyślnej weryfikacji, ich inicjowania obsługi klastra.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. W Kreatorze importowania certyfikatów zaimportowali pobranego certyfikatu na komputerach deweloperskich. Certyfikat jest używany do uwierzytelniania w klastrze.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Po zainicjowaniu obsługi klastra, łączą się z Eksploratora klastra usługi Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Należy wybrać właściwy certyfikat.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Obciążenia narzędzia Service Fabric Explorer i administratora firmy Contoso można zarządzać klastrem.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>Krok 4: Zarządzanie certyfikatami w usłudze Service Fabric

Firma Contoso potrzebuje certyfikatów klastra, aby zezwolić na dostęp usługom DevOps platformy Azure do klastra. Administratorzy firmy Contoso to skonfigurować.

1. One Otwórz witrynę Azure portal i przejdź do magazynu kluczy.
2. Otwórz przystawkę Certyfikaty i skopiuj odcisk palca certyfikatu, który został utworzony podczas procesu inicjowania obsługi administracyjnej.

    ![Skopiuj odcisk palca](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. One skopiuj go do pliku tekstowego do późniejszego wykorzystania.
4. Teraz Dodaj certyfikat klienta, który ma zostać certyfikat klienta administracyjnego w klastrze. Dzięki usługom DevOps platformy Azure, nawiązać połączenia z klastrem na potrzeby wdrażania aplikacji w potoku wydań. W celu ich należy otworzyć magazyn kluczy w portalu i wybierz **certyfikaty** > **Generuj/Import**.

    ![Generowanie certyfikatu klienta](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. Wprowadź nazwę certyfikatu i podaj nazwę wyróżniającą X.509 w **podmiotu**.

     ![Nazwa certyfikatu](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. Po utworzeniu certyfikatu będą oni mogli pobrać ją lokalnie w formacie PFX.

     ![Pobierz certyfikat](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. Teraz one wróć do listy certyfikatów w magazynie kluczy i skopiuj odcisk palca certyfikatu klienta, który właśnie został utworzony. Ich zapisywania w pliku tekstowym.

     ![Odcisk palca certyfikatu klienta](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. W przypadku wdrożenia usługi DevOps platformy Azure muszą określić wartość Base64 certyfikatu. One to robić na stacji roboczej dewelopera lokalnego przy użyciu programu PowerShell. Ich wkleić dane wyjściowe do pliku tekstowego do późniejszego użycia.

    ```powershell
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Wartość w formacie Base64](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Na koniec dodają nowy certyfikat do klastra usługi Service Fabric. Aby to zrobić, w portalu, otwórz klaster i kliknij przycisk **zabezpieczeń**.

     ![Dodaj certyfikat klienta](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. Polecenie **Dodaj** > **Klient administracyjny**i Wklej odcisk palca nowego certyfikatu klienta. A następnie kliknięciu **Dodaj**. Może to potrwać do 15 minut.

     ![Dodaj certyfikat klienta](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>Krok 5. Migracja bazy danych za pomocą programu DMA

Administratorzy firmy Contoso teraz przeprowadzać migrację bazy danych rozwiązania SmartHotel360, przy użyciu narzędzia DMA.

### <a name="install-dma"></a>Instalowanie programu DMA

1. Będą oni mogli pobrać narzędzia z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) do maszyny Wirtualnej w środowisku lokalnym SQL Server (**SQLVM**).
2. Instalatora (DownloadMigrationAssistant.msi), są one uruchomione na maszynie Wirtualnej.
3. Na **Zakończ** strony, wybierają **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

### <a name="configure-the-firewall"></a>Konfigurowanie zapory

Aby połączyć z usługą Azure SQL Database, Administratorzy firmy Contoso, skonfiguruj regułę zapory, aby zezwolić na dostęp.

1. W **zapory i sieci wirtualne** właściwości bazy danych, Zezwalaj na dostęp do usług platformy Azure i dodać regułę dla tego adresu IP klienta z maszyny Wirtualnej w środowisku lokalnym SQL Server.
2. Jest tworzona reguła zapory poziomu serwera.

    ![Zapora](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Potrzebujesz dodatkowej pomocy?

[Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) tworzenie i zarządzanie regułami zapory usługi Azure SQL Database.

### <a name="migrate"></a>Migrate (Migracja)

Administratorzy firmy Contoso teraz migracji bazy danych.

1. W narzędziu DMA Utwórz nowy projekt (**SmartHotelDB**) i wybierz **migracji** 
2. Wybierają typ serwera źródłowego jako **programu SQL Server**, jak i obiektem docelowym jako **usługi Azure SQL Database**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Informacje dotyczące migracji, co zwiększa **SQLVM** co serwer źródłowy i **SmartHotel.Registration** bazy danych. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Otrzyma błąd, który wydaje się być skojarzony z uwierzytelnianiem. Jednak po przeanalizowaniu, problem polega na kropki (.) w nazwie bazy danych. Jako obejście decyzję o udostępnianie nowej bazy danych SQL przy użyciu nazwy **rejestracji SmartHotel**, aby rozwiązać ten problem. Po uruchomieniu narzędzia DMA ponownie, są one możliwość wyboru **SmartHotel rejestracji**i kontynuować pracę w kreatorze.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. W **zaznacz obiekty**, wybierz tabele bazy danych i wygenerować skryptu SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Po utworzeniu usługi DMS skryptu, klikając **Wdróż schemat**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. Program DMA potwierdza, że wdrożenie zakończyło się pomyślnie.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Po rozpoczęciu migracji.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Po zakończeniu migracji, Contoso można sprawdzić, czy baza danych jest uruchomiony w wystąpieniu programu Azure SQL.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Usunięcie dodatkowych bazy danych SQL **SmartHotel.Registration** w witrynie Azure portal.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-azure-devops-services"></a>Krok 6: Konfigurowanie usługi DevOps platformy Azure

Firma Contoso potrzebuje do tworzenia infrastruktury DevOps i potoki pod kątem aplikacji.  Aby to zrobić, Administratorzy Contoso Utwórz nowy projekt DevOps platformy Azure, zaimportuj swój kod i następnie potoki kompilacji i wydań.

1.   W ramach konta Contoso DevOps platformy Azure tworzą nowy projekt (**ContosoSmartHotelRearchitect**) i wybierz **Git** kontroli wersji.
![Nowy projekt](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. Zaimportowali repozytorium Git, zawierający kod aplikacji. Jest on [publicznego repozytorium](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) i można go pobrać.

    ![Pobieranie kodu aplikacji](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. Po zaimportowaniu kod będzie łączenie programu Visual Studio do repozytorium i klonowanie kodu za pomocą programu Team Explorer.

4. Po repozytorium zostanie sklonowany maszyny dewelopera, ich otworzyć plik rozwiązania dla aplikacji. Usługi wcf i aplikacji sieci web każda z nich ma oddzielić projektu w pliku.

    ![Plik rozwiązania](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>Krok 7: Aplikację można przekonwertować do kontenera

Aplikacja w środowisku lokalnym jest tradycyjnych aplikacji trzy warstwy:

- Zawiera on formularzy sieci Web i usługi WCF, łączenie z serwerem SQL.
- Używa platformy Entity Framework do integracji z danymi w bazie danych SQL, uwidaczniania go za pośrednictwem usługi WCF.
- Aplikacja formularzy sieci Web wchodzi w interakcje z usługą WCF.

Administratorzy firmy Contoso przekonwertuje aplikacji kontenera za pomocą programu Visual Studio i SDK Tools, w następujący sposób:


1. W programie Visual Studio mogą zapoznać się z otwartego pliku rozwiązania (SmartHotel.Registration.sln) w **rozwiązania SmartHotel360 — wewnętrzne — rezerwacji apps\src\Registration** katalogu lokalnego repozytorium.  Dwie aplikacje są wyświetlane. Frontony sieci web SmartHotel.Registration.Web i aplikacja usługi WCF SmartHotel.Registration.WCF.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. One kliknij prawym przyciskiem myszy aplikację sieci web > **Dodaj** > **obsługi Orkiestratora kontenerów**.
3. W **Dodaj Obsługa Orkiestra kontenerów**, wybierają **usługi Service Fabric**.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. One należy powtórzyć dla SmartHotel.Registration.WCF aplikacji.
5. Teraz sprawdzają się, jak rozwiązanie został zmieniony.

   - Nowa aplikacja jest **SmartHotel.RegistrationApplication/**
   - Zawiera dwie usługi: **SmartHotel.Registration.WCF** i **SmartHotel.Registration.Web**.

     ![Kontener](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. Visual Studio utworzono plik Docker i obniżona wymagane obrazy lokalnie na komputerze dewelopera.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. Plik manifestu (**ServiceManifest.xml**) zostanie utworzony i otwarty przez program Visual Studio. Ten plik nakazuje usługi Service Fabric, jak skonfigurować kontenera, gdy aplikacja jest wdrożona na platformie Azure.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. Inny plik manifestu (** ApplicationManifest.xml) zawiera aplikacje configuration dla kontenerów.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. Adresat otworzy **ApplicationParameters/Cloud.xml** pliku i zaktualizuj parametry połączenia, aby połączyć aplikację z bazą danych Azure SQL. Parametry połączenia mogą znajdować się w bazie danych w witrynie Azure portal.

    ![Parametry połączenia](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. Zatwierdzeniem zaktualizowany kod go i przekazaniem do usługi DevOps platformy Azure.

    ![Zatwierdzenie](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>Krok 8: Potoki w usłudze Azure DevOps Services kompilacji i wydań

Administratorzy firmy Contoso teraz skonfigurować usługom DevOps platformy Azure do wykonywania kompilacji i procesu tworzenia wersji do akcji, metodykę DevOps.

1. W usługach infrastruktury DevOps platformy Azure, kliknij przycisk **kompilowania i wydawania** > **nowy potok**.

    ![Nowy potok](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. Wybierają **Azure DevOps usługi Git** i odpowiedniego repozytorium.

    ![Git i repozytorium](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. W **wybierz szablon**, wybierają sieci szkieletowej o obsługę platformy Docker.

     ![Service Fabric i platformy Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. Umożliwiają one zmianę obrazów Tag akcji na **Zbuduj obraz**i skonfigurować zadania przy użyciu aprowizowanej usługi ACR.

     ![Rejestr](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. W **wypychanie obrazów** zadań, ich skonfigurować obraz, który ma zostać wypchnięty do usługi ACR i zaznacz, aby uwzględnić najnowszą tagu.
6. W **wyzwalaczy**, Włącz ciągłą integrację i Dodaj gałęzi głównej.

    ![Wyzwalacze](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. Polecenie **Zapisz i kolejki** do uruchomienia kompilacji.
8. Po pomyślnym zainicjowaniu kompilacji podczas przenoszenia operacji do potoku tworzenia wersji. W usłudze Azure DevOps Services kliknij pozycję **wersji** > **nowy potok**.

    ![Potok wydania](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. Wybierają **wdrożenia usługi Azure Service Fabric** szablonu i nazwy etapu (**SmartHotelSF**).

    ![Środowisko](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Zapewniają one Nazwa potoku (**ContosoSmartHotel360Rearchitect**). Etap, kliknij przycisk **zadanie 1, 1 zadanie** umożliwia skonfigurowanie wdrażania usługi Service Fabric.

    ![Faza i zadania](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. Teraz kliknij **New** można dodać nowe połączenie klastra.

    ![Nowe połączenie](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. W **połączenia z usługą Dodawanie usługi Service Fabric**, konfigurują połączenia i ustawienia uwierzytelniania, które będą używane przez usługi DevOps platformy Azure do wdrożenia aplikacji. Punkt końcowy klastra może znajdować się w witrynie Azure portal i dodać **tcp: / /** jako prefiksu.
13. Informacje o certyfikacie, które są zbierane są dane wejściowe w **odcisk palca certyfikatu serwera** i **certyfikat klienta**.

    ![Certyfikat](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. Polecenie potoku > **Dodawanie artefaktu**.

     ![Artefakt](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. Zaznacz projekt, a tworzenie potoku za pomocą najnowszej wersji.

     ![Kompilacja](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Należy pamiętać, że zaznaczono błyskawicy artefaktu.

     ![Stan artefaktu](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Ponadto należy pamiętać, że włączono wyzwalacz ciągłego wdrażania.

    ![Ciągłe wdrażanie włączone](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. Polecenie **Zapisz** > **utworzyć wydanie**.

    ![Release](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. Po zakończeniu wdrażania rozwiązania SmartHotel360 będzie teraz być uruchomiona Usługa Service Fabric.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. Aby połączyć aplikację, mogą kierować ruch do publicznego adresu IP modułu równoważenia obciążenia platformy Azure, przed zestawem węzłów usługi Service Fabric.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>Krok 9: Rozszerz aplikację i ponownie opublikuj

Po uruchomieniu aplikacji rozwiązania SmartHotel360 i bazy danych na platformie Azure, firma Contoso chce rozszerzyć aplikację.

- Deweloperzy firmy Contoso mają tworzenia prototypów nową aplikację platformy .NET Core, która będzie działać w klastrze usługi Service Fabric.
- Aplikacja będzie służyć do pobierania danych wskaźniki nastrojów klientów z bazy danych cosmos DB.
- Te dane będą w formie Tweety, które są przetwarzane przy użyciu Bezserwerowej funkcji platformy Azure i Cognitive Services interfejsu API analizy tekstu.

### <a name="provision-azure-cosmos-db"></a>Aprowizowanie usługi Azure Cosmos DB

Pierwszym krokiem Administratorzy Contoso aprowizować bazy danych Azure Cosmos.

1. Tworzą zasób usługi Azure Cosmos DB w witrynie Azure Marketplace.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Zapewniają one nazwę bazy danych (**contososmarthotel**), a następnie wybierz interfejs API SQL i umieść zasobu w produkcyjnej grupie zasobów w regionie wschodnie stany USA 2 podstawowym.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. W **wprowadzenie**, wybierają **Eksplorator danych**i Dodaj nową kolekcję.
4. W **Dodaj kolekcję** zapewniają identyfikatorów i ustaw pojemność magazynu i przepływności.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. W portalu, otwarciu nowej bazy danych > **kolekcji** > **dokumenty** i kliknij przycisk **nowy dokument**.
6. One Wklej następujący kod JSON do okna dokumentu. To przykładowe dane w postaci jednego tweetu.

    ```json
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Mogą zlokalizować punktu końcowego usługi Cosmos DB i klucza uwierzytelniania. Są one używane w aplikacji połączyć się z kolekcji. W bazie danych, kliknij przycisk **klucze**, a następnie skopiuj identyfikator URI i klucz podstawowy do Notatnika.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Aktualizowanie aplikacji tonacji

Usługa Cosmos DB zainicjowano obsługę administracyjną Contoso Administratorzy mogą skonfigurować aplikację, aby nawiązać z nim.

1. W programie Visual Studio one Otwórz plik ApplicationModern\ApplicationParameters\cloud.xml w Eksploratorze rozwiązań.

    ![Aplikacja tonacji](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Wypełnij one następujące dwa parametry:

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Aplikacja tonacji](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Ponownie opublikować aplikację

Po rozszerzeniu aplikacji, Administratorzy Contoso ponownie ją opublikować na platformie Azure przy użyciu potoku.

1. One Zatwierdź i Wypchnij swój kod do usługom DevOps platformy Azure. Dotyczącego potoki kompilacji i wydania.

2. Po zakończeniu kompilowania i wdrażania rozwiązania SmartHotel360 będzie teraz być uruchomiona Usługa Service Fabric. Konsola zarządzania siecią szkieletową usługi zawiera teraz trzy usługi.

    ![Ponownie opublikuj](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. Teraz po kliknięciu za pośrednictwem usług, aby zobaczyć, że aplikacja SentimentIntegration jest uruchomiona

    ![Ponownie opublikuj](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji firma Contoso potrzebuje do wykonania tych kroków czyszczenia:  

- Usuń lokalne maszyny wirtualne z magazynu programu vCenter.
- Usuń maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizuj wewnętrzną dokumentację, aby wyświetlić nowe lokalizacje dla aplikacji rozwiązania SmartHotel360. Pokaż bazy danych jako uruchomiona w bazie danych Azure SQL i frontonu jako uruchomiona w usłudze Service Fabric.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.


## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

- Administratorzy firmy Contoso należy upewnić się, że ich nowych **rejestracji SmartHotel** bazy danych jest bezpieczna. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- W szczególności powinny one aktualizowane kontenera do używania protokołu SSL przy użyciu certyfikatów.
- Powinni rozważyć użycie magazynu kluczy do ochrony wpisu tajnego dla swoich aplikacji usługi Service Fabric. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Tworzenie kopii zapasowych

- Firma Contoso potrzebuje zapoznać się z wymaganiami kopii zapasowej usługi Azure SQL Database. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Administratorzy firmy Contoso powinien rozważyć grupy trybu failover, aby zapewnić rozwiązania regionalnej pracy awaryjnej dla bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Mogą one używać replikacji geograficznej rejestru Azure container Registry — wersja Premium jednostki SKU. [Dowiedz się więcej](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Firmy Contoso, należy rozważyć wdrożenie aplikacji sieci Web w regionie środkowe stany USA i główne wschodnie stany USA 2, po udostępnieniu Web App for Containers. Administratorzy firmy Contoso, można skonfigurować usługi Traffic Manager, aby upewnić się, pracy awaryjnej w przypadku regionalnej awarii.
- Usługa cosmos DB tworzy kopie zapasowe automatycznie. Contoso [przeczytaj](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) ten proces, aby dowiedzieć się więcej.

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu wszystkich zasobów firmy Contoso, należy przypisać tagi platformy Azure na podstawie [planowania infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Wszystkie licencjonowania jest wbudowana w koszt usługi PaaS, które zużywa firmy Contoso. Ta ilość zostanie odjęta z umowy EA.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso zaprojektowane od nowa w aplikacji rozwiązania SmartHotel360 na platformie Azure przy użyciu funkcji migracji maszyny Wirtualnej frontonu aplikacji do usługi Service Fabric. Baza danych aplikacji została poddana migracji do usługi Azure SQL database.





