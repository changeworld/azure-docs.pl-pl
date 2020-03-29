---
title: Wyświetlanie topologii sieci wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyświetlić zasoby w sieci wirtualnej i relacje między zasobami.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: 675919db55932d3ccc04fd5397f6f673832b4900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840574"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Wyświetlanie topologii sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak wyświetlić zasoby w sieci wirtualnej platformy Microsoft Azure i relacje między zasobami. Na przykład sieć wirtualna zawiera podsieci. Podsieci zawierają zasoby, takie jak maszyny wirtualne platformy Azure (VM). Maszyny wirtualne mają co najmniej jeden interfejs sieciowy. Każda podsieć może mieć grupę zabezpieczeń sieci i tabelę tras skojarzoną z nią. Możliwości topologii usługi Azure Network Watcher umożliwia wyświetlanie wszystkich zasobów w sieci wirtualnej, zasobów skojarzonych z zasobami w sieci wirtualnej i relacji między zasobami.

Można użyć [witryny Azure portal](#azure-portal), [interfejsu wiersza polecenia platformy Azure](#azure-cli)lub programu [PowerShell,](#powershell) aby wyświetlić topologię.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Zobacz topologię — witryna Azure portal

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta, które ma niezbędne [uprawnienia](required-rbac-permissions.md).
2. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi**.
3. W polu filtru **Wszystkie usługi** wprowadź *wpisać program Kontrola sieci .* Gdy w wynikach pojawi się nazwa **Network Watcher**, wybierz ją.
4. Wybierz pozycję **Topologia**. Generowanie topologii wymaga obserwatora sieci w tym samym regionie, w których istnieje sieć wirtualna, dla której ma zostać wygenerowana topologia. Jeśli nie masz włączonego obserwatora sieci w regionie, w którego znajduje się sieć wirtualna, dla której chcesz wygenerować topologię, obserwatorzy sieci są automatycznie tworzoni we wszystkich regionach. Obserwatorzy sieci są tworzone w grupie zasobów o nazwie **NetworkWatcherRG**.
5. Wybierz subskrypcję, grupę zasobów sieci wirtualnej, dla której chcesz wyświetlić topologię, a następnie wybierz sieć wirtualną. Na poniższej ilustracji jest wyświetlana topologia sieci wirtualnej o nazwie *MyVnet*w grupie zasobów o nazwie *MyResourceGroup*:

    ![Zobacz topologię](./media/view-network-topology/view-topology.png)

    Jak widać na poprzednim zdjęciu, sieć wirtualna zawiera trzy podsieci. Jedna podsieć ma w nim wdrożoną maszynę wirtualną. Maszyna wirtualna ma jeden interfejs sieciowy podłączony do niego i publiczny adres IP skojarzone z nim. Pozostałe dwie podsieci mają tabelę tras skojarzoną z nimi. Każda tabela tras zawiera dwie trasy. Do jednej podsieci jest skojarzona sieć zabezpieczeń. Informacje topologii są wyświetlane tylko dla zasobów, które są:
    
    - W obrębie tej samej grupy zasobów i regionu co sieć wirtualna *myVnet.* Na przykład sieciowa grupa zabezpieczeń istniejąca w grupie zasobów innej niż *MyResourceGroup*nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci jest skojarzona z podsiecią w sieci wirtualnej *MyVnet.*
    - W sieci wirtualnej *myVnet* lub skojarzonej z nimi. Na przykład sieciowa grupa zabezpieczeń, która nie jest skojarzona z podsiecią lub interfejsem sieciowym w sieci wirtualnej *myVnet,* nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieciowych znajduje się w grupie zasobów *MyResourceGroup.*

   Topologia pokazana na rysunku dotyczy sieci wirtualnej utworzonej po wdrożeniu **ruchu trasy za pośrednictwem przykładowego skryptu urządzenia wirtualnego sieci,** którą można wdrożyć przy użyciu interfejsu [wiersza polecenia platformy Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)lub programu [PowerShell.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

6. Wybierz **pobierz topologię,** aby pobrać obraz jako edytowalny plik w formacie svg.

Zasoby pokazane na diagramie są podzbiorem składników sieciowych w sieci wirtualnej. Na przykład, gdy wyświetlana jest grupa zabezpieczeń sieci, reguły zabezpieczeń w niej nie są wyświetlane na diagramie. Chociaż nie są zróżnicowane na diagramie, linie reprezentują jedną z dwóch relacji: *Hermetyzacja* lub *skojarzone*. Aby wyświetlić pełną listę zasobów w sieci wirtualnej i typ relacji między zasobami, wygeneruj topologię za pomocą [programu PowerShell](#powershell) lub [interfejsu wiersza polecenia platformy Azure.](#azure-cli)

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Wyświetlanie topologii — narzędzie azure cli

Polecenia można uruchamiać w kolejnych krokach:
- W usłudze Azure Cloud Shell, wybierając **try it** w prawym górnym rogu dowolnego polecenia. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, która ma wstępnie zainstalowane i skonfigurowane narzędzia platformy Azure do użycia z twoim kontem.
- Uruchamiając wiersz polecenia z komputera. Jeśli uruchomisz interfejsu wiersza polecenia z komputera, kroki opisane w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.

Konto, którego używasz, musi mieć niezbędne [uprawnienia](required-rbac-permissions.md).

1. Jeśli masz już obserwatora sieci w tym samym regionie co sieć wirtualna, dla której chcesz utworzyć topologię, przejdź do kroku 3. Utwórz grupę zasobów zawierającą obserwatora sieciowego z [tworzeniem grupy az](/cli/azure/group). Poniższy przykład tworzy grupę zasobów w regionie *eastus:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Utwórz obserwatora sieciowego z [skonfigurowaniem obserwatora sieciowego az](/cli/azure/network/watcher#az-network-watcher-configure). Poniższy przykład tworzy obserwatora sieci w regionie *eastus:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Zobacz topologię z [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). W poniższym przykładzie przedstawiono topologię grupy zasobów o nazwie *MyResourceGroup:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informacje topologii są zwracane tylko dla zasobów, które znajdują się w tej samej grupie zasobów co grupa zasobów *MyResourceGroup* i w tym samym regionie co obserwator sieci. Na przykład sieciowa grupa zabezpieczeń istniejąca w grupie zasobów innej niż *MyResourceGroup*nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci jest skojarzona z podsiecią w sieci wirtualnej *MyVnet.*

   Dowiedz się więcej o relacjach i właściwościach zwracanych danych [wyjściowych.](#properties) Jeśli nie masz istniejącej sieci wirtualnej do wyświetlania topologii, można utworzyć ją przy użyciu [ruchu trasy za pośrednictwem przykładu skryptu urządzenia wirtualnego sieci.](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Aby wyświetlić diagram topologii i pobrać go do edytowanego pliku, użyj [portalu](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Zobacz topologię — program PowerShell

Polecenia można uruchamiać w kolejnych krokach:
- W usłudze Azure Cloud Shell, wybierając **try it** w prawym górnym rogu dowolnego polecenia. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, która ma wstępnie zainstalowane i skonfigurowane narzędzia platformy Azure do użycia z twoim kontem.
- Uruchamiając program PowerShell z komputera. Jeśli program PowerShell jest uruchamiany z komputera, `Az` ten artykuł wymaga modułu programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Konto, którego używasz, musi mieć niezbędne [uprawnienia](required-rbac-permissions.md).

1. Jeśli masz już obserwatora sieci w tym samym regionie co sieć wirtualna, dla której chcesz utworzyć topologię, przejdź do kroku 3. Utwórz grupę zasobów zawierającą obserwatora sieciowego z [new-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Poniższy przykład tworzy grupę zasobów w regionie *eastus:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Utwórz obserwatora sieciowego z [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). Poniższy przykład tworzy obserwatora sieci w regionie eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Pobierz wystąpienie obserwatora sieci za pomocą [get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). Poniższy przykład pobiera obserwatora sieci w regionie wschodnie stany USA:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Pobierz topologię za pomocą [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). Poniższy przykład pobiera topologię sieci wirtualnej w grupie zasobów o nazwie *MyResourceGroup:*

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informacje topologii są zwracane tylko dla zasobów, które znajdują się w tej samej grupie zasobów co grupa zasobów *MyResourceGroup* i w tym samym regionie co obserwator sieci. Na przykład sieciowa grupa zabezpieczeń istniejąca w grupie zasobów innej niż *MyResourceGroup*nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci jest skojarzona z podsiecią w sieci wirtualnej *MyVnet.*

   Dowiedz się więcej o relacjach i właściwościach zwracanych danych [wyjściowych.](#properties) Jeśli nie masz istniejącej sieci wirtualnej do wyświetlania topologii, można utworzyć ją przy użyciu [ruchu trasy za pośrednictwem przykładu skryptu urządzenia wirtualnego sieci.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Aby wyświetlić diagram topologii i pobrać go do edytowanego pliku, użyj [portalu](#azure-portal).

## <a name="relationships"></a>Relacje

Wszystkie zasoby zwrócone w topologii mają jeden z następujących typów relacji z innym zasobem:

| Typ relacji | Przykład                                                                                                |
| ---               | ---                                                                                                    |
| Zawieranie       | Sieć wirtualna zawiera podsieć. Podsieć zawiera interfejs sieciowy.                            |
| Skojarzone        | Interfejs sieciowy jest skojarzony z maszyną wirtualną. Publiczny adres IP jest skojarzony z interfejsem sieciowym. |

## <a name="properties"></a>Właściwości

Wszystkie zasoby zwrócone w topologii mają następujące właściwości:

- **Nazwa**: Nazwa zasobu
- **Identyfikator:** Identyfikator URI zasobu.
- **Lokalizacja:** Region platformy Azure, w którym istnieje zasób.
- **Skojarzenia**: Lista skojarzeń z obiektem, do którego istnieje odwołanie. Każde skojarzenie ma następujące właściwości:
    - **AssociationType**: Odwołuje się do relacji między obiektem podrzędnym a obiektem nadrzędnym. Prawidłowe wartości *to Zawiera* lub *Skojarzone*.
    - **Nazwa**: Nazwa zasobu, do którego istnieje odwołanie.
    - **ResourceId**: - Identyfikator URI zasobu, do którego odwołuje się skojarzenie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zdiagnozować problem z filtrem ruchu sieciowego do lub z maszyny wirtualnej](diagnose-vm-network-traffic-filtering-problem.md) przy użyciu funkcji weryfikacji przepływu adresów IP obserwatora sieci
- Dowiedz się, jak [zdiagnozować problem z routingiem ruchu sieciowego z maszyny wirtualnej](diagnose-vm-network-routing-problem.md) przy użyciu funkcji następnego przeskoku obserwatora sieci
