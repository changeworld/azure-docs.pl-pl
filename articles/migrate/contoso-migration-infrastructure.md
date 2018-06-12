---
title: Contoso — Konfigurowanie infrastruktury migracji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Contoso konfiguruje infrastruktury platformy Azure dla migracji na platformie Azure.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 8b7f0675c1bbf378d02eb52843caf27a1dce2fb8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301205"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso — wdrażanie infrastruktury migracji

W tym artykule sprawdza jak Contoso konfiguruje lokalną i infrastruktury platformy Azure w ramach przygotowania do migracji do usługi Azure, a także uruchamiania biznesowe w środowisku hybrydowym.

- Jest to architektura próbki jest specyficzne dla firmy Contoso.
- Czy potrzebujesz wszystkie elementy opisane w artykule zależy od strategię migracji. Na przykład jeśli tworzysz tylko aplikacje natywne w chmurze na platformie Azure mogą wymagać mniej złożona struktury sieci.

Ten dokument jest drugi w serii artykuły, które dokumentu jak fikcyjnej firmy Contoso migruje zasobów lokalnych do chmury Microsoft Azure. Seria zawiera również ogólne informacje, oraz zestaw scenariusze wdrażania, który ilustruje sposób konfigurowania infrastruktury migracji, oceny przydatności zasobów lokalnych do migracji, a następnie uruchom różnych rodzajów migracji. Scenariusze zwiększa się złożoność i które będą dodawane dodatkowe artykuły wraz z upływem czasu.

**Artykuł** | **Szczegóły** | **Stan**
--- | --- | ---
[Artykuł 1: omówienie](contoso-migration-overview.md) | Zawiera omówienie strategii migracji firmy Contoso, serii artykułu i przykładowe aplikacje, których będziemy używać. | Dostępne
Artykuł 2: Wdrażanie infrastruktury platformy Azure (w tym artykule) | W tym artykule opisano, jak Contoso przygotowuje jego lokalnymi i infrastruktury platformy Azure do migracji. Infrastruktura jest używana w przypadku wszystkich scenariuszy migracji Contoso. | Dostępne
[Artykuł 3: Oceny zasobów lokalnych](contoso-migration-assessment.md) | Pokazuje, jak Contoso uruchamia ocenę ich lokalnych dwuwarstwowa SmartHotel aplikacji uruchomionej na VMware. Ich oceny maszyn wirtualnych aplikacji o [migracji Azure](migrate-overview.md) usługi i bazy danych programu SQL Server aplikacji z [Asystenta migracji bazy danych Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Dostępne
[Artykuł 4: Rehost maszyny wirtualne platformy Azure i zarządzanych wystąpienie serwera SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji na platformie Azure. Migrowania aplikacji frontonu maszyny Wirtualnej w programie [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)i przy użyciu bazy danych aplikacji [migracja bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview) usługi migrację do zarządzanego wystąpienie serwera SQL. | Dostępne
[Artykuł 5: Rehost na maszynach wirtualnych platformy Azure](contoso-migration-rehost-vm.md) | Pokazuje, jak Contoso migracji aplikacji SmartHotel maszyn wirtualnych tylko przy użyciu usługi Site Recovery.
[Artykuł 6: Rehost maszyny wirtualne platformy Azure i grup dostępności programu SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Pokazuje, jak Contoso migruje SmartHotel aplikacji. Site Recovery ich użyć do migracji aplikacji maszyny wirtualne i usługi migracja bazy danych do migracji aplikacji bazy danych do grupy dostępności programu SQL Server. | Dostępne
[Artykuł 7: Rehost aplikacji systemu Linux, aby maszyny wirtualne platformy Azure](contoso-migration-rehost-linux-vm.md) | Pokazuje, jak Contoso migruje aplikacji osTicket systemu Linux na maszynach wirtualnych platformy Azure. | Dostępne
[Artykuł 8: Rehost aplikację systemu Linux, aby maszyny wirtualne platformy Azure i serwerem MySQL Azure](contoso-migration-rehost-linux-vm-mysql.md) | Pokazuje, jak Contoso migruje Linux osTicket aplikacji przy użyciu usługi Site Recovery i MySQL Workbench do migracji (kopii zapasowej i przywracania) na wystąpienie serwera Azure MySQL. | Dostępne

W tym artykule Contoso zdefiniowane wszystkie elementy infrastruktury muszą zakończyć scenariuszy migracji. 


## <a name="overview"></a>Przegląd

Przed ich można migrować na platformie Azure, jest krytyczny, że Contoso przygotowanie infrastruktury.  Ogólnie rzecz biorąc istnieją pięć obszary potrzebnych można traktować:

1. **Subskrypcje platformy Azure**: jak będą one zakupu platformy Azure i interakcji z platformą Azure i usługi?
2. **Tożsamość hybrydowa**: jak będą one zarządzania i kontrolować dostęp do lokalnych i dostępnych zasobów platformy Azure po zakończeniu migracji? Jak one rozszerzać lub Przenieś zarządzania tożsamościami w chmurze?
3. **Odzyskiwanie po awarii i odporności**: jak będzie zapewnić że infrastruktura i aplikacje są odporne po wystąpieniu awarii i awarii?
4. **Sieć**: jak powinny one projektowania infrastruktury sieci i nawiąż łączność programów ich lokalnych centrów danych i Azure?
5. **Zabezpieczenia i nadzór nad**: jak będą one zabezpieczania wdrożenia ich hybrydowych/Azure i przechowuj je wyrównane wymagania dotyczące zabezpieczeń i zarządzania?

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem możemy patrzeć infrastruktury, można odczytać niektóre informacje o możliwości platformy Azure, które omówiono w tym artykule:

- Istnieją różne opcje dostępne do zakupu dostępu na platformie Azure, w tym płatność za rzeczywiste użycie, umowy Enterprise (EA) lub licencjonowania Open z odsprzedawców firmy Microsoft lub firm partnerskich Microsoft wiedzieć, jak dostawców rozwiązań w chmurze (CSP). Dowiedz się więcej o [opcji zakupu](https://azure.microsoft.com/pricing/purchase-options/)i przeczytaj temat [subskrypcji platformy Azure są zorganizowane](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Omówienie usługi Azure [zarządzania tożsamościami i dostępem](https://www.microsoft.com/en-us/trustcenter/security/identity). W szczególności, Dowiedz się więcej o [usługi Azure AD i rozszerzanie lokalnej usłudze AD do chmury](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Jest przydatne do pobrania Książka elektroniczna o [zarządzania tożsamościami i dostępem (IAM) w środowisku hybrydowym](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Platforma Azure oferuje niezawodną infrastrukturę sieci z opcjami połączeń hybrydowych. Omówienie [kontrola dostępu do sieci i sieci](https://docs.microsoft.com/azure/security/security-network-overview).
- Wprowadzenie do [zabezpieczeń Azure](https://docs.microsoft.com/azure/security/azure-security)i przeczytaj informacje o utworzeniu planu [ładu](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>Architektura lokalnej

Poniżej przedstawiono diagram przedstawiający bieżącej infrastruktury lokalnej firmy Contoso.

 ![Architektura firmy Contoso](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Firma Contoso ma jeden głównego centrum danych znajduje się w mieście z nowego Jorku w Wschodniej Stanów Zjednoczonych.
- Mają one trzy dodatkowe gałęzi lokalnej w Stanach Zjednoczonych.
- Głównego centrum danych jest połączony z Internetem za pomocą połączenia ethernet metro fiber (500 MB/s).
- Każda gałąź jest lokalnie połączony z Internetem przy użyciu połączenia klasy biznesowych, z tuneli protokołu IPSec sieci VPN do głównego centrum danych. Dzięki temu ich całej sieci trwale zostać nawiązane połączenie oraz optymalizuje łączności z Internetem.
- Głównego centrum danych jest w pełni zwirtualizowanych z programu VMware. Mają one dwa hosty wirtualizacji ESXi 6.5, które są zarządzane przez vCenter Server 6.5.
- Firma Contoso używa usługi Active Directory do zarządzania tożsamościami i serwery DNS w sieci wewnętrznej.
- Kontrolery domeny w centrum danych, uruchom na maszynach wirtualnych VMware. Na kontrolerach domeny w lokalnej gałęzi działa na serwerach fizycznych.


## <a name="step-1-buy-and-subscribe-to-azure"></a>Krok 1: Kup i Subskrypcja platformy Azure

Contoso chce dowiedzieć się, jak kupić usługę Azure, jak zaprojektować subskrypcje i sposobu licencjonowania usług i zasobów.

### <a name="buy-azure"></a>Kup Azure

Firma Contoso ma z [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Pociąga za sobą góry zobowiązaniom pieniężnym na platformie Azure, uprawniające je, aby uzyskać doskonałe korzyści, w tym elastyczne opcje rozliczeń i zoptymalizowane cennik.

- Szacowany firmy Contoso, co będzie ich corocznej spędzają na platformie Azure. One podpisane umowy, ich płatnej w pierwszym roku, w całości.
- Contoso musi korzystać ze wszystkich ich zobowiązań przed roku znajduje się nad lub spowoduje to utratę wartości dla tych dolarów.
- Jeśli niektóre przyczyny przekroczenia ich zobowiązań i poświęcić więcej, Microsoft będzie je faktury różnicy.
- Jakichkolwiek kosztów ponoszonych powyżej zobowiązania będzie na tych samych stawek i te w ich kontraktu. Nie ma żadnych kar za.

### <a name="manage-subscriptions"></a>Zarządzanie subskrypcjami

Po płatności dla platformy Azure, Contoso należy dowiedzieć się, jak zarządzać ich subskrypcji. Mają one EA, i w związku z tym brak limitu liczby subskrypcji platformy Azure są można skonfigurować.

- Rejestracja Enterprise Azure definiuje sposób kształtu firmy i używa usług Azure, a następnie definiuje strukturę zarządu core.
- Pierwszym krokiem Contoso określono struktury (znanych jako szkieletu enterprise dla ich rejestracji Enterprise. Używały [w tym artykule](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) aby pomóc zrozumieć i szkieletu do projektu.
- Obecnie Contoso zdecydował się użyć funkcjonalności podejście do zarządzania ich subskrypcji.
    - Wewnątrz przedsiębiorstw zapewnia jednego działu informatycznego, które kontroluje Azure budżetu. Są to tylko grupy z subskrypcjami.
    - Ich w przyszłości, rozszerzać ten model, aby dołączyć do innych grup firmowej jako działów w rejestracji Enterprise.
    - Wewnątrz dział IT firmy Contoso ma strukturę subskrypcje produkcyjnego i rozwoju.
    - Jeśli Contoso wymaga dodatkowych subskrypcji w przyszłości, muszą zarządzać dostępem, zasad i zgodności dla tych subskrypcji. One będzie można to zrobić przez wprowadzenie [grup zarządzania Azure](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), jako dodatkową warstwę powyżej subskrypcji.

    ![Struktura Enterprise](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Sprawdź licencjonowania

Z subskrypcją skonfigurowane mogą zobaczyć Contoso ich licencji firmy Microsoft. Ich licencjonowania strategii będzie zależeć od zasobów, które mają zostać poddane migracji do platformy Azure i jak maszynach wirtualnych platformy Azure i usługi są zaznaczone i wdrożone. 

#### <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W przypadku wdrażania maszyn wirtualnych na platformie Azure, standardowe obrazy obejmują licencji, która będzie naliczają opłaty za minutę używanego oprogramowania firmy Contoso. Jednak Contoso zostało długoterminowej klienta firmy Microsoft i utrzymywał EAs i otworzyć licencji z pakietem software assurance (SA). 

Korzyści hybrydowe platformy Azure udostępnia metodę ekonomicznego migracji firmy Contoso, umożliwiając zapisanie na maszynach wirtualnych platformy Azure i SQL Server obciążeń konwertowania lub ponowne użycie systemu Windows Server Datacenter i Standard licencji pakietem Software Assurance. Spowoduje to włączenie firmy Contoso w celu opłacenia mniejszej szybkości obliczeniowe na podstawie maszyn wirtualnych i programu SQL Server. [Dowiedz się więcej](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Przenoszenie licencji

Przenośność licencji za pośrednictwem SA umożliwiają klientów licencjonowania zbiorowego firmy Microsoft, takich jak Contoso elastyczność wdrażanie aplikacji serwera kwalifikujących się przy użyciu aktywne skojarzenia zabezpieczeń na platformie Azure. Eliminuje to potrzebę zakupu nowej licencji. Z nie opłat skojarzonych mobilności istniejących licencji można łatwo wdrożyć na platformie Azure. [Dowiedz się więcej](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Rezerwacja wystąpień przewidywalna obciążeń

Przewidywalna obciążeń są zawsze muszą być dostępne z uruchomionych maszyn wirtualnych. Na przykład biznesowych — aplikacje, takie jak systemu SAP ERP.  Z drugiej strony nieprzewidywalne obciążeń są tymi, które są zmienne. Na przykład maszyny wirtualne znajdujące się w trakcie wysokiej żądanie i wyłączone na godziny poza szczytem.

![Wystąpienie zarezerwowane](./media/contoso-migration-infrastructure/reserved-instance.png) 

W zamian za pomocą wystąpień zarezerwowane dla określonych wystąpień maszyn wirtualnych, które wiedzieli, muszą być przechowywane do dużych okresów czasu, konsoli można uzyskać zarówno rabat i priorytetową pojemności. Przy użyciu [wystąpień zastrzeżone Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/), wraz z Azure hybrydowego korzyści, Contoso może zapisać do % 82 poza regularne płatność za rzeczywiste użycie cennik (kwiecień 2018).


## <a name="step-2-manage-hybrid-identity"></a>Krok 2: Zarządzanie tożsamość hybrydowa

Nadanie i kontrola dostępu użytkownika do zasobów platformy Azure przy użyciu zarządzania tożsamościami i dostępem (IAM) jest ważnym krokiem w instalacji infrastruktury platformy Azure.  

- Firma Contoso chce rozszerzenie usługi Active Directory ich lokalnych do chmury, zamiast tworzenia nowej oddzielnym systemie na platformie Azure.
- Tworzenia opartych na usłudze Azure Active Directory w tym celu.
- Contoso nie mają usługi Office 365 w miejscu, więc należy udostępnić nowe usługi Azure AD.
- Usługi Office 365 używa usługi Azure AD do zarządzania użytkownikami. Contoso używania usługi Office 365, one będą już mieć cechą usługi Azure AD i używać go jako ich głównej AD.
- [Dowiedz się więcej](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) dotyczące usługi Azure AD dla usługi Office 365 i dowiedzieć się [sposób dodawania subskrypcji](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) do istniejącej usługi Azure AD.

### <a name="create-an-azure-ad"></a>Tworzenie usługi Azure AD

Contoso za pomocą bezpłatna wersja usługi Azure AD, która była dostępna z subskrypcją platformy Azure. Dodaj nowy katalog AD w następujący sposób:

1. W [portalu Azure](http://portal.azure.com/), Contoso, przejdź do **Utwórz zasób** > **tożsamości** > **usługi Azure Active Directory**.
2. W **Utwórz katalog**, określa nazwę katalogu, początkową nazwę domeny i regionu, w którym można utworzyć katalogu usługi Azure AD.

    ![Tworzenie usługi Azure AD](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Podczas tworzenia katalogu ma początkową nazwę domeny w domainname.onmicrosoft.com formularza. Nazwa nie można zmienić ani usunąć. Zamiast tego należy dodać ich zarejestrowaną nazwę domeny do usługi Azure AD.

### <a name="add-the-domain-name"></a>Dodaj nazwę domeny

Aby używać nazwy domeny standardowe, Contoso należy go dodać jako nazwy niestandardowego do usługi Azure AD. Ta opcja umożliwia administratorom przypisać nazwy znanych użytkowników. Na przykład użytkownik może zalogować się przy użyciu adresu e-mail billg@contoso.com, nie trzeba billg@contosomigration.onmicrosoft.com. 

Aby skonfigurować niestandardową nazwę one Dodaj go do katalogu, Dodaj wpis DNS, a następnie sprawdź nazwę w usłudze Azure AD.

1. W **niestandardowych nazw domen** > **Dodaj domenę niestandardową**, co zwiększa domeny.
2. Aby użyć wpis DNS na platformie Azure muszą zarejestrowanie go za pomocą ich rejestratora domen. 

    - W **niestandardowych nazw domen** listy przyjmują informacje dotyczące systemu DNS dla nazwy. Contoso są za pomocą wpisu MX.
    - Wymagany jest dostęp do serwera nazw w tym celu. W przypadku firmy Contoso logowanie do domeny Contoso.com i utworzyć nowy rekord MX wpis DNS udostępniony przez usługę Azure AD przy użyciu szczegółów inaczej.  
1. Po Propagacja rekordów DNS, w nazwie szczegółów dla danej domeny, użytkownik klika polecenie **Sprawdź** do sprawdzenia nazwy niestandardowego.

     ![Usługa DNS platformy Azure AD](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Konfigurowanie lokalnego i Azure grup i użytkowników

Teraz, ich usługi Azure AD jest uruchomiona, Contoso trzeba dodać pracowników do lokalnej grupy AD, które będzie synchronizować z usługą Azure AD. Zaleca się, że korzystają z lokalnych nazw grup, które pasują do nazw grup zasobów na platformie Azure. Dzięki temu można łatwiej zidentyfikować pasuje do celów synchronizacji.

#### <a name="create-resource-groups-in-azure"></a>Tworzenie grupy zasobów na platformie Azure

Grup zasobów platformy Azure zbiera zasobów platformy Azure. Za pomocą Identyfikatora grupy zasobów umożliwia Azure do wykonywania operacji na zasoby w grupie.

- Subskrypcja platformy Azure może mieć wiele grup zasobów, ale grupy zasobów może znajdować się tylko w ramach jednej subskrypcji.
- Ponadto pojedyncza grupa zasobów może mieć wielu zasobów, ale zasób może należeć tylko do jednej grupy.

Contoso Konfigurowanie grup zasobów platformy Azure, zgodnie z opisem w poniższej tabeli.

**Grupa zasobów** | **Szczegóły**
--- | ---
**ContosoCobRG** | Ta grupa zawiera wszystkie zasoby związane z ciągłości prowadzenia działalności (kolby).  Obejmuje on magazynów utworzonych Contoso podczas korzystania z usługi Azure Site Recovery i usługi Azure Backup.<br/><br/> Zawiera również zasoby używane dla migracji, m.in. Azure migracji i usługi migracji bazy danych.
**ContosoDevRG** | Ta grupa zawiera zasoby prac deweloperskich i testowych.
**ContosoFailoverRG** | Ta grupa służy jako strefy docelowej nie powiodła się nad zasobami.
**ContosoNetworkingRG** | Ta grupa zawiera wszystkie zasoby sieci.
**ContosoRG** | Ta grupa zawiera zasoby powiązane z baz danych i aplikacji w środowisku produkcyjnym.

Tworzenia grup zasobów w następujący sposób:

1. W portalu Azure > **grup zasobów**, co zwiększa grupy.
2. Dla każdej grupy określają nazwę, subskrypcję, do której należy grupa i region.
3. Grupy zasobów są wyświetlane w **grup zasobów** listy.

    ![Grupy zasobów](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Utwórz pasujące zabezpieczeń grup lokalnych

1. Ich lokalnej usługi Active Directory Contoso Konfigurowanie grup zabezpieczeń o nazwach odpowiadających nazwy grup zasobów platformy Azure.
 
    ![Lokalne grupy zabezpieczeń usługi AD](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Do celów zarządzania ich utworzyć dodatkowe grupy, który zostanie dodany do wszystkich innych grupach. Ta grupa ma uprawnienia do wszystkich grup zasobów na platformie Azure. Ograniczona liczba administratorów globalnych zostanie dodany do tej grupy.

### <a name="synchronize-ad"></a>Synchronizacja usługi AD

Contoso chce dostarczyć wspólną tożsamość do uzyskiwania dostępu do zasobów lokalnych i w chmurze. Aby to zrobić, ich integracji usługi Active Directory ich lokalnych z usługą Azure AD. W tym modelu:

- Użytkownicy i organizacje mogą korzystać z jednej tożsamości dostęp do aplikacji lokalnych i w chmurze usługi, takie jak usługi Office 365 lub tysięcy innych witryn w Internecie.
- Administratorzy mogą korzystać z tych grup w usłudze AD do zaimplementowania [kontroli dostępu na podstawie ról (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) na platformie Azure.

W celu ułatwienia integracji, użyj Contoso [narzędzia Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Po zainstalowaniu i skonfigurować narzędzie na kontrolerze domeny synchronizacji lokalnej lokalnej tożsamości usługi AD do usługi Azure AD. 

### <a name="download-the-tool"></a>Pobierz narzędzie

1. W portalu Azure, Azure przechodzi do **usługi Azure Active Directory** > **Azure AD Connect**i pliki do pobrania najnowszej wersji narzędzia na serwerze używanym do synchronizacji.

    ![Pobieranie AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Uruchamiają **AzureADConnect.msi** instalacji przy użyciu **Użyj ustawień ekspresowych**. Najbardziej typowe instalacji i może służyć do topologii z jednym lasem z synchronizacji skrótu hasła do uwierzytelnienia.

    ![AD Connect Kreatora](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. W **nawiązywanie połączenia z usługą Azure AD**, określają poświadczenia do połączenia z usługą Azure AD (w postaci CONTOSO\admin lub contoso.com\admin).

    ![AD Connect Kreatora](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. W **łączenie z usługami AD DS**, określają poświadczeń dla lokalnych AD.

     ![AD Connect Kreatora](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. W **wszystko gotowe do skonfigurowania**, użytkownik klika polecenie **Uruchom proces synchronizacji, po zakończeniu konfiguracji** Aby natychmiast uruchomić synchronizacji. Następnie instalacji.


- Firma Contoso ma bezpośredniego połączenia z platformą Azure. Jeśli lokalnej usługi AD jest za serwerem proxy, przeczytaj [artykułu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Po pierwszej synchronizacji w lokalnym AD obiekty są widoczne w usłudze Azure AD.

    ![Lokalnej usłudze AD w systemie Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Dział IT firmy Contoso są reprezentowane w każdej grupie, na podstawie ich ról.

    ![Lokalne elementy członkowskie AD na platformie Azure](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Konfigurowanie RBAC

Azure [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań. Odpowiednie role RBAC można przypisać do użytkowników, grup i aplikacji na poziomie zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji. 

Contoso teraz przypisać role do grupy AD, które są synchronizowane z lokalnej.

1. W **ControlCobRG** grupy zasobów, które użytkownik klika polecenie **(IAM) kontroli dostępu** > **Dodaj**.
2. W **dodać uprawnienia** > **roli**, wybierają **współautora**i wybierz **ContosoCobRG** grupy usługi Active Directory z listy. Pojawi się w grupie **wybranych składników** listy. 
3. One Powtórz te czynności przy użyciu tego samego uprawnień dla innych grup zasobów (z wyjątkiem **ContosoAzureAdmins**), przez dodawanie uprawnienia współautora do konta usługi AD, który odpowiada grupie zasobów.
4. Aby uzyskać **ContosoAzureAdmins** grupy AD przypisują **właściciela** roli.

    ![Lokalne elementy członkowskie AD na platformie Azure](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>Krok 3: Projektowanie pod kątem odporności i odzyskiwaniem po awarii

Zasoby platformy Azure są wdrażane w regionach.
- Regiony są zorganizowane w lokalizacji geograficznych i wymagania dotyczące siedziby, suwerenności, zgodności i odporności danych są honorowane w granicach geograficznych.
- Region składa się z zestawem centrów danych. Tych centrach danych są wdrożone w ramach obwodu zdefiniowane opóźnienia i połączone za pośrednictwem dedykowanej regionalnej sieci małe opóźnienia.
- Każdy region platformy Azure jest skojarzone z innym regionie odporność na awarie.
- Przeczytaj informacje o [regiony platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)i zrozumieć [jak regiony są skojarzone](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso zdecydował się go z wschodnie stany USA 2 (znajdujący się w Virginia) jako ich regionu podstawowego i środkowe stany USA jako ich regionie pomocniczym. Istnieje kilka przyczyn to:

- Centrum danych firmy Contoso znajduje się w Nowym Jorku, i są traktowane jako czas oczekiwania do najbliższego centrum danych.
- Regionu wschodnie stany USA 2 ma wszystkie usługi i produkty, które są im niezbędne do użycia. Nie wszystkie regiony platformy Azure są takie same, produktów i usług. Możesz przejrzeć [produkty Azure według regionu](https://azure.microsoft.com/global-infrastructure/services/).
- Środkowe stany USA jest Azure sparowanym regionu wschodnie stany USA 2.

Ich opinię na temat ich środowiska hybrydowego, Contoso należy wziąć pod uwagę sposób tworzenia odporności i strategii odzyskiwania po awarii w swoich konstrukcjach regionu. Ogólnie strategii należeć do zakresu od wdrożenia jeden region, która opiera się na platformie Azure, funkcje, takie jak domen błędów i regionalnych parowanie o odporności danych, za pośrednictwem do pełnego aktywny-aktywny modelu, w których usługi w chmurze i bazy danych są wdrożone i obsługi Użytkownicy z dwóch regionach.

Contoso zdecydował się zająć środkowej drogowej. One będzie wdrażać aplikacje i zasoby w regionie podstawowym, a następnie zapewnić pełnej infrastrukturze w regionie pomocniczym, dzięki czemu jest gotowy do działania jako pełnej kopii zapasowej w przypadku awarii pełnej aplikacji lub niepowodzenie regionu.


## <a name="step-4-design-a-network-infrastructure"></a>Krok 4: Projektowanie infrastruktury sieciowej

Z projektowaniem regionu w miejscu Contoso jest gotowe do należy wziąć pod uwagę strategii sieci. Należy traktować jak ich lokalnego centrum danych i Azure połączenie i komunikować się ze sobą i sposobu projektowania infrastruktury sieci na platformie Azure. W szczególności należy:

**Planowanie łączności sieciowej hybrydowego**: dowiedzieć się, jak w przypadku przechodzi do łączenia sieci w lokalnym i platformą Azure.
**Projektowanie infrastruktury sieciowej Azure**: zdecydować, jak ich będziesz wdrażać sieci za pośrednictwem ich regionów. Sposobu komunikacji między sieciami w tym samym regionie, a w regionach.
**Projektowanie i konfigurowanie sieci platformy Azure**: Skonfiguruj sieci platformy Azure i podsieci i zdecyduj, co będzie znajdować się w nich.

### <a name="plan-hybrid-network-connectivity"></a>Planowanie łączności sieciowej hybrydowego

Contoso uznawane za [liczba architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) dla hybrydowych sieci platformy Azure i ich lokalnego centrum danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) o porównanie opcji.

Dla przypomnienia infrastruktury sieci lokalnej firmy Contoso obecnie składa się z swoje centra danych w Nowym Jorku, i lokalnej gałęzi w wschodni ze Stanów Zjednoczonych.  Wszystkie lokalizacje dysponują połączeniem klasy biznesowych z Internetem.  Każdy gałęzie, które następnie jest podłączony do centrum danych za pośrednictwem tunelu IPSec sieci VPN w Internecie.

![Sieci firmy Contoso](./media/contoso-migration-infrastructure/contoso-networking.png) 

Oto, jak będę Contoso do zaimplementowania połączenia hybrydowe:

1. Konfigurowanie nowego połączenia sieci VPN typu lokacja lokacja między centrum danych firmy Contoso w Nowym Jorku i dwóch regionach platformy Azure w wschodnie stany USA 2 i środkowe stany USA.
2. Ruch office gałęzi powiązany dla sieci wirtualnych platformy Azure będzie przekierowywać za pośrednictwem głównego centrum danych firmy Contoso. 
3. Zgodnie z ich skalowanie w górę ich wdrożenia usługi Azure, nawiązaniu połączenia ExpressRoute między ich centrum danych i regiony platformy Azure. W takim przypadku zachowują połączenia sieci VPN lokacja lokacja tylko na potrzeby trybu failover.
    - [Dowiedz się więcej](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) dotyczące wybierania pomiędzy hybrydowego sieci VPN i ExpressRoute.
    - Sprawdź [ExpressRoute lokalizacje i pomocy technicznej](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Tylko VPN**

![Sieci VPN firmy Contoso](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**Sieć VPN i ExpressRoute**

![Sieć VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Projektowanie infrastruktury sieci platformy Azure

Bardzo ważne, czy Contoso umieszcza sieci w miejscu, w sposób umożliwiający ich wdrożenia hybrydowego bezpieczne i skalowalne jest. Aby to zrobić, Contoso trwa długoterminową i projektowania sieci wirtualnych (sieci wirtualne), odporności i enterprise gotowe. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) o planowaniu sieci wirtualnych.

Aby połączyć ich dwóch regionach, Contoso podjęto decyzję o implementacji modelu koncentratora do Centrum sieci:

- W każdym regionie Contoso użyje modelu koncentrator i klienci.
- Aby połączyć sieci i koncentratory, Contoso użyje równorzędna sieć platformy Azure.

#### <a name="network-peering"></a>Sieć równorzędna

Platforma Azure udostępnia sieci komunikację równorzędną, aby połączyć sieci wirtualnych i koncentratory. Globalne równorzędna zezwala na połączenia między sieciami wirtualnymi/koncentratorów w różnych regionach. Równorzędna lokalnych łączy sieci wirtualnych w tym samym regionie. Sieci wirtualnej komunikacji równorzędnej zapewniają następujące korzyści:

- Ruch sieciowy między połączyć za pomocą sieci wirtualnych jest prywatny.
- Ruchu między sieciami wirtualnymi jest przechowywany w sieci firmy Microsoft w sieci szkieletowej. Nie publicznej sieci internet, bram lub szyfrowania jest wymagany w komunikacji między sieciami wirtualnymi.
- Równorzędna udostępnia domyślny, małym opóźnieniu i dużej przepustowości połączenia między zasobami w różnych sieci wirtualnych.

[Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o równorzędna sieci.

#### <a name="hub-to-hub-across-regions"></a>Koncentrator do koncentratora w regionach

Contoso wdroży Centrum w każdym regionie. Koncentrator jest sieć wirtualną (VNet) na platformie Azure, który działa jako centralny punkt łączności z siecią lokalną. Koncentrator sieci wirtualne będą łączyć ze sobą przy użyciu globalnej sieci wirtualnej komunikacji równorzędnej. Globalne sieci wirtualnej komunikacji równorzędnej łączy sieci wirtualnych w regionach platformy Azure.

- Centrum w każdym regionie jest połączyć za pomocą jego Centrum partnera w innym regionie.
- Koncentrator jest połączyć za pomocą do każdej sieci, w tym regionie i połączyć do wszystkich zasobów sieciowych.

    ![Globalne komunikacji równorzędnej](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Gwiazdy w obrębie regionu

W każdym regionie Contoso wdroży sieci wirtualnych do różnych celów, jako gwiazdy sieci w Centrum regionu. Sieci wirtualnych w obrębie regionu Użyj komunikacji równorzędnej, aby przejść do ich Centrum i ze sobą.

#### <a name="design-the-hub-network"></a>Projekt sieci Centrum

W ramach modelu gwiazdy, który wybrano firmy Contoso, trzeba myśleć o tym, jak ruchu z ich lokalnego centrum danych i z Internetu, zostaną przesłane. Oto, jak Contoso zdecydował się obsługiwać routing wschodnie stany USA 2 i koncentratory środkowe stany USA:

- Ich projektowania sieci znana jako "odwrotnej c", jak jest to ścieżka pakiety postępuj zgodnie z przychodzącego i wychodzącego sieci.
- Ich architektury sieci ma dwa granice, strefy niezaufanych obwodowej frontonu i zaplecza strefy zaufanej.
- Zapora ma karty sieciowej w każdej strefie kontrolowania dostępu do zaufanej strefy.
- Z Internetu:
    - Ruch internetowy nastąpi trafienie równoważeniem obciążenia publiczny adres IP w sieci obwodowej.
    - Ten ruch jest kierowany przez zaporę z uwzględnieniem reguł zapory.
    - Po zaimplementowaniu kontroli dostępu do sieci są ruchu zostaną przekazane do odpowiedniej lokalizacji w zaufanej strefy.
    - Ruch wychodzący z sieci wirtualnej będą kierowane do Internetu za pomocą trasy zdefiniowane przez użytkownika (Udr). Ruch jest wymuszone przez zaporę i kontrolowane obowiązującymi w firmie Contoso.
- Z centrum danych firmy Contoso:
    - Ruch przychodzący za pośrednictwem sieci VPN lokacja lokacja (lub ExpressRoute) trafień publiczny adres IP bramy sieci VPN platformy Azure.
    - Ruch jest kierowany przez zaporę i mogą ulec reguł zapory.
    - Po zastosowaniu reguły ruch jest kierowany do wewnętrznego modułu równoważenia obciążenia (standardowy SKU) w podsieci wewnętrznej strefy Zaufane.
    - Ruch wychodzący z zaufanych podsieci do lokalnego centrum danych za pośrednictwem połączenia VPN jest kierowany przez zapory i reguł stosowanych przed przejściem za pośrednictwem połączenia VPN lokacja lokacja.



### <a name="design-and-set-up-azure-networks"></a>Projektowanie i konfigurowanie sieci platformy Azure

Sieć i topologię routingu w miejscu Contoso przystąpić do konfigurowania ich sieci platformy Azure i podsieci.

- Contoso wdroży sieci prywatnej klasy A na platformie Azure (0.0.0.0 127.255.255.255). To działa, od momentu lokalnego bieżącego mają 172.160.0/16 miejsca prywatnych adresów klasy B, może być się, że nie będzie żadnych nakładania się zakresów adresów.
- Są one przewidzianym do wdrożenia sieci wirtualnych w swoich regionach podstawowego i pomocniczego.
- Służą one konwencji nazewnictwa, która zawiera prefiks **sieci Wirtualnej** i skrót region **EUS2** lub **CUS**. Używa tego standardu, sieci Centrum będzie miała nazwę **EUS2-VNET-Centrum** (wschodnie stany USA, 2), i **VNET-Centrum-CUS** (środkowe stany USA).
- Nie masz contoso [rozwiązania IPAM](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), dlatego należy zaplanować routingu w sieci bez translatora adresów sieciowych.


#### <a name="virtual-networks-in-east-us-2"></a>Sieci wirtualne w wschodnie stany USA 2

Wschodnie stany USA 2 jest regionu podstawowego, który firma Contoso będzie używany do wdrażania zasobów i usług. Oto, jak masz zamiar Architekt sieci w niej:

- **Centrum**: Centrum sieci wirtualnej w wschodnie stany USA 2 to punkt centralny podstawowej łączności ich lokalnego centrum danych.
- **Sieci wirtualne**: Gwiazda sieci wirtualnych w wschodnie stany USA 2 może służyć do izolują obciążenia, jeśli jest to wymagane. Oprócz Centrum sieci wirtualnej firma Contoso ma gwiazdy dwie sieci wirtualne w wschodnie stany USA 2:
    - **SIEĆ WIRTUALNA — DEWELOPERÓW EUS2**. Ta sieć wirtualna umożliwi opracowywania i zespół będzie funkcjonalnej sieci dla deweloperów projektów. Będzie pełnił rolę obszaru pilotażowego produkcji i będzie używana w infrastrukturze produkcji funkcji.
    - **Sieć wirtualna — produkcyjną-EUS2**: składniki produkcji IaaS platformy Azure będą znajdować się w tej sieci. 
    -  Każdej sieci wirtualnej ma własną przestrzeń unikatowy adres bez nakładania się. Użytkownik chce skonfigurować routing bez konieczności translatora adresów sieciowych.
- **Podsieci**:
    - W każdej sieci dla każdej warstwy aplikacji będzie można podsieci
    - Każda podsieć w sieci produkcyjnej będzie mieć pasującą podsieć w sieci wirtualnej deweloperów.
    - Ponadto w sieci produkcyjnej ma podsieci dla kontrolerów domeny.

W poniższej tabeli przedstawiono podsumowanie sieci wirtualnych w wschodnie stany USA 2.

**Sieć wirtualna** | **Range** | **elementów równorzędnych**
--- | --- | ---
**EUS2-VNET-CENTRUM** | 10.240.0.0/20 | CUS2-VNET-KONCENTRATORA, EUS2-VNET-DEWELOPERÓW, WIRTUALNE SIECI RÓWNORZĘDNE — PRODUKCYJNĄ EUS2
**EUS2-VNET-DEWELOPERÓW** | 10.245.16.0/20 | EUS2-VNET-CENTRUM
**SIEĆ WIRTUALNA — PRODUKCYJNĄ EUS2** | 10.245.32.0/20 | SIEĆ WIRTUALNA — CENTRUM EUS2 VNET-PRODUKCYJNĄ CUS

![Gwiazda w regionie podstawowym](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Podsieci w sieci wschód nam 2 koncentratora (VNET-Centrum EUS2)

**Podsieć/strefy** | **CIDR** | ** Można używać adresów IP
--- | --- | ---
**IB UntrustZone** | 10.240.0.0/24 | 251
**IB TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Podsieci w sieci wschód nam 2 deweloperów (VNET-deweloperów-EUS2)

Tworzenie sieci wirtualnej jest używany przez zespół deweloperów jako obszar pilotażowego produkcji. Składa się z trzech podsieci.

**Podsieć** | **CIDR** | **adresy** | **W podsieci**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Frontends/warstwa sieci web maszyny wirtualne
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Maszyny wirtualne z bazy danych


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Podsieci w sieci wschód nam 2 produkcji (VNET-produkcyjną EUS2)

Składniki IaaS platformy Azure znajdują się w sieci produkcyjnej. Każda warstwa aplikacji ma własny podsieci. Podsieci odpowiadają sieci deweloperów, z uwzględnieniem podsieci dla kontrolerów domeny.

**Podsieć** | **CIDR** | **adresy** | **W podsieci**
--- | --- | --- | ---
**ZLECENIE FE-EUS2** | 10.245.32.0/22 | 1019 | Frontends/warstwa sieci web maszyny wirtualne
**ZLECENIE APP-EUS2** | 10.245.36.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**ZLECENIE DB-EUS2** | 10.245.40.0/23 | 507 | Maszyny wirtualne z bazy danych
**ZLECENIE DC-EUS2** | 10.245.42.0/23 | 251 | Kontroler domeny maszyny wirtualne


![Architektura sieci Centrum](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>Sieci wirtualne w środkowe stany USA (region pomocniczy)

Środkowe stany USA, to region pomocniczy firmy Contoso. Oto, jak masz zamiar Architekt sieci w niej:

- **Centrum**: Centrum sieci wirtualnej w wschodnie stany USA 2 jest punkt centralny połączenie ich w lokalnym centrum danych, a klienci sieci wirtualnych w wschodnie stany USA 2 może służyć do izolowania obciążeń w razie potrzeby zarządzana oddzielnie od innych partnerów.
- **Sieci wirtualne**: mają one dwie sieci wirtualne w środkowe stany USA:
    - CUS-VNET-PRODUKCYJNĄ. Ta sieć wirtualna jest w sieci produkcyjnej, podobnie jak PROD_EUS2 sieci Wirtualnej. 
    - SIEĆ WIRTUALNA — FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU CUS. Ta sieć wirtualna będzie działać jako lokalizację, w której maszyny wirtualne są tworzone po przejściu w tryb failover na lokalnym lub jako lokalizacji dla maszyn wirtualnych platformy Azure, która failover z serwera podstawowego w regionie pomocniczym. Ta sieć jest podobny do sieci produkcyjnej, ale bez żadnych kontrolerów domeny na nim.
    -  Każda sieć wirtualną w regionie ma własnej przestrzeni adresowej bez nakładania się. Użytkownik chce skonfigurować routing bez konieczności translatora adresów sieciowych.
- **Podsieci**: podsieci zostanie zaprojektowana w podobny sposób jak te w wschodnie stany USA 2. Wyjątek jest, że niepotrzebne podsieci dla kontrolerów domeny.

W poniższej tabeli przedstawiono podsumowanie sieci wirtualnych w środkowe stany USA.

**Sieć wirtualna** | **Range** | **elementów równorzędnych**
--- | --- | ---
**SIEĆ WIRTUALNA — CENTRUM CUS** | 10.250.0.0/20 | EUS2-VNET-KONCENTRATORA, WIRTUALNE SIECI RÓWNORZĘDNE — FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU CUS, CUS-VNET-PRODUKCYJNĄ
**SIEĆ WIRTUALNA — FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU CUS** | 10.255.16.0/20 | SIEĆ WIRTUALNA — CENTRUM CUS CUS-VNET-PRODUKCYJNĄ
**SIEĆ WIRTUALNA — PRODUKCYJNĄ CUS** | 10.255.32.0/20 | CUS-VNET-KONCENTRATORA, WIRTUALNE SIECI RÓWNORZĘDNE — FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU CUS, EUS2-VNET-PRODUKCYJNĄ  


![Gwiazda w regionie sparowanego](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>Podsieci w sieci centralnej nam koncentratora (VNET-Centrum CUS)

**Podsieć** | **CIDR** | **Można używać adresów IP**
--- | --- | ---
**IB UntrustZone** | 10.250.0.0/24 | 251
**IB TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>Podsieci w sieci centralnej nam produkcji (VNET-produkcyjną CUS)

Równolegle produkcyjnego środowiska sieciowego w regionie podstawowym wschodnie stany USA 2 istnieje środowiska produkcyjnego w regionie pomocniczym środkowe stany USA. 

**Podsieć** | **CIDR** | **adresy** | **W podsieci**
--- | --- | --- | ---
**ZLECENIE FE-CUS** | 10.255.32.0/22 | 1019 | Frontends/warstwa sieci web maszyny wirtualne
**ZLECENIE APP-CUS** | 10.255.36.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**ZLECENIE DB-CUS** | 10.255.40.0/23 | 507 | Maszyny wirtualne z bazy danych
**ZLECENIE DC-CUS** | 10.255.42.0/24 | 251 | Kontroler domeny maszyny wirtualne

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Podsieci w sieci/odzyskiwanie środkowe stany USA w środkowe stany USA (VNET-ASR CUS)

Sieci wirtualne sieci Równorzędne — funkcja automatycznego odzyskiwania systemu-CUS jest używany na potrzeby trybu failover między regionami. Replikację i tryb failover maszyny wirtualne Azure między regionami będzie można użyć usługi Site Recovery. Także działa jako centrum danych firmy Contoso do sieci platformy Azure dla chronionych obciążeń, które zachowują lokalnie, ale Failover do platformy Azure dla odzyskiwania po awarii.

Sieć wirtualna — funkcja automatycznego odzyskiwania systemu-CUS jest tej samej podsieci podstawowego jako produkcyjny sieci Wirtualnej w wschodnie stany USA 2, ale bez konieczności podsieci kontrolera domeny.

**Podsieć** | **CIDR** | **adresy** | **W podsieci**
--- | --- | --- | ---
**FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU FE-CUS** | 10.255.16.0/22 | 1019 | Frontends/warstwa sieci web maszyny wirtualne
**FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU APP-CUS** | 10.255.20.0/22 | 1019 | Maszyny wirtualne warstwy aplikacji
**FUNKCJA AUTOMATYCZNEGO ODZYSKIWANIA SYSTEMU DB-CUS** | 10.255.24.0/23 | 507 | Maszyny wirtualne z bazy danych

![Architektura sieci Centrum](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Konfigurowanie połączyć za pomocą połączeń

Będzie można połączyć za pomocą Centrum w każdym regionie do koncentratora w innym regionie i do wszystkich sieci wirtualnych w obrębie regionu koncentratora. Dzięki temu centra komunikację i wyświetlić wszystkie sieci wirtualne w obrębie regionu. Należy pamiętać, że:

- Komunikacja równorzędna tworzy połączenie dwustronnego. Z komputer inicjujący w pierwszej sieci wirtualnej, a inny w drugiej sieci wirtualnej.
- W ramach wdrożenia hybrydowego ruchu, który przechodzi między komputerami równorzędnymi musi być widoczny z połączenia sieci VPN między lokalnymi centrum danych i usługi Azure. Aby je włączyć, brak niektórych konkretnych ustawień, które należy ustawić połączyć za pomocą połączenia.

Dla połączeń z gwiazdy sieci wirtualnych za pośrednictwem Centrum w lokalnym centrum danych Contoso musi zezwalać na ruch przekazany dalej i przechodzącego bramy sieci VPN.

##### <a name="domain-controller"></a>Kontroler domeny

Kontrolerów domeny w sieci VNET-produkcyjną-EUS2 firma Contoso chce na przepływ zarówno między EUS2 Centrum/produkcyjnego środowiska sieciowego, jak i za pośrednictwem połączenia VPN do lokalnego. W tym celu muszą Zezwalaj na następujące:

1. **Zezwalaj przekazywane ruchu** i **Zezwalaj bramy przesyłania konfiguracje** peered połączenia. W tym przykładzie będzie to sieci Wirtualnej EUS2 KONCENTRATORA połączenia EUS2-VNET-produkcyjną.

    ![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering1.png)

2. **Zezwalaj na związanego z przekazywaniem ruchu** i **użycie bram zdalnego** po drugiej stronie komunikacji równorzędnej, w sieci Wirtualnej EUS2 produkcyjną EUS2-VNET-KONCENTRATORA połączenia.

    ![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering2.png)

3. Lokalna usługa zostanie skonfigurowany tras statycznych, który kieruje ruch lokalny do rozsyłania przez tunel sieci VPN do sieci wirtualnej. Konfiguracji można wykonać na bramy, która zapewnia tunelu VPN z firmy Contoso na platformie Azure. Firma Contoso używa Windows Routing i dostęp zdalny to.

    ![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Sieciach produkcyjnych 

Sieci spoked peer nie widzi sieci peer spoked w innym regionie za pośrednictwem koncentratora.

Dla sieci produkcyjnych firmy Contoso w obu regionach, aby zobaczyć siebie nawzajem muszą utworzyć połączenie bezpośrednie peered EUS2-VNET-produkcyjną i zdarzenie-produkcyjną-CUS. 

![Komunikacja równorzędna](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Skonfigurować usługę DNS

Podczas wdrażania zasobów w sieciach wirtualnych, masz kilka opcji w celu rozpoznania nazwy domeny. Użyj rozpoznawania nazw dostarczany przez platformę Azure, lub podaj serwery DNS dla rozpoznawania. Rodzaj rozpoznawania nazw, których używasz zależy od tego, jak zasoby muszą komunikować się ze sobą. Pobierz [więcej informacji](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) o usłudze Azure DNS.

Contoso zdecydował, że usługa Azure DNS nie jest dobrym rozwiązaniem w środowisku hybrydowym. Zamiast tego one zacząć korzystać z ich lokalnych serwerów DNS.

- Ponieważ jest to sieć hybrydowa wszystkich maszyn wirtualnych lokalnie i na platformie Azure muszą mieć możliwość rozpoznania nazwy do poprawnego działania. Oznacza to, że ustawienia niestandardowe DNS należy zastosować do wszystkich sieci wirtualnych.
- Firma Contoso ma obecnie kontrolery domeny wdrożone w centrum danych firmy Contoso, a w biurach oddziałów. Ich podstawowych serwerów DNS są CONTOSODC1(172.16.0.10) i CONTOSODC2(172.16.0.1)
- Po wdrożeniu sieci wirtualne są kontrolery domeny lokalnej ustawi ma być używany jako serwer DNS w sieci. 
- Przy użyciu niestandardowego serwera DNS w sieci wirtualnej, należy skonfigurować, to, adres IP platformy Azure cyklicznego rozpoznawania nazw (na przykład 168.63.129.16) należy dodać do listy DNS.  W tym celu firma Contoso konfiguruje ustawienia serwera DNS w każdej sieci wirtualnej. Na przykład niestandardowych ustawień DNS dla sieci VNET-Centrum-EUS2 będzie następujące:
    
    ![Niestandardowe DNS](./media/contoso-migration-infrastructure/custom-dns.png)

Oprócz kontrolerów domen lokalnymi Contoso mają zaimplementować cztery dodatkowe do obsługi ich sieci platformy Azure, dwa w poszczególnych regionach. Oto, co wdrożony na platformie Azure.

**Region** | **KONTROLER DOMENY** | **Sieć wirtualna** | **Podsieć** | **Adres IP**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | SIEĆ WIRTUALNA — PRODUKCYJNĄ EUS2 | ZLECENIE DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | SIEĆ WIRTUALNA — PRODUKCYJNĄ EUS2 | ZLECENIE DC-EUS2 | 10.245.42.5
CUS | CONTOSODC5 | SIEĆ WIRTUALNA — PRODUKCYJNĄ CUS | ZLECENIE DC-CUS | 10.255.42.4
CUS | CONTOSODC6 | SIEĆ WIRTUALNA — PRODUKCYJNĄ CUS | ZLECENIE DC-CUS | 10.255.42.4

Po wdrożeniu kontrolerów domeny lokalnej, Contoso należy zaktualizować ustawienia DNS w sieci w każdym regionie uwzględnienie nowych kontrolerów domeny w ich lista serwerów DNS.



#### <a name="set-up-domain-controllers-in-azure"></a>Konfigurowanie kontrolerów domeny w systemie Azure

Po zaktualizowaniu ustawień sieciowych, Contoso przystąpić do tworzenia kontrolerów domen na platformie Azure.

1. W portalu Azure ich wdrażania nowej maszyny Wirtualnej systemu Windows Server do odpowiedniej sieci wirtualnej.
2. Tworzenia zestawów dostępności w każdej lokalizacji dla maszyny Wirtualnej. Zestawy dostępności wykonaj następujące czynności:
    - Upewnij się, że sieci szkieletowej Azure oddziela maszyn wirtualnych w różnych infrastruktur w regionie Azure. 
    -  Umożliwia firmy Contoso na kwalifikować się do umowy SLA 99,95% dla maszyn wirtualnych na platformie Azure.  [Dowiedz się więcej](https://docs.microsoftcom/azure/virtual-machines/windows/regions-and-availability#availability-sets).

    ![Grupy dostępności](./media/contoso-migration-infrastructure/availability-group.png) 
3. Po wdrożeniu maszyny Wirtualnej one pióra interfejsu sieciowego dla maszyny Wirtualnej. W tym miejscu sami ustawiają prywatnego adresu IP adresów jako statyczny i podaj prawidłowy adres.

    ![MASZYNA WIRTUALNA KARTA SIECIOWA](./media/contoso-migration-infrastructure/vm-nic.png)

4. Teraz ich dołączyć nowego dysku danych do maszyny Wirtualnej. Ten dysk zawiera bazy danych usługi Active Directory i udziału sysvol. 
    - Rozmiar dysku określi liczbę IOPS, który go obsługuje.
    - Wraz z upływem czasu rozmiar dysku może być konieczne zwiększyć wraz z rozwojem środowiska.
    - Dysk nie należy ustawiać odczytu/zapisu dla buforowania na hoście. Active Directory baz danych nie obsługuje to.

     ![Active Directory dysku](./media/contoso-migration-infrastructure/ad-disk.png)

5. Po dodaniu dysku będzie łączyć się z maszyną Wirtualną za pośrednictwem pulpitu zdalnego i otwórz Menedżera serwera.
6. Następnie w **usług plików i magazynowania**, uruchomieniu Kreatora nowych woluminów zapewnia, że dysk jest podany litery F: lub nowszym na lokalnej maszynie Wirtualnej.

     ![Kreator nowego woluminu](./media/contoso-migration-infrastructure/volume-wizard.png)

7. W Menedżerze serwera, co zwiększa **usług domenowych w usłudze Active Directory** roli. Następnie skonfiguruj ich maszynę Wirtualną jako kontroler domeny.

      ![Rola serwera](./media/contoso-migration-infrastructure/server-role.png)  

9. Po maszyny Wirtualnej jest skonfigurowany jako kontroler domeny i ponownym uruchomieniu, otwórz Menedżera DNS i skonfigurować program rozpoznawania nazw usługi Azure DNS jako usługi przesyłania dalej.  Dzięki temu kontroler domeny do przesyłania zapytań DNS, których nie można rozpoznać w usłudze Azure DNS.

    ![Usługa przesyłania dalej DNS](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Teraz następnie zaktualizuj niestandardowych ustawień DNS dla każdej sieci wirtualnej z kontrolerem domeny odpowiednie dla regionu sieci wirtualnej. Obejmują one lokalnymi kontrolerów domeny na liście.

### <a name="set-up-active-directory"></a>Konfigurowanie usługi Active Directory

Usługi AD jest usługą krytyczne w sieci i muszą być prawidłowo skonfigurowane. Firma Contoso będzie tworzenie witryn AD dla centrum danych firmy Contoso i regiony EUS2 i CUS.  

1. Ich tworzyć dwa nowe witryny (AZURE EUS2 oraz AZURE CUS) wraz z witryny Centrum danych (ContosoDatacenter).
2. Po utworzeniu witryny, ich utworzyć podsieci w witrynach, aby były zgodne z sieciami wirtualnymi i centrum danych.

    ![Podsieci kontrolera domeny](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Następnie utwórz dwa łącza lokacji nawiązać wszystko. Kontrolery domeny następnie powinna zostać przeniesiona do ich lokalizacji.

    ![Łącza kontrolera domeny](./media/contoso-migration-infrastructure/dc-links.png)

4. Po skonfigurowaniu wszystkich topologii replikacji usługi Active Directory jest na miejscu.
    
    ![Replikacji kontrolera domeny](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Z pełną wszystko listę kontrolerów domeny i lokacji są wyświetlane w Centrum administracyjne usługi lokalnej Active Directory.

    ![Centrum administracyjne usługi AD](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>Krok 5: Planowanie ładu

Platforma Azure udostępnia szereg kontroli ładu przez usługi i platformą Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/security/governance-in-azure) dla podstawową wiedzę na temat opcji.

Konfigurowanie tożsamości i kontroli dostępu, Contoso już rozpoczęto do wprowadzenia niektóre aspekty zarządzania i zabezpieczeń. Ogólnie rzecz biorąc istnieją trzy obszary, które należy wziąć pod uwagę:

- **Zasady**: zasad w usłudze Azure stosuje i wymusza zasady i wpływ na zasoby, tak, aby zasoby zgodne z wymaganiami firmy i umowy SLA.
- **Blokuje**: Azure umożliwia blokady subskrypcji, grupy zasobów i innych zasobów, tak aby ich może zostać zmodyfikowany tylko przez osoby z urzędem, aby to zrobić.
- **Tagi**: zasobów może być kontrolowane, inspekcji i zarządzanych za pomocą tagów. Tagi dołączyć metadanych do zasobów, udostępniać informacje dotyczące zasobów lub właścicieli.

### <a name="set-up-policies"></a>Ustawianie zasad

Usługa Azure zasad oblicza zasobami skanowania dla tych, które nie są zgodne z definicjami zasad, które zostały spełnione. Na przykład może być zasadę tylko umożliwia określonego typu maszyn wirtualnych lub wymaga zasobów do określonego tagu. 

Zasady usługi Azure Określ definicję zasad, a przypisania zasad określ zakres, w którym można zastosować zasad. Zakres może należeć do zakresu od grupy zarządzania do grupy zasobów. [Dowiedz się](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) dotyczących tworzenia i zarządzania zasadami.

Firma Contoso chce wprowadzenie do kilku zasad:

- Mają one zasad, aby upewnić się, że zasoby mogą być wdrażane tylko w regionach EUS2 i CUS.
- Chce ograniczyć jednostki SKU maszyny Wirtualnej można tylko zatwierdzone jednostki SKU. Jest zapewnienie, że kosztowne SKU maszyny Wirtualnej nie są używane.

#### <a name="limit-resources-to-regions"></a>Limit zasobów do regionów

Firmy Contoso, użyj definicji zasad wbudowany **dozwolone lokalizacje** do ograniczania zasobów regionów.

1. W portalu Azure kliknij **wszystkie usługi**i wyszukaj **zasad**.
2. Wybierz **przypisania** > **przypisać zasady**.
3. Na liście zasad wybierz **dozwolone lokalizacje**.
4. Ustaw **zakres** nazwę subskrypcji platformy Azure i wybierz dwóch regionach na liście dozwolonych.

    ![Zasady mogą regionów](./media/contoso-migration-infrastructure/policy-region.png)

5. Domyślnie zasady zostały ustawione z **Odmów**, co oznacza, że jeśli ktoś uruchamia wdrożenie w ramach subskrypcji, która nie jest EUS2 lub CUS, wdrożenie zakończy się niepowodzeniem. Oto, co się stanie, jeśli ktoś w subskrypcji Contoso spróbuje skonfigurować wdrożenie w zachodnie stany USA.

    ![Zasad nie powiodło się](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Zezwalaj na określonej jednostki SKU maszyny Wirtualnej

Contoso użyje definicji zasad wbudowany **umożliwić maszynom wirtualnym jednostki SKU** ograniczenie typu maszyn wirtualnych, które mogą zostać utworzone w ramach subskrypcji. 

![Zasady jednostki SKU](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Sprawdzanie zasad zgodności

Zasady zaczynają obowiązywać natychmiast, a Contoso można sprawdzić zasoby dotyczące zgodności. 

1. W portalu Azure kliknij **zgodności** łącza.
2. Zostanie wyświetlony pulpit nawigacyjny zgodności. Użytkownik może przejść do szczegółów dalszych szczegółów.

    ![Zgodność zasad](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Ustawienie blokady

Contoso długo korzysta ITIL framework zarządzania systemów. Jest jednym z najważniejszych aspektów platformę, Zmień formant i Contoso chce upewnić się, że zmiana kontroli jest zaimplementowana w ich wdrożenia usługi Azure.

Contoso mają zaimplementować blokad w następujący sposób:

- Każdy produkcji lub składnik trybu failover musi być w grupie zasobów, która blokuje tylko do odczytu.  Oznacza to, że aby zmodyfikować lub usunąć elementy produkcji, muszą zostać usunięte blokady. 
- Grup zasobów w środowisku produkcyjnym nie ma blokad CanNotDelete. Oznacza to, że autoryzowany użytkownicy mogą odczytywać lub zmodyfikować zasobu, ale nie można go usunąć.

[Dowiedz się więcej](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) o blokad.

### <a name="set-up-tagging"></a>Konfigurowanie znakowanie

Aby śledzić zasobów, gdy są one dodawane, będzie coraz bardziej ważne w przypadku firmy Contoso skojarzyć zasoby z odpowiedniego działu, klientów i środowiska. 

Oprócz informacji o zasobach i właścicieli tagi umożliwi Contoso do agregacji i grupy zasobów i użyć tych danych do celów obciążenia zwrotnego.

Contoso konieczne Wizualizuj swoje zasoby platformy Azure w taki sposób, który ma sens dla firmy. Na przykład, ale roli lub działu. Należy pamiętać, że zasoby nie muszą znajdować się w tej samej grupie zasobów, aby udostępnić tag. Aby to zrobić, Contoso utworzy taksonomii prostych tagów, aby wszyscy używają tych samych znaczników.

**Nazwa tagu** | **Wartość**
--- | ---
Centrum kosztów | 12345: musi być centrum kosztów prawidłowe z SAP.
Jednostką biznesową | Nazwa jednostki biznesowej (od SAP). CostCenter dopasowań.
ApplicationTeam | Alias e-mail zespół, który jest właścicielem pomocy technicznej dla aplikacji.
CatalogName | Nazwa aplikacji lub ShareServices, w katalogu usług, który obsługuje zasobu.
Programu Service Manager | Alias e-mail programu ITIL Service Manager dla zasobu.
COBPriority | Priorytet ustawiony przez firmę dla BCDR. Wartości od 1 do 5.
ENV | Możliwe wartości są deweloperów, STG, produkcyjną. Reprezentujący tworzenie tymczasowych i produkcyjnych.

Na przykład: 

 ![Tagi Azure](./media/contoso-migration-infrastructure/azure-tag.png)

Po utworzeniu tagu, Contoso Przejdź wstecz i utworzyć nowe definicje zasad Azure i przypisania, aby wymusić użycie tagów wymagane w całej organizacji.


## <a name="step-6-consider-security"></a>Krok 6: Rozważ zabezpieczeń

Zabezpieczenia są niezwykle istotne w chmurze i platforma Azure oferuje szeroką gamę narzędzi zabezpieczeń i możliwości. Te ułatwiają tworzenie bezpiecznych rozwiązań na bezpiecznej platformie Azure. Odczyt [zaufania w chmurze zaufanych](https://azure.microsoft.com/overview/trusted-cloud/) Aby dowiedzieć się więcej na temat zabezpieczeń platformy Azure.

Istnieje kilka aspektach "contoso", które należy uwzględnić

- **Centrum zabezpieczeń Azure**: Centrum zabezpieczeń Azure umożliwia zarządzanie ujednoliconego zabezpieczeń i ochrony przed zagrożeniami zaawansowane między obciążeń chmury hybrydowej. Usługa Security Center umożliwia stosowanie zasad zabezpieczeń do różnych obciążeń, ograniczanie podatności na zagrożenia i wykrywanie ataków oraz reagowanie na nie.  [Dowiedz się więcej](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Sieciowe grupy zabezpieczeń (NSG)**: Grupa NSG jest filtr (zapory), który zawiera listę zabezpieczeń reguły, po zastosowaniu akceptować lub odrzucać ruch sieciowy do zasoby podłączone do sieci wirtualnych platformy Azure. [Dowiedz się więcej](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Szyfrowanie danych**: szyfrowania dysków Azure jest możliwość, która pomaga szyfrowania dysków maszyny wirtualnej systemu Windows i Linux IaaS. [Dowiedz się więcej](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Praca z Centrum zabezpieczeń Azure

Contoso szuka zapewnia szybki wgląd w stan zabezpieczeń ich nowych chmur hybrydowych i specjalnie ich Azure obciążeń.  W związku z tym Contoso podjęto decyzję o implementacji Centrum zabezpieczeń Azure, począwszy od następujących funkcji: 

- Scentralizowane zarządzanie zasadami
- Ciągłej oceny
- Zalecenia z możliwością wykonywania akcji 

#### <a name="centralize-policy-management"></a>Scentralizowane zarządzanie zasadami

Z scentralizowane zarządzanie Contoso zapewnia zgodność z wymagań dotyczących zabezpieczeń przez centralne zarządzanie zasadami zabezpieczeń między całego środowiska. Ich można szybko i łatwo zaimplementować zasady, która ma zastosowanie do wszystkich ich zasobów platformy Azure.

![Zasady zabezpieczeń](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Ocena i akcji

Firma Contoso będzie korzystać z oceny ciągłego zabezpieczeń, która monitoruje zabezpieczeń maszyny, sieci, magazynu, danych i aplikacji; Aby odnaleźć potencjalne problemy. 

- Centrum zabezpieczeń będzie analizować stan zabezpieczeń firmy Contoso, infrastruktury, zasoby obliczeniowe i danych oraz usługi i aplikacje platformy Azure.
- Ciągłej oceny pomaga zespołowi Contoso operacje odnajdywania potencjalnych problemów z zabezpieczeniami, takich jak systemy z brakujących aktualizacji zabezpieczeń lub udostępniane porty sieciowe. 
- W szczególności firma Contoso chce upewnij się, że wszystkie ich maszyny wirtualne są chronione. Centrum zabezpieczeń pomaga o tym, sprawdzania kondycji maszyny Wirtualnej i zalecenia priorytetową i możliwością skorygować luk w zabezpieczeniach, są one wykorzystać.

![Monitorowanie](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Praca z grup NSG

Contoso ograniczyć ruch sieciowy do zasobów w sieci wirtualnej przy użyciu grup zabezpieczeń sieci.

- Grupa zabezpieczeń sieci zawiera listę reguł zabezpieczeń, które zezwalają na lub blokują przychodzący lub wychodzący ruch sieciowy na podstawie źródłowego lub docelowego adresu IP, portu i protokołu.
- Po zastosowaniu do podsieci, reguły są stosowane do wszystkich zasobów w podsieci. Oprócz interfejsów sieciowych w tym wystąpienia usługi Azure wdrożonych w tej podsieci.
- Grupy zabezpieczeń aplikacji (ASGs) umożliwiają konfigurowanie zabezpieczeń sieciowych jako stanowi naturalne rozszerzenie struktury aplikacji, co pozwoli na grupy maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup.
    - Grupy zabezpieczeń aplikacji oznaczają, których można używać z zasadami zabezpieczeń na dużą skalę, bez ręcznej obsługi jawne adresów IP. Platforma obsługuje złożoność jawnych adresów IP i wiele zestawów reguł, co pozwala skupić się na logice biznesowej.
    - Grupę zabezpieczeń aplikacji można określić jako źródło i obiekt docelowy reguły zabezpieczeń. Po zdefiniowaniu zasad zabezpieczeń można utworzyć maszyny wirtualne i przypisywanie kart sieciowych maszyny Wirtualnej do grupy. 


Contoso wdroży kombinacji grup NSG i ASGs. Są one danych o zarządzaniu NSG. One Obawiamy się o zbyt częste używanie grup NSG i złożoności, który może oznaczać służbowych operacji.  Pamiętając o tym przyjętych dwóch głównych klucza użycia ogólną zasadą:

- Cały ruch do i z wszystkich podsieci (północ południe), będą podlegać reguły NSG, z wyjątkiem GatewaySubnets w sieciach koncentratora.
- Wszelkie zapory lub kontroler domeny będą chronione przez grupy NSG podsieci i karty Sieciowej grupy zabezpieczeń sieci.
- Wszystkie aplikacje produkcyjnego będą mieć ASGs zastosowane.


Contoso ma wbudowany model to wygląd dla swoich aplikacji.

![Bezpieczeństwo](./media/contoso-migration-infrastructure/asg.png)


Grupy zabezpieczeń sieci skojarzonych z ASGs zostaną skonfigurowane najniższych uprawnień, aby upewnić się, że dozwolone tylko pakiety może przepływać z jednej części sieci do miejsca docelowego.

**Akcja** | **Nazwa** | **Źródło** | **docelowy** | **Port**
--- | --- | --- | --- | --- 
Zezwalaj | AllowiInternetToFE | Sieć wirtualna — Centrum EUS1/IB-TrustZone | APP1-FE 80, 443
Zezwalaj | AllowWebToApp | APP1 FE | BAZY DANYCH NA KOMPUTERZE APP1 | 1433
Zezwalaj | AllowAppToDB | APLIKACJA APP1 | Dowolne | Dowolne
Zablokuj | DenyAllInbound | Dowolne | Dowolne | Dowolne

### <a name="encrypt-data"></a>Szyfrowanie danych

Szyfrowanie dysków Azure integruje się z usługą Azure Key Vault, aby kontrolować i zarządzać nimi klucze szyfrowania dysku i kluczy tajnych w magazynie kluczy subskrypcji. Gwarantuje, że wszystkie dane na dyskach maszyny Wirtualnej są szyfrowane, gdy w magazynie Azure.  

- Contoso stwierdził, że określone maszyn wirtualnych wymaga szyfrowania.
- One zacząć zastosowanie szyfrowania do maszyn wirtualnych z klientem, poufne, lub PPI danych.


## <a name="conclusion"></a>Podsumowanie

W tym artykule Contoso Konfigurowanie infrastruktury platformy Azure i skonfigurować lub planowanych infrastruktury zasady dla subskrypcji platformy Azure, zidentyfikować hybrydowych, odzyskiwania po awarii, sieci, zarządzania i zabezpieczeń. 

Nie wszystkie czynności, jakie Contoso wykonać w tym miejscu są wymagane do przeprowadzenia migracji do chmury. W ich przypadku miało być planowania infrastruktury sieci, które mogą służyć do wszystkich typów migracji, a jest bezpieczne, elastyczne i skalowalne. 

Z tej infrastruktury w miejscu są one gotowe do przeniesienia i wypróbować migracji.

## <a name="next-steps"></a>Kolejne kroki

Jako pierwszego scenariusza migracji Contoso mają migracji ich lokalnych SmartHotel dwuwarstwowej aplikacji działające na maszynach wirtualnych VMware do platformy Azure. Będzie migrowania maszyn wirtualnych do aplikacji na maszynach wirtualnych platformy Azure i bazę danych aplikacji na wystąpienie zarządzane SQL Azure.
