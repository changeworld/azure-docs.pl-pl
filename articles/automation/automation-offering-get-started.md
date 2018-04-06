---
title: Rozpocznij pracę z usługą Azure Automation
description: Ten artykuł zawiera omówienie usługi Automatyzacja Azure. Zapoznaje się projekt i implementację szczegółów w ramach przygotowania do dołączenia do oferty w witrynie Azure Marketplace.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2fcbbc2532e5cb9963922b4987ba0c7080fdb170
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-azure-automation"></a>Rozpocznij pracę z usługą Azure Automation

W tym artykule przedstawiono podstawowe pojęcia związane z wdrażaniem usługi Automatyzacja Azure. Jeśli dopiero zaczynasz korzystać z automatyzacji platformy Azure lub nie mają doświadczenia w automatyzacji oprogramowanie przepływu pracy, takie jak System Center Orchestrator nauczysz się, jak przygotować i dołączyć automatyzacji. Po przeczytaniu tego artykułu, będzie gotowy do rozpoczęcia tworzenia elementów runbook do różnych potrzeb automatyzacji procesu. 


## <a name="automation-architecture-overview"></a>Omówienie architektury usługi Automation

![Omówienie usługi Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Automatyzacja Azure to oprogramowanie jako usługa (SaaS) aplikacja, która zapewnia skalowalne i niezawodne środowisku wielodostępnym w którym można użyć elementów runbook do automatyzacji procesów. Można użyć konfiguracji żądanego stanu (DSC) na platformie Azure i innych usług w chmurze lub w lokalnej środowiska do zarządzania konfiguracją zmiany w systemach Windows i Linux. Jednostki w Twoje konto usługi Automatyzacja, w tym elementów runbook, zasoby i konta Uruchom jako są izolowane od innych kont automatyzacji w Twojej subskrypcji oraz inne subskrypcje.  

Elementy Runbook, które są uruchamiane na platformie Azure są wykonywane na piaskownic automatyzacji. Piaskownic są hostowane na platformie Azure jako maszyny wirtualne usługa (PaaS). 

Automatyzacja piaskownic Podaj izolacji dzierżawców za wszystkie aspekty wykonanie elementu runbook, łącznie z modułami, magazynu, pamięci, komunikacji sieciowej i strumieni zadania. Ta rola jest zarządzane przez usługę. Nie można uzyskać dostępu lub Zarządzanie rolą z Twojego konta Azure lub automatyzacji.         

Aby zautomatyzować wdrażania i zarządzania zasobami w lokalnym centrum danych lub innych usług w chmurze, po utworzeniu konta automatyzacji, możesz wyznaczyć przynajmniej jednej maszyny wirtualnej, aby uruchomić [hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md) roli. Każdy hybrydowy proces roboczy elementu Runbook wymaga instalacji agenta zarządzania Microsoft i konto usługi Automatyzacja. Agent musi mieć połączenie z obszarem roboczym usługi Analiza dzienników Azure. Log Analytics umożliwia bootstrap instalację, obsługa programu Microsoft Management Agent oraz monitorowanie funkcji hybrydowego procesu roboczego elementu Runbook. Automatyzacja Azure wykonuje dostarczania elementów runbook i instrukcji, aby uruchomić je.

Można wdrożyć wiele hybrydowych procesów roboczych elementu Runbook. Użyj hybrydowych procesów roboczych elementu Runbook, aby zapewnić wysoką dostępność dla elementów runbook i równoważenie obciążenia zadania elementu runbook. W niektórych przypadkach można przypisać zadania elementu runbook dla konkretnych obciążeń lub środowisk. Microsoft Monitoring Agent na hybrydowy proces roboczy elementu Runbook inicjuje komunikację z usługą automatyzacji za pośrednictwem portu TCP 443. Hybrydowe procesy robocze elementu Runbook nie mają żadnych wymagań zapory dla ruchu przychodzącego.  

Może być elementu runbook, który działa na hybrydowy proces roboczy elementu Runbook do wykonywania zadań zarządzania dla innych maszyny lub usługi w danym środowisku. W tym scenariuszu element runbook może być konieczne również inne porty dostępu. Jeśli zasady zabezpieczeń IT nie zezwalaj na komputerach w sieci, aby nawiązać połączenie z Internetem, przejrzyj [bramy OMS](../log-analytics/log-analytics-oms-gateway.md). Brama OMS działa jako serwer proxy dla hybrydowego procesu roboczego elementu Runbook. Zbiera stan zadania, a otrzymuje informacje o konfiguracji z Twojego konta automatyzacji.

Elementy Runbook, działające na hybrydowy proces roboczy elementu Runbook działają w kontekście konta systemu lokalnego na komputerze. Firma Microsoft zaleca kontekstu zabezpieczeń podczas wykonywania zadań administracyjnych na komputerze z systemem Windows. Jeśli chcesz, aby element runbook, aby uruchamiać zadania na zasoby, które są poza komputera lokalnego, może być konieczne zdefiniowanie trwałych bezpiecznych poświadczeń na koncie automatyzacji. Możesz uzyskiwać dostęp do zasobów bezpiecznych poświadczeń z elementu runbook i ich używać do uwierzytelniania dla zasobu zewnętrznego. Można użyć [poświadczeń](automation-credentials.md), [certyfikatu](automation-certificates.md), i [połączenia](automation-connections.md) zasoby w elemencie runbook. Za pomocą polecenia cmdlet, których można użyć do określenia poświadczenia umożliwiające uwierzytelnianie w nich zasoby.

Można zastosować konfiguracji DSC, które są przechowywane w automatyzacji Azure do maszyn wirtualnych. Konfiguracje mogą żądać innych fizycznych i maszyn wirtualnych z serwera ściągania usługi Konfiguracja DSC automatyzacji. Nie trzeba wdrażać dowolnej infrastruktury do obsługi serwera ściągania usługi Konfiguracja DSC automatyzacji zarządzania konfiguracjami sieci fizycznych lub wirtualnych Windows i Linux systemów lokalnych. Wystarczy tylko ruch wychodzący do Internetu z każdego systemu, które będą zarządzane przy użyciu usługi Konfiguracja DSC automatyzacji. Komunikacja odbywa się za pośrednictwem portu TCP 443 z usługą analizy dzienników.   

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="automation-dsc"></a>Usługa Automation DSC
Konfiguracja DSC automatyzacji można użyć do zarządzania tymi komputerami:

* Maszyny wirtualne platformy Azure (klasyczne) systemem Windows lub Linux.
* Azure maszyn wirtualnych z systemem Windows lub Linux.
* Maszyn wirtualnych Amazon Web Services (AWS) z systemem Windows lub Linux.
* Fizycznych i wirtualnych komputerów z systemem Windows lokalnie lub w chmurze innych niż Azure lub usług AWS.
* Fizyczne i wirtualne komputery z systemem Linux znajdujących się lokalnie lub w chmurze innych niż Azure lub usług AWS.

W przypadku komputerów z systemem Windows można zainstalować najnowszą wersję programu Windows Management Framework (WMF) 5. Dla maszyn z systemem Linux, najnowsza wersja [agenta DSC środowiska PowerShell dla systemu Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) musi być zainstalowany. Agent usługi Konfiguracja DSC środowiska PowerShell używa platformy WMF 5 do komunikowania się z automatyzacji. 

### <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook  
Po wskazaniu komputer w celu uruchomienia zadania elementów runbook hybrydowych, komputer musi spełniać następujące wymagania wstępne:

* Windows Server 2012 lub nowszym.
* Windows PowerShell 4.0 lub nowszy. Zwiększenie niezawodności zalecamy Windows PowerShell 5.0. Możesz [pobrać nową wersję](https://www.microsoft.com/download/details.aspx?id=50395) z Microsoft Download Center.
* .NET framework 4.6.2 lub nowszym.
* Co najmniej dwa rdzenie.
* Co najmniej 4 GB pamięci RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Uprawnienia wymagane do tworzenia konta automatyzacji
Utwórz lub zaktualizuj konto usługi Automatyzacja i wykonać zadania opisane w tym artykule, musi mieć następujące uprawnienia i uprawnienia:   
 
* Aby utworzyć konto usługi Automatyzacja, konto użytkownika usługi Azure Active Directory (Azure AD) musi dodany do roli uprawnienia równorzędne do roli właściciela dla **Microsoft.Automation** zasobów. Aby uzyskać więcej informacji, zobacz [opartej na rolach kontroli dostępu w usłudze Automatyzacja Azure](automation-role-based-access-control.md).  
* W portalu Azure w obszarze **usługi Azure Active Directory** > **ZARZĄDZAJ** > **rejestracji aplikacji**, jeśli **rejestracji aplikacji**  ustawiono **tak**, użytkownicy inni niż administratorzy w Twojej dzierżawie usługi Azure AD można [rejestrowania aplikacji usługi Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Jeśli **rejestracji aplikacji** ustawiono **nr**, użytkownik, który dokonuje tego działania musi być administratorem globalnym w usłudze Azure AD. 

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem do roli administratora globalnego/coadministrator subskrypcji, są dodawane do usługi Active Directory jako Gość. W tym scenariuszu ten komunikat zostanie wyświetlony na **Dodaj konto automatyzacji** strony: "Nie ma uprawnień do utworzenia." 

Jeśli użytkownik zostanie dodany do roli administratora globalnego/coadministrator najpierw należy można je usunąć z wystąpienia usługi Active Directory dla tej subskrypcji, a potem ponownie dodaj je do pełnego roli użytkownika w usłudze Active Directory.

Aby sprawdzić role użytkowników:
1. W portalu Azure, przejdź do **usługi Azure Active Directory** okienka.
2. Wybierz **użytkowników i grup**.
3. Wybierz **wszyscy użytkownicy**. 
4. Po wybraniu określonego użytkownika, wybierz **profilu**. Wartość **typ użytkownika** atrybutu w profilu użytkownika nie powinna być **gościa**.

## <a name="authentication-planning"></a>Planowanie uwierzytelniania
W automatyzacji Azure można automatyzować zadania na zasoby, które są na platformie Azure i lokalnego, a w innych usług w chmurze. Element runbook mógł wykonać żądane działania musi mieć uprawnienia do bezpiecznego dostępu do zasobów. Musi on mieć minimalne uprawnienia wymagane w ramach subskrypcji.  

### <a name="what-is-an-automation-account"></a>Co to jest konto usługi Automatyzacja 
Wszystkie zadania automatyzacji wykonujących względem zasobów za pomocą poleceń cmdlet w automatyzacji Azure uwierzytelniać na platformie Azure przy użyciu uwierzytelniania opartego na poświadczenia tożsamości w organizacji usługi Azure AD.  Konto automatyzacji jest oddzielony od konta, którego używasz do logowania do portalu do konfigurowania i używania zasobów platformy Azure. 

Konto usługi Automatyzacja dołączono następujące zasoby:

* **Certyfikaty**. Znajduje się certyfikat, który jest używany do uwierzytelniania z elementu runbook lub konfiguracji DSC. Można również dodać certyfikaty.
* **Połączenia**. Zawiera informacje uwierzytelniania i konfiguracji, które są wymagane do połączenia z zewnętrznej usługi lub aplikacji z elementu runbook lub konfiguracji DSC.
* **Poświadczenia**. Zawiera **PSCredential** obiektu, który ma poświadczenia zabezpieczeń, takie jak nazwa użytkownika i hasło. Poświadczenia są wymagane do uwierzytelniania z elementu runbook lub konfiguracji DSC.
* **Moduły integracji**. Moduły programu PowerShell są uwzględniane przy użyciu konta automatyzacji. Użyj modułów programu PowerShell do uruchamiania poleceń cmdlet w elementy runbook i konfiguracji DSC.
* **Harmonogramy**. Zawiera harmonogramy, które rozpocząć lub zatrzymać element runbook o określonej godzinie, włączając częstotliwości cyklu.
* **Zmienne**. Zawiera wartości, które są dostępne z poziomu elementu runbook lub konfiguracji DSC.
* **Konfiguracji DSC**. Skrypty programu PowerShell, które opisują jak skonfigurować ustawienia lub funkcji systemu operacyjnego lub sposobu instalowania aplikacji na komputerze z systemem Windows lub Linux.  
* **Elementy Runbook**. Zestaw zadań, wykonujących zautomatyzowanego procesu w automatyzacji oparte na programie Windows PowerShell.    

Automatyzacja zasobów dla każdego konta automatyzacji skojarzonych z pojedynczym regionie Azure. Jednak można użyć konta automatyzacji zarządzania wszystkich zasobów w ramach subskrypcji. Utwórz konta usługi Automation w różnych regionach, jeśli masz zasady, które wymagają, aby dane i zasoby były izolowane w określonym regionie.

Podczas tworzenia konta automatyzacji w portalu Azure, dwie jednostki uwierzytelniania są tworzone automatycznie:

* **Konto Uruchom jako**. To konto wykonuje następujące zadania:
  - Tworzy nazwy głównej usługi w usłudze Azure AD.
  - Tworzy certyfikat.
  - Przypisuje Contributor Role-Based kontroli dostępu (RBAC), która zarządza zasobów usługi Azure Resource Manager za pomocą elementów runbook.
* **Klasycznym konta Uruchom jako**. To konto będzie przekazywać certyfikatu zarządzania. Certyfikat służy do zarządzania zasoby klasyczne za pomocą elementów runbook.

RBAC jest dostępny za pomocą Menedżera zasobów udzielenia akcje dozwolone na konto użytkownika usługi Azure AD i konta Uruchom jako. RBAC służy również do uwierzytelniania tej nazwy głównej usługi. Aby uzyskać więcej informacji oraz uzyskać pomoc w tworzeniu model zarządzania uprawnieniami automatyzacji, zobacz [opartej na rolach kontroli dostępu w artykule usługi Automatyzacja Azure](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Metody uwierzytelniania
Poniższa tabela zawiera podsumowanie metod uwierzytelniania, których można użyć dla każdego środowiska, która obsługuje usługi Automatyzacja Azure.

| Metoda | Środowisko 
| --- | --- | 
| Konto Uruchom jako platformy Azure i klasyczne konto Uruchom jako |Wdrożenie usługi Azure Resource Manager i klasycznej platformy Azure |  
| Konto użytkownika usługi Azure AD |Wdrożenie usługi Azure Resource Manager i klasycznej platformy Azure |  
| Uwierzytelnianie systemu Windows |Lokalnego centrum danych lub inny dostawca usług chmury przy użyciu roli hybrydowy proces roboczy elementu Runbook |  
| Poświadczenia usługi Amazon Web Services |Amazon Web Services |  

Poniższe artykuły zawierają omówienie i wykonanie czynności, aby skonfigurować uwierzytelnianie dla tych środowisk. Artykuły zawierają przy użyciu istniejące i nowe konto, które można przypisać do tego środowiska. 
* [Utwórz autonomiczny konto usługi Automatyzacja Azure](automation-create-standalone-account.md)
* [Uwierzytelniania elementów Runbook w usłudze Azure wdrożenie klasyczne i Menedżer zasobów](automation-create-aduser-account.md)
* [Elementy Runbook za pomocą usług Amazon Web Services uwierzytelniania](automation-config-aws-account.md)
* [Aktualizacja konta Uruchom jako automatyzacji](automation-create-runas-account.md)

W przypadku kont Uruchom jako platformy Azure i klasycznego Uruchom jako [zaktualizować automatyzacji konta Uruchom jako](automation-create-runas-account.md) opisuje sposób aktualizacji istniejącego konta automatyzacji z kontami Uruchom jako z portalu. On również opis Jeśli konta automatyzacji nie została wcześniej skonfigurowana przy użyciu konta Uruchom jako lub klasycznego Uruchom jako za pomocą programu PowerShell. Konto Uruchom jako, a konto klasycznego Uruchom jako można utworzyć przy użyciu certyfikatu wystawionego przez urzędu certyfikacji (CA) przedsiębiorstwa. Przegląd [aktualizacji automatyzacji Uruchom jako konta](automation-create-runas-account.md) więcej informacji na temat tworzenia kont przy użyciu tej konfiguracji.     
 
## <a name="network-planning"></a>Planowanie sieci
Aby hybrydowy proces roboczy do nawiązania połączenia i zarejestruj analizy dzienników musi mieć dostęp do numer portu i adres URL, które zostały opisane w tej sekcji. Jest to dodatkowe [porty i adresy URL wymagane dla programu Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) do nawiązania połączenia analizy dzienników. 

Jeśli używasz serwera proxy do komunikacji między agentem i usługą analizy dzienników, upewnij się, że odpowiednie zasoby są dostępne. Jeśli używasz zapory, aby ograniczyć dostęp do Internetu, należy skonfigurować zaporę tak, aby zezwolić na dostęp.

Następujące adresy URL i port są wymagane dla roli hybrydowy proces roboczy elementu Runbook do komunikowania się z automatyzacji:

* Port: Ruch wychodzący do Internetu wymagany jest tylko TCP 443.
* Global URL: *.azure-automation.net.

Jeśli masz konto automatyzacji, który jest zdefiniowany dla konkretnego regionu, można ograniczyć komunikacji z tym regionalne centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu.

| **Region** | **Rekord DNS** |
| --- | --- |
| Środkowo-południowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Środkowo-zachodnie stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net |
| Administracja USA — Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Aby uzyskać listę adresów IP regionu zamiast nazwy regionów, Pobierz [adres IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653) pliku XML z Microsoft Download Center. 

> [!NOTE]
> Plik XML adres IP centrum danych Azure zawiera listę zakresów adresów IP, które są używane w centrach danych Microsoft Azure. Zakresy obliczeniowych, SQL i Magazyn znajdują się w pliku. 
>
>Zaktualizowany plik jest wysyłany co tydzień. Plik odzwierciedla aktualnie wdrożonych zakresy i nadchodzące zmiany zakresu adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień. 
>
> Należy dobrze do pobrania nowego pliku XML, co tydzień. Następnie należy zaktualizować lokację poprawnie zidentyfikować usługi działające na platformie Azure. Azure ExpressRoute użytkowników należy zauważyć, że ten plik ma być używane do aktualizowania anonsu protokołu BGP (Border Gateway) Azure miejsca w pierwszym tygodniu każdego miesiąca. 
> 

## <a name="creating-an-automation-account"></a>Utwórz konto automatyzacji

W poniższej tabeli zamieszczono metody tworzenia konta automatyzacji w portalu Azure. W tabeli opisano każdy typ środowisko wdrażania i różnice między nimi.  

|Metoda | Opis |
|-------|-------------|
| Wybierz **Automatyzacja i kontrola** w portalu Azure Marketplace | Oferty Azure Marketplace tworzy konto usługi Automatyzacja i obszaru roboczego analizy dzienników, które są powiązane i w tej samej grupie zasobów i region. Integracja z usługą analizy dzienników umożliwia także użycie go do monitorowania i analizy strumienia stanu i zadania zadania elementu runbook wraz z upływem czasu. Umożliwia także funkcje zaawansowane w analizy dzienników Eskaluj lub badania problemów. Oferta wdraża **śledzenia zmian** i **zarządzania aktualizacjami** rozwiązania, które są domyślnie włączone. |
| Wybierz **automatyzacji** w witrynie Marketplace | Ta metoda tworzy konto usługi Automatyzacja w grupie nowy lub istniejący zasób, który nie jest połączony z obszaru roboczego analizy dzienników. Nie zawiera żadnych dostępnych rozwiązań z **Automatyzacja i kontrola** oferty. Ta metoda jest podstawową konfigurację, która stanowi wprowadzenie do automatyzacji. Dowiedz się, jak napisać elementów runbook i konfiguracji DSC i Dowiedz się, jak korzystać z funkcji usługi może pomóc. |
| Wybierz **zarządzania** rozwiązań | W przypadku wybrania **zarządzania** rozwiązania, w tym [zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md), [uruchamiania/zatrzymywania maszyn wirtualnych podczas poza godzinami pracy](automation-solution-vm-management.md), lub [śledzenia zmian](../log-analytics/log-analytics-change-tracking.md), rozwiązanie zostanie wyświetlony monit o wybranie istniejącego konta automatyzacji i obszaru roboczego analizy dzienników. Rozwiązanie oferuje możliwość tworzenia konta automatyzacji i obszaru roboczego analizy dzienników, co jest wymagane dla rozwiązania do wdrożenia w ramach subskrypcji. |

### <a name="create-an-automation-account-thats-integrated-with-log-analytics"></a>Utwórz konto automatyzacji, który jest zintegrowany z analizy dzienników
Aby dołączyć automatyzacji zaleca się wybranie **Automatyzacja i kontrola** oferty w witrynie Marketplace. Za pomocą tej metody tworzy konto usługi Automatyzacja i ustanawia integracji z obszaru roboczego analizy dzienników. Korzystając z tej metody, masz również możliwość zainstalowania rozwiązań do zarządzania, które są dostępne z ofertą.  

[Tworzenie konta automatyzacji autonomiczny](automation-create-standalone-account.md) przeprowadzi Cię przez proces tworzenia konta automatyzacji i obszaru roboczego analizy dzienników przez dołączania **Automatyzacja i kontrola** oferty. Można poznać sposoby tworzenia konta automatyzacji do testowania autonomiczny lub podejrzeć usługi.  

Aby utworzyć konto usługi Automatyzacja i obszaru roboczego analizy dzienników przy użyciu **Automatyzacja i kontrola** oferta portalu Marketplace:

1. Zaloguj się do portalu Azure przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i coadministrator subskrypcji.
2. Wybierz **nowe**.<br><br> ![W portalu Azure wybierz opcję Nowy](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Wyszukaj **automatyzacji**. W wynikach wyszukiwania wybierz **automatyzacji & sterowanie**.<br><br> ![Wyszukaj i wybierz automatyzacji & kontroli w portalu Azure Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Przejrzyj opis oferty, a następnie wybierz **Utwórz**.  
5. W obszarze **Automatyzacja i kontrola**, wybierz pozycję **obszarem roboczym pakietu OMS**. W obszarze **obszarów roboczych OMS**, wybierz obszar roboczy analizy dzienników, która jest połączona z subskrypcją platformy Azure, konto automatyzacji jest w. Jeśli nie masz obszaru roboczego analizy dzienników, wybierz **Utwórz nowy obszar roboczy**. W obszarze **obszarem roboczym pakietu OMS**: 
  1. Aby uzyskać **obszarem roboczym pakietu OMS**, wprowadź nazwę dla nowego obszaru roboczego.
  2. Aby uzyskać **subskrypcji**, wybierz link do subskrypcji. Jeśli ustawienie domyślne nie ma subskrypcji, która ma być używany, wybierz subskrypcję z listy rozwijanej.
  3. Aby uzyskać **grupy zasobów**, Utwórz grupę zasobów lub wybierz istniejącą grupę zasobów.  
  4. Aby uzyskać **lokalizacji**, wybierz region. Aby uzyskać więcej informacji, zobacz [regiony usługi Automatyzacja Azure jest dostępna w](https://azure.microsoft.com/regions/services/). Rozwiązania są dostępne w dwóch warstw: warstwy bezpłatne i na węzeł (OMS). Warstwa bezpłatna ma ograniczenie ilości danych, które zostały zebrane codziennie, okres przechowywania i minut czasu wykonywania zadania elementu runbook. Na węzeł (OMS) warstwy nie ma ograniczenie ilości danych zbieranych dziennie.  
  5. Wybierz pozycję **Konto usługi Automation**.  Jeśli tworzysz nowy obszar roboczy analizy dzienników, należy utworzyć konto usługi Automatyzacja, który został skojarzony z nowego obszaru roboczego analizy dzienników. Obejmują Twojej subskrypcji platformy Azure, grupy zasobów i region. 
    1. Wybierz **Utwórz konto automatyzacji**.
    2. W obszarze **konto automatyzacji**w **nazwa** wprowadź nazwę konta automatyzacji.
    Inne opcje są wypełniane automatycznie na podstawie w obszarze roboczym analizy dzienników wybrane. Nie można zmodyfikować te opcje. 
    3. Konto Uruchom jako platformy Azure jest domyślną metodą uwierzytelniania dla oferty. Po wybraniu **OK**, opcje konfiguracji są weryfikowane i utworzeniu konta automatyzacji. Aby śledzić postęp, w menu, wybierz **powiadomienia**. 
    4. W przeciwnym razie wybierz istniejące konto Uruchom jako usługi Automation. Wybrane konto już nie można połączyć z innym obszaru roboczego analizy dzienników. Jeśli tak jest, zostanie wyświetlony komunikat z powiadomieniem. Jeśli konto jest już połączony z obszaru roboczego analizy dzienników, wybierz inne konto automatyzacji Uruchom jako lub utwórz je.
    5. Po wprowadzeniu lub wybierz wymagane informacje, wybierz **Utwórz**. Informacje jest weryfikowany i są tworzone na kontach konta automatyzacji i Uruchom jako. Automatycznie nastąpi powrót do **obszarem roboczym pakietu OMS** okienka.  
6. Po wprowadzeniu lub wybierz wymagane informacje na **obszarem roboczym pakietu OMS** okienku wybierz **Utwórz**.  Informacje jest weryfikowany i utworzeniu obszaru roboczego. Aby śledzić postęp, w menu, wybierz **powiadomienia**. Nastąpi powrót do **Dodaj rozwiązanie** okienka.  
7. W obszarze **Automatyzacja i kontrola** ustawienia, upewnij się, czy chcesz zainstalować zalecanych rozwiązań instalowane. Jeśli zmienisz jakiekolwiek domyślne opcje rozwiązania można zainstalować oddzielnie później.  
8. Aby kontynuować proces przechodzenia do automatyzacji i obszaru roboczego analizy dzienników, wybierz **Utwórz**. Wszystkie ustawienia są prawidłowe, a następnie próbuje wdrażanie oferty w ramach subskrypcji platformy Azure. Ten proces może potrwać kilka sekund. Aby śledzić postęp, w menu, wybierz **powiadomienia**. 

Po ofercie jest został załadowany, można wykonać następujące zadania:
* Rozpocznij tworzenie elementów runbook.
* Praca z rozwiązaniami do zarządzania, które są włączone.
* Wdrażanie [hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md) roli.
* Rozpoczęcia stosowaniu [analizy dzienników](https://docs.microsoft.com/azure/log-analytics) do zbierania danych jest generowany przez zasobów w chmurze lub lokalnym środowisku.   

## <a name="next-steps"></a>Kolejne kroki
* Upewnij się, że nowe konto automatyzacji można uwierzytelniać na zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [testu automatyzacji Uruchom jako platformy Azure uwierzytelnianie konta](automation-verify-runas-authentication.md).
* Dowiedz się, jak rozpocząć pracę z tworzeniem elementów runbook i powiązane zagadnienia, przed rozpoczęciem tworzenia. Aby uzyskać więcej informacji, zobacz [typy elementu runbook automatyzacji](automation-runbook-types.md).


