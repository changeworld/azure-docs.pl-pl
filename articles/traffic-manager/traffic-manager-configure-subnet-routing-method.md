---
title: Konfigurowanie metody routingu ruchu w podsieci za pomocą usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania usługi Traffic Manager na potrzeby kierowania ruchu z określonych podsieci.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: 6e5e6008741306d322ebd07bcbf144133ca4e632
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131280"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Bezpośrednie kierowanie ruchu do określonych punktów końcowych na podstawie podsieci użytkownika przy użyciu usługi Traffic Manager

W tym artykule opisano jak skonfigurować metodę routingu ruchu w podsieci. **Podsieci** metody routingu ruchu pozwala na mapowanie zestawu zakresów adresów IP do określonych punktów końcowych i po odebraniu żądania przez usługę Traffic Manager sprawdza źródłowy adres IP żądania i zwraca punkt końcowy skojarzony z nim. 

W tym scenariuszu omówionych w tym artykule przy użyciu routingu podsieci, w zależności od adresu IP zapytania użytkownika ruch odbywa się albo do wewnętrznej witryny sieci Web lub w produkcyjnej witrynie internetowej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było wyświetlić usługi Traffic Manager w działaniu, ten samouczek wymaga wdrażania następujących czynności:
- dwie witryny podstawowych sieci Web działające w różnych regionach platformy Azure — **wschodnie stany USA** (służy jako wewnętrznej witryny sieci Web) i **Europa Zachodnia** (służy jako witryny sieci Web w środowisku produkcyjnym).
- dwa testowe maszyny wirtualne do testowania usługi Traffic Manager — jedna maszyna wirtualna w **wschodnie stany USA** i drugą maszynę Wirtualną w **Europa Zachodnia**. 

Test maszyny wirtualne są używane do zilustrowania sposobu usługi Traffic Manager kierowania ruchu użytkowników do wewnętrznej witryny sieci Web lub witryny internetowej produkcji, na podstawie podsieci, z którego pochodzi kwerenda użytkownika.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

### <a name="create-websites"></a>Tworzenie witryn sieci Web

W tej sekcji opisano tworzenie dwóch wystąpień witryny sieci Web, które zapewniają dwa końcowych dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Tworzenie dwóch witryn sieci Web obejmuje następujące kroki:
1. Utwórz dwie maszyny wirtualne do uruchamiania podstawowe witryny sieci Web — jeden w **wschodnie stany USA**, a drugą w **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie Wirtualnej i zaktualizuj domyślną stronę witryny sieci Web w tym artykule opisano nazwę maszyny Wirtualnej, który jest połączony przez użytkownika podczas przeglądania witryny sieci Web.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn sieci Web
W tej sekcji utworzysz dwie maszyny wirtualne *myEndpointVMEastUS* i *myEndpointVMWEurope* w **wschodnie stany USA** i **Europa Zachodnia** platformy Azure regiony.

1. W górnym lewym rogu witryny Azure portal, wybierz opcję **Utwórz zasób** > **obliczenia** > **maszyny Wirtualnej systemu Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myIISVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz **New** , a następnie wpisz *myResourceGroupTM1*.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |||
4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:
    
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz **sieć wirtualna**w **Utwórz sieć wirtualną**, dla **nazwa**, wprowadź *myVNet1*, podsieć, wprowadź  *mySubnet*.|
    |Sieciowa grupa zabezpieczeń|Wybierz **podstawowe**, a następnie w **Dodaj publiczne porty wejściowe** listę rozwijaną, wybierz opcję **HTTP** i **protokołu RDP** |
    |Diagnostyka rozruchu|Wybierz **wyłączone**.|
    |||
6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–6 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Grupa zasobów | Wybierz pozycję **Nowa**, a następnie wpisz *myResourceGroupTM2*|
    |Lokalizacja|Europa Zachodnia|
    |Nazwa maszyny wirtualnej | myIISVMWEurope|
    |Sieć wirtualna | Wybierz **sieć wirtualna**w **Utwórz sieć wirtualną**, dla **nazwa**, wprowadź *myVNet2*, podsieć, wprowadź  *mySubnet*.|
    |||
8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

   ![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji, zainstaluj na serwerze usług IIS na dwie maszyny wirtualne - *myIISVMEastUS*  & *myIISVMWEurope*, a następnie zaktualizuj domyślną stronę witryny sieci Web. Na stronie niestandardowe witryny sieci Web zawiera nazwę maszyny wirtualnej, którą jest nawiązywane połączenie, gdy użytkownik odwiedza witryny sieci Web w przeglądarce sieci web.

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij *myIISVMEastUS* znajdującą się w *myResourceGroupTM1* grupy zasobów.
2. Na **Przegląd** kliknij **Connect**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **pliku RDP Pobierz**. 
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
7. Uruchamianie Windows PowerShell na *myIISVMEastUS* i przy użyciu następujących poleceń do zainstalowania serwera usług IIS i aktualizowania domyślny plik htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Zamknij połączenie RDP z *myIISVMEastUS*.
9. Powtórz kroki 1 – 6 z poprzez utworzenie połączenia RDP z maszyną Wirtualną *myIISVMWEurope* w ramach *myResourceGroupTM2* grupę zasobów, aby zainstalować usługi IIS i dostosować jego domyślnej strony sieci web.
10. Uruchamianie Windows PowerShell na *myIISVMWEurope* i przy użyciu następujących poleceń do zainstalowania serwera usług IIS i aktualizowania domyślny plik htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników tak, na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS dla serwerów usług IIS — *myIISVMEastUS* i *myIISVMWEurope*.

1. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz *myIISVMEastUS* znajdującą się w *myResourceGroupTM1* grupy zasobów.
2. Na **Przegląd** w obszarze **nazwy DNS**, wybierz opcję **Konfiguruj**.
3. Na **konfiguracji** stronie w obszarze etykiety nazwy DNS, Dodaj unikatową nazwę, a następnie wybierz **Zapisz**.
4. Powtórz kroki 1 – 3 dla maszyny Wirtualnej o nazwie *myIISVMWEurope* znajdującą się w *myResourceGroupTM1* grupy zasobów.

### <a name="create-test-vms"></a>Utwórz testowe maszyny wirtualne

W tej sekcji utworzysz Maszynę wirtualną (*mVMEastUS* i *myVMWestEurope*) w każdym regionie platformy Azure (**wschodnie stany USA** i **Europa Zachodnia**. Te maszyny wirtualne użyje do testowania, jak usługa Traffic Manager kieruje ruch do najbliższego serwera usług IIS po przejściu do witryny sieci Web.

1. W górnym lewym rogu witryny Azure portal, wybierz opcję **Utwórz zasób** > **obliczenia** > **maszyny Wirtualnej systemu Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Istniejąca** i wybierz grupę *myResourceGroupTM1*.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz **sieć wirtualna**w **Utwórz sieć wirtualną**, dla **nazwa**, wprowadź *myVNet3*, podsieć, wprowadź  *mySubnet3*.|
    |Sieciowa grupa zabezpieczeń|Wybierz **podstawowe**, a następnie w **Dodaj publiczne porty wejściowe** listę rozwijaną, wybierz opcję **HTTP** i **protokołu RDP** |
    |Diagnostyka rozruchu|Wybierz **wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki od 1 do 5, z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa maszyny wirtualnej | *myVMWEurope*|
    |Grupa zasobów | Wybierz **istniejące**, a następnie wpisz *myResourceGroupTM2*|
    |Sieć wirtualna | Wybierz **sieć wirtualna**w **Utwórz sieć wirtualną**, dla **nazwa**, wprowadź *myVNet4*, podsieć, wprowadź  *mySubnet4*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Utwórz profil usługi Traffic Manager, który umożliwia powrót do określonych punktów końcowych, w oparciu o źródłowy adres IP żądania.

1. W lewej górnej części ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Profil usługi Traffic Manager** > **Utwórz**.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:
    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Name (Nazwa)                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net i wyniki nazwę DNS, trafficmanager.net, która umożliwia dostęp do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz **podsieci** metody routingu.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz **istniejące** i wprowadź *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne uruchomione usługi IIS serwery — *myIISVMEastUS*  & *myIISVMWEurope* na potrzeby kierowania ruchu użytkowników na podstawie podsieci zapytania użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Punkt końcowy platformy Azure                                   |
    | Name (Nazwa)           | myTestWebSiteEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP** Aby wyświetlić listę zasobów przy użyciu publicznych adresów IP w ramach tej samej subskrypcji. W **zasobów**, wybierz publiczny adres IP o nazwie *myIISVMEastUS ip*. Jest to publiczny adres IP serwera usług IIS maszyny Wirtualnej w regionie wschodnie stany USA.|
    |  Ustawienia routingu dla podsieci    |   Dodaj adres IP *myVMEastUS* testowania maszyny Wirtualnej. Każde zapytanie użytkownika pochodzących z tej maszyny Wirtualnej zostanie skierowany do *myTestWebSiteEndpoint*.    |

4. Powtórz kroki 2 i 3, aby dodać innego punktu końcowego o nazwie *myProductionEndpoint* za publiczny adres IP *myIISVMWEurope ip* skojarzony z serwerem IIS maszyny Wirtualnej o nazwie *myIISVMWEurope* . Aby uzyskać **routingu ustawienia podsieci**, Dodaj adres IP testową maszynę Wirtualną — *myVMWestEurope*. Każde zapytanie użytkownika z tego testu, maszyn wirtualnych, które będą kierowane do endpoint - *myProductionWebsiteEndpoint*.
5.  Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

    ![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)
  
## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager
W tej sekcji możesz przetestować, kierowanie ruchu użytkowników w danej podsieci do określonego punktu końcowego usługi Traffic Manager. Aby wyświetlić usługi Traffic Manager w akcji, wykonaj następujące czynności:
1. Należy określić nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl usługi Traffic Manager działa w następujący sposób:
    - Z testową maszynę Wirtualną (*myVMEastUS*) znajdującą się w **wschodnie stany USA** regionu, w przeglądarce internetowej przejdź do profilu usługi Traffic Manager nazwę DNS.
    - Z testową maszynę Wirtualną (*myVMEastUS*) znajdującą się w **Europa Zachodnia** regionu, w przeglądarce internetowej przejdź do profilu usługi Traffic Manager nazwę DNS.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określ nazwę DNS, profilu usługi Traffic Manager
W tym samouczku dla uproszczenia umożliwia nazwę DNS profilu usługi Traffic Manager można znaleźć w witrynach sieci Web. 

Można określić nazwę DNS profilu usługi Traffic Manager w następujący sposób:

1.  Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
1. Kliknij pozycję **Przegląd**.
2. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych, można skonfigurować nazwę domeny znaczącej, aby wskazywała nazwę domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
W tej sekcji widać, że usługa Traffic Manager jest akcja. 

1. Wybierz **wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij *myVMEastUS* znajdującą się w *myResourceGroupTM1* grupy zasobów.
2. Na **Przegląd** kliknij **Connect**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **pliku RDP Pobierz**. 
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie. 
1. W przeglądarce sieci web na maszynie Wirtualnej *myVMEastUS*, wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witryny sieci Web. Ponieważ maszyna wirtualna *myVMEastUS* adres IP jest skojarzony z punktem końcowym *myIISVMEastUS*, przeglądarki sieci web uruchamia Test serwera witryny sieci Web - *myIISVMEastUS*.

   ![Testowanie profilu usługi Traffic Manager](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Następnie połącz się z maszyną wirtualną *myVMWestEurope* na terenie **Europa Zachodnia** korzystając z kroków 1 – 5 i przejdź do nazwy domeny profilu usługi Traffic Manager z tej maszyny Wirtualnej. Ponieważ maszyna wirtualna *myVMWestEurope* adres IP jest skojarzony z punktem końcowym *myIISVMEastUS*, przeglądarki sieci web uruchamia Test serwera witryny sieci Web - *myIISVMWEurope*. 
  
## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager
Gdy nie są już potrzebne, Usuń grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**). Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [ważona metody routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [metody routingu opartego na priorytecie](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [metody routingu geograficznego](traffic-manager-configure-geographic-routing-method.md).


