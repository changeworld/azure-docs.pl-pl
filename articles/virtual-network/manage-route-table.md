---
title: Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie tabeli tras.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 7ef57960b81e13eefebfab4430eec4db0c1eb2e8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344897"
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie lub usuwanie tabeli tras

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić domyślne platformy Azure routing, możesz to zrobić, tworząc tabelę tras. Jeśli jesteś nowym użytkownikiem routingu w sieciach wirtualnych, możesz uzyskać więcej informacji w [Omówienie routingu](virtual-networks-udr-overview.md) lub wykonując [samouczek](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Dla tego samouczka jest wymagany moduł Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje limit liczby tabel tras, które można utworzyć lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W lewym górnym rogu portalu, wybierz **+ Utwórz zasób**.
2. Wybierz **sieć**, a następnie wybierz **tabeli tras**.
3. Wprowadź **nazwa** tabeli tras można wybrać usługi **subskrypcji**, Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę zasobów, wybierz pozycję **lokalizacji** , a następnie wybierz **Utwórz**. Jeśli planujesz kojarzenie tabeli tras do podsieci w sieci wirtualnej, która jest połączona z siecią lokalną za pośrednictwem bramy sieci VPN i wyłączysz **propagację trasy protokołu BGP**, trasy w środowisku lokalnym nie są propagowane do Interfejsy sieciowe w podsieci.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [Tworzenie tabeli tras az sieci](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Wyświetl tabele tras

W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją. Tabele tras, które istnieją w Twojej subskrypcji są wyświetlane.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [listy tabelę tras sieciowych az](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Wyświetl szczegóły tabelę tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, na liście, który chcesz wyświetlić szczegóły. W obszarze **ustawienia**, można wyświetlić **trasy** w tabeli tras i **podsieci** tabelę tras jest skojarzona z.
3. Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące informacje:
    *   [Dziennik aktywności](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Kontrola dostępu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skrypt automatyzacji](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Zmień tabelę tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybieranie tabeli tras, które chcesz zmienić. Najbardziej typowe zmiany są [Dodawanie](#create-a-route) lub [usuwanie](#delete-a-route) tras i [kojarzenie](#associate-a-route-table-to-a-subnet) tabele, tras lub [skojarzenia](#dissociate-a-route-table-from-a-subnet) tabele z tras podsieci.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Podsieć może mieć zero lub jedną tabelę tras, powiązany. Tabela tras może być skojarzona z zera lub wiele podsieci. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, należy skojarzyć tabelę tras w każdej podsieci, które mają tabeli tras skojarzonej z. Cały ruch, pozostawiając podsieci jest kierowany oparte na trasach, został utworzony w tabelach tras, [domyślne trasy](virtual-networks-udr-overview.md#default), i propagowane trasy z siecią lokalną, jeśli sieć wirtualna jest połączona z (Brama sieci wirtualnej platformy Azure Usługa ExpressRoute lub sieci VPN, jeśli przy użyciu protokołu BGP z bramą sieci VPN). Można skojarzyć tylko tabelę tras do podsieci w sieciach wirtualnych, które istnieją w tej samej lokalizacji platformy Azure i subskrypcji co tabeli tras.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieć wirtualną na liście, który zawiera podsieć, którą chcesz skojarzyć tabelę tras w celu.
3. Wybierz **podsieci** w obszarze **ustawienia**.
4. Wybierz podsieć, którą chcesz skojarzyć tabelę tras w celu.
5. Wybierz **tabeli tras**, wybierz tabeli tras, które chcesz skojarzyć z podsiecią, a następnie wybierz **Zapisz**.

Jeśli Twoja sieć wirtualna jest podłączona do bramy sieci VPN platformy Azure, nie należy kojarzyć tabeli tras z [podsiecią bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) zawierającą trasy z adresem docelowym 0.0.0.0/0. Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby uzyskać więcej informacji o używaniu 0.0.0.0/0 w trasie, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md#default-route).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuń skojarzenie tabelę tras z podsiecią

Jeśli usuniesz skojarzenie elementu tabelę tras z podsieci, platforma Azure kieruje ruch na podstawie jego [domyślne trasy](virtual-networks-udr-overview.md#default).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieć wirtualną, która zawiera podsieci, na pewno chcesz usunąć skojarzenie tabelę tras z.
3. Wybierz **podsieci** w obszarze **ustawienia**.
4. Wybierz podsieć, na pewno chcesz usunąć skojarzenie tabeli tras z.
5. Wybierz **tabeli tras**, wybierz opcję **Brak**, a następnie wybierz **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Jeśli tabela tras jest skojarzona na wszelkie podsieci, nie można usunąć. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabelę tras z wszystkich podsieci przed próbą usunięcia go.

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz **...**  po prawej stronie tabeli tras do usunięcia.
3. Wybierz **Usuń**, a następnie wybierz pozycję **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [usunąć tabeli tras az sieci](/cli/azure/network/route-table/route#az_network_route_table_delete)
- Program PowerShell: [Remove AzureRmRouteTable](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermroutetable?view=azurermps-6.8.1) 

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje limit liczby trasy na tabelę tras można utworzyć dla lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras z listy, który chcesz dodać trasę do.
3. Wybierz **trasy**w obszarze **ustawienia**.
4. Wybierz pozycję **+ Dodaj**.
5. Wprowadź unikatową **nazwa** trasy w tabeli tras.
6. Wprowadź **prefiksu adresu**, w notacji CIDR, który chcesz kierowanie ruchu do. Nie można zduplikować prefiks w więcej niż jedna trasa w tabeli tras, chociaż prefiks mogą być zawarte w prefiksem innego. Na przykład jeśli 10.0.0.0/16 jest zdefiniowany jako prefiks w trasie jeden, nadal można zdefiniować innej trasy z prefiksem adresu 10.0.0.0/24. Platforma Azure wybiera trasę ruchu w oparciu o dopasowanie najdłuższego prefiksu. Aby dowiedzieć się, jak platforma Azure wybiera trasy, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Wybierz **typu następnego przeskoku**. Aby uzyskać szczegółowy opis wszystkich typów następnego przeskoku, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
8. Wprowadź adres IP dla **następnego przeskoku**. Adres można wprowadzić tylko w przypadku wybrania *urządzenie wirtualne* dla **typu następnego przeskoku**.
9. Kliknij przycisk **OK**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tworzenie az sieci route-table route](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Wyświetl trasy

Tabela tras zawiera zero lub wiele tras. Aby dowiedzieć się więcej o informacje podane podczas wyświetlania tras, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras z listy, który chcesz wyświetlić trasy dla.
3. Wybierz **trasy** w obszarze **ustawienia**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [listę trasy w tabeli tras sieci az](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, którą chcesz wyświetlić szczegółowe informacje o trasie dla.
3. Wybierz **trasy**.
4. Wybierz trasy, aby wyświetlić szczegóły.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Zmień trasę

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, aby zmienić trasę dla.
3. Wybierz **trasy**.
4. Wybierz trasy, który chcesz zmienić.
5. Zmień istniejące ustawienia do nowych ustawień, a następnie wybierz **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci route-table route aktualizacji](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Usuwanie trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, aby usunąć trasę dla.
3. Wybierz **trasy**.
4. Wybierz z listy tras, **...**  po prawej stronie trasy do usunięcia.
5. Wybierz **Usuń**, a następnie wybierz **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci tabeli tras route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Wyświetlanie obowiązujących tras

Skuteczne trasy dla każdego interfejsu sieciowego dołączonych do maszyny wirtualnej są kombinacją tabel tras, które zostały utworzone, domyślnych tras platformy i wszystkie trasy propagowane z sieciami lokalnymi za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Opis obowiązujące trasy dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Można wyświetlić obowiązujące trasy dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, którą chcesz wyświetlić obowiązujące trasy dla. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją i wybierz maszynę wirtualną z listy.
2. Wybierz **sieć** w obszarze **ustawienia**.
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **obowiązujące trasy** w obszarze **pomoc techniczna i rozwiązywanie problemów**.
5. Przejrzyj listę skuteczne trasy w celu ustalenia, czy dla której chcesz przekierować ruch do istnieje poprawnej trasy. Dowiedz się więcej na temat typów następnego przeskoku widoczni na tej liście [Omówienie routingu](virtual-networks-udr-overview.md).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network nic show obowiązywać route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 

## <a name="validate-routing-between-two-endpoints"></a>Sprawdź poprawność routingu między dwoma punktami końcowymi

Można określić typ następnego przeskoku między maszyną wirtualną i adres IP innego zasobu platformy Azure, lokalnych zasobów lub zasobu w Internecie. Określanie Azure routing jest przydatne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, konieczne jest posiadanie istniejącej usługi Network Watcher. Jeśli nie masz istniejącej usługi Network Watcher, utwórz ją, wykonując kroki opisane w [Tworzenie wystąpienia usługi Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. W polu wyszukiwania w górnej części portalu wprowadź *usługi network watcher* w polu wyszukiwania. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
2. Wybierz **następnego przeskoku** w obszarze **narzędzia diagnostyczne sieci**.
3. Wybierz swoje **subskrypcji** i **grupy zasobów** źródłowej maszyny wirtualnej, którą chcesz zweryfikować, routing od.
4. Wybierz **maszyny wirtualnej**, **interfejs sieciowy** dołączonych do maszyny wirtualnej i **źródłowy adres IP** przypisany do interfejsu sieciowego, który chcesz zweryfikować Routing od.
5. Wprowadź **docelowy adres IP** , którą chcesz zweryfikować routingu w celu.
6. Wybierz **następnego przeskoku**.
7. Po krótkim czasie oczekiwania informacje są zwracane, które informuje, typ następnego skoku i identyfikator trasy, które kierowało ruch. Dowiedz się więcej na temat typów następnego przeskoku wyświetlanych zwracane w [Omówienie routingu](virtual-networks-udr-overview.md).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network watcher show następnego przeskoku](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań na tabele tras i trasy, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                          |   Nazwa                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Odczyt tabeli tras                                    |
| Microsoft.Network/routeTables/write                             |   Utwórz lub zaktualizuj tabelę tras                        |
| Microsoft.Network/routeTables/delete                            |   Usuwanie tabeli tras                                  |
| Microsoft.Network/routeTables/join/action                       |   Kojarzenie tabeli tras z podsiecią                   |
| Microsoft.Network/routeTables/routes/read                       |   Przeczytaj trasy                                          |
| Microsoft.Network/routeTables/routes/write                      |   Utwórz lub zaktualizuj trasy                              |
| Microsoft.Network/routeTables/routes/delete                     |   Usuwanie trasy                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Pobierz tabelę skuteczną trasę dla interfejsu sieciowego |
| Microsoft.Network/networkWatchers/nextHop/action                |   Pobiera następny przeskok z maszyny Wirtualnej                           |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie tabeli tras przy użyciu [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
