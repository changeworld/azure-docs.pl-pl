---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej platformy Azure — różne modele wdrażania — ta sama subskrypcja | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzoną za pomocą różnych modeli wdrażania platformy Azure, które istnieją w tej samej subskrypcji platformy Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023263"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Tworzenie komunikacji równorzędnej sieci wirtualnej — różne modele wdrażania, ta sama subskrypcja

W tym samouczku nauczysz się tworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzoną za pośrednictwem różnych modeli wdrażania. Obie sieci wirtualne istnieją w tej samej subskrypcji. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasoby w różnych sieciach wirtualnych do komunikowania się ze sobą z taką samą przepustowość i opóźnienia, jak gdyby zasoby były w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacji równorzędnej w sieci wirtualnej](virtual-network-peering-overview.md).

Kroki tworzenia komunikacji równorzędnej sieci wirtualnej są różne, w zależności od tego, czy sieci wirtualne są w tej samej lub innej subskrypcji i który [model wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pośrednictwem. Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej w innych scenariuszach, klikając scenariusz z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Resource Manager — w obu przypadkach](tutorial-connect-virtual-networks-portal.md) |Ta sama|
|[Resource Manager — w obu przypadkach](create-peering-different-subscriptions.md) |Różne|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Jeśli musisz połączyć sieci wirtualne, które zostały utworzone za pośrednictwem klasycznego modelu wdrażania, możesz użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure do połączenia sieci wirtualnych.

W tym samouczku są wiązki sieci wirtualnych w tym samym regionie. Można również równorzędne sieci wirtualne w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region). Zaleca się zapoznanie się z [wymaganiami i ograniczeniami komunikacji równorzędnej](virtual-network-manage-peering.md#requirements-and-constraints) przed komunikacją równorzędną sieci wirtualnych.

Do utworzenia elementu równorzędnego sieci wirtualnej można użyć witryny Azure portal, [interfejsu wiersza polecenia](#cli) platformy Azure (CLI), usługi Azure [PowerShell](#powershell)lub szablonu usługi Azure Resource Manager. Kliknij dowolne z poprzednich łączy narzędzi, aby przejść bezpośrednio do kroków tworzenia komunikacji równorzędnej sieci wirtualnej przy użyciu wybranego narzędzia.

## <a name="create-peering---azure-portal"></a>Tworzenie komunikacji równorzędnej — witryna Azure portal

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Konto, za pomocą którego się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej w sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints).
2. Kliknij **przycisk + Nowy**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Sieć wirtualna**.
3. W bloku **Tworzenie sieci wirtualnej** wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Imię i nazwisko**: *myVnet1*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślna*
    - **Zakres adresów podsieci:** *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję
    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowe** i wprowadź *myResourceGroup*
    - **Lokalizacja**: *Wschodnie stany USA*
4. Kliknij pozycję **+ Nowy**. W polu **Wyszukaj w portalu Marketplace** wpisz polecenie *Sieć wirtualna*. Kliknij **pozycję Sieć wirtualna,** gdy pojawi się w wynikach wyszukiwania.
5. W bloku **Sieć wirtualna** wybierz pozycję **Klasyczny** w polu **Wybierz model wdrożenia,** a następnie kliknij przycisk **Utwórz**.
6. W bloku **Tworzenie sieci wirtualnej** wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Imię i nazwisko**: *myVnet2*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślna*
    - **Zakres adresów podsieci:** *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję
    - **Grupa zasobów**: Wybierz **pozycję Użyj istniejącego** i wybierz *myResourceGroup*
    - **Lokalizacja**: *Wschodnie stany USA*
7. W polu **Wyszukaj zasoby** u góry portalu wpisz *myResourceGroup*. Kliknij **myResourceGroup,** gdy pojawi się w wynikach wyszukiwania. Dla grupy zasobów grupy zasobów **grupy myresourcegroup** pojawi się blok. Grupa zasobów przechowuje dwie sieci wirtualne utworzone w poprzednich krokach.
8. Kliknij **myVNet1**.
9. W wyświetlonym bloku **myVnet1** kliknij pozycję **Peerings** z pionowej listy opcji po lewej stronie bloku.
10. W **myVnet1 - Peerings** bloku, który pojawił się, kliknij **+ Dodaj**
11. W wyświetlonym bloku **Dodaj komunikację równorzędną** wprowadź lub wybierz następujące opcje, a następnie kliknij przycisk **OK**:
     - **Nazwa**: *myVnet1ToMyVnet2*
     - **Model wdrażania sieci wirtualnej**: Wybierz **klasyczny**.
     - **Subskrypcja**: Wybierz subskrypcję
     - **Sieć wirtualna**: Kliknij **wybierz sieć wirtualną,** a następnie kliknij **myVnet2**.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że **wybrano** opcję Włączone.
    Żadne inne ustawienia nie są używane w tym samouczku. Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej, zobacz [Zarządzanie komunikacją równorzędową w sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknięciu **przycisku OK** w poprzednim kroku, **Dodaj równorzędne** bloku zamyka i widzisz **myVnet1 - Peerings** bloku ponownie. Po kilku sekundach utworzona komunikacja równorzędna pojawia się w bloku. **Połączono** jest wymieniony w kolumnie **STAN komunikacji równorzędnej** dla utworzonej komunikacji równorzędnej **myVnet1ToMyVnet2.**

    Komunikacja równorzędna jest teraz ustanowiona. Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
14. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w sekcji [Usuwanie zasobów](#delete-portal) w tym artykule.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Tworzenie komunikacji równorzędnej — interfejsu wiersza polecenia platformy Azure

Wykonaj następujące kroki przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure. Możesz wykonać kroki z usługi Azure Cloud Shell, po prostu wybierając przycisk **Wypróbuj** w dowolnym z poniższych kroków lub instalując [klasyczną interfejs wiersza polecenia i](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) interfejsu [wiersza polecenia](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) i uruchamiając polecenia na komputerze lokalnym.

1. Jeśli używasz usługi Cloud Shell, przejdź do kroku 2, ponieważ usługa Cloud Shell automatycznie loguje cię na platformie Azure. Otwórz sesję polecenia i zaloguj się `azure login` na platformie Azure za pomocą polecenia.
2. Uruchom interfejs wiersza polecenia w `azure config mode asm` trybie zarządzania usługami, wprowadzając polecenie.
3. Wprowadź następujące polecenie, aby utworzyć sieć wirtualną (klasyczna):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Wykonaj następujący skrypt interfejsu wiersza polecenia bash przy użyciu interfejsu wiersza polecenia, a nie klasycznego interfejsu wiersza polecenia. Aby uzyskać opcje uruchamiania skryptów interfejsu wiersza polecenia bash na komputerze z systemem Windows, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

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

5. Utwórz komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania przy użyciu interfejsu wiersza polecenia. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<subscription id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` to polecenie. Wartość **identyfikatora** w danych wyjściowych jest identyfikator subskrypcji. Wklej zmodyfikowany skrypt do sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
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

6. Po wykonaniu skryptu przejrzyj komunikację równorzędnej dla sieci wirtualnej (Menedżer zasobów). Skopiuj następujące polecenie, wklej go `Enter`do sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Dane wyjściowe pokazuje **połączone** w **peeringstate** kolumny.

   Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
8. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w [temacie Usuwanie zasobów](#delete-cli) w tym artykule.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Tworzenie komunikacji równorzędnej — program PowerShell

1. Zainstaluj najnowszą wersję modułów powershell [Azure](https://www.powershellgallery.com/packages/Azure) i [Az.](https://www.powershellgallery.com/packages/Az/) Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell zaloguj się na `Add-AzureAccount` platformie Azure, wprowadzając polecenie. Konto, za pomocą którego się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej w sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints).
4. Aby utworzyć sieć wirtualną (klasyczną) za pomocą programu PowerShell, należy utworzyć nowy lub zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujący element **VirtualNetworkSite** dla sieci wirtualnej używanej w tym samouczku:

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
    > Importowanie zmienionego pliku konfiguracji sieci może spowodować zmiany w istniejących sieciach wirtualnych (klasycznych) w ramach subskrypcji. Upewnij się, że dodasz tylko poprzednią sieć wirtualną i że nie zmienisz ani nie usuniesz żadnych istniejących sieci wirtualnych z subskrypcji.
5. Zaloguj się na platformie Azure, aby utworzyć sieć `Connect-AzAccount` wirtualną (Menedżer zasobów) przez wprowadzenie polecenia. Konto, za pomocą którego się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej w sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints).
6. Tworzenie grupy zasobów i sieci wirtualnej (Menedżer zasobów). Skopiuj skrypt, wklej go do `Enter`programu PowerShell, a następnie naciśnij klawisz .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Utwórz komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<subscription id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby go wyświetlić. Wartość **identyfikatora** w zwróconym wyjściu jest identyfikator subskrypcji. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt z edytora tekstu, następnie kliknij `Enter`prawym przyciskiem myszy sesję programu PowerShell, a następnie naciśnij klawisz .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po wykonaniu skryptu przejrzyj komunikację równorzędnej dla sieci wirtualnej (Menedżer zasobów). Skopiuj następujące polecenie, wklej go do `Enter`sesji programu PowerShell, a następnie naciśnij klawisz :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Dane wyjściowe pokazuje **połączone** w **peeringstate** kolumny.

    Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
10. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w [temacie Usuwanie zasobów](#delete-powershell) w tym artykule.

## <a name="delete-resources"></a><a name="delete"></a>Usuwanie zasobów

Po zakończeniu tego samouczka można usunąć zasoby utworzone w samouczku, aby nie ponosić opłat za użycie. Usunięcie grupy zasobów powoduje również usunięcie wszystkich zasobów w grupie zasobów.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal Azure

1. W polu wyszukiwania portalu wpisz **myResourceGroup**. W wynikach wyszukiwania kliknij pozycję **myResourceGroup**.
2. W bloku **myResourceGroup** kliknij ikonę **Usuń.**
3. Aby potwierdzić usunięcie, w polu **TYP NAZWA GRUPY ZASOBÓW** wprowadź **myResourceGroup**, a następnie kliknij przycisk **Usuń**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Użyj interfejsu wiersza polecenia platformy Azure, aby usunąć sieć wirtualną (Menedżer zasobów) za pomocą następującego polecenia:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Użyj klasycznego interfejsu wiersza polecenia, aby usunąć sieć wirtualną (klasyczną) za pomocą następujących poleceń:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Wprowadź następujące polecenie, aby usunąć sieć wirtualną (Menedżer zasobów):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Aby usunąć sieć wirtualną (klasyczną) za pomocą programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używanej w tym samouczku:

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
    > Importowanie zmienionego pliku konfiguracji sieci może spowodować zmiany w istniejących sieciach wirtualnych (klasycznych) w ramach subskrypcji. Upewnij się, że usuniesz tylko poprzednią sieć wirtualną i że nie zmienisz ani nie usuniesz innych istniejących sieci wirtualnych z subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Dokładnie zapoznaj się z [ważnymi ograniczeniami i zachowaniami komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem komunikacji równorzędnej sieci wirtualnej do użytku produkcyjnego.
- Dowiedz się więcej o wszystkich [ustawieniach komunikacji równorzędnej w sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [utworzyć topologię sieci i koncentratorów](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) sieci z komunikacją równorzędną sieci wirtualnej.
