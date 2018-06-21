---
title: Informacje o wersji zestawu Microsoft Azure stosu Development Kit | Dokumentacja firmy Microsoft
description: Ulepszenia, poprawki i znane problemy dotyczące Azure stosu Development Kit.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: bbd9bb0d56dd61fd0a32531ac425a1dbc1aa8923
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295791"
---
# <a name="azure-stack-development-kit-release-notes"></a>Informacje o wersji platformy Azure stosu Development Kit  
Te informacje o wersji zawierają informacje dotyczące ulepszeń, poprawki i znane problemy w systemie Azure stosu Development Kit. Jeśli nie masz pewności, której używasz wersji, możesz [sprawdzić za pomocą portalu](.\.\azure-stack-updates.md#determine-the-current-version).

> Stosowanie what's new in ASDK Subskrybuj [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [źródła danych](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Kompilacja 1.1805.1.47

> [!TIP]  
> Na podstawie opinii klientów, jest dostępna aktualizacja do wersji schematu na użytek systemu Microsoft Azure stosu. Począwszy od tej aktualizacji 1805, nowego schematu lepiej reprezentuje bieżącą wersję chmury.  
> 
> Wersja schematu jest teraz *Version.YearYearMonthMonth.MinorVersion.BuildNumber* gdzie drugi i trzeci zestawy wskazać wersji i wydania. Na przykład 1805.1 reprezentuje *wersji RTM* 1805 wersji (RTM).  


### <a name="new-features"></a>Nowe funkcje 
Ta kompilacja obejmuje następujących ulepszeń i poprawek stosu Azure.  

- <!-- 2297790 - IS, ASDK --> **Stos Azure zawiera teraz *Syslog* klienta** jako *funkcja w wersji zapoznawczej*. Ten klient umożliwia przekazywanie dzienników inspekcji i zabezpieczeń dotyczące infrastruktury stosu Azure w celu Syslog serwera lub zabezpieczeń informacji i zdarzenia (SIEM) oprogramowania do zarządzania zewnętrznej stos Azure. Syslog klienta obsługuje obecnie tylko nieuwierzytelnione połączenia protokołu UDP za pośrednictwem domyślnego portu 514. Ładunek każdego komunikatu dziennika systemu jest sformatowany wspólną formatu zdarzeń (CEF). 

  Aby skonfigurować klienta usługi Syslog, użyj **SyslogServer zestaw** polecenia cmdlet w uprzywilejowanych punktu końcowego. 

  W tej wersji zapoznawczej można napotkać następujące trzy alerty. Gdy prezentowany przez stos Azure, obejmują te alerty *opisy* i *korygowania* wskazówki. 
  - Tytuł: Integralność kodu wyłączanie  
  - Nazwa: Integralności kodu w trybie inspekcji 
  - Nazwa: Konto użytkownika utworzone

  Gdy ta funkcja jest dostępna w wersji zapoznawczej, go nie powinno być stosowane na w środowiskach produkcyjnych.   


### <a name="fixed-issues"></a>Rozwiązane problemy
- Usunięto problem zablokowany [otwierania nowe żądanie pomocy technicznej z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z portalu administratora. Teraz ta opcja działa poprawnie. 

- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- <!-- 2551834 - IS, ASDK --> Po wybraniu **omówienie** dla konta magazynu w portalach administratora lub użytkownika informacji z *Essentials* nie są wyświetlane w okienku.  W okienku Essentials Wyświetla informacje o koncie, takie jak jego *grupy zasobów*, *lokalizacji*, i *identyfikator subskrypcji*.  Omówienie inne opcje są dostępne, takich jak *usług* i *monitorowanie*, również w opcji *Otwórz w Eksploratorze* lub *usunięcie konta magazynu* .  

  Aby wyświetlić dostępne informacje, należy użyć [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell. 

- <!-- 2551834 - IS, ASDK --> Po wybraniu **tagi** dla konta magazynu w portalach administrator lub użytkownik, nie udało się załadować informacje, a nie są wyświetlane.  

  Aby wyświetlić dostępne informacje, należy użyć [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell.

- <!-- TBD - IS ASDK --> Nie należy używać nowych typów subskrypcji administracyjne *zliczania subskrypcji*, i *subskrypcji zużycie*. Te nowe typy subskrypcji zostały wprowadzone w wersji 1804, ale nie jest jeszcze gotowa do użycia. Można nadal używać *domyślny dostawca* typ subskrypcji.  

- <!-- 2403291 - IS ASDK --> Nie masz użyj poziomego paska przewijania wzdłuż dolnej części portali administratora i użytkownika. Jeśli nie masz dostępu do poziomego paska przewijania, za pomocą nawigacyjnej na przejście do poprzedniego bloku w portalu po wybraniu nazwy bloku mają być wyświetlane na liście stron nadrzędnych znajdujących się u góry po lewej portalu.
  ![Nawigacją](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


#### <a name="health-and-monitoring"></a>Monitorowania kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnika, który ma następujące informacje:  

   Alert #1:
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skanera jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.  

  Alert #2.
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera błędu jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować i zostanie automatycznie zamknięte w czasie.  

- <!-- 2392907 – ASDK -->   Można napotkać *krytyczne* alertów dla **niska pojemność pamięci**. Ten alert ma następujący opis: *region zużyła ponad 95,00 zł % dostępnej pamięci. Tworzenie maszyn wirtualnych z dużą ilością pamięci może zakończyć się niepowodzeniem.*

  Ten alert mogą być generowane, gdy stos Azure niepoprawnie kont do użycia pamięci w zestawie Azure stosu.  

  Można zignorować ten alert, a problem nie ma wpływu na umieszczanie maszyn wirtualnych. 

- <!-- 2368581 - IS. ASDK --> Operator stosu Azure, jeśli otrzymasz alert małej ilości pamięci i maszyn wirtualnych dzierżawcy nie można wdrożyć przy użyciu *błąd tworzenia sieci szkieletowej maszyny Wirtualnej*, istnieje możliwość, że sygnatura stosu Azure jest za mało dostępnej pamięci. Użyj [Planne pojemności stosu Azure](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie pojemności dla obciążeń. 


#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD - IS, ASDK --> Podczas wybierania rozmiaru maszyny wirtualnej dla wdrożenia maszyny wirtualnej, niektóre rozmiary serii F maszyny Wirtualnej nie są widoczne jako część selektora rozmiar podczas tworzenia maszyny Wirtualnej. Następujących rozmiarów maszyn wirtualnych nie pojawiają się w selektorze: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Jako obejście użyj jednej z następujących metod można wdrożyć maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, który ma być używany.

  - **Szablonu usługi Azure Resource Manager:** w przypadku użycia szablonu ustawić *vmSize* w szablonie równe rozmiar maszyny Wirtualnej, którego chcesz użyć. Na przykład następujący wpis służy do wdrażania maszyny Wirtualnej, która używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** można użyć [tworzenia maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Środowiska PowerShell:** przy użyciu programu PowerShell, można użyć [AzureRMVMConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć serii D maszyny Wirtualnej. Wszystkie obsługiwane D serii maszyn wirtualnych może obsłużyć jako wiele dysków z danymi jako konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyny Wirtualnej systemu Linux nie jest obsługiwana w stosie Azure. Podczas wdrażania maszyny Wirtualnej systemu Linux z włączoną diagnostykę maszyny Wirtualnej, wdrożenie zakończy się niepowodzeniem. Wdrożenie nie powiedzie się także włączenie metryk podstawowej maszyny Wirtualnej systemu Linux za pomocą ustawień diagnostycznych. 

#### <a name="networking"></a>Networking
- <!-- TBD - IS ASDK --> Nie można utworzyć trasy zdefiniowane przez użytkownika w jednym z portalu administratora lub użytkownika. Aby uniknąć tego problemu, należy użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> W obszarze **sieci**, jeśli klikniesz przycisk **Tworzenie bramy sieci VPN** do skonfigurowania połączenia sieci VPN, **oparta na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w stosie Azure.

- <!-- 2388980 -  IS ASDK --> Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia oryginalna maszyna wirtualna, a nie nowy.


- <!-- 2292271 - IS ASDK --> Po podniesieniu limitu przydziału dla zasobu sieciowego, będącej częścią ofertę i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowe subskrypcje, które są tworzone po zwiększeniu limit przydziału. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz temat jak [udostępnić plan dodatek](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która ma zasobów strefy DNS lub zasobów tabeli tras skojarzonych z nim. Aby pomyślnie usunąć subskrypcję, należy najpierw usunąć strefy DNS i tabeli tras zasobów z subskrypcji dzierżawcy. 


- <!-- 1902460 -  IS ASDK --> Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.
 
- <!-- TBD -  IS ASDK --> Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- <!-- TBD - ASDK --> Bazy danych serwerów hosta muszą być przeznaczone wyłącznie do użytku przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia, który jest używany przez innego użytkownika, łącznie z usługi aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy muszą zarejestrować dostawcy zasobów magazynu przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby skalować w poziomie infrastruktury (pracowników, zarządzania, role frontonu), musi być programu PowerShell, zgodnie z opisem w informacjach o wersji dla obliczania.  

- <!-- TBD - IS ASDK --> Usługi aplikacji można wdrożyć tylko do *subskrypcji domyślny dostawca* w tej chwili. W ramach przyszłej aktualizacji usługi aplikacji — zostanie wdrożona w nowej *zliczania subskrypcji* który został wprowadzony w 1804 stosu Azure. Gdy zliczania jest obsługiwane w przypadku użycia, wszystkie istniejące wdrożenia będą migrowane do ten nowy typ subskrypcji.

#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.

<!-- #### Identity -->



## <a name="build-201805131"></a>Kompilacja 20180513.1

### <a name="new-features"></a>Nowe funkcje 
Ta kompilacja obejmuje następujących ulepszeń i poprawek stosu Azure.  

- <!-- 1759172 - IS, ASDK --> **Nowe subskrypcje administracyjne**. Z 1804 istnieją dwa typy nowej subskrypcji dostępne w portalu. Te nowe typy subskrypcji są oprócz domyślny dostawca subskrypcji i są widoczne w przypadku nowych instalacji Azure stosu, począwszy od wersji 1804. *Nie należy używać tych nowych typów subskrypcji z tej wersji programu Azure stosu*. Firma Microsoft będzie poinformować o udostępnieniu używanie tych typów subskrypcji przy użyciu przyszłej aktualizacji. 

  Te nowe typy subskrypcji są widoczne, ale część większych zmian do zabezpieczania domyślny dostawca subskrypcji i ułatwiające wdrażanie udostępnionych zasobów, takich jak serwery SQL Hosting. 

  Są teraz dostępne typy trzy subskrypcji:  
  - Domyślny dostawca subskrypcji: nadal używać tego typu subskrypcji. 
  - Pomiaru subskrypcji: *nie należy używać tego typu subskrypcji.*
  - Zużycie subskrypcji: *nie należy używać tego typu subskrypcji*

### <a name="fixed-issues"></a>Rozwiązane problemy
- <!-- IS, ASDK -->  W portalu administracyjnym, masz już odświeżanie kafelka aktualizacji przed wyświetleniem informacji. 

- <!-- 2050709 - IS, ASDK -->  Można teraz korzystać z portalu administratora do edytowania metryki magazynu dla usługi obiektów Blob, usługi tabel i kolejek usługi.

- <!-- IS, ASDK --> W obszarze **sieci**, po kliknięciu **połączenia** do skonfigurowania połączenia sieci VPN, **lokacja lokacja (IPsec)** jest obecnie jedyną dostępną opcją. 

- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Dodatkowe wersje upłynął dzięki tej aktualizacji  
Poniżej są teraz dostępne, ale nie wymagają aktualizacji stosu Azure 1804.
- **Aktualizacja do pakiet monitorowania programu Microsoft Azure stosu System Center Operations Manager**. Nowa wersja (1.0.3.0) programu Microsoft System Center Operations Manager monitorowania Pack stosu Azure jest dostępna dla [Pobierz](https://www.microsoft.com/download/details.aspx?id=55184). W tej wersji można użyć nazwy główne usług, podczas dodawania podłączonej wdrożenia stosu Azure. Ta wersja zawiera też środowisko zarządzania aktualizacjami, które można podjąć akcję naprawczą bezpośrednio z poziomu programu Operations Manager. Istnieją nowe pulpity nawigacyjne, które wyświetlić dostawców zasobów, jednostek skalowania i skalowanie węzłów jednostki.

- **Nowe Azure stosu wersja programu PowerShell Admin 1.3.0**.  Program Azure PowerShell stosu 1.3.0 jest teraz dostępna do instalacji. Ta wersja zawiera polecenia dla wszystkich dostawców zasobów administratora do zarządzania Azure stosu.  W tej wersji, niektóre zawartości zostaną wycofane z usługi GitHub z narzędzia Azure stosu [repozytorium](https://github.com/Azure/AzureStack-Tools). 

   Aby uzyskać szczegółowe informacje dotyczące instalacji, należy wykonać [instrukcje](.\.\azure-stack-powershell-install.md) lub [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) zawartości dla usługi Azure stosu modułu 1.3.0. 

- **Początkowa wersja Azure stosu interfejsu API Rest odwołania**. [Dokumentacja interfejsu API dla wszystkich dostawców zasobów Azure stosu Admin](https://docs.microsoft.com/rest/api/azure-stack/) zostały opublikowane. 

### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> Możliwość [aby otworzyć nowe żądanie pomocy technicznej z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z wewnątrz administrator portalu nie jest dostępna. Zamiast tego użyj następującego łącza:     
    - Azure stosu Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Nie masz użyj poziomego paska przewijania wzdłuż dolnej części portali administratora i użytkownika. Jeśli nie masz dostępu do poziomego paska przewijania, za pomocą nawigacyjnej na przejście do poprzedniego bloku w portalu po wybraniu nazwy bloku mają być wyświetlane na liście stron nadrzędnych znajdujących się u góry po lewej portalu.
  ![Nawigacją](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

-   <!-- TBD -  IS ASDK --> W portalu administracyjnym, można napotkać alert krytyczny dotyczący składnika Microsoft.Update.Admin. Nazwa alertu, opis i korygowania wszystkich wyświetlania jako:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 

#### <a name="health-and-monitoring"></a>Monitorowania kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnika, który ma następujące informacje:  

   Alert #1:
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skanera jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.  

  Alert #2.
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera błędu jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować. Będą one Zamknij automatycznie wraz z upływem czasu.  

#### <a name="marketplace"></a>Portal Marketplace
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii DS maszyny Wirtualnej. Serii DS maszyny wirtualne mogą obsługiwać jako wiele dysków z danymi jako konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyny Wirtualnej systemu Linux nie jest obsługiwana w stosie Azure. Podczas wdrażania maszyny Wirtualnej systemu Linux z włączoną diagnostykę maszyny Wirtualnej, wdrożenie zakończy się niepowodzeniem. Wdrożenie nie powiedzie się także włączenie metryk podstawowej maszyny Wirtualnej systemu Linux za pomocą ustawień diagnostycznych. 

#### <a name="networking"></a>Networking
- <!-- 1766332 - IS, ASDK --> W obszarze **sieci**, jeśli klikniesz przycisk **Tworzenie bramy sieci VPN** do skonfigurowania połączenia sieci VPN, **oparta na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w stosie Azure.

- <!-- 2388980 -  IS ASDK --> Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia oryginalna maszyna wirtualna, a nie nowy.

- <!-- 2292271 - IS ASDK --> Po podniesieniu limitu przydziału dla zasobu sieciowego, będącej częścią ofertę i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowe subskrypcje, które są tworzone po zwiększeniu limit przydziału. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz temat jak [udostępnić plan dodatek](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która ma zasobów strefy DNS lub zasobów tabeli tras skojarzonych z nim. Aby pomyślnie usunąć subskrypcję, należy najpierw usunąć strefy DNS i tabeli tras zasobów z subskrypcji dzierżawcy. 


- <!-- 1902460 -  IS ASDK --> Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.
 
- <!-- TBD -  IS ASDK --> Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- <!-- TBD - ASDK --> Bazy danych serwerów hosta muszą być przeznaczone wyłącznie do użytku przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia, który jest używany przez innego użytkownika, łącznie z usługi aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Użytkownicy muszą zarejestrować dostawcy zasobów magazynu przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD -  IS ASDK --> Aby skalować w poziomie infrastruktury (pracowników, zarządzania, role frontonu), musi być programu PowerShell, zgodnie z opisem w informacjach o wersji dla obliczania.
 
#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Pobieranie narzędzia Azure stosu z usługi GitHub
- Korzystając z *webrequest wywołania* polecenia cmdlet programu PowerShell, aby pobrać stosu Azure narzędzi z serwisu Github, komunikat o błędzie:     
    -  *Wywołanie webrequest: żądanie zostało przerwane: nie można utworzyć bezpiecznego kanału SSL/TLS.*     

  Ten błąd występuje z powodu ostatnie amortyzacja Obsługa GitHub standardów kryptograficznych Tlsv1 i Tlsv1.1 (wartość domyślna dla środowiska PowerShell). Aby uzyskać więcej informacji, zobacz [powiadomienia usuwania słabe standardów kryptograficznych](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Kompilacja 20180302.1

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Nowe funkcje i poprawki wydane dla stosu Azure zintegrowanych systemów wersji 1803 dotyczą Azure stosu Development Kit. Zobacz [nowe funkcje](.\.\azure-stack-update-1803.md#new-features) i [stałej problemów](.\.\azure-stack-update-1803.md#fixed-issues) sekcje 1803 stosu Azure zaktualizować informacje o wersji, aby uzyskać szczegółowe informacje.  
> [!IMPORTANT]    
> Niektóre elementy na liście **nowe funkcje** i **stałej problemów** sekcjach dotyczą tylko stosu Azure zintegrowanych systemów.

### <a name="changes"></a>Zmiany
- Sposób zmiany stanu nowo utworzony ofertę otrzymaną *prywatnej* do *publicznego* lub *wycofany z eksploatacji* została zmieniona. Aby uzyskać więcej informacji, zobacz [utworzyć ofertę](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- Możliwość [aby otworzyć nowe żądanie pomocy technicznej z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z wewnątrz administrator portalu nie jest dostępna. Zamiast tego użyj następującego łącza:     
    - Azure stosu Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2050709 --> W portalu administracyjnym nie jest możliwe edytowanie metryki magazynu dla usługi obiektów Blob, usługi tabel lub kolejek usługi. Gdy przejdź do magazynu, a następnie wybierz obiektów blob, tabeli lub Kafelek usługi kolejki, zostanie otwarty nowy blok, który przedstawia wykres metryki dla tej usługi. W przypadku wybrania następnie edytuj od góry kafelka wykresu metryki, bloku Edytuj wykres zostanie otwarty, ale nie są wyświetlane opcje, aby zmienić metryki.  

- Zostanie wyświetlony **wymagana aktywacja** alert ostrzeżenia, zaleceniem można zarejestrować zestawu Azure stosu Development Kit. To zachowanie jest oczekiwane.

- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- Na pulpicie nawigacyjnym portalu administracyjnego kafelka aktualizacji nie są wyświetlane informacje o aktualizacjach. Aby rozwiązać ten problem, kliknij Kafelek, aby go odświeżyć.

-   W portalu administracyjnym, można napotkać alert krytyczny dotyczący składnika Microsoft.Update.Admin. Nazwa alertu, opis i korygowania wszystkich wyświetlania jako:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 

- W portalu administratora usługi i portalu użytkowników, bloku omówienie nie udało się załadować po wybraniu bloku omówienie kont magazynu, które zostały utworzone przy użyciu starszej wersji interfejsu API (przykład: 2015-06-15). 

  Jako obejście, użyj programu PowerShell, aby uruchomić **Start ResourceSynchronization.ps1** skryptu, aby przywrócić dostęp do szczegółów konta magazynu. [Skrypt jest dostępny w witrynie GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)i musi działać z poświadczeniami administratora usługi na hoście development kit, jeśli używasz ASDK.  

- **Kondycja usługi** bloku nie udało się załadować. Po otwarciu bloku usługi kondycji w portalu administratora lub użytkownika, stosu Azure jest wyświetlany błąd i nie zostanie załadowany informacji. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć usługi kondycji, ta funkcja nie jest jeszcze dostępny, ale będzie wykonywany w przyszłych wersjach programu Azure stosu.


#### <a name="health-and-monitoring"></a>Monitorowania kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnika, który ma następujące informacje:  

   Alert #1:
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skanera jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.  

  Alert #2.
   - Nazwa: Rolę infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera błędu jest niedostępny. Może to dotyczyć raportów o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować. Będą one Zamknij automatycznie wraz z upływem czasu.  

- W portalu administracyjnym stosu Azure zostanie wyświetlony alert krytyczny o nazwie **do czasu wygaśnięcia certyfikatu zewnętrznego**.  Ten alert można bezpiecznie zignorować i ma wpływu na operacje Azure stosu Development Kit. 


#### <a name="marketplace"></a>Portal Marketplace
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii DS maszyny Wirtualnej. Serii DS maszyny wirtualne mogą obsługiwać jako wiele dysków z danymi jako konfiguracji platformy Azure.

- Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

-  Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Diagnostyka maszyny Wirtualnej systemu Linux nie jest obsługiwana w stosie Azure. Podczas wdrażania maszyny Wirtualnej systemu Linux z włączoną diagnostykę maszyny Wirtualnej, wdrożenie zakończy się niepowodzeniem. Wdrożenie nie powiedzie się także włączenie metryk podstawowej maszyny Wirtualnej systemu Linux za pomocą ustawień diagnostycznych. 


#### <a name="networking"></a>Networking
- W obszarze **sieci**, jeśli klikniesz przycisk **połączenia** do skonfigurowania połączenia sieci VPN, **do wirtualnymi** jest wymieniony jako typ połączenia to możliwe. Nie należy zaznaczać tej opcji. Obecnie tylko **lokacja lokacja (IPsec)** opcja jest obsługiwana.

- Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia oryginalna maszyna wirtualna, a nie nowy.



- Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.
 
- Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.



#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- Może potrwać maksymalnie jedną godzinę, zanim użytkownicy mogą tworzyć baz danych w nowym SQL lub MySQL jednostki SKU.

- Bazy danych serwerów hosta muszą być przeznaczone wyłącznie do użytku przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia, który jest używany przez innego użytkownika, łącznie z usługi aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL.

#### <a name="app-service"></a>App Service
- Użytkownicy muszą zarejestrować dostawcy zasobów magazynu przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- Aby skalować w poziomie infrastruktury (pracowników, zarządzania, role frontonu), musi być programu PowerShell, zgodnie z opisem w informacjach o wersji dla obliczania.
 
#### <a name="usage"></a>Sposób użycia  
- Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Pobieranie narzędzia Azure stosu z usługi GitHub
- Korzystając z *webrequest wywołania* polecenia cmdlet programu PowerShell, aby pobrać stosu Azure narzędzi z serwisu Github, komunikat o błędzie:     
    -  *Wywołanie webrequest: żądanie zostało przerwane: nie można utworzyć bezpiecznego kanału SSL/TLS.*     

  Ten błąd występuje z powodu ostatnie amortyzacja Obsługa GitHub standardów kryptograficznych Tlsv1 i Tlsv1.1 (wartość domyślna dla środowiska PowerShell). Aby uzyskać więcej informacji, zobacz [powiadomienia usuwania słabe standardów kryptograficznych](https://githubengineering.com/crypto-removal-notice/).

  Aby rozwiązać ten problem, Dodaj `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` na początku skryptu, aby wymusić konsoli programu PowerShell, aby użyć TLSv1.2 podczas pobierania z repozytoriami GitHub.





