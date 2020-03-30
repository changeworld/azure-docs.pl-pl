---
title: Tworzenie, zmienianie lub usuwanie tabeli marszrut platformy Azure
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
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247065"
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie lub usuwanie tabeli marszruty

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić dowolną z domyślnych routingu platformy Azure, należy to zrobić, tworząc tabelę marszruty. Jeśli dopiero zaczynasz routing w sieciach wirtualnych, możesz dowiedzieć się więcej o tym w [routingu ruchu sieci wirtualnej](virtual-networks-udr-overview.md) lub wykonując [samouczek](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Następnie wykonaj jedno z tych zadań przed rozpoczęciem kroków w dowolnej sekcji tego artykułu:

- **Użytkownicy portalu:** Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

- **Użytkownicy programu PowerShell:** Uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Przeglądarka usługi Azure Cloud Shell znajdź listę rozwijana **Wybierz środowisko,** a następnie wybierz pozycję **PowerShell,** jeśli nie jest jeszcze zaznaczona.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom `Connect-AzAccount` również, aby utworzyć połączenie z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI):** uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchom interfejs wiersza polecenia z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom `az login` również, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do [roli współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje limit liczby tabel tras, które można utworzyć na lokalizację i subskrypcję platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Limity sieci — Usługa Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W menu [portalu azure](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wprowadź *tabelę Trasa*. Gdy w wynikach wyszukiwania pojawi się **tabela Trasa,** zaznacz ją.

1. Na stronie **Tabela Marszruta** wybierz pozycję **Utwórz**.

1. W oknie dialogowym **Tworzenie tabeli marszruty:**

    1. Wprowadź **nazwę** tabeli marszruty.
    1. Wybierz **subskrypcję**.
    1. Wybierz istniejącą **grupę zasobów** lub wybierz **pozycję Utwórz nowy,** aby utworzyć nową grupę zasobów.
    1. Wybierz **lokalizację**.
    1. Jeśli tabela marszruty ma być skojarzona z podsiecią w sieci wirtualnej połączonej z siecią lokalną za pośrednictwem bramy sieci VPN i nie chcesz propagować tras lokalnych do interfejsów sieciowych w podsieci, ustaw **propagację trasy bramy sieci wirtualnej** na **Wyłączone**.

1. Wybierz **pozycję Utwórz,** aby utworzyć nową tabelę tras.

### <a name="create-route-table---commands"></a>Tworzenie tabeli tras - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Wyświetlanie tabel tras

Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **tabele trasy**. Tabele tras, które istnieją w subskrypcji są wyświetlane.

### <a name="view-route-table---commands"></a>Wyświetlanie tabeli tras - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć tabela-tabela](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Tabela Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Wyświetlanie szczegółów tabeli tras

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras, dla której chcesz wyświetlić szczegóły.

1. Na stronie tabeli trasy w obszarze **Ustawienia**wyświetl **trasy** w tabeli trasy lub **Podsieci,** z którymi jest skojarzona tabela trasy.

Aby dowiedzieć się więcej o typowych ustawieniach platformy Azure, zobacz następujące informacje:

- [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md)
- [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
- [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skrypt usługi Automation](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Wyświetlanie szczegółów tabeli tras - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć tabela-tabela pokaż](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Tabela Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Zmienianie tabeli tras

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras, którą chcesz zmienić.

Najczęstszymi zmianami [jest dodawanie](#create-a-route) tras, [usuwanie](#delete-a-route) tras, [kojarzenie](#associate-a-route-table-to-a-subnet) tabel tras z podsieciami lub [odłączenie](#dissociate-a-route-table-from-a-subnet) tabel tras od podsieci.

### <a name="change-a-route-table---commands"></a>Zmienianie tabeli tras - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ aktualizacja tabeli tras sieciowych](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Tabela Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Opcjonalnie można skojarzyć tabelę marszruty z podsiecią. Tabela marszruty może być skojarzona z zerowymi lub większą liczeciami podsieci. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, należy skojarzyć tabelę marszrut z każdą podsiecią, z którą ma być skojarzona tabela tras. Platforma Azure kieruje cały ruch opuszczający podsieć na podstawie tras utworzonych w tabelach tras, [tras domyślnych](virtual-networks-udr-overview.md#default)i tras propagowanych z sieci lokalnej, jeśli sieć wirtualna jest połączona z bramą sieci wirtualnej platformy Azure (usługa ExpressRoute lub VPN). Tabelę tras można skojarzyć tylko z podsieciami w sieciach wirtualnych, które istnieją w tej samej lokalizacji i subskrypcji platformy Azure co tabela tras.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **sieć wirtualną**.

1. Na liście sieci wirtualnej wybierz sieć wirtualną zawierającą podsieć, z którą chcesz skojarzyć tabelę marszruty.

1. Na pasku menu sieci wirtualnej wybierz pozycję **Podsieci**.

1. Wybierz podsieć, z którą chcesz skojarzyć tabelę marszruty.

1. W **tabeli Marszruta**wybierz tabelę tras, którą chcesz skojarzyć z podsiecią.

1. Wybierz **pozycję Zapisz**.

Jeśli sieć wirtualna jest połączona z bramą sieci VPN platformy Azure, nie kojarz tabelę marszruty z [podsiecią bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) zawierającą trasę o docelowej *0.0.0.0/0.* Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby uzyskać więcej informacji na temat używania *0.0.0.0/0* w trasie, zobacz [Routing ruchu sieci wirtualnej](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Kojarzenie tabeli marszruty - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Po odłączeniu tabeli tras od podsieci platforma Azure kieruje ruch na podstawie [jego tras domyślnych](virtual-networks-udr-overview.md#default).

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **sieć wirtualną**.

1. Na liście sieci wirtualnej wybierz sieć wirtualną zawierającą podsieć, z której chcesz odłączyć tabelę marszruty.

1. Na pasku menu sieci wirtualnej wybierz pozycję **Podsieci**.

1. Wybierz podsieć, z której chcesz odłączyć tabelę tras.

1. W **tabeli Trasa**wybierz pozycję **Brak**.

1. Wybierz **pozycję Zapisz**.

### <a name="dissociate-a-route-table---commands"></a>Odłączyć tabelę trasy - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Nie można usunąć tabeli marszruty skojarzonej z żadnymi podsieciami. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabeli tras z wszystkimi podsieciami, zanim spróbujesz ją usunąć.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras, którą chcesz usunąć.

1. Wybierz **pozycję Usuń**, a następnie wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

### <a name="delete-a-route-table---commands"></a>Usuwanie tabeli marszruty - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ sieć -tabela-tabela usunąć](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Usuń-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje limit liczby tras na tabelę tras można utworzyć na lokalizację platformy Azure i subskrypcji. Aby uzyskać szczegółowe informacje, zobacz [Limity sieci — Usługa Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras, do której chcesz dodać trasę.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy** > **Dodaj**.

1. Wprowadź unikatową **nazwę trasy** dla trasy w tabeli trasy.

1. Wprowadź **prefiks adresu**w notacji CIDR (Classless Inter-Domain Routing), do którego chcesz kierować ruch. Prefiksu nie można zduplikować w więcej niż jednej trasie w tabeli trasy, chociaż prefiks może znajdować się w innym prefiksie. Jeśli na przykład zdefiniowano *10.0.0.0/16* jako prefiks w jednej trasie, nadal można zdefiniować inną trasę z prefiksem adresu *10.0.0.0/22.* Platforma Azure wybiera trasę dla ruchu na podstawie najdłuższego dopasowania prefiksu. Aby dowiedzieć się więcej, zobacz [Jak platforma Azure wybiera trasę](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Wybierz **następny typ przeskoku**. Aby dowiedzieć się więcej o następnych typach przeskoku, zobacz [Routing ruchu sieci wirtualnej](virtual-networks-udr-overview.md).

1. Jeśli wybrano **następny typ przeskoku** **urządzenia wirtualnego,** wprowadź adres IP dla **następnego adresu przeskoku**.

1. Kliknij przycisk **OK**.

### <a name="create-a-route---commands"></a>Tworzenie trasy - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Wyświetlanie tras

Tabela tras zawiera zero lub więcej tras. Aby dowiedzieć się więcej o informacjach wymienionych podczas wyświetlania tras, zobacz [Routing ruchu sieci wirtualnej](virtual-networks-udr-overview.md).

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras, dla której chcesz wyświetlić trasy.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

### <a name="view-routes---commands"></a>Wyświetlanie tras - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [lista tras tabeli tras sieciowych az](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras zawierającą trasę, dla której chcesz wyświetlić szczegóły.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

1. Wybierz trasę, o której chcesz wyświetlić szczegóły.

### <a name="view-details-of-a-route---commands"></a>Wyświetlanie szczegółów trasy - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć trasy tabeli trasy pokaż](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Zmienianie trasy

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabel tras wybierz tabelę tras zawierającą trasę, którą chcesz zmienić.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

1. Wybierz trasę, którą chcesz zmienić.

1. Zmień istniejące ustawienia na nowe ustawienia, a następnie wybierz pozycję **Zapisz**.

### <a name="change-a-route---commands"></a>Zmienianie trasy - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ aktualizacja trasy tabeli tras sieciowych](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Zestaw-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Usuwanie trasy

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelami tras. Wyszukaj i wybierz **tabele trasy**.

1. Na liście tabeli tras wybierz tabelę tras zawierającą trasę, którą chcesz usunąć.

1. Na pasku menu tabeli tras wybierz pozycję **Trasy,** aby wyświetlić listę tras.

1. Wybierz trasę, którą chcesz usunąć.

1. Wybierz **pozycję Usuń**, a następnie wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

### <a name="delete-a-route---commands"></a>Usuwanie trasy - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ sieć trasy tabeli usuwania](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Usuń-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Wyświetlanie efektywnych tras

Efektywne trasy dla każdego interfejsu sieciowego podłączonego do maszyny Wirtualnej to kombinacja utworzonych tabel tras, domyślnych tras platformy Azure i wszelkich tras propagowanych z sieci lokalnych za pośrednictwem protokołu Brama graniczna (BGP) za pośrednictwem sieci wirtualnej platformy Azure Bramy. Zrozumienie skutecznych tras dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Można wyświetlić efektywne trasy dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny Wirtualnej.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać maszynami wirtualnymi. Wyszukaj i wybierz **maszyny wirtualne**.

1. Na liście maszyny wirtualnej wybierz maszynę wirtualną, dla której chcesz wyświetlić efektywne trasy.

1. Na pasku menu maszyny Wirtualnej wybierz pozycję **Sieć**.

1. Wybierz nazwę interfejsu sieciowego.

1. Na pasku menu interfejsu sieciowego wybierz pozycję **Efektywne trasy**.

1. Przejrzyj listę skutecznych tras, aby sprawdzić, czy istnieje poprawna trasa dla miejsca, do którego chcesz kierować ruch. Dowiedz się więcej o następnych typach przeskoku widocznych na tej liście w [trybie Routingu ruchu sieci wirtualnej](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Wyświetlanie tras efektywnych - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Sprawdzanie poprawności routingu między dwoma punktami końcowymi

Można określić następny typ przeskoku między maszyną wirtualną a adresem IP innego zasobu platformy Azure, zasobu lokalnego lub zasobu w Internecie. Określanie routingu platformy Azure jest przydatne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, musisz mieć istniejącego obserwatora sieciowego. Jeśli nie masz już obserwatora sieci, utwórz go, wykonując czynności opisane w instancji [Utwórz obserwatora sieci.](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać obserwatorami sieci. Wyszukaj i wybierz **pozycję Obserwator sieci**.

1. Na pasku menu obserwatora sieci wybierz pozycję **Następny przeskok**.

1. W **obserwatorze sieci | Następna strona przeskoku:**

    1. Wybierz **subskrypcję** i **grupę Zasobów** źródłowej maszyny Wirtualnej, z której chcesz sprawdzić poprawność routingu.

    1. Wybierz **maszynę wirtualną** i **interfejs sieciowy,** który jest dołączony do maszyny Wirtualnej.
    
    1. Wprowadź **źródłowy adres IP** przypisany do interfejsu sieciowego, z którego chcesz sprawdzić poprawność routingu.

    1. Wprowadź **docelowy adres IP,** do którego chcesz sprawdzić poprawność routingu.

1. Wybierz **następny przeskok**.

Po krótkim oczekiwaniu platforma Azure informuje o następnym typie przeskoku i identyfikatorze trasy, która skierowała ruch. Dowiedz się więcej o następnych typach przeskoku, które są widoczne, zwracane w [routingu ruchu sieci wirtualnej](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Sprawdzanie poprawności routingu między dwoma punktami końcowymi - polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w tabelach i trasach tras, konto musi być przypisane do [roli współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która przypisuje odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                                          |   Nazwa                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Czytanie tabeli marszruty                                    |
| Microsoft.Network/routeTables/write                             |   Tworzenie lub aktualizowanie tabeli marszruty                        |
| Microsoft.Network/routeTables/delete                            |   Usuwanie tabeli tras                                  |
| Microsoft.Network/routeTables/join/action                       |   Kojarzenie tabeli tras z podsiecią                   |
| Microsoft.Network/routeTables/routes/read                       |   Czytanie trasy                                          |
| Microsoft.Network/routeTables/routes/write                      |   Tworzenie lub aktualizowanie trasy                              |
| Microsoft.Network/routeTables/routes/delete                     |   Usuwanie trasy                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Pobierz tabelę efektywnej trasy dla interfejsu sieciowego |
| Microsoft.Network/networkWatchers/nextHop/akcja                |   Pobiera następny przeskok z maszyny Wirtualnej                           |

## <a name="next-steps"></a>Następne kroki

- Tworzenie tabeli marszruty przy użyciu przykładowych skryptów interfejsu wiersza polecenia programu [PowerShell](powershell-samples.md) lub [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub [szablonów usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
