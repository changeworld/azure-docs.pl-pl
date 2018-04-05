---
title: Uzyskuj dostęp do sieci wirtualnych sieci wirtualnej komunikacji równorzędnej - portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak połączyć sieci wirtualnych z sieci wirtualnej komunikacji równorzędnej, przy użyciu portalu Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b864c71a62289b3abef13a98b52683f7d928b8e1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Uzyskuj dostęp do sieci wirtualnych sieci wirtualnej komunikacji równorzędnej przy użyciu portalu Azure

Sieci wirtualne można połączyć ze sobą z sieci wirtualnej komunikacji równorzędnej. Po połączyć się za pomocą sieci wirtualnych, zasobów w obie sieci wirtualne są mogły komunikować się ze sobą, z tym samym opóźnienia i przepustowości tak, jakby był zasoby w tej samej sieci wirtualnej. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz dwie sieci wirtualne
> * Połącz dwie sieci wirtualnej z sieci wirtualnej komunikacji równorzędnej
> * Wdróż maszynę wirtualną (VM) do każdej sieci wirtualnej
> * Komunikację między maszynami wirtualnymi

Jeśli wolisz, możesz wykonać przy użyciu tego artykułu [interfejsu wiersza polecenia Azure](tutorial-connect-virtual-networks-cli.md) lub [programu Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

1. Wybierz **+ Utwórz zasób** w górnym lewym rogu portalu Azure.
2. Wybierz **sieci**, a następnie wybierz **sieci wirtualnej**.
3. Wprowadź, lub wybierz poniższe informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork1|
    |Przestrzeń adresowa|10.0.0.0/16|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz **Utwórz nowy** , a następnie wprowadź *myResourceGroup*.|
    |Lokalizacja| Wybierz **wschodnie stany USA**.|
    |Nazwa podsieci|Subnet1|
    |Zakres adresów podsieci|10.0.0.0/24|

      ![Tworzenie sieci wirtualnej](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Wykonaj kroki 1 – 3 ponownie, z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork2|
    |Przestrzeń adresowa|10.1.0.0/16|
    |Grupa zasobów| Wybierz **Użyj istniejącego** , a następnie wybierz **myResourceGroup**.|
    |Zakres adresów podsieci|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Sieci wirtualne elementów równorzędnych

1. W polu wyszukiwania w górnej części portalu Azure, rozpocznij wpisywanie *MyVirtualNetwork1*. Gdy **myVirtualNetwork1** pojawia się w wynikach wyszukiwania, wybierz go.
2. Wybierz **komunikacji równorzędnych**w obszarze **ustawienia**, a następnie wybierz **+ Dodaj**, jak pokazano na poniższej ilustracji:

    ![Utwórz komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Wprowadź, lub wybierz poniższe informacje, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **OK**.

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork1-myVirtualNetwork2|
    |Subskrypcja| Wybierz subskrypcję.|
    |Sieć wirtualna|myVirtualNetwork2 — wybierz *myVirtualNetwork2* sieci wirtualnej, wybierz opcję **sieci wirtualnej**, a następnie wybierz pozycję **myVirtualNetwork2**.|

    ![Ustawienia komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **RÓWNORZĘDNA stan** jest *zainicjowano*, jak pokazano na poniższej ilustracji:

    ![Stan komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Jeśli stan nie jest widoczny, Odśwież przeglądarkę.

4. W **wyszukiwania** polu w górnej części portalu Azure, rozpocznij wpisywanie *MyVirtualNetwork2*. Gdy **myVirtualNetwork2** pojawia się w wynikach wyszukiwania, wybierz go.
5. Wykonaj kroki 2 – 3 ponownie, z następującymi zmianami, a następnie wybierz **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork2-myVirtualNetwork1|
    |Sieć wirtualna|myVirtualNetwork1|

    **RÓWNORZĘDNA stan** jest *połączony*. Azure również zmienić stan komunikacji równorzędnej *myVirtualNetwork2 myVirtualNetwork1* komunikacji równorzędnej z *zainicjowano* do *połączony.* Sieci wirtualnej komunikacji równorzędnej nie pełni nawiązano momentu komunikacji równorzędnej stan obie sieci wirtualne *połączony.* 

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Tworzenie maszyny Wirtualnej w każdej sieci wirtualnej, dzięki czemu użytkownik może komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny Wirtualnej

1. Wybierz **+ Utwórz zasób** w górnym lewym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. Można wybrać innego systemu operacyjnego, ale pozostałe kroki zakłada się wybrano **systemu Windows Server Datacenter 2016**. 
3. Wprowadź lub wybierz następujące informacje dotyczące **podstawy**, zaakceptuj ustawienia domyślne dla pozostałych ustawień, a następnie wybierz **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVm1|
    |Nazwa użytkownika| Wprowadź nazwę użytkownika wybrane.|
    |Hasło| Wprowadź hasło wybrane. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz **Użyj istniejącego** , a następnie wybierz **myResourceGroup**.|
    |Lokalizacja| Wybierz **wschodnie stany USA**.|
4. Wybierz rozmiar maszyny Wirtualnej, w obszarze **wybierz rozmiar**.
5. Wybierz następujące wartości dla **ustawienia**, a następnie wybierz pozycję **OK**:
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| myVirtualNetwork1 — Jeśli jeszcze nie jest wybrana, wybierz **sieci wirtualnej** , a następnie wybierz **myVirtualNetwork1** w obszarze **sieci wirtualnej wybierz**.|
    |Podsieć| Podsieć1 — Jeśli jeszcze nie jest wybrana, wybierz **podsieci** , a następnie wybierz **podsieć1** w obszarze **Wybierz podsieć**.|
    
    ![Ustawienia maszyny wirtualnej](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. W obszarze **Utwórz** w **Podsumowanie**, wybierz pozycję **Utwórz** rozpocząć wdrażanie maszyny Wirtualnej.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny Wirtualnej

Wykonaj kroki od 1 do 6 ponownie, z następującymi zmianami:

|Ustawienie|Wartość|
|---|---|
|Name (Nazwa) | myVm2|
|Sieć wirtualna | myVirtualNetwork2|

Maszyny wirtualne potrwać kilka minut, aby utworzyć. Nie Kontynuuj pozostałe kroki, dopóki obie maszyny wirtualne są tworzone.

## <a name="communicate-between-vms"></a>Komunikację między maszynami wirtualnymi

1. W *wyszukiwania* w górnej części portalu pozycję Rozpocznij wpisywanie *myVm1*. Gdy **myVm1** pojawia się w wynikach wyszukiwania, wybierz go.
2. Tworzenie połączenia pulpitu zdalnego z *myVm1* maszyny Wirtualnej, wybierając **Connect**, jak pokazano na poniższej ilustracji:

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Aby nawiązać połączenie z maszyną Wirtualną, Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz **Connect**.
4. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić poświadczenia zostały wprowadzone podczas tworzenia maszyny Wirtualnej), następnie wybierz **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **tak** Aby nawiązać połączenie.
6. W kolejnym kroku ping jest używany do komunikacji z *myVm2* maszyny Wirtualnej z *myVm1* maszyny Wirtualnej. Polecenie ping używa kontrolki komunikat protokołu protokołu ICMP (Internet), której odmówiono przez zaporę systemu Windows, domyślnie. Na *myVm1* maszyny Wirtualnej, włączyć ICMP przez zaporę systemu Windows, dzięki czemu można zbadać poleceniem ping tej maszyny Wirtualnej z *myVm2* w kolejnym kroku przy użyciu programu PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Chociaż ping jest używany do komunikacji między maszynami wirtualnymi w tym artykule, nie zaleca się stosowanie protokołu ICMP przez zaporę systemu Windows dla wdrożeń produkcyjnych.

7. Aby nawiązać połączenie *myVm2* maszyny Wirtualnej, wprowadź następujące polecenie w wierszu polecenia *myVm1* maszyny Wirtualnej:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Ponieważ włączony ping *myVm1*, można teraz wywołać go za pomocą adresu IP:

    ```
    ping 10.0.0.4
    ```
    
9. Odłączyć swoje sesje protokołu RDP do obu *myVm1* i *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń grupy zasobów i wszystkie zasoby, które zawiera: 

1. Wprowadź *myResourceGroup* w **wyszukiwania** pole w górnej części portalu. Po wyświetleniu **myResourceGroup** w wynikach wyszukiwania wybierz go.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź *myResourceGroup* dla **typu nazwa grupy zasobów:** i wybierz **usunąć**.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób połączyć dwie sieci w tym samym regionie Azure, z sieci wirtualnej komunikacji równorzędnej. Można również elementów równorzędnych sieci wirtualnych w różnych [obsługiwane regiony](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcji Azure](create-peering-different-subscriptions.md#portal), a także utworzyć [gwiazdy projektów sieci](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z komunikacji równorzędnej. Aby dowiedzieć się więcej na temat sieci wirtualnej komunikacji równorzędnej, zobacz [komunikacji równorzędnej omówienie sieci wirtualnej](virtual-network-peering-overview.md) i [Zarządzanie komunikacji równorzędnych sieci wirtualnych](virtual-network-manage-peering.md).

Połącz z komputera użytkownika do sieci wirtualnej za pośrednictwem sieci VPN i interakcji z zasobami w sieci wirtualnej lub połączyć za pomocą sieci wirtualnych, zobacz [połączenia komputera z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
