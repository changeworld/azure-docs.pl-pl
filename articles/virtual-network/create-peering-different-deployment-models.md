---
title: Tworzenie sieci wirtualnej platformy Azure komunikację równorzędną — różne modele wdrażania — tej samej subskrypcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną komunikacji równorzędnej między sieciami wirtualnymi, został utworzony za pomocą modeli różnych wdrożenia usługi Azure, które istnieją w tej samej subskrypcji platformy Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 2ab027c1159fec369aa7377a24ddd9ef330eab5e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Tworzenie sieci wirtualnej równorzędna — różne modele wdrażania, tej samej subskrypcji 

Z tego samouczka dowiesz się, można utworzyć sieci wirtualnej komunikacji równorzędnej między sieciami wirtualnymi utworzone przez różne modele wdrażania. Istnieją obie sieci wirtualne w tej samej subskrypcji. Komunikacja równorzędna dwa zasoby umożliwia sieci wirtualnych w różnych sieciach wirtualnych do komunikowania się ze sobą przy tym samym przepustowości i opóźnień, tak jakby były zasoby w tej samej sieci wirtualnej. Dowiedz się więcej o [równorzędna sieci wirtualnej](virtual-network-peering-overview.md). 

Kroki tworzenia sieci wirtualnej komunikacji równorzędnej są różne, w zależności od tego, czy sieci wirtualne są w tym samym lub różnych subskrypcji i które [modelu wdrożenia usługi Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną równorzędna w innych sytuacjach, klikając w scenariuszu z następującej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](tutorial-connect-virtual-networks-portal.md) |tym samym|
|[Obie usługi Resource Manager](create-peering-different-subscriptions.md) |Różne|
|[Jeden Resource Manager, co classic](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania. Jeśli musisz połączyć sieci wirtualnych, które były tworzone przy użyciu klasycznego modelu wdrażania, można użyć Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do łączenia sieci wirtualnej. 

W tym samouczku równorzędnymi użytkownikami sieci wirtualne w tym samym regionie. Możliwość elementów równorzędnych sieci wirtualnych w różnych regionach jest obecnie w przeglądzie. Wykonaj kroki [zarejestrować się w globalnej sieci wirtualnej komunikacji równorzędnej](#register) przed podjęciem próby elementów równorzędnych sieci wirtualnych w różnych regionach lub komunikacji równorzędnej kończy się niepowodzeniem. Możliwość łączenia z platformy Azure, sieci wirtualnych w różnych regionach [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) jest ogólnie dostępna i nie wymaga rejestracji.

Można użyć [portalu Azure](#portal), Azure [interfejsu wiersza polecenia](#cli) (CLI) Azure [PowerShell](#powershell), lub [szablonu usługi Azure Resource Manager](#template)można utworzyć sieci wirtualnej komunikacji równorzędnej. Kliknij dowolny z poprzedniej łączy narzędzia, aby przejść bezpośrednio do kroki tworzenia sieci wirtualnej komunikacji równorzędnej narzędzie wyboru.

## <a name="create-peering---azure-portal"></a>Utwórz równorzędna - portalu Azure

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Zobacz [uprawnienia](#permissions) sekcji tego artykułu, aby uzyskać szczegółowe informacje.
2. Kliknij przycisk **+ nowy**, kliknij przycisk **sieci**, następnie kliknij przycisk **sieci wirtualnej**.
3. W **Utwórz sieć wirtualną** bloku, wprowadź, lub wybierz wartości poniższych ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Nazwa**: *myVnet1*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję
    - **Grupa zasobów**: Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroup*
    - **Lokalizacja**: *wschodnie stany USA*
4. Kliknij pozycję **+ Nowy**. W **wyszukiwania portalu Marketplace** wpisz *sieci wirtualnej*. Kliknij przycisk **sieci wirtualnej** po wyświetleniu w wynikach wyszukiwania. 
5. W **sieci wirtualnej** bloku, wybierz opcję **klasycznego** w **wybierz model wdrożenia** , a następnie kliknij przycisk **Utwórz**.
6. W **Utwórz sieć wirtualną** bloku, wprowadź, lub wybierz wartości poniższych ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Nazwa**: *myVnet2*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję
    - **Grupa zasobów**: Wybierz **Użyj istniejącego** i wybierz *myResourceGroup*
    - **Lokalizacja**: *wschodnie stany USA*
7. W **wyszukiwania zasobów** pole w górnej części portalu typu *myResourceGroup*. Kliknij przycisk **myResourceGroup** po wyświetleniu w wynikach wyszukiwania. Zostanie wyświetlony blok **myresourcegroup** grupy zasobów. Grupa zasobów zawiera dwie sieci wirtualne utworzone w poprzednich krokach.
8. Kliknij przycisk **myVNet1**.
9. W **myVnet1** bloku, który jest wyświetlany, kliknij przycisk **komunikacji równorzędnych** z pionowy listy opcji w lewej części bloku.
10. W **myVnet1 - komunikacji równorzędnych** bloku, które wystąpiły, kliknij przycisk **+ Dodaj**
11. W **równorzędna Dodaj** bloku, zostanie wyświetlone, wprowadź, lub wybierz poniższe opcje, a następnie kliknij **OK**:
     - **Nazwa**: *myVnet1ToMyVnet2*
     - **Model wdrażania sieci wirtualnej**: Wybierz **klasycznego**. 
     - **Subskrypcja**: Wybierz subskrypcję
     - **Sieć wirtualna**: kliknij **wybierz sieć wirtualną**, następnie kliknij przycisk **myVnet2**.
     - **Zezwalaj na dostęp do sieci wirtualnej:** upewnij się, że **włączone** jest zaznaczone.
    W tym samouczku są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacji równorzędnych sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknięciu przycisku **OK** w poprzednim kroku, **równorzędna Dodaj** zamyka bloku, aby zobaczyć **myVnet1 - komunikacji równorzędnych** ponownie blok. Po kilku sekundach komunikacji równorzędnej, utworzony zostanie wyświetlony w bloku. **Połączone** ma na liście **RÓWNORZĘDNA stan** kolumny dla **myVnet1ToMyVnet2** równorzędna zostanie utworzony.

    Komunikacja równorzędna zostanie ustanowione. Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
14. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-portal) sekcji tego artykułu.

## <a name="cli"></a>Utwórz równorzędna - wiersza polecenia platformy Azure

1. [Zainstaluj](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 interfejsu wiersza polecenia Azure, aby utworzyć sieć wirtualna (klasyczna).
2. Otwórz sesję programu polecenia, a następnie zaloguj się do platformy Azure przy użyciu `azure login` polecenia.
3. Uruchamianie interfejsu wiersza polecenia w trybie zarządzania usługami, wprowadzając `azure config mode asm` polecenia.
4. Wprowadź następujące polecenie, aby utworzyć sieć wirtualną (klasyczne):
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Utwórz grupę zasobów i sieć wirtualną (Resource Manager). Można użyć interfejsu wiersza polecenia 1.0 lub 2.0 ([zainstalować](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). W tym samouczku 2.0 interfejsu wiersza polecenia jest używany do tworzenia sieci wirtualnej (Resource Manager), ponieważ 2.0 musi być używany do tworzenia, komunikację równorzędną. Wykonaj następujące bash interfejsu wiersza polecenia skrypt z komputera lokalnego z poziomu interfejsu wiersza polecenia 2.0.4 lub nowszej. Opcje uruchamiania bash CLI skrypty w kliencie systemu Windows, temacie [działający w systemie Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Można również uruchomić skrypt za pomocą powłoki chmury Azure. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **wypróbuj** przycisk w skrypcie, który następujące, które wywołuje powłoka chmury, która rejestruje możesz zalogować się do konta platformy Azure z. Aby wykonać skrypt, kliknij przycisk **kopiowania** przycisk i Wklej zawartość w chmurze powłoki, naciśnij klawisz `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnych utworzonych za pomocą różne modele wdrażania. Skopiuj poniższy skrypt do edytora tekstu, na komputerze. Zastąp `<subscription id>` z Twojego identyfikatora subskrypcji. Jeśli nie znasz identyfikator subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji Wklej zmodyfikowane skrypt w sesji środowiska interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnej dla sieci wirtualnej (Resource Manager). Skopiuj poniższe polecenie, wklej go w sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Przedstawia dane wyjściowe **połączony** w **PeeringState** kolumny. 

    Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
8. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
9. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-cli) w tym artykule.

## <a name="powershell"></a>Utwórz równorzędna — PowerShell

1. Zainstaluj najnowszą wersję programu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) i [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modułów. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell zaloguj się do platformy Azure, wprowadzając polecenie `Add-AzureAccount`.
4. Aby utworzyć sieć wirtualną (klasyczne) przy użyciu programu PowerShell, możesz utworzyć nową lub zmodyfikować istniejący, plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizować i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujące **VirtualNetworkSite** elementu dla sieci wirtualnej używane w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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
5. Logowanie do platformy Azure, aby utworzyć sieć wirtualną (Resource Manager), wprowadzając `login-azurermaccount` polecenia. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Zobacz [uprawnienia](#permissions) sekcji tego artykułu, aby uzyskać szczegółowe informacje.
6. Utwórz grupę zasobów i sieć wirtualną (Resource Manager). Skopiuj skrypt, wklej go do programu PowerShell i naciśnij klawisz `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnych utworzonych za pomocą różne modele wdrażania. Skopiuj poniższy skrypt do edytora tekstu, na komputerze. Zastąp `<subscription id>` z Twojego identyfikatora subskrypcji. Jeśli nie znasz identyfikator subskrypcji, wprowadź `Get-AzureRmSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** zwrócony wynik jest Twojego identyfikatora subskrypcji. Można wykonać skryptu, skopiuj skrypt modyfikacji z edytora tekstu, a następnie kliknij prawym przyciskiem myszy w sesji programu PowerShell, a następnie naciśnij klawisz `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnej dla sieci wirtualnej (Resource Manager). Skopiuj poniższe polecenie, wklej go w sesji programu PowerShell i naciśnij klawisz `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Przedstawia dane wyjściowe **połączony** w **PeeringState** kolumny.

    Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
10. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-powershell) w tym artykule.
 
## <a name="permissions"></a>Uprawnienia

Konta, które służy do tworzenia sieci wirtualnej komunikacji równorzędnej musi mieć ról lub uprawnień. Na przykład jeśli zostały równorzędna dwie sieci wirtualne o nazwach myVnet1 i myVnet2, Twoje konto musi zostać przypisane następujące minimalne roli lub uprawnienia dla każdej sieci wirtualnej:
    
|Sieć wirtualna|Model wdrażania|Rola|Uprawnienia|
|---|---|---|---|
|myVnet1|Resource Manager|[Współautor sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Wdrożenie klasyczne|[Współautor klasycznej sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|ND|
|myVnet2|Resource Manager|[Współautor sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Wdrożenie klasyczne|[Współautor klasycznej sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Dowiedz się więcej o [wbudowane role](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) i przypisywanie określonych uprawnień do [role niestandardowe](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (tylko Resource Manager).

## <a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka można usunąć utworzony w samouczka w celu uniknięcia opłat użycie zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroup**. W wynikach wyszukiwania kliknij **myResourceGroup**.
2. Na **myResourceGroup** bloku, kliknij przycisk **usunąć** ikony.
3. Aby potwierdzić decyzję, w **typu nazwa grupy zasobów** wprowadź **myResourceGroup**, a następnie kliknij przycisk **usunąć**.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Użyj 2.0 interfejsu wiersza polecenia Azure, można usunąć sieci wirtualnej (Resource Manager) za pomocą następującego polecenia:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Użyj 1.0 interfejsu wiersza polecenia Azure, aby usunąć sieć wirtualna (klasyczna) za pomocą następujących poleceń:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Wprowadź następujące polecenie, aby usunąć sieć wirtualną (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Można usunąć sieci wirtualnej (wdrożenia klasyczne) przy użyciu programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizować i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używane w tym samouczku:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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

## <a name="register"></a>Zarejestrować w wersji zapoznawczej komunikacji równorzędnej globalnej sieci wirtualnej

Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej w tym samym regionie jest ogólnie dostępne. Równorzędna sieci wirtualnych w różnych regionach jest obecnie w przeglądzie. Zobacz [aktualizacje sieci wirtualnej](https://azure.microsoft.com/en-us/updates/?product=virtual-network) dla dostępnych regionów. -To-peer sieci wirtualnych w regionach, najpierw należy zarejestrować w preview, wykonując następujące czynności (w ramach subskrypcji, w każdej sieci wirtualnej, aby elementu równorzędnego jest w) przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia Azure:

### <a name="powershell"></a>PowerShell

1. Zainstaluj najnowszą wersję modułu [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) programu PowerShell. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell i zaloguj się do platformy Azure przy użyciu `Login-AzureRmAccount` polecenia.
3. Zarejestruj subskrypcję, która każdej sieci wirtualnej, który chcesz elementu równorzędnego jest w wersji zapoznawczej, wprowadzając następujące polecenia:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Upewnij się, że są zarejestrowane dla wersji zapoznawczej, wprowadzając następujące polecenie:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    W sekcjach szablonu portalu, interfejsu wiersza polecenia Azure, programu PowerShell lub Menedżera zasobów tego artykułu, dopóki nie zostanie wypełnione **RegistrationState** dane wyjściowe po wprowadzeniu poprzedniego polecenia jest wyświetlany **zarejestrowane**  dla obu subskrypcji.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Upewnij się, czy używasz wersji 2.0.18 lub wyższej z wiersza polecenia platformy Azure, wprowadzając `az --version` polecenia. Jeśli nie masz, zainstaluj najnowszą wersję.
3. Logowanie do platformy Azure z `az login` polecenia.
4. Zarejestruj w wersji zapoznawczej, wprowadzając następujące polecenia:

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Upewnij się, że są zarejestrowane dla wersji zapoznawczej, wprowadzając następujące polecenie:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    W sekcjach szablonu portalu, interfejsu wiersza polecenia Azure, programu PowerShell lub Menedżera zasobów tego artykułu, dopóki nie zostanie wypełnione **RegistrationState** dane wyjściowe po wprowadzeniu poprzedniego polecenia jest wyświetlany **zarejestrowane**  dla obu subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

- Należy dokładnie zapoznać się z ważne [ograniczenia komunikacji równorzędnej sieci wirtualnej i zachowania](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem sieci wirtualnej komunikacji równorzędnej w środowisku produkcyjnym należy używać.
- Więcej informacji na temat wszystkich [sieci wirtualnej komunikacji równorzędnej ustawienia](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [tworzenie koncentratora i gwiazda topologii sieci](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z sieci wirtualnej komunikacji równorzędnej.
