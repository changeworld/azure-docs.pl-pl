---
title: Aktualizacja Azure stosu 1803 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji 1803 stosu Azure zintegrowanych systemów, znane problemy i pobierania aktualizacji.
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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: a74e77f84aa70519015a589cbc6e7478c0c41592
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318813"
---
# <a name="azure-stack-1803-update"></a>Azure aktualizacji 1803 stosu

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule opisano ulepszenia i poprawki w pakiecie aktualizacji 1803, znane problemy dotyczące tej wersji i pobierania aktualizacji. Znane problemy są podzielone na problemy bezpośrednio związane z procesem aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]        
> Ten pakiet aktualizacji jest tylko dla stosu Azure zintegrowanych systemów. Nie dotyczą tego pakietu aktualizacji Azure stosu Development Kit.

## <a name="build-reference"></a>Tworzenie odwołania    
Numer kompilacji aktualizacji 1803 stosu Azure jest **20180329.1**.


## <a name="before-you-begin"></a>Przed rozpoczęciem    
> [!IMPORTANT]    
> Nie należy próbować tworzyć maszyn wirtualnych podczas instalacji tej aktualizacji. Aby uzyskać więcej informacji na temat zarządzania aktualizacjami, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md#plan-for-updates).


### <a name="prerequisites"></a>Wymagania wstępne
- Zainstaluj stosu Azure [zaktualizować 1802](azure-stack-update-1802.md) przed zastosowaniem aktualizacji 1803 stosu Azure.   

- Zainstaluj **AzS poprawkę — 20180222.2 kompilacji 1.0.180312.1-** przed zastosowaniem aktualizacji 1803 stosu Azure. Ta poprawka aktualizacji usługi Windows Defender i jest dostępna podczas pobierania aktualizacji dla stosu Azure.

  Aby zainstalować poprawkę, wykonaj normalnych procedur [instalowania aktualizacji dla stosu Azure](azure-stack-apply-updates.md). Nazwa aktualizacji jest wyświetlana jako **poprawkę AzS — 1.0.180312.1**i zawiera następujące pliki: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Po przekazaniu tych plików, aby konto magazynu i kontener, uruchom instalację na kafelku aktualizacji w portalu administracyjnym. 
  
  W odróżnieniu od aktualizacje stosu Azure po zainstalowaniu tej aktualizacji nie powoduje zmiany wersji Azure stosu. Aby potwierdzić, ta aktualizacja jest zainstalowana, wyświetlić listę **zainstalowane aktualizacje**.



### <a name="new-features"></a>Nowe funkcje 
Ta aktualizacja obejmuje następujące ulepszenia i poprawki dla stosu Azure.

- **Aktualizowanie kluczy tajnych stosu Azure** — (kont i certyfikatów). Aby uzyskać więcej informacji o zarządzaniu klucze tajne, zobacz [Obróć kluczy tajnych w stosie Azure](azure-stack-rotate-secrets.md). 

- <!-- 1914853 --> **Automatyczne przekierowanie do HTTPS** przy dostęp do portali administratora i użytkownika za pomocą protokołu HTTP. To ulepszenie został utworzony na podstawie [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) opinię dotyczącą stosu Azure. 

- <!-- 2202621  --> **Dostęp do portalu Marketplace** — teraz możesz otworzyć Azure Marketplace stosu przy użyciu [+ nowy](https://ms.portal.azure.com/#create/hub) opcję w portalach administratora i użytkownika taki sam sposób jak w portalach usługi Azure.
 
- <!-- 2202621 --> **Azure Monitor** -dodaje stosu Azure [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) do portali administratora i użytkownika. Dotyczy to również nowy eksploratorów dzienników metryki i działania. Aby uzyskać dostęp do tego monitora Azure z sieci zewnętrznych, port **13012** musi być otwarty w konfiguracji zapory. Aby uzyskać więcej informacji na temat portów wymaganych przez stos Azure, zobacz [integracja centrum danych Azure stosu - publikowanie punktów końcowych](azure-stack-integrate-endpoints.md).

   Również w ramach tego zmienić, w obszarze **więcej usług**, *dzienniki inspekcji* jest teraz wyświetlany jako *Dzienniki aktywności*. Funkcja jest teraz zgodne z portalu Azure. 

- <!-- 1664791 --> **Plików rozrzedzonych** — podczas dodawania nowego obrazu do stosu Azure, lub dodać obraz za pomocą zespolonego marketplace obrazu jest konwertowana na plik rozrzedzony. Nie można przekonwertować obrazów, które zostały dodane przed użyciem wersji stosu Azure 1803. Zamiast tego należy użyć zespolonego marketplace ponownie przesłać tych obrazów, aby móc korzystać z tej funkcji. 
 
   Plików rozrzedzonych są wydajne formacie pozwala zmniejszyć użycie miejsca magazynu i skrócić we/wy.  Aby uzyskać więcej informacji, zobacz [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) dla systemu Windows Server. 

### <a name="fixed-issues"></a>Rozwiązane problemy

- <!-- 1739988 --> Wewnętrzny równoważenia obciążenia (ILB) teraz poprawnie obsługuje adresów MAC dla maszyn wirtualnych zaplecza, co powoduje, że ILB Porzuć pakiety do sieci wewnętrznej, korzystając z wystąpień systemu Linux w sieci wewnętrznej. ILB współpracuje prawidłowo z wystąpień systemu Windows w sieci wewnętrznej. 

- <!-- 1805496 --> Problem, gdy połączenia sieci VPN między stosu Azure czy rozłączony z powodu stosu Azure za pomocą różnych ustawień zasad IKE niż Azure. Wartości SALifetime (Time) i SALiftetime (w bajtach) nie jest zgodna z platformą Azure i zostały zmienione w 1803 zgodnie z ustawieniami systemu Azure. Wartość SALifetime (w sekundach) przed 1803 był 14,400 i teraz zmiany 27 000 w 1803. Wartość SALifetime (w bajtach) przed 1803 został 819,200 i zmiany 33,553,408 w 1803.

- <!-- 2209262 --> Problem IP, gdy połączenia sieci VPN został wcześniej widoczne w portalu; jednak włączenie lub przełączanie przesyłania dalej protokołu IP nie ma znaczenia. Funkcja jest włączona domyślnie i możliwość zmiany, to nie jest jeszcze obsługiwane.  Formant został usunięty z portalu. 

- <!-- 1766332 --> Stos Azure nie obsługuje zasady na podstawie bramy sieci VPN, nawet jeśli jest dostępna w portalu.  Opcja została usunięta z portalu. 

- <!-- 1868283 --> Azure stosu teraz uniemożliwia zmianę rozmiaru maszyny wirtualnej, który jest tworzony w przypadku dysków dynamicznych. 

- <!-- 1756324 --> Dane użycia dla maszyn wirtualnych jest teraz oddzielone co godzinę. Jest to zgodne z platformą Azure. 

- <!--  2253274 --> Problem w przypadku gdy w portalach administratora i użytkownika, bloku ustawienia podsieci sieci wirtualnej nie można załadować. Aby uniknąć tego problemu, przy użyciu programu PowerShell i [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) polecenia cmdlet, aby wyświetlać i zarządzać tymi informacjami.

- Po utworzeniu maszyny wirtualnej, komunikat *nie można wyświetlić cen* nie będzie już wyświetlany w przypadku wybrania rozmiar dla rozmiaru maszyny Wirtualnej.

- Różne poprawki dotyczące wydajności, stabilności, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure.


### <a name="changes"></a>Zmiany
- Sposób zmiany stanu nowo utworzony ofertę otrzymaną *prywatnej* do *publicznego* lub *wycofany z eksploatacji* została zmieniona. Aby uzyskać więcej informacji, zobacz [utworzyć ofertę](azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji    
<!-- 2328416 --> Podczas instalacji aktualizacji 1803 może być przestoje usługi obiektów blob i wewnętrzny usługi, które używają usługi blob. Obejmuje to niektóre operacje maszyny wirtualnej. Czas przestoju może to powodować błędy dzierżawcy operacje lub alerty z usług, których nie można uzyskać dostępu do danych. Ten problem rozwiązuje się po ukończeniu instalacji. 



### <a name="post-update-steps"></a>Czynności po aktualizacji
- Po zakończeniu instalacji 1803 Zainstaluj wszystkie odpowiednie poprawki. Uzyskać więcej informacji, zobacz następujące artykuły bazy wiedzy knowledge base, a także naszych [obsługi zasad](azure-stack-servicing-policy.md).

  - [KB 4341390 - Azure stosu poprawkę 1.0.180424.12](https://support.microsoft.com/en-us/help/4341390).

- Po zainstalowaniu tej aktualizacji, należy przejrzeć konfigurację zapory, aby upewnić się, [niezbędne porty](azure-stack-integrate-endpoints.md) są otwarte. Na przykład w tej aktualizacji wprowadzono *Azure Monitor* w tym zmiany dzienniki inspekcji, aby dzienniki aktywności. Dzięki tej zmianie portu 13012 obecnie jest używany i musi być otwarty.  


### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)
Poniżej przedstawiono znane problemy występujące po instalacji w kompilacji **20180323.2**.

#### <a name="portal"></a>Portal
- <!-- 2332636 - IS -->  Jeśli używasz usług AD FS dla systemu tożsamości stosu Azure i aktualizacji do tej wersji programu Azure stosu domyślnego właściciela subskrypcji dostawcy domyślnego jest ustawiany na wbudowanych **CloudAdmin** użytkownika.  
  Obejście: Aby rozwiązać ten problem, po zainstalowaniu tej aktualizacji, należy użyć krok 3 z [wyzwalacza automatyzacji do skonfigurowania zaufania dostawcy oświadczeń w stosie Azure](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1) procedury, aby zresetować właściciela subskrypcji dostawcy domyślnego.   

- Możliwość [aby otworzyć nowe żądanie pomocy technicznej z listy rozwijanej](azure-stack-manage-portals.md#quick-access-to-help-and-support) z wewnątrz administrator portalu nie jest dostępna. Zamiast tego użyj następującego łącza:     
    - Azure stosu Użyj zintegrowanych systemów https://aka.ms/newsupportrequest.

- <!-- 2050709 --> W portalu administracyjnym nie jest możliwe edytowanie metryki magazynu dla usługi obiektów Blob, usługi tabel lub kolejek usługi. Gdy przejdź do magazynu, a następnie wybierz obiektów blob, tabeli lub Kafelek usługi kolejki, zostanie otwarty nowy blok, który przedstawia wykres metryki dla tej usługi. W przypadku wybrania następnie edytuj od góry kafelka wykresu metryki, bloku Edytuj wykres zostanie otwarty, ale nie są wyświetlane opcje, aby zmienić metryki.

- Może nie być możliwe do wyświetlania zasobów obliczeniowych i magazynu w portalu administratora. Przyczyną tego problemu jest błąd podczas instalacji aktualizacji, które powoduje, że aktualizacja niepoprawnie zgłaszana jako powiodło się. Jeśli ten problem występuje, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

- Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.

- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- Na pulpicie nawigacyjnym portalu administracyjnego kafelka aktualizacji nie są wyświetlane informacje o aktualizacjach. Aby rozwiązać ten problem, kliknij Kafelek, aby go odświeżyć.

- W portalu administracyjnym, zostanie wyświetlony alert krytyczny dotyczący *Microsoft.Update.Admin* składnika. Nazwa alertu, opis i korygowania wszystkich wyświetlania jako:  
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
- Użytkowników może uzyskać administracyjnej elementy, takie jak plany i oferty i przeglądać pełnego portalu marketplace bez subskrypcji. Te elementy są niefunkcjonalne dla użytkowników.



#### <a name="compute"></a>Wystąpienia obliczeniowe
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- Po utworzeniu zestawem dostępności w portalu, przechodząc do **nowy** > **obliczeniowe** > **zestawu dostępności**, można tworzyć tylko z domeny błędów i Aktualizacja domeny 1 zestawu dostępności. Jako obejście, podczas tworzenia nowej maszyny wirtualnej, Utwórz dostępności przy użyciu programu PowerShell, interfejsu wiersza polecenia, lub z poziomu portalu.

- Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii D maszyny Wirtualnej. Wszystkie obsługiwane D serii maszyn wirtualnych może obsłużyć jako wiele dysków z danymi jako konfiguracji platformy Azure.

- Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

-  Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Diagnostyka maszyny Wirtualnej systemu Linux nie jest obsługiwana w stosie Azure. Podczas wdrażania maszyny Wirtualnej systemu Linux z włączoną diagnostykę maszyny Wirtualnej, wdrożenie zakończy się niepowodzeniem. Wdrożenie nie powiedzie się także włączenie metryk podstawowej maszyny Wirtualnej systemu Linux za pomocą ustawień diagnostycznych.  


#### <a name="networking"></a>Networking
- Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy.



- Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.

- Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.

- <!-- 2096388 --> Nie można korzystać z portalu administratora, można zaktualizować reguł sieciowej grupy zabezpieczeń. 

    Obejście usługi aplikacji: Jeśli potrzebujesz pulpitu zdalnego do wystąpień kontrolera, zmodyfikować zasady zabezpieczeń w ramach grup zabezpieczeń sieci przy użyciu programu PowerShell.  Poniżej przedstawiono przykłady *Zezwalaj*, a następnie Przywróć konfigurację, aby *odmowy*:  
    
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

    - *Odmów:*

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
- Przed kontynuowaniem przejrzyj ważne uwagi w [przed rozpoczęciem](#before-you-begin) pobliżu początku te informacje o wersji.

- Może potrwać maksymalnie jedną godzinę, zanim użytkownicy mogą tworzyć baz danych w ramach nowego wdrożenia bazy danych SQL lub MySQL.

- Tylko dostawcy zasobów jest obsługiwane tworzenie elementów na serwerach, że host SQL lub MySQL. Elementy utworzone na serwerze hosta, które nie są tworzone przez dostawcę zasobów może spowodować niezgodne stanu.  

- <!-- IS, ASDK --> Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **rodziny** nazwy podczas tworzenia jednostki SKU dla dostawców zasobów SQL i MySQL.

> [!NOTE]  
> Po aktualizacji do 1803 stosu Azure, można użyć dostawcy zasobów SQL i MySQL, które wcześniej wdrożona.  Firma Microsoft zaleca się, że po udostępnieniu nowej wersji aktualizacji SQL i MySQL. Podobnie jak stosu Azure sekwencyjnie Zastosuj aktualizacje do dostawców zasobów SQL i MySQL.  Na przykład jeśli używasz wersji 1711 najpierw zastosować wersji 1712, a następnie 1802, a następnie zaktualizuj do 1803.      
>   
> Instalowanie aktualizacji 1803 nie wpływa na użycie bieżącej bazy danych SQL lub MySQL dostawców zasobów przez użytkowników.
> Niezależnie od wersji dostawców zasobów, których używasz danych użytkowników w swoich baz danych nie jest dotknąć i pozostanie dostępny.    



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


## <a name="download-the-update"></a>Pobierz aktualizację
Możesz pobrać pakiet aktualizacji 1803 stosu Azure z [tutaj](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Zobacz także
- Aby użyć uprzywilejowanych punktu końcowego (program ten) do monitorowania i wznowić aktualizacji, zobacz [monitorowania aktualizacji w stosie Azure przy użyciu punktu końcowego uprzywilejowanych](azure-stack-monitor-update.md).
- Omówienie zarządzania aktualizacjami w stosie Azure, zobacz [zarządzania aktualizacjami w omówieniu stosu Azure](azure-stack-updates.md).
- Aby uzyskać więcej informacji dotyczących sposobu stosowania aktualizacji za pomocą stosu Azure, zobacz [stosowanie aktualizacji w stosie Azure](azure-stack-apply-updates.md).
