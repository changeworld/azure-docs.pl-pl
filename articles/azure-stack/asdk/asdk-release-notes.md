---
title: Informacje o wersji programu Microsoft Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Ulepszenia, poprawki i znane problemy dotyczące usługi Azure Stack Development Kit.
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
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2f732dfdfe9bf4aff2753114c3041f8f646421c2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344736"
---
# <a name="azure-stack-development-kit-release-notes"></a>Informacje o wersji usługi Azure Stack Development Kit
Te informacje o wersji zawierają informacje dotyczące ulepszeń, poprawek i znanych problemach w usłudze Azure Stack Development Kit. Jeśli nie masz pewności, której wersji używasz, możesz to zrobić [korzystanie z portalu, aby sprawdzić](.\.\azure-stack-updates.md#determine-the-current-version).

> Na bieżąco what's new in ASDK subskrybując [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [kanału informacyjnego](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805142"></a>Tworzenie 1.1805.1.42

<!-- ### New features 
This build includes the following improvements and fixes for Azure Stack.  
-->


### <a name="fixed-issues"></a>Rozwiązane problemy

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> Nie można zastosować aktualizacje sterowników za pomocą pakietu rozszerzenia producenta OEM w tej wersji usługi Azure Stack.  Nie ma sposobu obejścia tego problemu.
 
- <!-- TBD - IS ASDK --> Nie należy używać nowych typów subskrypcji administracyjne *pomiaru subskrypcji*, i *subskrypcji zużycie*. Te nowe typy subskrypcji zostały wprowadzone w wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać *domyślny dostawca* typu subskrypcji.  

- <!-- 2403291 - IS ASDK --> Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować. Będą one zamknięte automatycznie wraz z upływem czasu.  

#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Może następnie spróbować Pobierz serwer obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych. 

#### <a name="networking"></a>Networking
- <!-- 1766332 - IS, ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 2388980 -  IS ASDK --> Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonej maszyny Wirtualnej, a nie nowy.

- <!-- 2292271 - IS ASDK --> Jeśli zostanie podniesiony limitu przydziału dla zasobu sieciowego, który jest częścią oferty i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowych subskrypcjach, które są tworzone po nastąpi zwiększenie limitu przydziału. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz instrukcje [udostępnić planu dodatku](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która zawiera zasoby strefy DNS lub skojarzone z nią zasoby tabeli tras. Aby pomyślnie usunąć subskrypcję, musisz najpierw usunąć strefy DNS i tabelę tras zasobów z subskrypcji dzierżawcy. 


- <!-- 1902460 -  IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.
 
- <!-- TBD -  IS ASDK --> Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- <!-- TBD - ASDK --> Bazy danych serwerów hosta musi zostać przeznaczona do użytku przez obciążeń zasobów dostawcy oraz użytkownika. Nie można używać z wystąpieniem, który jest używany przez innego konsumenta, łącznie z usługami aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL. 

#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.  

- <!-- TBD - IS ASDK --> Usługa App Service można wdrażać tylko w *subskrypcję dostawcy domyślne* w tej chwili. W przyszłej aktualizacji usługi App Service zostanie wdrożona w nowe *pomiaru subskrypcji* która została wprowadzona w usłudze Azure Stack w wersji 1804. Gdy zliczania jest przeznaczony do użytku, wszystkie istniejące wdrożenia będą migrowane do tego nowego typu subskrypcji.

#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!-- #### Identity -->



## <a name="build-201805131"></a>Tworzenie 20180513.1

### <a name="new-features"></a>Nowe funkcje 
Ta kompilacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Nowe subskrypcje administracyjne**. W wersji 1804 istnieją dwóch nowych typów subskrypcji dostępnych w portalu. Te nowe typy subskrypcji są oprócz subskrypcję dostawcy domyślne i są widoczne w przypadku nowych instalacji usługi Azure Stack, począwszy od wersji 1804. *Nie należy używać tych nowych typów subskrypcji z tej wersji usługi Azure Stack*. Ogłosimy dostępności używanie tych typów subskrypcji przy użyciu przyszłej aktualizacji. 

  Te nowe typy subskrypcji są widoczne, ale część większych zmian, aby zabezpieczyć subskrypcję dostawcy domyślne i ułatwiają wdrażanie udostępnionych zasobów, takich jak serwerów do hostingu SQL. 

  Są teraz dostępne typy trzy subskrypcji:  
  - Domyślny dostawca subskrypcji: nadal używać tego typu subskrypcji. 
  - Zliczanie subskrypcji: *należy używać tego typu subskrypcji.*
  - Użycie subskrypcji: *należy używać tego typu subskrypcji*

### <a name="fixed-issues"></a>Rozwiązane problemy
- <!-- IS, ASDK -->  W portalu administracyjnym, masz już odświeżanie kafelka aktualizacji, przed wyświetleniem informacji. 

- <!-- 2050709 - IS, ASDK -->  Można teraz korzystać z portalu administratora do edycji metryk usługi storage, usługę Blob service, Table service i Queue service.

- <!-- IS, ASDK --> W obszarze **sieć**po kliknięciu **połączenia** do skonfigurowania połączenia sieci VPN, **lokacja lokacja (IPsec)** jest obecnie jedyną dostępną opcją. 

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Dodatkowe wersje upłynął limit czasu dzięki tej aktualizacji  
Poniżej są teraz dostępne, ale nie wymagają aktualizacji usługi Azure Stack 1804.
- **Aktualizuj pakiet monitorowania programu Microsoft Azure Stack System Center Operations Manager**. Nowa wersja (1.0.3.0) pakiet Microsoft System Center Operations Manager Monitoring dla usługi Azure Stack jest dostępna dla [Pobierz](https://www.microsoft.com/download/details.aspx?id=55184). W tej wersji można użyć jednostki usługi podczas dodawania podłączonej wdrożenia usługi Azure Stack. Ta wersja zawiera również środowisko zarządzania aktualizacjami, które umożliwia wykonanie czynności korygowania bezpośrednio z programu Operations Manager. Dostępne są także nowe pulpity nawigacyjne, które wyświetlanie dostawców zasobów, jednostek skalowania i skalowanie węzłów jednostki.

- **Nowe usługi Azure Stack Admin PowerShell w wersji 1.3.0**.  Usługa Azure Stack PowerShell 1.3.0 jest teraz dostępna do instalacji. Ta wersja zawiera polecenia dla wszystkich dostawców zasobów administratora do zarządzania usługi Azure Stack.  W tej wersji część zawartości zostaną wycofane z usługi Azure Stack narzędzia GitHub [repozytorium](https://github.com/Azure/AzureStack-Tools). 

   Aby uzyskać szczegółowe informacje dotyczące instalacji, postępuj zgodnie z [instrukcje](.\.\azure-stack-powershell-install.md) lub [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) zawartości dla usługi Azure Stack moduł 1.3.0. 

- **Początkowej wersji programu Azure Stack interfejsu API Rest odwołanie**. [Wykaz interfejsów API dla wszystkich dostawców zasobów administratora usługi Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) zostały opublikowane. 

### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> Nie można zastosować aktualizacje sterowników za pomocą pakietu rozszerzenia producenta OEM w tej wersji usługi Azure Stack.  Nie ma sposobu obejścia tego problemu.
 
- <!-- TBD - IS ASDK --> Możliwość [otworzyć nowe żądanie obsługi z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z w ramach administrator portalu nie jest dostępna. Zamiast tego należy użyć następującego linku:     
    - Azure Stack Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD -  IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

-   <!-- TBD -  IS ASDK --> W portalu administracyjnym, można napotkać alert krytyczny dotyczący składnika Microsoft.Update.Admin. Nazwa alertu, opis i wszystkie są wyświetlane jako korygowania:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 

#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować. Będą one zamknięte automatycznie wraz z upływem czasu.  

#### <a name="marketplace"></a>Portal Marketplace
- Użytkownicy mogą przeglądać pełny rynek bez subskrypcji i można zobaczyć elementy administracyjne, takie jak plany i oferty. Te elementy są nie działa dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD -  IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD -  IS ASDK --> W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Może następnie spróbować Pobierz serwer obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- <!-- TBD -  IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 - IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych. 

#### <a name="networking"></a>Networking
- <!-- 1766332 - IS, ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 2388980 -  IS ASDK --> Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonej maszyny Wirtualnej, a nie nowy.

- <!-- 2292271 - IS ASDK --> Jeśli zostanie podniesiony limitu przydziału dla zasobu sieciowego, który jest częścią oferty i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowych subskrypcjach, które są tworzone po nastąpi zwiększenie limitu przydziału. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz instrukcje [udostępnić planu dodatku](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która zawiera zasoby strefy DNS lub skojarzone z nią zasoby tabeli tras. Aby pomyślnie usunąć subskrypcję, musisz najpierw usunąć strefy DNS i tabelę tras zasobów z subskrypcji dzierżawcy. 


- <!-- 1902460 -  IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 -  IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.
 
- <!-- TBD -  IS ASDK --> Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- <!-- TBD - ASDK --> Bazy danych serwerów hosta musi zostać przeznaczona do użytku przez obciążeń zasobów dostawcy oraz użytkownika. Nie można używać z wystąpieniem, który jest używany przez innego konsumenta, łącznie z usługami aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD -  IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.
 
#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Pobieranie narzędzi usługi Azure Stack z usługi GitHub
- Korzystając z *invoke-webrequest* polecenia cmdlet programu PowerShell do pobrania usługi Azure Stack narzędzi z serwisu Github, otrzymasz komunikat o błędzie:     
    -  *Invoke-webrequest: żądanie zostało przerwane: nie można utworzyć bezpiecznego kanału SSL/TLS.*     

  Ten błąd występuje ze względu na ostatnie wycofywania obsługi GitHub standardów kryptograficznych Tlsv1 i Tlsv1.1 (ustawienie domyślne dla programu PowerShell). Aby uzyskać więcej informacji, zobacz [powiadomienie usuwania słabe standardów kryptograficznych](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Tworzenie 20180302.1

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Nowe funkcje i poprawki wydane dla usługi Azure Stack w wersji 1803 zintegrowanych systemów dotyczą usługi Azure Stack Development Kit. Zobacz [nowe funkcje](.\.\azure-stack-update-1803.md#new-features) i [Rozwiązano problemy](.\.\azure-stack-update-1803.md#fixed-issues) części wersji usługi Azure Stack 1803 zaktualizować informacje o wersji, aby uzyskać szczegółowe informacje.  
> [!IMPORTANT]    
> Niektóre elementy na liście **nowe funkcje** i **Rozwiązano problemy** sekcje mają zastosowanie tylko w systemach zintegrowanych w usłudze Azure Stack.

### <a name="changes"></a>Zmiany
- Sposób można zmienić stanu nowo utworzoną ofertę z *prywatnej* do *publicznych* lub *zlikwidowana* został zmieniony. Aby uzyskać więcej informacji, zobacz [Utwórz ofertę](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- Możliwość [otworzyć nowe żądanie obsługi z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z w ramach administrator portalu nie jest dostępna. Zamiast tego należy użyć następującego linku:     
    - Azure Stack Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2050709 --> W portalu administracyjnym nie jest możliwe edytowanie metryk usługi storage dla usługi Blob service, Table service lub kolejki usługi. Gdy możesz przejść do magazynu, a następnie wybierz obiekt blob, tabeli lub kafelka usługi kolejki, zostanie otwarty nowy blok, wyświetlającego wykresu metryki dla tej usługi. Jeśli wybierzesz opcję edycji w górnej części kafelka wykresu metryki, zostanie otwarty blok Edytuj wykres, ale nie wyświetla opcji, aby edytować metryki.  

- Zostanie wyświetlony **wymagana aktywacja** alert ostrzeżenia, zaleceniem rejestrowania usługi Azure Stack Development Kit. To zachowanie jest oczekiwane.

- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- Na pulpicie nawigacyjnym portalu administracyjnego Aktualizuj Kafelek nie są wyświetlane informacje o aktualizacjach. Aby rozwiązać ten problem, kliknij Kafelek, aby ją odświeżyć.

-   W portalu administracyjnym, można napotkać alert krytyczny dotyczący składnika Microsoft.Update.Admin. Nazwa alertu, opis i wszystkie są wyświetlane jako korygowania:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 

- W portalu administratora usługi i portalu użytkowników, blok przeglądu ładuje się po wybraniu blok Przegląd dla kont magazynu, które zostały utworzone za pomocą starszej wersji interfejsu API (przykład: 2015-06-15). 

  Jako obejście tego problemu, przy użyciu programu PowerShell do uruchamiania **Start ResourceSynchronization.ps1** skrypt w celu przywrócenia dostępu do szczegółów konta magazynu. [Skrypt jest dostępny w witrynie GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)i należy uruchomić przy użyciu poświadczeń administratora usługi na hoście development kit, jeśli używasz ASDK.  

- **Service Health** bloku nie można załadować. Po otwarciu bloku kondycja usługi w portalu administratora lub użytkownika usługi Azure Stack wyświetla komunikat o błędzie i nie ładuje informacje. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć Service Health, ta funkcja nie jest jeszcze dostępna, ale będą realizowane w przyszłych wersjach programu Azure Stack.


#### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
- <!-- 1264761 - IS ASDK -->  Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  

   Zgłoś alert, #1:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji pulsu skaner jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.  

  Zgłoś alert, #2:
   - Nazwa: Rola infrastruktury złej kondycji
   - Ważność: ostrzeżenie
   - SKŁADNIKÓW: Kontroler kondycji
   - Opis: Kontroler kondycji skanera kodów błędów jest niedostępny. To może mieć wpływ na raporty o kondycji i metryki.

  Obydwa alerty można bezpiecznie zignorować. Będą one zamknięte automatycznie wraz z upływem czasu.  

- W portalu administracyjnym usługi Azure Stack, zostanie wyświetlony alert krytyczny o nazwie **do czasu wygaśnięcia certyfikatu zewnętrznego**.  Ten alert można bezpiecznie zignorować i mają wpływ na operacje usługi Azure Stack Development Kit. 


#### <a name="marketplace"></a>Portal Marketplace
- Użytkownicy mogą przeglądać pełny rynek bez subskrypcji i można zobaczyć elementy administracyjne, takie jak plany i oferty. Te elementy są nie działa dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Może następnie spróbować Pobierz serwer obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

-  Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych. 


#### <a name="networking"></a>Networking
- W obszarze **sieć**, po kliknięciu **połączenia** do skonfigurowania połączenia sieci VPN, **sieć wirtualna-sieć wirtualna** jest wymieniony jako typ połączenia to możliwe. Nie należy zaznaczać tej opcji. Obecnie tylko **lokacja lokacja (IPsec)** opcja jest obsługiwana.

- Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonej maszyny Wirtualnej, a nie nowy.



- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.
 
- Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.



#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- Może potrwać do godziny, zanim użytkownicy mogą tworzyć baz danych w nowym SQL lub MySQL jednostki SKU.

- Bazy danych serwerów hosta musi zostać przeznaczona do użytku przez obciążeń zasobów dostawcy oraz użytkownika. Nie można używać z wystąpieniem, który jest używany przez innego konsumenta, łącznie z usługami aplikacji.

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.

#### <a name="app-service"></a>App Service
- Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.
 
#### <a name="usage"></a>Sposób użycia  
- Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Pobieranie narzędzi usługi Azure Stack z usługi GitHub
- Korzystając z *invoke-webrequest* polecenia cmdlet programu PowerShell do pobrania usługi Azure Stack narzędzi z serwisu Github, otrzymasz komunikat o błędzie:     
    -  *Invoke-webrequest: żądanie zostało przerwane: nie można utworzyć bezpiecznego kanału SSL/TLS.*     

  Ten błąd występuje ze względu na ostatnie wycofywania obsługi GitHub standardów kryptograficznych Tlsv1 i Tlsv1.1 (ustawienie domyślne dla programu PowerShell). Aby uzyskać więcej informacji, zobacz [powiadomienie usuwania słabe standardów kryptograficznych](https://githubengineering.com/crypto-removal-notice/).

  Aby rozwiązać ten problem, należy dodać `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` na początku skryptu, aby wymusić konsoli programu PowerShell, aby użyć zabezpieczeń TLSv1.2 podczas pobierania z repozytoriami GitHub.
