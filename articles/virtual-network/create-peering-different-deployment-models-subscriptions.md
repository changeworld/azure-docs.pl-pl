---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej platformy Azure — modele różnych wdrażania — różne subskrypcje | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną, komunikacja równorzędna między sieciami wirtualnymi utworzonymi za pomocą różnych Azure modeli wdrażania istnieje w innej subskrypcji platformy Azure.
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
ms.openlocfilehash: 4883791a32a65746a72afb63755ecf608dc840d9
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503829"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Utworzyć komunikację równorzędną sieci wirtualnej — różne modele wdrażania i subskrypcje

W tym samouczku dowiesz się, jak utworzyć wirtualną sieć równorzędną między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Sieci wirtualne istnieją w różnych subskrypcjach. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasobów w różnych sieciach wirtualnych do komunikowania się ze sobą przy użyciu tego samego przepustowości i opóźnienia, tak, jakby zasoby były w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

Kroki, aby utworzyć wirtualne sieci równorzędne są różne w zależności od tego, czy sieci wirtualne są w tej samej lub różnych subskrypcji i który [modelu wdrażania platformy](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną, komunikacja równorzędna w innych scenariuszach, klikając przycisk w scenariuszu z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ten sam|
|[Obie usługi Resource Manager](create-peering-different-subscriptions.md) |Różne|
|[Jeden usługi Resource Manager, druga — Model Klasyczny](create-peering-different-deployment-models.md) |Ten sam|

Nie można utworzyć wirtualnej sieci równorzędnej między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Ten samouczek używa sieci wirtualnych, które istnieją w tym samym regionie. W tym samouczku równorzędnych sieci wirtualnych w tym samym regionie. Można także komunikacji równorzędnej sieci wirtualnych w różnych [obsługiwane regiony](virtual-network-manage-peering.md#cross-region). Zalecane jest, że należy zapoznać się z [komunikacji równorzędnej wymagania i ograniczenia](virtual-network-manage-peering.md#requirements-and-constraints) przed komunikacji równorzędnej sieci wirtualnych.

Podczas tworzenia sieci wirtualnej komunikacji równorzędnej między sieciami wirtualnymi istniejącymi w różnych subskrypcjach, subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Jeśli nie masz już dzierżawę usługi Azure Active Directory, możesz szybko [utworzyć](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Można połączyć sieci wirtualne w różnych subskrypcjach i różne usługi Azure Active Directory dzierżawy, za pomocą platformy Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Możesz użyć [witryny Azure portal](#portal), Azure [interfejsu wiersza polecenia](#cli) (CLI) lub na platformie Azure [PowerShell](#powershell) do tworzenia wirtualnej sieci równorzędnej. Kliknij dowolny z poprzednich łącza narzędzia, aby przejść bezpośrednio do kroki tworzenia wirtualnej sieci równorzędnej przy użyciu narzędzia, wybranego.

## <a name="portal"></a>Utworzyć komunikację równorzędną — witryna Azure portal

Ten samouczek używa różnych kont, dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki dla rejestracji z portalu i pominąć kroki do przypisywania innego uprawnienia użytkownika do sieci wirtualnych.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako użytkownik a. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
2. Kliknij przycisk **+ nowy**, kliknij przycisk **sieć**, następnie kliknij przycisk **sieć wirtualna**.
3. W **Utwórz sieć wirtualną** bloku, wprowadź lub wybierz wartości dla następujących ustawień, a następnie kliknij przycisk **Utwórz**:
    - **Nazwa**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję A.
    - **Grupa zasobów**: Wybierz **Utwórz nową** i wprowadź *myResourceGroupA*
    - **Lokalizacja**: *wschodnie stany USA*
4. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetA*. Kliknij przycisk **myVnetA** , gdy pojawia się w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnetA** sieci wirtualnej.
5. W **myVnetA** bloku, które zostanie wyświetlone, kliknij przycisk **kontrola dostępu (IAM)** z pionowy listy opcji po lewej stronie bloku.
6. W **myVnetA — kontrola dostępu (IAM)** bloku, które zostanie wyświetlone, kliknij przycisk **+ Dodaj**.
7. W **Dodaj uprawnienia** wyświetlonym bloku wybierz **Współautor sieci** w **roli** pole.
8. W **wybierz** wybierz UżytkownikB lub wpisz adres e-mail firmy Użytkownik_b ją wyszukać. Na liście użytkowników, wyświetlane jest z tą samą dzierżawą usługi Azure Active Directory, co sieć wirtualna, którą konfigurujesz komunikacji równorzędnej dla. Gdy pojawi się na liście, kliknij przycisk Użytkownik_b.
9. Kliknij pozycję **Zapisz**.
10. Wyloguj się z portalu jako użytkownik a, a następnie zaloguj się jako Użytkownik_b.
11. Kliknij przycisk **+ nowy**, typ *sieć wirtualna* w **Przeszukaj witrynę Marketplace** polu, a następnie kliknij przycisk **sieć wirtualna** w wynikach wyszukiwania.
12. W **sieci wirtualnej** wyświetlonym bloku wybierz **klasycznego** w **wybierz model wdrożenia** polu, a następnie kliknij przycisk **Utwórz**.
13.   W przypadku tworzenia sieci wirtualnej (model klasyczny) pojawi się okno wprowadź następujące wartości:

    - **Nazwa**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję B.
    - **Grupa zasobów**: Wybierz **Utwórz nową** i wprowadź *myResourceGroupB*
    - **Lokalizacja**: *wschodnie stany USA*

14. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetB*. Kliknij przycisk **myVnetB** , gdy pojawia się w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnetB** sieci wirtualnej.
15. W **myVnetB** bloku, które zostanie wyświetlone, kliknij przycisk **właściwości** z pionowy listy opcji po lewej stronie bloku. Kopiuj **identyfikator ZASOBU**, która zostanie użyta w późniejszym kroku. Identyfikator zasobu jest podobny do poniższego przykładu: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Wykonaj kroki 5 – 9 dla myVnetB, wprowadzając **Użytkownik_a** w kroku 8.
17. Wyloguj się z portalu jako UżytkownikB, a następnie zaloguj się jako użytkownik a.
18. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetA*. Kliknij przycisk **myVnetA** , gdy pojawia się w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnet** sieci wirtualnej.
19. Kliknij przycisk **myVnetA**.
20. W **myVnetA** bloku, które zostanie wyświetlone, kliknij przycisk **komunikacje równorzędne** z pionowy listy opcji po lewej stronie bloku.
21. W **myVnetA — komunikacja równorzędna** bloku, które wystąpiły, kliknij przycisk **+ Dodaj**
22. W **Dodaj komunikację równorzędną** bloku, który pojawia się, wprowadź lub wybierz poniższe opcje, a następnie kliknij przycisk **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrożenia sieci wirtualnej**: Wybierz **klasycznego**.
     - **Wiem, identyfikator zasobu**: Zaznacz to pole wyboru.
     - **Identyfikator zasobu**: Podaj identyfikator zasobu myVnetB z kroku 15.
     - **Zezwalaj na dostęp do sieci wirtualnej:** upewnij się, że **włączone** jest zaznaczone.
    Żadne inne ustawienia są używane w ramach tego samouczka. Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacja równorzędna sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknięciu przycisku **OK** w poprzednim kroku **Dodaj komunikację równorzędną** blok zostanie zamknięty i zostanie wyświetlony **myVnetA — komunikacja równorzędna** ponownie blok. Po kilku sekundach komunikacji równorzędnej, utworzony zostanie wyświetlony w bloku. **Połączone** znajduje się w **stan komunikacji RÓWNORZĘDNEJ** kolumny **myVnetAToMyVnetB** komunikacji równorzędnej zostanie utworzony. Teraz nawiązano komunikacji równorzędnej. Nie ma potrzeby nawiązać komunikację równorzędną sieci wirtualnej (klasycznej) do sieci wirtualnej (Resource Manager).

    Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
25. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-portal) dalszej części tego artykułu.

## <a name="cli"></a>Utworzyć komunikację równorzędną — interfejs wiersza polecenia platformy Azure

Ten samouczek używa różnych kont, dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, możesz używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania możliwości platformy Azure i Usuń wiersze skryptu, które tworzenie przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów za pomocą nazw użytkowników, używasz osoby Użytkownik_a i Użytkownik_b. 

1. [Zainstaluj](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 interfejsu wiersza polecenia platformy Azure można utworzyć sieci wirtualnej (model klasyczny).
2. Otwórz sesję programu interfejsu wiersza polecenia, a następnie zaloguj się do platformy Azure, używając Użytkownik_b `azure login` polecenia. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
3. Uruchom interfejs wiersza polecenia w trybie zarządzania usługami, wprowadzając `azure config mode asm` polecenia.
4. Wprowadź następujące polecenie, aby utworzyć sieć wirtualną (klasyczny):
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. Pozostałe kroki należy wykonać przy użyciu powłoki bash, skorupach przy użyciu wiersza polecenia platformy Azure 2.0.4 lub nowszej [zainstalowane](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), lub za pomocą usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Kliknij przycisk **wypróbuj** przycisk w skryptach poniżej, co spowoduje otwarcie Cloud Shell, która rejestruje Cię do konta platformy Azure. Dla opcji na uruchamianie programu bash skryptami interfejsu wiersza polecenia na komputerze klienckim Windows, zobacz [zainstalować interfejs wiersza polecenia platformy Azure na Windows](/cli/azure/install-azure-cli-windows). 
6. Skopiuj poniższy skrypt do edytora tekstów na komputerze. Zastąp `<SubscriptionB-Id>` przy użyciu identyfikatora subskrypcji. Jeśli nie znasz Twojego identyfikatora subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji Skopiuj skrypt zmodyfikowane, wklej ją w sesji środowiska interfejsu wiersza polecenia 2.0, a następnie naciśnij `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Podczas tworzenia sieci wirtualnej (model klasyczny) w kroku 4, Azure utworzyć sieć wirtualną w *domyślnej sieci* grupy zasobów.
7. Zaloguj się Użytkownik_b spoza platformy Azure i zaloguj się jako użytkownik a interfejsu wiersza polecenia w wersji 2.0.
8. Utwórz grupę zasobów i sieci wirtualnej (Resource Manager). Skopiuj poniższy skrypt, wklej ją w sesji środowiska interfejsu wiersza polecenia i naciśnij klawisz `Enter`. 

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

9. Tworzenie sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Skopiuj poniższy skrypt do edytora tekstów na komputerze. Zastąp `<SubscriptionB-id>` ze swoją subskrypcją identyfikatora. Jeśli nie znasz Twojego identyfikatora subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji Azure utworzona sieć wirtualna (klasyczna) został utworzony w kroku 4 w grupie zasobów o nazwie *domyślnej sieci*. Wklej zmodyfikowany skrypt sesję interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Po wykonaniu skryptu, przejrzyj komunikacji równorzędnej sieci wirtualnej (Resource Manager). Skopiuj poniższy skrypt, a następnie wklej go w sesji programu interfejsu wiersza polecenia:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Pokazano w danych wyjściowych **połączono** w **PeeringState** kolumny.

    Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

11. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
12. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-cli) w tym artykule.

## <a name="powershell"></a>Utworzyć komunikację równorzędną — PowerShell

Ten samouczek używa różnych kont, dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, możesz używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania możliwości platformy Azure i Usuń wiersze skryptu, które tworzenie przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów za pomocą nazw użytkowników, używasz osoby Użytkownik_a i Użytkownik_b. 

1. Zainstaluj najnowszą wersję programu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) i [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modułów. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell Zaloguj się do subskrypcji firmy Użytkownik_b co UżytkownikB, wprowadzając `Add-AzureAccount` polecenia. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
4. Do utworzenia sieci wirtualnej (model klasyczny) przy użyciu programu PowerShell, możesz utworzyć nową lub zmodyfikować istniejący, plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizowanie i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Plik powinien zawierać następujące **VirtualNetworkSite** elementu dla sieci wirtualnej używane w tym samouczku:

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
    > Importowanie pliku konfiguracyjnego sieci zmienione mogą powodować zmiany w istniejących sieciach wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, należy dodawać tylko wtedy poprzedniego sieci wirtualnej i nie zmienić lub usunąć wszystkie istniejące sieci wirtualne z subskrypcji. 

5. Zaloguj się do subskrypcji firmy Użytkownik_b co Użytkownik_b używania poleceń usługi Resource Manager, wprowadzając `Connect-AzureRmAccount` polecenia.
6. Przypisz uprawnienia Użytkownik_a B. kopii w sieci wirtualnej poniższy skrypt do edytora tekstów na komputerze i Zastąp `<SubscriptionB-id>` z Identyfikatorem subskrypcji B. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzureRmSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** w dane wyjściowe to identyfikator subskrypcji. Azure utworzona sieć wirtualna (klasyczna) został utworzony w kroku 4 w grupie zasobów o nazwie *domyślnej sieci*. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej je do programu PowerShell i naciśnij klawisz `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Wyloguj się z platformy Azure jako Użytkownik_b i zaloguj się do subskrypcji przez użytkownika jako użytkownik a, wprowadzając `Connect-AzureRmAccount` polecenia. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
8. Tworzenie sieci wirtualnej (Resource Manager) poniższy skrypt kopiowanie, wklejanie go do programu PowerShell i naciskając `Enter`:

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

9. Przypisz uprawnienia Użytkownik_b myVnetA. Skopiuj poniższy skrypt do edytora tekstów na komputerze i Zastąp `<SubscriptionA-Id>` z Identyfikatorem subskrypcji A. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzureRmSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** w dane wyjściowe to identyfikator subskrypcji. Wklej zmodyfikowanej wersji skryptu w programie PowerShell, a następnie naciśnij klawisz `Enter` do jego wykonania.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Skopiuj poniższy skrypt do edytora tekstów na komputerze PC i Zastąp `<SubscriptionB-id>` z Identyfikatorem subskrypcji B. Do komunikacji równorzędnej myVnetA do myVNetB, skopiuj zmodyfikowany skrypt, wklej je do programu PowerShell i naciśnij klawisz `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Wyświetl stan komunikacji równorzędnej myVnetA kopiując następujący skrypt, wklejając je w programie PowerShell i naciskając klawisz `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan jest **połączono**. Zmienia się na **połączono** po skonfigurowaniu komunikacji równorzędnej, aby myVnetA myVnetB.

    Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
13. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-powershell) w tym artykule.

## <a name="delete"></a>Usuń zasoby
Po ukończeniu tego samouczka, możesz chcieć usunąć zasoby utworzone w tym samouczku, więc nie powodują naliczania opłat za użycie. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Witryna Azure portal

1. W polu wyszukiwania portalu wprowadź **myResourceGroupA**. W wynikach wyszukiwania kliknij **myResourceGroupA**.
2. Na **myResourceGroupA** bloku kliknij **Usuń** ikony.
3. Aby potwierdzić usunięcie, w **wpisz nazwę grupy zasobów** wprowadź **myResourceGroupA**, a następnie kliknij przycisk **Usuń**.
4. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetB*. Kliknij przycisk **myVnetB** , gdy pojawia się w wynikach wyszukiwania. Zostanie wyświetlony blok **myVnetB** sieci wirtualnej.
5. W **myVnetB** bloku kliknij **Usuń**.
6. Aby potwierdzić usunięcie, kliknij przycisk **tak** w **usuwania sieci wirtualnej** pole.

### <a name="delete-cli"></a>Wiersza polecenia platformy Azure

1. Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia 2.0, można usunąć sieci wirtualnej (Resource Manager) za pomocą następującego polecenia:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Zaloguj się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0 można usunąć sieci wirtualnej (klasycznej) za pomocą następujących poleceń:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. W wierszu polecenia programu PowerShell wprowadź następujące polecenie, aby usunąć sieci wirtualnej (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Można usunąć sieci wirtualnej (model klasyczny) przy użyciu programu PowerShell, należy zmodyfikować istniejący plik konfiguracji sieci. Dowiedz się, jak [eksportu, aktualizowanie i importowanie plików konfiguracyjnych sieci](virtual-networks-using-network-configuration-file.md). Usuń następujący element VirtualNetworkSite dla sieci wirtualnej używane w tym samouczku:

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
    > Importowanie pliku konfiguracyjnego sieci zmienione mogą powodować zmiany w istniejących sieciach wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, tylko Usuń poprzednie sieci wirtualnej, a nie zmienić lub usunąć innych istniejących sieci wirtualnych z subskrypcji. 

## <a name="next-steps"></a>Kolejne kroki

- Należy dokładnie zapoznać się z ważne [ograniczenia komunikacji równorzędnej sieci wirtualnej i zachowania](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem sieci wirtualnej komunikacji równorzędnej w środowisku produkcyjnym należy używać.
- Dowiedz się więcej o wszystkich [ustawieniami wirtualnych sieci równorzędnych](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [Utwórz koncentrator i topologię sieci typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) za pomocą komunikacji równorzędnej sieci wirtualnej.
