---
title: Ponownie skompiluj aplikację lokalną firmy Contoso na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso ponownie kompiluje aplikację na platformie Azure przy użyciu usługi Azure App Services, usługi Kubernetes, CosmosDB, Azure Functions i usługi Cognitive services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 3d835a7bd93426e57c5ab204d277faca22ae0638
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2018
ms.locfileid: "42061498"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migracja Contoso: ponownie skompilować aplikację w środowisku lokalnym na platformie Azure

W tym artykule pokazano, jak Contoso migruje i odbudowuje ich SmartHotel aplikacji na platformie Azure. Fronton aplikacji maszyny Wirtualnej oni migrować do usługi Azure App Services w sieci Web apps. Zaplecza aplikacji została stworzona przy użyciu mikrousług wdrożone kontenery, zarządzane usługi Azure Kubernetes Service (AKS). Witryny wchodzi w interakcję z usługą Azure Functions dostarczanie funkcji pet zdjęć. 

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
[Artykuł 9: Refaktoryzacja aplikację w usłudze Azure Web Apps i Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Pokazano, jak Contoso SmartHotel app jest migrowana do aplikacji sieci Web platformy Azure i bazy danych aplikacji jest migrowana do wystąpienia serwera SQL Azure | Dostępne
[Artykuł 10: Refaktoryzacja aplikacji systemu Linux do aplikacji sieci Web platformy Azure i usługi Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Pokazuje, jak firmy Contoso jest migrowana aplikacji osTicket systemu Linux do usługi Azure Web Apps w wielu lokacjach, zintegrowana z usługą GitHub ciągłego dostarczania. Bazy danych aplikacji ich migrację do wystąpienia usługi Azure MySQL. | Dostępne
[Artykuł 11: Refaktoryzacja TFS w usłudze VSTS](contoso-migration-tfs-vsts.md) | Pokazuje, jak Contoso migruje lokalnego wdrożenia Team Foundation Server (TFS), migrując je do programu Visual Studio Team Services (VSTS) na platformie Azure. | Dostępne
[Artykuł 12: Przekształcanie aplikacji kontenerów platformy Azure i bazy danych SQL](contoso-migration-rearchitect-container-sql.md) | Pokazuje, jak Contoso migruje i rearchitects SmartHotel aplikacji na platformie Azure. Przekształcanie ich warstwy sieci web aplikacji jako kontenerów Windows bazy danych oraz aplikacji w usłudze Azure SQL Database. | Dostępne
Artykuł 13: Ponownie skompilować aplikację na platformie Azure | Pokazuje, jak Contoso przebudować ich aplikacji SmartHotel, przy użyciu różnych funkcji platformy Azure i usług, w tym usług App Services, Azure Kubernetes, usługi Azure Functions, Cognitive services i usługi Cosmos DB. | W tym artykule.

W tym artykule Contoso migruje Windows dwuwarstwowy. Aplikacja NET SmartHotel działających na maszynach wirtualnych VMware na platformę Azure. Jeśli chcesz korzystać z tej aplikacji, znajduje się jako "open source" i można go pobrać z [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Czynniki biznesowe

Zespół kierowniczy IT ma ściśle współpracowała z ich partnerów biznesowych, aby zrozumieć, czego chcą osiągnąć za pomocą tej migracji:

- **Adres rozwój**: Contoso rośnie. Firma chce zapewnić zróżnicowane środowisko dla swoich klientów w swoich witrynach internetowych.
- **Elastyczność**: Contoso muszą mieć możliwość reagują szybciej niż zmiany w witrynie marketplace, aby umożliwić sukces w globalnej gospodarki. 
- **Skala**: w miarę rozwoju firmy pomyślnie, IT firmy Contoso, musisz podać systemów, które można rozwijać w tym samym tempie.
- **Koszty**: Contoso chce zminimalizować koszty licencjonowania.

## <a name="migration-goals"></a>Cele migracji

Zespół chmury firmy Contoso ma przypięte wymagania aplikacji dotyczące tej migracji. Te wymagania zostały użyte w celu ustalenia najlepszej metody migracji:
 - Aplikacji na platformie Azure będą obowiązywać tak ważne, ponieważ jest już dziś. Powinien działać dobrze i łatwe skalowanie.
 - Aplikacja nie należy używać składników modelu IaaS. Wszystko, co powinny zostać skompilowane do użycia PaaS lub bez użycia serwera usług.
 - Kompilacje aplikacji powinno być ono uruchomione usługi w chmurze i kontenery powinien znajdować się w rejestrze kontenerów dla przedsiębiorstw prywatnych w chmurze.
 - Usługa interfejsu API, używana do zdjęć pet powinien być dokładne i niezawodne w świecie rzeczywistym, ponieważ decyzje podjęte przez aplikację muszą honorowane w ich hotels. Wszelkie pet udzielić, że dostęp jest dozwolony na bieżąco w hotelach.

## <a name="solution-design"></a>Projekt rozwiązania

Po przypięciu dół swoje cele i wymagania, Contoso projektuje i przejrzyj rozwiązanie wdrożenia i identyfikuje proces migracji, w tym usług platformy Azure, które będą używały do migracji.

### <a name="current-app"></a>Bieżącej aplikacji

- SmartHotel aplikacji w środowisku lokalnym są rozmieszczone warstwowo na dwie maszyny wirtualne (WEBVM i SQLVM).
- Maszyny wirtualne znajdują się na hoście VMware ESXi **contosohost1.contoso.com** (w wersji 6.5)
- Środowisko VMware jest zarządzane przez program vCenter Server 6.5 (**vcenter.contoso.com**) uruchomionego na maszynie Wirtualnej.
- Firma Contoso ma lokalne centrum danych (contoso-datacenter), lokalnego kontrolera domeny (**contosodc1**).
- Lokalne maszyny wirtualne w centrum danych firmy Contoso zostanie zamknięty, po zakończeniu migracji.


### <a name="proposed-architecture"></a>Proponowana architektury

- Fronton aplikacji zostanie wdrożony jako aplikacja sieci Web usługi aplikacji Azure w swoich regionu podstawowego.
- Funkcję platformy Azure zawierają przekazywanie zdjęć domowych, a witryna będzie współpracować z tej funkcji.
- Funkcja pet zdjęcie korzysta z interfejsu API przetwarzania Cognitive Services i bazy danych cosmos DB.
- Zaplecza lokacji została stworzona przy użyciu mikrousług. Będą one wdrażane kontenery zarządzane w usłudze Azure Kubernetes service (AKS).
- Kontenery zostaną utworzone przy użyciu usługi VSTS i wypchnięte do usługi Azure Container Registry (ACR).
- Na razie Contoso ręcznego wdrażania kodu aplikacji i funkcji sieci Web przy użyciu programu Visual Studio.
- Mikrousługi zostanie wdrożony przy użyciu skryptu programu PowerShell, który wywołuje narzędzia wiersza polecenia usługi Kubernetes.

    ![Architektura scenariusza](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Przegląd rozwiązania
Contoso ocenia swoich konstrukcjach proponowanych przez zestawiania listę zalet i wad.

**Zagadnienia** | **Szczegóły**
--- | ---
**Specjaliści** | Za pomocą PaaS oraz rozwiązań nieużywających serwera dla wdrożenia end-to-end znacznie zmniejsza czas zarządzania należy podać firmy Contoso.<br/><br/> Przejście do architektury mikrousług umożliwia firmy Contoso w prosty sposób rozszerzyć swoje rozwiązanie wraz z upływem czasu.<br/><br/> Nowe funkcje mogą być wprowadzane online bez zakłócania pracy z dowolnego z istniejącej bazy kodu rozwiązania.<br/><br/> Aplikacja sieci Web zostanie skonfigurowana przy użyciu wielu wystąpień z usługą żadnego pojedynczego punktu awarii.<br/><br/> Skalowanie automatyczne zostaną włączone, dzięki czemu aplikacja może obsługiwać różnych ilościach ruchu.<br/><br/> Wraz z przejściem do usług PaaS Contoso można wycofać nieaktualne rozwiązania, które działa w systemie operacyjnym Windows Server 2008 R2.<br/><br/> Bazy danych cosmos DB ma wbudowaną odporność na uszkodzenia, która nie wymaga konfiguracji przez firmę Contoso. Oznacza to, że warstwa danych nie jest już pojedynczym punktem trybu failover.
**Wady** | Kontenery są bardziej skomplikowane niż inne opcje migracji. Nauki może to stanowić problem "contoso".  Wprowadzają nowe stopień złożoności, który zawiera wiele wartości, pomimo krzywej.<br/><br/> Zespół operacyjny w firmie Contoso należy podnieść do zrozumienia i pomocy technicznej platformy Azure, kontenery i mikrousługi aplikacji.<br/><br/> Contoso nie zostało w pełni zaimplementowane DevOps dla całego rozwiązania. Muszą oni zastanów się, że w przypadku wdrażania usługi AKS, funkcje i usługi aplikacji.



### <a name="migration-process"></a>Proces migracji

1. Contoso aprowizować usługi ACR, AKS i bazy danych cosmos DB.
2. Udostępnia infrastruktury dla wdrożenia, łącznie z aplikacji sieci Web platformy Azure, konta magazynu, funkcji i interfejsu API. 
3. Po zapewnieniu odpowiedniej infrastruktury w miejscu, mogą tworzyć ich mikrousług obrazów kontenerów przy użyciu usługi VSTS, który wypycha je do usługi ACR.
4. Contoso wdroży takich mikrousług pytanie za pomocą skryptu programu PowerShell.
5. Na koniec będzie ich wdrażanie funkcji platformy Azure i aplikacji sieci Web.

    ![Proces migracji](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Usługi platformy Azure

**Usługa** | **Opis** | **Koszty**
--- | --- | ---
[USŁUGI AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Upraszcza zarządzanie Kubernetes, wdrażania i operacji. Udostępnia w pełni zarządzanej usługi organizowania kontenerów Kubernetes.  | AKS to bezpłatna usługa.  Płać tylko maszyny wirtualne, skojarzony magazyn i wykorzystane zasoby sieciowe. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | Przyspiesza tworzenie aplikacji przy użyciu oparte na zdarzeniach bezserwerowemu środowisku obliczeniowemu. Skaluj na żądanie.  | Płacisz tylko za wykorzystane zasoby. Plan jest rozliczana w oparciu o liczby wykonań i użycia zasobów na sekundę. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/functions/).
[Usługa Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Magazyny dowolnych typów obrazów wdrożeń kontenerów. | Koszt na podstawie funkcji, magazynu i czas trwania użycia. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Szybko kompiluj, wdrażaj i skaluj aplikacje internetowe, aplikacje mobilne i aplikacje interfejsów API klasy korporacyjnej działające na dowolnej platformie. | Opłaty za plany usługi App Service są naliczane co sekundę. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Wymagania wstępne

Oto, co Ty (i firmy Contoso) potrzebne do uruchomienia tego scenariusza:

**Wymagania** | **Szczegóły**
--- | ---
**Subskrypcja platformy Azure** | Powinna już utworzono subskrypcję podczas wykonywania oceny w pierwszym artykułu w tej serii. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Jeśli bezpłatne konto właśnie zostało utworzone, jesteś administratorem subskrypcji i możesz wykonywać wszystkie akcje.<br/><br/> Jeśli używasz istniejącej subskrypcji i nie jesteś administratorem, musisz skontaktować się z administratorem w celu uprawnień właściciela lub współautora.
**Infrastruktura platformy Azure** | [Dowiedz się, jak](contoso-migration-infrastructure.md) Contoso Konfigurowanie infrastruktury platformy Azure.
**Wymagania wstępne dla deweloperów** | Firma Contoso potrzebuje następujących narzędzi na stacji roboczej dewelopera:<br/><br/> - [Programu Visual Studio 2017 Community Edition: W wersji 15.5](https://www.visualstudio.com/)<br/><br/> Włączony pakiet roboczy platformy .NET.<br/><br/> [Usługa Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (system Windows 10) lub Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) skonfigurowane do korzystania z kontenerów Windows.



## <a name="scenario-steps"></a>Kroki w scenariuszu

Poniżej przedstawiono, jak Uruchom migrację w Contoso:

> [!div class="checklist"]
> * **Krok 1: Aprowizowanie AKS i ACR**: Contoso Inicjuje obsługę zarządzany klaster AKS i usługi Azure container registry przy użyciu programu PowerShell
> * **Krok 2: Tworzenie kontenerów Docker**: Skonfiguruj ciągłą Integrację dla kontenerów Docker przy użyciu usługi VSTS i odesłać je do usługi ACR.
> * **Krok 3: Wdrażanie mikrousług zaplecza**: wdrożeniem w pozostałej części infrastruktury, które będą wykorzystywane przez mikrousług zaplecza.
> * **Krok 4: Wdrażanie infrastruktury frontonu**: wdrożeniu przez nich infrastruktura frontonu, w tym w magazynie obiektów blob dla telefonów domowych, Cosmos DB i interfejs API przetwarzania.
> * **Krok 5: Migrowanie zaplecza**: Wdrażanie mikrousług i uruchom w usłudze AKS, aby przeprowadzić migrację zaplecza.
> * **Krok 6. publikowanie fronton**: publikują SmartHotel aplikacji do usługi Azure App service i aplikację funkcji, która zostanie wywołana przez usługę domowych.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>Krok 1: Zainicjuj obsługę klastra AKS i rejestru Azure container Registry

Contoso uruchamia skrypt wdrażania w celu tworzenia zarządzanego klastra Kubernetes za pomocą usługi AKS i Azure Container Registry.

- Instrukcje dla tej sekcji użyto **rozwiązania SmartHotel360-Azure-backend** repozytorium.
- **Rozwiązania SmartHotel360-Azure-backend** repozytorium GitHub zawiera całe oprogramowanie w tej części procesu wdrażania.

Aprowizowania wykonaj następujące czynności:

1. Przed rozpoczęciem, Contoso zapewnia wszystkie wstępnie wymagane oprogramowanie jest zainstalowane na komputerze deweloperskim, z których korzysta. 
2. One sklonuj repozytorium lokalnie na komputerze deweloperskim, przy użyciu narzędzia Git.

    **klona usługi git https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso otwiera folder programu Visual Studio Code i przechodzi do **/wdrażanie/k8s** katalogu, który zawiera skrypt **ogólnego aks-env.ps1**.
4. Działają one skrypt, aby utworzyć zarządzanych klastrów Kubernetes za pomocą usługi AKS i rejestru kontenerów.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  Plik otwarty one aktualizowane parametr $location **eastus2**, a następnie zapisz plik.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. Polecenie **widoku** > **zintegrowany Terminal** aby otworzyć zintegrowany terminal w kodzie.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. W terminalu zintegrowanego środowiska PowerShell zalogowaniu się do platformy Azure przy użyciu polecenia Connect-AzureRmAccount. [Dowiedz się więcej](https://docs.microsoft.com/powershell/azure/get-started-azureps) temat rozpoczynania pracy przy użyciu programu PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. Podczas uwierzytelniania wiersza polecenia platformy Azure, uruchamiając **az login** poleceń i postępując zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu przeglądarki sieci web. [Dowiedz się więcej](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) o zalogowaniu się za pomocą wiersza polecenia platformy Azure.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. One uruchom następujące polecenie, przekazując nazwę grupy zasobów ContosoRG, nazwa usługi AKS klastra smarthotel-aks-eus2 i nową nazwę rejestru.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Platforma Azure tworzy innej grupy zasobów, zawierającą odpowiednie zasoby dla klastra usługi AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. Po zakończeniu wdrażania, Contoso zainstalować **kubectl** narzędzie wiersza polecenia. Narzędzie jest już zainstalowana na usługi Azure CloudShell.

    **az aks install-cli**

12. Weryfikują połączenia z klastrem, uruchamiając **kubectl get-węzły** polecenia. Ten węzeł jest taką samą nazwę jak maszyn wirtualnych w grupie automatycznie utworzony zasób.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. One uruchom następujące polecenie, aby uruchomić Pulpit nawigacyjny platformy Kubernetes: 

    **Przeglądaj az aks — ContosoRG grupy zasobów — Nazwa smarthotelakseus2**

14. Na karcie przeglądarki zostanie otwarty na pulpicie nawigacyjnym. Jest to połączenie tunelowania przy użyciu wiersza polecenia platformy Azure. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>Krok 2. Tworzenie kontenera platformy Docker

### <a name="create-a-vsts-and-build"></a>Usługa VSTS i tworzenia

Contoso tworzy projekt usługi VSTS i konfiguruje kompilacji ciągłej integracji do tworzenia kontenera, a następnie wypycha go do usługi ACR. Instrukcje w tej sekcji użyto [rozwiązania SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repozytorium.

1. Z witryną visualstudio.com, tworzą nowe konto (**contosodevops360.visualstudio.com**) i skonfiguruj ją za pomocą narzędzia Git.

2. Tworzą nowy projekt (**smarthotelrefactor**) przy użyciu narzędzia Git dla kontroli wersji i Agile dla przepływu pracy.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. Zaimportowali repozytorium GitHub.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. W **kompilowania i wydawania**, tworzą nową definicję przy użyciu Git programu VSTS jako źródło, z zaimportowanych **smarthotel** repozytorium. 

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

10. Teraz Contoso konfiguruje drugie zadanie platformy Docker (do wypchnięcia). One Wybierz subskrypcję i **smarthotelacreus2** rejestru Azure container Registry. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Ponownie ich wprowadzenie nazwy pliku do pliku docker-compose.yaml i wybierz **wypychanie obrazów usługi** i zawiera najnowsze tagu. Jeśli akcja zmiany **wypychanie obrazów usługi**, nazwa zadania usługi VSTS zmienia się na **automatycznego wypychania usługi**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Zadania usługi VSTS Contoso zapisuje definicji kompilacji i rozpoczyna proces kompilacji.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. Mogą kliknąć zadanie kompilacji, aby sprawdzić postęp.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. Po ukończeniu kompilacji usługi ACR pokazuje nowe repozytoria, które są wypełniane przy użyciu kontenerów, używane przez mikrousług.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>Krok 3: Wdrażanie mikrousług zaplecza

W przypadku klastra AKS utworzonego i kompilowanie obrazów platformy Docker Contoso wdraża pozostałej części infrastruktury, które będą wykorzystywane przez mikrousług zaplecza.

- Zgodnie z instrukcjami w sekcji używają [rozwiązania SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repozytorium.
- W **/deploy/k8s/arm** folderze Brak jednego skryptu, aby utworzyć wszystkie elementy. 

Wdrażanych w następujący sposób:

1. Firma Contoso używa pliku pliku deploy.cmd do wdrażania zasobów platformy Azure w regionie EUS2 i grupy zasobów ContosoRG, wpisując następujące polecenie:

    **. \deploy azuredeploy - c eastus2 ContosoRG**

    ![Wdrażanie zaplecza](./media/contoso-migration-rebuild/backend1.png)

2. Przechwyceniem parametry połączenia dla każdej bazy danych do użycia później.

    ![Wdrażanie zaplecza](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>Krok 4: Wdrażanie infrastruktury frontonu

Firma Contoso potrzebuje do wdrożenia infrastruktury, który będzie używany przez aplikacje frontonu. Tworzą kontenera magazynu obiektów blob do przechowywania obrazów pet; bazy danych Cosmos do przechowywania dokumentów pet informacje; i interfejs API przetwarzania witryny sieci Web. 

Instrukcje dla tej sekcji użyto [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) repozytorium.

### <a name="create-storage-containers"></a>Utwórz kontenery magazynu

1.  W witrynie Azure portal, Contoso zostanie otwarte okno konta magazynu, który został utworzony, a następnie klika przycisk **obiektów blob**.
2.  Tworzą nowy kontener (**zwierzęta**) z poziomem dostępu publicznego do zestawu do kontenera. Użytkownicy przekazać ich pet zdjęcia do tego kontenera.

    ![Obiekt blob magazynu](./media/contoso-migration-rebuild/blob1.png)

3. Tworzą drugi nowy kontener o nazwie **ustawienia**. Plik ze wszystkimi ustawieniami aplikacji frontonu, zostaną umieszczone w tym kontenerze.

    ![Obiekt blob magazynu](./media/contoso-migration-rebuild/blob2.png)

4. Mogą przechwytywać szczegóły dostępu dla konta magazynu w pliku tekstowym do użytku w przyszłości.

    ![Obiekt blob magazynu](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Udostępnianie bazy danych Cosmos

Firmy Contoso Inicjuje obsługę bazy danych Cosmos, służący do pet informacji.

1. Tworzą **usługi Azure Cosmos DB** w witrynie Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Określają nazwę (**contosomarthotel**), wybierz interfejs API SQL i umieść go w produkcyjnej grupie zasobów ContosoRG w główny region wschodnie stany USA 2.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Nowa kolekcja bazy danych, co zwiększa przy użyciu domyślnej pojemności i przepływności.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Należy pamiętać, one informacje o połączeniu dla bazy danych, do użytku w przyszłości. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Aprowizacja przetwarzanie obrazów

Firmy Contoso Inicjuje obsługę interfejsu API przetwarzania obrazów. Interfejs API zostanie wywołana przez funkcję, aby ocenić obrazy przekazane przez użytkowników.

1. Tworzą **komputerowej** wystąpienia w witrynie Azure Marketplace.

     ![Przetwarzanie obrazów](./media/contoso-migration-rebuild/vision1.png)

2. Udostępnia interfejs API (**smarthotelpets**) w zasobie produkcji grupy ContosoRG w główny region wschodnie stany USA 2.

    ![Przetwarzanie obrazów](./media/contoso-migration-rebuild/vision2.png)

3. Ustawienia połączenia dla interfejsu API zapisywane do pliku tekstowego do późniejszego wykorzystania.

     ![Przetwarzanie obrazów](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>Krok 5. wdrożenie usług zaplecza na platformie Azure

Teraz Contoso należy wdrożyć serwer NGINX kontroler danych przychodzących w celu zezwolenia na ruch przychodzący do usług, a następnie wdrażanie mikrousług w klastrze AKS.

Instrukcje w tej sekcji użyto [rozwiązania SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repozytorium.



1. Używają programu Visual Studio Code, aby zaktualizować **/deploy/k8s/config_loal.yml** pliku. Zaktualizowanie różnych połączenia z bazą danych, od informacji, które mogą zauważyć, wcześniej.

     ![Wdrażanie mikrousług](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Niektóre ustawienia konfiguracji (na przykład Active Directory B2C) nie są omówione w tym artykule. Istnieje więcej informacji na temat tych ustawień w repozytorium.

2. Plik deploy.ps1 spowoduje usunięcie całej zawartości klastra (z wyjątkiem ruchu przychodzącego oraz kontroler danych przychodzących), a następnie wdraża mikrousług. Uruchamiają go w następujący sposób:

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. One uruchom następujące polecenie, aby sprawdzić stan usługi:

    ```
    kubectl get services
    ```
4. Mogą otworzyć pulpit nawigacyjny platformy Kubernetes, aby zapoznać się z wdrożenia:

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>Krok 6. publikowanie frontonu

W ostatnim kroku Contoso publikuje aplikację SmartHotel w usłudze Azure App Service i aplikację funkcji, która jest wywoływana przez usługę Pet.

Instrukcje w tej sekcji użyto [SmartHotel-public-web repozytorium.](https://github.com/Microsoft/SmartHotel360-public-web) repozytorium.

### <a name="set-up-web-app-to-use-contoso-resources"></a>Konfigurowanie aplikacji sieci Web, aby korzystać z zasobów firmy Contoso

1. Contoso klonuje repozytorium lokalnie na komputerze deweloperskim, przy użyciu narzędzia Git:

    **klona usługi git https://github.com/Microsoft/SmartHotel360-public-web.git**

2. W programie Visual Studio one Otwórz folder, aby wyświetlić wszystkie pliki w repozytorium.

    ![Publikowanie frontonu](./media/contoso-migration-rebuild/front-publish1.png)

3. Oni wprowadzić zmiany konfiguracji, potrzebne do ustawień domyślnych.

    - Podczas uruchamiania aplikacji sieci web, szuka **SettingsUrl** ustawienia aplikacji.
    - Ta zmienna musi zawierać adres URL do pliku konfiguracji.
    - Domyślnie ustawienie używane jest publiczny punkt końcowy.

4. Zaktualizowanie **/config-sample.json/sample.json** pliku. Jest to plik konfiguracyjny dla sieci web, korzystając z publicznym punktem końcowym.

    - Edytuje zarówno **adresy URL** i **pets_config** sekcje z wartościami dla punktów końcowych interfejsu API usługi AKS, kont magazynu i bazy danych Cosmos. 
    - Adresy URL powinny odpowiadać nazwę DNS nowej aplikacji sieci web, który spowoduje utworzenie firmy Contoso.
    - W przypadku firmy Contoso, jest **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Publikowanie frontonu](./media/contoso-migration-rebuild/front-publish2.png)

5. Po zaktualizowaniu pliku mogą zmienić jego nazwę na **smarthotelsettingsurl**, a następnie przekaż go do obiektu blob magazynu, które są utworzone wcześniej.

     ![Publikowanie frontonu](./media/contoso-migration-rebuild/front-publish3.png)

6. One kliknij plik, aby uzyskać adres URL. Ten adres URL jest używany przez aplikację po uruchomieniu ściągnąć pliku konfiguracji.

    ![Publikowanie frontonu](./media/contoso-migration-rebuild/front-publish4.png)

7. Zaktualizowanie **SettingsUrl** w **appsettings. Production.JSON** plików, a adres URL nowego pliku.

    ![Publikowanie frontonu](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Wdrażanie witryny sieci Web w usłudze Azure App Service

Contoso można teraz publikować swoje witryny sieci Web.


1. Umożliwiają one, monitorowanie usługi Application Insights w programie Visual Studio 2017. Aby to zrobić, wybierz opcję **PublicWeb** projektu w Eksploratorze rozwiązań, a następnie wyszukaj **Dodaj usługę Application Insights**. One rejestrowanie aplikacji w usłudze Application Insights wystąpienia, który został utworzony po ich wdrożeniu infrastruktury.

    ![Publikowanie witryny sieci Web](./media/contoso-migration-rebuild/deploy-website1.png)

2. W programie Visual Studio Łączenie projektu PublicWeb do usługi App Insights, a zaktualizować, aby pokazać, że jest ono skonfigurowane.
 
    ![Publikowanie witryny sieci Web](./media/contoso-migration-rebuild/deploy-website2.png)

3. W programie Visual Studio tworzenie i publikowanie ich aplikacji sieci web.

    ![Publikowanie witryny sieci Web](./media/contoso-migration-rebuild/deploy-website3.png)

5. Określ nazwę aplikacji i umieść go w grupie zasobów w środowisku produkcyjnym **ContosoRG**, w regionie wschodnie stany USA 2, głównym.

    ![Publikowanie witryny sieci Web](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

1. Używają programu Visual Studio można tworzyć i publikować funkcji. Aby to zrobić, ich kliknij prawym przyciskiem myszy **PetCheckerFunction** > **Publikuj**. Następnie utwórz nową funkcję usługi App Service.

     ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function1.png)

2. Określają nazwę **smarthotelpetchecker**i umieścić go w grupie zasobów ContosoRG i nowy plan usługi app service.

     ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function2.png)

3. Wybierz magazyn kont i Utwórz funkcję.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function3.png)

4. Wdrożenie rozpoczyna się od inicjowania obsługi administracyjnej aplikacji funkcji na platformie Azure. W **Publikuj**, Contoso została wzbogacona o ustawienia aplikacji dla magazynu, bazy danych Cosmos i interfejs API przetwarzania obrazów.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function4.png)

5. Do uruchomienia tej funkcji lokalnie najpierw, zaktualizowanie ustawień w **PetCheckerFunction/local.settings.json**.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function5.png)

6. Po wdrożeniu funkcji wydaje się w witrynie Azure portal za pomocą **systemem** stanu.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function6.png)


7. Przeglądania do aplikacji, aby sprawdzić, czy aplikacji Narzędzie do sprawdzania Pet działa zgodnie z oczekiwaniami na [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. Kliknięciu Awatar można przekazać obrazu.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function7.png)

9. Pierwszy zdjęć, które użytkownicy mogą chcieć jest mały dog.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function8.png)

10. Aplikacja zwraca komunikat o akceptacji.

    ![Wdrażanie funkcji](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>Przegląd wdrożenia

Z migrowanych zasobów na platformie Azure firma Contoso potrzebuje do w pełni operacjonalizacji i zabezpieczanie ich nowej infrastruktury.

### <a name="security"></a>Bezpieczeństwo

- Firmy Contoso, musisz upewnić się, że ich nowych baz danych są bezpieczne. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Aplikacja musi zostać zaktualizowany do używania protokołu SSL przy użyciu certyfikatów. Wystąpienie kontenera należy ponownie wdrożyć odpowiedzieć na porcie 443.
- Powinni rozważyć użycie magazynu kluczy do ochrony wpisu tajnego dla swoich aplikacji usługi Service Fabric. [Dowiedz się więcej](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwania po awarii

- Firma Contoso potrzebuje zapoznać się z wymaganiami kopii zapasowej usługi Azure SQL Database. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Powinni rozważyć Implementowanie grupy trybu failover programu SQL, aby zapewnić rozwiązania regionalnej pracy awaryjnej dla bazy danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Mogą one używać replikacji geograficznej rejestru Azure container Registry — wersja Premium jednostki SKU. [Dowiedz się więcej](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Optymalizacja licencjonowania i kosztów

- Po wdrożeniu wszystkich zasobów firmy Contoso, należy przypisać tagi platformy Azure na podstawie ich [planowania infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Wszystkie licencjonowania jest wbudowana w koszt usługi PaaS, które zużywa firmy Contoso. Ta ilość zostanie odjęta z umowy EA.
- Contoso — spowoduje to włączenie usługi Azure Cost Management licencjonowana przez firmę Cloudyn, podmiot zależny firmy Microsoft. To rozwiązanie do zarządzania kosztami wielu chmur, które ułatwia najwydajniejsze korzystanie z oraz zarządzania platformą Azure i innych zasobów w chmurze.  [Dowiedz się więcej](https://docs.microsoft.com/azure/cost-management/overview) o usłudze Azure Cost Management.

## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso ponownie skompiluj aplikację SmartHotel na platformie Azure. One odbudować aplikacji lokalnej maszyny Wirtualnej frontonu do aplikacji sieci Web usługi aplikacji Azure. Stworzyli oni zaplecza aplikacji przy użyciu mikrousług wdrożone kontenery, zarządzane usługi Azure Kubernetes Service (AKS). One rozszerzoną funkcjonalność aplikacji za pomocą aplikacji pet zdjęć.




