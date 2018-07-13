---
title: Aktualizacja usługi Azure Stack 1803 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w wersji 1803 aktualizacji dla usługi Azure Stack zintegrowanych systemów, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 11430a0d194a722c0c0520c936db3c08b1a6b863
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989575"
---
# <a name="azure-stack-1803-update"></a>Aktualizacja usługi Azure Stack w wersji 1803

*Dotyczy: zintegrowane systemy usługi Azure Stack*

W tym artykule opisano ulepszeń i poprawek w pakiecie aktualizacji 1803, znane problemy dotyczące tej wersji i gdzie można pobrać aktualizację. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i kompilacji (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla usługi Azure Stack, zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji usługi Azure Stack Development Kit.

## <a name="build-reference"></a>Dokumentacja kompilowania    
Numerem kompilacji aktualizacji usługi Azure Stack w wersji 1803 **20180329.1**.


## <a name="before-you-begin"></a>Przed rozpoczęciem    
> [!IMPORTANT]    
> Nie należy próbować tworzyć maszyny wirtualne podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Wymagania wstępne
- Instalowanie usługi Azure Stack [aktualizacji 1802](azure-stack-update-1802.md) przed zastosowaniem aktualizacji usługi Azure Stack w wersji 1803.   

- Zainstaluj **AzS poprawkę — 20180222.2 kompilacji 1.0.180312.1-** przed zastosowaniem aktualizacji usługi Azure Stack w wersji 1803. Ta poprawka aktualizacji usługi Windows Defender i jest dostępny po pobraniu aktualizacji dla usługi Azure Stack.

  Aby zainstalować poprawkę, postępuj zgodnie z normalnych procedur dla [instalowanie aktualizacji dla usługi Azure Stack](azure-stack-apply-updates.md). Nazwa aktualizacji jest wyświetlany jako **AzS poprawkę — 1.0.180312.1**i zawiera następujące pliki: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Po przekazaniu te pliki na konto magazynu i kontener, uruchom instalację programu za pomocą kafelka aktualizacji w portalu administracyjnym. 
  
  W przeciwieństwie do aktualizacji do usługi Azure Stack zainstalowanie tej aktualizacji nie zmienia wersji usługi Azure Stack. Aby upewnić się, ta aktualizacja jest zainstalowana, Wyświetl listę **zainstalowane aktualizacje**.



### <a name="new-features"></a>Nowe funkcje 
Ta aktualizacja obejmuje następujące ulepszenia i poprawki dla usługi Azure Stack.

- **Aktualizacja hasła usługi Azure Stack** — (konta i certyfikatów). Aby uzyskać więcej informacji o zarządzaniu wpisami tajnymi, zobacz [Obróć klucze tajne w usłudze Azure Stack](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatyczne przekierowania HTTPS** kiedy korzystać z protokołu HTTP do uzyskania dostępu do portali administratora i użytkownika. To ulepszenie została wprowadzona w oparciu o [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) opinię dotyczącą usługi Azure Stack. 

- <!-- 2202621  --> **Dostęp do portalu Marketplace** — teraz możesz otworzyć portalu Marketplace usługi Azure Stack przy użyciu [+ nowy](https://ms.portal.azure.com/#create/hub) opcję w portalach administratora i użytkownika taki sam sposób jak w witrynie Azure Portal.
 
- <!-- 2202621 --> **Usługa Azure Monitor** — dodaje usługę Azure Stack [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) portale administratora i użytkownika. Dotyczy to również nowy eksploratory dla metryki i Dzienniki aktywności. Dostęp do tego monitora platformy Azure z sieciami zewnętrznymi, port **13012** musi być otwarty w konfiguracji zapory. Aby uzyskać więcej informacji na temat portów wymaganych przez usługę Azure Stack, zobacz [Integracja z centrum danych usługi Azure Stack — publikowanie punktów końcowych](azure-stack-integrate-endpoints.md).

   Również w ramach tej zmiany, w obszarze **więcej usług**, *dzienniki inspekcji* pojawi się jako *dzienników aktywności*. Funkcja jest teraz zgodne z witryny Azure portal. 

- <!-- 1664791 --> **Plików rozrzedzonych** — w przypadku dodawania nowego obrazu do usługi Azure Stack lub dodać obraz za pomocą syndykacji marketplace obraz, który jest konwertowany na plik rozrzedzony. Nie można przekonwertować obrazy, które zostały dodane przed użyciem usługi Azure Stack w wersji 1803. Zamiast tego należy użyć witryny marketplace syndykacji ponownie przesłać tych obrazów, aby móc korzystać z tej funkcji. 
 
   Plików rozrzedzonych są w formacie pliku wydajne, używane do zmniejszenia miejsca do magazynowania, użyj i ulepszania operacji We/Wy.  Aby uzyskać więcej informacji, zobacz [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) dla systemu Windows Server. 

### <a name="fixed-issues"></a>Rozwiązane problemy

- <!-- 1739988 --> Wewnętrzne Równoważenie obciążenia (ILB) teraz poprawnie obsługuje adresy MAC dla maszyn wirtualnych zaplecza, co powoduje, że wewnętrznego modułu równoważenia obciążenia, można usunąć pakiety do sieci wewnętrznej, korzystając z wystąpień systemu Linux w sieci wewnętrznej. Wewnętrznego modułu równoważenia obciążenia działa poprawnie z wystąpieniami Windows w sieci wewnętrznej. 

- <!-- 1805496 --> Problem ze względu na usłudze Azure Stack przy użyciu różnych ustawień zasad protokołu IKE niż Azure gdzie rozłączają czy połączenia sieci VPN między usługi Azure Stack. Wartości SALifetime (czas) i SALiftetime (w bajtach) aktualnie nie jest zgodna z platformą Azure i zostały zmienione w wersji 1803, aby dopasować ustawienia usługi Azure. Wartość SALifetime (w sekundach) przed 1803 była ona 14 400 i teraz ze zmianami dotyczącymi 27 000 w wersji 1803. Wartość przed 1803 SALifetime (w bajtach) został 819,200 i zmianami 33,553,408 w wersji 1803.

- <!-- 2209262 --> Problem adresów IP, w których połączenia sieci VPN było wcześniej widoczne w portalu; jednak włączenie lub przełączanie przekazywanie adresów IP nie ma znaczenia. Ta funkcja jest włączona domyślnie i możliwość wprowadzania zmian, to nie jest jeszcze obsługiwany.  Formant został usunięty z portalu. 

- <!-- 1766332 --> Usługi Azure Stack nie obsługuje zasady na podstawie bram sieci VPN, nawet jeśli opcja jest wyświetlana w portalu.  Opcja została usunięta z portalu. 

- <!-- 1868283 --> Usługa Azure Stack teraz uniemożliwia zmienianie rozmiaru maszyny wirtualnej, która jest tworzona przy użyciu dysków dynamicznych. 

- <!-- 1756324 --> Dane użycia dla maszyn wirtualnych, teraz są oddzielone co godzinę. Jest to zgodne z platformą Azure. 

- <!--  2253274 --> Problem gdzie w portalach administratora i użytkownika, blok ustawień dla podsieci sieci wirtualnej nie można załadować. Jako obejście tego problemu, przy użyciu programu PowerShell i [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) polecenia cmdlet, aby wyświetlać i zarządzać tymi informacjami.

- Po utworzeniu maszyny wirtualnej, komunikat *nie można wyświetlić cennika* nie jest już wyświetlany podczas wybierania rozmiaru dla rozmiaru maszyny Wirtualnej.

- Różne poprawki dla wydajności, stabilności, bezpieczeństwa i systemu operacyjnego, który jest używany przez usługę Azure Stack.


### <a name="changes"></a>Zmiany
- Sposób można zmienić stanu nowo utworzoną ofertę z *prywatnej* do *publicznych* lub *zlikwidowana* został zmieniony. Aby uzyskać więcej informacji, zobacz [Utwórz ofertę](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji    
<!-- 2328416 --> Podczas instalacji aktualizacji 1803 może być przestoje usługi obiektów blob i usługi wewnętrznej, które korzystają z usługi blob service. W tym niektóre operacje maszyny wirtualnej. Czas przestoju może to spowodować błędy dzierżawy operacji lub alertów z usług, które nie mogą uzyskać dostęp do danych. Ten problem rozwiązuje się po ukończeniu instalacji. 



### <a name="post-update-steps"></a>Kroki po aktualizacji
- Po zakończeniu instalacji 1803 Zainstaluj wszystkie odpowiednie poprawki. Uzyskać więcej informacji, zobacz następujące artykuły bazy wiedzy knowledge base, a także naszego [obsługi zasad](azure-stack-servicing-policy.md).

  - [KB 4344115 — usługa Azure Stack poprawkę 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Po zainstalowaniu tej aktualizacji, sprawdź konfigurację zapory, aby upewnić się, [wymagane porty](azure-stack-integrate-endpoints.md) są otwarte. Na przykład, ta aktualizacja obejmuje *usługi Azure Monitor* w tym zmiana dzienniki inspekcji dzienników aktywności. Dzięki tej zmianie portu 13012 obecnie jest używana i musi być otwarty.  


### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy po instalacji dla kompilacji **20180323.2**.

#### <a name="portal"></a>Portal
- <!-- 2332636 - IS -->  Korzystając z usług AD FS dla systemu tożsamości w usłudze Azure Stack i aktualizacji do tej wersji usługi Azure Stack, domyślnym właścicielem domyślną subskrypcję dostawcy jest resetowany do wbudowanej **CloudAdmin** użytkownika.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalania automatyzacji, aby skonfigurować oświadczeń zaufania dostawcy w usłudze Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

- Możliwość [otworzyć nowe żądanie obsługi z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z w ramach administrator portalu nie jest dostępna. Zamiast tego należy użyć następującego linku:     
    - Użyć zintegrowane systemy usługi Azure Stack https://aka.ms/newsupportrequest.

- <!-- 2050709 --> W portalu administracyjnym nie jest możliwe edytowanie metryk usługi storage dla usługi Blob service, Table service lub kolejki usługi. Gdy możesz przejść do magazynu, a następnie wybierz obiekt blob, tabeli lub kafelka usługi kolejki, zostanie otwarty nowy blok, wyświetlającego wykresu metryki dla tej usługi. Jeśli wybierzesz opcję edycji w górnej części kafelka wykresu metryki, zostanie otwarty blok Edytuj wykres, ale nie wyświetla opcji, aby edytować metryki.

- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyny tego problemu jest błąd podczas instalowania aktualizacji, który powoduje, że aktualizacja nieprawidłowo zgłaszane jako pomyślne. Jeśli wystąpi ten problem, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- Możesz zobaczyć pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz ikonę koła zębatego w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- Usuwanie subskrypcji użytkownika powoduje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkowników lub całą grupę zasobów, a następnie usuń subskrypcji użytkownika.

- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali usługi Azure Stack. Jako obejście tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- Na pulpicie nawigacyjnym portalu administracyjnego Aktualizuj Kafelek nie są wyświetlane informacje o aktualizacjach. Aby rozwiązać ten problem, kliknij Kafelek, aby ją odświeżyć.

- W portalu administracyjnym, zostanie wyświetlony alert krytyczny dotyczący *Microsoft.Update.Admin* składnika. Nazwa alertu, opis i wszystkie są wyświetlane jako korygowania:  
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
- Ustawienia skalowania dla zestawów skalowania maszyn wirtualnych nie są dostępne w portalu. Jako obejście tego problemu, możesz użyć [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Ze względu na różnice dotyczące wersji programu PowerShell, należy użyć `-Name` parametr zamiast `-VMScaleSetName`.

- Po utworzeniu zestaw dostępności w portalu, przechodząc do **New** > **obliczenia** > **zestawu dostępności**, można tworzyć tylko zestaw dostępności z domeny błędów i domeny aktualizacji 1. Obejść ten problem, tworząc nową maszynę wirtualną, utworzyć dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- Po utworzeniu maszyny wirtualne w portalu użytkowników usługi Azure Stack portal zawiera nieprawidłową liczbę dysków z danymi, które można dołączyć do maszyny Wirtualnej z serii D. Wszystkie obsługiwane serii D maszyn wirtualnych może obsłużyć tyle dysków z danymi konfiguracji platformy Azure.

- W przypadku awarii do utworzenia obrazu maszyny Wirtualnej nie powiodło się element, który nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Obejść ten problem, należy utworzyć nowy obraz maszyny Wirtualnej za pomocą fikcyjnego wirtualnego dysku twardego, które mogą być tworzone za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżki — Naprawiono SizeBytes — 1 GB). Ten proces należy rozwiązać ten problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu fikcyjnego możesz pomyślnie go usunąć.

  Może następnie spróbować Pobierz serwer obrazu maszyny Wirtualnej, która poprzednio zakończyła się niepowodzeniem.

-  Jeśli aprowizacja rozszerzenia na wdrożenie maszyny Wirtualnej trwa zbyt długo, użytkownicy pozwolić limit czasu inicjowania obsługi administracyjnej zamiast próby zatrzymania procesu cofnięcie przydziału lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Diagnostyka maszyn wirtualnych systemu Linux nie jest obsługiwana w usłudze Azure Stack. Podczas wdrażania maszyny Wirtualnej z systemem Linux przy użyciu włączenia diagnostyki maszyn wirtualnych, wdrożenie zakończy się niepowodzeniem. Wdrażanie nie powiedzie, jeśli włączysz podstawowe metryki maszyny Wirtualnej systemu Linux, za pomocą ustawień diagnostycznych.  


#### <a name="networking"></a>Networking
- Po maszyny Wirtualnej jest utworzona i skojarzona z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale wcześniej przypisany publiczny adres IP pozostaje skojarzona z oryginalnej maszyny Wirtualnej.

  Obecnie należy używać tylko nowe publiczne adresy IP dla nowych maszyn wirtualnych, które tworzysz.

  To zachowanie występuje nawet wtedy, gdy należy ponownie przypisać adres IP z nową maszynę Wirtualną (nazywaną *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonej maszyny Wirtualnej, a nie nowy.



- Usługa Azure Stack obsługuje jeden *bramy sieci lokalnej* dla adresu IP. Ta zasada obowiązuje dla wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalne bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej za pomocą tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się niestandardowy błąd serwera DNS. Zaktualizowano ustawienia nie zostaną wypchnięte do maszyn wirtualnych w tej sieci wirtualnej.

- Usługa Azure Stack nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, musi być zdefiniowany w czasie wdrażania.

- <!-- 2096388 --> Aby zaktualizować reguły sieciowej grupy zabezpieczeń nie może korzystać z portalu administratora. 

    Obejście dla usługi App Service: Jeśli zachodzi potrzeba pulpitu zdalnego do wystąpień kontrolera, zmodyfikować reguły zabezpieczeń w ramach grupy zabezpieczeń sieci przy użyciu programu PowerShell.  Poniżej przedstawiono przykłady jak *Zezwalaj na*, a następnie Przywróć konfigurację, aby *Odmów*:  
    
    - *Zezwalaj na:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
- Przed kontynuowaniem przejrzyj ważne uwagi w [przed rozpoczęciem](#before-you-begin) bliżej początku te informacje o wersji.

- Może potrwać do godziny, zanim użytkownicy mogą tworzyć baz danych w ramach nowego wdrożenia bazy danych SQL lub MySQL.

- Tworzenie elementów na serwerach tego hosta SQL lub MySQL jest obsługiwane tylko dostawcy zasobów. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może prowadzić do niezgodnego stanu.  

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy po utworzeniu jednostki SKU dla dostawców zasobów SQL i bazy danych MySQL.

> [!NOTE]  
> Po aktualizacji do usługi Azure Stack w wersji 1803, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożone.  Zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i bazy danych MySQL. Np. usługi Azure Stack należy zastosować aktualizacje do dostawcy zasobów SQL i MySQL sekwencyjnie.  Na przykład jeśli używasz wersji 1711, najpierw zastosowanie wersji 1712, a następnie 1802, a następnie zaktualizuj do wersji 1803.      
>   
> Instalacja aktualizacji 1803 nie ma wpływu na bieżące użycie bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz dane użytkowników w swoich baz danych nie jest Dotyk i pozostanie dostępne.    



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


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji usługi Azure Stack w wersji 1803 z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i Wznów aktualizacje, zobacz [monitorowanie aktualizacji w usłudze Azure Stack przy użyciu uprzywilejowanych punktu końcowego](azure-stack-monitor-update.md).
- Aby uzyskać omówienie zarządzania aktualizacjami w usłudze Azure Stack, zobacz [zarządzanie aktualizacjami w usłudze Azure Stack — omówienie](azure-stack-updates.md).
- Aby uzyskać więcej informacji na temat sposobu stosowania aktualizacji za pomocą usługi Azure Stack, zobacz [stosowanie aktualizacji w usłudze Azure Stack](azure-stack-apply-updates.md).
