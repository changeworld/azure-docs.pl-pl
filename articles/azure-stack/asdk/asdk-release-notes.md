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
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2aa6663ae598b32979411fd10e7bb8a2eacd21de
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Informacje o wersji platformy Azure stosu Development Kit
Te informacje o wersji zawierają informacje dotyczące ulepszeń, poprawki i znane problemy w systemie Azure stosu Development Kit. Jeśli nie masz pewności, której używasz wersji, możesz [sprawdzić za pomocą portalu](.\.\azure-stack-updates.md#determine-the-current-version).

> Stosowanie what's new in ASDK Subskrybuj [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [źródła danych](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).


## <a name="build-201805131"></a>Kompilacja 20180513.1

### <a name="new-features"></a>Nowe funkcje 
Ta kompilacja obejmuje następujących ulepszeń i poprawek stosu Azure.  

- <!-- 1759172 - IS, ASDK --> **More granular administrative subscriptions**. With version 1804 and later, the Default Provider subscription is now complemented with two additional subscriptions. The additions facilitate separating the management of core infrastructure, additional resource providers, and workloads. The following three subscriptions are available:
  - *Domyślny dostawca subskrypcji*. Ta subskrypcja jest używana tylko podstawowej infrastruktury. Nie należy wdrażać zasobów lub dostawców zasobów w tej subskrypcji.
  - *Pomiaru subskrypcji*. Ta subskrypcja jest używana do wdrażania dostawcy zasobów. Zasoby wdrożone w tej subskrypcji nie są naliczane.
  - *Subskrypcja zużycie*. Ta subskrypcja jest używana dla innych obciążeń, które mają zostać wdrożone. Zasoby wdrożone w tym miejscu są naliczane ceny normalnego użycia.


### <a name="fixed-issues"></a>Rozwiązane problemy
- <!-- IS, ASDK -->  In the admin portal, you no longer have to refresh the Update tile before it displays information. 

- <!-- 2050709 - IS, ASDK -->  You can now use the admin portal to edit storage metrics for Blob service, Table service, and Queue service.

- <!-- IS, ASDK --> Under **Networking**, when you click **Connection** to set up a VPN connection, **Site-to-site (IPsec)** is now the only available option. 

- **Różne poprawki** wydajności, trwałości, zabezpieczeń i systemu operacyjnego, który jest używany przez stos Azure

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Dodatkowe wersje upłynął dzięki tej aktualizacji  
Poniżej są teraz dostępne, ale nie wymagają aktualizacji stosu Azure 1804.
- **Aktualizacja do pakiet monitorowania programu Microsoft Azure stosu System Center Operations Manager**. Nowa wersja (1.0.3.0) programu Microsoft System Center Operations Manager monitorowania Pack stosu Azure jest dostępna dla [Pobierz](https://www.microsoft.com/download/details.aspx?id=55184). W tej wersji można użyć nazwy główne usług, podczas dodawania podłączonej wdrożenia stosu Azure. Ta wersja zawiera też środowisko zarządzania aktualizacjami, które można podjąć akcję naprawczą bezpośrednio z poziomu programu Operations Manager. Istnieją nowe pulpity nawigacyjne, które wyświetlić dostawców zasobów, jednostek skalowania i skalowanie węzłów jednostki.

- **Nowe Azure stosu administracyjnej wersja programu PowerShell pkt 1.2.12**.  Program Azure PowerShell stosu pkt 1.2.12 jest teraz dostępna do instalacji. Ta wersja zawiera polecenia dla wszystkich dostawców zasobów administratora do zarządzania Azure stosu.  W tej wersji, niektóre zawartości zostaną wycofane z usługi GitHub z narzędzia Azure stosu [repozytorium](https://github.com/Azure/AzureStack-Tools). 

   Aby uzyskać szczegółowe informacje dotyczące instalacji, należy wykonać [instrukcje](.\.\azure-stack-powershell-install.md) lub [pomocy](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.2.12) zawartości dla modułu stosu Azure pkt 1.2.12. 

- **Początkowa wersja Azure stosu interfejsu API Rest odwołania**. Dokumentacja interfejsu API dla wszystkich dostawcy zasobów usługi Azure stosu administratora zostały opublikowane

### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> The ability [to open a new support request from the dropdown](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) from within the administrator portal isn’t available. Instead, use the following link:     
    - Azure stosu Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> You might not have use of the horizontal scroll bar along the bottom of the admin and user portals. If you can’t access the horizontal scroll bar, use the breadcrumbs to navigate to a previous blade in the portal by selecting the name of the blade you want to view from the breadcrumb list found at the top left of the portal.
  ![Nawigacją](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Deleting user subscriptions results in orphaned resources. As a workaround, first delete user resources or the entire resource group, and then delete user subscriptions.

- <!-- TBD -  IS ASDK --> You cannot view permissions to your subscription using the Azure Stack portals. As a workaround, use PowerShell to verify permissions.

-   <!-- TBD -  IS ASDK --> In the admin portal, you might see a critical alert for the Microsoft.Update.Admin component. The Alert name, description, and remediation all display as:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 

#### <a name="compute"></a>Wystąpienia obliczeniowe
- <!-- TBD -  IS ASDK --> Scaling settings for virtual machine scale sets are not available in the portal. As a workaround, you can use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Because of PowerShell version differences, you must use the `-Name` parameter instead of `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> When you create virtual machines on the Azure Stack user portal, the portal displays an incorrect number of data disks that can attach to a DS series VM. DS series VMs can accommodate as many data disks as the Azure configuration.

- <!-- TBD -  IS ASDK --> When a VM image fails to be created, a failed item that you cannot delete might be added to the VM images compute blade.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

- <!-- TBD -  IS ASDK --> If provisioning an extension on a VM deployment takes too long, users should let the provisioning time-out instead of trying to stop the process to deallocate or delete the VM.  

- <!-- 1662991 - IS ASDK --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 

#### <a name="networking"></a>Networking
- <!-- 1766332 - IS, ASDK --> Under **Networking**, if you click **Create VPN Gateway** to set up a VPN connection, **Policy Based** is listed as a VPN type. Do not select this option. Only the **Route Based** option is supported in Azure Stack.

- <!-- 2388980 -  IS ASDK --> After a VM is created and associated with a public IP address, you can't disassociate that VM from that IP address. Disassociation appears to work, but the previously assigned public IP address remains associated with the original VM.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy.

- <!-- 2292271 - IS ASDK --> If you raise a Quota limit for a Network resource that is part of an Offer and Plan that is associated with a tenant subscription, the new limit is not applied to that subscription. However, the new limit does apply to new subscriptions that are created after the quota is increased. 

  Aby obejść ten problem, użyj planu dodatku, aby zwiększyć limit przydziału sieci, gdy plan jest już skojarzony z subskrypcją. Aby uzyskać więcej informacji, zobacz temat jak [udostępnić plan dodatek](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> You cannot delete a subscription that has DNS Zone resources or Route Table resources associated with it. To successfully delete the subscription, you must first delete DNS Zone and Route Table resources from the tenant subscription. 


- <!-- 1902460 -  IS ASDK --> Azure Stack supports a single *local network gateway* per IP address. This is true across all tenant subscriptions. After the creation of the first local network gateway connection, subsequent attempts to create a local network gateway resource with the same IP address are blocked.

- <!-- 16309153 -  IS ASDK --> On a Virtual Network that was created with a DNS Server setting of *Automatic*, changing to a custom DNS Server fails. The updated settings are not pushed to VMs in that Vnet.
 
- <!-- TBD -  IS ASDK --> Azure Stack does not support adding additional network interfaces to a VM instance after the VM is deployed. If the VM requires more than one network interface, they must be defined at deployment time.


#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- <!-- TBD - ASDK --> The database hosting servers must be dedicated for use by the resource provider and user workloads. You cannot use an instance that is being used by any other consumer, including App Services.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers. 

#### <a name="app-service"></a>App Service
- <!-- TBD -  IS ASDK --> Users must register the storage resource provider before they create their first Azure Function in the subscription.

- <!-- TBD -  IS ASDK --> In order to scale out infrastructure (workers, management, front-end roles), you must use PowerShell as described in the release notes for Compute.
 
#### <a name="usage"></a>Sposób użycia  
- <!-- TBD -  IS ASDK --> Usage Public IP address usage meter data shows the same *EventDateTime* value for each record instead of the *TimeDate* stamp that shows when the record was created. Currently, you can’t use this data to perform accurate accounting of public IP address usage.

<!-- #### Identity -->






## <a name="build-201803291"></a>Kompilacja 20180329.1

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

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- Zostanie wyświetlony **wymagana aktywacja** alert ostrzeżenia, zaleceniem można zarejestrować zestawu Azure stosu Development Kit. To zachowanie jest oczekiwane.

- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.

- Nie można wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Aby uniknąć tego problemu Sprawdź uprawnienia za pomocą programu PowerShell.

- Na pulpicie nawigacyjnym portalu administracyjnego kafelka aktualizacji nie są wyświetlane informacje o aktualizacjach. Aby rozwiązać ten problem, kliknij Kafelek, aby go odświeżyć.

-   W portalu administracyjnym, można napotkać alert krytyczny dotyczący składnika Microsoft.Update.Admin. Nazwa alertu, opis i korygowania wszystkich wyświetlania jako:  
    - *Błąd — szablon FaultType ResourceProviderTimeout Brak.*

    Można bezpiecznie zignorować ten alert. 



#### <a name="marketplace"></a>Portal Marketplace
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii DS maszyny Wirtualnej. Serii DS maszyny wirtualne mogą obsługiwać jako wiele dysków z danymi jako konfiguracji platformy Azure.

- Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

-  Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Networking
- W obszarze **sieci**, jeśli klikniesz przycisk **połączenia** do skonfigurowania połączenia sieci VPN, **do wirtualnymi** jest wymieniony jako typ połączenia to możliwe. Nie należy zaznaczać tej opcji. Obecnie tylko **lokacja lokacja (IPsec)** opcja jest obsługiwana.

- Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy.



- Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.
 
- Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.



#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- Może potrwać maksymalnie jedną godzinę, zanim użytkownicy mogą tworzyć baz danych w nowym SQL lub MySQL jednostki SKU.

- Bazy danych serwerów hosta muszą być przeznaczone wyłącznie do użytku przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia, który jest używany przez innego użytkownika, łącznie z usługi aplikacji.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** or **Tier** names when you create a SKU for the SQL and MySQL resource providers.

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






## <a name="build-201803021"></a>Kompilacja 20180302.1

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki
Zobacz [nowe funkcje i poprawki](.\.\azure-stack-update-1802.md#new-features-and-fixes) sekcji 1802 stosu Azure aktualizacji informacje o wersji programu Azure stosu zintegrowanych systemów.

> [!IMPORTANT]    
> Niektóre elementy na liście **nowe funkcje i poprawki** sekcji mają zastosowanie tylko do stosu Azure zintegrowanych systemów.


### <a name="known-issues"></a>Znane problemy
 
#### <a name="portal"></a>Portal
- Możliwość [aby otworzyć nowe żądanie pomocy technicznej z listy rozwijanej](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) z wewnątrz administrator portalu nie jest dostępna. Zamiast tego użyj następującego łącza:     
    - Azure stosu Development Kit, można użyć https://aka.ms/azurestackforum.    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

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
W portalu administracyjnym stosu Azure zostanie wyświetlony alert krytyczny o nazwie **do czasu wygaśnięcia certyfikatu zewnętrznego**.  Ten alert można bezpiecznie zignorować i ma wpływu na operacje Azure stosu Development Kit. 


#### <a name="marketplace"></a>Portal Marketplace
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

- Stos Azure obsługuje, używając tylko dysków VHD typu stałego. Niektóre obrazy oferowane za pośrednictwem portalu marketplace na stosie Azure Użyj dynamicznych wirtualnych dysków twardych, ale te zostały usunięte. Zmiana rozmiaru maszyny wirtualnej (VM) z dysku dynamicznego do niego dołączony pozostawia maszyny Wirtualnej w stanie niepowodzenia.

  Aby zminimalizować ten problem, należy usunąć maszynę Wirtualną bez usuwania dysku maszyny Wirtualnej, obiektu blob dysku VHD na koncie magazynu. Następnie Konwertuj wirtualny dysk twardy z dysk dynamiczny na dysk stały, a następnie ponownie utwórz maszynę wirtualną.

- Po utworzeniu maszyny wirtualnej w portalu Azure stosu użytkownika portalu zawiera nieprawidłową liczbę dysków z danymi, które można załączyć do serii DS maszyny Wirtualnej. Serii DS maszyny wirtualne mogą obsługiwać jako wiele dysków z danymi jako konfiguracji platformy Azure.

- Gdy nie można utworzyć obrazu maszyny Wirtualnej, elementu nie powiodło się, że nie można usunąć mogą być dodane do bloku obliczeń obrazów maszyny Wirtualnej.

  Jako obejście, Utwórz nowy obraz maszyny Wirtualnej z fikcyjny wirtualnego dysku twardego, który można utworzyć za pomocą funkcji Hyper-V (New-VHD-C:\dummy.vhd ścieżka-stałej SizeBytes — 1 GB). Ten proces powinno rozwiązać problem, który uniemożliwia usunięcie elementu nie powiodło się. Następnie 15 minut po utworzeniu obrazu zastępczego, można pomyślnie usunąć go.

  Następnie spróbujesz ponownie Pobierz obraz maszyny Wirtualnej, który zakończył się niepowodzeniem.

-  Jeśli Inicjowanie obsługi rozszerzenia w ramach wdrożenia maszyny Wirtualnej trwa zbyt długo, użytkownicy powinno pozwolić limit czasu inicjowania obsługi zamiast próby zatrzymania procesu deallocate lub Usuń maszynę Wirtualną.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>Networking
- W obszarze **sieci**, jeśli klikniesz przycisk **połączenia** do skonfigurowania połączenia sieci VPN, **do wirtualnymi** jest wymieniony jako typ połączenia to możliwe. Nie należy zaznaczać tej opcji. Obecnie tylko **lokacja lokacja (IPsec)** opcja jest obsługiwana.

- Po utworzeniu maszyny Wirtualnej i skojarzonych z publicznym adresem IP, nie można usunąć skojarzenie tej maszyny Wirtualnej z tego adresu IP. Usuwanie skojarzeń wydaje się działać, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna.

  Obecnie możesz korzystać tylko nowe publiczne adresy IP do tworzenia nowych maszyn wirtualnych.

  Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy.

-   Funkcja przesyłania dalej protokołu IP jest widoczny w portalu, jednak włączenie przesyłania dalej protokołu IP nie ma wpływu. Ta funkcja nie jest jeszcze obsługiwana.

- Stos Azure obsługuje jeden *bramy sieci lokalnej* według adresu IP. Dotyczy to we wszystkich subskrypcji dzierżawcy. Po utworzenia pierwszego lokalnej bramy połączenia sieciowego, kolejne podejmuje próbę utworzenia zasobu bramy sieci lokalnej przy użyciu tego samego adresu IP są zablokowane.

- W sieci wirtualnej, który został utworzony przy użyciu ustawienia serwera DNS z *automatyczne*, zmieniających się do niestandardowego błąd serwera DNS. Zaktualizowano ustawienia nie są przenoszone do maszyn wirtualnych w tej sieci wirtualnej.
 
- Stos Azure nie obsługuje dodawania dodatkowe interfejsy do wystąpienia maszyny Wirtualnej, po wdrożeniu maszyny Wirtualnej. Jeśli maszyna wirtualna wymaga więcej niż jeden interfejs sieciowy, muszą być zdefiniowane w czasie wdrażania.



#### <a name="sql-and-mysql"></a>SQL i bazy danych MySQL 
- Może potrwać maksymalnie jedną godzinę, zanim użytkownicy mogą tworzyć baz danych w nowym SQL lub MySQL jednostki SKU.

- Bazy danych serwerów hosta muszą być przeznaczone wyłącznie do użytku przez zasób obciążeń dostawcy i użytkownika. Nie można użyć wystąpienia, który jest używany przez innego użytkownika, łącznie z usługi aplikacji.

- <!-- IS, ASDK --> Special characters, including spaces and periods, are not supported in the **Family** name when you create a SKU for the SQL and MySQL resource providers.

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

