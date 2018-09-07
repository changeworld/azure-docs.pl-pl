---
title: Ponownie skompiluj aplikację lokalną firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ponownie kompiluje aplikację na platformie Azure przy użyciu usługi Azure App Services, usługi Kubernetes, CosmosDB, Azure Functions i usługi Cognitive services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: raynew
ms.openlocfilehash: ff195093f97f00fca54e4e1c5800b6b0ecf7605a
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054465"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migracja Contoso: ponownie skompilować aplikację w środowisku lokalnym na platformie Azure

W tym artykule pokazano, jak Contoso migruje i odbudowuje aplikacji rozwiązania SmartHotel360 na platformie Azure. Contoso migruje fronton aplikacji maszyny Wirtualnej do aplikacji sieci Web usługi aplikacji Azure. Zaplecza aplikacji została stworzona przy użyciu mikrousług wdrożone kontenery, zarządzane usługi Azure Kubernetes Service (AKS). Witryny wchodzi w interakcję z usługą Azure Functions umożliwiają korzystanie z funkcji pet zdjęć. 

W tym dokumencie jest jednym z serii artykułów, które pokazują, jak fikcyjnej firmy Contoso jest migrowana zasobów lokalnych do chmury Microsoft Azure. Seria zawiera dodatkowe informacje i scenariusze, które ilustrują konfigurowania infrastruktury migracji, ocena zasobów lokalnych do migracji i z różnymi typami na potrzeby migracji. Scenariusze zwiększanie się stopnia skomplikowania. Dodamy dodatkowe artykuły wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](contoso-migration-infrastructure.md) | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | Dostępne
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | Contoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne [artykuł 5: ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikacji rozwiązania SmartHotel360. Firma Contoso używa Usługa Site Recovery do migrowania aplikacji maszyn wirtualnych. Usługa migracji bazy danych używa migrować bazę danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne [artykuł 7: ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zostanie ukończona lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzacja aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso wykonuje migrację swoich aplikacji rozwiązania SmartHotel360 na aplikację internetową platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych. | Dostępne    
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowana z usługą GitHub ciągłego dostarczania. Contoso migruje bazy danych aplikacji do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzacja Team Foundation Server w programie Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Visual Studio Team Services na platformie Azure. | Dostępne
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso przeprowadza migrację swoich aplikacji rozwiązania SmartHotel360 na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazę danych za pomocą usługi Azure SQL Database. | Dostępne 
Artykuł 13: Ponownie skompilować aplikację na platformie Azure | Contoso odbudowuje swojej aplikacji rozwiązania SmartHotel360, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | W tym artykule


## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ściśle podjęła współpracę z partnerami biznesowymi, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie i chce udostępnić zróżnicowane środowiska dla klientów w witrynach sieci Web firmy Contoso.
- **Elastyczność**: Contoso muszą mieć możliwość reagują szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki. 
- **Skala**: w miarę rozwoju firmy pomyślnie, zespół IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Koszty**: Contoso chce zminimalizować koszty licencjonowania.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte wymagania aplikacji dotyczące tej migracji. Te wymagania zostały użyte w celu ustalenia najlepszej metody migracji:
 - Aplikacji na platformie Azure będą obowiązywać tak ważne, ponieważ jest już dziś. Powinien działać dobrze i łatwe skalowanie.
 - Aplikacja nie należy używać składników modelu IaaS. Wszystko, co powinny zostać skompilowane do użycia PaaS lub bez użycia serwera usług.
 - Kompilacje aplikacji powinno być ono uruchomione usługi w chmurze i kontenery powinien znajdować się w rejestrze kontenerów dla przedsiębiorstw prywatnych w chmurze.
 - Usługa interfejsu API, używana do zdjęć pet powinien być dokładne i niezawodne w świecie rzeczywistym, ponieważ decyzje podjęte przez aplikację muszą honorowane w ich hotels. Wszelkie pet udzielić, że dostęp jest dozwolony na bieżąco w hotelach.
 - Aby spełnić wymagania dla potoku metodyki DevOps, Contoso użyje Visual Studio Team Services (VSTS) dla źródła zarządzania kodu (SCM), korzystając z repozytoriów Git.  Zautomatyzowane kompilacje i wydania będzie służyć do tworzenia kodu, a następnie wdrożyć ją do usługi Azure Web Apps, Azure Functions i usługi AKS.
 - Różne potoków ciągłej integracji/ciągłego wdrażania są wymagane dla mikrousług do wewnętrznej bazy danych i dla witryny sieci web we frontonie.
 - Usługi wewnętrznej bazy danych ma inną wersję cyklu z aplikacji sieci web frontonu.  Aby spełnić to wymaganie, wdrożysz dwie różne potoków metodyki DevOps.
 - Firma Contoso potrzebuje zatwierdzenia zarządzania dla wszystkich wdrożenia witryny sieci Web frontonu i potoku ciągłej integracji/ciągłego wdrażania należy podać to.


## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół celami i wymaganiami, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, które będą używane do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- Aplikację lokalną rozwiązania SmartHotel360 są rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


### <a name="proposed-architecture"></a>Proponowana architektury

- Fronton aplikacji jest wdrażany jako aplikację sieci Web Azure App Services w regionie głównym platformy Azure.
- Funkcję platformy Azure umożliwia przekazywanie zdjęć domowych, a witryna korzysta z tej funkcji.
- Funkcja pet zdjęcie korzysta z interfejsu API przetwarzania Cognitive Services i bazy danych cosmos DB.
- Zaplecza witryna korzysta z mikrousług. Te są wdrażane kontenery zarządzane w usłudze Azure Kubernetes service (AKS).
- Kontenery są zbudowane przy użyciu usługi VSTS i wypchnięte do usługi Azure Container Registry (ACR).
- Na razie Contoso ręcznie wdraża kod aplikacji i funkcji sieci Web przy użyciu programu Visual Studio.
- Mikrousługi są wdrażane przy użyciu skryptu programu PowerShell, który wywołuje narzędzia wiersza polecenia usługi Kubernetes.

    ![Architektura scenariusza](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Przegląd rozwiązania

Contoso ocenia proponowane projektu poprzez umieszczenie razem listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | Za pomocą PaaS oraz rozwiązań nieużywających serwera dla wdrożenia end-to-end znacznie zmniejsza czas zarządzania należy podać firmy Contoso.<br/><br/> Przejście do architektury mikrousług umożliwia firmy Contoso łatwo rozbudowuj rozwiązanie, wraz z upływem czasu.<br/><br/> Nowe funkcje mogą być wprowadzane online bez zakłócania pracy z dowolnego z istniejącej bazy kodu rozwiązania.<br/><br/> Aplikacja sieci Web zostanie skonfigurowana przy użyciu wielu wystąpień z usługą żadnego pojedynczego punktu awarii.<br/><br/> Skalowanie automatyczne zostaną włączone, dzięki czemu aplikacja może obsługiwać różnych ilościach ruchu.<br/><br/> Wraz z przejściem do usług PaaS Contoso można wycofać nieaktualne rozwiązania, które działa w systemie operacyjnym Windows Server 2008 R2.<br/><br/> Bazy danych cosmos DB ma wbudowaną odporność na uszkodzenia, która nie wymaga konfiguracji przez firmę Contoso. Oznacza to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | Kontenery są bardziej skomplikowane niż inne opcje migracji. Nauki może to stanowić problem "contoso".  Wprowadzają nowe stopień złożoności, który zawiera wiele wartości, pomimo krzywej.<br/><br/> Zespół operacyjny w firmie Contoso należy podnieść do zrozumienia i pomocy technicznej platformy Azure, kontenery i mikrousługi aplikacji.<br/><br/> Contoso nie zostało w pełni zaimplementowane DevOps dla całego rozwiązania. Firma Contoso potrzebuje myśleć o tym w przypadku wdrażania usługi AKS, funkcje i usługi aplikacji.



### <a name="migration-process"></a>Proces migracji

1. Contoso przygotowuje i Inicjuje obsługę usług i infrastruktury, które są im potrzebne:

 - Zarządzanych klastrów Kubernetes za pomocą usługi AKS i usługi ACR.
 - Cosmos DB do pet zdjęć.
 - Funkcję platformy Azure w celu zapewnienia przekazuje pet zdjęć.
 - Konto magazynu, do obsługi kontenerów pet zdjęcia i ustawień aplikacji frontonu.
 - Interfejs API przetwarzania obrazów do oceny pet zdjęcia w aplikacji.
 - Aplikację internetową platformy Azure.
 
2. Po zapewnieniu odpowiedniej infrastruktury w miejscu Contoso konfiguruje DevOps kompilowania i wydawania potoku w celu tworzenia, wypychania i ciągłe integrowanie kontenerów do usługi ACR.  
3. Na koniec Contoso wdraża projektów usługi VSTS dla frontonu witryny i konfiguruje potok do wypychania i ciągłe integrowanie kodu na platformę Azure.

    ![Proces migracji](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[USŁUGI AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Upraszcza zarządzanie Kubernetes, wdrażania i operacji. Udostępnia w pełni zarządzanej usługi organizowania kontenerów Kubernetes.  | AKS to bezpłatna usługa.  Płać tylko maszyny wirtualne, skojarzony magazyn i wykorzystane zasoby sieciowe. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | Przyspiesza tworzenie aplikacji przy użyciu oparte na zdarzeniach bezserwerowemu środowisku obliczeniowemu. Skaluj na żądanie.  | Płacisz tylko za wykorzystane zasoby. Plan jest rozliczana w oparciu o liczby wykonań i użycia zasobów na sekundę. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/functions/).
[Usługa Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Magazyny dowolnych typów obrazów wdrożeń kontenerów. | Koszt na podstawie funkcji, magazynu i czas trwania użycia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Szybko kompiluj, wdrażaj i skaluj aplikacje internetowe, aplikacje mobilne i aplikacje interfejsów API klasy korporacyjnej działające na dowolnej platformie. | Opłaty za plany usługi App Service są naliczane co sekundę. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Wymagania wstępne

Oto, co firma Contoso potrzebuje dla tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Contoso utworzone subskrypcje wcześniejszy artykuł. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.
**Wymagania wstępne dla deweloperów** | Firma Contoso potrzebuje następujących narzędzi na stacji roboczej dewelopera:<br/><br/> - [Programu Visual Studio 2017 Community Edition: W wersji 15.5](https://www.visualstudio.com/)<br/><br/> Włączony pakiet roboczy platformy .NET.<br/><br/> [Usługa Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (system Windows 10) lub Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) skonfigurowane do korzystania z kontenerów Windows.



## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1: Inicjowanie obsługi administracyjnej zasobów zaplecza**: Contoso Inicjuje obsługę zarządzanych klastrów Kubernetes za pomocą usługi AKS i Azure container registry.
> * **Krok 2: Skonfiguruj potok do zaplecza**: Contoso konfiguruje w przepływie pracy DevOps. Importowanie repozytorium git do usługi VSTS i utworzyć nowy potok kompilacji. Tworzenie kontenerów platformy Docker i wypycha je do usługi ACR. Po zakończeniu procesu kompilacji w rekordu ACR pojawi się nowych repozytoriów, (wypełniony kontenery). Firma Contoso jest następnie wdrażana infrastruktury wewnętrznej bazy danych, aby przeprowadzić migrację do wewnętrznej bazy danych.
> * **Krok 3: Inicjowanie obsługi administracyjnej zasobów frontonu**: Contoso wdraża frontonu infrastruktury, takich jak usługi blob storage do pet zdjęć, bazy danych Cosmos do przechowywania dokumentów za pomocą pet informacji i interfejs API przetwarzania witryny sieci Web.
> * **Krok 4: Konfigurowanie potoku na potrzeby frontonu**: Contoso publikuje aplikacji rozwiązania SmartHotel360 w usłudze Azure App service, a następnie wdraża PetCheck aplikacji funkcji. Jest to obsługiwane w dwóch oddzielnych potokach do obsługi różnych cykle.



## <a name="step-1-provision-back-end-resources"></a>Krok 1: Inicjowanie obsługi administracyjnej zasobów zaplecza

Administratorzy firmy Contoso, uruchom skrypt wdrażania, aby utworzyć zarządzanych klastrów Kubernetes za pomocą usługi AKS i Azure Container Registry (ACR).

- Instrukcje dla tej sekcji użyto **rozwiązania SmartHotel360-Azure-backend** repozytorium.
- **Rozwiązania SmartHotel360-Azure-backend** repozytorium GitHub zawiera całe oprogramowanie w tej części procesu wdrażania.

### <a name="prerequisites"></a>Wymagania wstępne

1. Przed rozpoczęciem, upewnij się Administratorzy Contoso prerequisitie w nich zainstalowane całe oprogramowanie na komputerze deweloperskim, używane do wdrożenia.
2. One sklonuj repozytorium lokalnego na komputerze deweloperskim, przy użyciu narzędzia Git: **klona usługi git https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>Zainicjuj obsługę usługi AKS i rejestru Azure container Registry

Administratorzy firmy Contoso aprowizowania wykonaj następujące czynności:

1.  Otwórz folder programu Visual Studio Code i przechodzi do **/wdrażanie/k8s** katalogu, który zawiera skrypt **ogólnego aks-env.ps1**.
2. Działają one skrypt, aby utworzyć zarządzanych klastrów Kubernetes za pomocą usługi AKS i usługi ACR.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  Plik otwarty one aktualizowane parametr $location **eastus2**, a następnie zapisz plik.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. Polecenie **widoku** > **zintegrowany Terminal** aby otworzyć zintegrowany terminal w kodzie.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. W terminalu zintegrowanego środowiska PowerShell zalogowaniu się do platformy Azure przy użyciu polecenia Connect-AzureRmAccount. [Dowiedz się więcej](https://docs.microsoft.com/powershell/azure/get-started-azureps) temat rozpoczynania pracy przy użyciu programu PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. Podczas uwierzytelniania wiersza polecenia platformy Azure, uruchamiając **az login** poleceń i postępując zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu przeglądarki sieci web. [Dowiedz się więcej](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) o zalogowaniu się za pomocą wiersza polecenia platformy Azure.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. One uruchom następujące polecenie, przekazując nazwę grupy zasobów ContosoRG, nazwa usługi AKS klastra smarthotel-aks-eus2 i nową nazwę rejestru.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Platforma Azure tworzy innej grupy zasobów, zawierającą odpowiednie zasoby dla klastra usługi AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. Po zakończeniu wdrożenia instalacji **kubectl** narzędzie wiersza polecenia. Narzędzie jest już zainstalowana na usługi Azure CloudShell.

    **az aks install-cli**

10. Weryfikują połączenia z klastrem, uruchamiając **kubectl get-węzły** polecenia. Ten węzeł jest taką samą nazwę jak maszyn wirtualnych w grupie automatycznie utworzony zasób.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. One uruchom następujące polecenie, aby uruchomić Pulpit nawigacyjny platformy Kubernetes: 

    **Przeglądaj az aks — ContosoRG grupy zasobów — Nazwa smarthotelakseus2**

12. Na karcie przeglądarki zostanie otwarty na pulpicie nawigacyjnym. Jest to połączenie tunelowania przy użyciu wiersza polecenia platformy Azure. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>Krok 2: Konfigurowanie procesu zaplecza

### <a name="create-a-vsts-project-and-build-the-pipeline"></a>Tworzenie projektu usługi VSTS i tworzenie potoku

Administratorzy firmy Contoso Tworzenie projektu usługi VSTS i konfigurowanie kompilacji ciągłej integracji do tworzenia kontenera, a następnie wypycha go do usługi ACR. Instrukcje w tej sekcji użyto [rozwiązania SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repozytorium.

1. Z witryną visualstudio.com, tworzą nowe konto (**contosodevops360.visualstudio.com**) i skonfiguruj ją za pomocą narzędzia Git.

2. Tworzą nowy projekt (**SmartHotelBackend**) przy użyciu narzędzia Git dla kontroli wersji i Agile dla przepływu pracy.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. Zaimportowali repozytorium GitHub.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. W **kompilowania i wydawania**, tworzą nowy potok kompilacji przy użyciu Git programu VSTS jako źródło, z zaimportowanych **SmartHotelBackend** repozytorium. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. Należy wybrać, aby uruchomić z pustym potoku.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. Wybierają **(wersja zapoznawcza) systemu Linux hostowanych** dla definicji kompilacji.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. W **fazy 1**, co zwiększa **narzędzia Docker Compose** zadania. To zadanie kompilacji platformy Docker compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. Powtórz i Dodaj kolejną **narzędzia Docker Compose** zadania. Kontenery tego jednego Wypychanie do rejestru Azure container Registry.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. Wybierz pierwsze zadanie (w celu tworzenia) i konfigurowanie kompilacji przy użyciu subskrypcji platformy Azure, autoryzacji i usługi ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. Określ ich ścieżki **docket compose.yaml** pliku w **src** folderu repozytorium. Należy wybrać, aby skompilować obrazy usługi i zawierają najnowsze tagu. Jeśli akcja zmiany **skompilować obrazy usługi**, nazwa zadania usługi VSTS zmienia się na **automatycznie tworzyć usługi**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Teraz skonfigurować ich drugie zadanie platformy Docker (do wypchnięcia). One Wybierz subskrypcję i **smarthotelacreus2** rejestru Azure container Registry. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Ponownie ich wprowadzenie nazwy pliku do pliku docker-compose.yaml i wybierz **wypychanie obrazów usługi** i zawiera najnowsze tagu. Jeśli akcja zmiany **wypychanie obrazów usługi**, nazwa zadania usługi VSTS zmienia się na **automatycznego wypychania usługi**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Zadania usługi VSTS Administratorzy Contoso zapisać definicję kompilacji i rozpocząć proces kompilacji.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. Mogą kliknąć zadanie kompilacji, aby sprawdzić postęp.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. Po ukończeniu kompilacji usługi ACR pokazuje nowe repozytoria, które są wypełniane przy użyciu kontenerów, używane przez mikrousług.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>Wdrażanie infrastruktury zaplecza

Za pomocą klastra AKS utworzonego i obrazów platformy Docker Administratorzy Contoso teraz wdrożyć pozostałej części infrastruktury, które będą wykorzystywane przez mikrousług zaplecza.

- Zgodnie z instrukcjami w sekcji używają [rozwiązania SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repozytorium.
- W **/deploy/k8s/arm** folderze Brak jednego skryptu, aby utworzyć wszystkie elementy. 

Wdrażanych w następujący sposób:

1. Otwórz wiersz polecenia dla deweloperów i używać danych polecenia az logowania dla subskrypcji platformy Azure.
2. Korzystanie z pliku pliku deploy.cmd do wdrażania zasobów platformy Azure w regionie EUS2 i grupy zasobów ContosoRG, wpisując następujące polecenie:

    **.\deploy.cmd azuredeploy - c eastus2 ContosoRG**

    ![Wdrażanie zaplecza](./media/contoso-migration-rebuild/backend1.png)

2. W witrynie Azure portal przechwyceniem parametry połączenia dla każdej bazy danych do użycia później.

    ![Wdrażanie zaplecza](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>Twórz potoki wydania zaplecza

Teraz Administratorzy firmy Contoso, wykonaj następujące czynności:

- Wdrażanie kontrolera danych przychodzących NGINX, aby zezwolić na ruch przychodzący do usług.
- Wdrażanie mikrousług w klastrze AKS.
- Pierwszym krokiem ich potrzeby zaktualizuj ciągi połączeń do mikrousług przy użyciu usługi VSTS. Następnie skonfiguruj ich nowy potok wersji usługi VSTS do wdrażania mikrousług.
- Instrukcje w tej sekcji użyto [rozwiązania SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repozytorium.
- Należy pamiętać, że niektóre ustawienia konfiguracji (na przykład Active Directory B2C) nie są omówione w tym artykule. Przeczytaj więcej informacji na temat tych ustawień w repozytorium.

Tworzą potok:

1. Za pomocą programu Visual Studio, one aktualizowane **/deploy/k8s/config_local.yml** pliku z informacjami o połączeniu bazy danych, które zostały one zanotowanej wcześniej.

    ![Połączenia bazy danych](./media/contoso-migration-rebuild/back-pipe1.png)

2. Otwórz usługę VSTS, a w SmartHotel360 projektu w **wersji**, klikając **+ nowy potok**.

    ![Nowy potok](./media/contoso-migration-rebuild/back-pipe2.png)

3. Polecenie **puste zadanie** można uruchomić potok, który nie ma szablonu.

    ![Pusty zadania](./media/contoso-migration-rebuild/back-pipe3.png)

4. Zapewniają one nazwy środowiska i potoku.

      ![Nazwa środowiska](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Nazwa potoku](./media/contoso-migration-rebuild/back-pipe5.png)

5. One dodawanie artefaktu.

     ![Dodawanie artefaktu](./media/contoso-migration-rebuild/back-pipe6.png)

6. Wybierają **Git** jako źródło wpisz i określ projektu, źródła i gałąź główna dla aplikacji rozwiązania SmartHotel360.

    ![Ustawienia artefaktu](./media/contoso-migration-rebuild/back-pipe7.png)

7. Kliknięciu łącza do zadania.

    ![Łącza do zadania](./media/contoso-migration-rebuild/back-pipe8.png)

8. Dodają nowe zadanie programu Azure PowerShell, aby skrypt programu PowerShell mogą działać w środowisku platformy Azure.

    ![Program PowerShell w systemie Azure](./media/contoso-migration-rebuild/back-pipe9.png)

9. Wybierz subskrypcję platformy Azure dla zadania i wybierz **deploy.ps1** skrypt z repozytorium Git.

    ![Uruchom skrypt](./media/contoso-migration-rebuild/back-pipe10.png)


10. Dodają argumenty do skryptu. skrypt spowoduje usunięcie całej zawartości klastra (z wyjątkiem **ruch przychodzący** i **kontrolera danych przychodzących**) i wdrażanie mikrousług.

    ![Argumenty skryptu](./media/contoso-migration-rebuild/back-pipe11.png)

11. Ustaw preferowaną wersję programu Azure PowerShell do najnowszej wersji i Zapisz potoku.
12. Ich przenieść z powrotem do **wersji** strony, a następnie ręcznie utworzyć nową wersję.

    ![Nowa wersja](./media/contoso-migration-rebuild/back-pipe12.png)

13. Kliknij wersję, po jej utworzeniu, a w **akcje**, klikając **Wdróż**.

      ![Wdrażanie wersji](./media/contoso-migration-rebuild/back-pipe13.png)  

14. Po zakończeniu wdrożenia działają następujące polecenie, aby sprawdzić stan usług, za pomocą usługi Azure Cloud Shell: **kubectl get-usług**.


## <a name="step-3-provision-front-end-services"></a>Krok 3: Aprowizowanie usługi frontonu

Administratorzy firmy Contoso muszą wdrażać infrastrukturę, która będzie używana przez aplikacje frontonu. Tworzą kontenera magazynu obiektów blob do przechowywania obrazów pet; bazy danych Cosmos do przechowywania dokumentów pet informacje; i interfejs API przetwarzania witryny sieci Web. 

Instrukcje dla tej sekcji użyto [rozwiązania SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) repozytorium.

### <a name="create-blob-storage-containers"></a>Tworzenie kontenerów w usłudze Blob Storage

1.  W witrynie Azure portal, otwórz ich konta magazynu, który został utworzony, a następnie klika przycisk **obiektów blob**.
2.  Tworzą nowy kontener (**zwierzęta**) z poziomem dostępu publicznego do zestawu do kontenera. Użytkownicy przekazać ich pet zdjęcia do tego kontenera.

    ![Obiekt blob magazynu](./media/contoso-migration-rebuild/blob1.png)

3. Tworzą drugi nowy kontener o nazwie **ustawienia**. Plik ze wszystkimi ustawieniami aplikacji frontonu, zostaną umieszczone w tym kontenerze.

    ![Obiekt blob magazynu](./media/contoso-migration-rebuild/blob2.png)

4. Mogą przechwytywać szczegóły dostępu dla konta magazynu w pliku tekstowym do użytku w przyszłości.

    ![Obiekt blob magazynu](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Udostępnianie bazy danych Cosmos

Administratorzy firmy Contoso aprowizację bazy danych Cosmos służący do pet informacji.

1. Tworzą **usługi Azure Cosmos DB** w witrynie Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Określają nazwę (**contosomarthotel**), wybierz interfejs API SQL i umieść go w produkcyjnej grupie zasobów ContosoRG w główny region wschodnie stany USA 2.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Nowa kolekcja bazy danych, co zwiększa przy użyciu domyślnej pojemności i przepływności.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Należy pamiętać, one informacje o połączeniu dla bazy danych, do użytku w przyszłości. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Aprowizacja przetwarzanie obrazów

Administratorzy firmy Contoso dostarczać interfejs API przetwarzania obrazów. Interfejs API zostanie wywołana przez funkcję, aby ocenić obrazy przekazane przez użytkowników.

1. Tworzą **komputerowej** wystąpienia w witrynie Azure Marketplace.

     ![Przetwarzanie obrazów](./media/contoso-migration-rebuild/vision1.png)

2. Udostępnia interfejs API (**smarthotelpets**) w zasobie produkcji grupy ContosoRG w główny region wschodnie stany USA 2.

    ![Przetwarzanie obrazów](./media/contoso-migration-rebuild/vision2.png)

3. Ustawienia połączenia dla interfejsu API zapisywane do pliku tekstowego do późniejszego wykorzystania.

     ![Przetwarzanie obrazów](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Inicjowanie obsługi administracyjnej aplikacji sieci Web platformy Azure

Administratorzy firmy Contoso aprowizować aplikację internetową przy użyciu witryny Azure portal.


1. Wybierają **aplikacji sieci Web** w portalu.

    ![Aplikacja internetowa](media/contoso-migration-rebuild/web-app1.png)

2. Zapewniają one nazwę aplikacji (**smarthotelcontoso**), systemem Windows i umieść go w produkcyjnej grupie zasobów **ContosoRG**. Tworzą nowe wystąpienie usługi Application Insights do monitorowania aplikacji...

    ![Nazwa aplikacji internetowej](media/contoso-migration-rebuild/web-app2.png)

3. Po tych czynności przejdź do adresu aplikacji w celu sprawdzenia, czy została utworzona pomyślnie.

4. Teraz w witrynie Azure portal tworzą miejsca przejściowego dla kodu. potok zostanie wdrożony w tym miejscu. Daje to gwarancję, że kod nie jest wdrażana do momentu Administratorzy wykonywać wydania.

    ![Miejsce na tymczasową aplikację sieci Web](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Inicjowanie obsługi administracyjnej aplikacji funkcji platformy Azure

W witrynie Azure portal Administratorzy Contoso Inicjowanie obsługi administracyjnej aplikacji funkcji.

1. Wybierają **aplikacji funkcji**.

    ![Tworzenie aplikacji funkcji](./media/contoso-migration-rebuild/function-app1.png)

2. Zapewniają one nazwę aplikacji (**smarthotelpetchecker**). Ich umieszczenia aplikacji w grupie zasobów w środowisku produkcyjnym **ContosoRG**. Ustawiają miejsce hostowania **Plan zużycie**i umieść ją w regionie wschodnie stany USA 2. Nowe konto magazynu jest tworzone wraz z wystąpienia usługi Application Insights do monitorowania.

    ![Ustawienia aplikacji funkcji](./media/contoso-migration-rebuild/function-app2.png)


3. Po wdrożeniu aplikacji, przejdź do adresu aplikacji w celu sprawdzenia, czy została utworzona pomyślnie.


## <a name="step-4-set-up-the-front-end-pipeline"></a>Krok 4: Konfigurowanie frontonu potoku

Administratorzy firmy Contoso tworzyć dwóch różnych projektów frontonu witryny. 

1. W usłudze VSTS, mogą utworzyć projekt **SmartHotelFrontend**.

    ![Frontonu projektu](./media/contoso-migration-rebuild/function-app1.png)

2. Zaimportowali [rozwiązania SmartHotel360 frontonu](https://github.com/Microsoft/SmartHotel360-public-web.git) repozytorium Git do nowego projektu.
3. Dla aplikacji funkcji, Utwórz innego projektu usługi VSTS (SmartHotelPetChecker) i zaimportuj [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) repozytorium Git do tego projektu.

### <a name="configure-the-web-app"></a>Konfigurowanie aplikacji sieci Web

Teraz Administratorzy Contoso skonfigurować aplikacji sieci Web, aby korzystać z zasobów firmy Contoso.

1. Połącz się z projektem usługi VSTS i sklonuj repozytorium lokalnie na komputerze deweloperskim.
2. W programie Visual Studio one Otwórz folder, aby wyświetlić wszystkie pliki w repozytorium.

    ![Pliki w repozytorium](./media/contoso-migration-rebuild/configure-webapp1.png)

3. Zaktualizowanie zmiany konfiguracji zgodnie z potrzebami.

    - Podczas uruchamiania aplikacji sieci web, szuka **SettingsUrl** ustawienia aplikacji.
    - Ta zmienna musi zawierać adres URL do pliku konfiguracji.
    - Domyślnie ustawienie używane jest publiczny punkt końcowy.

4.  Zaktualizowanie pliku /config-sample.json/sample.json.

    - Jest to plik konfiguracyjny dla sieci web, korzystając z publicznym punktem końcowym.
    - Edytuje **adresy URL** i **pets_config** sekcje z wartościami dla punktów końcowych interfejsu API usługi AKS, kont magazynu i bazy danych Cosmos.
    - Adresy URL powinny odpowiadać nazwę DNS nowej aplikacji sieci web, który spowoduje utworzenie firmy Contoso.
    - W przypadku firmy Contoso, jest **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Ustawienia JSON](./media/contoso-migration-rebuild/configure-webapp2.png)

5. Po zaktualizowaniu pliku mogą zmienić jego nazwę **smarthotelsettingsurl**, a następnie przekaż go do blogu magazynu one utworzone wcześniej.

    ![Zmiana nazwy i przekazywania](./media/contoso-migration-rebuild/configure-webapp3.png)

6. One kliknij plik, aby uzyskać adres URL. Adres URL jest używany przez aplikację podczas jej ściąga pliki konfiguracji.

    ![Adres URL aplikacji](./media/contoso-migration-rebuild/configure-webapp4.png)

7. W **appsettings. Production.JSON** zaktualizowanie pliku **SettingsURL** adres URL nowego pliku.

    ![Adres URL aktualizacji](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Wdrażanie witryny sieci Web w usłudze Azure App Service

Contoso Administratorzy mogą teraz publikować w witrynie sieci Web.


1. Otworzą usługi VSTS, a następnie w **SmartHotelFrontend** projektu w **kompilacje i wydania**, klikając **+ nowy potok**.
2. Wybierają **Git programu VSTS** jako źródło.

    ![Nowy potok](./media/contoso-migration-rebuild/vsts-publishfront1.png)

3. Wybierają **platformy ASP.NET Core** szablonu.
4. Przejrzyj potoku i sprawdź, czy **publikowanie projektów sieci Web** i **Zip projektów opublikowane** są zaznaczone.

    ![Ustawienia potoku](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. W **wyzwalaczy**, Włącz ciągłą integrację i Dodaj gałęzi głównej. Daje to gwarancję, że każdy tim rozwiązanie ma nowego kodu do gałęzi głównej, potok kompilacji rozpoczyna się.

    ![Ciągła integracja](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. Polecenie **Zapisz k & olejką** do uruchomienia kompilacji.
7. Po zakończeniu kompilacji, ich konfigurowania potoku wersji przy użyciu **wdrożenie usługi aplikacji Azure**.
8. Zapewniają one Nazwa środowiska **przemieszczania**.

    ![Nazwa środowiska](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. Dodawanie artefaktu i wybierz kompilację, które są skonfigurowane.

     ![Dodawanie artefaktu](./media/contoso-migration-rebuild/vsts-publishfront5.png)

6. Kliknij ikonę pioruna na artifcat i włączyć wdrażanie ciągłe.

    ![Ciągłe wdrażanie](./media/contoso-migration-rebuild/vsts-publishfront6.png)

7. W **środowiska**, klikając **fazy 1, 1 zadanie** w obszarze **przemieszczania**.
8. Po wybraniu subskrypcji i nazwę aplikacji, otwarciu **wdrożenia usługi Azure App Service** zadania. Wdrożenie jest skonfigurowane do użycia **przemieszczania** miejsce wdrożenia. Automatycznie powoduje to skompilowanie kodu do przeglądu i zatwierdzania, w tym gnieździe.

     ![Gniazdo](./media/contoso-migration-rebuild/vsts-publishfront7.png)

9. W **potoku wydania nowe**, dodawane są nowe środowisko.

    ![Nowe środowisko](./media/contoso-migration-rebuild/vsts-publishfront8.png)

10. Wybierają **wdrożenia usługi Azure App Service z gniazdem**i nadaj środowisku **Prod**.

    ![Nazwa środowiska](./media/contoso-migration-rebuild/vsts-publishfront9.png)

11. Kliknięciu **fazy 1, 2 zadania**i wybierz subskrypcję, nazwa usługi app service, a **przemieszczania** miejsca.

    ![Nazwa środowiska](./media/contoso-migration-rebuild/vsts-publishfront10.png)

12. Usuwają **wdrożenia usługi Azure App Service do gniazda** z potoku. Niezaktualizowanym dostępne w poprzednich krokach.

    ![Usuń z potoku](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. Zapisywane potoku. W potoku, kliknij polecenie na **po wdrożeniu warunków**.

    ![Po wdrożeniu](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. Umożliwiają one **zatwierdzenia po wdrożeniu**, i Dodaj potencjalnego klienta dev jako osoba zatwierdzająca.

    ![Po wdrożeniu zatwierdzenia](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. W potoku kompilacji ich ręcznie uruchamiał kompilację. Spowoduje to wyzwolenie nowego potoku tworzenia wersji wdraża lokacji do miejsca przejściowego. "Contoso", adres URL dla gniazda jest **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. Po kompilacja zakończy się, a wydanie wdraża do miejsca, usługa VSTS wiadomości e-mail dev potencjalnego klienta do zatwierdzenia.
17. Kliknięć potencjalny klient dev **wyświetlić zatwierdzenie**i może zatwierdzić lub odrzucić żądanie w portalu usługi VSTS.

    ![Wiadomość e-mail dotyczącą zatwierdzenia](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. Potencjalny klient sprawia, że komentarz i zatwierdza. Spowoduje to uruchomienie wymiany z **przemieszczania** i **prod** miejsca i przenosi kompilacji w środowisku produkcyjnym.

    ![Zatwierdzanie i Zamień](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. Wymiany ukończeniu potoku.

    ![Ukończ zamianę](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. Sprawdzanie zespołu **prod** miejsce, aby sprawdzić, czy aplikacja sieci web jest w środowisku produkcyjnym w **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>Wdrażanie aplikacji PetChecker — funkcja

Administratorzy firmy Contoso wdrożyć aplikację w następujący sposób.

1. One sklonować repozytorium lokalnie na komputerze deweloperskim przez nawiązanie połączenia w projekcie usługi VSTS.
2. W programie Visual Studio one Otwórz folder, aby wyświetlić wszystkie pliki w repozytorium.
3. Adresat otworzy **src/PetCheckerFunction/local.settings.json** pliku i dodaj ustawienia aplikacji dla magazynu, bazy danych Cosmos i interfejs API przetwarzania obrazów.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function5.png)

4. Zatwierdź kod i zsynchronizować ją z powrotem do usługi VSTS, wypychanie ich zmiany.
5. Dodaj nowy potok kompilacji i wybierz **Git programu VSTS** źródła.
6. Wybierają **platformy ASP.NET Core (.NET Framework)** szablonu.
7. One zaakceptuj wartości domyślne dla szablonu.
8. W **wyzwalaczy**, a następnie wybierz, aby **Włącz ciągłą integrację**i kliknij przycisk **Zapisz k & Olejką** do uruchomienia kompilacji.
9. Po pomyślnym kompilacji tworzą potok tworzenia wersji do dodawania **wdrożenia usługi Azure App Service z gniazdem**.
10. Ich nazwy środowiska **Prod**i wybierz subskrypcję. Ustawiają **typ aplikacji** do **Ap funkcji**, a nazwa usługi app service jako **smarthotelpetchecker**.

    ![Aplikacja funkcji](./media/contoso-migration-rebuild/petchecker2.png)

11. One dodawanie artefaktu **kompilacji**.

    ![Artefakt](./media/contoso-migration-rebuild/petchecker3.png)

12. Umożliwiają one **wyzwalacz ciągłego wdrażania**i kliknij przycisk **Zapisz**.
13. Polecenie **nową kompilację w kolejce** uruchomić pełny potok ciągłej integracji/ciągłego wdrażania.
14. Po wdrożeniu funkcji wydaje się w witrynie Azure portal za pomocą **systemem** stanu.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function6.png)


7. Przeglądania do aplikacji, aby sprawdzić, czy aplikacji Narzędzie do sprawdzania Pet działa zgodnie z oczekiwaniami na [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. Kliknięciu Awatar można przekazać obrazu.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function7.png)

9. Pierwszy zdjęć, które użytkownicy mogą chcieć jest mały dog.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function8.png)

10. Aplikacja zwraca komunikat o akceptacji.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje teraz do w pełni operacjonalizacji i zabezpieczania nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

- Firma Contoso potrzebuje upewnić się, że nowe bazy danych są bezpieczne. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Aplikacja musi zostać zaktualizowany do używania protokołu SSL przy użyciu certyfikatów. Wystąpienie kontenera należy ponownie wdrożyć odpowiedzieć na porcie 443.
- Contoso rozważyć użycie magazynu kluczy do ochrony wpisu tajnego dla swoich aplikacji usługi Service Fabric. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwania po awarii

- Firma Contoso potrzebuje zapoznać się z wymaganiami kopii zapasowej usługi Azure SQL Database. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Firmy Contoso powinien rozważyć grupy trybu failover programu SQL, aby zapewnić rozwiązania regionalnej pracy awaryjnej dla bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso mogą korzystać z replikacji geograficznej rejestru Azure container Registry — wersja Premium jednostki SKU. [Dowiedz się więcej](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Usługa cosmos DB tworzy kopie zapasowe automatycznie. Firma Contoso może [więcej](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) o tym procesie.

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu wszystkich zasobów firmy Contoso, należy przypisać tagi platformy Azure na podstawie ich [planowania infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Wszystkie licencjonowania jest wbudowana w koszt usługi PaaS, które zużywa firmy Contoso. Ta ilość zostanie odjęta z umowy EA.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso odbudowuje aplikacji rozwiązania SmartHotel360 na platformie Azure. Aplikację w środowisku lokalnym, do której maszyna wirtualna frontonu zostanie ponownie skompilowany w usłudze Azure App Services Web Apps. Zaplecza aplikacji została stworzona przy użyciu mikrousług wdrożone kontenery, zarządzane usługi Azure Kubernetes Service (AKS). Contoso rozszerzoną funkcjonalność aplikacji za pomocą aplikacji pet zdjęć.




