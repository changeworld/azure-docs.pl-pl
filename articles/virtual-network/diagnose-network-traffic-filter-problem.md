---
title: Zdiagnozować problem filtru ruchu sieciowego maszyny wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdiagnozować problem filtru ruchu sieciowego maszyny wirtualnej poprzez wyświetlenie reguł efektywnym elementem systemu zabezpieczeń dla maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: jdial
ms.openlocfilehash: 1c33a75363eec2b4e338ba64e3d1ad877d8b1610
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757231"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Zdiagnozować problem filtru ruchu sieciowego maszyny wirtualnej

W tym artykule możesz dowiedzieć się, jak zdiagnozować problem z siecią ruchu filtru wyświetlając reguły zabezpieczeń (NSG) grupy zabezpieczeń sieci, które zostaną zastosowane dla maszyny wirtualnej (VM).

Grupy NSG umożliwiają kontrolę typów ruchu przepływające i maszyny Wirtualnej. Możesz skojarzyć grupy NSG do podsieci w sieci wirtualnej platformy Azure, dołączony do maszyny Wirtualnej i/lub interfejs sieciowy. Reguły efektywnym elementem systemu zabezpieczeń stosowane do interfejsu sieciowego są agregacji reguł, które istnieją w grupie NSG skojarzoną z interfejsem sieci i podsieci interfejsu sieciowego. Reguły w różne grupy NSG można czasami konflikt ze sobą i mieć wpływ na łączność sieciową z maszyny Wirtualnej. Można wyświetlić wszystkie reguły efektywnym elementem systemu zabezpieczeń ze grup NSG, które są stosowane w interfejsach sieciowych maszyny Wirtualnej. Jeśli nie znasz z sieci wirtualnej, interfejsu sieciowego lub pojęcia NSG, zobacz [omówienie sieci wirtualnej](virtual-networks-overview.md), [interfejsu sieciowego](virtual-network-network-interface.md), i [grupy zabezpieczeń sieci — omówienie](security-overview.md).

## <a name="scenario"></a>Scenariusz

Połączenie z maszyną wirtualną za pośrednictwem portu 80 z Internetu, ale połączenie nie powiedzie się. Aby ustalić, dlaczego nie masz dostępu do portu 80 z Internetu, można wyświetlić reguły efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego przy użyciu platformy Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), lub [interfejsu wiersza polecenia Azure](#diagnose-using-azure-cli).

Czynności, które wykonują przyjęto założenie, że masz istniejącą maszynę Wirtualną, aby wyświetlić zasady efektywnym elementem systemu zabezpieczeń. Jeśli nie masz istniejącej maszyny Wirtualnej, najpierw wdrożyć [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny Wirtualnej, aby wykonać zadania w tym artykule i. Przykłady w tym artykule odnoszą się do maszyny Wirtualnej o nazwie *myVM* z karty sieciowej o nazwie *myVMVMNic*. Interfejs maszyny Wirtualnej i sieci znajdują się w grupie zasobów o nazwie *myResourceGroup*, a w *wschodnie stany USA* regionu. Zmień kroki odpowiednie dla maszyny Wirtualnej są diagnozowania problemu dla wartości.

## <a name="diagnose-using-azure-portal"></a>Diagnozowanie za pomocą portalu Azure

1. Zaloguj się do platformy Azure [portal](https://portal.azure.com) z konta platformy Azure, która ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).
2. W górnej części portalu Azure wprowadź nazwę maszyny wirtualnej w polu wyszukiwania. Gdy nazwa maszyny Wirtualnej pojawi się w wynikach wyszukiwania, zaznacz go.
3. W obszarze **ustawienia**, wybierz pozycję **sieci**, jak pokazano na poniższej ilustracji:

    ![Widok reguły zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

    Zostanie wyświetlony na liście na poprzedniej ilustracji są dla karty sieciowej o nazwie **myVMVMNic**. Zobacz, czy **reguły ruchu przychodzącego dla portu** dla interfejsu sieciowego z dwóch różnych sieciowych grup zabezpieczeń:- **mySubnetNSG**: skojarzony z podsiecią, z którą interfejs sieciowy jest.
        - **myVMNSG**: skojarzonego z interfejsu sieciowego w maszynie Wirtualnej o nazwie **myVMVMNic**.

    Reguła o nazwie **DenyAllInBound** jest, co uniemożliwia komunikacji przychodzącej do maszyny Wirtualnej za pośrednictwem portu 80, przez Internet, zgodnie z opisem w [scenariusza](#scenario). Wyświetla reguły *0.0.0.0/0* dla **źródła**, w tym Internetu. Żadna inna reguła o wyższym priorytecie (niższym numerze) zezwala na porcie 80 dla ruchu przychodzącego. Pozwoli to port 80 dla ruchu przychodzącego maszyny Wirtualnej z Internetu, zobacz [rozwiązać problem](#resolve-a-problem). Aby dowiedzieć się więcej na temat zasad zabezpieczeń i jak Azure zastosuje je, zobacz [sieciowej grupy zabezpieczeń](security-overview.md).

    W dolnej części obrazu, zostanie również wyświetlony **reguł portów dla ruchu WYCHODZĄCEGO**. Zgodnie z tym są reguły portów wychodzących dla interfejsu sieciowego. Chociaż na ilustracji przedstawiono tylko cztery reguły ruchu przychodzącego dla każdej grupy NSG, NSG może mieć wiele więcej niż cztery reguły. Na ilustracji widać **VirtualNetwork** w obszarze **źródła** i **docelowego** i **AzureLoadBalancer** w obszarze  **ŹRÓDŁO**. **VirtualNetwork** i **AzureLoadBalancer** są [usługi tagi](security-overview.md#service-tags). Tagi usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoności tworzenia reguły zabezpieczeń.

4. Upewnij się, że maszyna wirtualna jest w działaniu stanu, a następnie wybierz **reguły efektywnym elementem systemu zabezpieczeń**, jak pokazano na rysunku powyżej, aby wyświetlić reguły efektywnym elementem systemu zabezpieczeń, pokazane na poniższej ilustracji:

    ![Wyświetl reguły efektywnym elementem systemu zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

    Reguły na liście są takie same w sposób opisany w kroku 3, chociaż istnieją różne karty grupy NSG skojarzonego z interfejsem sieciowym i podsieć. Jak widać na ilustracji, wyświetlane są tylko pierwsze 50 reguły. Aby pobrać plik CSV, który zawiera wszystkie reguły, wybierz **Pobierz**.

    Aby zobaczyć, które prefiksy każdy numer seryjny reprezentuje, wybierz regułę, takie jak reguły o nazwie **AllowAzureLoadBalancerInbound**. Na poniższej ilustracji przedstawiono prefiksów dla **AzureLoadBalancer** usługi tagu:

    ![Wyświetl reguły efektywnym elementem systemu zabezpieczeń](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

    Chociaż **AzureLoadBalancer** numer seryjny reprezentuje tylko jeden prefiks, inne tagi usługi reprezentują kilka prefiksów.

4. Poprzednie kroki wykazało reguły zabezpieczeń dla interfejsu sieciowego o nazwie **myVMVMNic**, ale także w tym samouczku karty sieciowej o nazwie **myVMVMNic2** w niektórych poprzednich obrazów. Maszyna wirtualna w tym przykładzie ma dwa interfejsy sieciowe podłączone do niego. Reguły efektywnym elementem systemu zabezpieczeń mogą być różne dla każdego interfejsu sieciowego.

    Aby wyświetlić zasady **myVMVMNic2** interfejsu sieciowego, wybierz ją. Jak pokazano na rysunku poniżej, interfejs sieciowy ma te same zasady skojarzone z jego podsieci jako **myVMVMNic** sieci interfejsu, ponieważ obu interfejsów znajdują się w tej samej podsieci. Po skojarzeniu grupy NSG z podsiecią, jego zasady są stosowane do wszystkich interfejsów sieciowych należących do podsieci.

    ![Widok reguły zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

    W odróżnieniu od **myVMVMNic** interfejsu sieciowego, **myVMVMNic2** interfejsu sieciowego nie ma sieciową grupę zabezpieczeń skojarzoną do niego. Każdego interfejsu sieci i podsieci może mieć wartość zero lub jeden NSG skojarzony. Grupa NSG skojarzona do każdego interfejsu sieciowego lub podsieci mogą być takie same, lub innych. Można skojarzyć tej samej grupy zabezpieczeń sieci na tyle interfejsy sieciowe i podsieci, wybierz polecenie.

Chociaż zasady efektywnym elementem systemu zabezpieczeń były wyświetlane przy użyciu maszyny Wirtualnej, można również wyświetlić reguły efektywnym elementem systemu zabezpieczeń, za pomocą:
- **Interfejs sieciowy z poszczególnych**: Dowiedz się, jak [wyświetlić interfejs sieciowy](virtual-network-network-interface.md#view-network-interface-settings).
- **Poszczególne grupy NSG**: Dowiedz się, jak [wyświetlić grupy NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

Możesz uruchamiać polecenia, które należy wykonać w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Powłoka chmury Azure jest bezpłatne powłoki interakcyjne. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy *AzureRM* modułu programu PowerShell, wersji 6.0.1 lub nowszej. Uruchom `Get-Module -ListAvailable AzureRM` na komputerze, aby znaleźć zainstalowanej wersji. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, należy uruchomić `Login-AzureRmAccount` do zalogowania się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions)].

Pobieranie reguł efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego z [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup). Poniższy przykład pobiera zasady efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic*, która jest w grupie zasobów o nazwie *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic interface `
  -ResourceGroupName myResourceGroup
```

Dane wyjściowe są zwracane w formacie json. Aby zrozumieć dane wyjściowe, zobacz [interpretować dane wyjściowe polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko, jeśli grupa NSG jest skojarzona z interfejsu sieciowego i podsieci, w której znajduje się interfejs sieciowy. Maszyna wirtualna musi być w stanie uruchomienia. Maszyna wirtualna może mieć wiele interfejsów sieciowych z różnych grup NSG stosowana. Podczas rozwiązywania problemu, uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwę interfejs sieciowy jest dołączony do maszyny wirtualnej, następujące polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
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

Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. W tym artykule wymaga wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` i zaloguj się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Pobieranie reguł efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego z [az sieci karty sieciowej listy obowiązującej nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Poniższy przykład pobiera zasady efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic* jest w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Dane wyjściowe są zwracane w formacie json. Aby zrozumieć dane wyjściowe, zobacz [interpretować dane wyjściowe polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko, jeśli grupa NSG jest skojarzona z interfejsu sieciowego i podsieci, w której znajduje się interfejs sieciowy. Maszyna wirtualna musi być w stanie uruchomienia. Maszyna wirtualna może mieć wiele interfejsów sieciowych z różnych grup NSG stosowana. Podczas rozwiązywania problemu, uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwę interfejs sieciowy jest dołączony do maszyny wirtualnej, następujące polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

W ramach dane wyjściowe zostaną wyświetlone informacje podobne do poniższego przykładu:

```azurecli
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

W wyniku poprzedniego Nazwa interfejsu sieciowego jest *myVMVMNic interfejsu*.

## <a name="interpret-command-output"></a>Interpretowanie danych wyjściowych polecenia

Niezależnie od tego, czy użyto [PowerShell](#diangose-using-powershell), lub [interfejsu wiersza polecenia Azure](#diagnose-using-azure-cli) Aby zdiagnozować problem, zostanie wyświetlony komunikat, który zawiera następujące informacje:

- **Grupy NetworkSecurityGroup**: identyfikator grupy zabezpieczeń sieci.
- **Skojarzenie**: czy grupy zabezpieczeń sieci jest skojarzony z *interfejsu sieciowego* lub *podsieci*. Jeśli grupa NSG jest skojarzona z obu, dane wyjściowe są zwracane z **grupy NetworkSecurityGroup**, **skojarzenia**, i **EffectiveSecurityRules**, dla każdej grupy NSG. Jeśli grupa NSG jest skojarzona lub usunąć skojarzenia bezpośrednio przed uruchomienie tego polecenia, aby wyświetlić reguły efektywnym elementem systemu zabezpieczeń, może być konieczne kilka sekund zmiany do uwzględnienia w danych wyjściowych polecenia.
- **EffectiveSecurityRules**: wyjaśnienie każda właściwość została szczegółowo opisana w [utworzyć reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Reguły nazw poprzedzone znakiem *defaultSecurityRules /* są domyślne reguły zabezpieczeń, które istnieją w każdej grupie NSG. Reguły nazw poprzedzone znakiem *securityRules /* są regułami, które zostały utworzone. Reguły określające [usługi tag](security-overview.md#service-tags), takich jak **Internet**, **VirtualNetwork**, i **AzureLoadBalancer** dla  **destinationAddressPrefix** lub **sourceAddressPrefix** właściwości również mają wartości **expandedDestinationAddressPrefix** właściwości. **ExpandedDestinationAddressPrefix** wszystkie prefiksy adresów reprezentowany przez numer seryjny listy właściwości.

Jeśli widzisz zduplikowane reguły wymienionych w danych wyjściowych to, że grupa NSG jest skojarzona do interfejsu sieciowego i podsieci. Zarówno grupy NSG mają te same zasady domyślne i może mieć dodatkowe zduplikowane reguły, jeśli utworzono własne reguły, które są takie same, w obu grup NSG.

Reguła o nazwie **defaultSecurityRules/DenyAllInBound** jest, co uniemożliwia komunikacji przychodzącej do maszyny Wirtualnej za pośrednictwem portu 80, przez Internet, zgodnie z opisem w [scenariusza](#scenario). Żadna inna reguła o wyższym priorytecie (niższym numerze) umożliwia port 80 przychodzące z Internetu.

## <a name="resolve-a-problem"></a>Rozwiąż problem

Określa, czy używasz platformy Azure [portalu](#diagnose-using-azure-portal), [programu PowerShell](#diagnose-using-powershell), lub [interfejsu wiersza polecenia Azure](#diagnose-using-azure-cli) Aby zdiagnozować problem przedstawionych w [scenariusza](#scenario) w tym artykuł, rozwiązaniem jest utworzenie reguły zabezpieczeń sieci o następujących właściwościach:

| Właściwość                | Wartość                                                                              |
|---------                |---------                                                                           |
| Element źródłowy                  | Dowolne                                                                                |
| Zakresy portów źródłowych      | Dowolne                                                                                |
| Element docelowy             | Adres IP maszyny wirtualnej, zakres adresów IP lub wszystkich adresów w podsieci. |
| Zakresy portów docelowych | 80                                                                                 |
| Protocol (Protokół)                | TCP                                                                                |
| Akcja                  | Zezwalaj                                                                              |
| Priorytet                | 100                                                                                |
| Name (Nazwa)                    | Zezwalaj na-HTTP-All                                                                     |

Po utworzeniu reguły port 80 jest dozwolone przychodzące z Internetu, ponieważ priorytet reguły jest wyższy niż domyślną regułę zabezpieczeń o nazwie *DenyAllInBound*, która nie zezwala na ruch. Dowiedz się, jak [utworzyć reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Jeżeli różne grupy NSG są skojarzone z interfejsem sieciowym i podsieć, należy utworzyć tę samą zasadę w obu grup NSG.

Podczas procesów Azure ruchu przychodzącego, przetwarzania reguły w grupie NSG skojarzony z podsiecią (jeśli jest skojarzony grupy NSG), a następnie przetwarza zasady w grupie NSG skojarzonego z interfejsem sieciowym. W przypadku grupy NSG skojarzonej interfejsu sieci i podsieci, numer portu musi być otwarty w obu grup NSG dla ruchu nawiązać połączenie z maszyną Wirtualną. Aby ułatwić administracji i komunikacji problemów, firma Microsoft zaleca, że skojarzeniu grupy NSG do podsieci, a nie poszczególnych interfejsów. Maszyn wirtualnych w obrębie podsieci muszą zasady zabezpieczeń, można spowodować, że sieć interfejsy członkami grupy zabezpieczeń aplikacji (ASG) i określ ASG jako źródłowy i docelowy reguły zabezpieczeń. Dowiedz się więcej o [grup zabezpieczeń aplikacji](security-overview.md#application-security-groups).

Jeśli nadal występują problemy z komunikacją, zobacz [zagadnienia](#considerations) i [dodatkowe diagnostyki](#additional-dignosis).

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z łącznością, należy wziąć pod uwagę następujące kwestie:

* Domyślne reguły zabezpieczeń blokowanie dostępu przychodzące z Internetu, a tylko zezwala na ruch przychodzący z sieci wirtualnej. Aby zezwolić na ruch przychodzący z Internetu, należy dodać reguły zabezpieczeń z wyższym priorytetem niż reguły domyślne. Dowiedz się więcej o [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules), lub porady [Dodawanie reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule).
* Jeśli użytkownik ma połączyć za pomocą sieci wirtualnych, domyślnie **VIRTUAL_NETWORK** numer seryjny automatycznie rozszerza zawierały prefiksy połączyć za pomocą sieci wirtualnej. Aby rozwiązać problemy związane z sieci wirtualnej komunikacji równorzędnej, można wyświetlić prefiksy **ExpandedAddressPrefix** listy. Dowiedz się więcej o [sieci wirtualnej komunikacji równorzędnej](virtual-network-peering-overview.md) i [usługi tagi](security-overview.md#service-tags).
* Reguły efektywnym elementem systemu zabezpieczeń są wyświetlane tylko dla interfejsu sieciowego, jeśli istnieje grupa NSG skojarzonych z interfejsu sieciowego maszyny Wirtualnej i lub podsieci, a Jeśli maszyna wirtualna jest w stanie uruchomienia.
* Jeśli nie ma żadnych grup NSG skojarzonej z interfejsem sieciowym lub podsieci i masz [publicznego adresu IP](virtual-network-public-ip-address.md) przypisana do maszyny Wirtualnej, wszystkie porty są otwarte dla ruchu przychodzącego dostęp z i wychodzący dostęp do dowolnego miejsca. Jeśli maszyna wirtualna ma publiczny adres IP, firma Microsoft zaleca stosowanie grupy NSG do podsieci interfejsu sieciowego.

## <a name="additional-diagnosis"></a>Dodatkowe diagnostyki

* Aby uruchomić test szybkie ustalenie, jeśli ruch jest dozwolony na lub z maszyny Wirtualnej, użyj [Sprawdź przepływ IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) możliwości obserwatora sieciowego Azure. Sprawdź przepływ IP informuje, jeśli ruch jest dozwolony lub zabroniony. Odmowy przepływu IP Sprawdź informuje, reguły zabezpieczeń, które odmawia ruchu.
* Jeśli nie ma reguł zabezpieczeń powoduje niepowodzenie łączności sieciowej maszyny Wirtualnej, problem może być z powodu:
  * Oprogramowanie zapory działających w systemie operacyjnym maszyny Wirtualnej
  * Trasy skonfigurowanych dla urządzenia wirtualnego lub ruch lokalny. Można przekierować ruch internetowy do sieci lokalnej za pośrednictwem [tunelowanie wymuszone](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli wymusisz ruchu internetowego tunelu urządzenie wirtualne lub lokalnymi nie można nawiązać połączenia z maszyną wirtualną z Internetu. Aby dowiedzieć się, jak diagnozować problemy trasy, które może utrudniać przepływ ruchu z maszyny Wirtualnej, zobacz [zdiagnozować problem routingu ruchu w sieci maszyny wirtualnej](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat wszystkich zadań, właściwości i ustawienia [sieciowej grupy zabezpieczeń](manage-network-security-group.md#work-with-network-security-groups) i [reguły zabezpieczeń](manage-network-security-group.md#work-with-security-rules).
- Dowiedz się więcej o [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules), [usługi tagi](security-overview.md#service-tags), i [jak Azure przetwarza reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego](security-overview.md#network-security-groups) dla maszyny Wirtualnej.
