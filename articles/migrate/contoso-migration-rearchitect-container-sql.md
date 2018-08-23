---
title: Przekształcanie aplikacji firmy Contoso w usłudze Azure container i usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ponowne Ustalanie architektury aplikacji w kontenerach Windows Azure i usługi Azure SQL Database.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 733a93d0fc80d86d28f13a9e1d32108b58893bf0
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "42055389"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migracja Contoso: Przekształcanie aplikacji lokalnych do usługi kontenera platformy Azure i usługi Azure SQL Database

W tym artykule pokazano, jak Contoso migruje i przekształcanie ich SmartHotel aplikacji na platformie Azure. Fronton aplikacji maszyny Wirtualnej oni migrować do kontenera usługi Windows Azure i bazę danych aplikacji do usługi Azure SQL database.

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
[Artykuł 9: Refaktoryzacja aplikację z bazą danych Azure SQL i aplikacji sieci Web platformy Azure](contoso-migration-refactor-web-app-sql.md) | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux do aplikacji sieci Web platformy Azure i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne
[Artykuł 11: Refaktoryzacja TFS w usłudze VSTS](contoso-migration-tfs-vsts.md) | Pokazuje, jak Contoso migruje ich lokalnego wdrożenia Team Foundation Server (TFS), migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure.
Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | W tym artykule
[Artykuł 13: Ponownie skompilować aplikację na platformie Azure](contoso-migration-rebuild.md) | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | Dostępne

W tym artykule Contoso migruje Windows dwuwarstwowy. Aplikacja NET SmartHotel działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji, znajduje się jako "open source" i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso się, a w rezultacie istnieje nacisk na swoich systemów lokalnych i infrastruktury.
- **Zwiększenie wydajności**: Contoso musi usunąć niepotrzebne procedur i usprawniać procesy dla deweloperów i użytkowników.  Potrzeby biznesowe IT szybkie i nie odpadów czasu lub pieniądze, w związku z tym dostarczania szybciej na wymagania klientów.
- **Zwiększenie elastyczności**: Contoso IT musi być w reakcji na potrzeby biznesowe. Musi być w stanie szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki reagować.  Nie może pobrać w taki sposób, lub stają się blocker biznesowych.
- **Skala**: w miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Koszty**: Contoso chce zminimalizować koszty licencjonowania.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte dół cele tej migracji. Te cele zostały użyte w celu ustalenia najlepszej metody migracji.

**Cele** | **Szczegóły**
--- | --- 
**Systemowe aplikacji** | Aplikacji na platformie Azure będą obowiązywać tak ważne, ponieważ jest już dziś.<br/><br/> Powinien on te same możliwości wydajności, jak obecnie w środowisku programu VMWare<br/><br/> Firma chce polegająca na wyłączeniu obsługi systemu Windows Server 2008 R2, na którym aplikacja obecnie działa i zgadzasz się inwestować w aplikacji.<br/><br/> Firma chce przesuwania kursora od programu SQL Server 2008 R2 na nowoczesnej platformie PaaS bazy danych, minimalizuje potrzebę zarządzania.<br/><br/> Firma Contoso ma wykorzystanie inwestycji w Licencjonowanie programu SQL Server i pakietem Software Assurance, jeśli jest to możliwe.<br/><br/> Firma chce mieć możliwość skalowania w górę do warstwy sieci web aplikacji.
**Ograniczenia** | Aplikacja składa się z aplikacji platformy ASP.NET i usługi WCF uruchomionych na tej samej maszyny Wirtualnej. Firma chce podzielić to na dwie aplikacje internetowe przy użyciu usługi Azure App Service. 
**Systemowe platformy Azure** | Firma chce przenieść aplikację na platformę Azure, a następnie uruchom go w kontenerze, aby rozszerzyć życia aplikacji. Nie chcą rozpocząć całkowicie od nowa, aby wdrożyć aplikację na platformie Azure. 

## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół swoje cele i wymagania, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, które będą używały do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- SmartHotel aplikacji w środowisku lokalnym są rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


### <a name="proposed-architecture"></a>Proponowana architektury

- W warstwie bazy danych aplikacji firmy Contoso w porównaniu z programu SQL Server przy użyciu usługi Azure SQL Database [w tym artykule](https://docs.microsoft.com/azure/sql-database/sql-database-features). Zespół postanowił do korzystania z usługi Azure SQL Database z kilku powodów:
    - Usługa Azure SQL Database to zarządzana usługa relacyjnej bazy danych. System ten zapewnia przewidywalną wydajność na różnych poziomach usługi, przy bliskich zeru nakładach na administrację. Korzyści obejmują dynamiczną skalowalność bez przestojów, wbudowany mechanizm inteligentnej optymalizacji i globalną skalowalność i dostępność.
    - Mogą one używać uproszczonej Data Migration Assistant (DMA) do oceny i migracji lokalnej bazy danych do bazy danych SQL Azure.
    - Z pakietem Software Assurance mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych w usłudze SQL Database za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server. Może to zapewnić oszczędności nawet do 30%.
    - SQL Database oferuje pewną liczbę funkcji zabezpieczeń, takich jak zawsze zaszyfrowane dane dynamiczne maskowanie i wykrywanie zagrożeń zabezpieczeń poziomu wierszy.
- W przypadku warstwy aplikacji sieci web już ustalona przekonwertować go do kontenera Windows przy użyciu programu Visual Studio.
    - One zostanie wdrożona za pomocą usługi Azure Service Fabric i Ściągnij obraz kontenera Windows z usługi Azure Container Registry (ACR).
    - Prototyp do rozszerzania możliwości aplikacji o analizę tonacji zostaną zaimplementowane jako innej usługi w usłudze Service Fabric podłączone do usługi Cosmos DB.  Spowoduje to odczytu informacji z Tweetów i wyświetlane w aplikacji.

    ![Architektura scenariusza](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Przegląd rozwiązania
Contoso ocenia swoich konstrukcjach proponowanych przez zestawiania listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | SmartHotel kodu aplikacji będzie konieczne można zmienić pod kątem migracji do usługi Azure Service Fabric. Jednak nakład pracy jest minimalny, za pomocą narzędzi zestawu SDK usługi Service Fabric dla zmian.<br/><br/> Wraz z przejściem do usługi Service Fabric można uruchomić na tworzenie mikrousług, aby dodać do aplikacji, szybko wraz z upływem czasu, bez ryzyka do oryginalnej bazy kodu.<br/><br/> Kontenery Windows oferuje te same korzyści w postaci kontenerów, ogólnie rzecz biorąc. Zwiększają elastyczność, przenoszenia i kontroli.<br/><br/> Mogą one używać swoich inwestycji związanych z pakietem Software Assurance dla programu SQL Server i Windows Server za pomocą korzyści użycia hybrydowego platformy Azure.<br/><br/> Po zakończeniu migracji one już należy do obsługi systemu Windows Server 2008 R2. [Dowiedz się więcej](https://support.microsoft.com/lifecycle).<br/><br/> Warstwa sieci web aplikacji z wieloma wystąpieniami można skonfigurować tak, aby nie jest już pojedynczym punktem awarii.<br/><br/> Współpracownicy będą nie jest już zależny od przestarzałej programu SQL Server 2008 R2.<br/><br/> Usługa SQL Database obsługuje wymagania techniczne firmy Contoso. Ich użycia w celu oceny lokalnej bazy danych, używając Asystenta migracji bazy danych i wykrył, że jest zgodny.<br/><br/> Usługa SQL Database ma wbudowaną odporność na uszkodzenia, który Contoso trzeba skonfigurować. Zapewnia to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | Kontenery są bardziej skomplikowane niż inne opcje migracji. Nauki kontenerów może to stanowić problem "contoso".  Wprowadzają nowe stopień złożoności, który zawiera wiele wartości, pomimo krzywej.<br/><br/> Zespół operacyjny w firmie Contoso należy podnieść do zrozumienia i pomocy technicznej platformy Azure, kontenery i mikrousługi aplikacji.<br/><br/> Jeśli korzystają z programu Data Migration Assistant zamiast usługi migracji danych do migracji ich bazy danych, Contoso, nie będziesz mieć gotowy do migracji infrastruktury baz danych na dużą skalę.



### <a name="migration-process"></a>Proces migracji

1. Contoso aprowizować klaster platformy Azure usługi Service fabric dla Windows.
2. Aprowizowanie wystąpienia usługi Azure SQL i migrację bazy danych SmartHotel do niego.
3. Maszyna wirtualna w warstwie Web konwertują do kontenera platformy Docker za pomocą narzędzi zestawu SDK usługi Service Fabric.
4. Połącz klaster usługi Service fabric i usługi ACR i wdrażanie aplikacji przy użyciu usługi Azure service fabric.

    ![Proces migracji](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Użyjemy ich DMA do oceny i wykrycia problemów ze zgodnością, które mogą mieć wpływ na ich funkcje bazy danych na platformie Azure. Program DMA ocenia równoważność funkcji między źródłami SQL i elementy docelowe i zaleca wydajności i niezawodności. | Narzędzie to można pobrać bezpłatnie.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentna, w pełni zarządzana usługa relacyjnej w chmurze bazy danych. | Koszt na podstawie funkcji, przepływności i rozmiaru. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Usługa Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Store obrazów wdrożeń kontenerów dowolnego typu. | Koszt na podstawie funkcji, magazynu i czas trwania użycia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Tworzenia oraz obsługiwania aplikacji zawsze włączone, skalowalne i rozproszone rozwiązanie | Koszt na podstawie rozmiaru, lokalizacji i czasu trwania węzłów obliczeniowych. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Wymagania wstępne

Oto, co Ty (i firmy Contoso) potrzebne do uruchomienia tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.
**Wymagania wstępne dla deweloperów** | Firma Contoso potrzebuje następujących narzędzi na stacji roboczej dewelopera:<br/><br/> - [Programu Visual Studio 2017 Community Edition: W wersji 15.5](https://www.visualstudio.com/)<br/><br/> — Włączony pakiet roboczy .NET.<br/><br/> - [usługi git](https://git-scm.com/)<br/><br/> - [V zestawu SDK usługi Service Fabric w wersji 3.0 lub nowszej](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (system Windows 10) lub Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) skonfigurowane do korzystania z kontenerów Windows.



## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1: Aprowizowanie wystąpienia bazy danych SQL na platformie Azure**: Contoso Inicjuje obsługę wystąpienie serwera SQL na platformie Azure. Po frontonu sieci web, które maszyna wirtualna jest migrowana do usługi Azure container wystąpienia kontenera za pomocą aplikacji frontonu sieci web będzie wskazywać tej bazy danych.
> * **Krok 2: Świadczenia usługi Azure Service Fabric**: one aprowizować klaster usługi Service Fabric.
> * **Krok 4: Migrację bazy danych za pomocą programu DMA**: migrowania bazy danych aplikacji przy użyciu Asystenta migracji bazy danych.
> * **Krok 5: Aplikację można przekonwertować na kontener**: konwertują aplikacji do kontenera za pomocą programu Visual Studio i SDK Tools.
> * **Krok 6: Publikowanie aplikacji**: publikować aplikację do usługi ACR i klaster usługi Service Fabric.
> * **Krok 7: Rozszerzanie aplikacji**: gdy aplikacji jest publiczny, mogą go rozszerzyć w celu skorzystaj z możliwości platformy Azure i ponownie ją opublikować na platformie Azure.



## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Aprowizowanie bazy danych Azure SQL

1. Należy wybrać, aby utworzyć bazę danych SQL na platformie Azure. 

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Określają nazwę bazy danych w celu dopasowania bazy danych uruchomione na maszynie Wirtualnej w środowisku lokalnym (**SmartHotel.Registration**). W grupie zasobów ContosoRG mogą umieścić bazę danych. To jest grupa zasobów, których używają zasobów w środowisku produkcyjnym na platformie Azure.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Konfigurowania nowego wystąpienia programu SQL Server (**eus2-sql-smarthotel**) w regionie podstawowym.

    ![Aprowizacja SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Ustawiają warstwę cenową, aby dopasować ich serwer i bazę danych. I należy wybrać, aby oszczędzać pieniądze dzięki korzyści użycia hybrydowego platformy Azure, ponieważ mają one już licencję na program SQL Server.
5. W przypadku ustalania rozmiaru Użyj oparte na v-Core zakupu, a Ustaw limity dla ich oczekiwanego wymagań.

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


1. Contoso — tworzy rejestr kontenera w witrynie Azure portal.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Zapewniają one nazwę rejestru (**contosoacreus2**) i umieścić go w regionie podstawowym w grupie zasobów, które używają swoich zasobów infrastruktury. Włącz dostęp dla użytkowników, administrator i ustaw go jako SKU typu premium, dzięki czemu mogą korzystać z replikacji geograficznej.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Krok 3: Aprowizowanie usługi Azure Service Fabric

Kontener SmartHotel będzie działał w Sluster sieci szkieletowej usługi platformy Azure. Contoso tworzy klaster usługi Service Fabric w następujący sposób:

1. Utwórz zasób usługi Service Fabric w portalu Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. W **podstawowe**, zapewniają unikatową nazwę usługi Katalogowej dla klastra i poświadczenia na potrzeby uzyskiwania dostępu do lokalnej maszyny Wirtualnej. Zasób ich umieszczenia w grupie zasobów w środowisku produkcyjnym (**ContosoRG**) w regionie wschodnie stany USA 2 podstawowym.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. W **konfiguracja typu węzła**, ich dane wejściowe, nazwa typu węzła, ustawienia trwałości, rozmiar maszyny Wirtualnej i punktów końcowych aplikacji.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. W **Tworzenie magazynu kluczy**, tworzą nowy magazyn kluczy w swojej grupie zasobów infrastruktury do przechowywania certyfikatu.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. W **zasady dostępu** one eanble dostęp do maszyn wirtualnych do wdrożenia usługi key vault.

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


## <a name="step-3-migrate-the-database-with-dma"></a>Krok 3: Migrację bazy danych za pomocą programu DMA

Contoso zostaną zmigrowane SmartHotel bazy danych przy użyciu narzędzia DMA.

### <a name="install-dma"></a>Instalowanie programu DMA

1. Będą oni mogli pobrać narzędzia z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) do maszyny Wirtualnej w środowisku lokalnym SQL Server (**SQLVM**).
2. Instalatora (DownloadMigrationAssistant.msi), są one uruchomione na maszynie Wirtualnej.
3. Na **Zakończ** strony, wybierają **Uruchom program Microsoft Data Migration Assistant** przed zakończeniem pracy kreatora.

### <a name="configure-the-firewall"></a>Konfigurowanie zapory

Aby połączyć z usługą Azure SQL Database, regułę zapory jest wymagana.

1. W **zapory i sieci wirtualne** właściwości bazy danych, Zezwalaj na dostęp do usług platformy Azure i dodać regułę dla tego adresu IP klienta z maszyny Wirtualnej w środowisku lokalnym SQL Server.
2. Jest tworzona reguła zapory poziomu serwera.

    ![Zapora](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Potrzebujesz dodatkowej pomocy?

[Dowiedz się więcej o](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) tworzenie i zarządzanie regułami zapory usługi Azure SQL Database.

### <a name="migrate"></a>Migrate (Migracja)

1. W narzędziu DMA Utwórz nowy projekt (**SmartHotelDB**) i wybierz **migracji** 
2. Wybierają typ serwera źródłowego jako **programu SQL Server**, jak i obiektem docelowym jako **usługi Azure SQL Database**. 

    ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Informacje dotyczące migracji, co zwiększa **SQLVM** co serwer źródłowy i **SmartHotel.Registration** bazy danych. 

     ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Otrzyma błąd, który wydaje się być skojarzony z uwierzytelnianiem. Jednak po przeanalizowaniu, problem polega na kropki (.) w nazwie bazy danych. Jako obejście decyzję o udostępnianie nowej bazy danych SQL przy użyciu nazwy **rejestracji SmartHotel**, aby rozwiązać ten problem. Po uruchomieniu narzędzia DMA ponownie, są one możliwość wyboru **SmartHotel rejestracji**i kontynuować pracę w kreatorze.

    ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. W **zaznacz obiekty**, wybierz tabele bazy danych i wygenerować skryptu SQL.

    ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Po utworzeniu usługi DMS skryptu, klikając **Wdróż schemat**.

    ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. Program DMA potwierdza, że wdrożenie zakończyło się pomyślnie.

    ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Po rozpoczęciu migracji.

    ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Po zakończeniu migracji, Contoso można sprawdzić, czy baza danych jest uruchomiony w wystąpieniu programu Azure SQL.

     ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Usunięcie dodatkowych bazy danych SQL **SmartHotel.Registration** w witrynie Azure portal.

     ![PROGRAM DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>Krok 4: Aplikację można przekonwertować do kontenera

Aplikacja w środowisku lokalnym jest tradycyjnych aplikacji trzy warstwy:

- Zawiera on formularzy sieci Web i usługi WCF, łączenie z serwerem SQL.
- Używa platformy Entity Framework do integracji z danymi w bazie danych SQL, uwidaczniania go za pośrednictwem usługi WCF.
- Aplikacja formularzy sieci Web wchodzi w interakcje z usługą WCF.

Contoso przekonwertuje aplikacji kontenera za pomocą Visual Studio i SDK Tools, w następujący sposób:

1. One sklonuj repozytorium lokalnie na komputerze dewelopera:

    **klona usługi git https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Za pomocą programu Visual Studio, one Otwórz plik rozwiązania (SmartHotel.Registration.sln) w **rozwiązania SmartHotel360 — wewnętrzne — rezerwacji apps\src\Registration** katalogu lokalnego repozytorium.  Dwie aplikacje są wyświetlane. Frontony sieci web, nad SmartHotel.Registration.Web aplikacja usługi WCF SmartHotel.Registration.WCF.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. One kliknij prawym przyciskiem myszy aplikację sieci web > **Dodaj** > **obsługi Orkiestratora kontenerów**.
4. W **Dodaj Obsługa Orkiestra kontenerów**, wybierają **usługi Service Fabric**.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso proces powtarza się dla aplikacji SmartHotel.Registration.WCF.
6. Teraz Contoso umożliwia sprawdzenie, jak rozwiązania został zmieniony.

    - Nowa aplikacja jest **SmartHotel.RegistrationApplication/**
    - Zawiera on dwie usługi: **SmartHotel.Registration.WCF** i **SmartHotel.Registration.Web**.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. Visual Studio utworzono plik Docker i obniżona wymagane obrazy lokalnie na komputerze dewelopera.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Plik manifestu (**ServiceManifest.xml**) zostanie utworzony i otwarty przez program Visual Studio. Ten plik nakazuje usługi Service Fabric, jak skonfigurować kontenera, gdy aplikacja jest wdrożona na platformie Azure.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Inny plik manifestu (** ApplicationManifest.xml) zawiera aplikacje configuration dla kontenerów.

    ![Kontener](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>Krok 5. Publikowanie aplikacji


Na koniec Contoso można opublikować aplikację do usługi ACR i klaster usługi Service Fabric.

> [!NOTE]
> Pewne zmiany związane z klastra usługi Service Fabric zostały wprowadzone w aplikacji SmartHotel. Możesz pobrać kod aplikacji oryginalnego i zmodernizowane z [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). Jest zmienionego pliku **AppliationModern/ApplicationParameters/Cloud.xml**.


1. W programie Visual Studio update parametry połączenia, aby połączyć aplikację z bazą danych SQL Azure. Parametry połączenia można znaleźć w bazie danych w witrynie Azure portal.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso publikuje aplikację usługi Service Fabric za pomocą programu Visual Studio. One kliknij prawym przyciskiem myszy aplikację usługi Service Fabric > **Publikuj**.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Wybierają subskrypcji, punkt końcowy połączenia i usługi ACR. Następnie kliknij przycisk **Publikuj**.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. Po zakończeniu wdrażania SmartHotel będzie teraz być uruchomiona Usługa Service Fabric.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Aby połączyć aplikację, Contoso kieruje ruch do publicznego adresu IP modułu równoważenia obciążenia platformy Azure w wierzc tych węzłów usługi Service Fabric.

    ![Publikowanie](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>Krok 6: Rozszerzenie aplikacji i ponownie opublikuj

Po uruchomieniu aplikacji SmartHotel i bazy danych na platformie Azure, firma Contoso chce rozszerzyć aplikację.

- Deweloperzy firmy Contoso mają tworzenia prototypów nową aplikację platformy .NET Core, która będzie działać w klastrze usługi Service Fabric.
- Aplikacja będzie służyć do pobierania danych wskaźniki nastrojów klientów z bazy danych cosmos DB.
- Te dane będą w formie Tweety, które są przetwarzane przy użyciu Bezserwerowej funkcji platformy Azure i Cognitive Services interfejsu API analizy tekstu.

### <a name="provision-azure-cosmos-db"></a>Aprowizowanie usługi Azure Cosmos DB

Pierwszym krokiem Contoso aprowizować bazy danych Azure Cosmos.

1. Tworzą zasób usługi Azure Cosmos DB w witrynie Azure Marketplace.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Zapewniają one nazwę bazy danych (**contososmarthotel**), a następnie wybierz interfejs API SQL i umieść zasobu w produkcyjnej grupie zasobów w regionie wschodnie stany USA 2 podstawowym.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. W **wprowadzenie**, wybierają **Eksplorator danych**i Dodaj nową kolekcję.
4. W **Dodaj kolekcję** zapewniają identyfikatorów i ustaw pojemność magazynu i przepływności.

    ![Rozszerzanie](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. W portalu, otwarciu nowej bazy danych > **kolekcji** > **dokumenty** i kliknij przycisk **nowy dokument**.
6. One Wklej następujący kod JSON do okna dokumentu. To przykładowe dane w postaci jednego tweetu.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
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

Usługa Cosmos DB zainicjowano obsługę administracyjną Contoso można skonfigurować aplikację, aby nawiązać z nim.

1. W programie Visual Studio one Otwórz plik ApplicationModern\ApplicationParameters\cloud.xml w Eksploratorze rozwiązań.

    ![Aplikacja tonacji](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Wypełnij one następujące dwa parametry:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Aplikacja tonacji](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Ponownie opublikować aplikację

Po rozszerzeniu aplikacji, Contoso je opublikuje go na platformę Azure.

1. W portalu, ich prawym przyciskiem myszy aplikację usługi Service Fabric > **Publikuj**.

    ![Ponownie opublikuj](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Wybierają subskrypcji, punkt końcowy połączenia i usługi ACR. Następnie kliknij przycisk **Publikuj**.

    ![Ponownie opublikuj](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. Po zakończeniu wdrażania SmartHotel będzie teraz być uruchomiona Usługa Service Fabric. Konsola zarządzania siecią szkieletową na zawiera teraz trzy usługi.

    ![Ponownie opublikuj](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Kliknięcie Contoso za pośrednictwem usług, aby zobaczyć, że aplikacja SentimentIntegration jest uruchomiona

    ![Ponownie opublikuj](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Wyczyść zasoby po ukończeniu migracji

Po zakończeniu migracji firma Contoso potrzebuje do wykonania tych kroków czyszczenia:  

- Usuń lokalne maszyny wirtualne z magazynu programu vCenter.
- Usuń maszyny wirtualne z lokalnego zadania tworzenia kopii zapasowej.
- Zaktualizuj wewnętrzną dokumentację, aby wyświetlić nowe lokalizacje dla aplikacji SmartHotel. Pokaż bazy danych jako uruchomiona w bazie danych Azure SQL i frontonu jako uruchomiona w usłudze Service Fabric.
- Przejrzyj wszystkie zasoby, które wchodzić w interakcje z wycofany maszynami wirtualnymi i zaktualizuj wszelkie odpowiednie ustawienia lub dokumentacji, aby odzwierciedlić nową konfigurację.


## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

- Firmy Contoso, musisz upewnić się, że ich nowych **rejestracji SmartHotel** bazy danych jest bezpieczna. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- W szczególności powinny one aktualizowane kontenera do używania protokołu SSL przy użyciu certyfikatów.
- Powinni rozważyć użycie magazynu kluczy do ochrony wpisu tajnego dla swoich aplikacji usługi Service Fabric. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Tworzenie kopii zapasowych

- Firma Contoso potrzebuje zapoznać się z wymaganiami kopii zapasowej usługi Azure SQL Database. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Powinni rozważyć Implementowanie grupy trybu failover, aby zapewnić rozwiązania regionalnej pracy awaryjnej dla bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Mogą one używać replikacji geograficznej rejestru Azure container Registry — wersja Premium jednostki SKU. [Dowiedz się więcej](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Firmy Contoso, należy rozważyć wdrożenie aplikacji sieci Web w regionie środkowe stany USA i główne wschodnie stany USA 2, po udostępnieniu Web App for Containers. One może skonfigurować usługi Traffic Manager, aby upewnić się, pracy awaryjnej w przypadku regionalnej awarii.

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu wszystkich zasobów firmy Contoso, należy przypisać tagi platformy Azure na podstawie ich [planowania infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Wszystkie licencjonowania jest wbudowana w koszt usługi PaaS, które zużywa firmy Contoso. Ta ilość zostanie odjęta z umowy EA.
1. Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management. 

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso zaprojektowane od nowa aplikacja SmartHotel na platformie Azure przy użyciu funkcji migracji maszyny Wirtualnej frontonu aplikacji do usługi Service Fabric. Baza danych aplikacji one migracji do usługi Azure SQL database.





