---
title: Informacje o wersji zestawu Microsoft Azure stosu Development Kit | Dokumentacja firmy Microsoft
description: Ulepszenia, poprawki i znane problemy dotyczące Azure stosu Development Kit.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 1a8cbdef8f3d8a5aa4aeab0e51275933160360c2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Informacje o wersji platformy Azure stosu Development Kit
Te informacje o wersji zawierają informacje dotyczące ulepszeń, poprawki i znane problemy w systemie Azure stosu Development Kit. Jeśli nie masz pewności, której używasz wersji, możesz [sprawdzić za pomocą portalu](.\.\azure-stack-updates.md#determine-the-current-version).

> Stosowanie what's new in ASDK Subskrybuj [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [źródła danych](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

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

- Po wyświetleniu właściwości zasób lub grupa zasobów **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przenoszenie zasobów lub grup zasobów między grupami zasobów lub subskrypcji nie jest obecnie obsługiwane.
 
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

- Po wyświetleniu właściwości zasób lub grupa zasobów **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przenoszenie zasobów lub grup zasobów między grupami zasobów lub subskrypcji nie jest obecnie obsługiwane.
 
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




## <a name="build-201801032"></a>Kompilacja 20180103.2

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

- Zobacz [nowe funkcje i poprawki](.\.\azure-stack-update-1712.md#new-features-and-fixes) sekcji 1712 stosu Azure aktualizacji informacje o wersji programu Azure stosu zintegrowanych systemów.

    > [!IMPORTANT]
    > Niektóre elementy na liście **nowe funkcje i poprawki** sekcji mają zastosowanie tylko do stosu Azure zintegrowanych systemów.

### <a name="known-issues"></a>Znane problemy
 
#### <a name="deployment"></a>Wdrożenie
- Należy określić serwer czasu za pomocą adresu IP podczas wdrażania.

#### <a name="infrastructure-management"></a>Zarządzanie infrastrukturą
- Nie należy włączać infrastruktura kopii zapasowej na **infrastruktura kopii zapasowej** bloku.
- Adres IP kontrolera zarządzania płytą główną i modelu nie są wyświetlane w ważne informacje węzła jednostki skalowania. To zachowanie jest oczekiwane w Azure stosu Development Kit.

#### <a name="portal"></a>Portal
- Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.
- Po wyświetleniu właściwości grupy zasobów, **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przeniesienie grup zasobów między subskrypcjami nie jest obecnie obsługiwane.
-  Na każdy przepływ pracy, gdzie wybierz subskrypcji, grupy zasobów lub lokalizacji w listy rozwijanej może wystąpić jeden lub więcej z następujących problemów:

   - Może pojawić się pusty wiersz na początku listy. Nadal można wybrać elementu zgodnie z oczekiwaniami.
   - Jeśli na liście elementów na liście rozwijanej jest krótki, nie można przeglądać nazwy elementu.
   - Jeśli masz wiele subskrypcji użytkownika listy rozwijanej grupy zasobów może być pusta. 

   Aby obejść problemy z ostatnich dwóch, możesz wpisać nazwę subskrypcji lub grupy zasobów (jeśli ją znasz), lub można użyć programu PowerShell.

- Zobaczysz **wymagana aktywacja** alert ostrzeżenia, zaleceniem można zarejestrować zestawu Azure stosu Development Kit. To zachowanie jest oczekiwane.
- Jeśli **składnika** kliknięciu łącza za pomocą dowolnego **rolę infrastruktury** alertów, powstałe w ten sposób **omówienie** bloku próbuje załadować środowisko i kończy się niepowodzeniem. Ponadto ** omówienie ** bloku jest nie upłynął limit czasu.
- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.
- Nie jest możliwe wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Jako rozwiązanie alternatywne można sprawdzić uprawnień za pomocą programu PowerShell.
- **Kondycja usługi** bloku nie udało się załadować. Po otwarciu bloku usługi kondycji w portalu administratora lub użytkownika, stosu Azure jest wyświetlany błąd i nie zostanie załadowany informacji. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć usługi kondycji, ta funkcja nie jest jeszcze dostępny, ale będzie wykonywany w przyszłych wersjach programu Azure stosu.
#### <a name="marketplace"></a>Portal Marketplace
- Niektóre elementy marketplace są usuwane w tej wersji z powodu problemów ze zgodnością. Te zostaną ponownie włączone po dalszych weryfikacji.
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- Użytkownicy skorzystać z opcji, aby utworzyć maszynę wirtualną z magazynu geograficznie nadmiarowego. Ta konfiguracja powoduje niepowodzenie tworzenia maszyny wirtualnej. 
- Można skonfigurować dostępność maszyn wirtualnych, ustaw tylko z jednej domeny błędów i jedną domenę aktualizacji.
- Brak nie doświadczenie marketplace, aby utworzyć zestawy skalowania maszyny wirtualnej. Można utworzyć skali ustawić za pomocą szablonu.
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

#### <a name="networking"></a>Networking
- Nie można utworzyć modułu równoważenia obciążenia z publicznym adresem IP, za pomocą portalu. Jako rozwiązanie alternatywne można utworzyć modułu równoważenia obciążenia za pomocą programu PowerShell.
- Podczas tworzenia modułu równoważenia obciążenia sieciowego, należy utworzyć regułę translatora adresów sieciowych adres. Jeśli nie, otrzymasz wystąpił błąd podczas próby dodania reguły NAT po utworzeniu usługi równoważenia obciążenia.
- W obszarze **sieci**, jeśli klikniesz przycisk **połączenia** do skonfigurowania połączenia sieci VPN, **do wirtualnymi** jest wymieniony jako typ połączenia to możliwe. Nie należy zaznaczać tej opcji. Obecnie tylko **lokacja lokacja (IPsec)** opcja jest obsługiwana.
- Nie można usunąć skojarzenie publicznego adresu IP z maszyną wirtualną (VM), po utworzeniu maszyny Wirtualnej i skojarzonych z tym adresem IP. Usuwanie skojarzeń pojawi się do pracy, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna. Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy. Obecnie tylko musi używać nowego publiczne adresy IP do tworzenia nowej maszyny Wirtualnej.
- Operatory stosu Azure może być niemożliwe do wdrożenia, usuwanie, modyfikowanie, sieciami wirtualnymi lub grup zabezpieczeń sieci. Ten problem występuje przede wszystkim prób kolejnych aktualizacji tego samego pakietu. Jest to spowodowane problemem pakietów za pomocą aktualizacji, który jest obecnie badanym.
- Wewnętrzny równoważenia obciążenia (ILB) nieprawidłowo obsługuje adresów MAC dla maszyn wirtualnych zaplecza, które odrzucają z siecią wewnętrzną, korzystając z wystąpień systemu Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Może potrwać do godziny dzierżawców można utworzyć bazy danych w nowym SQL lub MySQL jednostki SKU. 
- Tworzenie elementów bezpośrednio na SQL i MySQL serwerów, które nie są wykonywane przez dostawcę zasobów hosta nie jest obsługiwana i może spowodować niezgodne stanu.

#### <a name="app-service"></a>App Service
- Użytkownik musi zarejestrować dostawcy zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.
 
#### <a name="usage"></a>Sposób użycia  
- Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.

#### <a name="identity"></a>Tożsamość

W usłudze Azure Active Directory Federation Services (ADFS) wdrożone w środowiskach **azurestack\azurestackadmin** konto nie jest już właściciela subskrypcji domyślny dostawca. Zamiast podczas logowania się na **portalu administracyjnego / punktu końcowego adminmanagement** z **azurestack\azurestackadmin**, można użyć **azurestack\cloudadmin** konta, dlatego Czy można zarządzać i korzystania z subskrypcji domyślny dostawca.

> [!IMPORTANT]
> Nawet **azurestack\cloudadmin** konta jest właścicielem subskrypcji dostawcy domyślne w środowiskach usług AD FS wdrożona, nie ma uprawnień do protokołu RDP na hoście. Nadal używać **azurestack\azurestackadmin** konta lub lokalnego konta administratora do logowania, dostęp i zarządzać hostem zgodnie z potrzebami.




## <a name="build-201711221"></a>Kompilacja 20171122.1

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

- Zobacz [nowe funkcje i poprawki](.\.\azure-stack-update-1711.md#new-features-and-fixes) sekcji 1711 stosu Azure aktualizacji informacje o wersji programu Azure stosu zintegrowanych systemów.

    > [!IMPORTANT]
    > Niektóre elementy na liście **nowe funkcje i poprawki** sekcji mają zastosowanie tylko do stosu Azure zintegrowanych systemów.

### <a name="known-issues"></a>Znane problemy

 
#### <a name="deployment"></a>Wdrożenie
- Należy określić serwer czasu za pomocą adresu IP podczas wdrażania.
- Począwszy od wersji 1711, **CloudAdmin** jest nazwą konta zastrzeżone i nie powinien być ręcznie określony podczas wdrażania zestaw deweloperski. 

#### <a name="infrastructure-management"></a>Zarządzanie infrastrukturą
- Nie należy włączać infrastruktura kopii zapasowej na **infrastruktura kopii zapasowej** bloku.
- Adres IP kontrolera zarządzania płytą główną i modelu nie są wyświetlane w ważne informacje węzła jednostki skalowania. To zachowanie jest oczekiwane w Azure stosu Development Kit.

#### <a name="portal"></a>Portal
- Może pojawić się pustego pulpitu nawigacyjnego w portalu. Aby odzyskać pulpitu nawigacyjnego, wybierz koło zębate ikonę w prawym górnym rogu portalu, a następnie wybierz **przywrócić ustawienia domyślne**.
- Po wyświetleniu właściwości grupy zasobów, **Przenieś** przycisk jest niedostępny. To zachowanie jest oczekiwane. Przeniesienie grup zasobów między subskrypcjami nie jest obecnie obsługiwane.
-  Na każdy przepływ pracy, gdzie wybierz subskrypcji, grupy zasobów lub lokalizacji w listy rozwijanej może wystąpić jeden lub więcej z następujących problemów:

   - Może pojawić się pusty wiersz na początku listy. Nadal można wybrać elementu zgodnie z oczekiwaniami.
   - Jeśli na liście elementów na liście rozwijanej jest krótki, nie można przeglądać nazwy elementu.
   - Jeśli masz wiele subskrypcji użytkownika listy rozwijanej grupy zasobów może być pusta. 

   Aby obejść problemy z ostatnich dwóch, możesz wpisać nazwę subskrypcji lub grupy zasobów (jeśli ją znasz), lub można użyć programu PowerShell.

- Zobaczysz **wymagana aktywacja** alert ostrzeżenia, zaleceniem można zarejestrować zestawu Azure stosu Development Kit. To zachowanie jest oczekiwane.
- Jeśli **składnika** kliknięciu łącza za pomocą dowolnego **rolę infrastruktury** alertów, powstałe w ten sposób **omówienie** bloku próbuje załadować środowisko i kończy się niepowodzeniem. Ponadto **omówienie** bloku jest nie upłynął limit czasu.
- Usunięcie użytkownika powoduje subskrypcje zasoby oddzielone. Jako obejście najpierw usuń zasoby użytkownika lub grupy zasobów całej, a następnie usuń subskrypcji użytkownika.
- Nie jest możliwe wyświetlić uprawnienia do subskrypcji przy użyciu portali stosu Azure. Jako rozwiązanie alternatywne można sprawdzić uprawnień za pomocą programu PowerShell.
- **Kondycja usługi** bloku nie udało się załadować. Po otwarciu bloku usługi kondycji w portalu administratora lub użytkownika, stosu Azure jest wyświetlany błąd i nie zostanie załadowany informacji. Jest to oczekiwane zachowanie. Chociaż można wybrać i otworzyć usługi kondycji, ta funkcja nie jest jeszcze dostępny, ale będzie wykonywany w przyszłych wersjach programu Azure stosu.

#### <a name="marketplace"></a>Portal Marketplace
- Podczas dodawania elementów do stosu Azure marketplace przy użyciu **Dodaj z platformy Azure** opcji, nie wszystkie elementy mogą być widoczne do pobrania.
- Użytkownicy można przeglądać pełnego portalu marketplace bez subskrypcji i widoczne elementy administracyjne, takie jak plany i oferty. Te elementy są niefunkcjonalne dla użytkowników.
 
#### <a name="compute"></a>Wystąpienia obliczeniowe
- Użytkownicy skorzystać z opcji, aby utworzyć maszynę wirtualną z magazynu geograficznie nadmiarowego. Ta konfiguracja powoduje niepowodzenie tworzenia maszyny wirtualnej. 
- Można skonfigurować dostępność maszyn wirtualnych, ustaw tylko z jednej domeny błędów i jedną domenę aktualizacji.
- Brak nie doświadczenie marketplace, aby utworzyć zestawy skalowania maszyny wirtualnej. Można utworzyć skali ustawić za pomocą szablonu.
- Ustawienia skalowania dla zestawy skalowania maszyny wirtualnej nie są dostępne w portalu. Jako rozwiązanie alternatywne można zastosować [programu Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Z powodu różnic wersji programu PowerShell, należy użyć `-Name` parametru zamiast `-VMScaleSetName`.

#### <a name="networking"></a>Networking
- Nie można utworzyć modułu równoważenia obciążenia z publicznym adresem IP, za pomocą portalu. Jako rozwiązanie alternatywne można utworzyć modułu równoważenia obciążenia za pomocą programu PowerShell.
- Podczas tworzenia modułu równoważenia obciążenia sieciowego, należy utworzyć regułę translatora adresów sieciowych adres. Jeśli nie, otrzymasz wystąpił błąd podczas próby dodania reguły NAT po utworzeniu usługi równoważenia obciążenia.
- W obszarze **sieci**, jeśli klikniesz przycisk **połączenia** do skonfigurowania połączenia sieci VPN, **do wirtualnymi** jest wymieniony jako typ połączenia to możliwe. Nie należy zaznaczać tej opcji. Obecnie tylko **lokacja lokacja (IPsec)** opcja jest obsługiwana.
- Nie można usunąć skojarzenie publicznego adresu IP z maszyną wirtualną (VM), po utworzeniu maszyny Wirtualnej i skojarzonych z tym adresem IP. Usuwanie skojarzeń pojawi się do pracy, ale poprzednio przypisanych publiczny adres IP pozostają skojarzone z oryginalna maszyna wirtualna. Dzieje się tak nawet w przypadku ponownego przypisywania adresów IP do nowej maszyny Wirtualnej (nazywane *wymiany wirtualnych adresów IP*). Wszystkie przyszłe próbuje nawiązać połączenie za pośrednictwem tego wyniku adresów IP w przypadku połączenia pierwotnie skojarzonego VM, a nie nowy. Obecnie tylko musi używać nowego publiczne adresy IP do tworzenia nowej maszyny Wirtualnej.
- Operatory stosu Azure może być niemożliwe do wdrożenia, usuwanie, modyfikowanie, sieciami wirtualnymi lub grup zabezpieczeń sieci. Ten problem występuje przede wszystkim prób kolejnych aktualizacji tego samego pakietu. Jest to spowodowane problemem pakietów za pomocą aktualizacji, który jest obecnie badanym.
- Wewnętrzny równoważenia obciążenia (ILB) nieprawidłowo obsługuje adresów MAC dla maszyn wirtualnych zaplecza, które odrzucają z siecią wewnętrzną, korzystając z wystąpień systemu Linux.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Może potrwać do godziny dzierżawców można utworzyć bazy danych w nowym SQL lub MySQL jednostki SKU. 
- Tworzenie elementów bezpośrednio na SQL i MySQL serwerów, które nie są wykonywane przez dostawcę zasobów hosta nie jest obsługiwana i może spowodować niezgodne stanu.

    > [!NOTE]
    > Zobacz poszczególne [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) i [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) artykuły, aby uzyskać wskazówki dotyczące zgodności wersji Instalatora.

#### <a name="app-service"></a>App Service
- Użytkownik musi zarejestrować dostawcy zasobów magazynu, przed utworzeniem ich pierwszej funkcji platformy Azure w ramach subskrypcji.

#### <a name="usage"></a>Sposób użycia  
- Dane pomiaru użycia adres publiczny adres IP użycia zawiera takie same *Data/godzina zdarzenia* wartość dla każdego rekordu zamiast *TimeDate* sygnatury pokazujący utworzenia rekordu. Obecnie nie można używać tych danych do wykonania dokładnego rozliczania użycie publicznego adresu IP.

#### <a name="identity"></a>Tożsamość

W usłudze Azure Active Directory Federation Services (ADFS) wdrożone w środowiskach **azurestack\azurestackadmin** konto nie jest już właściciela subskrypcji domyślny dostawca. Zamiast podczas logowania się na **portalu administracyjnego / punktu końcowego adminmanagement** z **azurestack\azurestackadmin**, można użyć **azurestack\cloudadmin** konta, dlatego Czy można zarządzać i korzystania z subskrypcji domyślny dostawca.

> [!IMPORTANT]
> Nawet **azurestack\cloudadmin** konta jest właścicielem subskrypcji dostawcy domyślne w środowiskach usług AD FS wdrożona, nie ma uprawnień do protokołu RDP na hoście. Nadal używać **azurestack\azurestackadmin** konta lub lokalnego konta administratora do logowania, dostęp i zarządzać hostem zgodnie z potrzebami.

