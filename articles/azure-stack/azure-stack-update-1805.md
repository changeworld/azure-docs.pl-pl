---
title: Aktualizacja Azure stosu 1805 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1805 do stosu Azure zintegrowanych systemów, łącznie z znane problemy i pobierania aktualizacji.
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
ms.date: 06/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 80ed0d2353fc6ea3a515c0d05475c713920abe46
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295747"
---
# <a name="azure-stack-1805-update"></a>Azure aktualizacji 1805 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszeń i poprawek w pakiecie aktualizacji 1805 znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla stosu Azure zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania    
Numer kompilacji aktualizacji 1805 stosu Azure jest **1.1805.1.47**.  

> [!TIP]  
> Na podstawie opinii klientów, jest dostępna aktualizacja do wersji schematu na użytek systemu Microsoft Azure stosu.  Począwszy od tej aktualizacji 1805, nowego schematu lepiej reprezentuje bieżącą wersję chmury.  
> 
> Wersja schematu jest teraz *Version.YearYearMonthMonth.MinorVersion.BuildNumber* gdzie drugi i trzeci zestawy wskazać wersji i wydania. Na przykład 1805.1 reprezentuje *wersji RTM* 1805 wersji (RTM).  


### <a name="new-features"></a>Nowe funkcje
Ta aktualizacja obejmuje następujące ulepszenia stosu Azure.
<!-- 2297790 - IS, ASDK --> 
- **Stos Azure zawiera teraz *Syslog* klienta** jako *funkcja w wersji zapoznawczej*. Ten klient umożliwia przekazywanie dzienników inspekcji i zabezpieczeń dotyczące infrastruktury stosu Azure w celu Syslog serwera lub zabezpieczeń informacji i zdarzenia (SIEM) oprogramowania do zarządzania zewnętrznej stos Azure. Syslog klienta obsługuje obecnie tylko nieuwierzytelnione połączenia protokołu UDP za pośrednictwem domyślnego portu 514. Ładunek każdego komunikatu dziennika systemu jest sformatowany wspólną formatu zdarzeń (CEF). 

  Aby skonfigurować klienta usługi Syslog, użyj **SyslogServer zestaw** polecenia cmdlet w uprzywilejowanych punktu końcowego. 

  W tej wersji zapoznawczej można napotkać następujące trzy alerty. Gdy prezentowany przez stos Azure, obejmują te alerty *opisy* i *korygowania* wskazówki. 
  - Tytuł: Integralność kodu wyłączanie  
  - Nazwa: Integralności kodu w trybie inspekcji 
  - Nazwa: Konto użytkownika utworzone

  Gdy ta funkcja jest dostępna w wersji zapoznawczej, go nie powinno być stosowane na w środowiskach produkcyjnych.   




### <a name="fixed-issues"></a>Rozwiązane problemy

<!-- # - applicability -->
- Usunięto problem zablokowany [otwierania nowe żądanie pomocy technicznej z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z portalu administratora. Teraz ta opcja działa poprawnie. 

- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Przed rozpoczęciem    

### <a name="prerequisites"></a>Wymagania wstępne
- Zainstaluj stosu Azure [zaktualizować 1804](azure-stack-update-1804.md) przed zastosowaniem aktualizacji 1805 stosu Azure.    
- Przed rozpoczęciem instalacji aktualizacji 1805 Uruchom [AzureStack testu](azure-stack-diagnostic-test.md) Aby zweryfikować stan stosu Azure i rozwiązać wszystkie problemy z działaniem znaleziono. Również przejrzeć aktywne alerty i rozwiązania, które wymagają akcji. 

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji   
- Podczas instalacji aktualizacji 1805, użytkownik może widzieć alerty z tytułem *błąd — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu aktualizacji do 1805.   

- <!-- 2489559 - IS --> Nie należy próbować tworzyć maszyn wirtualnych podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, seSe [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Czynności po aktualizacji
Po zakończeniu instalacji 1805 Zainstaluj wszystkie odpowiednie poprawki. Uzyskać więcej informacji, zobacz następujące artykuły bazy wiedzy knowledge base, a także naszych [obsługi zasad](azure-stack-servicing-policy.md).  
 - [KB 4340474 - Azure stosu poprawkę 1.1805.4.53](https://support.microsoft.com/en-us/help/4340474).


## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy występujące po instalacji w tej wersji kompilacji.

### <a name="portal"></a>Portal  
- <!-- 2551834 - IS, ASDK --> Po wybraniu **omówienie** dla konta magazynu w portalach administratora lub użytkownika informacji z *Essentials* nie są wyświetlane w okienku.  W okienku Essentials Wyświetla informacje o koncie, takie jak jego *grupy zasobów*, *lokalizacji*, i *identyfikator subskrypcji*.  Omówienie inne opcje są dostępne, takich jak *usług* i *monitorowanie*, również w opcji *Otwórz w Eksploratorze* lub *usunięcie konta magazynu* . 

  Aby wyświetlić dostępne informacje, należy użyć [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell. 

- <!-- 2551834 - IS, ASDK --> Po wybraniu **tagi** dla konta magazynu w portalach administrator lub użytkownik, nie udało się załadować informacje, a nie są wyświetlane.  

  Aby wyświetlić dostępne informacje, należy użyć [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell.


- <!-- 2332636 - IS -->  Jeśli używasz usług AD FS dla systemu tożsamości stosu Azure i aktualizacji do tej wersji programu Azure stosu domyślnego właściciela subskrypcji dostawcy domyślnego jest ustawiany na wbudowanych **CloudAdmin** użytkownika.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalacza automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

- <!-- TBD - IS ASDK --> Niektóre typy subskrypcji administracyjne nie są dostępne.  Po uaktualnieniu stosu Azure do tej wersji subskrypcji dwa typy, które były [wprowadzonym w wersji 1804](azure-stack-update-1804.md#new-features) nie są widoczne w konsoli. Jest to oczekiwane. Typy subskrypcji niedostępne są *zliczania subskrypcji*, i *subskrypcji zużycie*. Te typy subskrypcji są widoczne w nowych środowisk Azure stosu, począwszy od wersji 1804, ale nie jest jeszcze gotowa do użycia. Można nadal używać *domyślny dostawca* typ subskrypcji.  

- <!-- 2403291 - IS ASDK --> Nie masz użyj poziomego paska przewijania wzdłuż dolnej części portali administratora i użytkownika. Jeśli nie masz dostępu do poziomego paska przewijania, za pomocą nawigacyjnej na przejście do poprzedniego bloku w portalu po wybraniu nazwy bloku mają być wyświetlane na liście stron nadrzędnych znajdujących się u góry po lewej portalu.
  ![Nawigacją](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyną tego problemu jest błąd podczas instalacji aktualizacji, które powoduje, że aktualizacja niepoprawnie zgłaszana jako powiodło się. Jeśli ten problem występuje, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- <!-- TBD - IS --> Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- <!-- TBD - IS ASDK --> Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- <!-- TBD - IS ASDK --> Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


### <a name="health-and-monitoring"></a>Monitorowania kondycji i
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

  Obydwa alerty można bezpiecznie zignorować i będzie automatycznie zamknąć wraz z upływem czasu.  

- <!-- 2368581 - IS. ASDK --> Operator stosu Azure, jeśli otrzymasz alert małej ilości pamięci i maszyn wirtualnych dzierżawcy nie można wdrożyć przy użyciu *błąd tworzenia sieci szkieletowej maszyny Wirtualnej*, istnieje możliwość, że sygnatura stosu Azure jest za mało dostępnej pamięci. Użyj [Azure stosu Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie pojemności dla obciążeń. 


### <a name="compute"></a>Wystąpienia obliczeniowe
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


- <!-- TBD - IS ASDK --> Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- <!-- TBD - IS --> Po utworzeniu zestawem dostępności w portalu, przechodząc do **nowy** > **obliczeniowe** > **zestawu dostępności**, można tworzyć tylko z domeny błędów i Aktualizacja domeny 1 zestawu dostępności. Jako obejście, podczas tworzenia nowej maszyny wirtualnej, Utwórz dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- <!-- TBD - IS ASDK --> Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii DS maszyny Wirtualnej. Serii DS maszyny wirtualne mogą obsługiwać jako wiele dysków z danymi jako konfiguracji platformy Azure.

- <!-- TBD - IS ASDK --> Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

- <!-- TBD - IS ASDK --> Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 IS ASDK --> Diagnostyka maszyny Wirtualnej systemu Linux nie jest obsługiwana w stosie Azure. Podczas wdrażania maszyny Wirtualnej systemu Linux z włączoną diagnostykę maszyny Wirtualnej, wdrożenie zakończy się niepowodzeniem. Wdrożenie nie powiedzie się także włączenie metryk podstawowej maszyny Wirtualnej systemu Linux za pomocą ustawień diagnostycznych.  


### <a name="networking"></a>Networking
- <!-- TBD - IS ASDK --> Nie można utworzyć trasy zdefiniowane przez użytkownika w jednym z portalu administratora lub użytkownika. Aby uniknąć tego problemu, należy użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS ASDK --> W obszarze **sieci**, jeśli klikniesz przycisk **Tworzenie bramy sieci VPN** do skonfigurowania połączenia sieci VPN, **oparta na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w stosie Azure.

- <!-- 2388980 - IS ASDK --> Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia oryginalna maszyna wirtualna, a nie nowy.

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


### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL

- <!-- TBD - IS --> Tylko dostawcy zasobów jest obsługiwane tworzenie elementów na serwerach, że host SQL lub MySQL. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może spowodować niezgodne stanu.  

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL.


> [!NOTE]  
> <!-- TBD - IS --> Po aktualizacji do 1805 stosu Azure, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożona.  Firma Microsoft zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i MySQL. Podobnie jak stosu Azure sekwencyjnie Zastosuj aktualizacje do dostawców zasobów SQL i MySQL. Na przykład jeśli używasz wersji 1803, najpierw zastosować wersji 1804, a następnie zaktualizuj do 1805.      
>   
> Instalowanie aktualizacji 1805 nie wpływa na użycie bieżącej bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz danych użytkowników w swoich baz danych nie jest dotknąć i pozostanie dostępny.    



### <a name="app-service"></a>App Service
- <!-- 2352906 - IS ASDK --> Użytkownicy muszą zarejestrować dostawcy zasobów magazynu przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

- <!-- 2489178 - IS ASDK --> Aby skalować w poziomie infrastruktury (pracowników, zarządzania, role frontonu), musi być programu PowerShell, zgodnie z opisem w informacjach o wersji dla obliczania.

- <!-- TBD - IS ASDK --> Usługi aplikacji można wdrożyć tylko do *subskrypcji domyślny dostawca* w tej chwili. W ramach przyszłej aktualizacji usługi aplikacji — zostanie wdrożona w nowej *zliczania subskrypcji* który został wprowadzony w 1804 stosu Azure. Gdy zliczania jest obsługiwane w przypadku użycia, wszystkie istniejące wdrożenia będą migrowane do ten nowy typ subskrypcji.


### <a name="usage"></a>Sposób użycia  
- <!-- TBD - IS ASDK --> Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji 1805 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i wznowić aktualizacji, zobacz [monitorowania aktualizacji w stosie Azure przy użyciu punktu końcowego uprzywilejowanych](azure-stack-monitor-update.md).
- Omówienie zarządzania aktualizacjami w stosie Azure, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md).
- Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą stosu Azure, zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md).
