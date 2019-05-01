---
title: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych — samouczek — Azure Portal | Microsoft Docs
description: W tym samouczku dowiesz się, jak połączyć sieci wirtualne za pomocą komunikacji równorzędnej sieci wirtualnych z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 943cad871330e2f3b6e13b33dca582ab545fe4be
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726569"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Samouczek: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych przy użyciu witryny Azure portal

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Te sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (określane także jako globalne wirtualne sieci równorzędne). Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dwóch sieci wirtualnych
> * Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
> * Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
> * Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-connect-virtual-networks-cli.md) lub [programu Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork1|
    |Przestrzeń adresowa|10.0.0.0/16|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |Nazwa podsieci|Subnet1|
    |Zakres adresów podsieci|10.0.0.0/24|

      ![Tworzenie sieci wirtualnej](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Ponownie wykonaj kroki 1–3 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork2|
    |Przestrzeń adresowa|10.1.0.0/16|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Zakres adresów podsieci|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Tworzenia komunikacji równorzędnej sieci wirtualnych

1. W polu wyszukiwania w górnej części witryny Azure Portal rozpocznij wpisywanie ciągu *myVirtualNetwork1*. Gdy pozycja **myVirtualNetwork1** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Komunikacja równorzędna** w obszarze **USTAWIENIA**, a następnie wybierz pozycję **+ Dodaj**, jak pokazano na poniższej ilustracji:

    ![Tworzenie komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**.

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork1-myVirtualNetwork2|
    |Subskrypcja| Wybierz subskrypcję.|
    |Sieć wirtualna|myVirtualNetwork2 — aby wybrać sieć wirtualną *myVirtualNetwork2*, wybierz pozycję **Sieć wirtualna**, a następnie wybierz pozycję **myVirtualNetwork2**. Możesz wybrać sieć wirtualną znajdującą się w tym samym regionie lub w innym regionie.|

    ![Ustawienia komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **STAN KOMUNIKACJI RÓWNORZĘDNEJ** to *Zainicjowano*, jak pokazano na poniższym obrazie:

    ![Stan komunikacji równorzędnej](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Jeśli stan nie jest widoczny, odśwież przeglądarkę.

4. W polu **Szukaj** w górnej części witryny Azure Portal rozpocznij wpisywanie ciągu *myVirtualNetwork2*. Gdy pozycja **myVirtualNetwork2** pojawi się w wynikach wyszukiwania, wybierz ją.
5. Ponownie wykonaj kroki 2–3 z następującymi zmianami, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork2-myVirtualNetwork1|
    |Sieć wirtualna|myVirtualNetwork1|

    **STAN KOMUNIKACJI RÓWNORZĘDNEJ** to *Połączono*. Platforma Azure również zmieniła stan komunikacji równorzędnej dla komunikacji równorzędnej *myVirtualNetwork2-myVirtualNetwork1* z *Zainicjowano* na *Połączono*. Komunikacja równorzędna sieci wirtualnych nie zostanie w pełni nawiązana do chwili, gdy obie sieci wirtualne nie będą miały stanu *Połączono*. 

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. Możesz wybrać inny system operacyjny, ale w pozostałych krokach założono, że wybierzesz system **Windows Server 2016 Datacenter**. 
3. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVm1|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| myVirtualNetwork1 — jeśli jeszcze nie jest wybrana, wybierz pozycję **Sieć wirtualna**, a następnie wybierz pozycję **myVirtualNetwork1** w obszarze **Wybierz sieć wirtualną**.|
    |Podsieć| Subnet1 — jeśli jeszcze nie jest wybrana, wybierz pozycję **Podsieć**, a następnie wybierz pozycję **Subnet1** w obszarze **Wybierz podsieć**.|
    
    ![Ustawienia maszyny wirtualnej](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Wykonaj ponownie kroki 1–6 z następującymi zmianami:

|Ustawienie|Wartość|
|---|---|
|Name (Nazwa) | myVm2|
|Sieć wirtualna | myVirtualNetwork2|

Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. W polu *Szukaj* w górnej części portalu rozpocznij wpisywanie ciągu *myVm1*. Gdy pozycja **myVm1** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Utwórz połączenie pulpitu zdalnego z maszyną wirtualną *myVm1*, wybierając pozycję **Połącz**, jak pokazano na poniższym obrazie:

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Aby nawiązać połączenie z maszyną wirtualną, otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.
4. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.
6. W kolejnym kroku polecenie ping jest używane do komunikacji z maszyną wirtualną *myVm2* z poziomu maszyny wirtualnej *myVm1*. Polecenie ping używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest domyślnie blokowany przez zaporę systemu Windows. Na maszynie wirtualnej *myVm1* wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, dzięki czemu w kolejnym kroku będzie można wykonać polecenie ping względem tej maszyny wirtualnej z poziomu maszyny wirtualnej *myVm2* przy użyciu programu PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Chociaż w tym samouczku do komunikacji między maszynami wirtualnymi jest używane polecenie ping, nie zaleca się zezwalania na komunikację za pomocą protokołu ICMP w zaporze systemu Windows we wdrożeniach produkcyjnych.

7. Aby nawiązać połączenie z maszyną wirtualną *myVm2*, wprowadź następujące polecenie w wierszu polecenia maszyny wirtualnej *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Ponieważ polecenie ping zostało włączone na maszynie wirtualnej *myVm1*, możesz teraz wykonać polecenie ping przy użyciu adresu IP:

    ```
    ping 10.0.0.4
    ```
    
9. Odłącz swoje sesje protokołu RDP do obu maszyn wirtualnych, *myVm1* i *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją wraz ze wszystkimi zasobami, które zawiera: 

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób połączenia dwóch sieci w tym samym regionie świadczenia usługi Azure za pomocą komunikacji równorzędnej sieci wirtualnych. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#portal), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Aby połączyć własny komputer z siecią wirtualną za pośrednictwem sieci VPN i mieć możliwość interakcji z zasobami w sieci wirtualnej lub w sieciach wirtualnych z komunikacją równorzędną, zobacz [Connect your computer to a virtual network (Łączenie komputera z siecią wirtualną)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
