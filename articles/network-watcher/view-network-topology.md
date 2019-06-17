---
title: Wyświetlanie topologii sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie wyświetlania zasobów w sieci wirtualnej i relacje między zasobami.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: a67f1cca54c89ead9ae7fc46ef0c9fc8c5217c74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64682057"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Wyświetlanie topologii sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak do wyświetlania zasobów w sieci wirtualnej Microsoft Azure i relacje między zasobami. Na przykład sieć wirtualna zawiera podsieci. Podsieci zawierają zasoby, takie jak Azure Virtual Machines (VM). Maszyny wirtualne mają co najmniej jeden interfejs sieciowy. Każda podsieć może mieć sieciowej grupy zabezpieczeń i tabelę tras, powiązany. Topologia możliwości usługi Azure Network Watcher umożliwia wyświetlanie wszystkich zasobów w sieci wirtualnej zasoby skojarzone z zasobami w sieci wirtualnej i relacje między zasobami.

Możesz użyć [witryny Azure portal](#azure-portal), [wiersza polecenia platformy Azure](#azure-cli), lub [PowerShell](#powershell) do wyświetlania topologii.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name = "azure-portal"></a>Wyświetlanie topologii — witryna Azure portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta które ma niezbędne [uprawnienia](required-rbac-permissions.md).
2. W górnej części, lewym rogu portalu, wybierz opcję **wszystkich usług**.
3. W **wszystkich usług** filtrowania pole, wprowadź *usługi Network Watcher*. Gdy w wynikach pojawi się nazwa **Network Watcher**, wybierz ją.
4. Wybierz **topologii**. Generowanie topologii wymaga w tym samym regionie, który znajduje się w sieci wirtualnej, która ma zostać wygenerowany topologii usługi network watcher. Jeśli nie masz usługi network watcher w regionie, w którym znajduje się w sieci wirtualnej, który chcesz wygenerować topologii dla włączone obserwatorów sieci są tworzone automatycznie dla Ciebie we wszystkich regionach. Obserwatorzy sieciowi są tworzone w grupie zasobów o nazwie **NetworkWatcherRG**.
5. Wybierz subskrypcję i grupę zasobów w sieci wirtualnej, którą chcesz wyświetlić topologii, a następnie wybierz sieć wirtualną. Na poniższej ilustracji przedstawiono topologię sieci wirtualnej o nazwie *MyVnet*, w grupie zasobów o nazwie *MyResourceGroup*:

    ![Wyświetlanie topologii](./media/view-network-topology/view-topology.png)

    Jak widać na poprzedniej ilustracji, sieć wirtualna zawiera trzy podsieci. Jedną podsieć zawiera maszyny Wirtualnej wdrożonej w nim. Maszyna wirtualna ma jeden interfejs sieciowy dołączony do niego i publiczny adres IP, powiązany. Dwie podsieci mają tabelę tras skojarzone z nimi. Każda tabela tras zawiera dwie trasy. Jedną podsieć ma sieciową grupę zabezpieczeń skojarzoną do niego. Informacje o topologii jest wyświetlana tylko dla zasobów, które są:
    
    - W ramach tej samej grupie zasobów i regionie co *myVnet* sieci wirtualnej. Na przykład, sieciowej grupy zabezpieczeń, która istnieje w grupie zasobów innej niż *MyResourceGroup*, nie jest widoczne, nawet jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią w *MyVnet* sieci wirtualnej .
    - W ciągu lub skojarzona z zasobami w ramach, *myVnet* sieci wirtualnej. Na przykład, sieciowej grupy zabezpieczeń, która nie jest skojarzona z podsiecią lub interfejsem sieciowym w *myVnet* sieci wirtualnej nie jest widoczne, nawet jeśli sieciowa grupa zabezpieczeń jest w *MyResourceGroup* Grupa zasobów.

   Topologia pokazano na rysunku jest tworzone po wdrożeniu sieci wirtualnej **kierowanie ruchu przez przykładowy skrypt urządzenie wirtualne sieci**, którą można wdrożyć przy użyciu [wiersza polecenia platformy Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), lub [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Wybierz **pobieranie topologii** pobranie obrazu jako plik można edytować, w formacie svg.

Wyświetlane na diagramie zasoby są podzbiorem składniki sieciowe w sieci wirtualnej. Na przykład gdy sieciowa grupa zabezpieczeń jest wyświetlany, zasady zabezpieczeń w nim nie są wyświetlane na diagramie. Chociaż nie są rozróżniane na diagramie, wiersze reprezentują jedną dwie relacje: *Zawieranie* lub *skojarzone*. Aby wyświetlić pełną listę zasobów w sieci wirtualnej i typ relacji między zasobami, generowanie topologia z [PowerShell](#powershell) lub [wiersza polecenia platformy Azure](#azure-cli).

## <a name = "azure-cli"></a>Wyświetlanie topologii — interfejs wiersza polecenia platformy Azure

Polecenia można uruchomić w opisanych poniżej:
- W usłudze Azure Cloud Shell, wybierając **wypróbuj** u góry prawo do dowolnego polecenia. Azure Cloud Shell to bezpłatna interaktywna powłoka, zawierającej popularnych narzędzi platformy Azure wstępnie zainstalowane i skonfigurowane do korzystania z Twoim kontem.
- Korzystając z polecenia interfejsu wiersza polecenia na komputerze. Po uruchomieniu interfejsu wiersza polecenia z komputera, kroki opisane w tym artykule wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konto, którego używasz, musi mieć niezbędne [uprawnienia](required-rbac-permissions.md).

1. Jeśli masz już usługę network watcher w tym samym regionie co sieć wirtualna, którą chcesz utworzyć topologii dla, przejdź do kroku 3. Utwórz grupę zasobów, aby zawierała przy użyciu usługi network watcher [Tworzenie grupy az](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów w *eastus* regionu:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Tworzenie przy użyciu usługi network watcher [Konfigurowanie usługi network watcher az](/cli/azure/network/watcher#az-network-watcher-configure). Poniższy przykład obejmuje tworzenie usługi network watcher w *eastus* regionu:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Wyświetlanie topologii z [obserwatora show topologia sieci az](/cli/azure/network/watcher#az-network-watcher-show-topology). Poniższy przykład wyświetla topologii dla grupy zasobów o nazwie *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informacje o topologii zwracany jest tylko dla zasobów, które znajdują się w tej samej grupie zasobów co *MyResourceGroup* grupy zasobów i tym samym regionie co usługa network watcher. Na przykład, sieciowej grupy zabezpieczeń, która istnieje w grupie zasobów innej niż *MyResourceGroup*, nie jest widoczne, nawet jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią w *MyVnet* sieci wirtualnej .

   Dowiedz się więcej na temat relacji i [właściwości](#properties) zwróconych danych wyjściowych. Jeśli masz istniejącą sieć wirtualną, aby wyświetlić topologii dla ją utworzyć, korzystając [kierowania ruchu przez wirtualne urządzenie sieciowe](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przykładowy skrypt. Aby wyświetlić diagram topologii i pobrać plik można edytować, użyj [portal](#azure-portal).

## <a name = "powershell"></a>Wyświetlanie topologii — PowerShell

Polecenia można uruchomić w opisanych poniżej:
- W usłudze Azure Cloud Shell, wybierając **wypróbuj** u góry prawo do dowolnego polecenia. Azure Cloud Shell to bezpłatna interaktywna powłoka, zawierającej popularnych narzędzi platformy Azure wstępnie zainstalowane i skonfigurowane do korzystania z Twoim kontem.
- Korzystając z polecenia programu PowerShell na komputerze. Po uruchomieniu programu PowerShell z komputera, w tym artykule wymaga programu Azure PowerShell `Az` modułu. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Konto, którego używasz, musi mieć niezbędne [uprawnienia](required-rbac-permissions.md).

1. Jeśli masz już usługę network watcher w tym samym regionie co sieć wirtualna, którą chcesz utworzyć topologii dla, przejdź do kroku 3. Utwórz grupę zasobów, aby zawierała przy użyciu usługi network watcher [New AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Poniższy przykład obejmuje tworzenie grupy zasobów w *eastus* regionu:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Tworzenie przy użyciu usługi network watcher [New AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). Poniższy przykład tworzy usługi network watcher w regionie eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Pobieranie z wystąpienia usługi Network Watcher [Get AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). Poniższy przykład pobiera usługi network watcher w regionie wschodnie stany USA:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Pobieranie topologii z [Get AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). Poniższy przykład pobiera topologii sieci wirtualnej w grupie zasobów o nazwie *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informacje o topologii zwracany jest tylko dla zasobów, które znajdują się w tej samej grupie zasobów co *MyResourceGroup* grupy zasobów i tym samym regionie co usługa network watcher. Na przykład, sieciowej grupy zabezpieczeń, która istnieje w grupie zasobów innej niż *MyResourceGroup*, nie jest widoczne, nawet jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią w *MyVnet* sieci wirtualnej .

   Dowiedz się więcej na temat relacji i [właściwości](#properties) zwróconych danych wyjściowych. Jeśli masz istniejącą sieć wirtualną, aby wyświetlić topologii dla ją utworzyć, korzystając [kierowania ruchu przez wirtualne urządzenie sieciowe](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) przykładowy skrypt. Aby wyświetlić diagram topologii i pobrać plik można edytować, użyj [portal](#azure-portal).

## <a name="relationships"></a>Relacje

Wszystkie zasoby zwracane w topologii mieć jedną z następujących typów relacji do innego zasobu:

| Typ relacji | Przykład                                                                                                |
| ---               | ---                                                                                                    |
| Zawierania       | Sieć wirtualna zawiera podsieci. Podsieć zawiera interfejs sieciowy.                            |
| Skojarzone        | Interfejs sieciowy jest skojarzony z maszyną Wirtualną. Publiczny adres IP jest skojarzona z interfejsem sieciowym. |

## <a name="properties"></a>Właściwości

Wszystkie zasoby zwracane w topologii mają następujące właściwości:

- **Nazwa**: Nazwa zasobu
- **Identyfikator**: Identyfikator URI zasobu.
- **Lokalizacja**: Region platformy Azure, czy zasób istnieje w.
- **Skojarzenia**: Lista skojarzenia przywoływanego obiektu. Każdego skojarzenia ma następujące właściwości:
    - **AssociationType**: Odwołuje się do relacji między obiekt podrzędny i nadrzędny obiekt. Prawidłowe wartości to *zawiera* lub *skojarzone*.
    - **Nazwa**: Nazwa zasobu, do którego istnieje odwołanie.
    - **ResourceId**: — identyfikator URI zasobu, do których odwołuje się do skojarzenia.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [diagnozowania sieci problemu z filtrowaniem ruchu do lub z maszyny Wirtualnej](diagnose-vm-network-traffic-filtering-problem.md) przy użyciu przepływu dla adresu IP usługi Network Watcher Sprawdź możliwości
- Dowiedz się, jak [diagnozowanie problemu routingu ruchu sieciowym z maszyny Wirtualnej](diagnose-vm-network-routing-problem.md) przy użyciu usługi Network Watcher następnego przeskoku możliwości
