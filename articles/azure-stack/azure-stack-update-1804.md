---
title: Aktualizacja usługi Azure Stack w wersji 1804 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w wersji 1804 aktualizacji dla usługi Azure Stack zintegrowanych systemów, znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 0190298cbf6352feeb71e365f5815e174c9e30cc
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413520"
---
# <a name="azure-stack-1804-update"></a>Aktualizacja usługi Azure Stack w wersji 1804

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano ulepszeń i poprawek w pakiecie aktualizacji 1804, znane problemy dotyczące tej wersji i gdzie można pobrać aktualizację. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania    
Numerem kompilacji aktualizacji usługi Azure Stack w wersji 1804 **20180513.1**.   

### <a name="new-features"></a>Nowe funkcje
Ta aktualizacja obejmuje następujące ulepszenia usługi Azure Stack.

- <!-- 15028744 - IS -->  **Visual Studio Obsługa odłączonych wdrożenia usługi Azure Stack przy użyciu usług AD FS**. W programie Visual Studio możesz teraz dodawać subskrypcje i uwierzytelniania za pomocą usług AD FS federacyjnych poświadczeń użytkownika. 
 
- <!-- 1779474, 1779458 - IS --> **Użyj maszyn wirtualnych serii Av2 i F**. Usługa Azure Stack można teraz używać maszyn wirtualnych na podstawie rozmiarów maszyn wirtualnych serii Av2 i serii F. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych obsługiwanych w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Nowe subskrypcje administracyjne**. W wersji 1804 istnieją dwóch nowych typów subskrypcji dostępnych w portalu. Te nowe typy subskrypcji są oprócz subskrypcję dostawcy domyślne i są widoczne w przypadku nowych instalacji usługi Azure Stack, począwszy od wersji 1804. *Nie należy używać tych nowych typów subskrypcji z tej wersji usługi Azure Stack*. Ogłosimy dostępności używanie tych typów subskrypcji przy użyciu przyszłej aktualizacji. 

  Po zaktualizowaniu usługi Azure Stack w wersji 1804 dwóch nowych typów subskrypcji nie są widoczne. Jednak nowe wdrożenia usługi Azure Stack zintegrowane systemy i instalacji programu Azure Stack Development Kit w wersji 1804 lub nowszej, mają dostęp do wszystkich typów trzy subskrypcji.  

  Te nowe typy subskrypcji są częścią większych zmian, aby zabezpieczyć subskrypcję dostawcy domyślne i ułatwiają wdrażanie udostępnionych zasobów, takich jak serwerów do hostingu SQL. Ponieważ nieustannie dodajemy więcej części tej większe zmiany z przyszłymi aktualizacjami do usługi Azure Stack zasobów wdrożonych w ramach tych nowych typów subskrypcji mogą zostać utracone. 

  Są teraz widoczne typy trzy subskrypcji:  
  - Domyślny dostawca subskrypcji: nadal używać tego typu subskrypcji. 
  - Zliczanie subskrypcji: *należy używać tego typu subskrypcji.*
  - Użycie subskrypcji: *należy używać tego typu subskrypcji*

  



## <a name="fixed-issues"></a>Rozwiązane problemy

- <!-- IS, ASDK -->  W portalu administracyjnym, masz już odświeżanie kafelka aktualizacji, przed wyświetleniem informacji.
 
- <!-- 2050709  -->  Można teraz korzystać z portalu administratora do edycji metryk usługi storage, usługę Blob service, Table service i Queue service.
 
- <!-- IS, ASDK --> W obszarze **sieć**po kliknięciu **połączenia** do skonfigurowania połączenia sieci VPN, **lokacja lokacja (IPsec)** jest obecnie jedyną dostępną opcją.

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Dodatkowe wersje upłynął limit czasu dzięki tej aktualizacji  
Poniżej są teraz dostępne, ale nie wymagają aktualizacji usługi Azure Stack 1804.
- **Aktualizuj pakiet monitorowania programu Microsoft Azure Stack System Center Operations Manager**. Nowa wersja (1.0.3.0) pakiet Microsoft System Center Operations Manager Monitoring dla usługi Azure Stack jest dostępna dla [Pobierz](https://www.microsoft.com/download/details.aspx?id=55184). W tej wersji można użyć jednostki usługi podczas dodawania podłączonej wdrożenia usługi Azure Stack. Ta wersja zawiera również środowisko zarządzania aktualizacjami, które umożliwia wykonanie czynności korygowania bezpośrednio z programu Operations Manager. Dostępne są także nowe pulpity nawigacyjne, które wyświetlanie dostawców zasobów, jednostek skalowania i skalowanie węzłów jednostki.

- **Nowe usługi Azure Stack Admin PowerShell w wersji 1.3.0**.  Usługa Azure Stack PowerShell 1.3.0 jest teraz dostępna do instalacji. Ta wersja zawiera polecenia dla wszystkich dostawców zasobów administratora do zarządzania usługi Azure Stack.  W tej wersji część zawartości zostaną wycofane z usługi Azure Stack narzędzia GitHub [repozytorium](https://github.com/Azure/AzureStack-Tools). 

   Aby uzyskać szczegółowe informacje dotyczące instalacji, postępuj zgodnie z [instrukcje](azure-stack-powershell-install.md) lub [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) zawartości dla usługi Azure Stack moduł 1.3.0. 

- **Początkowej wersji programu Azure Stack interfejsu API Rest odwołanie**. [Wykaz interfejsów API dla wszystkich dostawców zasobów administratora usługi Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) zostały opublikowane. 


## <a name="before-you-begin"></a>Przed rozpoczęciem    

### <a name="prerequisites"></a>Wymagania wstępne
- Instalowanie usługi Azure Stack [aktualizacji 1803](azure-stack-update-1803.md) przed zastosowaniem aktualizacji usługi Azure Stack w wersji 1804.  
  
- Zainstaluj najnowszy dostępny [aktualizacja lub poprawka dla wersji 1803](azure-stack-update-1803.md#post-update-steps). 


### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji   
- Podczas instalacji aktualizacji 1804, użytkownik może widzieć alerty z tytułem *błędu — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu aktualizacji do wersji 1804.   
 
- <!-- TBD - IS --> Nie należy próbować tworzyć maszyny wirtualne podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Kroki po aktualizacji
Po zainstalowaniu wersji 1804 Zainstaluj wszystkie odpowiednie poprawki. Uzyskać więcej informacji, zobacz następujące artykuły bazy wiedzy knowledge base, a także naszego [obsługi zasad](azure-stack-servicing-policy.md).  
 - [KB 4344114 — usługa Azure Stack poprawkę 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy po instalacji dla kompilacji **20180513.1**.

#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> Nie można zastosować aktualizacje sterowników za pomocą pakietu rozszerzenia producenta OEM w tej wersji usługi Azure Stack.  Nie ma sposobu obejścia tego problemu.

- <!-- 1272111 - IS --> Po zainstalowaniu lub zaktualizowaniu do tej wersji usługi Azure Stack, nie można wyświetlić jednostki skalowania usługi Azure Stack w portalu administracyjnym.  
  Obejście: Użyj programu PowerShell do wyświetlania informacji na temat skalowania jednostek. Aby uzyskać więcej informacji, zobacz [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) zawartości dla usługi Azure Stack moduł 1.3.0. 

- <!-- 2332636 - IS -->  Korzystając z usług AD FS dla systemu tożsamości w usłudze Azure Stack i aktualizacji do tej wersji usługi Azure Stack, domyślnym właścicielem domyślną subskrypcję dostawcy jest resetowany do wbudowanej **CloudAdmin** użytkownika.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalania automatyzacji, aby skonfigurować oświadczeń zaufania dostawcy w usłudze Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

- <!-- TBD - IS ASDK --> Niektórych typów subskrypcji administracyjne nie są dostępne.  Po uaktualnieniu usługi Azure Stack w tej wersji typów dwóch subskrypcji, które były [wprowadzonym w wersji 1804](#new-features) nie są widoczne w konsoli. Jest to oczekiwane. Typy subskrypcji niedostępne są *pomiaru subskrypcji*, i *subskrypcji zużycie*. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać *domyślny dostawca* typu subskrypcji.  


- <!-- TBD -  IS ASDK -->Możliwość [otworzyć nowe żądanie obsługi z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z w ramach administrator portalu nie jest dostępna. Zamiast tego należy użyć następującego linku:     
    - Użyć zintegrowane systemy usługi Azure Stack https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyny tego problemu jest błąd podczas instalowania aktualizacji, który powoduje, że aktualizacja nieprawidłowo zgłaszane jako pomyślne. Jeśli wystąpi ten problem, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- <!-- TBD - IS --> Możesz zobaczyć pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz ikonę koła zębatego w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- <!-- TBD - IS ASDK --> Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- <!-- TBD - IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- <!-- TBD - IS ASDK --> W portalu administracyjnym, zostanie wyświetlony alert krytyczny dotyczący *Microsoft.Update.Admin* składnika. Nazwa alertu, opis i wszystkie są wyświetlane jako korygowania:  
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
 

#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD - IS --> Wybierając rozmiar maszyny wirtualnej do wdrażania maszyn wirtualnych, niektóre rozmiary serii F maszyn wirtualnych nie są widoczne jako część selektor rozmiar podczas tworzenia maszyny Wirtualnej. Następujących rozmiarów maszyn wirtualnych nie są wyświetlane w selektorze: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Obejść ten problem należy użyć jednej z następujących metod do wdrożenia maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, którego chcesz użyć.

  - **Szablon usługi Azure Resource Manager:** gdy używasz szablonu, ustaw *vmSize* w szablonie, aby równa żądany rozmiar maszyny Wirtualnej. Na przykład, następujące służy do wdrażania maszyn wirtualnych, który używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Interfejs wiersza polecenia platformy Azure:** można użyć [tworzenie az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Program PowerShell:** przy użyciu programu PowerShell, można użyć [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- <!-- TBD - IS --> Po utworzeniu zestaw dostępności w portalu, przechodząc do **New** > **obliczenia** > **zestawu dostępności**, można tworzyć tylko zestaw dostępności z domeny błędów i domeny aktualizacji 1. Obejść ten problem, tworząc nową maszynę wirtualną, utworzyć dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- <!-- TBD - IS ASDK --> Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- <!-- TBD - IS ASDK --> W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Może następnie spróbować Pobierz serwer obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

- <!-- TBD - IS ASDK --> Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 IS ASDK --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  


#### <a name="networking"></a>Networking
- <!-- 1766332 - IS ASDK --> W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

- <!-- 2388980 - IS ASDK --> Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonej maszyny Wirtualnej, a nie nowy.

- <!-- 2292271 - IS ASDK --> Jeśli zostanie podniesiony limitu przydziału dla zasobu sieciowego, który jest częścią oferty i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowych subskrypcjach, które są tworzone po nastąpi zwiększenie limitu przydziału. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz instrukcje [udostępnić planu dodatku](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która zawiera zasoby strefy DNS lub skojarzone z nią zasoby tabeli tras. Aby pomyślnie usunąć subskrypcję, musisz najpierw usunąć strefy DNS i tabelę tras zasobów z subskrypcji dzierżawcy. 
  

- <!-- 1902460 - IS ASDK --> Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- <!-- 16309153 - IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- TBD - IS ASDK --> Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.

- <!-- 2096388 IS --> Aby zaktualizować reguły sieciowej grupy zabezpieczeń nie może korzystać z portalu administratora. 

    Obejście dla usługi App Service: Jeśli zachodzi potrzeba pulpitu zdalnego do wystąpień kontrolera, zmodyfikować reguły zabezpieczeń w ramach grupy zabezpieczeń sieci przy użyciu programu PowerShell.  Poniżej przedstawiono przykłady jak *Zezwalaj na*, a następnie Przywróć konfigurację, aby *Odmów*:  
    
    - *Zezwalaj na:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Odmowa:*

        ```powershell
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```

#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL

- <!-- TBD - IS --> Tworzenie elementów na serwerach tego hosta SQL lub MySQL jest obsługiwane tylko dostawcy zasobów. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może prowadzić do niezgodnego stanu.  

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Po aktualizacji do usługi Azure Stack w wersji 1804, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożone.  Zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i bazy danych MySQL. Np. usługi Azure Stack należy zastosować aktualizacje do dostawcy zasobów SQL i MySQL sekwencyjnie.  Na przykład jeśli używasz wersji 1802, najpierw zastosowanie wersji 1803, a następnie zaktualizuj do wersji 1804.      
>   
> Instalacja aktualizacji 1804 nie ma wpływu na bieżące użycie bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz dane użytkowników w swoich baz danych nie jest Dotyk i pozostanie dostępne.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.

- <!-- TBD - IS ASDK --> Usługa App Service można wdrażać tylko w **domyślne subskrypcję dostawcy** w tej chwili.  W przyszłej aktualizacji usługi App Service zostanie wdrożona na nową subskrypcję pomiaru wprowadzone w usłudze Azure Stack w wersji 1804 i wszystkie istniejące wdrożenia będą migrowane do tej nowej subskrypcji również.

#### <a name="usage"></a>Sposób użycia  
- <!-- TBD - IS ASDK --> Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji usługi Azure Stack w wersji 1804 z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
