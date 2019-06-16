---
title: Tworzenie sieci wirtualnej platformy Azure (model klasyczny) z wieloma podsieciami | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną (model klasyczny) z wieloma podsieciami na platformie Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
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
ms.openlocfilehash: e40648ef47b108050486d43eefdb1564786c053e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62098120"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Tworzenie sieci wirtualnej (klasycznej) z wieloma podsieciami

> [!IMPORTANT]
> Platforma Azure ma dwa [różnych modeli wdrażania](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do tworzenia i pracy z zasobami: Usługi Resource Manager i model klasyczny. Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca tworzenie większości nowych sieci wirtualnych za pomocą [usługi Resource Manager](quick-create-portal.md) modelu wdrażania.

W tym samouczku informacje o sposobie tworzenia Podstawowa sieć wirtualna platformy Azure (model klasyczny) ma oddzielne podsieci publicznych i prywatnych. Można tworzyć zasoby platformy Azure, takich jak maszyny wirtualne i usługi w chmurze w podsieci. Zasoby utworzone w sieci wirtualne (klasyczne) mogą komunikować się ze sobą oraz z zasobami w innych sieciach podłączone do sieci wirtualnej.

Dowiedz się więcej o wszystkich [sieci wirtualnej](manage-virtual-network.md) i [podsieci](virtual-network-manage-subnet.md) ustawienia.

> [!WARNING]
> Sieci wirtualne (klasyczne) zostaną natychmiast usunięte przez platformę Azure podczas [subskrypcja została wyłączona](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Sieci wirtualne (klasyczne) są usuwane niezależnie od tego, czy istnieją zasoby w sieci wirtualnej. Jeśli później ponownie włączysz subskrypcji, zasoby, które istniały w sieci wirtualnej muszą zostać ponownie utworzone.

Można utworzyć sieci wirtualnej (model klasyczny) przy użyciu [witryny Azure portal](#portal), [interfejsu wiersza polecenia platformy Azure (CLI) 1.0](#azure-cli), lub [PowerShell](#powershell).

## <a name="portal"></a>Portal

1. W przeglądarce internetowej przejdź do [witryny Azure portal](https://portal.azure.com). Zaloguj się przy użyciu usługi [konta platformy Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Jeśli nie masz konta platformy Azure, możesz zarejestrować się w celu [bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Kliknij przycisk **Utwórz zasób** w portalu.
3. Wprowadź *sieć wirtualna* w **Przeszukaj witrynę Marketplace** polu w górnej części **New** wyświetlonym okienku. Kliknij przycisk **sieć wirtualna** , gdy pojawia się w wynikach wyszukiwania.
4. Wybierz **klasycznego** w **wybierz model wdrożenia** pole w **sieci wirtualnej** okienko zostanie wyświetlone, następnie kliknij przycisk **Utwórz**. 
5. Wprowadź następujące wartości **Utwórz sieć wirtualną (wersja klasyczna)** okienka, a następnie kliknij przycisk **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVnet|
    |Przestrzeń adresowa|10.0.0.0/16|
    |Nazwa podsieci|Public|
    |Zakres adresów podsieci|10.0.0.0/24|
    |Grupa zasobów|Pozostaw **Utwórz nową** zaznaczone, a następnie wprowadź **myResourceGroup**.|
    |Subskrypcji i lokalizacji|Wybierz subskrypcję i lokalizację.

    Jeśli jesteś nowym użytkownikiem platformy Azure, Dowiedz się więcej o [grup zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [subskrypcje](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), i [lokalizacje](https://azure.microsoft.com/regions) (nazywane także *regionów*).
4. W portalu można utworzyć tylko jedną podsieć, po utworzeniu sieci wirtualnej. W tym samouczku utworzysz drugą podsieć po utworzeniu sieci wirtualnej. Możesz później utworzyć zasoby dostępne za pośrednictwem Internetu w **publicznych** podsieci. Można także utworzyć zasoby, które są dostępne z Internetu w **prywatnej** podsieci. Aby utworzyć drugą podsieć, wprowadź **myVnet** w **Wyszukaj zasoby** polu w górnej części strony. Kliknij przycisk **myVnet** , gdy pojawia się w wynikach wyszukiwania.
5. Kliknij przycisk **podsieci** (w **ustawienia** sekcji) na **Utwórz sieć wirtualną (wersja klasyczna)** wyświetlonym okienku.
6. Kliknij przycisk **+ Dodaj** na **Mojasiećwirtualna — podsieci** wyświetlonym okienku.
7. Wprowadź **prywatnej** dla **nazwa** na **Dodaj podsieć** okienka. Wprowadź **10.0.1.0/24** dla **zakres adresów**.  Kliknij przycisk **OK**.
8. Na **Mojasiećwirtualna — podsieci** okienku zostanie wyświetlony **publicznych** i **prywatnej** podsieci, które zostały utworzone.
9. **Opcjonalnie**: Po ukończeniu tego samouczka, możesz chcieć usunąć zasoby, które zostały utworzone, dlatego, że nie powodują naliczania opłat za użycie:
    - Kliknij przycisk **Przegląd** na **myVnet** okienka.
    - Kliknij przycisk **Usuń** ikonę na **myVnet** okienka.
    - Aby potwierdzić usunięcie, kliknij przycisk **tak** w **usuwania sieci wirtualnej** pole.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Możesz albo [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub za pomocą interfejsu wiersza polecenia w ramach usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Aby uzyskać pomoc dotyczącą poleceń interfejsu wiersza polecenia, wpisz `azure <command> --help`. 
2. W sesji programu interfejsu wiersza polecenia należy zalogować się przy użyciu następującego polecenia na platformie Azure. Jeśli klikniesz **wypróbuj** w polu poniżej, usługi Cloud Shell zostanie otwarta. Możesz zalogować się do subskrypcji platformy Azure bez konieczności wprowadzania następujące polecenie:

    ```azurecli-interactive
    azure login
    ```

3. Aby upewnić się, że interfejs wiersza polecenia znajduje się w trybie zarządzania usługami, wpisz następujące polecenie:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Utwórz sieć wirtualną z podsiecią prywatnych:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Utwórz publiczny podsieci w sieci wirtualnej:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Przejrzyj sieci wirtualnej i podsieci:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Opcjonalnie**: Możesz chcieć usunąć zasoby, które są tworzone po ukończeniu tego samouczka, aby nie powodują naliczania opłat za użycie:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Chociaż nie można określić grupę zasobów, aby utworzyć sieć wirtualną (klasycznego) przy użyciu interfejsu wiersza polecenia, platforma Azure tworzy sieć wirtualną w grupie zasobów o nazwie *domyślnej sieci*.

## <a name="powershell"></a>PowerShell

1. Zainstaluj najnowszą wersję programu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modułu. Jeśli jesteś nowym użytkownikiem programu Azure PowerShell, zobacz temat [Azure PowerShell overview (Omówienie programu Azure PowerShell)](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Uruchom sesję programu PowerShell.
3. W programie PowerShell zaloguj się do platformy Azure, wprowadzając polecenie `Add-AzureAccount`.
4. Zmień następujące ścieżkę i nazwę pliku, zgodnie z potrzebami, a następnie wyeksportować do istniejącego pliku konfiguracji sieci:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Aby utworzyć sieć wirtualną z podsieciami publicznych i prywatnych, należy użyć dowolnego edytora tekstów, Dodaj **VirtualNetworkSite** elementu, który następuje do pliku konfiguracji sieci.

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

    Przejrzyj pełną [schemat pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importuj plik konfiguracji sieci:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Importowanie pliku konfiguracyjnego sieci zmienione mogą powodować zmiany w istniejących sieciach wirtualnych (klasyczne) w ramach subskrypcji. Upewnij się, należy dodawać tylko wtedy poprzedniego sieci wirtualnej i nie zmienić lub usunąć wszystkie istniejące sieci wirtualne z subskrypcji. 

7. Przejrzyj sieci wirtualnej i podsieci:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Opcjonalnie**: Możesz chcieć usunąć zasoby, które są tworzone po ukończeniu tego samouczka, aby nie powodują naliczania opłat za użycie. Można usunąć sieci wirtualnej, wykonaj kroki 4 – 6 ponownie, ten czas usuwania **VirtualNetworkSite** element dodanej w kroku 5.
 
> [!NOTE]
> Chociaż nie można określić grupę zasobów, aby utworzyć sieć wirtualną (klasycznego) przy użyciu programu PowerShell, platforma Azure tworzy sieć wirtualną w grupie zasobów o nazwie *domyślnej sieci*.

---

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się o wszystkich sieci wirtualnej i podsieci ustawień, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md) i [Zarządzanie podsieciami sieci wirtualnej](virtual-network-manage-subnet.md). Dostępne są różne opcje dotyczące używania sieci wirtualne i podsieci w środowisku produkcyjnym, aby spełnić różne wymagania.
- Tworzenie [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny wirtualnej, a następnie połącz go z istniejącej sieci wirtualnej.
- Aby połączyć dwie sieci wirtualne w tej samej lokalizacji platformy Azure, Utwórz [wirtualne sieci równorzędne](create-peering-different-deployment-models.md) między sieciami wirtualnymi. Komunikacja równorzędna sieci wirtualnej (Resource Manager) do sieci wirtualnej (model klasyczny), ale nie można utworzyć komunikację równorzędną między dwiema sieciami wirtualnymi (model klasyczny).
- Łączenie sieci wirtualnej z siecią lokalną za pomocą [bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [usługi Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) obwodu.
