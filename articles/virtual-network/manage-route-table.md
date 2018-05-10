---
title: Tworzenie, zmienianie lub usuwanie tabeli tras | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć, zmienić lub usunąć tabeli tras.
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
ms.openlocfilehash: 065ac8b2e9cb48408c7922a1937e541521ccd8cf
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie lub usuwanie tabeli tras

Azure automatycznie kieruje ruchem między podsieciami Azure, sieci wirtualnych i sieciami lokalnymi. Jeśli chcesz zmienić domyślne Azure routingu, możesz to zrobić, tworząc tabelę tras. Jeśli jesteś nowym użytkownikiem routingu w sieci wirtualnych, możesz dowiedzieć się więcej o w [Omówienie routingu](virtual-networks-udr-overview.md) lub wykonując [samouczek](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

Konta, zaloguj się do lub z usługą Azure, musi być przypisany do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania na liście [uprawnień ](#permissions).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje limit liczby tabel tras, można utworzyć dla każdej lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W lewym górnym rogu portalu, wybierz **+ Utwórz zasób**.
2. Wybierz **sieci**, a następnie wybierz pozycję **tabeli tras**.
3. Wprowadź **nazwa** dla tabeli tras, wybierz użytkownika **subskrypcji**, Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę zasobów, wybierz pozycję **lokalizacji** , a następnie wybierz pozycję **Utwórz**. **Propagowania tras Wyłącz protokół BGP** opcja zapobiega o lokalnych trasach propagowanie za pośrednictwem protokołu BGP do interfejsów sieciowych w żadnej podsieci, która jest skojarzona tabela tras. Jeśli w Twojej sieci wirtualnej nie jest podłączony do bramy sieci platformy Azure (sieci VPN lub ExpressRoute), pozostaw tę opcję *wyłączone*.

**Polecenia**

- Azure CLI: [utworzyć tabelę tras az sieci](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Wyświetlanie tabel tras

W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją. Wymieniono tabele tras, które istnieją w Twojej subskrypcji.

**Polecenia**

- Azure CLI: [listy tabelę tras sieciowych az](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Wyświetl szczegóły tabelę tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz z listy, który chcesz wyświetlić szczegóły dotyczące tabeli tras. W obszarze **ustawienia**, można wyświetlić **tras** w tabeli tras i **podsieci** jest skojarzona tabela tras.
3. Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące informacje:
    *   [Dziennik aktywności](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Kontrola dostępu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skrypt automatyzacji](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Polecenia**

- Azure CLI: [Pokaż tabelę tras sieciowych az](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Zmień tabelę tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, które chcesz zmienić. Najbardziej typowe zmiany są [Dodawanie](#create-a-route) lub [usuwanie](#delete-a-route) tras i [kojarzenie](#associate-a-route-table-to-a-subnet) tabel, tras lub [usunięciu](#dissociate-a-route-table-from-a-subnet) tabel z tras podsieci.

**Polecenia**

- Azure CLI: [zaktualizować tabeli tras sieci az](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Podsieć może mieć zero lub jedną tabelę tras skojarzony. Tabela tras może być skojarzona z zero lub wiele podsieci. Ponieważ tabel tras nie są skojarzone z siecią wirtualną, należy skojarzyć tabelę tras do każdej podsieci ma powiązanych z tabeli tras. Wszystkie ruchu wychodzącego do podsieci jest kierowany oparte na trasach utworzonego w ramach tabel tras [domyślne trasy](virtual-networks-udr-overview.md#default), i propagowane trasy z sieci lokalnej, jeśli sieci wirtualnej jest podłączona do sieci wirtualnej platformy Azure (bramy ExpressRoute lub sieci VPN, jeśli za pomocą protokołu BGP dla bramy sieci VPN). Można skojarzyć tylko tabelę tras do podsieci w sieci wirtualnych, które istnieją w tej samej lokalizacji platformy Azure i subskrypcji jako tabeli tras.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz z listy, które podsieci ma zostać skojarzona tabela tras, która ma sieci wirtualnej.
3. Wybierz **podsieci** w obszarze **ustawienia**.
4. Wybierz podsieć, chcesz skojarzyć tabeli tras.
5. Wybierz **tabeli tras**, wybierz tabelę routingu, które chcesz skojarzyć z podsiecią, a następnie wybierz **zapisać**.

**Polecenia**

- Azure CLI: [zaktualizować podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuń skojarzenie tabelę tras z podsiecią

Jeśli usuniesz skojarzenie tabelę tras z podsiecią, Azure kieruje ruchem na podstawie jego [domyślne trasy](virtual-networks-udr-overview.md#default).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieć wirtualną, która zawiera podsieć chcesz usunąć skojarzenie z tabeli tras.
3. Wybierz **podsieci** w obszarze **ustawienia**.
4. Wybierz podsieć, aby usunąć skojarzenie z tabeli tras.
5. Wybierz **tabeli tras**, wybierz pozycję **Brak**, a następnie wybierz pozycję **zapisać**.

**Polecenia**

- Azure CLI: [zaktualizować podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Usuń tabelę tras

Jeśli tabelę tras jest skojarzony z żadnych podsieci, nie można usunąć. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabelę tras z wszystkich podsieci przed próbą usunięcia go.

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz **...**  w prawej tabeli tras do usunięcia.
3. Wybierz **usunąć**, a następnie wybierz **tak**.

**Polecenia**

- Azure CLI: [usunąć tabeli tras az sieci](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje limit liczby tras na tabelę tras można utworzyć dla każdej lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras z listy, który chcesz dodać trasę do.
3. Wybierz **tras**w obszarze **ustawienia**.
4. Wybierz pozycję **+ Dodaj**.
5. Wprowadź unikatową **nazwa** trasy w tabeli tras.
6. Wprowadź **prefiks adresu**, w notacji CIDR, który ma zostać kierować ruchem do. Nie można zduplikować prefiks w więcej niż jedną trasę w tabeli tras, chociaż prefiks mogą być zawarte w innym prefiks. Na przykład jeśli 10.0.0.0/16 jest zdefiniowany jako prefiksu w jedną trasę, nadal można zdefiniować innej trasy z prefiksem adresu 10.0.0.0/24. Azure wybiera trasę dla ruchu w oparciu o najdłuższe dopasowanie prefiksu. Aby dowiedzieć się więcej na temat sposobu Azure wybierania tras, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Wybierz **następnego przeskoku typu**. Aby uzyskać szczegółowy opis wszystkich typów następnego przeskoku, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
8. Wprowadź adres IP dla **następnego przeskoku**. Adres można wprowadzić tylko w przypadku wybrania *urządzenie wirtualne* dla **następnego przeskoku typu**.
9. Kliknij przycisk **OK**.

**Polecenia**

- Azure CLI: [utwórz trasę tabeli tras sieciowych az](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Widok tras

Tabela tras zawiera zero lub wiele tras. Aby dowiedzieć się więcej o informacje podane podczas wyświetlania tras, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras z listy, który chcesz wyświetlić trasy dla.
3. Wybierz **tras** w obszarze **ustawienia**.

**Polecenia**

- Azure CLI: [listy trasy tabeli tras sieciowych az](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Wyświetl szczegóły trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, który chcesz wyświetlić szczegółowe informacje o trasie dla.
3. Wybierz **tras**.
4. Wybierz trasy, który chcesz wyświetlić szczegóły.

**Polecenia**

- Azure CLI: [Pokaż trasy tabeli tras sieciowych az](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Zmień trasę

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz tabelę tras, aby zmienić trasę dla.
3. Wybierz **tras**.
4. Wybierz trasy, który chcesz zmienić.
5. Zmień istniejące ustawienia nowe ustawienia, a następnie wybierz **zapisać**.

**Polecenia**

- Azure CLI: [az sieci tabeli tras trasy aktualizacji](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Usuń trasę

1. W polu wyszukiwania w górnej części portalu wprowadź *tabel tras* w polu wyszukiwania. Gdy **tabel tras** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz chcesz usunąć trasę dla tabeli tras.
3. Wybierz **tras**.
4. Wybierz z listy tras, **...**  po prawej stronie trasy do usunięcia.
5. Wybierz **usunąć**, a następnie wybierz pozycję **tak**.

**Polecenia**

- Azure CLI: [az sieci tabeli tras trasy usunięcie](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Widok skuteczne tras

Skuteczne trasy dla każdego interfejsu sieciowego dołączony do maszyny wirtualnej są kombinacją tabele tras, które zostały utworzone, trasy domyślne platformy Azure i wszelkie tras propagowane z lokalnymi sieciami za pomocą protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Opis wprowadzenia trasy dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Można wyświetlić skuteczne trasy dla dowolnego interfejsu sieciowego, który jest podłączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę chcesz wyświetlić skuteczne trasy dla maszyny wirtualnej. Jeśli nie znasz nazwę maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, zaznacz go i wybierz maszynę wirtualną z listy.
2. Wybierz **sieci** w obszarze **ustawienia**.
3. Wybierz nazwę karty sieciowej.
4. Wybierz **skuteczne tras** w obszarze **pomocy technicznej i rozwiązywania problemów**.
5. Zapoznaj się z listą skuteczne trasy do ustalenia, czy trasa poprawne istnieje dla której chcesz kierować ruchem do. Więcej informacji na temat typów następnego przeskoku, które są wyświetlane na liście w [Omówienie routingu](virtual-networks-udr-overview.md).

**Polecenia**

- Azure CLI: [az sieci karty sieciowej Pokaż obowiązującej--tabeli tras](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Sprawdź poprawność routingu między dwoma punktami końcowymi

Można określić typ następnego przeskoku między maszyną wirtualną i adresu IP innego zasobów platformy Azure, zasób lokalną lub zasobów w Internecie. Określanie Azure routingu jest przydatne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, musi mieć istniejący obserwatora sieciowego. Jeśli nie masz istniejących obserwatora sieciowego, utwórz go, wykonując kroki opisane w [utworzyć wystąpienia obserwatora sieciowego](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. W polu wyszukiwania w górnej części portalu wprowadź *obserwatora sieciowego* w polu wyszukiwania. Gdy **obserwatora sieciowego** pojawia się w wynikach wyszukiwania, wybierz go.
2. Wybierz **następnego przeskoku** w obszarze **narzędzia diagnostyczne sieci**.
3. Wybierz użytkownika **subskrypcji** i **grupy zasobów** dla źródłowej maszyny wirtualnej chcesz zweryfikować routingu z.
4. Wybierz **maszyny wirtualnej**, **interfejsu sieciowego** dołączony do maszyny wirtualnej i **źródłowy adres IP** przypisany do interfejsu sieciowego, który chcesz zweryfikować Routing z.
5. Wprowadź **docelowy adres IP** , który chcesz zweryfikować routingu do.
6. Wybierz **następnego przeskoku**.
7. Po krótki czas oczekiwania informacje są zwracane, które informuje, typ następnego przeskoku i identyfikator kierowany ruch trasy. Więcej informacji na temat typów następnego przeskoku widoczne zwracane w [Omówienie routingu](virtual-networks-udr-overview.md).

**Polecenia**

- Azure CLI: [az sieci obserwatora Pokaż następnego przeskoku](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Uprawnienia

Do wykonania zadań dotyczących tras i tabele tras, Twoje konto musi mieć przypisaną do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rola przypisana odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                                          |   Name (Nazwa)                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Odczytać tabeli tras                                    |
| Microsoft.Network/routeTables/write                             |   Utwórz lub zaktualizuj tabelę tras                        |
| Microsoft.Network/routeTables/delete                            |   Usuń tabelę tras                                  |
| Microsoft.Network/routeTables/join/action                       |   Kojarzenie tabeli tras z podsiecią                   |
| Microsoft.Network/routeTables/routes/read                       |   Trasy do odczytu                                          |
| Microsoft.Network/routeTables/routes/write                      |   Utwórz lub zaktualizuj trasę                              |
| Microsoft.Network/routeTables/routes/delete                     |   Usuń trasę                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Pobierz obowiązująca tabela routingu dla interfejsu sieciowego |
| Microsoft.Network/networkWatchers/nextHop/action                |   Pobiera następnego przeskoku z maszyny Wirtualnej                           |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie tabeli tras przy użyciu [PowerShell](powershell-samples.md) lub [interfejsu wiersza polecenia Azure](cli-samples.md) przykładowe skrypty lub przy użyciu usługi Azure [szablonów Resource Manager](template-samples.md)
- Tworzenie i stosowanie [Azure zasad](policy-samples.md) dla sieci wirtualnych
