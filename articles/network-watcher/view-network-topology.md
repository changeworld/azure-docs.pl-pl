---
title: Wyświetl topologii sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie wyświetlania zasobów w sieci wirtualnej i relacje między zasobami.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: 6ef165ddc481bf84c6189635e36b97eb9518261e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Widok topologii sieci wirtualnej platformy Azure

W tym artykule dowiesz sposób wyświetlania zasobów w sieci wirtualnej Microsoft Azure i relacje między zasobami. Na przykład sieć wirtualna zawiera podsieci. Podsieci zawierają zasoby, takie jak maszyny wirtualne Azure (VM). Maszyny wirtualne mają jeden lub więcej interfejsów sieciowych. Każda podsieć może mieć sieciowej grupy zabezpieczeń i skojarzony tabeli tras. Możliwości topologii obserwatora sieciowego Azure umożliwia wyświetlenie wszystkich zasobów w sieci wirtualnej, zasoby skojarzone z zasobami w sieci wirtualnej i relacje między zasobami.

Można użyć [portalu Azure](#azure-portal), [interfejsu wiersza polecenia Azure](#azure-cli), lub [PowerShell](#powershell) do wyświetlania topologii.

## <a name = "azure-portal"></a>Topologia widoku — portalu Azure

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, które ma niezbędne [uprawnienia](required-rbac-permissions.md).
2. W górnej części, lewym rogu portalu, wybierz opcję **wszystkie usługi**.
3. W **wszystkie usługi** filtrować pole, wprowadź *obserwatora sieciowego*. Gdy **obserwatora sieciowego** pojawia się w wynikach, wybierz go.
4. Wybierz **topologii**. Generowanie topologii wymaga obserwatora sieciowego, w tym samym regionie, występującego w sieci wirtualnej, który chcesz wygenerować topologii. Jeśli nie masz obserwatora sieciowego, włączona w regionie, który znajduje się w sieci wirtualnej, aby wygenerować topologii dla obserwatorów sieci są tworzone automatycznie dla Ciebie we wszystkich regionach. Obserwatorów sieci są tworzone w grupie zasobów o nazwie **NetworkWatcherRG**.
5. Wybierz subskrypcję, grupy zasobów w sieci wirtualnej, który chcesz wyświetlić topologii, a następnie wybierz sieci wirtualnej. Na poniższej ilustracji przedstawiono topologię sieci wirtualnej o nazwie *MyVnet*, w grupie zasobów o nazwie *MyResourceGroup*:

    ![Wyświetl topologię](./media/view-network-topology/view-topology.png)

    Jak widać na poprzedniej ilustracji sieć wirtualna zawiera trzy podsieci. W jednej podsieci ma maszyn wirtualnych wdrożonych w niej. Maszyna wirtualna ma jeden interfejs sieciowy do niego dołączony i publiczny adres IP skojarzony. Dwie podsieci ma tabelę tras powiązanych z nimi. Każda tabela tras zawiera dwie trasy. W jednej podsieci ma sieciową grupę zabezpieczeń skojarzoną do niego. Informacje o topologii jest wyświetlana tylko dla zasobów, które są: — w tej samej grupie zasobów i regionu co *myVnet* sieci wirtualnej. Na przykład grupa zabezpieczeń sieci, który istnieje w grupie zasobów innych niż *MyResourceGroup*, nie jest widoczne, nawet jeśli sieciowej grupy zabezpieczeń jest skojarzony z podsiecią w *MyVnet* sieci wirtualnej .
        — W ciągu lub skojarzona z zasobami, *myVnet* sieci wirtualnej. Na przykład grupa zabezpieczeń sieci, nie jest skojarzony z interfejsem podsieci lub sieci w *myVnet* sieci wirtualnej nie jest widoczne, nawet jeśli grupa zabezpieczeń sieci znajduje się w *MyResourceGroup* grupy zasobów.

    Topologia pokazany na rysunku jest utworzony po wdrożeniu sieci wirtualnej **kierowania ruchu za pośrednictwem przykładowym skrypcie urządzenie wirtualne sieci**, które można wdrożyć przy użyciu [interfejsu wiersza polecenia Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), lub [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Wybierz **Pobierz topologii** pobranie obrazu jako plik można edytować, w formacie svg.

Zasoby przedstawione na diagramie są podzbiorem składników sieciowych w sieci wirtualnej. Na przykład gdy grupa zabezpieczeń sieci jest wyświetlany, zasady zabezpieczeń w nim nie są wyświetlane na diagramie. Chociaż nie są rozróżniane na diagramie, wiersze reprezentuje jeden dwie relacje: *zawierania* lub *skojarzone*. Aby zapoznać się z pełną listą zasobów w sieci wirtualnej, a typ relacji między zasobami, generowanie topologia z [PowerShell](#powershell) lub [interfejsu wiersza polecenia Azure](#azure-cli).

## <a name = "azure-cli"></a>Wyświetl topologia — wiersza polecenia platformy Azure

W kolejnych krokach można uruchomić polecenia:
- W powłoce chmury Azure, wybierając **spróbuj on** u góry po prawej z dowolnego polecenia. Powłoka chmury Azure jest wolnego powłoka interaktywne, która zawiera typowe narzędzia Azure wstępnie zainstalowane i skonfigurowane do korzystania z Twoim kontem.
- Za pomocą interfejsu wiersza polecenia z tego komputera. Po uruchomieniu interfejsu wiersza polecenia z tego komputera, kroki opisane w tym artykule wymagają interfejsu wiersza polecenia Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

Konto, którego używasz, musi mieć niezbędnych [uprawnienia](required-rbac-permissions.md).

1. Jeśli masz już obserwatora sieciowego, w tym samym regionie co sieć wirtualna, która ma zostać utworzona topologii dla, przejdź do kroku 3. Utwórz grupę zasobów zawiera obserwatora sieciowego, z [Tworzenie grupy az](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupy zasobów w *eastus* regionu:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Utwórz obserwatora sieciowego, z [skonfigurować obserwatora sieciowego az](/cli/azure/network/watcher#az-network-watcher-configure). Poniższy przykład tworzy obserwatora sieciowego, w *eastus* regionu:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Wyświetl topologia z [obserwatora Pokaż topologii sieci az](/cli/azure/network/watcher#az-network-watcher-show-topology). Poniższy przykład widoki topologii dla grupy zasobów o nazwie *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informacje o topologii jest zwracany wyłącznie dla zasobów, które znajdują się w tej samej grupie zasobów co *MyResourceGroup* grupy zasobów i tym samym regionie co obserwatora sieciowego. Na przykład grupa zabezpieczeń sieci, który istnieje w grupie zasobów innych niż *MyResourceGroup*, nie jest widoczne, nawet jeśli sieciowej grupy zabezpieczeń jest skojarzony z podsiecią w *MyVnet* sieci wirtualnej .

  Dowiedz się więcej o [relacje](#relationhips) i [właściwości](#properties) w dane wyjściowe. Jeśli nie masz istniejącej sieci wirtualnej, aby wyświetlić topologii dla, można utworzyć przy użyciu jednego [kierować ruchem przez urządzenie wirtualne sieci](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przykładowym skrypcie. Aby wyświetlić diagram topologii i pobierz go w pliku można edytować, użyj [portal](#azure-portal).

## <a name = "powershell"></a>Topologia widoku — PowerShell

W kolejnych krokach można uruchomić polecenia:
- W powłoce chmury Azure, wybierając **spróbuj on** u góry po prawej z dowolnego polecenia. Powłoka chmury Azure jest wolnego powłoka interaktywne, która zawiera typowe narzędzia Azure wstępnie zainstalowane i skonfigurowane do korzystania z Twoim kontem.
- Korzystając z polecenia programu PowerShell z komputera. Po uruchomieniu programu PowerShell z komputera, kroki opisane w tym artykule wymagają wersji 5.7.0 lub nowszej modułu AzureRm. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Konto, którego używasz, musi mieć niezbędnych [uprawnienia](required-rbac-permissions.md).

1. Jeśli masz już obserwatora sieciowego, w tym samym regionie co sieć wirtualna, która ma zostać utworzona topologii dla, przejdź do kroku 3. Utwórz grupę zasobów zawiera obserwatora sieciowego, z [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Poniższy przykład tworzy grupy zasobów w *eastus* regionu:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Utwórz obserwatora sieciowego, z [AzureRmNetworkWatcher nowy](/powershell/module/azurerm.network/new-azurermnetworkwatcher). Poniższy przykład tworzy obserwatora sieciowego w regionie eastus:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Pobrać wystąpienia obserwatora sieciowego, z [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). Poniższy przykład pobiera obserwatora sieciowego, w regionie wschodnie stany USA:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Topologia z pobrać [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). Poniższy przykład pobiera topologii sieci wirtualnej w grupie zasobów o nazwie *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informacje o topologii jest zwracany wyłącznie dla zasobów, które znajdują się w tej samej grupie zasobów co *MyResourceGroup* grupy zasobów i tym samym regionie co obserwatora sieciowego. Na przykład grupa zabezpieczeń sieci, który istnieje w grupie zasobów innych niż *MyResourceGroup*, nie jest widoczne, nawet jeśli sieciowej grupy zabezpieczeń jest skojarzony z podsiecią w *MyVnet* sieci wirtualnej .

  Dowiedz się więcej o [relacje](#relationhips) i [właściwości](#properties) w dane wyjściowe. Jeśli nie masz istniejącej sieci wirtualnej, aby wyświetlić topologii dla, można utworzyć przy użyciu jednego [kierować ruchem przez urządzenie wirtualne sieci](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przykładowym skrypcie. Aby wyświetlić diagram topologii i pobierz go w pliku można edytować, użyj [portal](#azure-portal).

## <a name="relationships"></a>Relacje

Wszystkie zasoby zwracane w topologii mieć jeden z następujących typów relacji do innego zasobu:

| Typ relacji | Przykład                                                                                                |
| ---               | ---                                                                                                    |
| Zawierania       | Sieć wirtualna zawiera podsieci. Podsieć zawiera karty sieciowej.                            |
| Skojarzone        | Interfejs sieciowy jest skojarzona z maszyną Wirtualną. Publiczny adres IP jest skojarzony z karty sieciowej. |

## <a name="properties"></a>Właściwości

Wszystkie zasoby zwracane w topologii mieć następujące właściwości:

- **Nazwa**: Nazwa zasobu
- **Identyfikator**: identyfikator URI zasobu.
- **Lokalizacja**: regionu Azure, czy zasób istnieje w.
- **Skojarzenia**: listę skojarzeń odwołuje się do obiektu. Każdego skojarzenia ma następujące właściwości:
    - **Obiekt AssociationType**: odwołuje się do relacji między obiekt podrzędny i obiektu nadrzędnego. Prawidłowe wartości to *zawiera* lub *skojarzone*.
    - **Nazwa**: Nazwa zasobu, do którego istnieje odwołanie.
    - **ResourceId**:-identyfikator URI zasobu, do którego odwołuje się powiązanie.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [zdiagnozować problem z siecią ruchu filtr do lub z maszyny Wirtualnej](diagnose-vm-network-traffic-filtering-problem.md) za pomocą Monitora sieci IP przepływu Sprawdź możliwości
- Dowiedz się, jak [zdiagnozować problem routingu ruchu sieciowego z maszyny Wirtualnej](diagnose-vm-network-routing-problem.md) przy użyciu obserwatora sieciowego następnego przeskoku możliwości