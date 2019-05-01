---
title: Tworzenie sieci wirtualnej platformy Azure komunikację równorzędną - różnych modeli wdrażania — tej samej subskrypcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną, komunikacja równorzędna między sieciami wirtualnymi utworzonymi za pomocą różnych Azure modeli wdrażania istnieje w tej samej subskrypcji platformy Azure.
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
ms.author: kumud;anavin
ms.openlocfilehash: 56474ee56051c3b0b7482e81b0174b7945537654
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694710"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Utworzyć komunikację równorzędną sieci wirtualnej — różne modele wdrażania, w tej samej subskrypcji

W tym samouczku dowiesz się, jak utworzyć wirtualną sieć równorzędną między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Obie sieci wirtualne istnieją w tej samej subskrypcji. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasobów w różnych sieciach wirtualnych do komunikowania się ze sobą przy użyciu tego samego przepustowości i opóźnienia, tak, jakby zasoby były w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

Kroki, aby utworzyć wirtualne sieci równorzędne są różne w zależności od tego, czy sieci wirtualne są w tej samej lub różnych subskrypcji i który [modelu wdrażania platformy](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną, komunikacja równorzędna w innych scenariuszach, klikając przycisk w scenariuszu z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ten sam|
|[Obie usługi Resource Manager](create-peering-different-subscriptions.md) |Różne|
|[Jeden usługi Resource Manager, druga — Model Klasyczny](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć wirtualnej sieci równorzędnej między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Jeśli zachodzi potrzeba łączenia sieci wirtualnych, zarówno utworzone za pomocą klasycznego modelu wdrażania, możesz użyć platformy Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do łączenia sieci wirtualnych.

W tym samouczku równorzędnych sieci wirtualnych w tym samym regionie. Można także komunikacji równorzędnej sieci wirtualnych w różnych [obsługiwane regiony](virtual-network-manage-peering.md#cross-region). Zalecane jest, że należy zapoznać się z [komunikacji równorzędnej wymagania i ograniczenia](virtual-network-manage-peering.md#requirements-and-constraints) przed komunikacji równorzędnej sieci wirtualnych.

Można użyć witryny Azure portal, Azure [interfejsu wiersza polecenia](#cli) (CLI) platformy Azure [PowerShell](#powershell), lub szablonu usługi Azure Resource Manager do tworzenia wirtualnej sieci równorzędnej. Kliknij dowolny z poprzednich łącza narzędzia, aby przejść bezpośrednio do kroki tworzenia wirtualnej sieci równorzędnej przy użyciu narzędzia, wybranego.

## <a name="create-peering---azure-portal"></a>Utworzyć komunikację równorzędną — witryna Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Konto, na którym się logujesz, musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#requirements-and-constraints).
2. Kliknij przycisk **+ nowy**, kliknij przycisk **sieć**, następnie kliknij przycisk **sieć wirtualna**.
3. W **Utwórz sieć wirtualną** bloku, wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Nazwa**: *myVnet1*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: Wybierz swoją subskrypcję
    - **Grupa zasobów**: Wybierz **Utwórz nową** i wprowadź *myResourceGroup*
    - **Lokalizacja**: *Wschodnie stany USA*
4. Kliknij pozycję **+ Nowy**. W **Przeszukaj witrynę Marketplace** wpisz *sieć wirtualna*. Kliknij przycisk **sieć wirtualna** , gdy pojawia się w wynikach wyszukiwania.
5. W **sieć wirtualna** bloku wybierz **klasycznego** w **wybierz model wdrożenia** , a następnie kliknij przycisk **Utwórz**.
6. W **Utwórz sieć wirtualną** bloku, wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Nazwa**: *myVnet2*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybierz swoją subskrypcję
    - **Grupa zasobów**: Wybierz **Użyj istniejącej** i wybierz *myResourceGroup*
    - **Lokalizacja**: *Wschodnie stany USA*
7. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myResourceGroup*. Kliknij przycisk **myResourceGroup** , gdy pojawia się w wynikach wyszukiwania. Zostanie wyświetlony blok **myresourcegroup** grupy zasobów. Grupa zasobów zawiera dwie sieci wirtualne utworzone w poprzednich krokach.
8. Kliknij przycisk **myVNet1**.
9. W **myVnet1** bloku, które zostanie wyświetlone, kliknij przycisk **komunikacje równorzędne** z pionowy listy opcji po lewej stronie bloku.
10. W **myVnet1 — komunikacja równorzędna** bloku, które wystąpiły, kliknij przycisk **+ Dodaj**
11. W **Dodaj komunikację równorzędną** bloku, który pojawia się, wprowadź lub wybierz poniższe opcje, a następnie kliknij przycisk **OK**:
     - **Nazwa**: *myVnet1ToMyVnet2*
     - **Model wdrożenia sieci wirtualnej**:  Wybierz **klasycznego**.
     - **Subskrypcja**: Wybierz swoją subskrypcję
     - **Sieć wirtualna**:  Kliknij przycisk **wybierz sieć wirtualną**, następnie kliknij przycisk **myVnet2**.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że **włączone** jest zaznaczone.
    Żadne inne ustawienia są używane w ramach tego samouczka. Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacja równorzędna sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknięciu przycisku **OK** w poprzednim kroku **Dodaj komunikację równorzędną** blok zostanie zamknięty i zostanie wyświetlony **myVnet1 — komunikacja równorzędna** ponownie blok. Po kilku sekundach komunikacji równorzędnej, utworzony zostanie wyświetlony w bloku. **Połączone** znajduje się w **stan komunikacji RÓWNORZĘDNEJ** kolumny **myVnet1ToMyVnet2** komunikacji równorzędnej zostanie utworzony.

    Teraz nawiązano komunikacji równorzędnej. Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są możliwe do rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
14. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-portal) dalszej części tego artykułu.

## <a name="cli"></a>Utworzyć komunikację równorzędną — interfejs wiersza polecenia platformy Azure

Wykonaj poniższe kroki, które są za pomocą platformy Azure klasyczny interfejs wiersza polecenia i interfejsu wiersza polecenia platformy Azure. Możesz wykonać kroki z usługi Azure Cloud Shell, po prostu wybierając **wypróbuj** przycisk w dowolnym z następujących czynności lub dzięki zainstalowaniu [klasyczny interfejs wiersza polecenia](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) i [interfejsu wiersza polecenia](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) i uruchamianie polecenia na komputerze lokalnym.

1. Jeśli używasz powłoki Cloud, przejdź do kroku 2, ponieważ w usłudze Cloud Shell automatycznie zaloguje Cię na platformie Azure. Otwórz sesję programu polecenia, a następnie zaloguj się do platformy Azure za pomocą `azure login` polecenia.
2. Uruchom interfejs wiersza polecenia w trybie zarządzania usługami, wprowadzając `azure config mode asm` polecenia.
3. Wprowadź następujące polecenie, aby utworzyć sieć wirtualną (klasyczny):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Uruchom następujący skrypt interfejsu wiersza polecenia powłoki bash, przy użyciu interfejsu wiersza polecenia, nie klasyczny interfejs wiersza polecenia. Dla opcji na uruchamianie programu bash skryptami interfejsu wiersza polecenia na komputerze Windows, zobacz [zainstalować interfejs wiersza polecenia platformy Azure na Windows](/cli/azure/install-azure-cli-windows).

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

5. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania za pomocą interfejsu wiersza polecenia. Skopiuj poniższy skrypt do edytora tekstów na komputerze. Zastąp `<subscription id>` przy użyciu identyfikatora subskrypcji. Jeśli nie znasz Identyfikatora subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji. Wklej zmodyfikowany skrypt w sesji środowiska interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter`.

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

6. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnej sieci wirtualnej (Resource Manager). Skopiuj poniższe polecenie, wklej go w sesji programu interfejsu wiersza polecenia i naciśnij klawisz `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Pokazano w danych wyjściowych **połączono** w **PeeringState** kolumny.

   Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są możliwe do rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
8. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-cli) w tym artykule.

## <a name="powershell"></a>Utworzyć komunikację równorzędną — PowerShell

1. Zainstaluj najnowszą wersję programu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) i [Az](https://www.powershellgallery.com/packages/Az/) modułów. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell Zaloguj się do platformy Azure, wprowadzając `Add-AzureAccount` polecenia. Konto, na którym się logujesz, musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#requirements-and-constraints).
4. Do utworzenia sieci wirtualnej (model klasyczny) przy użyciu programu PowerShell, możesz utworzyć nową lub zmodyfikować istniejący, plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizowanie i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujące **VirtualNetworkSite** elementu dla sieci wirtualnej używane w tym samouczku:

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
    > Importowanie pliku konfiguracyjnego sieci zmienione mogą powodować zmiany w istniejących sieciach wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, należy dodawać tylko wtedy poprzedniego sieci wirtualnej i nie zmienić lub usunąć wszystkie istniejące sieci wirtualne z subskrypcji.
5. Logowanie do platformy Azure, aby utworzyć sieć wirtualną (Resource Manager), wprowadzając `Connect-AzAccount` polecenia. Konto, na którym się logujesz, musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#requirements-and-constraints).
6. Utwórz grupę zasobów i sieci wirtualnej (Resource Manager). Skopiuj skrypt, wklej go do programu PowerShell i naciśnij klawisz `Enter`.

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

7. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Skopiuj poniższy skrypt do edytora tekstów na komputerze. Zastąp `<subscription id>` przy użyciu identyfikatora subskrypcji. Jeśli nie znasz Identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** w dane wyjściowe to identyfikator subskrypcji. Można wykonać skryptu, skopiuj zmodyfikowany skrypt z Twojego edytora tekstu, a następnie kliknij prawym przyciskiem myszy w sesji programu PowerShell, a następnie naciśnij klawisz `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnej sieci wirtualnej (Resource Manager). Skopiuj poniższe polecenie, wklej go w sesji programu PowerShell i naciśnij klawisz `Enter`:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Pokazano w danych wyjściowych **połączono** w **PeeringState** kolumny.

    Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są możliwe do rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Opcjonalnie**: Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
10. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-powershell) w tym artykule.

## <a name="delete"></a>Usuń zasoby

Po ukończeniu tego samouczka, możesz chcieć usunąć zasoby utworzone w tym samouczku, więc nie powodują naliczania opłat za użycie. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure Portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroup**. W wynikach wyszukiwania kliknij **myResourceGroup**.
2. Na **myResourceGroup** bloku kliknij **Usuń** ikony.
3. Aby potwierdzić usunięcie, w **wpisz nazwę grupy zasobów** wprowadź **myResourceGroup**, a następnie kliknij przycisk **Usuń**.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Użyj wiersza polecenia platformy Azure można usunąć sieci wirtualnej (Resource Manager) za pomocą następującego polecenia:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Użyj klasyczny interfejs wiersza polecenia, aby usunąć sieć wirtualna (klasyczna), za pomocą następujących poleceń:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Wprowadź następujące polecenie, aby usunąć sieci wirtualnej (Resource Manager):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Można usunąć sieci wirtualnej (model klasyczny) przy użyciu programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizowanie i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używane w tym samouczku:

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
    > Importowanie pliku konfiguracyjnego sieci zmienione mogą powodować zmiany w istniejących sieciach wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, tylko Usuń poprzednie sieci wirtualnej, a nie zmienić lub usunąć innych istniejących sieci wirtualnych z subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

- Należy dokładnie zapoznać się z ważne [ograniczenia komunikacji równorzędnej sieci wirtualnej i zachowania](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem sieci wirtualnej komunikacji równorzędnej w środowisku produkcyjnym należy używać.
- Dowiedz się więcej o wszystkich [ustawieniami wirtualnych sieci równorzędnych](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [Utwórz koncentrator i topologię sieci typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) za pomocą komunikacji równorzędnej sieci wirtualnej.
