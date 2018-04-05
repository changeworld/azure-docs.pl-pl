---
title: Tworzenie sieci wirtualnej platformy Azure komunikację równorzędną — modele wdrażania różnych - różnych subskrypcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną komunikacji równorzędnej między sieciami wirtualnymi, została utworzona za pośrednictwem różne Azure modele wdrażania znajdujące się w różnych subskrypcji platformy Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 7a0104e68b07dbdff5483b771429fb9bc19a523f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Tworzenie sieci wirtualnej równorzędna — różne modele wdrażania i subskrypcji

Z tego samouczka dowiesz się, można utworzyć sieci wirtualnej komunikacji równorzędnej między sieciami wirtualnymi utworzone przez różne modele wdrażania. Sieci wirtualne istnieją w ramach różnych subskrypcji. Komunikacja równorzędna dwa zasoby umożliwia sieci wirtualnych w różnych sieciach wirtualnych do komunikowania się ze sobą przy tym samym przepustowości i opóźnień, tak jakby były zasoby w tej samej sieci wirtualnej. Dowiedz się więcej o [równorzędna sieci wirtualnej](virtual-network-peering-overview.md).

Kroki tworzenia sieci wirtualnej komunikacji równorzędnej są różne, w zależności od tego, czy sieci wirtualne są w tym samym lub różnych subskrypcji i które [modelu wdrożenia usługi Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną równorzędna w innych sytuacjach, klikając w scenariuszu z następującej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](tutorial-connect-virtual-networks-portal.md) |tym samym|
|[Obie usługi Resource Manager](create-peering-different-subscriptions.md) |Różne|
|[Jeden Resource Manager, co classic](create-peering-different-deployment-models.md) |tym samym|

Nie można utworzyć sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania. Ten samouczek używa sieci wirtualnych, które istnieją w tym samym regionie. W tym samouczku równorzędnymi użytkownikami sieci wirtualne w tym samym regionie. Można również elementów równorzędnych sieci wirtualnych w różnych [obsługiwane regiony](virtual-network-manage-peering.md#cross-region).  

Podczas tworzenia sieci wirtualnej komunikacji równorzędnej między sieciami wirtualnymi, które istnieją w ramach różnych subskrypcji, subskrypcje muszą być skojarzone z tej samej dzierżawy usługi Azure Active Directory. Jeśli nie masz już dzierżawę usługi Azure Active Directory, możesz szybko [utworzyć](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Możesz połączyć sieci wirtualnych w ramach różnych subskrypcji i różne usługi Azure Active Directory dzierżawy, przy użyciu platformy Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Można użyć [portalu Azure](#portal), Azure [interfejsu wiersza polecenia](#cli) (CLI) lub Azure [PowerShell](#powershell) można utworzyć sieci wirtualnej komunikacji równorzędnej. Kliknij dowolny z poprzedniej łączy narzędzia, aby przejść bezpośrednio do kroki tworzenia sieci wirtualnej komunikacji równorzędnej narzędzie wyboru.

## <a name="portal"></a>Utwórz równorzędna - portalu Azure

W tym samouczku korzysta z różnych kont dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania poza portalem i pomiń kroki przypisywania innym użytkownikom uprawnień do sieci wirtualnych.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako Użytkownik_a. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
2. Kliknij przycisk **+ nowy**, kliknij przycisk **sieci**, następnie kliknij przycisk **sieci wirtualnej**.
3. W **Utwórz sieć wirtualną** bloku, wprowadź, lub wybierz wartości poniższych ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Nazwa**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję A.
    - **Grupa zasobów**: Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroupA*
    - **Lokalizacja**: *wschodnie stany USA*
4. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetA*. Kliknij przycisk **myVnetA** po wyświetleniu w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnetA** sieci wirtualnej.
5. W **myVnetA** bloku, który jest wyświetlany, kliknij przycisk **(IAM) kontroli dostępu** z pionowy listy opcji w lewej części bloku.
6. W **myVnetA — kontrola dostępu (IAM)** bloku, który jest wyświetlany, kliknij przycisk **+ Dodaj**.
7. W **dodać uprawnienia** wyświetlonym bloku, wybierz **współautora sieci** w **roli** pole.
8. W **wybierz** wybierz Użytkownik_b lub wpisz adres e-mail firmy Użytkownik_b za pomocą funkcji wyszukiwania. Lista użytkowników wyświetlany jest dzierżawy usługi Azure Active Directory jako sieć wirtualną, której podczas konfigurowania komunikacji równorzędnej dla. Gdy zostanie wyświetlony na liście, kliknij przycisk Użytkownik_b.
9. Kliknij pozycję **Zapisz**.
10. Wyloguj się z portalem jako Użytkownik_a, a następnie zaloguj się jako Użytkownik_b.
11. Kliknij przycisk **+ nowy**, typ *sieci wirtualnej* w **wyszukiwania portalu Marketplace** polu, a następnie kliknij przycisk **sieci wirtualnej** w wynikach wyszukiwania.
12. W **sieci wirtualnej** wyświetlonym bloku, wybierz **klasycznego** w **wybierz model wdrożenia** polu, a następnie kliknij przycisk **Utwórz**.
13.   W polu Utwórz sieć wirtualną (klasyczne) wyświetlany wprowadź następujące wartości:

    - **Nazwa**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję B.
    - **Grupa zasobów**: Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroupB*
    - **Lokalizacja**: *wschodnie stany USA*

14. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetB*. Kliknij przycisk **myVnetB** po wyświetleniu w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnetB** sieci wirtualnej.
15. W **myVnetB** bloku, który jest wyświetlany, kliknij przycisk **właściwości** z pionowy listy opcji w lewej części bloku. Kopiuj **identyfikator ZASOBU**, która jest używana w kolejnym kroku. Identyfikator zasobu jest podobny do poniższego przykładu: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Wykonaj kroki 5 – 9 dla myVnetB wprowadzania **Użytkownik_a** w kroku 8.
17. Wyloguj się z portalem jako Użytkownik_b i zaloguj się jako Użytkownik_a.
18. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetA*. Kliknij przycisk **myVnetA** po wyświetleniu w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnet** sieci wirtualnej.
19. Kliknij przycisk **myVnetA**.
20. W **myVnetA** bloku, który jest wyświetlany, kliknij przycisk **komunikacji równorzędnych** z pionowy listy opcji w lewej części bloku.
21. W **myVnetA - komunikacji równorzędnych** bloku, które wystąpiły, kliknij przycisk **+ Dodaj**
22. W **równorzędna Dodaj** bloku, zostanie wyświetlone, wprowadź, lub wybierz poniższe opcje, a następnie kliknij **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrażania sieci wirtualnej**: Wybierz **klasycznego**.
     - **Sprawdzić mój identyfikator zasobu**: Zaznacz to pole wyboru.
     - **Identyfikator zasobu**: Wprowadź identyfikator zasobu myVnetB z kroku 15.
     - **Zezwalaj na dostęp do sieci wirtualnej:** upewnij się, że **włączone** jest zaznaczone.
    W tym samouczku są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacji równorzędnych sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknięciu przycisku **OK** w poprzednim kroku, **równorzędna Dodaj** zamyka bloku, aby zobaczyć **myVnetA - komunikacji równorzędnych** ponownie blok. Po kilku sekundach komunikacji równorzędnej, utworzony zostanie wyświetlony w bloku. **Połączone** ma na liście **RÓWNORZĘDNA stan** kolumny dla **myVnetAToMyVnetB** równorzędna zostanie utworzony. Komunikacja równorzędna zostanie ustanowione. Nie istnieje potrzeba-to-peer sieć wirtualna (klasyczna) do sieci wirtualnej (Resource Manager).

    Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
25. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-portal) sekcji tego artykułu.

## <a name="cli"></a>Utwórz równorzędna - wiersza polecenia platformy Azure

W tym samouczku korzysta z różnych kont dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania z platformy Azure i usuwanie wierszy skryptu utworzonych przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów z nazw użytkowników, używasz Użytkownik_a i Użytkownik_b. 

1. [Zainstaluj](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 interfejsu wiersza polecenia Azure, aby utworzyć sieć wirtualna (klasyczna).
2. Otwórz sesję programu interfejsu wiersza polecenia, a następnie zaloguj się do platformy Azure jako przy użyciu Użytkownik_b `azure login` polecenia. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
3. Uruchamianie interfejsu wiersza polecenia w trybie zarządzania usługami, wprowadzając `azure config mode asm` polecenia.
4. Wprowadź następujące polecenie, aby utworzyć sieć wirtualną (klasyczne):
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. Pozostałe kroki należy wykonać przy użyciu bash skorupach z wiersza polecenia platformy Azure 2.0.4 lub nowszym [zainstalowane](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), lub za pomocą powłoki chmury Azure. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **wypróbuj** przycisku w skryptach poniżej, która otwiera powłokę chmury, w którym zaloguje się do konta platformy Azure. Opcje uruchamiania bash skrypty interfejsu wiersza polecenia na komputerze klienckim z systemem Windows, temacie [działający w systemie Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Skopiuj poniższy skrypt do edytora tekstu, na komputerze. Zastąp `<SubscriptionB-Id>` z identyfikatorem subskrypcji Jeśli nie znasz identyfikator subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji Skopiuj skrypt zmodyfikowane, wklej go w sesji środowiska 2.0 interfejsu wiersza polecenia i naciśnij klawisz `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Podczas tworzenia sieci wirtualnej (wdrożenia klasyczne) w kroku 4, Azure utworzone sieci wirtualnej w *sieci domyślne* grupy zasobów.
7. Zaloguj się Użytkownik_b poza Azure i zaloguj się jako Użytkownik_a w 2.0 interfejsu wiersza polecenia.
8. Utwórz grupę zasobów i sieć wirtualną (Resource Manager). Skopiuj poniższy skrypt, wklej go w sesji środowiska interfejsu wiersza polecenia i naciśnij klawisz `Enter`. 

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

9. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnych utworzonych za pomocą różne modele wdrażania. Skopiuj poniższy skrypt do edytora tekstu, na komputerze. Zastąp `<SubscriptionB-id>` z Twojego identyfikatora subskrypcji. Jeśli nie znasz identyfikator subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji Azure utworzona sieć wirtualna (klasyczna) został utworzony w kroku 4 w grupie zasobów o nazwie *sieci domyślne*. Wklej zmodyfikowane skrypt w sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnej dla sieci wirtualnej (Resource Manager). Skopiuj poniższy skrypt, a następnie wklej go w sesji interfejsu wiersza polecenia:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Przedstawia dane wyjściowe **połączony** w **PeeringState** kolumny.

    Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

11. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
12. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-cli) w tym artykule.

## <a name="powershell"></a>Utwórz równorzędna — PowerShell

W tym samouczku korzysta z różnych kont dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania z platformy Azure i usuwanie wierszy skryptu utworzonych przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów z nazw użytkowników, używasz Użytkownik_a i Użytkownik_b. 

1. Zainstaluj najnowszą wersję programu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) i [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modułów. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell, zaloguj się do subskrypcji na Użytkownik_b jako Użytkownik_b wprowadzając `Add-AzureAccount` polecenia. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
4. Aby utworzyć sieć wirtualną (klasyczne) przy użyciu programu PowerShell, możesz utworzyć nową lub zmodyfikować istniejący, plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizować i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujące **VirtualNetworkSite** elementu dla sieci wirtualnej używane w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importowanie pliku konfiguracji sieci zmienione może spowodować zmiany w istniejących sieci wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, tylko dodać poprzedniej sieci wirtualnej i nie zmienić lub usunąć istniejące sieci wirtualnych z subskrypcji. 

5. Zaloguj się do subskrypcji na Użytkownik_b jako Użytkownik_b użycie poleceń Menedżera zasobów, wprowadzając `login-azurermaccount` polecenia.
6. Przypisz uprawnienia Użytkownik_a sieci wirtualnej kopiowania B. poniższy skrypt do edytora tekstu, na komputerze i Zastąp `<SubscriptionB-id>` z Identyfikatorem subskrypcji B. Jeśli nie znasz identyfikator subskrypcji, wprowadź `Get-AzureRmSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** zwrócony wynik jest Twojego identyfikatora subskrypcji. Azure utworzona sieć wirtualna (klasyczna) został utworzony w kroku 4 w grupie zasobów o nazwie *sieci domyślne*. Można wykonać skryptu, skopiuj skrypt zmodyfikowane, wklej je do środowiska PowerShell i naciśnij klawisz `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Wyloguj się Azure jako Użytkownik_b i zaloguj się do subskrypcji na Użytkownik_a jako Użytkownik_a wprowadzając `login-azurermaccount` polecenia. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
8. Utwórz sieć wirtualną (Resource Manager), kopiując poniższy skrypt, wklejenie go do programu PowerShell, a następnie naciskając klawisz `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Przypisz uprawnienia Użytkownik_b myVnetA. Skopiuj poniższy skrypt do edytora tekstu, na komputerze i Zastąp `<SubscriptionA-Id>` z Identyfikatorem subskrypcji A. Jeśli nie znasz identyfikator subskrypcji, wprowadź `Get-AzureRmSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** zwrócony wynik jest Twojego identyfikatora subskrypcji. Wklej zmodyfikowanej wersji skrypt programu PowerShell, a następnie naciśnij klawisz `Enter` go wykonać.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Skopiuj poniższy skrypt do edytora tekstu, na komputerze i Zastąp `<SubscriptionB-id>` z Identyfikatorem subskrypcji B. -To-peer myVnetA do myVNetB, skopiuj skrypt zmodyfikowane, wklej je do środowiska PowerShell i naciśnij klawisz `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Wyświetlać stan komunikacji równorzędnej myVnetA kopiowanie poniższy skrypt, wklejając je w programie PowerShell i naciskając klawisz `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan jest **połączony**. Zmienia się na **połączony** po skonfigurowaniu komunikację równorzędną, aby myVnetA z myVnetB.

    Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
13. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-powershell) w tym artykule.

## <a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka można usunąć utworzony w samouczka w celu uniknięcia opłat użycie zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroupA**. W wynikach wyszukiwania kliknij **myResourceGroupA**.
2. Na **myResourceGroupA** bloku, kliknij przycisk **usunąć** ikony.
3. Aby potwierdzić decyzję, w **typu nazwa grupy zasobów** wprowadź **myResourceGroupA**, a następnie kliknij przycisk **usunąć**.
4. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetB*. Kliknij przycisk **myVnetB** po wyświetleniu w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnetB** sieci wirtualnej.
5. W **myVnetB** bloku, kliknij przycisk **usunąć**.
6. Aby potwierdzić usunięcie, kliknij przycisk **tak** w **sieci wirtualnej Delete** pole.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia 2.0, można usunąć sieci wirtualnej (Resource Manager) za pomocą następującego polecenia:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Zaloguj się do platformy Azure przy użyciu Azure CLI 1.0 można usunąć sieci wirtualnej (klasyczne) za pomocą następujących poleceń:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. W wierszu polecenia programu PowerShell wpisz następujące polecenie, aby usunąć sieć wirtualną (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Można usunąć sieci wirtualnej (wdrożenia klasyczne) przy użyciu programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizować i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używane w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Importowanie pliku konfiguracji sieci zmienione może spowodować zmiany w istniejących sieci wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, tylko Usuń poprzednie sieci wirtualnej, a nie zmienić lub usunąć innych istniejących sieci wirtualnej z subskrypcji. 

## <a name="next-steps"></a>Kolejne kroki

- Należy dokładnie zapoznać się z ważne [ograniczenia komunikacji równorzędnej sieci wirtualnej i zachowania](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem sieci wirtualnej komunikacji równorzędnej w środowisku produkcyjnym należy używać.
- Więcej informacji na temat wszystkich [sieci wirtualnej komunikacji równorzędnej ustawienia](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [tworzenie koncentratora i gwiazda topologii sieci](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z sieci wirtualnej komunikacji równorzędnej.
