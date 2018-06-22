---
title: Aktualizacja Azure stosu 1804 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1804 stosu Azure zintegrowanych systemów, znane problemy i pobierania aktualizacji.
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
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 2c2813a7f2d909a23c8f5d4f5ac0280b3f932ba6
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "34700128"
---
# <a name="azure-stack-1804-update"></a>Azure aktualizacji 1804 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszenia i poprawki w pakiecie aktualizacji 1804 znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla stosu Azure zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania    
Numer kompilacji aktualizacji 1804 stosu Azure jest **20180513.1**.   

### <a name="new-features"></a>Nowe funkcje
Ta aktualizacja obejmuje następujące ulepszenia stosu Azure.

- <!-- 15028744 - IS -->  **Visual Studio obsługę odłączonego wdrożeń stosu Azure przy użyciu usług AD FS**. W programie Visual Studio, możesz teraz dodawać subskrypcje i uwierzytelniania za pomocą usług AD FS federacyjnych poświadczeń użytkownika. 
 
- <!-- 1779474, 1779458 - IS --> **Użyj maszyn wirtualnych serii Av2 i F**. Stos Azure można teraz używać maszyn wirtualnych na podstawie Av2 serii i F-series rozmiarów maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych obsługiwanych w stosie Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). 

- <!-- 1759172 - IS, ASDK --> **Nowe subskrypcje administracyjne**. Z 1804 istnieją dwa typy nowej subskrypcji dostępne w portalu. Te nowe typy subskrypcji są oprócz domyślny dostawca subskrypcji i są widoczne w przypadku nowych instalacji Azure stosu, począwszy od wersji 1804. *Nie należy używać tych nowych typów subskrypcji z tej wersji programu Azure stosu*. Firma Microsoft będzie poinformować o udostępnieniu używanie tych typów subskrypcji przy użyciu przyszłej aktualizacji. 

  Po zaktualizowaniu stosu Azure do wersji 1804 dwóch nowych typów subskrypcji nie są widoczne. Jednak nowych wdrożeń stosu Azure zintegrowanych systemów i instalacje wersji Azure stosu Development Kit 1804 lub nowszym mają dostęp do wszystkich typów trzy subskrypcji.  

  Te nowe typy subskrypcji są częścią większych zmian do zabezpieczania domyślny dostawca subskrypcji i ułatwiające wdrażanie udostępnionych zasobów, takich jak serwery SQL Hosting. Przy wdrażaniu więcej części tej zmiany większe przy przyszłych aktualizacji platformy Azure stosu, zasoby wdrożone w tych nowych typów subskrypcji mogą zostać utracone. 

  Teraz widoczne subskrypcji trzy typy to:  
  - Domyślny dostawca subskrypcji: nadal używać tego typu subskrypcji. 
  - Pomiaru subskrypcji: *nie należy używać tego typu subskrypcji.*
  - Zużycie subskrypcji: *nie należy używać tego typu subskrypcji*

  



## <a name="fixed-issues"></a>Rozwiązane problemy

- <!-- IS, ASDK -->  W portalu administracyjnym, masz już odświeżanie kafelka aktualizacji przed wyświetleniem informacji.
 
- <!-- 2050709  -->  Można teraz korzystać z portalu administratora do edytowania metryki magazynu dla usługi obiektów Blob, usługi tabel i kolejek usługi.
 
- <!-- IS, ASDK --> W obszarze **sieci**, po kliknięciu **połączenia** do skonfigurowania połączenia sieci VPN, **lokacja lokacja (IPsec)** jest obecnie jedyną dostępną opcją.

- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure.

## <a name="additional-releases-timed-with-this-update"></a>Dodatkowe wersje upłynął dzięki tej aktualizacji  
Poniżej są teraz dostępne, ale nie wymagają aktualizacji stosu Azure 1804.
- **Aktualizacja do pakiet monitorowania programu Microsoft Azure stosu System Center Operations Manager**. Nowa wersja (1.0.3.0) programu Microsoft System Center Operations Manager monitorowania Pack stosu Azure jest dostępna dla [Pobierz](https://www.microsoft.com/download/details.aspx?id=55184). W tej wersji można użyć nazwy główne usług, podczas dodawania podłączonej wdrożenia stosu Azure. Ta wersja zawiera też środowisko zarządzania aktualizacjami, które można podjąć akcję naprawczą bezpośrednio z poziomu programu Operations Manager. Istnieją nowe pulpity nawigacyjne, które wyświetlić dostawców zasobów, jednostek skalowania i skalowanie węzłów jednostki.

- **Nowe Azure stosu wersja programu PowerShell Admin 1.3.0**.  Program Azure PowerShell stosu 1.3.0 jest teraz dostępna do instalacji. Ta wersja zawiera polecenia dla wszystkich dostawców zasobów administratora do zarządzania Azure stosu.  W tej wersji, niektóre zawartości zostaną wycofane z usługi GitHub z narzędzia Azure stosu [repozytorium](https://github.com/Azure/AzureStack-Tools). 

   Aby uzyskać szczegółowe informacje dotyczące instalacji, należy wykonać [instrukcje](azure-stack-powershell-install.md) lub [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) zawartości dla usługi Azure stosu modułu 1.3.0. 

- **Początkowa wersja Azure stosu interfejsu API Rest odwołania**. [Dokumentacja interfejsu API dla wszystkich dostawców zasobów Azure stosu Admin](https://docs.microsoft.com/rest/api/azure-stack/) zostały opublikowane. 


## <a name="before-you-begin"></a>Przed rozpoczęciem    

### <a name="prerequisites"></a>Wymagania wstępne
- Zainstaluj stosu Azure [zaktualizować 1803](azure-stack-update-1803.md) przed zastosowaniem aktualizacji 1804 stosu Azure.    

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji   
- Podczas instalacji aktualizacji 1804, użytkownik może widzieć alerty z tytułem *błąd — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu aktualizacji do 1804.   
 
- <!-- TBD - IS --> Nie należy próbować tworzyć maszyn wirtualnych podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Czynności po aktualizacji
*Nie istnieją żadne czynności po aktualizacji dla aktualizacji 1804.*



### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy występujące po instalacji w kompilacji **20180513.1**.

#### <a name="portal"></a>Portal
- <!-- 1272111 - IS --> Po zainstalowaniu lub aktualizacji do tej wersji programu Azure stosu nie można wyświetlić jednostki skalowania stosu Azure w portalu administracyjnym.  
  Obejście problemu: Użyj programu PowerShell do wyświetlania informacji o jednostkach skalowania. Aby uzyskać więcej informacji, zobacz [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) zawartości dla usługi Azure stosu modułu 1.3.0. 

- <!-- 2332636 - IS -->  Jeśli używasz usług AD FS dla systemu tożsamości stosu Azure i aktualizacji do tej wersji programu Azure stosu domyślnego właściciela subskrypcji dostawcy domyślnego jest ustawiany na wbudowanych **CloudAdmin** użytkownika.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalacza automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

- <!-- TBD - IS ASDK --> Niektóre typy subskrypcji administracyjne nie są dostępne.  Po uaktualnieniu stosu Azure do tej wersji subskrypcji dwa typy, które były [wprowadzonym w wersji 1804](#new-features) nie są widoczne w konsoli. Jest to oczekiwane. Typy subskrypcji niedostępne są *zliczania subskrypcji*, i *subskrypcji zużycie*. Te typy subskrypcji są widoczne w nowych środowisk Azure stosu, począwszy od wersji 1804, ale nie jest jeszcze gotowa do użycia. Można nadal używać *domyślny dostawca* typ subskrypcji.  


- <!-- TBD -  IS ASDK -->Możliwość [aby otworzyć nowe żądanie pomocy technicznej z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z wewnątrz administrator portalu nie jest dostępna. Zamiast tego użyj następującego łącza:     
    - Azure stosu Użyj zintegrowanych systemów https://aka.ms/newsupportrequest.

- <!-- 2403291 - IS ASDK --> Nie masz użyj poziomego paska przewijania wzdłuż dolnej części portali administratora i użytkownika. Jeśli nie masz dostępu do poziomego paska przewijania, za pomocą nawigacyjnej na przejście do poprzedniego bloku w portalu po wybraniu nazwy bloku mają być wyświetlane na liście stron nadrzędnych znajdujących się u góry po lewej portalu.
  ![Nawigacją](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyną tego problemu jest błąd podczas instalacji aktualizacji, które powoduje, że aktualizacja niepoprawnie zgłaszana jako powiodło się. Jeśli ten problem występuje, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- <!-- TBD - IS --> Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- <!-- TBD - IS ASDK --> Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- <!-- TBD - IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- <!-- TBD - IS ASDK --> W portalu administracyjnym, zostanie wyświetlony alert krytyczny dotyczący *Microsoft.Update.Admin* składnika. Nazwa alertu, opis i korygowania wszystkich wyświetlania jako:  
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
 

#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD - IS --> Podczas wybierania rozmiaru maszyny wirtualnej dla wdrożenia maszyny wirtualnej, niektóre rozmiary serii F maszyny Wirtualnej nie są widoczne jako część selektora rozmiar podczas tworzenia maszyny Wirtualnej. Następujących rozmiarów maszyn wirtualnych nie pojawiają się w selektorze: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Jako obejście użyj jednej z następujących metod można wdrożyć maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, który ma być używany.

  - **Szablonu usługi Azure Resource Manager:** w przypadku użycia szablonu ustawić *vmSize* w szablonie równe żądany rozmiar maszyny Wirtualnej. Na przykład następujące służy do wdrażania maszyny Wirtualnej, która używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** można użyć [tworzenia maszyny wirtualnej az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Środowiska PowerShell:** przy użyciu programu PowerShell, można użyć [AzureRMVMConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


- <!-- TBD - IS ASDK --> Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- <!-- TBD - IS --> Po utworzeniu zestawem dostępności w portalu, przechodząc do **nowy** > **obliczeniowe** > **zestawu dostępności**, można tworzyć tylko z domeny błędów i Aktualizacja domeny 1 zestawu dostępności. Jako obejście, podczas tworzenia nowej maszyny wirtualnej, Utwórz dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- <!-- TBD - IS ASDK --> Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii D maszyny Wirtualnej. Wszystkie obsługiwane D serii maszyn wirtualnych może obsłużyć jako wiele dysków z danymi jako konfiguracji platformy Azure.

- <!-- TBD - IS ASDK --> Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

- <!-- TBD - IS ASDK --> Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 IS ASDK --> Diagnostyka maszyny Wirtualnej systemu Linux nie jest obsługiwana w stosie Azure. Podczas wdrażania maszyny Wirtualnej systemu Linux z włączoną diagnostykę maszyny Wirtualnej, wdrożenie zakończy się niepowodzeniem. Wdrożenie nie powiedzie się także włączenie metryk podstawowej maszyny Wirtualnej systemu Linux za pomocą ustawień diagnostycznych.  


#### <a name="networking"></a>Networking
- <!-- 1766332 - IS ASDK --> W obszarze **sieci**, jeśli klikniesz przycisk **Tworzenie bramy sieci VPN** do skonfigurowania połączenia sieci VPN, **oparta na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w stosie Azure.

- <!-- 2388980 - IS ASDK --> Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy.

- <!-- 2292271 - IS ASDK --> Po podniesieniu limitu przydziału dla zasobu sieciowego, będącej częścią ofertę i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowe subskrypcje, które są tworzone po zwiększeniu limit przydziału. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz temat jak [udostępnić plan dodatek](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Nie można usunąć subskrypcji, która ma zasobów strefy DNS lub zasobów tabeli tras skojarzonych z nim. Aby pomyślnie usunąć subskrypcję, należy najpierw usunąć strefy DNS i tabeli tras zasobów z subskrypcji dzierżawcy. 
  

- <!-- 1902460 - IS ASDK --> Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- <!-- 16309153 - IS ASDK --> W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.

- <!-- TBD - IS ASDK --> Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.

- <!-- 2096388 IS --> Nie można korzystać z portalu administratora, można zaktualizować reguł sieciowej grupy zabezpieczeń. 

    Obejście usługi aplikacji: Jeśli potrzebujesz pulpitu zdalnego do wystąpień kontrolera, zmodyfikować zasady zabezpieczeń w ramach grup zabezpieczeń sieci przy użyciu programu PowerShell.  Poniżej przedstawiono przykłady *Zezwalaj*, a następnie Przywróć konfigurację, aby *odmowy*:  
    
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

    - *Odmów:*

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

- <!-- TBD - IS --> Tylko dostawcy zasobów jest obsługiwane tworzenie elementów na serwerach, że host SQL lub MySQL. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może spowodować niezgodne stanu.  

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Po aktualizacji do 1804 stosu Azure, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożona.  Firma Microsoft zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i MySQL. Podobnie jak stosu Azure sekwencyjnie Zastosuj aktualizacje do dostawców zasobów SQL i MySQL.  Na przykład jeśli używasz wersji 1802 najpierw zastosować wersji 1803, a następnie zaktualizuj do 1804.      
>   
> Instalowanie aktualizacji 1804 nie wpływa na użycie bieżącej bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz danych użytkowników w swoich baz danych nie jest dotknąć i pozostanie dostępny.    



#### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy muszą zarejestrować dostawcy zasobów magazynu przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- TBD - IS ASDK --> Aby skalować w poziomie infrastruktury (pracowników, zarządzania, role frontonu), musi być programu PowerShell, zgodnie z opisem w informacjach o wersji dla obliczania.

- <!-- TBD - IS ASDK --> Usługi aplikacji można wdrożyć tylko do **domyślny dostawca subskrypcji** w tej chwili.  W przyszłej aktualizacji usługi aplikacji — zostanie wdrożona na nową subskrypcję pomiaru wprowadzone w 1804 stosu Azure i wszystkie istniejące wdrożenia będą migrowane do tej nowej subskrypcji również.

#### <a name="usage"></a>Sposób użycia  
- <!-- TBD - IS ASDK --> Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji 1804 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i wznowić aktualizacji, zobacz [monitorowania aktualizacji w stosie Azure przy użyciu punktu końcowego uprzywilejowanych](azure-stack-monitor-update.md).
- Omówienie zarządzania aktualizacjami w stosie Azure, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md).
- Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą stosu Azure, zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md).
