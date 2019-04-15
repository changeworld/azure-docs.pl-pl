---
title: Tworzenie sieci wirtualnej platformy Azure komunikację równorzędną — Menedżer zasobów — różne subskrypcje
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć sieć wirtualną, komunikacja równorzędna między sieciami wirtualnymi utworzonymi za pomocą usługi Resource Manager, które istnieją w różnych subskrypcjach platformy Azure.
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
ms.openlocfilehash: 3294eda4d9330332bf23c3a8f1804f067373bf7a
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528265"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Tworzenie, wirtualne sieci równorzędne — różne subskrypcje usługa Resource Manager

W tym samouczku dowiesz się, jak utworzyć wirtualną sieć równorzędną między sieciami wirtualnymi utworzonymi za pomocą usługi Resource Manager. Sieci wirtualne istnieją w różnych subskrypcjach. Komunikacja równorzędna dwóch sieci wirtualnych umożliwia zasobów w różnych sieciach wirtualnych do komunikowania się ze sobą przy użyciu tego samego przepustowości i opóźnienia, tak, jakby zasoby były w tej samej sieci wirtualnej. Dowiedz się więcej o [komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

Kroki, aby utworzyć wirtualne sieci równorzędne są różne w zależności od tego, czy sieci wirtualne są w tej samej lub różnych subskrypcji i który [modelu wdrażania platformy](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sieci wirtualne są tworzone za pomocą. Dowiedz się, jak utworzyć sieć wirtualną, komunikacja równorzędna w innych scenariuszach, wybierając w scenariuszu z poniższej tabeli:

|Model wdrażania platformy Azure  | Subskrypcja platformy Azure  |
|--------- |---------|
|[Obie usługi Resource Manager](tutorial-connect-virtual-networks-portal.md) |Ten sam|
|[Jeden usługi Resource Manager, druga — Model Klasyczny](create-peering-different-deployment-models.md) |Ten sam|
|[Jeden usługi Resource Manager, druga — Model Klasyczny](create-peering-different-deployment-models-subscriptions.md) |Różne|

Nie można utworzyć wirtualnej sieci równorzędnej między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Jeśli zachodzi potrzeba łączenia sieci wirtualnych, zarówno utworzone za pomocą klasycznego modelu wdrażania, możesz użyć platformy Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do łączenia sieci wirtualnych.

W tym samouczku równorzędnych sieci wirtualnych w tym samym regionie. Można także komunikacji równorzędnej sieci wirtualnych w różnych [obsługiwane regiony](virtual-network-manage-peering.md#cross-region). Zalecane jest, że należy zapoznać się z [komunikacji równorzędnej wymagania i ograniczenia](virtual-network-manage-peering.md#requirements-and-constraints) przed komunikacji równorzędnej sieci wirtualnych.

Możesz użyć [witryny Azure portal](#portal), Azure [interfejsu wiersza polecenia](#cli) (CLI) platformy Azure [PowerShell](#powershell), lub [szablonu usługi Azure Resource Manager](#template)do tworzenia wirtualnej sieci równorzędnej. Wybierz dowolny z poprzednich łącza narzędzia, aby przejść bezpośrednio do kroki tworzenia wirtualnej sieci równorzędnej przy użyciu narzędzia, wybranego.

## <a name="portal"></a>Utworzyć komunikację równorzędną — witryna Azure portal

W przypadku sieci wirtualnych, które chcesz nawiązać komunikację równorzędną w subskrypcji, które są skojarzone z różnych dzierżaw usługi Azure Active Directory, postępuj zgodnie z instrukcjami w sekcji programu PowerShell i interfejsu wiersza polecenia, w tym artykule. Portal nie ma obsługi nawiązać komunikację równorzędną sieci wirtualnych należących do subskrypcji z inną aktywnych dzierżaw katalogu. 

Należy pamiętać, że usługa Cloud Shell ma ograniczenia przełączania subskrypcji i dzierżaw z powodu których komunikacja równorzędna sieci wirtualnych lub globalnych wirtualnych sieci równorzędnych między sieciami wirtualnymi należącymi do subskrypcji w różnych dzierżawach usługi Azure Active Directory nie będzie działać. Przy użyciu programu PowerShell lub interfejsu wiersza polecenia.

Poniższe kroki używanie różnych kont, dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, można używać tego samego konta dla wszystkich kroków, pomiń kroki dla rejestracji z portalu i pominąć kroki do przypisywania innego uprawnienia użytkownika do sieci wirtualnych.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako *Użytkownik_a*. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
2. Wybierz **+ Utwórz zasób**, wybierz opcję **sieć**, a następnie wybierz pozycję **sieć wirtualna**.
3. Wybierz lub wprowadź następujące przykładowe wartości dla następujących ustawień, a następnie wybierz **Utwórz**:
    - **Nazwa**: *myVnetA*
    - **Przestrzeń adresowa**: *10.0.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.0.0.0/24*
    - **Subskrypcja**: Wybierz subskrypcję A.
    - **Grupa zasobów**: Wybierz **Utwórz nową** i wprowadź *myResourceGroupA*
    - **Lokalizacja**: *Wschodnie stany USA*
4. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetA*. Wybierz **myVnetA** , gdy pojawia się w wynikach wyszukiwania. 
5. Wybierz **kontrola dostępu (IAM)** z pionowy listy opcji po lewej stronie.
6. W obszarze **myVnetA — kontrola dostępu (IAM)**, wybierz opcję **+ Dodaj przypisanie roli**.
7. Wybierz **Współautor sieci** w **roli** pole.
8. W **wybierz** wybierz *Użytkownik_b*, lub wpisz adres e-mail firmy Użytkownik_b ją wyszukać.
9. Wybierz pozycję **Zapisz**.
10. W obszarze **myVnetA — kontrola dostępu (IAM)**, wybierz opcję **właściwości** z pionowy listy opcji po lewej stronie. Kopiuj **identyfikator ZASOBU**, która zostanie użyta w późniejszym kroku. Identyfikator zasobu jest podobny do poniższego przykładu: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`.
11. Wyloguj się z portalu jako użytkownik a, a następnie zaloguj się jako Użytkownik_b.
12. Wykonaj kroki 2 – 3, wprowadź lub wybierz następujące wartości w kroku 3:

    - **Nazwa**: *myVnetB*
    - **Przestrzeń adresowa**: *10.1.0.0/16*
    - **Nazwa podsieci**: *domyślne*
    - **Zakres adresów podsieci**: *10.1.0.0/24*
    - **Subskrypcja**: Wybieranie subskrypcji B.
    - **Grupa zasobów**: Wybierz **Utwórz nową** i wprowadź *myResourceGroupB*
    - **Lokalizacja**: *Wschodnie stany USA*

13. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetB*. Wybierz **myVnetB** , gdy pojawia się w wynikach wyszukiwania.
14. W obszarze **myVnetB**, wybierz opcję **właściwości** z pionowy listy opcji po lewej stronie. Kopiuj **identyfikator ZASOBU**, która zostanie użyta w późniejszym kroku. Identyfikator zasobu jest podobny do poniższego przykładu: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`.
15. Wybierz **kontrola dostępu (IAM)** w obszarze **myVnetB**, a następnie wykonaj kroki 5 – 10 myVnetB, wprowadzając **Użytkownik_a** w kroku 8.
16. Wyloguj się z portalu jako UżytkownikB, a następnie zaloguj się jako użytkownik a.
17. W **Wyszukaj zasoby** polu w górnej części portalu wpisz *myVnetA*. Wybierz **myVnetA** , gdy pojawia się w wynikach wyszukiwania.
18. Wybierz **myVnetA**.
19. W obszarze **ustawienia**, wybierz opcję **komunikacje równorzędne**.
20. W obszarze **myVnetA — komunikacja równorzędna**, wybierz opcję **+ Dodaj**
21. W obszarze **Dodaj komunikację równorzędną**, wprowadź lub wybierz poniższe opcje, a następnie wybierz **OK**:
     - **Nazwa**: *myVnetAToMyVnetB*
     - **Model wdrożenia sieci wirtualnej**:  Wybierz opcję **Menedżer zasobów**.
     - **Wiem, identyfikator zasobu**: zaznacz to pole wyboru.
     - **Identyfikator zasobu**: Podaj identyfikator zasobu z kroku 14.
     - **Zezwalaj na dostęp do sieci wirtualnej:** Upewnij się, że **włączone** jest zaznaczone.
    Żadne inne ustawienia są używane w ramach tego samouczka. Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej, przeczytaj [Zarządzanie komunikacja równorzędna sieci wirtualnych](virtual-network-manage-peering.md#create-a-peering).
22. Komunikacji równorzędnej, możesz utworzyć pojawia się z krótkim czasie oczekiwania, po wybraniu **OK** w poprzednim kroku. **Zainicjowano** znajduje się w **stan komunikacji RÓWNORZĘDNEJ** kolumny **myVnetAToMyVnetB** komunikacji równorzędnej zostanie utworzony. Została połączona myVnetA do myVnetB, ale teraz musi komunikacji równorzędnej myVnetB do myVnetA. Komunikacji równorzędnej muszą być tworzone w obu kierunkach, aby umożliwić zasobami w sieciach wirtualnych do komunikowania się ze sobą.
23. Wyloguj się z portalu jako użytkownik a, a następnie zaloguj się jako Użytkownik_b.
24. Wykonaj kroki 17-21 ponownie dla myVnetB. W kroku 21 nazwa komunikacji równorzędnej *myVnetBToMyVnetA*, wybierz opcję *myVnetA* dla **sieć wirtualna**, a następnie wprowadź identyfikator w kroku 10 w **identyfikator zasobu**pole.
25. Kilka sekund po wybraniu **OK** do utworzenia komunikacji równorzędnej dla myVnetB, **myVnetBToMyVnetA** komunikacji równorzędnej właśnie utworzony jest wyświetlany na liście **połączono** w **Stan komunikacji RÓWNORZĘDNEJ** kolumny.
26. Wyloguj się z portalu jako UżytkownikB, a następnie zaloguj się jako użytkownik a.
27. Ponownie wykonaj kroki 17-19. **Stan komunikacji RÓWNORZĘDNEJ** dla **myVnetAToVNetB** komunikacji równorzędnej jest teraz również **połączono**. Komunikacja równorzędna zostanie pomyślnie nawiązana, po wyświetleniu **połączono** w **stan komunikacji RÓWNORZĘDNEJ** kolumny dla obu sieci wirtualnych w komunikacji równorzędnej. Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
29. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-portal) dalszej części tego artykułu.

## <a name="cli"></a>Utworzyć komunikację równorzędną — interfejs wiersza polecenia platformy Azure

Ten samouczek używa różnych kont, dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, możesz używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania możliwości platformy Azure i Usuń wiersze skryptu, które tworzenie przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów za pomocą nazw użytkowników, używasz osoby Użytkownik_a i Użytkownik_b. Jeśli sieci wirtualne należą do różnych subskrypcji, a subskrypcje będą skojarzone z różnych dzierżaw usługi Azure Active Directory, wykonaj następujące kroki, aby kontynuować:
 - Dodaj użytkownika z każdej dzierżawy usługi Active Directory jako [użytkownik-Gość](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przeciwny dzierżawy usługi Azure Active Directory.
 - Każdy użytkownik musi zaakceptować zaproszenie użytkownika gościa z odwrotną dzierżawy usługi Azure Active Directory.

Następujące skrypty:

- Wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli musisz uaktualnić, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Działa w powłoce Bash. Aby uzyskać informacje na temat opcji uruchamiania skryptów interfejsu wiersza polecenia platformy Azure w ramach klienta systemu Windows, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

Zamiast instalowania interfejsu wiersza polecenia i jego zależności, można użyć usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz **wypróbuj** skrypt, który następuje, i wywołuje Cloud Shell, że możesz zalogować się do konta platformy Azure za pomocą przycisku.

1. Otwórz sesję programu interfejsu wiersza polecenia, a następnie zaloguj się do platformy Azure jako Użytkownika_a przy użyciu `azure login` polecenia. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
2. Skopiuj poniższy skrypt do edytora tekstów na komputerze PC, Zastąp `<SubscriptionA-Id>` identyfikator SubscriptionA, a następnie skopiuj zmodyfikowany skrypt, wklej go w sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`. Jeśli nie znasz Twojego identyfikatora subskrypcji, wprowadź `az account show` polecenia. Wartość **identyfikator** w danych wyjściowych jest identyfikator subskrypcji

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

3. Wylogować się platformy Azure, używając Użytkownik_a `az logout` polecenia, a następnie zaloguj się do platformy Azure jako Użytkownik_b. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
4. Utwórz myVnetB. Skopiuj zawartość skryptu w kroku 2 do edytora tekstów na komputerze. Zastąp `<SubscriptionA-Id>` z Identyfikatorem subskrypcji b. Zmiana 10.0.0.0/16 10.1.0.0/16, Zmień wszystko co B i wszystkich Bs do skopiowania A. zmodyfikowany skrypt wklej ją w sesji interfejsu wiersza polecenia i naciśnij klawisz `Enter`.
5. Wyloguj się z platformy Azure jako Użytkownik_b i zaloguj się do platformy Azure jako użytkownik a.
6. Tworzenie sieci wirtualnej komunikacji równorzędnej myVnetA myVnetB. Skopiuj poniższą zawartość skryptu do edytora tekstów na komputerze. Zastąp `<SubscriptionB-Id>` z Identyfikatorem subskrypcji b. Można wykonać skryptu, skopiuj zmodyfikowany skrypt, wklej go do sesji środowiska interfejsu wiersza polecenia i naciśnij klawisz Enter.

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

    Stan jest **zainicjowano**. Zmienia się na **połączono** po utworzeniu komunikacji równorzędnej, aby myVnetA myVnetB.

8. Wylogować użytkownika z platformy Azure i zaloguj się do platformy Azure jako Użytkownik_b.
9. Tworzenie komunikacji równorzędnej, myVnetB myVnetA. Skopiuj zawartość skryptu w kroku 6 do edytora tekstów na komputerze. Zastąp `<SubscriptionB-Id>` o identyfikatorze SubscriptionA i Zmień wszystko co B i wszystkich Bs do serwera A. Po dokonaniu zmiany Skopiuj zmodyfikowany skrypt, wklej go do swojej sesji interfejsu wiersza polecenia, a następnie naciśnij klawisz `Enter`.
10. Wyświetl stan komunikacji równorzędnej myVnetB. Do edytora tekstów na komputerze, skopiuj zawartość skryptu w kroku 7. Zmiany od A do B dla grupy zasobów i nazwy sieci wirtualnej, skopiuj skrypt, Wklej zmodyfikowany skrypt w sesji środowiska interfejsu wiersza polecenia i naciśnij klawisz `Enter`. Stan komunikacji równorzędnej jest **połączono**. Stan komunikacji równorzędnej myVnetA zmiany **połączono** po utworzeniu komunikacji równorzędnej, myVnetB myVnetA. Zaloguj się Użytkownik_a ponownie na platformie Azure i kompletne kroku 7 ponownie, aby sprawdzić stan komunikacji równorzędnej myVnetA. 

    > [!NOTE]
    > Komunikacja równorzędna nie zostanie nawiązane, dopóki nie zostanie stan komunikacji równorzędnej **połączono** obie sieci wirtualne.

11. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
12. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-cli) w tym artykule.

Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="powershell"></a>Utworzyć komunikację równorzędną — PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ten samouczek używa różnych kont, dla każdej subskrypcji. Jeśli używasz konta mającego uprawnienia do obu subskrypcji, możesz używać tego samego konta dla wszystkich kroków, pomiń kroki rejestrowania możliwości platformy Azure i Usuń wiersze skryptu, które tworzenie przypisań ról użytkownika. Zastąp UserA@azure.com i UserB@azure.com we wszystkich następujących skryptów za pomocą nazw użytkowników, używasz osoby Użytkownik_a i Użytkownik_b.
Jeśli sieci wirtualne należą do różnych subskrypcji, a subskrypcje będą skojarzone z różnych dzierżaw usługi Azure Active Directory, wykonaj następujące kroki, aby kontynuować:
 - Dodaj użytkownika z każdej dzierżawy usługi Active Directory jako [użytkownik-Gość](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przeciwny dzierżawy usługi Azure Active Directory.
 - Każdy użytkownik musi zaakceptować zaproszenie użytkownika gościa z odwrotną dzierżawy usługi Active Directory.

1. Upewnij się, że masz Azure PowerShell w wersji 1.0.0 lub nowszej. Można to zrobić, uruchamiając `Get-Module -Name Az` zaleca się zainstalowanie najnowszej wersji programu PowerShell [modułu Az](/powershell/azure/install-az-ps). Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell Zaloguj się do platformy Azure jako użytkownik a, wprowadzając `Connect-AzAccount` polecenia. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
4. Utwórz grupę zasobów i sieć wirtualna kopiowania A. poniższy skrypt do edytora tekstów na komputerze z systemem. Zastąp `<SubscriptionA-Id>` o identyfikatorze SubscriptionA. Jeśli nie znasz Twojego identyfikatora subskrypcji, wprowadź `Get-AzSubscription` polecenie, aby go wyświetlić. Wartość **identyfikator** w dane wyjściowe to identyfikator subskrypcji. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej je do programu PowerShell i naciśnij klawisz `Enter`.

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

5. Wylogować użytkownika z platformy Azure i zaloguj się Użytkownik_b. Zaloguj się przy użyciu konta musi mieć uprawnienia niezbędne do tworzenia wirtualnej sieci równorzędnej. Aby uzyskać listę uprawnień, zobacz [wirtualnych sieci równorzędnych uprawnienia](virtual-network-manage-peering.md#permissions).
6. Skopiuj zawartość skryptu w kroku 4 do edytora tekstów na komputerze. Zastąp `<SubscriptionA-Id>` identyfikatorem subskrypcji B. zmiana 10.0.0.0/16 do 10.1.0.0/16. Zmień wszystko co B i wszystkich Bs A. Można wykonać skryptu, skopiuj zmodyfikowany skrypt, Wklej do programu PowerShell i naciśnij klawisz `Enter`.
7. Wyloguj się Użytkownikowi_b na platformie Azure i logowania użytkownika.
8. Tworzenie komunikacji równorzędnej, myVnetA myVnetB. Skopiuj poniższy skrypt do edytora tekstów na komputerze. Zastąp `<SubscriptionB-Id>` z Identyfikatorem subskrypcji B. Można wykonać skryptu, zmodyfikowany skrypt do kopiowania, wklejania do programu PowerShell i naciśnij klawisz `Enter`.

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

    Stan jest **zainicjowano**. Zmienia się na **połączono** po skonfigurowaniu komunikacji równorzędnej, aby myVnetA myVnetB.

10. Wylogować użytkownika z platformy Azure i zaloguj się Użytkownik_b.
11. Tworzenie komunikacji równorzędnej, myVnetB myVnetA. Skopiuj zawartość skryptu w kroku 8 do edytora tekstów na komputerze. Zastąp `<SubscriptionB-Id>` o identyfikatorze subskrypcji A i Zmień wszystko co B i wszystkich Bs do serwera A. Aby wykonać skrypt, skopiuj zmodyfikowany skrypt, wklej je do programu PowerShell i naciśnij klawisz `Enter`.
12. Wyświetl stan komunikacji równorzędnej myVnetB. Do edytora tekstów na komputerze, skopiuj zawartość skryptu w kroku 9. Zmiany od A do B dla grupy zasobów i nazwy sieci wirtualnej. Aby wykonać skrypt, Wklej zmodyfikowany skrypt programu PowerShell, a następnie naciśnij `Enter`. Stan jest **połączono**. Stan komunikacji równorzędnej z **myVnetA** zmieni się na **połączono** po utworzeniu komunikacji równorzędnej z **myVnetB** do **myVnetA**. Zaloguj się Użytkownik_a powrót do platformy Azure i kompletne krok 9 ponownie, aby sprawdzić stan komunikacji równorzędnej myVnetA.

    > [!NOTE]
    > Komunikacja równorzędna nie zostanie nawiązane, dopóki nie zostanie stan komunikacji równorzędnej **połączono** obie sieci wirtualne.

    Zasoby platformy Azure, który zostanie utworzony w dowolnej sieci wirtualnej mogą teraz komunikować się ze sobą za pomocą ich adresów IP. Jeśli używasz rozpoznawania nazw platformy Azure w domyślnej dla sieci wirtualnych, zasoby w sieciach wirtualnych nie będą mogli rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw między sieciami wirtualnymi w komunikacji równorzędnej, możesz utworzyć własnego serwera DNS. Dowiedz się, jak skonfigurować [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
14. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete-powershell) w tym artykule.

## <a name="template"></a>Utworzyć komunikację równorzędną — szablon usługi Resource Manager

Jeśli sieci wirtualne należą do różnych subskrypcji, a subskrypcje będą skojarzone z różnych dzierżaw usługi Azure Active Directory, wykonaj następujące kroki, aby kontynuować:
 - Dodaj użytkownika z każdej dzierżawy usługi Active Directory jako [użytkownik-Gość](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przeciwny dzierżawy usługi Azure Active Directory.
 - Każdy użytkownik musi zaakceptować zaproszenie użytkownika gościa z odwrotną dzierżawy usługi Active Directory.

1. Aby utworzyć sieć wirtualną i przypisz odpowiednie [uprawnienia](virtual-network-manage-peering.md#permissions), wykonaj kroki [Portal](#portal), [wiersza polecenia platformy Azure](#cli), lub [PowerShell](#powershell)sekcjach tego artykułu.
2. Zapisz tekst znajdujący się w pliku na komputerze lokalnym. Zastąp `<subscription ID>` identyfikatorem subskrypcji na użytkownika Może na przykład Zapisz plik jako vnetpeeringA.json.

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

3. Zaloguj się na platformie Azure jako Użytkownik_a i wdrażanie jej za pomocą szablonu [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template), lub [wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Określ nazwę pliku, który przykładowego tekstu json został zapisany w kroku 2, aby.
4. Skopiuj kod json przykładu w kroku 2 do pliku na komputerze i wprowadź zmiany do wierszy, które zaczynają się od:
   - **name**: Zmiana *myVnetA/myVnetAToMyVnetB* do *myVnetB/myVnetBToMyVnetA*.
   - **Identyfikator**: Zastąp `<subscription ID>` przy użyciu Identyfikatora subskrypcji i zmiany jego Użytkownik_b *myVnetB* do *myVnetA*.
5. Wykonaj krok 3 ponownie zalogować do platformy Azure jako Użytkownik_b.
6. **Opcjonalnie**: Chociaż nie jest objęty tworzenia maszyn wirtualnych w ramach tego samouczka, można utworzyć maszynę wirtualną w każdej sieci wirtualnej i nawiązać połączenie z jednej maszyny wirtualnej do drugiej strony, aby zweryfikować połączenie.
7. **Opcjonalnie**: Aby usunąć zasoby, które tworzysz w ramach tego samouczka, wykonaj kroki opisane w [usunąć zasoby](#delete) dalszej części tego artykułu, przy użyciu witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="delete"></a>Usuń zasoby
Po ukończeniu tego samouczka, możesz chcieć usunąć zasoby utworzone w tym samouczku, więc nie powodują naliczania opłat za użycie. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich zasobów, które znajdują się w grupie zasobów.

### <a name="delete-portal"></a>Azure Portal

1. Zaloguj się do witryny Azure portal jako użytkownika.
2. W polu wyszukiwania portalu wprowadź **myResourceGroupA**. W wynikach wyszukiwania wybierz **myResourceGroupA**.
3. Wybierz pozycję **Usuń**.
4. Aby potwierdzić usunięcie, w **wpisz nazwę grupy zasobów** wprowadź **myResourceGroupA**, a następnie wybierz pozycję **Usuń**.
5. Wyloguj się z portalu jako użytkownik a, a następnie zaloguj się jako Użytkownik_b.
6. Wykonaj kroki 2 – 4 dla myResourceGroupB.

### <a name="delete-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Logowanie do platformy Azure jako użytkownik a i uruchom następujące polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Wyloguj się z platformy Azure jako użytkownik a, a następnie zaloguj się jako Użytkownik_b.
3. Wykonaj następujące polecenie:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. Logowanie do platformy Azure jako użytkownik a i uruchom następujące polecenie:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Wyloguj się z platformy Azure jako użytkownik a, a następnie zaloguj się jako Użytkownik_b.
3. Wykonaj następujące polecenie:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Kolejne kroki

- Należy dokładnie zapoznać się z ważne [ograniczenia komunikacji równorzędnej sieci wirtualnej i zachowania](virtual-network-manage-peering.md#requirements-and-constraints) przed utworzeniem sieci wirtualnej komunikacji równorzędnej w środowisku produkcyjnym należy używać.
- Dowiedz się więcej o wszystkich [ustawieniami wirtualnych sieci równorzędnych](virtual-network-manage-peering.md#create-a-peering).
- Dowiedz się, jak [Utwórz koncentrator i topologię sieci typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) za pomocą komunikacji równorzędnej sieci wirtualnej.
