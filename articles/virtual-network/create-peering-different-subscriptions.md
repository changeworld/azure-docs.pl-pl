---
title: Tworzenie sieci wirtualnej platformy Azure komunikację równorzędną - Resource Manager - różnych subskrypcji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną komunikacji równorzędnej między sieciami wirtualnymi utworzone za pomocą Menedżera zasobów, które istnieją w ramach różnych subskrypcji Azure.
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
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: b67b13f30538d21f1a4db9675ee7c13d999f842a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726282"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Tworzenie sieci wirtualnej równorzędna - Resource Manager różnych subskrypcji 

Z tego samouczka dowiesz się, można utworzyć sieci wirtualnej komunikacji równorzędnej między sieciami wirtualnymi utworzone za pomocą Menedżera zasobów. Sieci wirtualne istnieją w ramach różnych subskrypcji. Komunikacja równorzędna dwa zasoby umożliwia sieci wirtualnych w różnych sieciach wirtualnych do komunikowania się ze sobą przy tym samym przepustowości i opóźnień, tak jakby były zasoby w tej samej sieci wirtualnej. Dowiedz się więcej o [równorzędna sieci wirtualnej](virtual-network-peering-overview.md). 

Kroki tworzenia sieci wirtualnej komunikacji równorzędnej są różne, w zależności od tego, czy sieci wirtualne są w tym samym lub różnych subskrypcji i które [modelu wdrożenia usługi Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną równorzędna w innych sytuacjach, wybierając w scenariuszu z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](tutorial-connect-virtual-networks-portal.md) |tym samym|
|[Jeden Resource Manager, co classic](create-peering-different-deployment-models.md) |tym samym|
|[Jeden Resource Manager, co classic](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć sieci wirtualnej komunikacji równorzędnej między dwiema sieciami wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania. Jeśli musisz połączyć sieci wirtualnych, które były tworzone przy użyciu klasycznego modelu wdrażania, można użyć Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do łączenia sieci wirtualnej.

W tym samouczku równorzędnymi użytkownikami sieci wirtualne w tym samym regionie. Można również elementów równorzędnych sieci wirtualnych w różnych [obsługiwane regiony](virtual-network-manage-peering.md#cross-region). Zaleca się, że możesz się zapoznać [komunikacji równorzędnej wymagań i ograniczeń](virtual-network-manage-peering.md#requirements-and-constraints) przed równorzędna sieci wirtualnych.

Można użyć [portalu Azure](#portal), Azure [interfejsu wiersza polecenia](#cli) (CLI) Azure [PowerShell](#powershell), lub [szablonu usługi Azure Resource Manager](#template)można utworzyć sieci wirtualnej komunikacji równorzędnej. Wybierz dowolne z poprzednich łączy narzędzia, aby przejść bezpośrednio do kroki tworzenia sieci wirtualnej komunikacji równorzędnej narzędzie wyboru.

## <a name="portal"></a>Utwórz równorzędna - portalu Azure

W tym samouczku korzysta z różnych kont dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania poza portalem i pomiń kroki przypisywania innym użytkownikom uprawnień do sieci wirtualnych.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) jako *Użytkownik_a*. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
2. Wybierz **+ Utwórz zasób**, wybierz pozycję **sieci**, a następnie wybierz **sieci wirtualnej**.
3. Wybierz lub wprowadź następujące przykładowe wartości dla następujących ustawień, a następnie wybierz **Utwórz**:
    - **Nazwa**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję A.
    - **Grupa zasobów**: Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroupA*
    - **Lokalizacja**: *wschodnie stany USA*
4. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetA*. Wybierz **myVnetA** po wyświetleniu w wynikach wyszukiwania. 
5. Wybierz **(IAM) kontroli dostępu** z pionowy listy opcji po lewej stronie.
6. W obszarze **myVnetA — kontrola dostępu (IAM)**, wybierz pozycję **+ Dodaj**.
7. Wybierz **współautora sieci** w **roli** pole.
8. W **wybierz** wybierz *Użytkownik_b*, lub wpisz adres e-mail firmy Użytkownik_b za pomocą funkcji wyszukiwania. Lista użytkowników wyświetlany jest dzierżawy usługi Azure Active Directory jako sieć wirtualną, której podczas konfigurowania komunikacji równorzędnej dla. Jeśli nie widzisz Użytkownik_b, prawdopodobnie ponieważ Użytkownik_b znajduje się w innej dzierżawy usługi Active Directory niż Użytkownik_a. Jeśli chcesz się połączyć sieci wirtualnych w różnych dzierżawców usługi Active Directory, możesz połączyć je z [bramy sieci VPN Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), zamiast sieci wirtualnej komunikacji równorzędnej.
9. Wybierz pozycję **Zapisz**.
10. W obszarze **myVnetA — kontrola dostępu (IAM)**, wybierz pozycję **właściwości** z pionowy listy opcji po lewej stronie. Kopiuj **identyfikator ZASOBU**, która jest używana w kolejnym kroku. Identyfikator zasobu jest podobny do poniższego przykładu: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Wyloguj się z portalem jako Użytkownik_a, a następnie zaloguj się jako Użytkownik_b.
12. Wykonaj kroki od 2 do 3, wprowadzania lub wybierz następujące wartości w kroku 3:

    - **Nazwa**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję B.
    - **Grupa zasobów**: Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroupB*
    - **Lokalizacja**: *wschodnie stany USA*

13. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetB*. Wybierz **myVnetB** po wyświetleniu w wynikach wyszukiwania.
14. W obszarze **myVnetB**, wybierz pozycję **właściwości** z pionowy listy opcji po lewej stronie. Kopiuj **identyfikator ZASOBU**, która jest używana w kolejnym kroku. Identyfikator zasobu jest podobny do poniższego przykładu: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Wybierz **(IAM) kontroli dostępu** w obszarze **myVnetB**, a następnie wykonaj kroki 5 – 10 dla myVnetB wprowadzania **Użytkownik_a** w kroku 8.
16. Wyloguj się z portalem jako Użytkownik_b i zaloguj się jako Użytkownik_a.
17. W **wyszukiwania zasobów** pole w górnej części portalu typu *myVnetA*. Wybierz **myVnetA** po wyświetleniu w wynikach wyszukiwania.
18. Wybierz **myVnetA**.
19. W obszarze **ustawienia**, wybierz pozycję **komunikacji równorzędnych**.
20. W obszarze **myVnetA - komunikacji równorzędnych**, wybierz pozycję **+ Dodaj**
21. W obszarze **równorzędna Dodaj**, wprowadź, lub wybierz poniższe opcje, a następnie wybierz **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrażania sieci wirtualnej**: Wybierz **Resource Manager**.
     - **Sprawdzić mój identyfikator zasobu**: Zaznacz to pole wyboru.
     - **Identyfikator zasobu**: Wprowadź identyfikator zasobu z kroku 14.
     - **Zezwalaj na dostęp do sieci wirtualnej:** upewnij się, że **włączone** jest zaznaczone.
    W tym samouczku są używane żadne inne ustawienia. Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacji równorzędnych sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
22. Równorzędna utworzonego pojawi się krótki oczekiwania po wybraniu **OK** w poprzednim kroku. **Zainicjowano** znajduje się w **RÓWNORZĘDNA stan** kolumny dla **myVnetAToMyVnetB** równorzędna zostanie utworzony. Już połączyć za pomocą myVnetA do myVnetB, ale teraz musi równorzędnego myVnetB do myVnetA. Komunikację równorzędną muszą być tworzone w obu kierunkach, aby włączyć zasobów w sieci wirtualne do komunikowania się ze sobą.
23. Wyloguj się z portalem jako Użytkownik_a i zaloguj się jako Użytkownik_b.
24. Wykonaj kroki 17 21 ponownie dla myVnetB. W kroku 21 nazwa komunikację równorzędną *myVnetBToMyVnetA*, wybierz pozycję *myVnetA* dla **sieci wirtualnej**i podaj identyfikator z kroku 10 **identyfikator zasobu**pole.
25. Kilka sekund po wybraniu **OK** utworzyć komunikacji równorzędnej dla myVnetB, **myVnetBToMyVnetA** równorzędna właśnie utworzony znajduje się **połączony** w **RÓWNORZĘDNA stan** kolumny.
26. Wyloguj się z portalem jako Użytkownik_b i zaloguj się jako Użytkownik_a.
27. Wykonaj ponownie czynności 17-19. **RÓWNORZĘDNA stan** dla **myVnetAToVNetB** komunikacji równorzędnej jest teraz również **połączony**. Komunikację równorzędną zostanie pomyślnie nawiązane po **połączony** w **RÓWNORZĘDNA stan** kolumny dla obu sieci wirtualnych w komunikacji równorzędnej. Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
29. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-portal) sekcji tego artykułu.

## <a name="cli"></a>Utwórz równorzędna - wiersza polecenia platformy Azure

W tym samouczku korzysta z różnych kont dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania z platformy Azure i usuwanie wierszy skryptu utworzonych przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów z nazw użytkowników, używasz Użytkownik_a i Użytkownik_b. Obie sieci wirtualne, które mają być elementu równorzędnego musi być w ramach subskrypcji skojarzonych z tej samej dzierżawy usługi Azure Active Directory.  Jeśli chcesz się połączyć sieci wirtualnych w różnych dzierżawców usługi Active Directory, możesz połączyć je z [bramy sieci VPN Azure](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), zamiast sieci wirtualnej komunikacji równorzędnej.

Następujące skrypty:

- Wymaga wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Działanie powłoki Bash. Aby wyświetlić opcje uruchamiania skryptów wiersza polecenia platformy Azure na kliencie systemu Windows, zobacz [zainstalować w systemie Windows Azure CLI](/cli/azure/install-azure-cli-windows). 

Zamiast instalowania interfejsu wiersza polecenia i jego zależności, można użyć powłoki chmury Azure. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz **wypróbuj** przycisk skrypt, który jest zgodny, i wywołuje powłoki chmury, które możesz zalogować się do konta platformy Azure z. 

1. Otwórz sesję programu interfejsu wiersza polecenia, a następnie zaloguj się do platformy Azure jako przy użyciu Użytkownik_a `azure login` polecenia. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
2. Skopiuj poniższy skrypt do edytora tekstu, na komputerze, zastąpić `<SubscriptionA-Id>` o identyfikatorze SubscriptionA, a następnie skopiuj zmodyfikowanego skryptu, wklej go w sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`. Jeśli nie znasz identyfikator subskrypcji, wprowadź polecenie "Pokaż konto az". Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji

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
    
3. Wyloguj się z platformy Azure jako przy użyciu Użytkownik_a `az logout` polecenia, a następnie zaloguj się na platformie Azure jako Użytkownik_b. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
4. Utwórz myVnetB. Skopiuj zawartość skryptu w kroku 2 do edytora tekstu, na komputerze. Zastąp `<SubscriptionA-Id>` o identyfikatorze SubscriptionB. Zmień 10.0.0.0/16 10.1.0.0/16, Zmień wszystkie co B i wszystkich Bs kopii A. zmodyfikowanego skryptu, wklej je do sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`. 
5. Wyloguj Azure jako Użytkownik_b i logowanie do platformy Azure jako Użytkownik_a.
6. Tworzenie sieci wirtualnej komunikacji równorzędnej z myVnetA do myVnetB. Skopiuj następujące zawartość skryptu do edytora tekstu, na komputerze. Zastąp `<SubscriptionB-Id>` o identyfikatorze SubscriptionB. Można wykonać skryptu, skopiuj skrypt zmodyfikowane, wklej go do sesji interfejsu wiersza polecenia i naciśnij klawisz Enter.
 
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

7. Wyświetl stan myVnetA komunikacji równorzędnej.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Stan jest **zainicjowano**. Zmienia się na **połączony** po utworzeniu komunikację równorzędną, aby myVnetA z myVnetB.

8. Wyloguj się Użytkownik_a z platformy Azure i zaloguj się na platformie Azure jako Użytkownik_b.
9. Utwórz komunikacji równorzędnej z myVnetB do myVnetA. Skopiuj zawartość skryptu w kroku 6 do edytora tekstu, na komputerze. Zastąp `<SubscriptionB-Id>` o identyfikatorze SubscriptionA i Zmień wszystkie co B i wszystkich Bs do serwera A. Po dokonaniu zmiany, skopiuj skrypt zmodyfikowane, wklej go do sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`.
10. Wyświetl stan myVnetB komunikacji równorzędnej. Skopiuj zawartość skryptu w kroku 7 do edytora tekstu, na komputerze. Zmiana A do B dla grupy zasobów i nazwy sieci wirtualnej, skopiuj skrypt, Wklej zmodyfikowane skrypt w sesji środowiska interfejsu wiersza polecenia i naciśnij klawisz `Enter`. Stan komunikacji równorzędnej jest **połączony**. Zmiany stanu komunikacji równorzędnej myVnetA **połączony** po utworzeniu komunikacji równorzędnej z myVnetB do myVnetA. Zaloguj się Użytkownik_a w krok 7. ponownie, aby sprawdzić stan komunikacji równorzędnej myVnetA na platformie Azure i kompletne. 

    > [!NOTE]
    > Komunikację równorzędną nie zostanie nawiązane, dopóki stan komunikacji równorzędnej jest **połączony** dla obu sieci wirtualnej.

11. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
12. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-cli) w tym artykule.

Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Utwórz równorzędna — PowerShell

W tym samouczku korzysta z różnych kont dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania z platformy Azure i usuwanie wierszy skryptu utworzonych przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów z nazw użytkowników, używasz Użytkownik_a i Użytkownik_b. Obie sieci wirtualne, które mają być elementu równorzędnego musi być w ramach subskrypcji skojarzonych z tej samej dzierżawy usługi Azure Active Directory.  Jeśli chcesz się połączyć sieci wirtualnych w różnych dzierżawców usługi Active Directory, możesz połączyć je z [bramy sieci VPN Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md), zamiast sieci wirtualnej komunikacji równorzędnej.

1. Zainstaluj najnowszą wersję modułu [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) programu PowerShell. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell, zaloguj się na platformie Azure jako Użytkownik_a wprowadzając `Connect-AzureRmAccount` polecenia. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
4. Tworzenie grupy zasobów i siecią wirtualną kopiowania A. poniższy skrypt do edytora tekstu na komputerze. Zastąp `<SubscriptionA-Id>` o identyfikatorze SubscriptionA. Jeśli nie znasz identyfikator subskrypcji, wprowadź `Get-AzureRmSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** zwrócony wynik jest Twojego identyfikatora subskrypcji. Można wykonać skryptu, skopiuj skrypt zmodyfikowane, wklej je do środowiska PowerShell i naciśnij klawisz `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Wyloguj się Użytkownik_a z platformy Azure i zaloguj się za Użytkownik_b. Konto logowania przy użyciu musi mieć uprawnienia do tworzenia sieci wirtualnej komunikacji równorzędnej. Aby uzyskać listę uprawnień, zobacz [komunikacji równorzędnej uprawnień sieci wirtualnej](virtual-network-manage-peering.md#permissions).
6. Skopiuj zawartość skryptu w kroku 4 do edytora tekstu, na komputerze. Zastąp `<SubscriptionA-Id>` o identyfikatorze subskrypcji 10.0.0.0/16 zmiany B. Aby 10.1.0.0/16. Zmień wszystkie co B i wszystkich Bs do serwera A. Można wykonać skryptu, skopiuj skrypt zmodyfikowane, Wklej do środowiska PowerShell i naciśnij klawisz `Enter`.
7. Wyloguj się Użytkownik_b z platformy Azure i zaloguj się za Użytkownik_a.
8. Utwórz komunikacji równorzędnej z myVnetA do myVnetB. Skopiuj poniższy skrypt do edytora tekstu, na komputerze. Zastąp `<SubscriptionB-Id>` z Identyfikatorem subskrypcji B. Można wykonać skryptu, skopiuj skrypt zmodyfikowane, Wklej do środowiska PowerShell i naciśnij klawisz `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Wyświetl stan myVnetA komunikacji równorzędnej.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stan jest **zainicjowano**. Zmienia się na **połączony** po skonfigurowaniu komunikację równorzędną, aby myVnetA z myVnetB.

10. Wyloguj się Użytkownik_a z platformy Azure i zaloguj się za Użytkownik_b.
11. Utwórz komunikacji równorzędnej z myVnetB do myVnetA. Skopiuj zawartość skryptu w kroku 8 do edytora tekstu, na komputerze. Zastąp `<SubscriptionB-Id>` o identyfikatorze subskrypcji A i Zmień wszystkie co B i wszystkich Bs do serwera A. Można wykonać skryptu, skopiuj skrypt zmodyfikowane, wklej je do środowiska PowerShell i naciśnij klawisz `Enter`.
12. Wyświetl stan myVnetB komunikacji równorzędnej. Skopiuj zawartość skryptu w kroku 9 do edytora tekstu, na komputerze. Zmiana A do B dla grupy zasobów i nazwy sieci wirtualnej. Można wykonać skryptu, wkleić zmodyfikowanego skryptu programu PowerShell, a następnie naciśnij klawisz `Enter`. Stan jest **połączony**. Stan komunikacji równorzędnej **myVnetA** zmienia się na **połączony** po utworzeniu komunikacji równorzędnej z **myVnetB** do **myVnetA**. Zaloguj się Użytkownik_a wstecz do platformy Azure i kompletne krok 9 ponownie, aby sprawdzić stan myVnetA komunikacji równorzędnej. 

    > [!NOTE]
    > Komunikację równorzędną nie zostanie nawiązane, dopóki stan komunikacji równorzędnej jest **połączony** dla obu sieci wirtualnej.

    Dowolnych zasobów platformy Azure, utworzone w każdej sieci wirtualnej mogą teraz komunikować się ze sobą za pośrednictwem ich adresy IP. Jeśli używasz domyślnego rozwiązania Azure nazwy sieci wirtualnych, zasobów w sieci wirtualne nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, należy utworzyć serwer DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
14. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete-powershell) w tym artykule.

## <a name="template"></a>Utwórz równorzędna - szablonu usługi Resource Manager

1. Aby utworzyć sieć wirtualną i przypisać odpowiednie [uprawnienia](virtual-network-manage-peering.md#permissions), wykonaj kroki [Portal](#portal), [interfejsu wiersza polecenia Azure](#cli), lub [PowerShell](#powershell)sekcje w tym artykule.
2. Zapisz tekst znajdujący się w pliku na komputerze lokalnym. Zastąp `<subscription ID>` z identyfikatorem Użytkownik_a w subskrypcji. Może na przykład Zapisz plik jako vnetpeeringA.json.

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

3. Zaloguj się na platformie Azure jako Użytkownik_a i wdrożyć przy użyciu szablonu [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), lub [interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Określ nazwę pliku, który przykładowy tekst json jest zapisany w kroku 2 do.
4. Skopiuj przykładowy kod json z kroku 2 do pliku na komputerze i wprowadzić zmiany w wierszach, które zaczynają się od:
    - **Nazwa**: zmiana *myVnetA/myVnetAToMyVnetB* do *myVnetB/myVnetBToMyVnetA*.
    - **Identyfikator**: Zastąp `<subscription ID>` o identyfikatorze subskrypcji i zmień jego Użytkownik_b *myVnetB* do *myVnetA*.
5. Wykonaj krok 3 ponownie zalogować na platformie Azure jako Użytkownik_b.
6. **Opcjonalne**: Chociaż tworzenia maszyn wirtualnych nie została uwzględniona w tym samouczku, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i połączyć się z jednej maszyny wirtualnej do drugiej strony, aby sprawdzić łączność.
7. **Opcjonalne**: Aby usunąć zasoby, które możesz utworzyć w tym samouczku, wykonaj kroki [zasoby zostaną usunięte](#delete) sekcji tego artykułu, przy użyciu portalu Azure, programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="delete"></a>Usuwanie zasobów
Po zakończeniu tego samouczka można usunąć utworzony w samouczka w celu uniknięcia opłat użycie zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure portal

1. Zaloguj się do portalu Azure jako Użytkownik_a.
2. W polu wyszukiwania portalu wprowadź **myResourceGroupA**. W wynikach wyszukiwania wybierz **myResourceGroupA**.
3. Wybierz pozycję **Usuń**.
4. Aby potwierdzić decyzję, w **typu nazwa grupy zasobów** wprowadź **myResourceGroupA**, a następnie wybierz **usunąć**.
5. Wyloguj się z portalem jako Użytkownik_a i zaloguj się jako Użytkownik_b.
6. Wykonaj kroki od 2 do 4 dla myResourceGroupB.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zaloguj się na platformie Azure jako Użytkownik_a i uruchom następujące polecenie:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Wyloguj Azure jako Użytkownik_a i zaloguj się jako Użytkownik_b.
3. Uruchom następujące polecenie:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Zaloguj się na platformie Azure jako Użytkownik_a i uruchom następujące polecenie:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Wyloguj Azure jako Użytkownik_a i zaloguj się jako Użytkownik_b.
3. Uruchom następujące polecenie:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Kolejne kroki

- Należy dokładnie zapoznać się z ważne [ograniczenia komunikacji równorzędnej sieci wirtualnej i zachowania](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem sieci wirtualnej komunikacji równorzędnej w środowisku produkcyjnym należy używać.
- Więcej informacji na temat wszystkich [sieci wirtualnej komunikacji równorzędnej ustawienia](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [tworzenie koncentratora i gwiazda topologii sieci](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z sieci wirtualnej komunikacji równorzędnej.
