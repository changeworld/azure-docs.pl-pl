---
title: Tworzenie sieci wirtualnej platformy Azure (klasycznej) z wieloma podsieciami | Microsoft Docs
description: Dowiedz się, jak utworzyć sieć wirtualną (klasyczną) z wieloma podsieciami na platformie Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: d934386a47c339cd3abdf72578736b44d40e7952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059005"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Tworzenie sieci wirtualnej (klasycznej) z wieloma podsieciami

> [!IMPORTANT]
> Platforma Azure ma dwa [różne modele wdrażania](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) służące do tworzenia zasobów i pracy z nimi: Menedżer zasobów i klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca tworzenie większości nowych sieci wirtualnych za pomocą modelu wdrażania [Menedżer zasobów](quick-create-portal.md) .

W tym samouczku dowiesz się, jak utworzyć podstawową sieć wirtualną platformy Azure (klasyczną) z oddzielnymi podsieciami publicznymi i prywatnymi. W podsieci można tworzyć zasoby platformy Azure, takie jak maszyny wirtualne i usługi w chmurze. Zasoby utworzone w sieciach wirtualnych (klasycznych) mogą komunikować się ze sobą oraz z zasobami w innych sieciach podłączonych do sieci wirtualnej.

Dowiedz się więcej na temat wszystkich ustawień [sieci wirtualnej](manage-virtual-network.md) i [podsieci](virtual-network-manage-subnet.md) .

> [!WARNING]
> Sieci wirtualne (klasyczne) są natychmiast usuwane przez platformę Azure, gdy [subskrypcja jest wyłączona](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Sieci wirtualne (klasyczne) są usuwane niezależnie od tego, czy zasoby istnieją w sieci wirtualnej. Po ponownym włączeniu subskrypcji należy ponownie utworzyć zasoby, które istniały w sieci wirtualnej.

Sieć wirtualną (klasyczną) można utworzyć przy użyciu [Azure Portal](#portal), [interfejsu wiersza polecenia platformy Azure (CLI) 1,0](#azure-cli)lub [programu PowerShell](#powershell).

## <a name="portal"></a>Portal

1. W przeglądarce internetowej przejdź do [Azure Portal](https://portal.azure.com). Zaloguj się przy użyciu [konta platformy Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Jeśli nie masz konta platformy Azure, możesz zarejestrować się w celu [bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kliknij pozycję **Utwórz zasób** w portalu.
3. W polu **Wyszukaj w witrynie Marketplace** w górnej części **nowego** wyświetlonego okienka wprowadź wartość *Sieć wirtualna* . Kliknij pozycję **Sieć wirtualna** , gdy zostanie wyświetlona w wynikach wyszukiwania.
4. W wyświetlonym okienku **Virtual Network** wybierz pozycję **klasyczny** , a następnie kliknij pozycję **Utwórz**. 
5. Wprowadź następujące wartości w okienku **Tworzenie sieci wirtualnej (klasycznej)** , a następnie kliknij pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name|myVnet|
    |Przestrzeń adresowa|10.0.0.0/16|
    |Nazwa podsieci|Public|
    |Zakres adresów podsieci|10.0.0.0/24|
    |Resource group|Pozostaw opcję **Utwórz nowy** , a następnie wprowadź nazwę **zasobu**.|
    |Subskrypcja i lokalizacja|Wybierz swoją subskrypcję i lokalizację.

    Jeśli dopiero zaczynasz na platformie Azure, Dowiedz się więcej na temat [grup zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)i [lokalizacji](https://azure.microsoft.com/regions) (nazywanych również *regionami*).
4. W portalu można utworzyć tylko jedną podsieć podczas tworzenia sieci wirtualnej. W tym samouczku utworzysz drugą podsieć po utworzeniu sieci wirtualnej. Możesz później utworzyć zasoby dostępne w Internecie w podsieci **publicznej** . Można również tworzyć zasoby, które nie są dostępne z Internetu w podsieci **prywatnej** . Aby utworzyć drugą podsieć, wprowadź **myVnet** w polu **Wyszukaj zasoby** w górnej części strony. Kliknij pozycję **myVnet** , gdy pojawi się w wynikach wyszukiwania.
5. Kliknij pozycję **podsieci** (w sekcji **Ustawienia** ) w wyświetlonym okienku **Utwórz sieć wirtualną (klasyczną)** .
6. Kliknij pozycję **+ Dodaj** w wyświetlonym okienku **myVnet-Subnets** .
7. Wprowadź wartość **Private** dla **nazwy** w okienku **Dodaj podsieć** . Wprowadź **10.0.1.0/24** dla **zakresu adresów**.  Kliknij przycisk **OK**.
8. W okienku **myVnet-Subnets** (podsieci) można zobaczyć utworzone przez siebie podsieci **publiczne** i **prywatne** .
9. **Opcjonalnie**: Po zakończeniu tego samouczka możesz chcieć usunąć utworzone przez siebie zasoby, aby nie były naliczane opłaty za użycie:
    - Kliknij pozycję **Przegląd** w okienku **myVnet** .
    - Kliknij ikonę **Usuń** w okienku **myVnet** .
    - Aby potwierdzić usunięcie, kliknij przycisk **tak** w polu **Usuń sieć wirtualną** .

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Można [zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyć interfejsu wiersza polecenia w Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Aby uzyskać pomoc dotyczącą poleceń interfejsu wiersza `azure <command> --help`polecenia, wpisz polecenie. 
2. W sesji interfejsu wiersza polecenia Zaloguj się do platformy Azure przy użyciu poniższego polecenie. Jeśli klikniesz pozycję **Wypróbuj** w polu poniżej, zostanie otwarte Cloud Shell. Możesz zalogować się do subskrypcji platformy Azure bez wprowadzania następującego polecenia:

    ```azurecli-interactive
    azure login
    ```

3. Aby upewnić się, że interfejs wiersza polecenia jest w trybie zarządzania usługami, wprowadź następujące polecenie:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Utwórz sieć wirtualną z podsiecią prywatną:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Utwórz podsieć publiczną w ramach sieci wirtualnej:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Zapoznaj się z siecią wirtualną i podsieciami:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcjonalnie**: Możesz chcieć usunąć zasoby, które zostały utworzone po zakończeniu pracy z tym samouczkiem, aby nie naliczane były opłaty za użycie:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Chociaż nie można określić grupy zasobów w celu utworzenia sieci wirtualnej (klasycznej) przy użyciu interfejsu wiersza polecenia, platforma Azure tworzy sieć wirtualną w grupie zasobów o nazwie *default-Networking*.

## <a name="powershell"></a>PowerShell

1. Zainstaluj najnowszą wersję modułu PowerShell [platformy Azure](https://www.powershellgallery.com/packages/Azure) . Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Rozpocznij sesję programu PowerShell.
3. W programie PowerShell zaloguj się do platformy Azure, wprowadzając polecenie `Add-AzureAccount`.
4. Zmień poniższą ścieżkę i nazwę pliku, stosownie do potrzeb, a następnie wyeksportuj istniejący plik konfiguracji sieci:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Aby utworzyć sieć wirtualną z podsieciami publicznymi i prywatnymi, użyj dowolnego edytora tekstów w celu dodania elementu **VirtualNetworkSite** , który następuje po pliku konfiguracji sieci.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Zapoznaj się z całym [schematem pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Zaimportuj plik konfiguracji sieci:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Zaimportowanie zmienionego pliku konfiguracji sieci może spowodować zmianę istniejących sieci wirtualnych (klasycznych) w Twojej subskrypcji. Upewnij się, że dodano tylko poprzednią sieć wirtualną, ale nie wszystkie istniejące sieci wirtualne nie są zmieniane ani usuwane z subskrypcji. 

7. Zapoznaj się z siecią wirtualną i podsieciami:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcjonalnie**: Możesz chcieć usunąć zasoby, które zostały utworzone po zakończeniu pracy z tym samouczkiem, aby nie naliczane były opłaty za użycie. Aby usunąć sieć wirtualną, należy ponownie wykonać kroki 4-6, tym razem usuwając element **VirtualNetworkSite** dodany w kroku 5.
 
> [!NOTE]
> Chociaż nie można określić grupy zasobów w celu utworzenia sieci wirtualnej (klasycznej) przy użyciu programu PowerShell, platforma Azure tworzy sieć wirtualną w grupie zasobów o nazwie *default-Networking*.

---

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat wszystkich ustawień sieci wirtualnej i podsieci, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md) i zarządzanie podsieciami [sieci wirtualnej](virtual-network-manage-subnet.md). Dostępne są różne opcje używania sieci wirtualnych i podsieci w środowisku produkcyjnym w celu spełnienia innych wymagań.
- Utwórz maszynę wirtualną z [systemem Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , a następnie podłącz ją do istniejącej sieci wirtualnej.
- Aby połączyć dwie sieci wirtualne w tej samej lokalizacji platformy Azure, należy utworzyć [komunikację równorzędną sieci wirtualnej](create-peering-different-deployment-models.md) między sieciami wirtualnymi. Sieć wirtualną sieci wirtualnej (Menedżer zasobów) można połączyć z siecią wirtualną (klasyczną), ale nie można utworzyć komunikacji równorzędnej między dwiema sieciami wirtualnymi (klasycznymi).
- Połącz sieć wirtualną z siecią lokalną przy użyciu [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub obwodu [usługi Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
