---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej platformy Azure — różne modele wdrażania — różne subskrypcje
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzoną za pomocą różnych modeli wdrażania platformy Azure, które istnieją w różnych subskrypcjach platformy Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 6823514e284f75f0580578dcabaa1b1bdcbe2f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239836"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Tworzenie komunikacji równorzędnej sieci wirtualnej — różne modele wdrażania i subskrypcje

W tym samouczku nauczysz się tworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzoną za pośrednictwem różnych modeli wdrażania. Sieci wirtualne istnieją w różnych subskrypcjach. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasoby w różnych sieciach wirtualnych do komunikowania się ze sobą z taką samą przepustowość i opóźnienia, jak gdyby zasoby były w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacji równorzędnej w sieci wirtualnej](virtual-network-peering-overview.md).

Kroki tworzenia komunikacji równorzędnej sieci wirtualnej są różne, w zależności od tego, czy sieci wirtualne są w tej samej lub innej subskrypcji i który [model wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pośrednictwem. Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej w innych scenariuszach, klikając scenariusz z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Resource Manager — w obu przypadkach](tutorial-connect-virtual-networks-portal.md) |Ta sama|
|[Resource Manager — w obu przypadkach](create-peering-different-subscriptions.md) |Różne|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models.md) |Ta sama|

Nie można utworzyć komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. W tym samouczku użyto sieci wirtualnych, które istnieją w tym samym regionie. W tym samouczku są wiązki sieci wirtualnych w tym samym regionie. Można również równorzędne sieci wirtualne w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region). Zaleca się zapoznanie się z [wymaganiami i ograniczeniami komunikacji równorzędnej](virtual-network-manage-peering.md#requirements-and-constraints) przed komunikacją równorzędną sieci wirtualnych.

Podczas tworzenia komunikacji równorzędnej sieci wirtualnej między sieciami wirtualnymi, które istnieją w różnych subskrypcjach, subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Jeśli nie masz jeszcze dzierżawy usługi Azure Active Directory, możesz ją szybko [utworzyć.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant) Sieci wirtualne można łączyć w różnych subskrypcjach i różnych dzierżawach usługi Azure Active Directory przy użyciu [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)platformy Azure.

Za pomocą [witryny Azure Portal](#portal), [interfejsu wiersza polecenia](#cli) platformy Azure (CLI) lub programu Azure [PowerShell](#powershell) można utworzyć komunikację równorzędną sieci wirtualnej. Kliknij dowolne z poprzednich łączy narzędzi, aby przejść bezpośrednio do kroków tworzenia komunikacji równorzędnej sieci wirtualnej przy użyciu wybranego narzędzia.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Tworzenie komunikacji równorzędnej — witryna Azure portal

W tym samouczku użyto różnych kont dla każdej subskrypcji. Jeśli używasz konta, które ma uprawnienia do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć kroki wylogowania z portalu i pominąć kroki przypisywania innym użytkownikom uprawnień do sieci wirtualnych.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako UserA. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
2. Kliknij **przycisk + Nowy**, kliknij pozycję **Sieć**, a następnie kliknij pozycję **Sieć wirtualna**.
3. W bloku **Tworzenie sieci wirtualnej** wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Imię i nazwisko**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślna*
    - **Zakres adresów podsieci:** *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję A.
    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowe** i wprowadź *myResourceGroupA*
    - **Lokalizacja**: *Wschodnie stany USA*
4. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetA*. Kliknij **myVnetA,** gdy pojawi się w wynikach wyszukiwania. Dla sieci wirtualnej **myVnetA** pojawi się blok.
5. W wyświetlonym bloku **myVnetA** kliknij pozycję **Kontrola dostępu (IAM)** z pionowej listy opcji po lewej stronie bloku.
6. W wyświetlonym bloku **myVnetA — kontrola dostępu (IAM)** kliknij przycisk **+ Dodaj przypisanie roli**.
7. W wyświetlonym bloku **Dodaj przypisanie roli** wybierz pozycję **Współautor sieci** w polu **Rola.**
8. W polu **Wybierz** wybierz userB lub wpisz adres e-mail UserB, aby go wyszukać. Lista wyświetlanych użytkowników pochodzi z tej samej dzierżawy usługi Azure Active Directory co sieć wirtualna, dla której konfigurujesz komunikację równorzędnej. Kliknij userb, gdy pojawi się na liście.
9. Kliknij przycisk **Zapisz**.
10. Wyloguj się z portalu jako UserA, a następnie zaloguj się jako UserB.
11. Kliknij **przycisk + Nowy**, wpisz sieć *wirtualną* w polu **Wyszukaj w portalu Marketplace,** a następnie kliknij pozycję **Sieć wirtualna** w wynikach wyszukiwania.
12. W wyświetlonym bloku **Sieć wirtualna** wybierz pozycję **Klasyczny** w polu **Wybierz model wdrożenia,** a następnie kliknij przycisk **Utwórz**.
13. W wyświetlonym polu Utwórz sieć wirtualną (klasyczna) wprowadź następujące wartości:

    - **Imię i nazwisko**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślna*
    - **Zakres adresów podsieci:** *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję B.
    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowe** i wprowadź *myResourceGroupB*
    - **Lokalizacja**: *Wschodnie stany USA*

14. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetB*. Kliknij **myVnetB,** gdy pojawi się w wynikach wyszukiwania. Dla sieci wirtualnej **myVnetB** pojawi się blok.
15. W wyświetlonym bloku **myVnetB** kliknij pozycję **Właściwości** z pionowej listy opcji po lewej stronie bloku. Skopiuj **identyfikator zasobu**, który jest używany w późniejszym kroku. Identyfikator zasobu jest podobny do następującego przykładu:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Wykonaj kroki 5-9 dla myVnetB, wprowadzając **UserA** w kroku 8.
17. Wyloguj się z portalu jako UserB i zaloguj się jako UserA.
18. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetA*. Kliknij **myVnetA,** gdy pojawi się w wynikach wyszukiwania. Dla sieci wirtualnej **myVnet** pojawi się blok.
19. Kliknij **myVnetA**.
20. W wyświetlonym bloku **myVnetA** kliknij przycisk **Peerings** z pionowej listy opcji po lewej stronie bloku.
21. W **myVnetA - Peerings** blade, który pojawił się, kliknij **+ Dodaj**
22. W wyświetlonym bloku **Dodaj komunikację równorzędną** wprowadź lub wybierz następujące opcje, a następnie kliknij przycisk **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrażania sieci wirtualnej**: Wybierz **klasyczny**.
     - **Znam identyfikator zasobu:** Zaznacz to pole.
     - **Identyfikator zasobu:** Wprowadź identyfikator zasobu myVnetB z kroku 15.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że **wybrano** opcję Włączone.
    Żadne inne ustawienia nie są używane w tym samouczku. Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej, zobacz [Zarządzanie komunikacją równorzędową w sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknięciu **przycisku OK** w poprzednim kroku, **Dodaj równorzędne** bloku zamyka i widzisz **myVnetA - Peerings** bloku ponownie. Po kilku sekundach utworzona komunikacja równorzędna pojawia się w bloku. **Połączono** jest wymieniony w kolumnie **STAN komunikacji równorzędnej** dla utworzonej komunikacji równorzędnej **myVnetAToMyVnetB.** Komunikacja równorzędna jest teraz ustanowiona. Nie ma potrzeby równowaki sieci wirtualnej (klasycznej) z siecią wirtualną (Menedżer zasobów).

    Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
25. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w sekcji [Usuwanie zasobów](#delete-portal) w tym artykule.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Tworzenie komunikacji równorzędnej — interfejsu wiersza polecenia platformy Azure

W tym samouczku użyto różnych kont dla każdej subskrypcji. Jeśli używasz konta, które ma uprawnienia do obu subskrypcji, można użyć tego samego konta dla wszystkich kroków, pomiń kroki wylogowywania się z platformy Azure i usuń wiersze skryptu, które tworzą przypisania ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich poniższych skryptach nazwy użytkowników, których używasz dla UserA i UserB. Wykonaj następujące kroki przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure. Możesz wykonać kroki z usługi Azure Cloud Shell, po prostu wybierając przycisk **Wypróbuj** w dowolnym z poniższych kroków lub instalując [klasyczną interfejs wiersza polecenia i](/cli/azure/install-classic-cli) interfejsu [wiersza polecenia](/cli/azure/install-azure-cli) i uruchamiając polecenia na komputerze lokalnym.

1. Jeśli używasz usługi Cloud Shell, przejdź do kroku 2, ponieważ usługa Cloud Shell automatycznie loguje cię na platformie Azure. Otwórz sesję polecenia i zaloguj się `azure login` na platformie Azure za pomocą polecenia.
2. Uruchom klasyczną interfejs wiersza polecenia `azure config mode asm` w trybie zarządzania usługami, wprowadzając polecenie.
3. Wprowadź następujące klasyczne polecenie interfejsu wiersza polecenia, aby utworzyć sieć wirtualną (klasyczną):

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Pozostałe kroki muszą zostać wykonane przy użyciu powłoki bash z interfejsu wiersza polecenia platformy Azure (nie klasycznego interfejsu wiersza polecenia).
5. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<SubscriptionB-Id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` to polecenie. Wartością **dla identyfikatora** w danych wyjściowych jest identyfikator subskrypcji. `Enter`

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Podczas tworzenia sieci wirtualnej (klasycznej) w kroku 4 platforma Azure utworzyła sieć wirtualną w grupie zasobów *sieci domyślnych.*
6. Zaloguj UserB z platformy Azure i zaloguj się jako UserA w interfejsie wiersza polecenia.
7. Tworzenie grupy zasobów i sieci wirtualnej (Menedżer zasobów). Skopiuj następujący skrypt, wklej go do `Enter`sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz .

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

8. Utwórz komunikację równorzędną sieci wirtualnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<SubscriptionB-id>` na identyfikator subskrypcji. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` to polecenie. Wartość **identyfikatora** w danych wyjściowych to identyfikator subskrypcji. *Default-Networking* Wklej zmodyfikowany skrypt w sesji interfejsu `Enter`wiersza polecenia, a następnie naciśnij klawisz .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Po wykonaniu skryptu przejrzyj komunikację równorzędnej dla sieci wirtualnej (Menedżer zasobów). Skopiuj następujący skrypt, a następnie wklej go do sesji interfejsu wiersza polecenia:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    Dane wyjściowe pokazuje **połączone** w **peeringstate** kolumny.

    Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
11. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w [temacie Usuwanie zasobów](#delete-cli) w tym artykule.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Tworzenie komunikacji równorzędnej — program PowerShell

W tym samouczku użyto różnych kont dla każdej subskrypcji. Jeśli używasz konta, które ma uprawnienia do obu subskrypcji, można użyć tego samego konta dla wszystkich kroków, pomiń kroki wylogowywania się z platformy Azure i usuń wiersze skryptu, które tworzą przypisania ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich poniższych skryptach nazwy użytkowników, których używasz dla UserA i UserB. 

1. Zainstaluj najnowszą wersję modułów powershell [Azure](https://www.powershellgallery.com/packages/Azure) i [Az.](https://www.powershellgallery.com/packages/Az) Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell zaloguj się do subskrypcji UserB jako `Add-AzureAccount` UserB, wprowadzając polecenie. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
4. Aby utworzyć sieć wirtualną (klasyczną) za pomocą programu PowerShell, należy utworzyć nowy lub zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujący element **VirtualNetworkSite** dla sieci wirtualnej używanej w tym samouczku:

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
    > Importowanie zmienionego pliku konfiguracji sieci może spowodować zmiany w istniejących sieciach wirtualnych (klasycznych) w ramach subskrypcji. Upewnij się, że dodasz tylko poprzednią sieć wirtualną i że nie zmienisz ani nie usuniesz żadnych istniejących sieci wirtualnych z subskrypcji. 

5. Zaloguj się do subskrypcji UserB jako UserB, aby używać `Connect-AzAccount` poleceń Menedżera zasobów, wprowadzając polecenie.
6. Przypisz uprawnienia UserA do sieci wirtualnej B. Skopiuj `<SubscriptionB-id>` następujący skrypt do edytora tekstu na komputerze i zastąp identyfikatorem subskrypcji B. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby go wyświetlić. Wartość **identyfikatora** w zwróconym wyjściu jest identyfikator subskrypcji. Platforma Azure utworzyła sieć wirtualną (klasyczną) utworzoną w kroku 4 w grupie zasobów o nazwie *Sieć domyślna*. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go `Enter`do programu PowerShell, a następnie naciśnij klawisz .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Wyloguj się z platformy Azure jako UserB i zaloguj `Connect-AzAccount` się do subskrypcji UserA jako UserA, wprowadzając polecenie. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
8. Utwórz sieć wirtualną (Menedżer zasobów), kopiując następujący skrypt, wklejając `Enter`go do programu PowerShell, a następnie naciskając:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Przypisywanie uprawnień UserB do myVnetA. Skopiuj następujący skrypt do edytora tekstu na komputerze i zastąp `<SubscriptionA-Id>` identyfikatorem subskrypcji A. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby go wyświetlić. Wartość **identyfikatora** w zwróconym wyjściu jest identyfikator subskrypcji. Wklej zmodyfikowaną wersję skryptu w programie PowerShell, a następnie naciśnij, `Enter` aby go wykonać.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Skopiuj następujący skrypt do edytora `<SubscriptionB-id>` tekstu na komputerze i zastąp identyfikatorem subskrypcji B. Aby nazwać myVnetA myVnetA myVNetB, skopiuj zmodyfikowany `Enter`skrypt, wklej go do programu PowerShell, a następnie naciśnij klawisz .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Wyświetl stan komunikacji równorzędnej myVnetA, kopiując następujący skrypt, `Enter`wklejając go do programu PowerShell i naciskając .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan jest **połączony**. To zmienia się **na Połączony** po skonfigurowaniu komunikacji równorzędnej do myVnetA z myVnetB.

    Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
13. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w [temacie Usuwanie zasobów](#delete-powershell) w tym artykule.

## <a name="delete-resources"></a><a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka można usunąć zasoby utworzone w samouczku, aby nie ponosić opłat za użycie. Usunięcie grupy zasobów powoduje również usunięcie wszystkich zasobów w grupie zasobów.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal Azure

1. W polu wyszukiwania portalu wpisz **myResourceGroupA**. W wynikach wyszukiwania kliknij pozycję **myResourceGroupA**.
2. W bloku **myResourceGroupA** kliknij ikonę **Usuń.**
3. Aby potwierdzić usunięcie, w polu **TYP NAZWA GRUPY ZASOBÓW** wprowadź **myResourceGroupA**, a następnie kliknij przycisk **Usuń**.
4. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetB*. Kliknij **myVnetB,** gdy pojawi się w wynikach wyszukiwania. Dla sieci wirtualnej **myVnetB** pojawi się blok.
5. W bloku **myVnetB** kliknij przycisk **Usuń**.
6. Aby potwierdzić usunięcie, kliknij przycisk **Tak** w polu **Usuń sieć wirtualną.**

### <a name="azure-cli"></a><a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zaloguj się na platformie Azure przy użyciu interfejsu wiersza polecenia, aby usunąć sieć wirtualną (Menedżer zasobów) za pomocą następującego polecenia:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Zaloguj się na platformie Azure przy użyciu klasycznego interfejsu wiersza polecenia, aby usunąć sieć wirtualną (klasyczną) za pomocą następujących poleceń:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. W wierszu polecenia programu PowerShell wprowadź następujące polecenie, aby usunąć sieć wirtualną (Menedżer zasobów):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Aby usunąć sieć wirtualną (klasyczną) za pomocą programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportować, aktualizować i importować pliki konfiguracji sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używanej w tym samouczku:

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
   > Importowanie zmienionego pliku konfiguracji sieci może spowodować zmiany w istniejących sieciach wirtualnych (klasycznych) w ramach subskrypcji. Upewnij się, że usuniesz tylko poprzednią sieć wirtualną i że nie zmienisz ani nie usuniesz innych istniejących sieci wirtualnych z subskrypcji. 

## <a name="next-steps"></a>Następne kroki

- Dokładnie zapoznaj się z [ważnymi ograniczeniami i zachowaniami komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem komunikacji równorzędnej sieci wirtualnej do użytku produkcyjnego.
- Dowiedz się więcej o wszystkich [ustawieniach komunikacji równorzędnej w sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [utworzyć topologię sieci i koncentratorów](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) sieci z komunikacją równorzędną sieci wirtualnej.