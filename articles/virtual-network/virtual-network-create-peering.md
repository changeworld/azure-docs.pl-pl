---
title: Tworzenie sieci wirtualnej platformy Azure komunikację równorzędną - Resource Manager — tej samej subskrypcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną komunikacji równorzędnej między sieciami wirtualnymi utworzone za pomocą Menedżera zasobów, które istnieją w tej samej subskrypcji platformy Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: 70fe948070147c01922fab68fb55a0f00c26a0f3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767157"
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>Tworzenie sieci wirtualnej równorzędna - Resource Manager tej samej subskrypcji

Z tego samouczka dowiesz się, można utworzyć sieci wirtualnej komunikacji równorzędnej między sieciami wirtualnymi utworzone za pomocą Menedżera zasobów. Istnieją obie sieci wirtualne w tej samej subskrypcji. Komunikacja równorzędna dwa zasoby umożliwia sieci wirtualnych w różnych sieciach wirtualnych do komunikowania się ze sobą przy tym samym przepustowości i opóźnień, tak jakby były zasoby w tej samej sieci wirtualnej. Dowiedz się więcej o [równorzędna sieci wirtualnej](virtual-network-peering-overview.md). 

Kroki tworzenia sieci wirtualnej komunikacji równorzędnej są różne, w zależności od tego, czy sieci wirtualne są w tym samym lub różnych subskrypcji i które [modelu wdrożenia usługi Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną równorzędna w innych sytuacjach, klikając w scenariuszu z następującej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](create-peering-different-subscriptions.md) |Różne|
|[Jeden Resource Manager, co classic](create-peering-different-deployment-models.md) |tym samym|
|[Jeden Resource Manager, co classic](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania. Jeśli musisz połączyć sieci wirtualnych, które były tworzone przy użyciu klasycznego modelu wdrażania, można użyć Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do łączenia sieci wirtualnej. 

W tym samouczku równorzędnymi użytkownikami sieci wirtualne w tym samym regionie. Możliwość elementów równorzędnych sieci wirtualnych w różnych regionach jest obecnie w przeglądzie. Wykonaj kroki [zarejestrować się w globalnej sieci wirtualnej komunikacji równorzędnej](#register) przed podjęciem próby elementów równorzędnych sieci wirtualnych w różnych regionach lub komunikacji równorzędnej kończy się niepowodzeniem. Możliwość łączenia z platformy Azure, sieci wirtualnych w różnych regionach [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) jest ogólnie dostępna i nie wymaga rejestracji.

Można użyć [portalu Azure](#portal), Azure [interfejsu wiersza polecenia](#cli) (CLI) Azure [PowerShell](#powershell), lub [szablonu usługi Azure Resource Manager](#template)można utworzyć sieci wirtualnej komunikacji równorzędnej. Kliknij dowolny z poprzedniej łączy narzędzia, aby przejść bezpośrednio do kroki tworzenia sieci wirtualnej komunikacji równorzędnej narzędzie wyboru.

## <a name="portal"></a>Utwórz równorzędna - portalu Azure

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
4. Wykonaj kroki 2 – 3 ponownie określanie następujące wartości w kroku 3:
    - **Nazwa**: *myVnet2*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję
    - **Grupa zasobów**: Wybierz **Użyj istniejącego** i wybierz *myResourceGroup*
    - **Lokalizacja**: *wschodnie stany USA*
5. W **wyszukiwania zasobów** pole w górnej części portalu typu *myResourceGroup*. Kliknij przycisk **myResourceGroup** po wyświetleniu w wynikach wyszukiwania. Zostanie wyświetlony blok **myresourcegroup** grupy zasobów. Grupa zasobów zawiera dwie sieci wirtualne utworzone w poprzednich krokach.
6. Kliknij przycisk **myVNet1**.
7. W **myVnet1** bloku, który jest wyświetlany, kliknij przycisk **komunikacji równorzędnych** z pionowy listy opcji w lewej części bloku.
8. W **myVnet1 - komunikacji równorzędnych** bloku, które wystąpiły, kliknij przycisk **+ Dodaj**
9. W **równorzędna Dodaj** bloku, zostanie wyświetlone, wprowadź, lub wybierz poniższe opcje, a następnie kliknij **OK**:
     - **Nazwa**: *myVnet1ToMyVnet2*
     - **Model wdrażania sieci wirtualnej**: Wybierz **Resource Manager**. 
     - **Subskrypcja**: Wybierz subskrypcję
     - **Sieć wirtualna**: kliknij **wybierz sieć wirtualną**, następnie kliknij przycisk **myVnet2**.
     - **Zezwalaj na dostęp do sieci wirtualnej:** upewnij się, że **włączone** jest zaznaczone.
    W tym samouczku są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacji równorzędnych sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
10. Po kliknięciu przycisku **OK** w poprzednim kroku, **równorzędna Dodaj** zamyka bloku, aby zobaczyć **myVnet1 - komunikacji równorzędnych** ponownie blok. Po kilku sekundach komunikacji równorzędnej, utworzony zostanie wyświetlony w bloku. **Zainicjowano** znajduje się w **RÓWNORZĘDNA stan** kolumny dla **myVnet1ToMyVnet2** równorzędna zostanie utworzony. Już połączyć za pomocą Vnet1 do Vnet2, ale teraz musi równorzędnego myVnet2 do myVnet1. Komunikację równorzędną muszą być tworzone w obu kierunkach, aby włączyć zasobów w sieci wirtualne do komunikowania się ze sobą.
11. Wykonaj kroki 5 – 10 ponownie dla myVnet2. Nazwa komunikację równorzędną *myVnet2ToMyVnet1*.
12. Kilka sekund po kliknięciu przycisku **OK** utworzyć komunikacji równorzędnej dla MyVnet2, **myVnet2ToMyVnet1** równorzędna właśnie utworzony znajduje się **połączony** w  **Komunikacja RÓWNORZĘDNA stan** kolumny.
13. Wykonaj kroki 5-7 ponownie dla MyVnet1. **RÓWNORZĘDNA stan** dla **myVnet1ToVNet2** komunikacji równorzędnej jest teraz również **połączony**. Komunikację równorzędną zostanie pomyślnie nawiązane po **połączony** w **RÓWNORZĘDNA stan** kolumny dla obu sieci wirtualnych w komunikacji równorzędnej.
14. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
15. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-portal) sekcji tego artykułu.

Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Utwórz równorzędna - wiersza polecenia platformy Azure

Poniższy skrypt:

- Wymaga wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby znaleźć wersję, uruchom `az --version` polecenia. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Działanie powłoki Bash. Aby wyświetlić opcje uruchamiania skryptów wiersza polecenia platformy Azure na kliencie systemu Windows, zobacz [działający w systemie Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Zamiast instalowania interfejsu wiersza polecenia i jego zależności, można użyć powłoki chmury Azure. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **wypróbuj** przycisk w skrypcie, który następujące, które wywołuje powłoka chmury, która rejestruje możesz zalogować się do konta platformy Azure z. Aby wykonać skrypt, kliknij przycisk **kopiowania** przycisk i Wklej zawartość w chmurze powłoki.

1. Utwórz grupę zasobów i dwie sieci wirtualne.

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnymi.
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnych dla każdej sieci wirtualnej. 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Poprzednie polecenie ponownie uruchomić, zastępując *myVnet1* z *myVnet2*. Zarówno dane wyjściowe polecenia pokazuje **połączony** w **PeeringState** kolumny.

     Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

4. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
5. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-cli) w tym artykule.


## <a name="powershell"></a>Utwórz równorzędna — PowerShell

1. Zainstaluj najnowszą wersję modułu [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) programu PowerShell. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Aby rozpocząć sesję programu PowerShell, przejdź do pozycji **Start**, wprowadź ciąg **powershell**, a następnie kliknij pozycję **PowerShell**.
3. W programie PowerShell zaloguj się do platformy Azure, wprowadzając polecenie `login-azurermaccount`. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Zobacz [uprawnienia](#permissions) sekcji tego artykułu, aby uzyskać szczegółowe informacje.
4. Utwórz grupę zasobów i dwie sieci wirtualne. Można wykonać skryptu, skopiuj poniższy skrypt, wklej go do programu PowerShell, a następnie naciśnij klawisz `Enter` po ostatnim wierszu są wyświetlane na ekranie:

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnymi. Skopiuj poniższy skrypt, Wklej do środowiska PowerShell i naciśnij klawisz `Enter` po ostatnim wierszu są wyświetlane na ekranie:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. Aby przejrzeć podsieci sieci wirtualnej, skopiuj poniższe polecenie, Wklej do środowiska PowerShell i naciśnij klawisz `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Poprzednie polecenie ponownie uruchomić, zastępując *myVnet1* z *myVnet2*. Zarówno dane wyjściowe polecenia pokazuje **połączony** w **PeeringState** kolumny.
7. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
8. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-powershell) w tym artykule.

Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Utwórz równorzędna - szablonu usługi Resource Manager

1. Odwołanie [tworzenie sieci wirtualnej komunikacji równorzędnej](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) szablonu usługi Resource Manager. Szablon jest dostarczany wraz z instrukcjami wdrażania przy użyciu witryny Azure Portal, programu PowerShell lub wiersza polecenia platformy Azure. Logowanie w zależności od tego narzędzia, możesz wybrać opcję wdrożyć szablon przy użyciu konta, które ma odpowiednie uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Zobacz [uprawnienia](#permissions) sekcji tego artykułu, aby uzyskać szczegółowe informacje.
2. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
3. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete) sekcji tego artykułu, przy użyciu portalu Azure, programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="permissions"></a>Uprawnienia

Konta, które służy do tworzenia sieci wirtualnej komunikacji równorzędnej musi mieć ról lub uprawnień. Na przykład jeśli zostały równorzędna dwie sieci wirtualne o nazwach VNet1 i VNet2, Twoje konto musi zostać przypisane następujące minimalne roli lub uprawnienia dla każdej sieci wirtualnej:
    
|Sieć wirtualna|Rola|Uprawnienia|
|---|---|---|
|Sieć wirtualna 1|[Współautor sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[Współautor sieci](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Dowiedz się więcej o [wbudowane role](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) i przypisywanie określonych uprawnień do [role niestandardowe](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (tylko Resource Manager).

## <a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka można usunąć utworzony w samouczka w celu uniknięcia opłat użycie zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroup**. W wynikach wyszukiwania kliknij **myResourceGroup**.
2. Na **myResourceGroup** bloku, kliknij przycisk **usunąć** ikony.
3. Aby potwierdzić decyzję, w **typu nazwa grupy zasobów** wprowadź **myResourceGroup**, a następnie kliknij przycisk **usunąć**.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

Wprowadź następujące polecenie:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Wprowadź następujące polecenie:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

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
