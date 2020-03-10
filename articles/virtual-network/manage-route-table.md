---
title: Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć, zmienić lub usunąć tabelę tras.
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
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356655"
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie lub usuwanie tabeli tras

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić domyślny Routing systemu Azure, możesz to zrobić, tworząc tabelę tras. Jeśli jesteś nowym sposobem routingu w sieciach wirtualnych, możesz dowiedzieć się więcej na jego temat w [omówieniu routingu](virtual-networks-udr-overview.md) lub przez zakończenie korzystania z [samouczka](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

* Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).<br>
* Jeśli używasz portalu, Otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.<br>
* W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.<br>
* W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić `az login`, aby utworzyć połączenie z platformą Azure.

Konto, do którego chcesz się zalogować, lub Połącz się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje limit liczby tabel tras, które można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W lewym górnym rogu portalu wybierz pozycję **+ Utwórz zasób**.
1. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **tabela tras**.
1. Wprowadź **nazwę** tabeli tras, wybierz **subskrypcję**, Utwórz nową **grupę zasobów**lub wybierz istniejącą grupę zasobów, wybierz **lokalizację**, a następnie wybierz pozycję **Utwórz**. Jeśli planujesz skojarzenie tabeli tras z podsiecią z siecią wirtualną, która jest połączona z siecią lokalną za pośrednictwem bramy sieci VPN, i wyłączysz **propagację trasy bramy sieci wirtualnej**, trasy lokalne nie są propagowane do interfejsów sieciowych w podsieci.

### <a name="create-route-table---commands"></a>Tworzenie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Wyświetlanie tabel tras

W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je. Zostaną wyświetlone tabele tras, które istnieją w Twojej subskrypcji.

### <a name="view-route-table---commands"></a>Wyświetlanie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table list](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Wyświetlanie szczegółów tabeli tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz tabelę tras na liście, dla której chcesz wyświetlić szczegóły. W obszarze **Ustawienia**można wyświetlić **trasy** w tabeli tras i **podsieci** , z którymi jest skojarzona tabela tras.
1. Aby dowiedzieć się więcej o typowych ustawieniach platformy Azure, zobacz następujące informacje:

    * [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)<br>
    * [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Zamki](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Skrypt automatyzacji](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Wyświetl szczegóły trasy — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Zmiana tabeli tras

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz tabelę tras, którą chcesz zmienić. Najczęstszymi zmianami są [Dodawanie](#create-a-route) lub [usuwanie](#delete-a-route) tras oraz [kojarzenie](#associate-a-route-table-to-a-subnet) tabel tras z lub [skojarzenie](#dissociate-a-route-table-from-a-subnet) tabel tras z podsieciami.

### <a name="change-a-route-table---commands"></a>Zmienianie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Update](/cli/azure/network/route-table/route)<br>
* PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Podsieć może mieć skojarzoną jedną tabelę tras lub żadną. Tabela tras może być skojarzona z wieloma podsieciami lub żadną. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, musisz skojarzyć tabelę tras z każdą podsiecią, która ma mieć skojarzoną tabelę tras. Cały ruch wychodzący z podsieci jest kierowany na podstawie tras utworzonych w ramach tabel tras, [tras domyślnych](virtual-networks-udr-overview.md#default)i tras propagowanych z sieci lokalnej, jeśli sieć wirtualna jest połączona z bramą sieci wirtualnej platformy Azure (ExpressRoute lub sieci VPN). Tabelę tras można skojarzyć tylko z podsieciami w sieciach wirtualnych, które istnieją w tej samej lokalizacji i subskrypcji platformy Azure co tabela tras.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
1. Wybierz sieć wirtualną z listy zawierającej podsieć, do której chcesz skojarzyć tabelę tras.
1. W obszarze **Ustawienia**wybierz pozycję **podsieci** .
1. Wybierz podsieć, do której chcesz skojarzyć tabelę tras.
1. Wybierz pozycję **tabela tras**, wybierz tabelę tras, która ma zostać skojarzona z podsiecią, a następnie wybierz pozycję **Zapisz**.

Jeśli Twoja sieć wirtualna jest podłączona do bramy sieci VPN platformy Azure, nie należy kojarzyć tabeli tras z [podsiecią bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) zawierającą trasy z adresem docelowym 0.0.0.0/0. Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby uzyskać więcej informacji o używaniu 0.0.0.0/0 w trasie, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Kojarzenie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Po skojarzeniu tabeli tras z podsiecią platforma Azure kieruje ruchem na podstawie jego [domyślnych tras](virtual-networks-udr-overview.md#default).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
1. Wybierz sieć wirtualną zawierającą podsieć, z której chcesz usunąć skojarzenie tabeli tras.
1. W obszarze **Ustawienia**wybierz pozycję **podsieci** .
1. Wybierz podsieć, z której chcesz usunąć skojarzenie tabeli tras.
1. Wybierz pozycję **tabela tras**, wybierz pozycję **Brak**, a następnie wybierz pozycję **Zapisz**.

### <a name="dissociate-a-route-table---commands"></a>Usuwanie skojarzenia tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Jeśli tabela tras jest skojarzona z podsieciami, nie można jej usunąć. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabeli tras z wszystkimi podsieciami, zanim spróbujesz ją usunąć.

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz pozycję **...** znajdującą się po prawej stronie tabeli tras, która ma zostać usunięta.
1. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak**.

### <a name="delete-a-route-table---commands"></a>Usuwanie tabeli tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje ograniczenie, ile tras na tabelę tras można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz z listy tabelę tras, do której chcesz dodać trasę.
1. W obszarze **Ustawienia**wybierz pozycję **trasy**.
1. Wybierz pozycję **+ Dodaj**.
1. Wprowadź unikatową **nazwę** trasy w tabeli tras.
1. Wprowadź **prefiks adresu**w notacji CIDR, do której chcesz kierować ruchem. Prefiksu nie można zduplikować w więcej niż jednej trasie w tabeli tras, chociaż sam prefiks może być zawarty w innym prefiksie. Na przykład jeśli zdefiniowano 10.0.0.0/16 jako prefiks w jednej trasie, nadal można zdefiniować inną trasę z prefiksem adresu 10.0.0.0/24. Platforma Azure wybiera trasę dla ruchu na podstawie najdłuższych pasujących prefiksów. Aby dowiedzieć się więcej o tym, jak platforma Azure wybiera trasy, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Wybierz **Typ następnego przeskoku**. Aby uzyskać szczegółowy opis wszystkich typów następnego przeskoku, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
1. Wprowadź adres IP dla **adresu następnego przeskoku**. Adres można wprowadzić tylko w przypadku wybrania *urządzenia wirtualnego* dla **typu następnego przeskoku**.
1. Kliknij przycisk **OK**.

### <a name="create-a-route---commands"></a>Tworzenie trasy — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Route Create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Wyświetlanie tras

Tabela tras zawiera zero lub wiele tras. Aby dowiedzieć się więcej na temat informacji wyświetlanych podczas wyświetlania tras, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz z listy tabelę tras, dla której chcesz wyświetlić trasy.
1. Wybierz pozycję **trasy** w obszarze **Ustawienia**.

### <a name="view-routes---commands"></a>Wyświetlanie tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz tabelę tras, dla której chcesz wyświetlić szczegóły trasy.
1. Wybierz pozycję **trasy**.
1. Wybierz trasę, dla której chcesz wyświetlić szczegóły.

### <a name="view-details-of-a-route---commands"></a>Wyświetlanie szczegółów dotyczących tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Zmienianie trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz tabelę tras, dla której chcesz zmienić trasę.
1. Wybierz pozycję **trasy**.
1. Wybierz trasę, którą chcesz zmienić.
1. Zmień istniejące ustawienia na nowe ustawienia, a następnie wybierz pozycję **Zapisz**.

### <a name="change-a-route---commands"></a>Zmienianie trasy — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Route Update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Usuwanie trasy

1. W polu wyszukiwania w górnej części portalu wprowadź *tabele tras* w polu wyszukiwania. Gdy **tabele tras** są wyświetlane w wynikach wyszukiwania, wybierz je.
1. Wybierz tabelę tras, dla której chcesz usunąć trasę.
1. Wybierz pozycję **trasy**.
1. Z listy tras wybierz pozycję **...** po prawej stronie trasy, która ma zostać usunięta.
1. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak**.

### <a name="delete-a-route---commands"></a>Usuwanie trasy — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network Route-Table Route Delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Wyświetl efektywne trasy

Efektywne trasy dla każdego interfejsu sieciowego dołączonego do maszyny wirtualnej to kombinacja utworzonych tabel tras, domyślnych tras platformy Azure oraz wszelkich tras propagowanych z sieci lokalnych za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Zrozumienie efektywnych tras dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Efektywne trasy można wyświetlić dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić obowiązujące trasy. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyny wirtualne* w polu wyszukiwania. Gdy **maszyny wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je i wybierz maszynę wirtualną z listy.
1. W obszarze **Ustawienia**wybierz pozycję **Sieć** .
1. Wybierz nazwę interfejsu sieciowego.
1. Wybierz pozycję **efektywne trasy** w obszarze **Pomoc techniczna i rozwiązywanie problemów**.
1. Przejrzyj listę efektywnych tras, aby ustalić, czy istnieje prawidłowa trasa dla miejsca, do którego chcesz kierować ruchem. Dowiedz się więcej na temat typów następnego przeskoku, które są widoczne na tej liście w temacie [Omówienie routingu](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Wyświetlanie efektywnych tras — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network nic show-skuteczna-Route-Table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Weryfikowanie routingu między dwoma punktami końcowymi

Możesz określić typ następnego przeskoku między maszyną wirtualną i adresem IP innego zasobu platformy Azure, zasobem lokalnym lub zasobem w Internecie. Określanie routingu platformy Azure jest pomocne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, musisz mieć istniejące Network Watcher. Jeśli nie masz istniejącego Network Watcher, utwórz je, wykonując kroki z sekcji [Tworzenie wystąpienia Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. W polu wyszukiwania w górnej części portalu wprowadź *obserwatora sieci* w polu wyszukiwania. Gdy w wynikach wyszukiwania pojawi się nazwa **Network Watcher**, wybierz ją.
1. Wybierz kolejno pozycje **Następny przeskok** w obszarze **narzędzia diagnostyki sieci**.
1. Wybierz swoją **subskrypcję** i **grupę zasobów** źródłowej maszyny wirtualnej, z której chcesz sprawdzić poprawność routingu.
1. Wybierz **maszynę wirtualną**, **interfejs sieciowy** dołączony do maszyny wirtualnej i **źródłowy adres IP** przypisany do interfejsu sieciowego, z którego chcesz sprawdzić poprawność routingu.
1. Wprowadź **docelowy adres IP** , na który chcesz sprawdzić poprawność routingu.
1. Wybierz **Następny przeskok**.
1. Po krótkim czasie oczekiwania zwracane są informacje, które mówią, że typ następnego przeskoku i identyfikator trasy, która skierowała ruch. Dowiedz się więcej na temat typów następnego przeskoku, które są widoczne w temacie [Omówienie routingu](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Weryfikowanie routingu między dwoma punktami końcowymi — polecenia

* Interfejs wiersza polecenia platformy Azure: [AZ Network obserwator show-Next-przeskok](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące tabel tras i tras, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                          |   Name (Nazwa)                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Odczytaj tabelę tras                                    |
| Microsoft.Network/routeTables/write                             |   Tworzenie lub aktualizowanie tabeli tras                        |
| Microsoft.Network/routeTables/delete                            |   Usuwanie tabeli tras                                  |
| Microsoft.Network/routeTables/join/action                       |   Kojarzenie tabeli tras z podsiecią                   |
| Microsoft.Network/routeTables/routes/read                       |   Odczytaj trasę                                          |
| Microsoft.Network/routeTables/routes/write                      |   Utwórz lub zaktualizuj trasę                              |
| Microsoft.Network/routeTables/routes/delete                     |   Usuwanie trasy                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Pobierz obowiązującą tabelę tras dla interfejsu sieciowego |
| Microsoft.Network/networkWatchers/nextHop/action                |   Pobiera następny przeskok z maszyny wirtualnej                           |

## <a name="next-steps"></a>Następne kroki

* Tworzenie tabeli tras przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)<br>
* Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
