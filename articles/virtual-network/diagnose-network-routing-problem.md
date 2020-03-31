---
title: Diagnozowanie problemu z routingiem maszyn wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak zdiagnozować problem routingu maszyny wirtualnej, wyświetlając efektywne trasy dla maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350602"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnozowanie problemu z routingiem maszyny wirtualnej

W tym artykule dowiesz się, jak zdiagnozować problem routingu, wyświetlając trasy, które są skuteczne dla interfejsu sieciowego na maszynie wirtualnej (VM). Platforma Azure tworzy kilka tras domyślnych dla każdej podsieci sieci wirtualnej. Domyślne trasy platformy Azure można zastąpić, definiując trasy w tabeli tras, a następnie kojarząc tabelę tras z podsiecią. Połączenie utworzonych tras, domyślne trasy platformy Azure i wszelkie trasy propagowane z sieci lokalnej za pośrednictwem bramy sieci VPN platformy Azure (jeśli sieć wirtualna jest połączona z siecią lokalną) za pośrednictwem protokołu bramy granicznej (BGP), są efektywnych tras dla wszystkich interfejsów sieciowych w podsieci. Jeśli nie znasz pojęcia dotyczące sieci wirtualnej, interfejsu sieciowego lub routingu, zobacz [Omówienie sieci wirtualnej](virtual-networks-overview.md), [Interfejs sieci](virtual-network-network-interface.md)i [Omówienie routingu](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenariusz

Próba nawiązania połączenia z maszyną wirtualną, ale połączenie nie powiedzie się. Aby ustalić, dlaczego nie można połączyć się z maszyną wirtualną, można wyświetlić efektywne trasy dla interfejsu sieciowego przy użyciu [portalu Azure,](#diagnose-using-azure-portal) [programu PowerShell](#diagnose-using-powershell)lub [interfejsu wiersza polecenia platformy Azure.](#diagnose-using-azure-cli)

Kroki, które należy wykonać założyć, że masz istniejącą maszynę wirtualną, aby wyświetlić skuteczne trasy dla. Jeśli nie masz istniejącej maszyny Wirtualnej, najpierw wdrożyć [maszynę](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) wirtualną z systemem Linux lub [Windows,](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby wykonać zadania w tym artykule. Przykłady w tym artykule są dla maszyny Wirtualnej o nazwie *myVM* z interfejsem sieciowym o nazwie *myVMNic1*. Maszyna wirtualna i interfejs sieciowy znajdują się w grupie zasobów o nazwie *myResourceGroup*i znajdują się w regionie *Wschodnie stany USA.* Zmień wartości w krokach, w stosownych przypadkach, dla maszyny Wirtualnej, dla której diagnozujesz problem.

## <a name="diagnose-using-azure-portal"></a>Diagnozowanie przy użyciu witryny Azure portal

1. Zaloguj się do [witryny](https://portal.azure.com) Azure portal przy użyciu konta platformy Azure, które ma [niezbędne uprawnienia.](virtual-network-network-interface.md#permissions)
2. W górnej części witryny Azure portal wprowadź nazwę maszyny Wirtualnej, która jest w stanie uruchomionym, w polu wyszukiwania. Gdy w wynikach wyszukiwania pojawi się nazwa maszyny Wirtualnej, zaznacz ją.
3. W obszarze **Ustawienia** po lewej stronie wybierz pozycję **Sieć**i przejdź do zasobu interfejsu sieciowego, wybierając jego nazwę.
     ![Wyświetlanie interfejsów sieciowych](./media/diagnose-network-routing-problem/view-nics.png)
4. Po lewej stronie wybierz pozycję **Efektywne trasy**. Efektywne trasy interfejsu sieciowego o nazwie **myVMNic1** ![są pokazane na poniższym rysunku: Zobacz trasy efektywne](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Jeśli do maszyny wirtualnej jest podłączonych wiele interfejsów sieciowych, można wyświetlić efektywne trasy dla dowolnego interfejsu sieciowego, wybierając go. Ponieważ każdy interfejs sieciowy może znajdować się w innej podsieci, każdy interfejs sieciowy może mieć różne efektywne trasy.

    W przykładzie pokazanym na poprzednim obrazie wymienione trasy są domyślnymi trasami, które platforma Azure tworzy dla każdej podsieci. Lista ma co najmniej te trasy, ale może mieć dodatkowe trasy, w zależności od możliwości, które mogły być włączone dla sieci wirtualnej, takich jak jest równorzędny z innej sieci wirtualnej lub podłączony do sieci lokalnej za pośrednictwem bramy sieci VPN platformy Azure. Aby dowiedzieć się więcej o każdej z tras i innych trasach, które mogą być widoczne dla interfejsu sieciowego, zobacz [Routing ruchu sieci wirtualnej](virtual-networks-udr-overview.md). Jeśli lista ma dużą liczbę tras, może być łatwiej wybrać **opcję Pobierz**, aby pobrać plik csv z listą tras.

Chociaż efektywne trasy były wyświetlane za pośrednictwem maszyny Wirtualnej w poprzednich krokach, można również wyświetlić skuteczne trasy za pośrednictwem:
- **Indywidualny interfejs sieciowy**: Dowiedz się, jak [wyświetlić interfejs sieciowy](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela poszczególnych tras**: Dowiedz się, jak [wyświetlić tabelę tras](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli program PowerShell jest uruchamiany z komputera, potrzebny jest moduł programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, `Connect-AzAccount` musisz również uruchomić, aby zalogować się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia.](virtual-network-network-interface.md#permissions)

Uzyskaj skuteczne trasy dla interfejsu sieciowego dzięki [get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). W poniższym przykładzie pobiera skuteczne trasy dla interfejsu sieciowego o nazwie *myVMNic1*, który znajduje się w grupie zasobów o nazwie *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Aby zrozumieć informacje zwrócone w danych wyjściowych, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe są zwracane tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomionym. Jeśli do maszyny wirtualnej jest podłączonych wiele interfejsów sieciowych, można przejrzeć efektywne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może znajdować się w innej podsieci, każdy interfejs sieciowy może mieć różne efektywne trasy. Jeśli nadal masz problem z komunikacją, zapoznaj się [z dodatkową diagnozą](#additional-diagnosis) i [uwagami](#considerations).

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny Wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych podłączonych do maszyny Wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymujesz dane wyjściowe podobne do następującego przykładu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

W poprzednim wyjściu nazwa interfejsu sieciowego to *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnozowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiając interfejsu wiersza polecenia z komputera. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, musisz również uruchomić i zalogować się na platformę Azure przy użyciu konta, które ma [niezbędne uprawnienia.](virtual-network-network-interface.md#permissions)

Uzyskaj skuteczne trasy dla interfejsu sieciowego z [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). W poniższym przykładzie pobiera skuteczne trasy dla interfejsu sieciowego o nazwie *myVMNic1,* który znajduje się w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Aby zrozumieć informacje zwrócone w danych wyjściowych, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe są zwracane tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomionym. Jeśli do maszyny wirtualnej jest podłączonych wiele interfejsów sieciowych, można przejrzeć efektywne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może znajdować się w innej podsieci, każdy interfejs sieciowy może mieć różne efektywne trasy. Jeśli nadal masz problem z komunikacją, zapoznaj się [z dodatkową diagnozą](#additional-diagnosis) i [uwagami](#considerations).

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny Wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych podłączonych do maszyny Wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Rozwiązywanie problemu

Rozwiązywanie problemów z routingiem zazwyczaj składa się z:

- Dodawanie trasy niestandardowej w celu zastąpienia jednej z domyślnych tras platformy Azure. Dowiedz się, jak [dodać trasę niestandardową](manage-route-table.md#create-a-route).
- Zmień lub usuń trasę niestandardową, która może spowodować routing do niepożądanej lokalizacji. Dowiedz się, jak [zmienić](manage-route-table.md#change-a-route) lub [usunąć](manage-route-table.md#delete-a-route) trasę niestandardową.
- Upewnienie się, że tabela tras zawierająca zdefiniowane trasy niestandardowe jest skojarzona z podsiecią, w której znajduje się interfejs sieciowy. Dowiedz się, jak [skojarzyć tabelę marszrut z podsiecią](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zapewnienie, że urządzenia, takie jak brama sieci VPN platformy Azure lub wdrożone urządzenia wirtualne sieci, są obsługiwane. Użyj funkcji [diagnostyki sieci VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkcji Obserwatora sieci, aby określić wszelkie problemy z bramą sieci VPN platformy Azure.

Jeśli nadal występują problemy z komunikacją, zobacz [Zagadnienia](#considerations) i Dodatkowa diagnoza.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z komunikacją należy wziąć pod uwagę następujące kwestie:

- Routing jest oparty na najdłuższym dopasowaniu prefiksu (LPM) między zdefiniowanymi trasami, protokołem bramy granicznej (BGP) i trasami systemowymi. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, trasa jest wybierana na podstawie jej pochodzenia w kolejności podanej w [przeglądzie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route). W skutecznych trasach można zobaczyć tylko efektywne trasy, które są dopasowaniem LPM na podstawie wszystkich dostępnych tras. Wyświetlanie sposobu oceny tras dla interfejsu sieciowego znacznie ułatwia rozwiązywanie problemów z określonymi trasami, które mogą mieć wpływ na komunikację z maszyny Wirtualnej.
- Jeśli zdefiniowano niestandardowe trasy do sieciowego urządzenia wirtualnego (NVA), z *urządzeniem wirtualnym* jako następnym typem przeskoku, upewnij się, że przekazywanie adresów IP jest włączone w urządzeniu WUS odbierającym ruch lub pakiety są odrzucane. Dowiedz się więcej o [włączaniu przekazywania adresów IP dla interfejsu sieciowego](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Ponadto system operacyjny lub aplikacja w ramach urządzenia WUS musi również mieć możliwość przekazywania ruchu sieciowego i być skonfigurowana w tym celu.
- Jeśli utworzono trasę do 0.0.0.0/0, cały wychodzący ruch internetowy jest kierowany do następnego przeskoku, który został określony, na przykład do bramy WUS lub sieci VPN. Tworzenie takiej trasy jest często określane jako wymuszone tunelowanie. Połączenia zdalne korzystające z protokołów RDP lub SSH z Internetu do maszyny Wirtualnej mogą nie działać z tą trasą, w zależności od tego, jak następny przeskok obsługuje ruch. Wymuszone tunelowanie można włączyć:
    - W przypadku korzystania z sieci VPN typu lokacja lokacja, tworząc trasę z następnym typem przeskoku *bramy sieci VPN*. Dowiedz się więcej o [konfigurowaniu tunelowania wymuszonego](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Jeśli 0.0.0.0/0 (trasa domyślna) jest anonsowana za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej podczas korzystania z sieci VPN typu lokacja lokacja lub obwodu usługi ExpressRoute. Dowiedz się więcej o używaniu protokołu BGP w [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [usługi ExpressRoute.](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)
- Aby ruch komunikacji równorzędnej w sieci wirtualnej działał poprawnie, dla zakresu prefiksów sieci wirtualnej równorzędnej musi istnieć następna trasa przeskoku z następnym *typem sieci wirtualnej.* Jeśli taka trasa nie istnieje, a łącze komunikacji równorzędnej sieci wirtualnej jest **Połączone:**
    - Odczekaj kilka sekund i ponów próbę. Jeśli jest to nowo utworzone łącze równorzędne, od czasu do czasu trwa dłużej propagować trasy do wszystkich interfejsów sieciowych w podsieci. Aby dowiedzieć się więcej o komunikacji równorzędnej w sieci wirtualnej, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md) i [zarządzanie komunikacją równorzędnie sieci wirtualnej](virtual-network-manage-peering.md).
    - Reguły sieciowej grupy zabezpieczeń mogą mieć wpływ na komunikację. Aby uzyskać więcej informacji, zobacz [Diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md).
- Chociaż platforma Azure przypisuje trasy domyślne do każdego interfejsu sieciowego platformy Azure, jeśli masz wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, tylko podstawowy interfejs sieciowy jest przypisany domyślną trasą (0.0.0.0/0) lub bramą w systemie operacyjnym maszyny Wirtualnej. Dowiedz się, jak utworzyć trasę domyślną dla pomocniczych interfejsów sieciowych dołączonych do maszyny Wirtualnej [systemu Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Linux.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Dowiedz się więcej o [podstawowych i pomocniczych interfejsach sieciowych](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Dodatkowa diagnoza

* Aby uruchomić szybki test, aby określić następny typ przeskoku dla ruchu przeznaczonego do lokalizacji, należy użyć funkcji [Następnego przeskoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usługi Azure Network Watcher. Następny przeskok informuje, jaki jest następny typ przeskoku dla ruchu przeznaczonego do określonej lokalizacji.
* Jeśli nie ma żadnych tras powodujących niepowodzenie komunikacji sieciowej maszyny Wirtualnej, problem może być spowodowany oprogramowaniem zapory uruchomionym w systemie operacyjnym maszyny Wirtualnej
* Jeśli [wymuszasz tunelowanie](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ruchu do urządzenia lokalnego za pośrednictwem bramy sieci VPN lub urządzenia WUS, łączenie się z maszyną wirtualną z Internetu może być nie możliwe, w zależności od sposobu skonfigurowania routingu dla urządzeń. Upewnij się, że routing skonfigurowany dla urządzenia kieruje ruch na publiczny lub prywatny adres IP maszyny Wirtualnej.
* Użyj możliwości [rozwiązywania problemów z połączeniem](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkcji Obserwatora sieci, aby określić przyczyny routingu, filtrowania i obsługi w os, powodujące problemy z komunikacją wychodzącą.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wszystkich zadaniach, właściwościach i ustawieniach [tabeli tras i tras](manage-route-table.md).
- Dowiedz się więcej o [wszystkich następnych typach przeskoków, trasach systemowych i o tym, jak platforma Azure wybiera trasę](virtual-networks-udr-overview.md).
