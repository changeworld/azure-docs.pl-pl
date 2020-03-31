---
title: Tworzenie komunikacji równorzędnej sieci wirtualnej — różne subskrypcje
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzonymi za pośrednictwem Menedżera zasobów, które istnieją w różnych subskrypcjach platformy Azure.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245124"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Tworzenie komunikacji równorzędnej w sieci wirtualnej — Menedżer zasobów, różne subskrypcje

W tym samouczku nauczysz się tworzyć komunikację równorzędną sieci wirtualnej między sieciami wirtualnymi utworzoną za pośrednictwem Menedżera zasobów. Sieci wirtualne istnieją w różnych subskrypcjach. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasoby w różnych sieciach wirtualnych do komunikowania się ze sobą z taką samą przepustowość i opóźnienia, jak gdyby zasoby były w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacji równorzędnej w sieci wirtualnej](virtual-network-peering-overview.md).

Kroki tworzenia komunikacji równorzędnej sieci wirtualnej są różne, w zależności od tego, czy sieci wirtualne są w tej samej lub innej subskrypcji i który [model wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pośrednictwem. Dowiedz się, jak utworzyć komunikację równorzędną sieci wirtualnej w innych scenariuszach, wybierając scenariusz z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Resource Manager — w obu przypadkach](tutorial-connect-virtual-networks-portal.md) |Ta sama|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models.md) |Ta sama|
|[Jedna sieć — Resource Manager, druga — model klasyczny](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Jeśli musisz połączyć sieci wirtualne, które zostały utworzone za pośrednictwem klasycznego modelu wdrażania, możesz użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure do połączenia sieci wirtualnych.

W tym samouczku są wiązki sieci wirtualnych w tym samym regionie. Można również równorzędne sieci wirtualne w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region). Zaleca się zapoznanie się z [wymaganiami i ograniczeniami komunikacji równorzędnej](virtual-network-manage-peering.md#requirements-and-constraints) przed komunikacją równorzędną sieci wirtualnych.

Za pomocą [witryny Azure Portal](#portal), [interfejsu wiersza polecenia](#cli) platformy Azure (CLI), usługi Azure [PowerShell](#powershell)lub [szablonu usługi Azure Resource Manager](#template) można utworzyć komunikację równorzędną sieci wirtualnej. Wybierz dowolne z poprzednich łączy narzędzi, aby przejść bezpośrednio do kroków tworzenia komunikacji równorzędnej sieci wirtualnej przy użyciu wybranego narzędzia.

Jeśli sieci wirtualne znajdują się w różnych subskrypcjach, a subskrypcje są skojarzone z różnymi dzierżawami usługi Azure Active Directory, wykonaj następujące kroki przed kontynuowaniem:
1. Dodaj użytkownika z każdej dzierżawy usługi Active Directory jako [użytkownika-gościa](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przeciwnej dzierżawie usługi Azure Active Directory.
1. Każdy użytkownik musi zaakceptować zaproszenie użytkownika gościa z przeciwnej dzierżawy usługi Azure Active Directory.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Tworzenie komunikacji równorzędnej — witryna Azure portal

W poniższych krokach użyto różnych kont dla każdej subskrypcji. Jeśli używasz konta, które ma uprawnienia do obu subskrypcji, możesz użyć tego samego konta dla wszystkich kroków, pominąć kroki wylogowania z portalu i pominąć kroki przypisywania innym użytkownikom uprawnień do sieci wirtualnych.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako *UserA*. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
2. Wybierz **+ Utwórz zasób**, wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wybierz lub wprowadź następujące przykładowe wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz:**
    - **Imię i nazwisko**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślna*
    - **Zakres adresów podsieci:** *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję A.
    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowe** i wprowadź *myResourceGroupA*
    - **Lokalizacja**: *Wschodnie stany USA*
4. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetA*. Wybierz **myVnetA,** gdy pojawi się w wynikach wyszukiwania. 
5. Wybierz **pozycję Kontrola dostępu (IAM)** z pionowej listy opcji po lewej stronie.
6. W obszarze **myVnetA — kontrola dostępu (IAM)** wybierz **pozycję + Dodaj przypisanie roli**.
7. Wybierz **pozycję Współautor sieci** w polu **Rola.**
8. W polu **Wybierz** wybierz *userB*lub wpisz adres e-mail UżytkownikaB, aby go wyszukać.
9. Wybierz **pozycję Zapisz**.
10. W **obszarze myVnetA - Kontrola dostępu (IAM)** wybierz **pozycję Właściwości** z pionowej listy opcji po lewej stronie. Skopiuj **identyfikator zasobu**, który jest używany w późniejszym kroku. Identyfikator zasobu jest podobny do `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`następującego przykładu: .
11. Wyloguj się z portalu jako UserA, a następnie zaloguj się jako UserB.
12. Wykonaj kroki 2-3, wprowadzając lub wybierając następujące wartości w kroku 3:

    - **Imię i nazwisko**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślna*
    - **Zakres adresów podsieci:** *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję B.
    - **Grupa zasobów**: Wybierz **pozycję Utwórz nowe** i wprowadź *myResourceGroupB*
    - **Lokalizacja**: *Wschodnie stany USA*

13. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetB*. Wybierz **myVnetB,** gdy pojawi się w wynikach wyszukiwania.
14. W **obszarze myVnetB**wybierz **pozycję Właściwości** z pionowej listy opcji po lewej stronie. Skopiuj **identyfikator zasobu**, który jest używany w późniejszym kroku. Identyfikator zasobu jest podobny do `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`następującego przykładu: .
15. Wybierz **opcję Kontrola dostępu (IAM)** w obszarze **myVnetB**, a następnie wykonaj kroki 5-10 dla myVnetB, wprowadzając **UserA** w kroku 8.
16. Wyloguj się z portalu jako UserB i zaloguj się jako UserA.
17. W polu **Szukaj zasobów** u góry portalu wpisz *myVnetA*. Wybierz **myVnetA,** gdy pojawi się w wynikach wyszukiwania.
18. Wybierz **myVnetA**.
19. W obszarze **USTAWIENIA**wybierz **pozycję Peerings**.
20. W obszarze **myVnetA — komunikacja równorzędna**, wybierz **+ Dodaj**
21. W obszarze **Dodaj komunikację równorzędna**wprowadź lub wybierz następujące opcje, a następnie wybierz przycisk **OK:**
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrażania sieci wirtualnej:** Wybierz **Menedżera zasobów**.
     - **Znam identyfikator zasobu:** Zaznacz to pole.
     - **Identyfikator zasobu:** Wprowadź identyfikator zasobu z kroku 14.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że **wybrano** opcję Włączone.
    Żadne inne ustawienia nie są używane w tym samouczku. Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej, zobacz [Zarządzanie komunikacją równorzędową w sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
22. Utworzona komunikacja równorzędna pojawia się po krótkim oczekiwaniu po wybraniu **przycisku OK** w poprzednim kroku. **Zainicjowane** jest wymienione w kolumnie **STAN komunikacji równorzędnej** dla utworzonej komunikacji równorzędnej **myVnetAToMyVnetB.** Masz peered myVnetA do myVnetB, ale teraz musisz peer myVnetB do myVnetA. Komunikacja równorzędna musi być utworzona w obu kierunkach, aby umożliwić zasoby w sieciach wirtualnych do komunikowania się ze sobą.
23. Wyloguj się z portalu jako UserA i zaloguj się jako UserB.
24. Wykonaj kroki 17-21 ponownie dla myVnetB. W kroku 21 nazwij *myVnetBToMyVnetA*, wybierz *myVnetA* dla **sieci wirtualnej**i wprowadź identyfikator z kroku 10 w polu **Identyfikator zasobu.**
25. Kilka sekund po wybraniu **przycisku OK,** aby utworzyć komunikację równorzędną dla myVnetB, **myVnetBToMyVnetA** komunikacji równorzędnej, który właśnie utworzono, jest wyświetlany z **połączoną** w kolumnie **STAN komunikacji równorzędnej.**
26. Wyloguj się z portalu jako UserB i zaloguj się jako UserA.
27. Wykonaj kroki 17-19 ponownie. **Stan komunikacji równorzędnej** dla komunikacji równorzędnej **myVnetAToVNetB** jest teraz również **połączony**. Komunikacja równorzędna jest pomyślnie ustanawiana po wyświetleniu **pozycji Połączone** w kolumnie STAN **komunikacji równorzędnej** dla obu sieci wirtualnych w komunikacji równorzędnej. Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
29. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w sekcji [Usuwanie zasobów](#delete-portal) w tym artykule.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Tworzenie komunikacji równorzędnej — interfejsu wiersza polecenia platformy Azure

W tym samouczku użyto różnych kont dla każdej subskrypcji. Jeśli używasz konta, które ma uprawnienia do obu subskrypcji, można użyć tego samego konta dla wszystkich kroków, pomiń kroki wylogowywania się z platformy Azure i usuń wiersze skryptu, które tworzą przypisania ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich poniższych skryptach nazwy użytkowników, których używasz dla UserA i UserB. 

Następujące skrypty:

- Wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli chcesz przeprowadzić uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Działa w powłoce Basha. Aby uzyskać informacje na temat opcji uruchamiania skryptów interfejsu wiersza polecenia platformy Azure w ramach klienta systemu Windows, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

Zamiast instalowania interfejsu wiersza polecenia i jego zależności, można użyć usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w następującym skrypcie, który wywołuje powłokę w chmurze, za pomocą której można zalogować się do konta platformy Azure.

1. Otwórz sesję interfejsu wiersza polecenia i zaloguj `azure login` się na platformie Azure jako UserA za pomocą polecenia. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
2. Skopiuj następujący skrypt do edytora tekstu na komputerze, zastąp `<SubscriptionA-Id>` identyfikator subscriptiona, a następnie skopiuj zmodyfikowany skrypt, wklej go w sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `az account show` to polecenie. Wartość **identyfikatora** w danych wyjściowych jest identyfikator subskrypcji.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Wyloguj się z `az logout` platformy Azure jako UserA za pomocą polecenia, a następnie zaloguj się na platformie Azure jako UserB. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
4. Utwórz myVnetB. Skopiuj zawartość skryptu w kroku 2 do edytora tekstu na komputerze. Zamień `<SubscriptionA-Id>` na identyfikator SubscriptionB. Zmień 10.0.0.0/16 na 10.1.0.0/16, zmień wszystkie Co na B, a wszystkie Bs na A. Skopiuj zmodyfikowany skrypt, wklej go do sesji CLI i naciśnij `Enter`.
5. Wyloguj się z platformy Azure jako UserB i zaloguj się na platformie Azure jako UserA.
6. Utwórz komunikację równorzędną sieci wirtualnej z myVnetA do myVnetB. Skopiuj następującą zawartość skryptu do edytora tekstu na komputerze. Zamień `<SubscriptionB-Id>` na identyfikator SubscriptionB. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go do sesji interfejsu wiersza polecenia i naciśnij klawisz Enter.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Wyświetl stan komunikacji równorzędnej myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Stan jest **inicjowany**. To zmienia się **na Połączony** po utworzeniu komunikacji równorzędnej do myVnetA z myVnetB.

8. Wyloguj usera z platformy Azure i zaloguj się na platformie Azure jako UserB.
9. Utwórz komunikację równorzędnych z myVnetB do myVnetA. Skopiuj zawartość skryptu w kroku 6 do edytora tekstu na komputerze. Zamień `<SubscriptionB-Id>` na identyfikator subscriptiona i zmień wszystkie As na B i wszystkie Bs na A. Po wprowadzeniu zmian skopiuj zmodyfikowany skrypt, wklej go do `Enter`sesji interfejsu wiersza polecenia i naciśnij klawisz .
10. Wyświetl stan komunikacji równorzędnej myVnetB. Skopiuj zawartość skryptu w kroku 7 do edytora tekstu na komputerze. Zmień A na B dla grupy zasobów i nazw sieci wirtualnych, skopiuj skrypt, `Enter`wklej zmodyfikowany skrypt do sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz . Stan komunikacji równorzędnej to **Połączone**. Stan komunikacji równorzędnej myVnetA zmienia się na **Połączony** po utworzeniu komunikacji równorzędnej z myVnetB do myVnetA. Możesz zalogować UserA z powrotem na platformie Azure i ponownie wykonać krok 7, aby zweryfikować stan komunikacji równorzędnej myVnetA. 

    > [!NOTE]
    > Komunikacja równorzędna nie jest ustanawiana, dopóki stan komunikacji równorzędnej jest **połączony** dla obu sieci wirtualnych.

11. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
12. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w [temacie Usuwanie zasobów](#delete-cli) w tym artykule.

Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Tworzenie komunikacji równorzędnej — program PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym samouczku użyto różnych kont dla każdej subskrypcji. Jeśli używasz konta, które ma uprawnienia do obu subskrypcji, można użyć tego samego konta dla wszystkich kroków, pomiń kroki wylogowywania się z platformy Azure i usuń wiersze skryptu, które tworzą przypisania ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich poniższych skryptach nazwy użytkowników, których używasz dla UserA i UserB.

1. Upewnij się, że masz usługę Azure PowerShell w wersji 1.0.0 lub nowszej. Można to zrobić, `Get-Module -Name Az` uruchamiając zalecamy zainstalowanie najnowszej wersji [modułu PowerShell Az](/powershell/azure/install-az-ps). Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell zaloguj się do platformy `Connect-AzAccount` Azure jako UserA, wprowadzając polecenie. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
4. Utwórz grupę zasobów i sieć wirtualną A. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<SubscriptionA-Id>` na identyfikator SubscriptionA. Jeśli nie znasz identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby go wyświetlić. Wartość **identyfikatora** w zwróconym wyjściu jest identyfikator subskrypcji. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go `Enter`do programu PowerShell, a następnie naciśnij klawisz .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Wyloguj usera z platformy Azure i zaloguj się userB. Konto, na które się logujesz, musi mieć uprawnienia niezbędne do utworzenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać listę uprawnień, zobacz [Uprawnienia komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#permissions).
6. Skopiuj zawartość skryptu w kroku 4 do edytora tekstu na komputerze. Zamień `<SubscriptionA-Id>` na identyfikator subskrypcji B. Zmień 10.0.0.0/16 na 10.1.0.0/16. Zmień wszystkie As na B i wszystkie Bs na A. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej `Enter`do programu PowerShell, a następnie naciśnij klawisz .
7. Wyloguj userB z platformy Azure i zaloguj się usera.
8. Utwórz komunikację równorzędnej z myVnetA do myVnetB. Skopiuj następujący skrypt do edytora tekstu na komputerze. Zamień `<SubscriptionB-Id>` na identyfikator subskrypcji B. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej `Enter`do programu PowerShell, a następnie naciśnij klawisz .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Wyświetl stan komunikacji równorzędnej myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan jest **inicjowany**. To zmienia się **na Połączony** po skonfigurowaniu komunikacji równorzędnej do myVnetA z myVnetB.

10. Wyloguj usera z platformy Azure i zaloguj się userB.
11. Utwórz komunikację równorzędnych z myVnetB do myVnetA. Skopiuj zawartość skryptu w kroku 8 do edytora tekstu na komputerze. Zamień `<SubscriptionB-Id>` identyfikator subskrypcji A i zmień wszystkie As na B i wszystkie Bs na A. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej go `Enter`do programu PowerShell, a następnie naciśnij klawisz .
12. Wyświetl stan komunikacji równorzędnej myVnetB. Skopiuj zawartość skryptu w kroku 9 do edytora tekstu na komputerze. Zmień A na B dla grupy zasobów i nazw sieci wirtualnych. Aby wykonać skrypt, wklej zmodyfikowany skrypt do programu `Enter`PowerShell, a następnie naciśnij klawisz . Stan jest **połączony**. Stan komunikacji równorzędnej **myVnetA** zmienia się na **Połączony** po utworzeniu komunikacji równorzędnej z **myVnetB** do **myVnetA**. Możesz zalogować UserA z powrotem na platformie Azure i ponownie wykonać krok 9, aby zweryfikować stan komunikacji równorzędnej myVnetA.

    > [!NOTE]
    > Komunikacja równorzędna nie jest ustanawiana, dopóki stan komunikacji równorzędnej jest **połączony** dla obu sieci wirtualnych.

    Wszystkie zasoby platformy Azure utworzone w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem swoich adresów IP. Jeśli używasz domyślnego rozpoznawania nazw platformy Azure dla sieci wirtualnych, zasoby w sieciach wirtualnych nie są w stanie rozpoznać nazw w sieciach wirtualnych. Jeśli chcesz rozpoznać nazwy w sieciach wirtualnych w komunikacji równorzędnej, musisz utworzyć własny serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
14. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w [temacie Usuwanie zasobów](#delete-powershell) w tym artykule.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Tworzenie komunikacji równorzędnej — szablon Menedżera zasobów

1. Aby utworzyć sieć wirtualną i przypisać odpowiednie [uprawnienia,](virtual-network-manage-peering.md#permissions)wykonaj kroki w sekcji [Portal](#portal), [Azure CLI](#cli)lub [PowerShell](#powershell) w tym artykule.
2. Zapisz tekst, który następuje po pliku na komputerze lokalnym. Zamień `<subscription ID>` na identyfikator subskrypcji UserA. Można zapisać plik jako vnetpeeringA.json, na przykład.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Zaloguj się do platformy Azure jako UserA i wdrożyć szablon przy użyciu [portalu](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [Programu PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)lub [interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Określ nazwę pliku, do której został zapisany przykładowy tekst json w kroku 2.
4. Skopiuj przykładowy json z kroku 2 do pliku na komputerze i wprowadzać zmiany w wierszach, które zaczynają się od:
   - **nazwa**: Zmień *myVnetA/myVnetAToMyVnetB* na *myVnetB/myVnetBToMyVnetA*.
   - **id**: `<subscription ID>` Zamień identyfikator subskrypcji UserB i zmień *myVnetB* na *myVnetA*.
5. Wykonaj krok 3 ponownie, zalogowany na platformie Azure jako UserB.
6. **Opcjonalnie:** Chociaż tworzenie maszyn wirtualnych nie jest omówione w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej, aby sprawdzić poprawność łączności.
7. **Opcjonalnie:** Aby usunąć zasoby utworzone w tym samouczku, wykonaj kroki opisane w sekcji [Usuń zasoby](#delete) tego artykułu, korzystając z witryny Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="delete-resources"></a><a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka można usunąć zasoby utworzone w samouczku, aby nie ponosić opłat za użycie. Usunięcie grupy zasobów powoduje również usunięcie wszystkich zasobów w grupie zasobów.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portal Azure

1. Zaloguj się do portalu Azure jako UserA.
2. W polu wyszukiwania portalu wpisz **myResourceGroupA**. W wynikach wyszukiwania wybierz **myResourceGroupA**.
3. Wybierz pozycję **Usuń**.
4. Aby potwierdzić usunięcie, w polu **TYP NAZWA GRUPY ZASOBÓW** wprowadź **myResourceGroupA**, a następnie wybierz pozycję **Usuń**.
5. Wyloguj się z portalu jako UserA i zaloguj się jako UserB.
6. Wykonaj kroki 2-4 dla myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zaloguj się na platformie Azure jako UserA i wykonaj następujące polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Wyloguj się z platformy Azure jako UserA i zaloguj się jako UserB.
3. Wykonaj następujące polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Zaloguj się na platformie Azure jako UserA i wykonaj następujące polecenie:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Wyloguj się z platformy Azure jako UserA i zaloguj się jako UserB.
3. Wykonaj następujące polecenie:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Następne kroki

- Dokładnie zapoznaj się z [ważnymi ograniczeniami i zachowaniami komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem komunikacji równorzędnej sieci wirtualnej do użytku produkcyjnego.
- Dowiedz się więcej o wszystkich [ustawieniach komunikacji równorzędnej w sieci wirtualnej](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [utworzyć topologię sieci i koncentratorów](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) sieci z komunikacją równorzędną sieci wirtualnej.
