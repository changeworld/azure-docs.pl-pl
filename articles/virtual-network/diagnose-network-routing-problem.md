---
title: Diagnozowanie problemu z routingiem maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak diagnozowanie problemu z routingiem maszyny wirtualnej, wyświetlając obowiązujące trasy dla maszyny wirtualnej.
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
ms.openlocfilehash: 465d44ea823c99afbb4f25541d64770c114ba7e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730511"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnozowanie problemu z routingiem maszyny wirtualnej

W tym artykule dowiesz się, jak diagnozowanie problemu z routingiem, wyświetlając tras, które obowiązują dla interfejsu sieciowego w maszynie wirtualnej (VM). Platforma Azure tworzy kilka trasy domyślne dla każdej podsieci sieci wirtualnej. Definiowanie tras w tabeli tras, a następnie kojarzenie tabeli tras do podsieci, można zastąpić domyślnych tras platformy. Są kombinacją tworzenia tras domyślnych tras platformy i wszystkie trasy propagowane z sieci lokalnej za pośrednictwem bramy sieci VPN platformy Azure (Jeśli w Twojej sieci wirtualnej jest połączona z siecią lokalną) za pośrednictwem protokołu border gateway protocol (BGP) skuteczne trasy dla wszystkich interfejsów sieciowych w podsieci. Jeśli nie jesteś zaznajomiony z siecią wirtualną, interfejsu sieciowego lub routingu pojęć, zobacz [Omówienie usługi Virtual network](virtual-networks-overview.md), [interfejs sieciowy](virtual-network-network-interface.md), i [Omówienie routingu](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Scenariusz

Połączenie z maszyną wirtualną, ale połączenie nie powiedzie się. Aby ustalić, dlaczego nie można połączyć z maszyną wirtualną, możesz wyświetlić obowiązujące trasy dla interfejsu sieciowego przy użyciu platformy Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), lub [wiersza polecenia platformy Azure](#diagnose-using-azure-cli).

Poniższe kroki przyjęto założenie, że masz istniejącej maszyny Wirtualnej, aby wyświetlić obowiązujące trasy. Jeśli nie masz istniejącej maszyny Wirtualnej, najpierw wdrożyć [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny Wirtualnej w celu wykonania zadań w tym artykule. Przykłady w niniejszym artykule są dla maszyny Wirtualnej o nazwie *myVM* z interfejsem sieciowym o nazwie *myVMVMNic*. Interfejs maszyny Wirtualnej i sieci znajdują się w grupie zasobów o nazwie *myResourceGroup*, a w *wschodnie stany USA* regionu. Zmień wartości w krokach, odpowiednio do diagnozowania problemów dla maszyny Wirtualnej.

## <a name="diagnose-using-azure-portal"></a>Diagnozować przy użyciu witryny Azure portal

1. Zaloguj się do platformy Azure [portal](https://portal.azure.com) przy użyciu konta Azure, która ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).
2. W górnej części witryny Azure portal wprowadź nazwę maszyny Wirtualnej, która jest w stanie uruchomienia, w polu wyszukiwania. Gdy nazwa maszyny Wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
3. Wybierz **diagnozowanie i rozwiązywanie problemów**, a następnie w obszarze **zalecane kroki**, wybierz opcję **obowiązujące trasy** w pozycji 7, jak pokazano na poniższej ilustracji:

    ![Wyświetlanie obowiązujących tras](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Obowiązujących tras dla interfejsu sieciowego o nazwie **myVMVMNic** przedstawiono na poniższej ilustracji:

     ![Wyświetlanie obowiązujących tras](./media/diagnose-network-routing-problem/effective-routes.png)

    Jeśli istnieje wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, możesz wyświetlić obowiązujące trasy dla dowolnego interfejsu sieciowego, wybierając ją. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdy interfejs sieciowy może mieć inną aktywnych tras.

    W tym przykładzie pokazano na poprzedniej ilustracji trasy wymienione są trasy domyślne, które platforma Azure tworzy dla każdej podsieci. Na liście ma co najmniej te trasy, ale mogą mieć dodatkowe trasy, w zależności od możliwości, które zostało włączone dla sieci wirtualnej, takie jak jego jest połączona z inną siecią wirtualną lub połączenia z siecią lokalną za pośrednictwem bramy sieci VPN platformy Azure. Aby dowiedzieć się więcej na temat każdej trasy i innych tras, może zostać wyświetlony dla interfejsu sieciowego, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md). Jeśli lista zawiera dużą liczbę tras, może okazać się łatwiej wybrać **Pobierz**, aby pobrać plik CSV zawierający listę trasy.

Chociaż obowiązujące trasy były wyświetlane za pomocą maszyny Wirtualnej w poprzednich krokach, można również wyświetlić obowiązujące trasy za pomocą:
- **Interfejs sieciowy poszczególnych**: Dowiedz się, jak [wyświetlania interfejsu sieciowego](virtual-network-network-interface.md#view-network-interface-settings).
- **Tabela tras poszczególnych**: Dowiedz się, jak [Wyświetl tabelę tras](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy modułu Azure PowerShell w wersji 1.0.0 lub nowszym. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, trzeba będzie również uruchomić `Connect-AzAccount` zalogować się przy użyciu konta które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz efektywne trasy dla interfejsu sieciowego z [Get AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). Poniższy przykład pobiera obowiązujące trasy dla interfejsu sieciowego o nazwie *myVMVMNic*, która jest w grupie zasobów o nazwie *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Aby poznać informacje zwrócone w danych wyjściowych, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe jest zwracany tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomienia. Jeśli istnieje wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, możesz przejrzeć obowiązujące trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdy interfejs sieciowy może mieć inną aktywnych tras. Jeśli nadal masz problem z komunikacją, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwy interfejs sieciowy jest podłączony do maszyny wirtualnej, poniższe polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymasz dane wyjściowe podobne do poniższego przykładu:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

W danych wyjściowych poprzedniej, nazwa interfejsu sieciowego jest *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnozować przy użyciu wiersza polecenia platformy Azure

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` i zaloguj się do platformy Azure za pomocą konta mającego [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz efektywne trasy dla interfejsu sieciowego z [az network nic show obowiązywać route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Poniższy przykład pobiera obowiązujące trasy dla interfejsu sieciowego o nazwie *myVMVMNic* jest w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Aby poznać informacje zwrócone w danych wyjściowych, zobacz [Omówienie routingu](virtual-networks-udr-overview.md). Dane wyjściowe jest zwracany tylko wtedy, gdy maszyna wirtualna jest w stanie uruchomienia. Jeśli istnieje wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, możesz przejrzeć obowiązujące trasy dla każdego interfejsu sieciowego. Ponieważ każdy interfejs sieciowy może być w innej podsieci, każdy interfejs sieciowy może mieć inną aktywnych tras. Jeśli nadal masz problem z komunikacją, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwy interfejs sieciowy jest podłączony do maszyny wirtualnej, poniższe polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Rozwiązywanie problemów

Rozwiązywanie problemów z routingiem zwykle składa się z:

- Dodawanie tras niestandardowych, aby zastąpić jedną z domyślnych tras platformy. Dowiedz się, jak [dodać trasy niestandardowej](manage-route-table.md#create-a-route).
- Zmień lub usuń tras niestandardowych, które mogą powodować routingu do lokalizacji niepożądane. Dowiedz się, jak [zmienić](manage-route-table.md#change-a-route) lub [Usuń](manage-route-table.md#delete-a-route) tras niestandardowych.
- Zapewnienie, że tabelę tras, która zawiera wszystkie trasy niestandardowe zdefiniowany przez użytkownika jest skojarzona z podsiecią, w której znajduje się interfejs sieciowy. Dowiedz się, jak [kojarzenie tabeli tras do podsieci](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Zapewnienie, że urządzeń, takich jak sieci VPN platformy Azure bramy lub sieci wirtualnych urządzeń, w których wdrożono są obsługiwane. Użyj [diagnostyki sieci VPN](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) możliwości usługi Network Watcher ustalenie problemów z bramą Azure VPN gateway.

Jeśli nadal występują problemy z komunikacją, zobacz [zagadnienia](#considerations) i dodatkowe diagnostyki.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z komunikacją, należy wziąć pod uwagę następujące kwestie:

- Routing jest oparty na dopasowanie najdłuższego prefiksu (LPM) między trasy, zdefiniowany przez użytkownika, trasy bramy obramowania protocol (BGP) i systemu. Jeśli istnieje więcej niż jedna trasa z tym samym dopasowaniem LPM, a następnie trasy jest zaznaczone, pochodzenia w podanej kolejności, w oparciu o [Omówienie routingu](virtual-networks-udr-overview.md#how-azure-selects-a-route). Ze skutecznymi trasami może zobaczyć tylko obowiązujących tras, które spełniają LPM, w oparciu o dostępnych tras. Oglądanie, jak trasy są oceniane pod kątem interfejsu sieciowego w znacznie ułatwia rozwiązywanie problemów z określonej trasy, które mogą mieć wpływ na komunikację z maszyną Wirtualną.
- Jeśli zdefiniowany przez użytkownika trasy niestandardowe do wirtualnego urządzenia sieciowego (WUS), za pomocą *urządzenie wirtualne* jako typ następnego przeskoku zapewnić, że przesyłanie dalej IP jest włączone na urządzeniu WUS odbierającym ruch, pakiety są porzucane. Dowiedz się więcej o [Włączanie przekazywanie adresów IP dla interfejsu sieciowego](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Ponadto systemu operacyjnego lub aplikacji w urządzeniu WUS również należy przekazywać dalej ruch sieciowy i być skonfigurowana w tym celu.
- Jeśli utworzono trasę do 0.0.0.0/0, cały ruch wychodzący z Internetu jest kierowany do następnego przeskoku określonego, np. Brama sieci VPN lub urządzenia WUS. Tworzenie takich trasy jest często określany mianem wymuszonego tunelowania. Połączenia zdalne za pomocą protokołów protokołu RDP lub SSH z Internetu do maszyny Wirtualnej może nie działać z tej trasy, w zależności od tego, jak następny przeskok obsługuje ruch. Można włączyć tunelowania wymuszanego:
    - Korzystając z sieci VPN typu lokacja lokacja, tworząc trasę z typem następnego przeskoku dla *bramy sieci VPN*. Dowiedz się więcej o [Konfigurowanie wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Jeśli 0.0.0.0/0 (trasy domyślnej) jest anonsowane za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej, korzystając z sieci VPN lokacja lokacja lub obwodu ExpressRoute. Dowiedz się więcej o korzystaniu z protokołu BGP z [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- Dla komunikacji równorzędnej ruchu w sieci wirtualnej działają prawidłowo, trasa systemowa z typem następnego przeskoku dla *komunikacja równorzędna sieci wirtualnych* musi istnieć w równorzędnej sieci wirtualnej prefiks zakresu. Jeśli takie trasy nie istnieje, a połączenie komunikacji równorzędnej sieci wirtualnej **połączono**:
    - Odczekaj kilka sekund, a następnie spróbuj ponownie. Jeśli nowo utworzonego linku komunikacji równorzędnej, od czasu do czasu zajmuje więcej czasu na propagację trasy, aby wszystkie interfejsy sieciowe w podsieci. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnej, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md) i [zarządzanie, wirtualne sieci równorzędne](virtual-network-manage-peering.md).
    - Reguły sieciowych grup zabezpieczeń może mieć wpływ na komunikację. Aby uzyskać więcej informacji, zobacz [diagnozowanie problemu z filtrowaniem ruchu maszyny wirtualnej sieci](diagnose-network-traffic-filter-problem.md).
- Jednak platforma Azure przypisuje trasy domyślne do każdego interfejsu sieci platformy Azure, jeśli masz wiele interfejsów sieciowych dołączonych do maszyny Wirtualnej, tylko podstawowy interfejs sieciowy jest przypisany trasy domyślnej (0.0.0.0/0) lub bramy w ramach systemu operacyjnego maszyny Wirtualnej. Dowiedz się, jak utworzyć trasę domyślną dla dodatkowych interfejsów sieciowych dołączonych do [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) maszyny Wirtualnej. Dowiedz się więcej o [interfejsów sieciowych podstawowych i pomocniczych](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Dodatkowe diagnostyki

* Aby uruchomić szybkie testowe, aby określić typ następnego przeskoku dla ruchu kierowanego do lokalizacji, należy użyć [miejsca docelowego następnego skoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) możliwości usługi Azure Network Watcher. Następny przeskok informuje, co to jest typ następnego przeskoku dla ruchu kierowanego do określonej lokalizacji.
* Jeśli istnieje nie tras, powodując komunikacji sieciowej maszyny Wirtualnej nie powiedzie się, problem może być spowodowane oprogramowanie zapory, w ramach systemu operacyjnego maszyny Wirtualnej
* Jeśli jesteś [wymuszanie tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ruch do urządzenia w środowisku lokalnym za pośrednictwem bramy sieci VPN lub urządzenia WUS, nie można nawiązać połączenie z maszyną wirtualną z Internetu, w zależności od tego, jak zostały skonfigurowane, routing dla urządzeń. Upewnij się, że routingu, które zostały skonfigurowane dla tego urządzenia kieruje ruch do albo publicznych lub prywatnych adresów IP dla maszyny Wirtualnej.
* Użyj [Rozwiązywanie problemów z połączeniami](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) możliwość usługi Network Watcher ustalić routingu, filtrowania i -OS przyczyny problemów komunikacji wychodzącej.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o wszystkich zadań, właściwości i ustawienia dla [trasy w tabeli i tras](manage-route-table.md).
- Dowiedz się więcej o wszystkich [następnego przeskoku typy trasy systemowe i jak platforma Azure wybiera trasę](virtual-networks-udr-overview.md).
