---
title: Aktualizacja usługi Azure Stack 1805 | Dokumentacja firmy Microsoft
description: Informacje na temat nowości w aktualizacji 1805 dla usługi Azure Stack zintegrowane systemy, w tym znanych problemów i umożliwiające pobranie aktualizacji.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 7cf3e08caad93bb7fb7fd221b6fdb9b2fc39397a
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816739"
---
# <a name="azure-stack-1805-update"></a>Aktualizacja usługi Azure Stack 1805

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano ulepszeń i poprawek dotyczących w pakiecie aktualizacji 1805, znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania    
Numerem kompilacji aktualizacji usługi Azure Stack 1805 **1.1805.1.47**.  

> [!TIP]  
> Na podstawie opinii klientów, jest dostępna aktualizacja do wersji schematu używane dla usługi Microsoft Azure Stack.  Począwszy od tej aktualizacji 1805, nowy schemat lepiej reprezentuje bieżącą wersję w chmurze.  
> 
> Wersja schematu jest teraz *Version.YearYearMonthMonth.MinorVersion.BuildNumber* gdzie zestawy drugi i trzeci wskazywać wersji i wydania. Na przykład 1805.1 reprezentuje *wydania do produkcji* wersji 1805 (RTM).  


### <a name="new-features"></a>Nowe funkcje
Ta aktualizacja obejmuje następujące ulepszenia usługi Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **Usługa Azure Stack obejmuje teraz *Syslog* klienta** jako *funkcja w wersji zapoznawczej*. Ten klient umożliwia przekazywanie dzienników inspekcji i zabezpieczeń związane z infrastruktury Azure Stack w celu Syslog serwera lub zabezpieczeń informacjami i zdarzeniami (SIEM) oprogramowania do zarządzania zewnętrznej dla usługi Azure Stack. Klient usługi Syslog obsługuje obecnie tylko nieuwierzytelnione połączenia protokołu UDP przez domyślny port 514. Ładunek każdego komunikatu dziennika systemu jest sformatowany w typowych Event Format (CEF). 

  Aby skonfigurować klienta usługi Syslog, należy użyć **SyslogServer zestaw** polecenia cmdlet ujawnione w uprzywilejowanych punktu końcowego. 

  W tej wersji zapoznawczej można napotkać następujące trzy alerty. Gdy przedstawiony przez usługę Azure Stack, te alerty zawierają *opisy* i *korygowania* wskazówki. 
  - Tytuł: Integralność kodu wyłączone  
  - Tytuł: Integralność kodu w trybie inspekcji 
  - Tytuł: Konto użytkownika utworzone

  Chociaż ta funkcja jest dostępna w wersji zapoznawczej, go nie powinna być używana po w środowiskach produkcyjnych.   




### <a name="fixed-issues"></a>Rozwiązane problemy

<!-- # - applicability -->
- Rozwiązaliśmy ten problem, które zablokowały [otworzyć nowe żądanie obsługi z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z poziomu portalu administracyjnego. Obecnie ta opcja działa zgodnie z oczekiwaniami. 

- **Różne poprawki** dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Przed rozpoczęciem    

### <a name="prerequisites"></a>Wymagania wstępne
- Instalowanie usługi Azure Stack [aktualizacji 1804](azure-stack-update-1804.md) przed zastosowaniem aktualizacji usługi Azure Stack 1805.  
- Zainstaluj najnowszy dostępny [aktualizacja lub poprawka dla wersji 1804](azure-stack-update-1804.md#post-update-steps).   
- Przed rozpoczęciem instalacji aktualizacji 1805 Uruchom [AzureStack testu](azure-stack-diagnostic-test.md) Aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono. Również przejrzeć aktywne alerty i rozwiązywanie tych, które wymagają akcji. 

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji   
- Podczas instalacji aktualizacji 1805, użytkownik może widzieć alerty z tytułem *błędu — szablon FaultType UserAccounts.New Brak.*  Te alerty można bezpiecznie zignorować. Te alerty zostanie zamknięte automatycznie po zakończeniu aktualizacji do 1805.   

<!-- 2489559 - IS --> 
- Nie należy próbować tworzyć maszyny wirtualne podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Kroki po aktualizacji
Po zakończeniu instalacji 1805 Zainstaluj wszystkie odpowiednie poprawki. Uzyskać więcej informacji, zobacz następujące artykuły bazy wiedzy knowledge base, a także naszego [obsługi zasad](azure-stack-servicing-policy.md).  
 - [KB 4344102 — usługa Azure Stack poprawkę 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy po instalacji tej wersji kompilacji.

### <a name="portal"></a>Portal  

- Dokumentacja techniczna usługi Azure Stack koncentruje się na najnowszej wersji. Z powodu portalu zmian między wersjami Zobacz podczas przy użyciu portali usługi Azure Stack mogą różnić się od wyświetlanych w dokumentacji. 

<!-- 2931230 – IS  ASDK --> 
- Plany, które są dodawane do subskrypcji użytkownika, ponieważ nie można usunąć planu dodatku, nawet wtedy, gdy usuniesz plan z subskrypcji użytkownika. Plan pozostanie, dopóki odwołujące się do planu dodatku subskrypcje zostaną również usunięte. 

<!-- TBD - IS ASDK --> 
- Nie można zastosować aktualizacje sterowników za pomocą pakietu rozszerzenia producenta OEM w tej wersji usługi Azure Stack.  Nie ma sposobu obejścia tego problemu.

<!-- 2551834 - IS, ASDK --> 
- Po wybraniu **Przegląd** dla konta magazynu w portalach administratora lub użytkownika informacji z *Essentials* nie są wyświetlane w okienku.  Okienko podstawy Wyświetla informacje o koncie, takie jak jego *grupy zasobów*, *lokalizacji*, i *identyfikator subskrypcji*.  Omówienie inne opcje są dostępne, takich jak *usług* i *monitorowanie*, jak również jako opcji do *Otwórz w Eksploratorze* lub *Usuń konto magazynu* . 

  Aby wyświetlić dostępne informacje, należy użyć [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell. 

<!-- 2551834 - IS, ASDK --> 
- Po wybraniu **tagi** dla konta magazynu w portalach administratora lub użytkownika, informacje nie ładuje się i nie są wyświetlane.  

  Aby wyświetlić dostępne informacje, należy użyć [Get AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) polecenia cmdlet programu PowerShell.


<!-- 2332636 - IS -->  
- Korzystając z usług AD FS dla systemu tożsamości w usłudze Azure Stack i aktualizacji do tej wersji usługi Azure Stack, domyślnym właścicielem domyślną subskrypcję dostawcy jest resetowany do wbudowanej **CloudAdmin** użytkownika.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalania automatyzacji, aby skonfigurować oświadczeń zaufania dostawcy w usłudze Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

<!-- TBD - IS ASDK --> 
- Niektórych typów subskrypcji administracyjne nie są dostępne.  Po uaktualnieniu usługi Azure Stack w tej wersji typów dwóch subskrypcji, które były [wprowadzonym w wersji 1804](azure-stack-update-1804.md#new-features) nie są widoczne w konsoli. Jest to oczekiwane. Typy subskrypcji niedostępne są *pomiaru subskrypcji*, i *subskrypcji zużycie*. Te typy subskrypcji są wyświetlane w nowych wersji środowiska usługi Azure Stack począwszy od wersji 1804, ale nie są jeszcze gotowe do użycia. Można nadal używać *domyślny dostawca* typu subskrypcji.  

<!-- 2403291 - IS ASDK --> 
- Nie może być użycie poziomych pasków przewijania u dołu portali administratora i użytkownika. Jeśli nie masz dostępu poziomych pasków przewijania, użyj linki do stron nadrzędnych na przejście do poprzedniego bloku w portalu, wybierając nazwę bloku mają być wyświetlane na liście łączy do stron nadrzędnych znajdujących się u góry lewym rogu portalu.
  ![Łącza do stron nadrzędnych](media/azure-stack-update-1804/breadcrumb.png)

<!-- TBD - IS --> 
- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyny tego problemu jest błąd podczas instalowania aktualizacji, który powoduje, że aktualizacja nieprawidłowo zgłaszane jako pomyślne. Jeśli wystąpi ten problem, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

<!-- TBD - IS --> 
- Możesz zobaczyć pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz ikonę koła zębatego w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

<!-- TBD - IS ASDK --> 
- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

<!-- TBD - IS ASDK --> 
- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.


### <a name="health-and-monitoring"></a>Monitorowanie kondycji i
<!-- 1264761 - IS ASDK -->  
- Użytkownik może widzieć alerty dla *kondycji kontrolera* składnik, który ma następujące informacje:  
 
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

  Obydwa alerty #1 i #2 można bezpiecznie zignorować i zostanie zamknięte automatycznie wraz z upływem czasu. 

  Mogą również pojawić się następujący alert dotyczący *pojemności*. Dla tego alertu wartość procentowa dostępnej pamięci określonych w opisie mogą się różnić:  

  Zgłoś alert, #3:
   - Nazwa: Brak pamięci pojemności
   - Waga: krytyczny
   - SKŁADNIK: pojemności
   - Opis: Region zużyła ponad 80,00% dostępnej pamięci. Tworzenie maszyn wirtualnych z dużą ilością pamięci może zakończyć się niepowodzeniem.  

  W tej wersji usługi Azure Stack ten alert może wyzwalać niepoprawnie. Jeśli maszyny wirtualne dzierżawcy w dalszym ciągu pomyślnie wdrożone, można bezpiecznie zignorować ten alert. 
  
  Alert #3 nie automatycznie zamyka. Zamknij ten alert usługi Azure Stack utworzy tego samego alertu w ciągu 15 minut.  

<!-- 2368581 - IS. ASDK --> 
- Jako operatorów usługi Azure Stack, jeśli pojawi się alert małej ilości pamięci, a maszyny wirtualne dzierżawcy nie można wdrożyć za pomocą *błąd podczas tworzenia maszyny Wirtualnej w sieci szkieletowej*, istnieje możliwość, że sygnatury usługi Azure Stack jest mało dostępnej pamięci. Użyj [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) poprawnie dostępnej pojemności dla obciążeń. 


### <a name="compute"></a>Wystąpienia obliczeniowe
<!-- TBD - IS, ASDK --> 
- Wybierając rozmiar maszyny wirtualnej do wdrażania maszyn wirtualnych, niektóre rozmiary serii F maszyn wirtualnych nie są widoczne jako część selektor rozmiar podczas tworzenia maszyny Wirtualnej. Następujących rozmiarów maszyn wirtualnych nie są wyświetlane w selektorze: *F8s_v2*, *F16s_v2*, *F32s_v2*, i *F64s_v2*.  
  Obejść ten problem należy użyć jednej z następujących metod do wdrożenia maszyny Wirtualnej. W każdej metodzie należy określić rozmiar maszyny Wirtualnej, którego chcesz użyć.

  - **Szablon usługi Azure Resource Manager:** gdy używasz szablonu, ustaw *vmSize* w szablonie wartość rozmiaru maszyny Wirtualnej, w której chcesz użyć. Na przykład, następujący wpis jest używany do wdrażania maszyn wirtualnych, który używa *F32s_v2* rozmiar:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Interfejs wiersza polecenia platformy Azure:** można użyć [tworzenie az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) polecenia i określić rozmiar maszyny Wirtualnej jako parametr, podobnie jak `--size "Standard_F32s_v2"`.

  - **Program PowerShell:** przy użyciu programu PowerShell, można użyć [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) z parametrem, który określa rozmiar maszyny Wirtualnej, podobnie jak `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

<!-- TBD - IS --> 
- Po utworzeniu zestaw dostępności w portalu, przechodząc do **New** > **obliczenia** > **zestawu dostępności**, można tworzyć tylko zestaw dostępności z domeny błędów i domeny aktualizacji 1. Obejść ten problem, tworząc nową maszynę wirtualną, utworzyć dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

<!-- TBD - IS ASDK --> 
- Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej serii DS. Maszyny wirtualne z serii DS może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

<!-- TBD - IS ASDK --> 
- W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Może następnie spróbować Pobierz serwer obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

<!-- TBD - IS ASDK --> 
- Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

<!-- 1662991 IS ASDK --> 
- Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  


### <a name="networking"></a>Networking
<!-- TBD - IS ASDK --> 
- Nie można utworzyć trasy zdefiniowane przez użytkownika w jednym portalu administratora lub użytkownika. Jako obejście tego problemu, należy użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS ASDK --> 
- W obszarze **sieć**, po kliknięciu **Utwórz bramę sieci VPN** do skonfigurowania połączenia sieci VPN, **oparte na zasadach** jest wymieniony jako typ sieci VPN. Nie należy zaznaczać tej opcji. Tylko **na podstawie trasy** opcja jest obsługiwana w usłudze Azure Stack.

<!-- 2388980 - IS ASDK --> 
- Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adres IP, w związku z oryginalnej maszyny Wirtualnej, a nie nowy.

<!-- 2292271 - IS ASDK --> 
- Jeśli zostanie podniesiony limitu przydziału dla zasobu sieciowego, który jest częścią oferty i Plan, który jest skojarzony z subskrypcją dzierżawy, nowy limit nie ma zastosowania do tej subskrypcji. Jednakże nowy limit dotyczą nowych subskrypcjach, które są tworzone po nastąpi zwiększenie limitu przydziału.

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz instrukcje [udostępnić planu dodatku](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Nie można usunąć subskrypcji, która zawiera zasoby strefy DNS lub skojarzone z nią zasoby tabeli tras. Aby pomyślnie usunąć subskrypcję, musisz najpierw usunąć strefy DNS i tabelę tras zasobów z subskrypcji dzierżawcy.


<!-- 1902460 - IS ASDK --> 
- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

<!-- 16309153 - IS ASDK --> 
- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

<!-- TBD - IS ASDK --> 
- Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.

<!-- 2096388 IS --> 
- Aby zaktualizować reguły sieciowej grupy zabezpieczeń nie może korzystać z portalu administratora.

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


### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL

<!-- TBD - IS --> 
- Tworzenie elementów na serwerach tego hosta SQL lub MySQL jest obsługiwane tylko dostawcy zasobów. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może prowadzić do niezgodnego stanu.  

<!-- IS, ASDK --> 
- Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** lub **warstwy** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.

<!-- TBD - IS --> 
> [!NOTE]  
> Po aktualizacji do usługi Azure Stack 1805, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożone.  Zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i bazy danych MySQL. Np. usługi Azure Stack należy zastosować aktualizacje do dostawcy zasobów SQL i MySQL sekwencyjnie. Na przykład jeśli używasz wersji 1803, najpierw zastosowanie wersji 1804, a następnie zaktualizuj do 1805.      
>   
> Instalacja aktualizacji 1805 nie ma wpływu na bieżące użycie bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz dane użytkowników w swoich baz danych nie jest Dotyk i pozostanie dostępne.    



### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Użytkownicy, należy zarejestrować dostawcę zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

<!-- 2489178 - IS ASDK --> 
- Aby zapewnić skalowanie infrastruktury (pracowników, zarządzanie, frontonu role), należy użyć programu PowerShell, zgodnie z opisem w informacjach o wersji dla środowiska obliczeniowego.

<!-- TBD - IS ASDK --> 
- Usługa App Service można wdrażać tylko w *subskrypcję dostawcy domyślne* w tej chwili. 


### <a name="usage"></a>Sposób użycia  
<!-- TBD - IS ASDK --> 
- Użycie publicznego adresu IP adres użycie licznika dane pokazują, taka sama *Data/godzina zdarzenia* wartość dla każdego rekordu, zamiast *TimeDate* sygnatury, który pokazuje, kiedy rekord został utworzony. Obecnie nie można użyć tych danych do wykonania dokładnego rozliczania użycia publicznego adresu IP.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji usługi Azure Stack 1805 z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
