---
title: Zdiagnozować problem routingu maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdiagnozować problem routingu maszyny wirtualnej wyświetlając skuteczne trasy dla maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 07352a5d7c8b465440efab17c654979662a95f8e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702374"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Zdiagnozować problem routingu maszyny wirtualnej

W tym artykule możesz dowiedzieć się, jak zdiagnozować problem routingu, wyświetlając tras, na których zostaną zastosowane dla karty sieciowej na maszynie wirtualnej (VM). Platforma Azure tworzy kilka tras domyślnych dla każdej podsieci sieci wirtualnej. Definiowanie tras w tabeli tras, a następnie kojarzenie tabeli tras z podsiecią można zastąpić trasy domyślne platformy Azure. Kombinacja tworzenia tras, trasy domyślne platformy Azure i wszelkie tras rozprowadzane z sieci lokalnej za pośrednictwem bramy sieci VPN platformy Azure (jeśli sieci wirtualnej jest podłączony do sieci lokalnej) za pośrednictwem protokołu border gateway protocol (BGP) skuteczne trasy dla wszystkich interfejsów sieciowych w podsieci. Jeśli nie masz doświadczenia w obsłudze sieci wirtualnej, interfejsu sieciowego lub routingu pojęcia, zobacz [omówienie sieci wirtualnej](virtual-networks-overview.md), [interfejsu sieciowego](virtual-network-network-interface.md), i [Omówienie routingu](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenariusz

Połączenie z maszyną wirtualną, ale połączenie nie powiedzie się. Aby ustalić, dlaczego nie można połączyć się z maszyną Wirtualną, można wyświetlić skuteczne trasy dla interfejsu sieciowego przy użyciu platformy Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), lub [interfejsu wiersza polecenia Azure](#diagnose-using-azure-cli).

Czynności, które wykonują przyjęto założenie, że masz istniejącą maszynę Wirtualną, aby wyświetlić skuteczne trasy dla. Jeśli nie masz istniejącej maszyny Wirtualnej, najpierw wdrożyć [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny Wirtualnej, aby wykonać zadania w tym artykule i. Przykłady w tym artykule odnoszą się do maszyny Wirtualnej o nazwie *myVM* z karty sieciowej o nazwie *myVMVMNic*. Interfejs maszyny Wirtualnej i sieci znajdują się w grupie zasobów o nazwie *myResourceGroup*, a w *wschodnie stany USA* regionu. Zmień kroki odpowiednie dla maszyny Wirtualnej są diagnozowania problemu dla wartości.

## <a name="diagnose-using-azure-portal"></a>Diagnozowanie za pomocą portalu Azure

1. Zaloguj się do platformy Azure [portal](https://portal.azure.com) z konta platformy Azure, która ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).
2. W górnej części portalu Azure wprowadź nazwę maszyny wirtualnej, która jest w stanie uruchomienia, w polu wyszukiwania. Gdy nazwa maszyny Wirtualnej pojawi się w wynikach wyszukiwania, zaznacz go.
3. Wybierz **diagnozowanie i rozwiązywanie problemów**, a następnie w obszarze **zalecane kroki**, wybierz pozycję **skuteczne tras** w pozycji 7, jak pokazano na poniższej ilustracji:

    ![Widok skuteczne tras](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Obowiązującą trasy dla karty sieciowej o nazwie **myVMVMNic** przedstawiono na poniższej ilustracji:

     ![Widok skuteczne tras](./media/diagnose-network-routing-problem/effective-routes.png)

    Jeśli istnieje wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, można wyświetlać skuteczne trasy dla dowolnego interfejsu sieciowego, wybierając go. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdego interfejsu sieciowego może mieć różne trasy skuteczne.

    W tym przykładzie pokazano na rysunku powyżej trasy wymienione na liście są trasy domyślne, które platforma Azure tworzy dla każdej podsieci. Na liście ma co najmniej te trasy, ale mogą mieć dodatkowe trasy, w zależności od możliwości, które zostało włączone dla sieci wirtualnej, takie jak jego połączyć za pomocą z innej sieci wirtualnej lub podłączony do sieci lokalnej za pośrednictwem bramy sieci VPN platformy Azure. Aby dowiedzieć się więcej na temat trasy i innych tras, może zostać wyświetlony dla interfejsu sieciowego, zobacz [routingu ruchu sieciowego wirtualnej](virtual-networks-udr-overview.md). Jeśli lista zawiera wiele tras, może być łatwiej wybrać **Pobierz**, aby pobrać plik CSV zawierający listę trasy.

Chociaż skuteczne trasy były wyświetlane przy użyciu maszyny Wirtualnej w poprzednich krokach, możesz również wyświetlić skuteczne tras za pomocą:
- **Interfejs sieciowy z poszczególnych**: Dowiedz się, jak [wyświetlić interfejs sieciowy](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela tras poszczególnych**: Dowiedz się, jak [wyświetlić tabelę tras](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

Możesz uruchamiać polecenia, które należy wykonać w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Powłoka chmury Azure jest bezpłatne powłoki interakcyjne. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy *AzureRM* modułu programu PowerShell, wersji 6.0.1 lub nowszej. Uruchom `Get-Module -ListAvailable AzureRM` na komputerze, aby znaleźć zainstalowanej wersji. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, należy uruchomić `Login-AzureRmAccount` do zalogowania się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz skuteczne trasy dla interfejsu sieciowego z [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Poniższy przykład pobiera skuteczne trasy dla karty sieciowej o nazwie *myVMVMNic*, która jest w grupie zasobów o nazwie *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Aby poznać zwracanych w danych wyjściowych informacji, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe są zwracane tylko, jeśli maszyna wirtualna jest w stanie uruchomienia. Jeśli istnieje wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, można przejrzeć skuteczne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdego interfejsu sieciowego może mieć różne trasy skuteczne. Jeśli nadal masz problem z komunikacją, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwę interfejs sieciowy jest dołączony do maszyny wirtualnej, następujące polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Pojawi się dane wyjściowe podobne do poniższego przykładu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

W wyniku poprzedniego Nazwa interfejsu sieciowego jest *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnozowanie przy użyciu wiersza polecenia platformy Azure

Możesz uruchamiać polecenia, które należy wykonać w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. W tym artykule wymaga wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` i zaloguj się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz skuteczne trasy dla interfejsu sieciowego z [az sieci karty sieciowej Pokaż obowiązującej--tabeli tras](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Poniższy przykład pobiera skuteczne trasy dla karty sieciowej o nazwie *myVMVMNic* jest w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Aby poznać zwracanych w danych wyjściowych informacji, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe są zwracane tylko, jeśli maszyna wirtualna jest w stanie uruchomienia. Jeśli istnieje wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, można przejrzeć skuteczne trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdego interfejsu sieciowego może mieć różne trasy skuteczne. Jeśli nadal masz problem z komunikacją, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwę interfejs sieciowy jest dołączony do maszyny wirtualnej, następujące polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Rozwiąż problem

Rozwiązywanie problemów z routingiem zazwyczaj składa się z:

- Dodawanie tras niestandardowych, aby zastąpić jedną z tras domyślnych platformy Azure. Dowiedz się, jak [dodać tras niestandardowych](manage-route-table.md#create-a-route).
- Zmień lub usuń tras niestandardowych, który może powodować routingu do lokalizacji niepożądane. Dowiedz się, jak [zmienić](manage-route-table.md#change-a-route) lub [usunąć](manage-route-table.md#delete-a-route) tras niestandardowych.
- Zapewnienie, że tabeli tras, która zawiera wszystkie trasy niestandardowe zdefiniowany przez użytkownika jest skojarzona z podsieci, w której znajduje się interfejs sieciowy. Dowiedz się, jak [skojarzyć tabelę tras z podsiecią](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Sprawdzeniu, czy urządzeń, takich jak sieci VPN platformy Azure bramy lub sieci wirtualnych urządzeń, w których została wdrożona są obsługiwane. Użyj [diagnostyki sieci VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) możliwości obserwatora sieciowego ustalenie problemów z bramy sieci VPN platformy Azure.

Jeśli nadal występują problemy z komunikacją, zobacz [zagadnienia](#considerations) i [dodatkowe diagnostyki](#additional-dignosis).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z komunikacją, należy wziąć pod uwagę następujące kwestie:

- Routing jest oparta na najdłuższe dopasowanie prefiksu (LPM Wybierane) spośród tras czy zdefiniowany przez użytkownika, obramowania gateway protocol (BGP) i systemu trasy. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, wybór trasy odbywa się w kolejności podanej w [Omówienie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route). Skuteczne trasy może zobaczyć tylko skuteczne ścieżek, które są dopasowaniem LPM, oparte na dostępnych tras. Wyświetlanie, jak są analizowane trasy dla interfejsu sieciowego w znacznie ułatwia rozwiązywanie problemów z określonej trasy, które mogą mieć wpływ na komunikację z maszyny Wirtualnej.
- Jeśli zdefiniowano niestandardowe trasy do sieci (NVA), urządzenie wirtualne z *urządzenie wirtualne* jako typ następnego przeskoku upewnij się, że przekazywanie IP jest włączona na NVA odbieranie ruchu lub porzucenia pakietów. Dowiedz się więcej o [umożliwiające przesyłanie dalej IP dla karty sieciowej](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Ponadto systemu operacyjnego lub aplikacji analizę NVA musi również mieć możliwość przekazywania ruchu sieciowego i można skonfigurować, aby to zrobić.
- Jeśli po utworzeniu trasę do 0.0.0.0/0, cały ruch wychodzący z Internetu jest kierowany do następnego przeskoku określone, na przykład w ramach bramy NVA lub sieci VPN. Tworzenie takich trasy jest często określane jako wymuszonego tunelowania. Połączenia zdalne za pomocą protokołu RDP lub SSH protokoły z Internetu do maszyny Wirtualnej nie może działać przy użyciu tej trasy, w zależności od tego, jak następny przeskok obsługuje ruch. Tunelowanie wymuszone można włączyć:
    - Korzystając z sieci VPN typu lokacja lokacja, tworząc trasę z typem następnego przeskoku *bramy sieci VPN*. Dowiedz się więcej o [Konfigurowanie wymuszone tunelowanie](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Jeśli wartość 0.0.0.0/0 (domyślna) jest anonsowana za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej przy użyciu sieci VPN lokacja lokacja lub obwodu usługi expressroute. Dowiedz się więcej o korzystaniu z protokołu BGP z [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Dla komunikacji równorzędnej ruchu w sieci wirtualnej działają prawidłowo, trasy systemowe z typem następnego przeskoku *sieci wirtualnej komunikacji równorzędnej* musi istnieć dla zakresu prefiksu peered sieci wirtualnej. Jeśli taka trasa nie istnieje, a łącze sieci wirtualnej komunikacji równorzędnej jest **połączony**:
    - Poczekaj kilka sekund, a następnie spróbuj ponownie. Jeśli jest to nowo utworzonego łącze komunikacji równorzędnej, czasami trwa dłużej propagowania tras dla wszystkich interfejsów sieciowych w podsieci. Aby dowiedzieć się więcej na temat sieci wirtualnej komunikacji równorzędnej, zobacz [komunikacji równorzędnej omówienie sieci wirtualnej](virtual-network-peering-overview.md) i [Zarządzanie sieci wirtualnej komunikacji równorzędnej](virtual-network-manage-peering.md).
    - Reguły grupy zabezpieczeń sieci może wpływać na komunikację. Aby uzyskać więcej informacji, zobacz [zdiagnozować problem filtru ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md).
- Chociaż Azure przypisuje tras domyślnych do każdego interfejsu sieci platformy Azure, jeśli masz wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, tylko podstawowy interfejs sieciowy jest przypisany trasa domyślna (0.0.0.0/0) lub bramy, w ramach systemu operacyjnego maszyny Wirtualnej. Dowiedz się, jak utworzyć domyślną trasę dla dodatkowych interfejsów sieciowych dołączonych do [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) maszyny Wirtualnej. Dowiedz się więcej o [interfejsów sieciowych podstawowych i pomocniczych](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Dodatkowe diagnostyki

* Aby uruchomić test szybki, aby określić typ następnego przeskoku ruch kierowany do lokalizacji, należy użyć [następnego przeskoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) możliwości obserwatora sieciowego Azure. Następny przeskok informuje Typ następnego przeskoku jest dla ruchu kierowanego do określonej lokalizacji.
* Jeśli nie ma tras, co powoduje niepowodzenie komunikacji w sieci maszyny Wirtualnej, problem może wynikać z oprogramowanie zapory działających w systemie operacyjnym maszyny Wirtualnej
* Jeśli jesteś [wymuszanie tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ruchu do lokalnego urządzenia za pośrednictwem bramy sieci VPN lub NVA, nie można nawiązać połączenia z maszyną wirtualną z Internetu, w zależności od tego, jak skonfigurowano routingu dla urządzeń. Upewnij się, że routingu, które zostały skonfigurowane dla urządzenia kieruje ruchem jednej publicznych lub prywatnych adresów IP dla maszyny Wirtualnej.
* Użyj [Rozwiązywanie problemów z połączenia](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) możliwości obserwatora sieciowego, aby ustalić przyczyny problemów komunikacji wychodzącej routingu, filtrowania i w systemu operacyjnego.

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat wszystkich zadań, właściwości i ustawienia [trasy tras i tabeli](manage-route-table.md).
- Więcej informacji na temat wszystkich [następnego przeskoku typy tras systemowych i jak Azure wybiera trasę](virtual-networks-udr-overview.md).
