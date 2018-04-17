---
title: Tworzenie sieci wirtualnej platformy Azure (klasyczną) z wieloma podsieciami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną (klasyczne) z wieloma podsieciami na platformie Azure.
services: virtual-network
documentationcenter: ''
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: f9a3788ae13fe198cd40eceed78a407d7f0439a9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Tworzenie sieci wirtualnej (wdrożenia klasyczne) z wieloma podsieciami

> [!IMPORTANT]
> Platforma Azure ma dwa [różne modele wdrażania](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia i pracy z zasobami: Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca utworzenie większości nowych sieci wirtualnych za pomocą [Resource Manager](quick-create-portal.md) modelu wdrażania.

W tym samouczku jak utworzyć podstawowy sieć wirtualna platformy Azure (klasyczną) ma oddzielne podsieci publiczne i prywatne. Można utworzyć zasobów platformy Azure, takich jak maszyny wirtualne i usługi w chmurze w podsieci. Zasoby utworzone w sieciach wirtualnych (klasyczne) może komunikować się ze sobą oraz z zasobami w innych sieciach, połączony z siecią wirtualną.

Dowiedz się więcej na temat wszystkich [sieci wirtualnej](manage-virtual-network.md) i [podsieci](virtual-network-manage-subnet.md) ustawienia.

> [!WARNING]
> Sieci wirtualne (klasyczne) są natychmiast usuwane przez Azure podczas [subskrypcja została wyłączona](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Sieci wirtualne (klasyczne) są usuwane niezależnie od tego, czy istnieje zasobów w sieci wirtualnej. Jeśli później ponownie włączysz subskrypcji, muszą zostać ponownie utworzone zasoby, które były dostępne w sieci wirtualnej.

Można utworzyć sieci wirtualnej (wdrożenia klasyczne) przy użyciu [portalu Azure](#portal), [Azure interfejsu wiersza polecenia (CLI) 1.0](#azure-cli), lub [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. W przeglądarce sieci Web, przejdź do [portalu Azure](https://portal.azure.com). Zaloguj się za pomocą programu [konta Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Jeśli nie masz konta platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kliknij przycisk **Utwórz zasób** w portalu.
3. Wprowadź *sieci wirtualnej* w **wyszukiwania portalu Marketplace** pole w górnej części **nowy** okienko zostanie wyświetlone. Kliknij przycisk **sieci wirtualnej** po wyświetleniu w wynikach wyszukiwania.
4. Wybierz **klasycznego** w **wybierz model wdrożenia** polu **sieci wirtualnej** okienko zostanie wyświetlone, następnie kliknij przycisk **Utwórz**. 
5. Wprowadź następujące wartości **Utwórz sieć wirtualną (klasyczne)** okienka, a następnie kliknij przycisk **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVnet|
    |Przestrzeń adresowa|10.0.0.0/16|
    |Nazwa podsieci|Publiczne|
    |Zakres adresów podsieci|10.0.0.0/24|
    |Grupa zasobów|Pozostaw **Utwórz nowy** zaznaczone, a następnie wprowadź **myResourceGroup**.|
    |Subskrypcji i lokalizacji|Wybierz subskrypcji i lokalizacji.

    Jeśli jesteś nowym użytkownikiem usługi Azure, Dowiedz się więcej o [grup zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subskrypcje](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), i [lokalizacje](https://azure.microsoft.com/regions) (zwaną także *regionów*).
4. W portalu można utworzyć tylko jedną podsieć, podczas tworzenia sieci wirtualnej. W tym samouczku utworzysz drugiej podsieci po utworzeniu sieci wirtualnej. Później można utworzyć Internet dostępnych zasobów w **publicznego** podsieci. Można także utworzyć zasoby, które nie są dostępne z Internetu w **prywatnej** podsieci. Aby utworzyć drugą podsieć, wprowadź **myVnet** w **wyszukiwania zasobów** u góry strony. Kliknij przycisk **myVnet** po wyświetleniu w wynikach wyszukiwania.
5. Kliknij przycisk **podsieci** (w **ustawienia** sekcji) na **Utwórz sieć wirtualną (klasyczne)** okienko zostanie wyświetlone.
6. Kliknij przycisk **+ Dodaj** na **myVnet - podsieci** okienko zostanie wyświetlone.
7. Wprowadź **prywatnej** dla **nazwa** na **Dodaj podsieć** okienka. Wprowadź **10.0.1.0/24** dla **zakres adresów**.  Kliknij przycisk **OK**.
8. Na **myVnet - podsieci** okienku zostanie wyświetlony **publicznego** i **prywatnej** podsieci, które zostały utworzone.
9. **Opcjonalne**: po zakończeniu tego samouczka warto usunąć zasoby, które zostały utworzone, tak aby nie wiąże się z opłatami użycia:
    - Kliknij przycisk **omówienie** na **myVnet** okienka.
    - Kliknij przycisk **usunąć** ikonę na **myVnet** okienka.
    - Aby potwierdzić usunięcie, kliknij przycisk **tak** w **sieci wirtualnej Delete** pole.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Możesz albo [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub użyj interfejsu wiersza polecenia powłoki chmury Azure. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Aby uzyskać pomoc dotyczącą poleceń interfejsu wiersza polecenia, wpisz `azure <command> --help`. 
2. W sesji interfejsu wiersza polecenia należy zalogować się przy użyciu polecenia znajdujący się na platformie Azure. Jeśli klikniesz przycisk **wypróbuj** w polu poniżej, otwiera powłokę chmury. Możesz zalogować się do subskrypcji platformy Azure, bez konieczności wprowadzania następujące polecenie:

    ```azurecli-interactive
    azure login
    ```

3. Aby upewnić się, że interfejsu wiersza polecenia jest w trybie zarządzania usługami, wprowadź następujące polecenie:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Utwórz sieć wirtualną z prywatnej podsieci:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Utwórz publiczny podsieci w sieci wirtualnej:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Przegląd sieci wirtualnej i podsieci:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcjonalne**: można usunąć zasoby, które zostały utworzone po zakończeniu tego samouczka, dzięki czemu nie wiąże się z opłatami użycia:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Chociaż nie można określić grupę zasobów, aby utworzyć sieć wirtualną (klasyczne) za pomocą interfejsu wiersza polecenia, platforma Azure tworzy sieć wirtualną w grupie zasobów o nazwie *sieci domyślne*.

## <a name="powershell"></a>PowerShell

1. Zainstaluj najnowszą wersję programu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modułu. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell zaloguj się do platformy Azure, wprowadzając polecenie `Add-AzureAccount`.
4. Zmień ścieżkę i nazwę pliku, zgodnie z potrzebami, następujące, a następnie wyeksportować do istniejącego pliku konfiguracji sieci:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Aby utworzyć sieć wirtualną przy użyciu publiczne i prywatne podsieci, użyj dowolnego edytora tekstów, aby dodać **VirtualNetworkSite** element znajdujący się w pliku konfiguracji sieci.

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

    Przejrzyj pełnego [schemat pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importowanie pliku konfiguracji sieci:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importowanie pliku konfiguracji sieci zmienione może spowodować zmiany w istniejących sieci wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, tylko dodać poprzedniej sieci wirtualnej i nie zmienić lub usunąć istniejące sieci wirtualnych z subskrypcji. 

7. Przegląd sieci wirtualnej i podsieci:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcjonalne**: można usunąć zasoby, które zostały utworzone po zakończeniu tego samouczka, dzięki czemu nie wiąże się z opłatami użycia. Można usunąć sieci wirtualnej, pełną kroki 4 – 6 ponownie, usunięcie tego czasu **VirtualNetworkSite** element dodanej w kroku 5.
 
> [!NOTE]
> Chociaż nie można określić grupę zasobów, aby utworzyć sieć wirtualną (klasyczne) przy użyciu programu PowerShell, platforma Azure tworzy sieć wirtualną w grupie zasobów o nazwie *sieci domyślne*.

---

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o wszystkich ustawieniach podsieci i sieci wirtualnej, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md) i [Zarządzanie podsieci sieci wirtualnej](virtual-network-manage-subnet.md). Istnieją różne opcje dotyczące używania sieci wirtualnych i podsieci w środowisku produkcyjnym, aby spełnić różne wymagania.
- Filtrowanie ruchu przychodzącego i wychodzącego podsieci, tworzenie i stosowanie [sieciowej grupy zabezpieczeń](virtual-networks-nsg.md) do podsieci.
- Utwórz [systemu Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny wirtualnej, a następnie połącz się z istniejącą siecią wirtualną.
- Aby połączyć dwie sieci wirtualne w tej samej lokalizacji platformy Azure, Utwórz [sieci wirtualnej komunikacji równorzędnej](create-peering-different-deployment-models.md) między sieciami wirtualnymi. Elementu równorzędnego sieci wirtualnej (Resource Manager) do sieci wirtualnej (wdrożenia klasyczne), ale nie można utworzyć komunikacji równorzędnej między dwoma sieciami wirtualnymi (klasyczny).
- Połączyć sieć wirtualną z siecią lokalną przy użyciu [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) obwodu.
