---
title: Contoso — Konfigurowanie infrastruktury migracji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso konfiguruje infrastruktury platformy Azure pod kątem migracji do platformy Azure.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/1/2018
ms.author: raynew
ms.openlocfilehash: 5dfe768ddb3509f896b90f913ffecdf33907357a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682799"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso — wdrażanie infrastruktury migracji

W tym artykule Contoso przygotowuje swoją infrastrukturę lokalnych do migracji i konfiguruje infrastruktury platformy Azure, w ramach przygotowania do migracji i prowadzenie działalności w środowisku hybrydowym.

- Jest przykładowej architektury, które są specyficzne dla firmy Contoso.
- Czy potrzebujesz, aby wszystkie elementy, które są opisane w tym artykule są zależne od strategię migracji. Na przykład jeśli tworzysz tylko natywnych aplikacji w chmurze na platformie Azure, możesz potrzebować mniej złożone struktury sieci.

Ten artykuł jest częścią serii artykułów, które dokumentują sposób fikcyjnej firmy Contoso migruje swoje zasoby lokalne do chmury Microsoft Azure. Seria zawiera ogólne informacje i szereg scenariuszy wdrażania, które ilustrują sposób konfigurowania infrastruktury migracji, ocenić przydatność, korzystając z zasobów lokalnych do migracji, a następnie uruchom różnego rodzaju migracji. Scenariusze zwiększanie się stopnia skomplikowania. Artykuły zostaną dodane do serii wraz z upływem czasu.


**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: Przegląd](contoso-migration-overview.md) | Omówienie serię artykułów, strategia migracji do firmy Contoso i przykładowe aplikacje, które są używane w tej serii. | Dostępne
Artykuł 2: Wdrażanie infrastruktury platformy Azure | Contoso przygotowuje swoją infrastrukturę lokalną i jej infrastruktury platformy Azure do migracji. Tej samej infrastruktury jest używany dla wszystkich artykułów migracji w serii. | W tym artykule
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](contoso-migration-assessment.md)  | SContoso uruchamia oceny aplikacji rozwiązania SmartHotel360 jej w środowisku lokalnym, działające w programie VMware. Contoso ocenia aplikacji maszyn wirtualnych przy użyciu usługi Azure Migrate bazy danych oraz aplikacji programu SQL Server przy użyciu Data Migration Assistant. | Dostępne
[Artykuł 4: Ponowne hostowanie aplikacji na maszynie Wirtualnej platformy Azure oraz wystąpienie zarządzane usługi SQL Database](contoso-migration-rehost-vm-sql-managed-instance.md) | Firmy Contoso jest uruchamiana lift-and-shift migracja na platformę Azure dla swoich aplikacji rozwiązania SmartHotel360 w środowisku lokalnym. Firma Contoso jest migrowana aplikację frontonu maszyn wirtualnych za pomocą [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso bazy danych aplikacji jest migrowana do wystąpienia zarządzanego Azure SQL Database przy użyciu [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Dostępne   
[Artykuł 5: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Contoso migruje swojej aplikacji rozwiązania SmartHotel360 maszyn wirtualnych do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności AlwaysOn programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso przeprowadza migrację aplikacji przy użyciu Site Recovery do migracji maszyn wirtualnych aplikacji i Database Migration Service migrację bazy danych aplikacji do klastra programu SQL Server, który jest chroniony przez grupy dostępności AlwaysOn. | Dostępne
[Artykuł 7: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure](contoso-migration-rehost-linux-vm.md) | Contoso zostanie ukończona lift-and-shift migrację swoich aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure przy użyciu usługi Site Recovery. | Dostępne
[Artykuł 8: Ponowne hostowanie aplikacji systemu Linux na maszynach wirtualnych platformy Azure i usługa Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso swoją aplikację osTicket systemu Linux jest migrowana do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Jej zmigrowaniu bazy danych aplikacji do usługi Azure Database for MySQL za pomocą aplikacji MySQL Workbench. | Dostępne
[Artykuł 9: Refaktoryzuj aplikacji w aplikacji internetowej platformy Azure i usługi Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso wykonuje migrację swoich aplikacji rozwiązania SmartHotel360 na aplikację internetową platformy Azure i wykonuje migrację bazy danych aplikacji na wystąpienie serwera SQL Azure przy użyciu Asystenta migracji bazy danych. | Dostępne    
[Artykuł 10: Refaktoryzuj aplikacji systemu Linux w aplikacji internetowej platformy Azure i usługi Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso przeprowadza migrację swoich aplikacji osTicket systemu Linux do aplikacji sieci web platformy Azure w wielu lokacjach. Aplikacja sieci web jest zintegrowana z usługą GitHub ciągłego dostarczania. Baza danych aplikacji jest migrowana do usługi Azure Database for MySQL — wystąpienia. | Dostępne
[Artykuł 11: Refaktoryzuj Team Foundation Server w usługach Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migruje jego lokalnego wdrożenia Team Foundation Server do usługi Azure DevOps Services na platformie Azure. | Dostępne
[Artykuł 12: Ponowne Ustalanie architektury aplikacji kontenerów platformy Azure i usługi Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jego SmartHotel aplikacji na platformie Azure. Następnie rearchitects warstwy sieci web aplikacji jako kontener Windows w usłudze Azure Service Fabric i bazy danych aplikacji przy użyciu usługi Azure SQL Database. | Dostępne    
[Artykuł 13: Ponownie skompiluj aplikację na platformie Azure](contoso-migration-rebuild.md) | Contoso odbudowuje swojej aplikacji SmartHotel, korzystając z możliwości platformy Azure i usług, w tym usługi Azure App Service, Azure Kubernetes Service (AKS), usługi Azure Functions, Azure Cognitive Services i Azure Cosmos DB. | Dostępne
[Artykuł 14: Skalowanie migracji na platformę Azure](contoso-migration-scale.md) | Po wypróbowanie kombinacje migracji, Contoso przygotowuje aby możliwe było skalowanie pełną migrację na platformę Azure. | Dostępne 

W tym artykule, który Contoso skonfiguruje wszystkie elementy infrastruktury potrzebny do ukończenia wszystkich scenariuszy migracji. 


## <a name="overview"></a>Omówienie

Contoso było migrować na platformę Azure, ważne jest przygotowanie infrastruktury platformy Azure.  Ogólnie rzecz biorąc istnieje pięć szerokiego obszary, które firma Contoso potrzebuje wziąć pod uwagę:

**Krok 1. Subskrypcje platformy Azure**: Jak będzie Contoso zakupu platformy Azure i korzystać z platformy Azure i usług?  
**Krok 2. Tożsamość hybrydowa**: Jak będzie go zarządzanie i kontrolowanie dostępu do lokalnych i zasobów platformy Azure po migracji? Jak Contoso rozszerzyć lub Przenieś Zarządzanie tożsamościami do chmury?  
**Krok 3. Odzyskiwanie po awarii i odporności**: Jak będzie Contoso upewnij się, że jego aplikacji i infrastruktury odporne na błędy ewentualnych wyłączeń i awarii?  
**Krok 4. Sieć**: Jak Contoso projektowania infrastruktury sieci oraz poprawnie ustanowić połączenia między jej w lokalnym centrum danych a platformą Azure?  
**Krok 5. Zabezpieczenia**: Jak będzie jego bezpiecznego wdrażania hybrydowego/platformy Azure?  
**Krok 6: Zarządzanie**: Jak Contoso zostanie zachowana wdrożenia dostosowane do wymagań dotyczących zabezpieczeń i zarządzanie

## <a name="before-you-start"></a>Przed rozpoczęciem

Zanim zaczniemy spojrzenie na infrastrukturę, warto przeczytać pewne informacje dotyczące możliwości platformy Azure, którą omówimy w tym artykule:

- Liczba opcji są dostępne dla zakupu platformy Azure, łącznie z rzeczywistym użyciem, umowy Enterprise Agreement (EA), licencjonowania Open z odprzedawcy produktów firmy Microsoft lub od Partners firmy Microsoft, znane jako dostawców rozwiązań w chmurze (CSP). Dowiedz się więcej o [opcje zakupu](https://azure.microsoft.com/pricing/purchase-options/)i przeczytaj o tym, jak [subskrypcji platformy Azure są zorganizowane](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Zapoznaj się z omówieniem usługi Azure [Zarządzanie tożsamościami i dostępem](https://www.microsoft.com/trustcenter/security/identity). W szczególności, Dowiedz się więcej o [usługi Azure AD i rozszerzanie lokalnej usługi AD w chmurze](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Jest przydatne do pobrania książki elektronicznej dotyczących [Zarządzanie tożsamościami i dostępem (IAM) w środowisku hybrydowym](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- System Azure oferuje niezawodną infrastrukturę sieci dzięki opcjom łączności hybrydowej. Zapoznaj się z omówieniem programu [kontrola dostępu do sieci i sieci](https://docs.microsoft.com/azure/security/security-network-overview).
- Wprowadzenie do [zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/azure-security)i dowiedzieć się o tworzeniu plan [nadzoru](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Architektura środowiska lokalnego

Poniżej przedstawiono diagram przedstawiający bieżącej infrastruktury lokalnej firmy Contoso.

 ![Architektura firmy Contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Firma Contoso ma jednego głównego centrum danych znajdujące się w mieście z nowego Jorku w wschodnich Stanach Zjednoczonych.
- Istnieją trzy dodatkowe gałęzie lokalne na terenie Stanów Zjednoczonych.
- Główne centrum danych jest połączony z Internetem za pomocą połączenia ethernet metro fiber (500 MB/s).
- Każda gałąź jest lokalnie połączony z Internetem, przy użyciu połączeń klasy biznesowej, przy użyciu tuneli IPSec sieci VPN, wróć do głównego centrum danych. Dzięki temu cała sieć jest trwale połączony oraz optymalizuje łączności z Internetem.
- Główne centrum danych jest w pełni zwirtualizowany z oprogramowaniem VMware. Firma Contoso ma dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez program vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami i serwery DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Kontrolery domeny w lokalnej gałęzi, uruchom na serwerach fizycznych.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Krok 1: Kup i subskrypcję platformy Azure

Firma Contoso potrzebuje zorientować się, jak kupić platformę Azure, jak zaprojektować subskrypcje i sposobu licencjonowania usług i zasobów.

### <a name="buy-azure"></a>Kup platformę Azure

Firmy Contoso przechodzi z [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). To pociąga za sobą złożenie zobowiązania pieniężnego na platformie Azure, uprawniających domeny Contoso do zyskują ogromne korzyści, w tym elastyczne opcje rozliczania i zoptymalizowane pod kątem ceny.

- Szacowany firmy Contoso, co będzie jego corocznej wydatki na platformę Azure. Po podpisaniu umowy, na podstawie jego Contoso wypłacane przez pierwszy rok w całości.
- Firma Contoso potrzebuje do wszystkich zobowiązań przed rokiem znajduje się nad lub utracić wartości dla tych dolarów.
- Jeśli zaistnieje Contoso przekroczy jego zobowiązanie, zużywa więcej firmy Microsoft będzie wykonać faktury różnicy.
- Koszt powyżej zobowiązanie będzie na te same stawki i te w umowie dotyczącej firmy Contoso. Nie ma żadnych kar za.

### <a name="manage-subscriptions"></a>Zarządzanie subskrypcjami

Po płacenia za usługi Azure, firma Contoso potrzebuje ustalenie sposobu zarządzania subskrypcjami platformy Azure. Firma Contoso ma umowy EA, a zatem nie limit liczby subskrypcji platformy Azure można skonfigurować.

- Rejestracji Enterprise na platformie Azure definiuje, jak firma kształty i korzysta z usług platformy Azure i definiuje strukturę zarządu core.
- Pierwszym krokiem Contoso stwierdził strukturę (znana jako enterprise scaffold rejestracji Enterprise. Contoso używane [w tym artykule](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance) pomagające zrozumieć i projektowania szkieletu.
- Na razie Contoso scrumowy zdecydował się korzystać funkcjonalne podejście do zarządzania subskrypcjami.
  - W przedsiębiorstwie użyje jednego działu IT, który kontroluje budżetu na platformie Azure. Są to grupy tylko dla subskrypcji.
  - Contoso rozszerzy tego modelu w przyszłości, tak, aby dołączyć inne grupy firmowej jako działom w rejestracji Enterprise.
  - Wewnątrz dział IT firmy Contoso ma strukturę dwiema subskrypcjami, środowiskami produkcyjnym i deweloperskim.
  - Jeśli Contoso wymaga dodatkowych subskrypcji w przyszłości, musi ona zarządzanie dostępem, zasad i zgodności dla tych subskrypcji. Contoso zostaną wprowadzone przez wprowadzenie [grup zarządzania systemu Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), jako dodatkową warstwę powyżej subskrypcji.

    ![Struktura przedsiębiorstwa](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Sprawdź licencjonowania

Z subskrypcją skonfigurowane Contoso obejrzeć licencjonowania firmy Microsoft. Strategia licencjonowania będzie zależeć od zasobów, które firma Contoso ma zostać poddana migracji do platformy Azure i jak maszyny wirtualne platformy Azure i usługi są zaznaczone i wdrożone. 

#### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Podczas wdrażania maszyn wirtualnych na platformie Azure, standardowych obrazów są dołączane licencje, który wiąże Contoso za minutę dla oprogramowanie wykorzystywane. Jednak Contoso został długoterminowe klienta firmy Microsoft i utrzymywał EA i open licencji z Software Assurance (SA). 

Korzyść użycia hybrydowego platformy Azure zapewnia ekonomiczną metodę migracji firmy Contoso, umożliwiając jej spowoduje zapisanie dla obciążeń maszyn wirtualnych platformy Azure i programu SQL Server za pomocą konwersji lub ponowne użycie systemu Windows Server Datacenter i Standard edition licencje objętych pakietem Software Assurance. Spowoduje to włączenie Contoso płacić wolniejszym tempie obliczeniowe na podstawie maszyn wirtualnych i programu SQL Server. [Dowiedz się więcej](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Przenoszenie licencji

Przenośność licencji za pośrednictwem SA zapewnia klientom licencjonowania zbiorowego firmy Microsoft, np. Contoso elastyczność wdrażania kwalifikujących się serwera aplikacji za pomocą aktywne skojarzenia zabezpieczeń na platformie Azure. Eliminuje to konieczności zakupu nowych licencji. Za pomocą nie trzeba ponosić opłat związanych z przenoszeniem istniejące licencje można łatwo wdrożyć na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Wystąpienia zarezerwowane dla przewidywalnymi obciążeniami

Przewidywalnymi obciążeniami są zawsze muszą być dostępne z uruchomionych maszyn wirtualnych. Na przykład line-of-business apps takich jak system SAP ERP.  Z drugiej strony nieprzewidywalne obciążenia są te, które są zmienne. Na przykład maszyny wirtualne, które znajdują się w trakcie wysokiej ilości pamięci i off w godzinach szczytu —.

![Wystąpienie zarezerwowane](./media/contoso-migration-infrastructure/reserved-instance.png) 

W zamian za przy użyciu wystąpienia zarezerwowane dla określonej maszyny Wirtualnej wystąpienia muszą być utrzymywane dla dużych czasów trwania czasu konsoli można uzyskać rabat i priorytety możliwościom obliczeniowym. Za pomocą [Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/), wraz z korzyści użycia hybrydowego, Contoso pozwalają zaoszczędzić do 82% zniżki na regularne płatność za rzeczywiste użycie ceny (kwiecień 2018 r.).


## <a name="step-2-manage-hybrid-identity"></a>Krok 2: Zarządzanie tożsamości hybrydowej

Zapewniając i kontrolować dostęp użytkowników do zasobów platformy Azure za pomocą funkcji zarządzania tożsamościami i dostępem (IAM) jest ważnym krokiem połączenie ze sobą infrastruktury platformy Azure.  

- Contoso decyduje się na rozszerzanie jej w lokalnej usłudze Active Directory na chmurę, zamiast tworzyć nowy oddzielnego systemu na platformie Azure.
- Tworzy oparte na platformie Azure Active Directory, aby to zrobić.
- Contoso nie ma usługi Office 365 w miejscu, więc należy ją aprowizować nowe usługi Azure AD.
- Usługi Office 365 używa usługi Azure AD do zarządzania użytkownikami. Jeśli Contoso używano usługi Office 365, może już dzierżawę usługi Azure AD i można używać go jako podstawowego usług AD.
- [Dowiedz się więcej](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) o usłudze Azure AD dla usługi Office 365 i Dowiedz się, [sposób dodawania subskrypcji](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) do istniejącej dzierżawy usługi Azure AD.

### <a name="create-an-azure-ad"></a>Tworzenie usługi Azure AD

Firma Contoso używa bezpłatna wersja usługi Azure AD, która została uwzględniona z subskrypcją platformy Azure. Administratorzy firmy Contoso Skonfiguruj katalog usługi AD w następujący sposób:

1. W [witryny Azure portal](https://portal.azure.com/), przejściu do **Utwórz zasób** > **tożsamości** > **usługi Azure Active Directory**.
2. W **Tworzenie katalogu**, określają nazwę katalogu, początkowa nazwa domeny i region, w którym można utworzyć katalogu usługi Azure AD.

    ![Tworzenie usługi Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Katalog, który jest tworzony ma początkową nazwę domeny w postaci **domainname.onmicrosoft.com**. Nazwy nie można zmienić ani usunąć. Zamiast tego należy dodać jej zarejestrowaną nazwę domeny do usługi Azure AD.

### <a name="add-the-domain-name"></a>Dodawanie nazwy domeny

Aby użyć nazwy domeny standardowej, Administratorzy Contoso należy go dodać jako niestandardowej nazwy domeny do usługi Azure AD. Ta opcja umożliwia im przypisać nazwy użytkowników znane. Na przykład użytkownik może logować się przy użyciu adresu e-mail billg@contoso.com, nie trzeba już billg@contosomigration.microsoft.com. 

Aby skonfigurować niestandardową nazwę domeny ich dodać go do katalogu, Dodaj wpis DNS, a następnie sprawdź nazwę w usłudze Azure AD.

1. W **niestandardowe nazwy domen** > **Dodaj domenę niestandardową**, co zwiększa domeny.
2. Aby użyć wpis DNS na platformie Azure, należy zarejestrować go za pomocą swojego rejestratora domen. 

    - W **niestandardowe nazwy domen** listy one informacje DNS dla nazwy należy zwrócić uwagę. Jest ona za pomocą wpisu MX.
    - Potrzebują dostępu do serwera nazw w tym celu. Zaloguj się do domeny Contoso.com i Utwórz nowy rekord MX dla wpis DNS udostępniony przez usługę Azure AD, przy użyciu podano szczegółowe informacje.  
1. Po Propagacja rekordów DNS, w nazwie Szczegóły dla danej domeny, polecenie **Sprawdź** do sprawdzenia niestandardowej nazwy domeny.

     ![System DNS Azure AD](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Konfigurowanie środowiska lokalnego i grupy usługi Azure i użytkowników

Teraz, gdy usługi Azure AD jest uruchomiona, Administratorzy muszą dodać pracownikom firmy Contoso lokalnych grup usługi AD, które synchronizują się z usługą Azure AD. Powinny używać nazw grup lokalnych, które pasują do nazw grup zasobów na platformie Azure. Dzięki temu można łatwiej identyfikować pasuje do celów synchronizacji.

#### <a name="create-resource-groups-in-azure"></a>Tworzenie grupy zasobów na platformie Azure

Grupy zasobów platformy Azure grupowania zasobów platformy Azure. Przy użyciu Identyfikatora grupy zasobów umożliwia platformie Azure wykonywać operacje na zasoby w grupie.

- Subskrypcja platformy Azure może mieć wiele grup zasobów, ale grupa zasobów może znajdować się tylko w ramach jednej subskrypcji.
- Ponadto pojedyncza grupa zasobów może mieć wiele zasobów, ale zasób może należeć tylko do pojedynczej grupy zasobów.

Administratorzy firmy Contoso Konfigurowanie grup zasobów platformy Azure, zgodnie z opisem w poniższej tabeli.

**Grupa zasobów** | **Szczegóły**
--- | ---
**ContosoCobRG** | Ta grupa zawiera wszystkie zasoby związane z ciągłości prowadzenia działalności (kolby).  Obejmuje magazyny używające Contoso dla usługi Azure Site Recovery, a usługa Azure Backup.<br/><br/> Spowoduje to również obejmować zasoby używane do celów migracji, w tym usługi Azure Migrate i usługi migracji bazy danych.
**ContosoDevRG** | Ta grupa zawiera zasoby deweloperskie i testowe.
**ContosoFailoverRG** | Ta grupa służy jako strefa docelowa nie powiodło się nad zasobami.
**ContosoNetworkingRG** | Ta grupa zawiera wszystkie zasoby sieciowe.
**ContosoRG** | Ta grupa zawiera zasoby dotyczące baz danych i aplikacji produkcyjnych.

Mogą tworzyć grupy zasobów w następujący sposób:

1. W witrynie Azure portal > **grup zasobów**, mogą dodać grupę.
2. Dla każdej grupy określają nazwę, subskrypcji, do której należy grupa i regionu.
3. Grupy zasobów są wyświetlane w **grup zasobów** listy.

    ![Grupy zasobów](./media/contoso-migration-infrastructure/resource-groups.png) 

##### <a name="scaling-resource-groups"></a>Skalowanie grup zasobów

W przyszłości spowoduje to dodanie Contoso inne grupy zasobów, w zależności od potrzeb. Na przykład można określają grupy zasobów dla każdej aplikacji lub usługi, tak, aby można zarządzać, a secure niezależnie.

#### <a name="create-matching-security-groups-on-premises"></a>Utwórz pasujące zabezpieczeń grupy lokalnej

1. W lokalnej usłudze Active Directory administratorzy Contoso skonfigurować grupy zabezpieczeń o nazwach odpowiadających nazwy grup zasobów platformy Azure.
 
    ![Lokalne grupy zabezpieczeń usługi AD](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Do celów zarządzania tworzą dodatkowe grupy, który zostanie dodany do wszystkich innych grup. Ta grupa ma uprawnienia do wszystkich grup zasobów na platformie Azure. Ograniczona liczba administratorów globalnych zostaną dodane do tej grupy.

### <a name="synchronize-ad"></a>Synchronizowanie usługi AD

Firma Contoso chce posługiwać się wspólną tożsamością do uzyskiwania dostępu do zasobów lokalnych i w chmurze. Aby to zrobić, zintegruje ją z usługą Azure AD w lokalnej usłudze Active Directory. W tym modelu:

- Użytkownicy i organizacje mogą korzystać z jednej tożsamości do uzyskania dostępu do aplikacji lokalnych i w chmurze usług takich jak usługi Office 365 i tysiącach innych witryn w Internecie.
- Administratorzy mogą korzystać z grup w AD, aby zaimplementować [kontroli dostępu na podstawie ról (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) na platformie Azure.

W celu ułatwienia integracji, firma Contoso używa [narzędzia Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Po zainstalowaniu i skonfigurować narzędzie na kontrolerze domeny synchronizuje lokalnych w środowisku lokalnym tożsamości usługi AD z usługą Azure AD. 

### <a name="download-the-tool"></a>Pobierz narzędzia


1. W witrynie Azure portal, Administratorzy Contoso przejść do **usługi Azure Active Directory** > **program Azure AD Connect**i pobrać najnowszą wersję narzędzia do serwera, są one używane do synchronizacji.

    ![Pobieranie programu AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Uruchamiają **AzureADConnect.msi** instalacji, za pomocą **Użyj ustawień ekspresowych**. Jest to najbardziej typowe instalacja i może służyć do topologii obejmujących jeden las z synchronizacją skrótów haseł dla uwierzytelniania.

    ![AD Connect Kreatora](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. W **nawiązywanie połączenia z usługi Azure AD**, określają poświadczenia do łączenia się z usługą Azure AD (w postaci CONTOSO\admin lub contoso.com\admin).

    ![AD Connect Kreatora](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. W **łączenie z usługami AD DS**, mogą określić poświadczenia dla lokalnej usługi AD.

     ![AD Connect Kreatora](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. W **wszystko gotowe do skonfigurowania**, klikając **proces synchronizacji rozpocznie się po ukończeniu konfiguracji** Aby natychmiast rozpocząć synchronizacji. Następnie podczas instalacji.

Należy pamiętać, że:
- Firma Contoso ma bezpośredniego połączenia z platformą Azure. Jeśli lokalnej usługi AD systemu Azure jest za serwerem proxy, przeczytaj ten [artykułu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Po pierwszej synchronizacji lokalne obiekty AD są widoczne w usłudze Azure AD.

    ![Lokalnej usługi AD na platformie Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Zespół IT firmy Contoso jest reprezentowana w każdej grupie na podstawie jego roli.

    ![W środowisku lokalnym członków AD na platformie Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Konfigurowanie funkcji RBAC

Azure [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić tylko takiego dostępu potrzebnym użytkownikom do wykonywania zadań. Odpowiednią rolę RBAC można przypisać do użytkowników, grup i aplikacji na poziomie zakresu. Zakres przypisania roli może być subskrypcją, grupą zasobów lub pojedynczy zasób. 

Administratorzy firmy Contoso teraz przypisuje role do grup usługi AD, które są synchronizowane ze środowiska lokalnego.

1. W **ControlCobRG** grupy zasobów, klikając **kontrola dostępu (IAM)** > **Dodaj przypisanie roli**.
2. W **Dodaj przypisanie roli** > **roli**, > **Współautor**, wybierają **ContosoCobRG** grupy usługi AD z listy. Pojawi się w grupie **wybrane elementy członkowskie** listy. 
3. One Powtórz te czynności przy użyciu tych samych uprawnień dla innych grup zasobów (z wyjątkiem **ContosoAzureAdmins**), dodając uprawnienia współautora do konta usługi AD, który odpowiada grupie zasobów.
4. Aby uzyskać **ContosoAzureAdmins** przypisują grupy usługi AD **właściciela** roli.

    ![W środowisku lokalnym członków AD na platformie Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Krok 3: Projektowanie pod kątem odporności na błędy i odzyskiwanie po awarii

### <a name="set-up-regions"></a>Konfigurowanie regionów

Zasoby platformy Azure są wdrażane w ramach regionów.

- Regiony są zorganizowane w lokalizacje geograficzne i wymagania dotyczące rezydencji i niezależności, zgodności odporności danych są honorowane w granicach geograficznych.
- Region składa się z zestawu centrów danych. Te centra danych są wdrożonego w ramach obwodu o zdefiniowanym opóźnieniu i połączony za pośrednictwem dedykowanej, regionalnej sieci o małych opóźnieniach.
- Każdy region platformy Azure jest powiązany z innym regionie, co gwarantuje odporność.
- Przeczytaj o [regionów świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/)i zrozumieć [jak są sparowane regiony](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Firma Contoso ma zdecydowano o wybraniu wschodnie stany USA 2 (znajdujący się w Wirginia) jako regionu podstawowego, a środkowe stany USA (znajdujący się w Iowa) jako regionu pomocniczego. Istnieje kilka przyczyn:

- Centrum danych firmy Contoso znajduje się w Nowym Jorku, a Contoso traktowane jako opóźnienie do najbliższego centrum danych.
- Region wschodnie stany USA 2 ma wszystkie usługi i produkty, które firma Contoso potrzebuje do użycia. Nie wszystkie regiony platformy Azure są takie same, pod względem dostępności usług i produktów. Możesz przejrzeć [produkty Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/).
- Środkowe stany USA jest platformy Azure sparowanym regionie wschodnie stany USA 2.

Jak uważa się o środowisku hybrydowym, firma Contoso potrzebuje wziąć pod uwagę sposób budowania odporności i strategii odzyskiwania po awarii do projektowania regionu. Ogólnie strategii w zakresie od wdrożenia w jednym regionie, który opiera się na platformie Azure, funkcje, takie jak domen błędów i regionalnych, parowanie odporności, za pośrednictwem do pełnego aktywne-aktywne modelu w które usług w chmurze i bazy danych są wdrożone i obsługi Użytkownicy z dwóch regionach.

Contoso postanowiła podjąć środkowej podróży. Wówczas wdrażanie aplikacji i zasobów w regionie podstawowym i zachować pełną kopię infrastruktury w regionie pomocniczym, tak, aby była gotowa do działania jako utworzenia pełnej kopii zapasowej w przypadku pełnej aplikacji po awarii lub regionalnych awarii.

### <a name="set-up-availability-zones"></a>Konfigurowanie strefy dostępności

Strefy dostępności chronią aplikacje i dane przed awariami centrum danych.

- Każda strefa dostępności to unikatowe lokalizację fizyczną w obrębie regionu platformy Azure.
- Każda strefa składa się z co najmniej jeden centrów danych, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe. 
- Istnieje co najmniej trzy osobne strefy we wszystkich regionach włączone.
- Fizyczne rozdzielenie strefach w regionie chroni aplikacje i dane przed awariami centrum danych.

Contoso wdroży dostępność strefy jako aplikacje wymagają skalowalność, wysoką dostępność i odporność. [Dowiedz się więcej](https://docs.microsoft.com/azure/availability-zones/az-overview). 


## <a name="step-4-design-a-network-infrastructure"></a>Krok 4: Projektowanie infrastruktury sieciowej

W projekcie regionalne w miejscu Contoso jest gotowy do należy wziąć pod uwagę strategii sieci. Musi ona zastanów się, jak w lokalnym centrum danych i platformą Azure, łączenie i komunikują się ze sobą oraz sposobu projektowania infrastruktury sieci na platformie Azure. W szczególności firma Contoso potrzebuje:

- **Planowanie hybrydowych łączności sieciowej**: Zorientować się, jak ma to łączenie sieci lokalnych i platformy Azure.
- **Projektowanie infrastruktury sieci platformy Azure**: Zdecyduj, jak ją wdrożyć sieci za pośrednictwem regionów. Sposobu komunikacji między sieciami w tym samym regionie, a w regionach?
- **Projektowanie i konfigurowanie sieci platformy Azure**: Skonfiguruj sieci platformy Azure i podsieci i zdecyduj, jakie będą znajdować się w nich.

### <a name="plan-hybrid-network-connectivity"></a>Planowanie hybrydowych łączności sieciowej

Contoso uważane za [liczba architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) w hybrydowym sieci między platformą Azure i lokalnym centrum danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o opcje porównywania.

Przypominamy infrastruktury sieci lokalnej firmy Contoso obecnie składa się z centrum danych w Nowym Jorku, a lokalne gałęzie w części Wschodniej Stanów Zjednoczonych.  Wszystkie lokalizacje mają klasy biznesowej połączenie z Internetem.  Każdej gałęzi jest następnie połączony centrum danych za pośrednictwem tunelu IPSec VPN za pośrednictwem Internetu.

![Sieci firmy Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Poniżej przedstawiono, jak Contoso zdecydowała się zaimplementować połączenia hybrydowe:

1. Konfigurowanie nowego połączenia sieci VPN typu lokacja lokacja między centrum danych firmy Contoso w Nowym Jorku i dwóch regionach platformy Azure w regionie wschodnie stany USA 2 i środkowe stany USA.
2. Ruch office gałęzi granica sieci wirtualnych platformy Azure będzie kierować przez główne centrum danych firmy Contoso. 
3. Jak firmy Contoso jest skalowany w górę wdrażania na platformie Azure, ustanowią połączenie usługi ExpressRoute między centrum danych i regionów platformy Azure. W takim przypadku firmy Contoso zachowa połączenia sieci VPN lokacja lokacja tylko w celach trybu failover.
    - [Dowiedz się więcej](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o wybieraniu między hybrydowe rozwiązanie sieci VPN i ExpressRoute.
    - Sprawdź [ExpressRoute lokalizacje i pomocy technicznej](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Tylko sieci VPN**

![Sieci VPN firmy Contoso](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**Sieci VPN i ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Projektowanie infrastruktury sieci platformy Azure

Koniecznie czy Contoso umieszcza sieci w miejscu, w taki sposób, że wdrożenie hybrydowe, bezpieczny i skalowalny. Aby to zrobić, Contoso trwa długoterminową i projektowania sieci wirtualnych (Vnet), aby była odporna i z myślą o przedsiębiorstwach. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) o planowaniu sieci wirtualnych.

Aby połączyć dwa regiony, Contoso zdecydował się implementacji modelu sieci koncentratora do Centrum:

- W każdym regionie Contoso użyje modelu koncentrator i klienci.
- Aby połączyć sieci i koncentratory, Contoso użyje, komunikację równorzędną sieci platformy Azure.

#### <a name="network-peering"></a>Komunikacja równorzędna w sieci

Platforma Azure udostępnia, komunikacja równorzędna w sieci do połączenia sieci wirtualnych i koncentratory. Globalnej komunikacji równorzędnej zezwala na połączenia między sieciami wirtualnymi/hubs, w różnych regionach. Lokalne komunikacji równorzędnej nawiązuje połączenie z sieciami wirtualnymi w tym samym regionie. Komunikacja równorzędna sieci wirtualnych zapewniają następujące korzyści:

- Ruch sieciowy między wirtualnymi sieciami równorzędnymi jest prywatny.
- Ruch między sieciami wirtualnymi jest pozostawiony w sieci szkieletowej firmy Microsoft. Nie publicznej sieci internet, bramy lub szyfrowania jest wymagany do komunikacji między sieciami wirtualnymi.
- Komunikacja równorzędna zapewnia domyślne, połączenie o małych opóźnieniach i dużej przepustowości między zasobami w różnych sieciach wirtualnych.

[Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o komunikacja równorzędna w sieci.

#### <a name="hub-to-hub-across-regions"></a>Centrum do Centrum w regionach

Contoso wdroży Centrum w każdym regionie. Koncentrator jest używana sieć wirtualna (VNet) na platformie Azure, która działa jako centralny punkt łączności z siecią lokalną. Centrum sieci wirtualne połączy się ze sobą przy użyciu globalnych wirtualnych sieci równorzędnych. Globalnych wirtualnych sieci równorzędnych nawiązuje połączenie z sieci wirtualnych między regionami platformy Azure.

- Centrum w każdym regionie jest połączona z jej Centrum partnerów w innym regionie.
- Centrum jest połączona z siecią, co w tym regionie i połączyć się wszystkie zasoby sieci.

    ![Globalnej komunikacji równorzędnej](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Gwiazda w obrębie regionu

W każdym regionie Contoso wdroży sieci wirtualnych do różnych celów, jako sieci gwiazdy z Centrum regionu. Sieci wirtualne w obrębie regionu za pomocą komunikacji równorzędnej połączyć swoje Centrum i ze sobą.

#### <a name="design-the-hub-network"></a>Projekt sieci Centrum

W ramach modelu gwiazdy, który wybrał firmy Contoso, trzeba myśleć o tym, jak ruch z lokalnego centrum danych i z Internetu, będą kierowane. Poniżej przedstawiono, jak Contoso zdecydował się do obsługi routingu dla wschodnie stany USA 2 i środkowe stany USA hubs:

- Firmy Contoso jest projektowanie sieci, znane jako "odwrotnej c", ponieważ jest to ścieżka, które pakiety postępuj zgodnie z przychodzącego i wychodzącego sieci.
- Architektura sieci ma dwa granice, strefie niezaufane obwodzie frontonu i zaplecza z zaufanej strefy.
- Zapora ma kartę sieciową w każdej strefie kontrolowanie dostępu do stref zaufanych.
- Z Internetu:
    - Ruch internetowy spowoduje osiągnięcie ze zrównoważonym obciążeniem publicznego adresu IP w sieci obwodowej.
    - Ten ruch jest kierowany przez zaporę z uwzględnieniem reguł zapory.
    - Po wprowadzeniu kontrolę dostępu do sieci, ruch będzie przekazywany do odpowiedniej lokalizacji w zaufanej strefy.
    - Ruch wychodzący z sieci wirtualnej będą kierowane do Internetu przy użyciu tras zdefiniowanych przez użytkownika (Udr). Ruch jest wymuszone przez zaporę i sprawdził obowiązującymi w Contoso.
- Z centrum danych firmy Contoso:
    - Ruch przychodzący za pośrednictwem sieci VPN lokacja lokacja (lub usługi ExpressRoute), liczba trafień publiczny adres IP bramy sieci VPN platformy Azure.
    - Ruch jest kierowany przez zaporę i będą podlegać reguł zapory.
    - Po zastosowaniu reguły zapory, ruch jest przekazywany do wewnętrznego modułu równoważenia obciążenia (standardowej jednostki SKU) w zaufanej strefy wewnętrznej podsieci.
    - Ruch wychodzący z podsieci zaufane w lokalnym centrum danych za pośrednictwem połączenia VPN jest kierowany przez zaporę, a zasady stosowane przed przejściem za pośrednictwem połączenia sieci VPN typu lokacja lokacja.



### <a name="design-and-set-up-azure-networks"></a>Projektowanie i konfigurowanie sieci platformy Azure

Sieć i topologię routingu w miejscu Contoso jest gotowe do skonfigurowania sieci platformy Azure i podsieci.

- Contoso wdroży sieci prywatnej klasy A na platformie Azure (0.0.0.0 do 127.255.255.255). Ta funkcja działa, od lokalnego obecnie ma ona 172.160.0/16 przestrzeń prywatnych adresów klasy B, firma Contoso może być się, że nie będzie żadnych nakładanie się zakresów adresów.
- Chce wdrożyć sieci wirtualne w regionach podstawowego i pomocniczego.
- Firma Contoso będzie używać konwencji nazewnictwa, która zawiera prefiks **sieci Wirtualnej** i skrót regionu **EUS2** lub **CUS**. Korzystając z tego standardu, sieci Centrum będą miały nazwę nadaną **VNET-HUB-EUS2** (wschodnie stany USA 2) i **VNET-HUB-CUS** (środkowe stany USA).
- Nie ma contoso [rozwiązania IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), więc należy ją zaplanować routingu w sieci bez translatora adresów sieciowych.


#### <a name="virtual-networks-in-east-us-2"></a>Sieci wirtualne w regionie wschodnie stany USA 2

Wschodnie stany USA 2 jest regionu podstawowego, który firma Contoso będzie używany do wdrażania zasobów i usług. Poniżej przedstawiono, jak firma Contoso będzie architektury sieci:

- **Centrum**: Piastą w regionie wschodnie stany USA 2 to centralny punkt podstawowej łączności z lokalnym centrum danych.
- **Sieci wirtualne**: Sieci wirtualne będące szprychami, w regionie wschodnie stany USA 2 może służyć do izolowania obciążeń, jeśli jest to wymagane. Oprócz piastą firma Contoso będzie miał szprychy dwie sieci wirtualne w regionie wschodnie stany USA 2:
    - **VNET-DEV-EUS2**. Ta sieć wirtualna zapewni opracowywania i testowania zespołu za pomocą w pełni funkcjonalnej sieci dla projektów deweloperskich. Będzie działał jako obszaru pilotażowego produkcji i będzie zależny od infrastruktury produkcji do funkcji.
    - **SIEĆ WIRTUALNA PROD-EUS2**: Składniki IaaS platformy Azure w środowisku produkcyjnym będzie znajdować się w tej sieci. 
    -  Każda sieć wirtualna ma swój własny unikatowy przestrzeń adresowa, bez nakładania się. Firma Contoso zamierza Konfigurowanie routingu bez translatora adresów sieciowych.
- **Podsieci**:
    - Nastąpi podsieci w każdej sieci dla każdej warstwy aplikacji
    - Każda podsieć w sieci produkcyjnej, będzie miała pasującą podsieć w sieci wirtualnej rozwoju.
    - Ponadto produkcyjnego środowiska sieciowego ma podsieci dla kontrolerów domeny.

Sieci wirtualne w regionie wschodnie stany USA 2 są podsumowane w poniższej tabeli.

**Sieć wirtualna** | **Range** | **Elementu równorzędnego**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | SIEĆ WIRTUALNA HUB-CUS2, EUS2-DEV-SIEĆ WIRTUALNA, SIEĆ WIRTUALNA PROD-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-PROD-CUS

![Gwiazda w regionie podstawowym](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Podsieci w sieci wschodnie stany USA 2 Centrum (sieć wirtualna-HUB-EUS2)

**Podsieć/strefy** | **CIDR** | ** Można używać adresów IP
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Podsieci w sieci wschodnie stany USA 2 Dev (sieć wirtualna-DEV-EUS2)

Tworzenie sieci wirtualnej jest używany przez zespół projektowy jako obszar pilotażowego produkcji. Zawiera trzy podsieci.

**Podsieć** | **CIDR** | **Adresy** | **W podsieci**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Maszyn wirtualnych warstwy internetowej/frontonów
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Maszyny wirtualne z bazy danych


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Podsieci w sieci wschodnie stany USA 2 produkcyjnych (sieć wirtualna-PROD-EUS2)

Składniki modelu IaaS platformy Azure znajdują się w sieci produkcyjnej. Każda warstwa aplikacji ma własnej podsieci. Podsieci odpowiadają polom w sieci rozwoju, dodając podsieci dla kontrolerów domeny.

**Podsieć** | **CIDR** | **Adresy** | **W podsieci**
--- | --- | --- | ---
**FE-PROD-EUS2** | 10.245.32.0/22 | 1019 | Maszyn wirtualnych warstwy internetowej/frontonów
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Maszyny wirtualne z bazy danych
**PROD-DC-EUS2** | 10.245.42.0/24 | 251 | Maszyny wirtualne kontrolerów domeny


![Centrum architektury sieci](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Sieci wirtualne w środkowych stanach USA (region pomocniczy)

Środkowe stany USA jest firmy Contoso w regionie pomocniczym. Poniżej przedstawiono, jak firma Contoso będzie architektury sieci:

- **Centrum**: Koncentrator, sieć wirtualną w regionie wschodnie stany USA 2, to centralny punkt łączności z lokalnym centrum danych i szprychy, sieciami wirtualnymi w regionie wschodnie stany USA 2, może być używane do izolowania obciążeń w razie potrzeby zarządzana oddzielnie od innych szprych.
- **Sieci wirtualne**: Firma Contoso będzie mieć dwie sieci wirtualne w środkowych stanach USA:
    - SIEĆ WIRTUALNA PROD-JEDNOSTKI POJEMNOŚCI. Ta sieć wirtualna jest sieci produkcyjnej, podobnie jak PROD_EUS2 sieci Wirtualnej. 
    - VNET-ASR-CUS. Ta sieć wirtualna będzie działać jako lokalizacja, w której maszyny wirtualne są tworzone po włączeniu trybu failover ze środowiska lokalnego lub jako lokalizację dla maszyn wirtualnych platformy Azure, które są przełączone w tryb failover z serwera podstawowego regionu pomocniczego. Ta sieć jest podobne do sieci w środowisku produkcyjnym, ale nie wszystkich kontrolerów domeny na nim.
    -  Każda sieć wirtualna, w regionie będzie własnej przestrzeni adresowej, bez nakładania się. Contoso skonfiguruje routing bez translatora adresów sieciowych.
- **Podsieci**: Podsieci zostanie zaprojektowana w podobny sposób jak te w regionie wschodnie stany USA 2. Wyjątkiem jest, że Contoso nie podsieć jest wymagana dla kontrolerów domeny.

Sieci wirtualne w środkowych stanach USA są podsumowane w poniższej tabeli.

**Sieć wirtualna** | **Range** | **Elementu równorzędnego**
--- | --- | ---
**SIEĆ WIRTUALNA HUB-JEDNOSTEK POJEMNOŚCI** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-PROD-CUS
**VNET-ASR-CUS** | 10.255.16.0/20 | SIEĆ WIRTUALNA HUB-JEDNOSTEK POJEMNOŚCI, SIEĆ WIRTUALNA PROD-JEDNOSTEK POJEMNOŚCI
**SIEĆ WIRTUALNA PROD-JEDNOSTEK POJEMNOŚCI** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![Gwiazda w sparowanym regionie](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Podsieci w sieci centralnej nam Centrum (sieć wirtualna-HUB-CUS)

**Podsieć** | **CIDR** | **Można używać adresów IP**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Podsieci w sieci centralnej nam produkcyjnych (sieć wirtualna-PROD-CUS)

Równolegle sieci produkcyjnej w regionie wschodnie stany USA 2 podstawowym istnieje produkcyjnego środowiska sieciowego w dodatkowym regionie środkowe stany USA. 

**Podsieć** | **CIDR** | **Adresy** | **W podsieci**
--- | --- | --- | ---
**FE-PROD-CUS** | 10.255.32.0/22 | 1019 | Maszyn wirtualnych warstwy internetowej/frontonów
**PROD-APLIKACJI-JEDNOSTEK POJEMNOŚCI** | 10.255.36.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**DB-PROD-CUS** | 10.255.40.0/23 | 507 | Maszyny wirtualne z bazy danych
**CUS-PROD-DC** | 10.255.42.0/24 | 251 | Maszyny wirtualne kontrolerów domeny

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Podsieci w sieci odzyskiwanie w trybie failover/środkowe stany USA w środkowych stanach USA (sieć wirtualna — ASR CUS)

Sieć VNET-ASR-CUS jest używana na potrzeby trybu failover między regionami. Usługa Site Recovery będzie służyć do replikacji i pracy awaryjnej maszyn wirtualnych platformy Azure między regionami. Działa ona również jako centrum danych firmy Contoso do sieci platformy Azure dla chronionych obciążeń, które pozostają w środowisku lokalnym, ale Failover na platformie Azure w celu odzyskiwania po awarii.

Sieć wirtualna — ASR-CUS jest podstawowa tej samej podsieci sieci wirtualnej w regionie wschodnie stany USA 2, ale bez konieczności dla podsieci kontrolera domeny w środowisku produkcyjnym.

**Podsieć** | **CIDR** | **Adresy** | **W podsieci**
--- | --- | --- | ---
**USŁUGA ASR-FE-JEDNOSTEK POJEMNOŚCI** | 10.255.16.0/22 | 1019 | Maszyn wirtualnych warstwy internetowej/frontonów
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**USŁUGA ASR-DB-JEDNOSTEK POJEMNOŚCI** | 10.255.24.0/23 | 507 | Maszyny wirtualne z bazy danych

![Centrum architektury sieci](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Konfigurowanie połączeń równorzędnych

Centrum w innym regionie i wszystkie sieci wirtualne w regionie Centrum, będzie skomunikowane równorzędnie Centrum w każdym regionie. Dzięki temu centra do komunikowania się i Wyświetl wszystkie sieci wirtualne w obrębie regionu. Należy pamiętać, że:

- Komunikacja równorzędna tworzy połączenie dwustronnego. Jeden z komputer inicjujący w pierwszej sieci wirtualnej, a inny na drugiej sieci wirtualnej.
- W ramach wdrożenia hybrydowego ruchu, który przechodzi między komputerami równorzędnymi musi być widoczny z połączenia sieci VPN między lokalnym centrum danych a platformą Azure. Aby je włączyć, istnieją określone ustawienia, które muszą zostać ustawione dla połączeń równorzędnych.

Aby wszystkie połączenia z sieci wirtualne będące szprychami za pośrednictwem Centrum w lokalnym centrum danych Contoso musi zezwalać na ruch jest przekazywane i przechodzącego bram sieci VPN.

##### <a name="domain-controller"></a>Kontroler domeny

Dla kontrolerów domeny w sieci VNET-PROD-EUS2 firma Contoso chce, aby ruch do usługi flow zarówno między siecią Centrum/produkcyjnych EUS2 i za pośrednictwem połączenia VPN do sieci lokalnej. Należy go Contoso Administratorzy muszą zezwalać na następujące czynności:

1. **Zezwalaj na przekazywany ruch** i **Zezwalaj konfiguracje tranzyt bramy** równorzędnej połączenia. W naszym przykładzie jest to sieć wirtualna EUS2 Centrum połączenia sieć wirtualna-PROD-EUS2.

    ![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering1.png)

2. **Zezwalaj na ruch przesłany dalej** i **Użyj bram zdalnych** po drugiej stronie komunikacji równorzędnej, w sieci Wirtualnej EUS2 PROD połączenia sieć wirtualna-HUB-EUS2.

    ![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering2.png)

3. Lokalne mogą skonfigurować trasę statyczną, który kieruje ruch lokalny do kierowania przez tunel sieci VPN do sieci wirtualnej. Będzie można ukończyć konfiguracji w bramie, który zapewnia tunelu sieci VPN firmy Contoso na platformie Azure. W tym korzystają z usługi RRAS.

    ![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Sieciach produkcyjnych 

Sieć równorzędna spoked nie może wyświetlić sieci peer spoked w innym regionie za pośrednictwem Centrum.

Dla sieci produkcyjnych firmy Contoso w obu regionach widzą się nawzajem Administratorzy Contoso należy utworzyć bezpośrednie połączenie równorzędne dla sieci Wirtualnej-PROD-EUS2 i DARZENIE-PROD-jednostki pojemności. 

![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Skonfigurować usługę DNS

Podczas wdrażania zasobów w sieciach wirtualnych, masz kilka opcji do rozpoznawania nazw domeny. Korzystanie z rozpoznawania nazw udostępnianych przez platformę Azure lub podaj serwerów DNS do rozpoznawania. Rodzaj rozpoznawania nazw, których używasz, zależy od tego, jak zasoby muszą komunikować się ze sobą. Pobierz [informacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) o usłudze Azure DNS.

Administratorzy firmy Contoso zdecydował, że usługa Azure DNS nie jest dobrym wyborem w środowisku hybrydowym. Zamiast tego mogą zacząć korzystać z lokalnych serwerów DNS.

- Ponieważ jest to sieć hybrydowa wszystkich maszyn wirtualnych w środowisku lokalnym i na platformie Azure muszą mieć możliwość rozpoznania nazwy, aby działać prawidłowo. Oznacza to, że niestandardowych ustawień DNS musi dotyczyć wszystkie sieci wirtualne.
- Firma Contoso ma obecnie kontrolery domeny wdrożone w centrum danych firmy Contoso i w biurach oddziałów. Podstawowe serwery DNS są CONTOSODC1(172.16.0.10) i CONTOSODC2(172.16.0.1)
- Gdy sieci wirtualne są wdrażane, kontrolery domeny w środowisku lokalnym ustawi ma być używany jako serwery DNS w sieci. 
- Do skonfigurowania tego ustawienia należy, gdy przy użyciu niestandardowego systemu DNS w sieci wirtualnej, należy dodać adres IP platformy Azure rekursywnego rozpoznawania nazw (na przykład 168.63.129.16) do listy DNS.  Aby to zrobić, Contoso konfiguruje ustawienia serwera DNS w każdej sieci wirtualnej. Na przykład niestandardowe ustawienia DNS sieci VNET-HUB-EUS2 byłoby w następujący sposób:
    
    ![Niestandardowe DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Oprócz kontrolerów domeny w środowisku lokalnym firma Contoso zamierza cztery dodatkowe do obsługi sieci platformy Azure, dwóch dla każdego regionu wdrożenia. Oto, co Contoso zostanie wdrożona na platformie Azure.

**Region** | **DC** | **Sieć wirtualna** | **Podsieć** | **Adres IP**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
JEDNOSTKI POJEMNOŚCI | CONTOSODC5 | SIEĆ WIRTUALNA PROD-JEDNOSTEK POJEMNOŚCI | CUS-PROD-DC | 10.255.42.4
JEDNOSTKI POJEMNOŚCI | CONTOSODC6 | SIEĆ WIRTUALNA PROD-JEDNOSTEK POJEMNOŚCI | CUS-PROD-DC | 10.255.42.4

Po wdrożeniu kontrolerów domeny w środowisku lokalnym, firma Contoso potrzebuje można zaktualizować ustawień DNS w sieciach w dowolnym regionie w celu uwzględnienia nowych kontrolerów domeny na liście serwera DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Konfigurowanie kontrolerów domeny na platformie Azure

Po zaktualizowaniu ustawień sieci, Administratorzy firmy Contoso jest gotowe do kompilowania kontrolerów domeny na platformie Azure.

1. W witrynie Azure portal ich wdrażania nowej maszyny Wirtualnej systemu Windows Server z odpowiednią siecią wirtualną.
2. Tworzą zestawy dostępności w każdej lokalizacji maszyny wirtualnej. Zestawy dostępności wykonaj następujące czynności:
   - Upewnij się, że usługa Azure Service fabric oddziela maszyn wirtualnych do różnych infrastruktur w regionie platformy Azure. 
   - Umożliwia Contoso kwalifikował się do umowy SLA 99,95% czasu dostępności dla maszyn wirtualnych na platformie Azure.  [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

     ![Grupa dostępności](./media/contoso-migration-infrastructure/availability-group.png) 
3. Po wdrożeniu maszyny Wirtualnej otwarciu interfejsu sieciowego dla maszyny Wirtualnej. Ustaw jako prywatny adres IP na statyczne i podaj prawidłowy adres.

    ![VM NIC](./media/contoso-migration-infrastructure/vm-nic.png)

4. Teraz mogą dołączyć nowy dysk danych do maszyny Wirtualnej. Ten dysk zawiera bazy danych usługi Active Directory i udziału sysvol. 
   - Rozmiar dysku określi liczbę operacji We/Wy, która go obsługuje.
   - Wraz z upływem czasu rozmiar dysku może być konieczne zwiększa się wraz z rozwojem środowiska.
   - Dysk nie należy ustawiać odczytu/zapisu dla buforowania hosta. Active Directory bazy danych nie obsługują tego.

     ![Active Directory dysku](./media/contoso-migration-infrastructure/ad-disk.png)

5. Po dodaniu dysku będzie się z maszyną Wirtualną za pośrednictwem pulpitu zdalnego i otwórz Menedżera serwera.
6. Następnie w **usług plików i magazynowania**, uruchomieniu Kreatora nowych woluminów zapewnia, że dysk jest podany literę F: lub nowszej na lokalnej maszynie Wirtualnej.

     ![Kreator nowego woluminu](./media/contoso-migration-infrastructure/volume-wizard.png)

7. W Menedżerze serwera, co zwiększa **Active Directory Domain Services** roli. Następnie należy skonfigurować ich maszynę Wirtualną jako kontroler domeny.

      ![Rola serwera](./media/contoso-migration-infrastructure/server-role.png)  

9. Po maszyny Wirtualnej jest skonfigurowany jako kontroler domeny i ponownie uruchomiony, otwórz Menedżera DNS i skonfigurować program rozpoznawania nazw DNS platformy Azure jako usługi przesyłania dalej.  Dzięki temu kontrolera domeny do przesyłania zapytań DNS, których nie można rozpoznać w usłudze Azure DNS.

    ![DNS forwarder](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Teraz zaktualizowanie niestandardowych ustawień DNS dla poszczególnych sieci wirtualnych za pomocą odpowiedni kontroler domeny w regionie sieci wirtualnej. Obejmują one kontrolerów domeny w środowisku lokalnym, na liście.

### <a name="set-up-active-directory"></a>Konfigurowanie usługi Active Directory

Usługi AD jest krytyczne usługi w zakresie sieci i muszą być prawidłowo skonfigurowane. Administratorzy firmy Contoso utworzy Lokacje usług AD centrum danych firmy Contoso i regiony EUS2 i jednostek pojemności.  

1. Tworzą dwie nowe lokacje (EUS2 platformy AZURE i AZURE CUS) wraz z witryny Centrum danych (ContosoDatacenter).
2. Po utworzeniu witryny, tworzą podsieci w witrynach, aby były zgodne z sieciami wirtualnymi i centrum danych.

    ![Podsieci kontrolera domeny](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Następnie utwórz dwa łącza lokacji, aby połączyć wszystkie elementy. Następnie zostanie przeniesiona na kontrolerach domeny w ich lokalizacji.

    ![Linki kontrolera domeny](./media/contoso-migration-infrastructure/dc-links.png)

4. Po skonfigurowaniu jest wszystko, czego topologię replikacji usługi Active Directory jest w miejscu.
    
    ![Replikacji kontrolera domeny](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Wszystko — pełna listę kontrolerów domeny i lokacje są wyświetlane w Centrum administracyjnym w środowisku lokalnym Active Directory.

    ![Centrum administracyjne usługi AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Krok 5. Planowanie zarządzania

System Azure oferuje szeroką gamę elementów sterujących nadzoru, usług i platformą Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/security/governance-in-azure) uzyskać podstawową wiedzę na temat opcji.

Konfigurowanie tożsamości i kontroli dostępu, Contoso ma już Państwo zacząć opracowywać wprowadzone pewne aspekty zarządzania i zabezpieczeń. Ogólnie rzecz biorąc istnieją trzy obszary, które należy wziąć pod uwagę:

- **Zasady**: Zasady na platformie Azure ma zastosowanie i wymusza zasady i wpływ dotyczące zasobów, tak, aby zasoby pozostają zgodne z wymaganiami firmy i umów SLA.
- **Blokuje**: Platforma Azure zezwoli Ci do blokady subskrypcji, grupy zasobów i innych zasobów, tak, aby ich mogą być modyfikowane tylko przez osoby z urzędem, aby to zrobić.
- **Tagi**: Zasoby można kontrolowany, inspekcji i zarządzane przy użyciu tagów. Tagi dołączyć metadanych do zasobów, przekazywania informacji na temat zasobów lub właścicieli.

### <a name="set-up-policies"></a>Konfigurowanie zasad

Usługa Azure Policy oblicza zasobami skanowania dla osób, które nie są zgodne z definicjami zasad, które zostały spełnione. Na przykład Niewykluczone, że zasady, które tylko niektóre rodzaje maszyn wirtualnych umożliwia lub wymaga zasobów, które mają konkretny tag. 

Zasady usługi Azure, określ definicję zasad i przypisania zasad określa zakres, w którym można zastosować zasady. Zakres może wynosić od grupy zarządzania w grupie zasobów. [Dowiedz się,](../governance/policy/tutorials/create-and-manage.md) dotyczące tworzenia zasad i zarządzanie nimi.

Contoso chce zacząć korzystać z kilku zasad:

- Należy utworzyć zasadę, aby upewnić się, że zasoby mogą być wdrażane tylko w regionach EUS2 i jednostek pojemności.
- Chce ograniczyć jednostek SKU maszyn wirtualnych do zatwierdzonych jednostek SKU tylko. Zamiar jest zapewnienie, że kosztowne jednostek SKU maszyn wirtualnych nie są używane.

#### <a name="limit-resources-to-regions"></a>Limit zasobów w regionach

Firma Contoso używa wbudowana definicja zasad **dozwolone lokalizacje** ograniczenie regionów zasobów.

1. W witrynie Azure portal kliknij pozycję **wszystkich usług**i wyszukaj **zasad**.
2. Wybierz **przypisania** > **przypisać zasady**.
3. Na liście zasad wybierz **dozwolone lokalizacje**.
4. Ustaw **zakres** nazwę subskrypcji platformy Azure, a następnie wybierz dwa regiony na liście dozwolonych.

    ![Zasady mogą regionów](./media/contoso-migration-infrastructure/policy-region.png)

5. Domyślnie zasady zostały ustawione przy użyciu **Odmów**, co oznacza, że jeśli ktoś uruchamia wdrożenie w ramach subskrypcji, który nie znajduje się w EUS2 lub jednostek pojemności, wdrożenie zakończy się niepowodzeniem. Oto, co się stanie, jeśli ktoś w subskrypcji Contoso spróbuje skonfigurować wdrożenie w regionie zachodnie stany USA.

    ![Zasady nie powiodło się](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Zezwalaj na określonych wersji SKU maszyn wirtualnych

Firma Contoso będzie używać wbudowana definicja zasad **umożliwić maszynom wirtualnym jednostki SKU** do ograniczenia typów maszyn wirtualnych, które mogą być tworzone w ramach subskrypcji. 

![Jednostka SKU zasad](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Sprawdź zgodność z zasadami

Zasady zaczną obowiązywać natychmiast, a Contoso umożliwia usunięcie zasobów pod kątem zgodności. 

1. W witrynie Azure portal kliknij pozycję **zgodności** łącza.
2. Pojawi się pulpit nawigacyjny zgodności. Aby uzyskać więcej informacji można przejść do szczegółów.

    ![Zgodność zasad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Ustawianie blokady

Contoso długo używał ITIL framework do zarządzania swoimi systemami. Jest jednym z najważniejszych aspektów platformę, kontroli zmian i Contoso chce mieć pewność, że zmiana kontroli jest implementowana w ramach wdrożenia usługi Azure.

Firmy Contoso przechodzi do zaimplementowania blokady w następujący sposób:

- Każdy produkcji lub składnik trybu failover musi być w grupie zasobów, która ma blokadę tylko do odczytu.  Oznacza to, że można zmodyfikować lub usunąć elementy w środowisku produkcyjnym, blokada należy usunąć. 
- Grupy zasobów nieprodukcyjne mają CanNotDelete blokad. Oznacza to, że autoryzowany użytkownicy mogą odczytywać lub modyfikowanie zasobu, ale nie można go usunąć.

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) o blokady.

### <a name="set-up-tagging"></a>Konfigurowanie znaczników

Aby śledzić zasoby, ponieważ są one dodawane, będzie coraz ważniejsze dla firmy Contoso skojarzyć zasobów przy użyciu odpowiedniego działu, klientów i środowiska. 

Oprócz przekazywania informacji na temat zasobów i właścicieli, tagi spowoduje włączenie Contoso do agregacji i grupy zasobów i używać tych danych do celów dotyczących obciążenia zwrotnego.

Firma Contoso potrzebuje w celu wizualizacji swoich zasobów platformy Azure w sposób, który ma sens w firmie. Na przykład przez rolę lub działów. Pamiętaj, że zasoby nie muszą znajdować się w tej samej grupie zasobów tagiem. Contoso utworzy taksonomii prostych tagów, tak aby wszyscy używali tych samych tagów.

**Nazwa tagu** | **Wartość**
--- | ---
Centrum kosztów | 12345: Musi to być centrum kosztów prawidłowe od firmy SAP.
Jednostką biznesową | Nazwa jednostki biznesowej (od firmy SAP). CostCenter dopasowań.
ApplicationTeam | Alias adresu e-mail zespołu, który jest właścicielem pomocy technicznej dla aplikacji.
CatalogName | Nazwa aplikacji lub ShareServices, na katalog usług obsługującego zasobu.
Programu Service Manager | Alias e-mail Menedżera usług ITIL dla zasobu.
COBPriority | Priorytet ustawiony przez firmę w przypadku BCDR. Wartości od 1 do 5.
ENV | Możliwe wartości są Deweloperskie, STG, produkcyjne. Reprezentujący tworzenie środowisk przejściowych i produkcyjnych.

Na przykład: 

 ![Tagi platformy Azure](./media/contoso-migration-infrastructure/azure-tag.png)

Po utworzeniu tagu Contoso Przejdź wstecz i tworzenie nowych definicji zasad platformy Azure i przydziały, aby wymusić stosowanie wymaganych tagów w całej organizacji.


## <a name="step-6-consider-security"></a>Krok 6: Należy wziąć pod uwagę zabezpieczeń

Zabezpieczenia są niezwykle istotne w chmurze, a system Azure oferuje szeroką gamę narzędzi zabezpieczeń i możliwości. Ułatwiają one tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Odczyt [chmura zaufać](https://azure.microsoft.com/overview/trusted-cloud/) Aby dowiedzieć się więcej o zabezpieczeniach platformy Azure.

Istnieje kilka aspektów Contoso wziąć pod uwagę:

- **Usługa Azure Security Center**: Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Usługa Security Center umożliwia stosowanie zasad zabezpieczeń do różnych obciążeń, ograniczanie podatności na zagrożenia i wykrywanie ataków oraz reagowanie na nie.  [Dowiedz się więcej](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Sieciowe grupy zabezpieczeń (NSG)**: Sieciowa grupa zabezpieczeń jest filtr (zapora), który zawiera listę zabezpieczeń reguły, po zastosowaniu blokują lub zezwalają na ruch sieciowy do zasobów połączonych z sieciami wirtualnymi platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Szyfrowanie danych**: Usługa Azure Disk Encryption jest możliwość ułatwiające szyfrowanie dysków maszyn wirtualnych IaaS systemu Linux i Windows. [Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Praca z usługą Azure Security Center

Contoso szuka szybki wgląd w stan zabezpieczeń swojej nowej chmury hybrydowej, a konkretnie jej obciążeń platformy Azure.  Co w efekcie Contoso podjęła decyzję o wdrożenia usługi Azure Security Center, zaczynając od następujących funkcji: 

- Scentralizowane zarządzanie zasadami
- Ciągła ocena
- Zalecenia z możliwością wykonywania akcji 

#### <a name="centralize-policy-management"></a>Scentralizowane zarządzanie zasadami

Za pomocą scentralizowanego zarządzania zasadami Contoso zapewnia zgodność z wymaganiami dotyczącymi zabezpieczeń przez centralne zarządzanie zasadami zabezpieczeń WE całego środowiska. Można szybko i łatwo implementuje ona zasadę, która ma zastosowanie do wszystkich swoich zasobów platformy Azure.

![Zasady zabezpieczeń](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Ocena i akcji

Firma Contoso będzie korzystać ciągła ocena zabezpieczeń, który monitoruje bezpieczeństwo maszyn, sieci, magazynowania, danych i aplikacji; Aby odnaleźć potencjalne problemy z zabezpieczeniami. 

- Usługa Security Center analizuje stan zabezpieczeń wystąpień obliczeniowych, infrastruktury i zasobów danych firmy Contoso i aplikacje platformy Azure i usług.
- Ciągła ocena ułatwia zespół operacyjny Contoso do wykrywania potencjalnych problemów z zabezpieczeniami, takich jak systemy z brakującymi aktualizacjami zabezpieczeń lub narażone porty sieciowe. 
- W szczególności Contoso chce mieć pewność, że wszystkie maszyny wirtualne są chronione. Usługa Security Center pomaga dzięki temu sprawdzania kondycji maszyny Wirtualnej i zalecenia określonymi priorytetami i możliwością wykonywania akcji korygowania luk w zabezpieczeniach, zanim one się wykorzystać.

![Monitorowanie](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Praca z sieciowymi grupami zabezpieczeń

Firma Contoso może ograniczać ruch sieciowy do zasobów w sieci wirtualnej przy użyciu sieciowych grup zabezpieczeń.

- Grupa zabezpieczeń sieci zawiera listę reguł zabezpieczeń, które zezwalają na lub blokują przychodzący lub wychodzący ruch sieciowy na podstawie źródłowego lub docelowego adresu IP, portu i protokołu.
- Po zastosowaniu do podsieci, zasady są stosowane do wszystkich zasobów w podsieci. Oprócz interfejsów sieciowych w tym wystąpienia usług platformy Azure wdrożone w tej podsieci.
- Grupy zabezpieczeń aplikacji (asg) umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.
    - Grupy zabezpieczeń aplikacji oznacza, że Contoso można ponownie użyć zasad zabezpieczeń na dużą skalę bez ręcznej obsługi jawnych adresów IP. Platforma obsługuje złożoność jawnych adresów IP i wiele zestawów reguł, co pozwala skupić się na logice biznesowej.
    - Contoso można określić grupy zabezpieczeń aplikacji jako źródło i obiekt docelowy reguły zabezpieczeń. Po zdefiniowaniu zasad zabezpieczeń firmy Contoso można tworzyć maszyny wirtualne i przypisywanie kart sieciowych maszyny Wirtualnej do grupy. 


Contoso wdroży kombinacji grup zabezpieczeń sieci i grupy zabezpieczeń aplikacji. Contoso faktycznie zajmuje się zarządzania sieciowej grupy zabezpieczeń. Jest również martwisz się o nadużycia sieciowych grup zabezpieczeń i złożonością dodaną dla pracowników operacyjnych. Oto, co będzie wykonywać firma Contoso:

- Cały ruch do i z wszystkich podsieci (północ południe), będą podlegać regułę sieciowej grupy zabezpieczeń, z wyjątkiem GatewaySubnets w sieciach koncentratora.
- Wszystkie zapory lub kontroler domeny będą chronione za sieciowych grup zabezpieczeń w podsieci i sieciowych grup zabezpieczeń z karty Sieciowej.
- Wszystkie aplikacje w środowisku produkcyjnym będzie stosowane grupy zabezpieczeń aplikacji.


Contoso opracowała modelu to podgląd dla swojej aplikacji.

![Bezpieczeństwo](./media/contoso-migration-infrastructure/asg.png)


Sieciowe grupy zabezpieczeń skojarzone z grupy zabezpieczeń aplikacji zostanie skonfigurowany z najniższymi uprawnieniami, aby upewnić się, które są dozwolone tylko pakiety mogą przepływać z jednej części sieci do miejsca docelowego.

**Akcja** | **Nazwa** | **Element źródłowy** | **Docelowy** | **Port**
--- | --- | --- | --- | --- 
Zezwalaj | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | APP1-FE 80, 443
Zezwalaj | AllowWebToApp | FE KOMPUTERA APP1 | APP1-DB | 1433
Zezwalaj | AllowAppToDB | APP1-APP | Dowolne | Dowolne
Zablokuj | DenyAllInbound | Dowolne | Dowolne | Dowolne

### <a name="encrypt-data"></a>Szyfrowanie danych

Usługa Azure Disk Encryption integruje się z usługą Azure Key Vault ułatwiają sterowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. Zapewnia, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage.  

- Contoso stwierdził, że określone maszyny wirtualne wymagają szyfrowania.
- Contoso zastosuje szyfrowania do maszyn wirtualnych przy użyciu klientów i poufne, lub PPI danych.


## <a name="conclusion"></a>Podsumowanie

W tym artykule Konfigurowanie infrastruktury platformy Azure i zasad dla subskrypcji platformy Azure, firma Contoso hybrydowego zidentyfikować, odzyskiwania po awarii, sieci, zarządzania i zabezpieczeń. 

Nie wszystkie kroki Contoso zakończone, w tym miejscu są wymagane do migracji do chmury. W tym przypadku on chciała Planowanie infrastruktury sieci, które mogą służyć do wszystkich typów migracji i jest bezpieczne, odporne na błędy i skalowalnej. 

Za pomocą tej infrastruktury w miejscu Contoso jest gotowy do dalej i wypróbuj działanie migracji.

## <a name="next-steps"></a>Kolejne kroki

Jako pierwszego scenariusza migracji, firma Contoso ma [oceny lokalnej rozwiązania SmartHotel360 dwuwarstwowej aplikacji pod kątem migracji do usługi Azure](contoso-migration-assessment.md). 
