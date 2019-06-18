---
title: Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie tabeli tras.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849801"
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie lub usuwanie tabeli tras

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić domyślne platformy Azure routing, możesz to zrobić, tworząc tabelę tras. Jeśli jesteś nowym użytkownikiem routingu w sieciach wirtualnych, możesz uzyskać więcej informacji w [Omówienie routingu](virtual-networks-udr-overview.md) lub wykonując [samouczek](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

* Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).<br>
* Jeśli przy użyciu portalu, otwórz https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.<br>
* Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.<br>
* Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje limit liczby tabel tras, które można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W lewym górnym rogu portalu, wybierz **+ Utwórz zasób**.
1. Wybierz **sieć**, a następnie wybierz **tabeli tras**.
1. Wprowadź **nazwa** tabeli tras można wybrać usługi **subskrypcji**, Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę zasobów, wybierz pozycję **lokalizacji** , a następnie wybierz **Utwórz**. Jeśli planujesz kojarzenie tabeli tras do podsieci w sieci wirtualnej, która jest połączona z siecią lokalną za pośrednictwem bramy sieci VPN i wyłączysz **propagacji trasy bramy sieci wirtualnej**, nie są trasy w środowisku lokalnym propagowane do interfejsów sieciowych w podsieci.

### <a name="create-route-table---commands"></a>Tworzenie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [Tworzenie tabeli tras az sieci](/cli/azure/network/route-table/route)<br>
* Program PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Wyświetl tabele tras

W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją. Tabele tras, które istnieją w Twojej subskrypcji są wyświetlane.

### <a name="view-route-table---commands"></a>Widok tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [listy tabelę tras sieciowych az](/cli/azure/network/route-table/route)<br>
* Program PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Wyświetl szczegóły tabelę tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz tabelę tras, na liście, który chcesz wyświetlić szczegóły. W obszarze **ustawienia**, można wyświetlić **trasy** w tabeli tras i **podsieci** tabelę tras jest skojarzona z.
1. Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące informacje:

    * [Dziennik aktywności](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)<br>
    * [Tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Skrypt automatyzacji](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>Wyświetl szczegóły tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [az sieci route-table show](/cli/azure/network/route-table/route)<br>
* Program PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Zmień tabelę tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybieranie tabeli tras, które chcesz zmienić. Najbardziej typowe zmiany są [Dodawanie](#create-a-route) lub [usuwanie](#delete-a-route) tras i [kojarzenie](#associate-a-route-table-to-a-subnet) tabele, tras lub [skojarzenia](#dissociate-a-route-table-from-a-subnet) tabele z tras podsieci.

### <a name="change-a-route-table---commands"></a>Zmień tabelę tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [az sieci route-table update](/cli/azure/network/route-table/route)<br>
* Program PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Podsieć może mieć skojarzoną jedną tabelę tras lub żadną. Tabela tras może być skojarzona z wieloma podsieciami lub żadną. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, musisz skojarzyć tabelę tras z każdą podsiecią, która ma mieć skojarzoną tabelę tras. Cały ruch, pozostawiając podsieci jest kierowany oparte na trasach, został utworzony w tabelach tras, [domyślne trasy](virtual-networks-udr-overview.md#default), i propagowane trasy z siecią lokalną, jeśli sieć wirtualna jest połączona z (Brama sieci wirtualnej platformy Azure Usługa ExpressRoute lub sieci VPN). Tabelę tras można skojarzyć tylko z podsieciami w sieciach wirtualnych, które istnieją w tej samej lokalizacji i subskrypcji platformy Azure co tabela tras.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz sieć wirtualną na liście, który zawiera podsieć, którą chcesz skojarzyć tabelę tras w celu.
1. Wybierz **podsieci** w obszarze **ustawienia**.
1. Wybierz podsieć, którą chcesz skojarzyć tabelę tras w celu.
1. Wybierz **tabeli tras**, wybierz tabeli tras, które chcesz skojarzyć z podsiecią, a następnie wybierz **Zapisz**.

Jeśli Twoja sieć wirtualna jest podłączona do bramy sieci VPN platformy Azure, nie należy kojarzyć tabeli tras z [podsiecią bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) zawierającą trasy z adresem docelowym 0.0.0.0/0. Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby uzyskać więcej informacji o używaniu 0.0.0.0/0 w trasie, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Kojarzenie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* Program PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Jeśli usuniesz skojarzenie elementu tabelę tras z podsieci, platforma Azure kieruje ruch na podstawie jego [domyślne trasy](virtual-networks-udr-overview.md#default).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz sieć wirtualną, która zawiera podsieci, na pewno chcesz usunąć skojarzenie tabelę tras z.
1. Wybierz **podsieci** w obszarze **ustawienia**.
1. Wybierz podsieć, na pewno chcesz usunąć skojarzenie tabeli tras z.
1. Wybierz **tabeli tras**, wybierz opcję **Brak**, a następnie wybierz **Zapisz**.

### <a name="dissociate-a-route-table---commands"></a>Usuń skojarzenie tabelę tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* Program PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Jeśli tabela tras jest skojarzona z podsieciami, nie można jej usunąć. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabeli tras z wszystkimi podsieciami, zanim spróbujesz ją usunąć.

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz **...**  po prawej stronie tabeli tras do usunięcia.
1. Wybierz **Usuń**, a następnie wybierz pozycję **tak**.

### <a name="delete-a-route-table---commands"></a>Usuwanie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [usunąć tabeli tras az sieci](/cli/azure/network/route-table/route)<br>
* Program PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje limit liczby trasy na tabelę tras można utworzyć dla lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz tabelę tras z listy, który chcesz dodać trasę do.
1. Wybierz **trasy**w obszarze **ustawienia**.
1. Wybierz pozycję **+ Dodaj**.
1. Wprowadź unikatową **nazwa** trasy w tabeli tras.
1. Wprowadź **prefiksu adresu**, w notacji CIDR, który chcesz kierowanie ruchu do. Prefiksu nie można zduplikować w więcej niż jednej trasie w tabeli tras, chociaż sam prefiks może być zawarty w innym prefiksie. Na przykład jeśli 10.0.0.0/16 jest zdefiniowany jako prefiks w trasie jeden, nadal można zdefiniować innej trasy z prefiksem adresu 10.0.0.0/24. Platforma Azure wybiera trasę ruchu w oparciu o dopasowanie najdłuższego prefiksu. Aby dowiedzieć się, jak platforma Azure wybiera trasy, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Wybierz **typu następnego przeskoku**. Aby uzyskać szczegółowy opis wszystkich typów następnego przeskoku, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
1. Wprowadź adres IP dla **następnego przeskoku**. Adres można wprowadzić tylko w przypadku wybrania *urządzenie wirtualne* dla **typu następnego przeskoku**.
1. Kliknij przycisk **OK**.

### <a name="create-a-route---commands"></a>Tworzona jest trasa — polecenia

* Interfejs wiersza polecenia platformy Azure: [tworzenie az sieci route-table route](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Program PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Wyświetl trasy

Tabela tras zawiera zero lub wiele tras. Aby dowiedzieć się więcej o informacje podane podczas wyświetlania tras, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz tabelę tras z listy, który chcesz wyświetlić trasy dla.
1. Wybierz **trasy** w obszarze **ustawienia**.

### <a name="view-routes---commands"></a>Wyświetl tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [listę trasy w tabeli tras sieci az](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Program PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz tabelę tras, którą chcesz wyświetlić szczegółowe informacje o trasie dla.
1. Wybierz **trasy**.
1. Wybierz trasy, aby wyświetlić szczegóły.

### <a name="view-details-of-a-route---commands"></a>Wyświetl szczegóły trasę — polecenia

* Interfejs wiersza polecenia platformy Azure: [az sieci route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Program PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Zmień trasę

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz tabelę tras, aby zmienić trasę dla.
1. Wybierz **trasy**.
1. Wybierz trasy, który chcesz zmienić.
1. Zmień istniejące ustawienia do nowych ustawień, a następnie wybierz **Zapisz**.

### <a name="change-a-route---commands"></a>Zmień trasę — polecenia

* Interfejs wiersza polecenia platformy Azure: [az sieci route-table route aktualizacji](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Program PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Usuwanie trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
1. Wybierz tabelę tras, aby usunąć trasę dla.
1. Wybierz **trasy**.
1. Wybierz z listy tras, **...**  po prawej stronie trasy do usunięcia.
1. Wybierz **Usuń**, a następnie wybierz **tak**.

### <a name="delete-a-route---commands"></a>Usuń trasę — polecenia

* Interfejs wiersza polecenia platformy Azure: [az sieci tabeli tras route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Program PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Wyświetlanie obowiązujących tras

Skuteczne trasy dla każdego interfejsu sieciowego dołączonych do maszyny wirtualnej są kombinacją tabel tras, które zostały utworzone, domyślnych tras platformy i wszystkie trasy propagowane z sieciami lokalnymi za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Opis obowiązujące trasy dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Można wyświetlić obowiązujące trasy dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, którą chcesz wyświetlić obowiązujące trasy dla. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją i wybierz maszynę wirtualną z listy.
1. Wybierz **sieć** w obszarze **ustawienia**.
1. Wybierz nazwę interfejsu sieciowego.
1. Wybierz **obowiązujące trasy** w obszarze **pomoc techniczna i rozwiązywanie problemów**.
1. Przejrzyj listę skuteczne trasy w celu ustalenia, czy dla której chcesz przekierować ruch do istnieje poprawnej trasy. Dowiedz się więcej na temat typów następnego przeskoku widoczni na tej liście [Omówienie routingu](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Wyświetlanie obowiązujących tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [az network nic show obowiązywać route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* Program PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Sprawdź poprawność routingu między dwoma punktami końcowymi

Można określić typ następnego przeskoku między maszyną wirtualną i adres IP innego zasobu platformy Azure, lokalnych zasobów lub zasobu w Internecie. Określanie Azure routing jest przydatne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, konieczne jest posiadanie istniejącej usługi Network Watcher. Jeśli nie masz istniejącej usługi Network Watcher, utwórz ją, wykonując kroki opisane w [Tworzenie wystąpienia usługi Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. W polu wyszukiwania w górnej części portalu wprowadź *usługi network watcher* w polu wyszukiwania. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
1. Wybierz **następnego przeskoku** w obszarze **narzędzia diagnostyczne sieci**.
1. Wybierz swoje **subskrypcji** i **grupy zasobów** źródłowej maszyny wirtualnej, którą chcesz zweryfikować, routing od.
1. Wybierz **maszyny wirtualnej**, **interfejs sieciowy** dołączonych do maszyny wirtualnej i **źródłowy adres IP** przypisany do interfejsu sieciowego, który chcesz zweryfikować Routing od.
1. Wprowadź **docelowy adres IP** , którą chcesz zweryfikować routingu w celu.
1. Wybierz **następnego przeskoku**.
1. Po krótkim czasie oczekiwania informacje są zwracane, które informuje, typ następnego skoku i identyfikator trasy, które kierowało ruch. Dowiedz się więcej na temat typów następnego przeskoku wyświetlanych zwracane w [Omówienie routingu](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Sprawdź poprawność routingu między dwoma punktami końcowymi — polecenia

* Interfejs wiersza polecenia platformy Azure: [az network watcher show następnego przeskoku](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* Program PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań na tabele tras i trasy, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                          |   Name (Nazwa)                                                  |
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

* Tworzenie tabeli tras przy użyciu [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)<br>
* Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
