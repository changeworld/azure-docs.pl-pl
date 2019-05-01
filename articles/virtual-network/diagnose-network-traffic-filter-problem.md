---
title: Diagnozowanie problemu z filtrowaniem ruchu maszyny wirtualnej sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, jak diagnozowanie problemu z filtrowaniem ruchu maszyny wirtualnej sieci, wyświetlając obowiązujących reguł zabezpieczeń dla maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: f84e8a24e8f28cdccc987afbd1449cb17422ce0c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712669"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnozowanie problemu z filtrowaniem ruchu maszyny wirtualnej sieci

W tym artykule dowiesz się, jak diagnozować problemu z filtrowaniem ruchu sieciowego, wyświetlając reguły sieciowej grupy zabezpieczeń (NSG) zabezpieczeń, które obowiązują dla maszyny wirtualnej (VM).

Sieciowe grupy zabezpieczeń umożliwiają kontrolowanie typów ruchu tego przepływu do i z maszyny Wirtualnej. Możesz skojarzyć sieciową grupę zabezpieczeń do podsieci w sieci wirtualnej platformy Azure, interfejsu sieciowego dołączonego do maszyny Wirtualnej lub obu. Efektywne reguły zabezpieczeń stosowane do interfejsu sieciowego są zagregowane zasady, które istnieją w sieciowej grupie zabezpieczeń skojarzoną z interfejsem sieciowym i podsiecią interfejsu sieciowego jest w. Reguły w różne sieciowe grupy zabezpieczeń mogą czasami kolidują ze sobą i mieć wpływ na łączność sieciową z maszyny Wirtualnej. Można wyświetlić wszystkich obowiązujących reguł zabezpieczeń z sieciowymi grupami zabezpieczeń, które są stosowane w interfejsach sieciowych maszyny Wirtualnej. Jeśli nie jesteś zaznajomiony z siecią wirtualną, interfejsu sieciowego lub pojęcia sieciowej grupy zabezpieczeń, zobacz [Omówienie usługi Virtual network](virtual-networks-overview.md), [interfejs sieciowy](virtual-network-network-interface.md), i [Omówieniesieciowychgrupzabezpieczeń](security-overview.md).

## <a name="scenario"></a>Scenariusz

Spróbuj nawiązać połączenie z maszyną wirtualną za pośrednictwem portu 80 z Internetu, ale połączenie nie powiedzie się. Aby ustalić, dlaczego nie masz dostępu do portu 80 z Internetu, można wyświetlić obowiązujących reguł zabezpieczeń dla interfejsu sieciowego przy użyciu platformy Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), lub [wiersza polecenia platformy Azure](#diagnose-using-azure-cli).

Poniższe kroki przyjęto założenie, że masz istniejącej maszyny Wirtualnej, aby wyświetlić obowiązujących reguł zabezpieczeń dla. Jeśli nie masz istniejącej maszyny Wirtualnej, najpierw wdrożyć [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny Wirtualnej w celu wykonania zadań w tym artykule. Przykłady w niniejszym artykule są dla maszyny Wirtualnej o nazwie *myVM* z interfejsem sieciowym o nazwie *myVMVMNic*. Interfejs maszyny Wirtualnej i sieci znajdują się w grupie zasobów o nazwie *myResourceGroup*, a w *wschodnie stany USA* regionu. Zmień wartości w krokach, odpowiednio do diagnozowania problemów dla maszyny Wirtualnej.

## <a name="diagnose-using-azure-portal"></a>Diagnozować przy użyciu witryny Azure portal

1. Zaloguj się do platformy Azure [portal](https://portal.azure.com) przy użyciu konta Azure, która ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).
2. W górnej części witryny Azure portal wprowadź nazwę maszyny Wirtualnej w polu wyszukiwania. Gdy nazwa maszyny Wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
3. W obszarze **ustawienia**, wybierz opcję **sieć**, jak pokazano na poniższej ilustracji:

   ![Wyświetl reguły zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Dla karty sieciowej o nazwie dostępne są następujące reguły zostanie wyświetlony na liście na poprzedniej ilustracji **myVMVMNic**. Zobaczysz, że istnieją **reguły portów wejściowych** dla interfejsu sieciowego z dwóch różnych sieciowych grup zabezpieczeń:
   
   - **mySubnetNSG**: Skojarzoną z podsiecią, należącym do interfejsu sieciowego.
   - **myVMNSG**: Skojarzone z interfejsem sieciowym na maszynie Wirtualnej o nazwie **myVMVMNic**.

   Reguły o nazwie **DenyAllInBound** to, co uniemożliwia komunikacji przychodzącej z maszyną wirtualną za pośrednictwem portu 80, przez Internet, zgodnie z opisem w [scenariusza](#scenario). Wyświetla reguły *0.0.0.0/0* dla **źródła**, w tym Internetu. Żadna inna reguła z wyższym priorytetem (niższym numerem) zezwala na użycie portu 80 dla ruchu przychodzącego. Aby zezwolić na porcie 80 dla ruchu przychodzącego do maszyny Wirtualnej z Internetu, zobacz [rozwiązać problem](#resolve-a-problem). Aby dowiedzieć się więcej na temat reguł zabezpieczeń i jak Azure zastosuje je, zobacz [sieciowe grupy zabezpieczeń](security-overview.md).

   W dolnej części obraz powinien być też widoczny **reguł dla ruchu WYCHODZĄCEGO portu**. Zgodnie z tym są reguły portów wyjściowych dla interfejsu sieciowego. Chociaż na ilustracji przedstawiono tylko cztery reguły dla ruchu przychodzącego dla poszczególnych sieciowych grupach zabezpieczeń, sieciowych grup zabezpieczeń może mieć wiele więcej niż cztery reguły. Na ilustracji widać **VirtualNetwork** w obszarze **źródła** i **DOCELOWY** i **AzureLoadBalancer** w obszarze  **ŹRÓDŁO**. **Sieć wirtualna** i **AzureLoadBalancer** są [tagów usług](security-overview.md#service-tags). Tagi usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń.

4. Upewnij się, że maszyna wirtualna jest uruchomiona, stan, a następnie wybierz **obowiązujących reguł zabezpieczeń**, jak pokazano na poprzedniej ilustracji, aby zobaczyć obowiązujących reguł zabezpieczeń, pokazane na poniższej ilustracji:

   ![Wyświetlanie obowiązujących reguł zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Reguły na liście są takie same jak opisany w kroku 3, chociaż są różne karty sieciowej grupy zabezpieczeń skojarzone z interfejsu sieci i podsieci. Jak widać na ilustracji, są wyświetlane tylko pierwszych 50 reguł. Aby pobrać plik CSV, który zawiera wszystkie reguły, wybierz **Pobierz**.

   Aby zobaczyć, które prefiksy każdy tag usługi reprezentuje, wybierz regułę, takie jak reguły o nazwie **AllowAzureLoadBalancerInbound**. Na poniższej ilustracji przedstawiono prefiksów **AzureLoadBalancer** tag usługi:

   ![Wyświetlanie obowiązujących reguł zabezpieczeń](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Chociaż **AzureLoadBalancer** tag usługi reprezentuje tylko jedną prefiksu, inne tagi usługi reprezentują kilka prefiksów.

5. Poprzednie kroki wykazało, że reguły zabezpieczeń dla interfejsu sieciowego o nazwie **myVMVMNic**, ale także w tym samouczku interfejs sieciowy o nazwie **myVMVMNic2** w niektórych poprzednich obrazów. Maszyna wirtualna, w tym przykładzie ma dwa interfejsy sieciowe podłączone do niego. Efektywne reguły zabezpieczeń mogą być różne dla każdego interfejsu sieciowego.

   Aby wyświetlić zasady **myVMVMNic2** interfejs sieciowy, wybierz ją. Jak pokazano na ilustracji poniżej, interfejs sieciowy ma te same zasady, które są skojarzone z jego podsieci **myVMVMNic** sieci interfejsu, ponieważ oba interfejsy sieciowe znajdują się w tej samej podsieci. Po skojarzeniu sieciowej grupy zabezpieczeń z podsiecią jego reguły są stosowane do wszystkich interfejsów sieciowych w podsieci.

   ![Wyświetl reguły zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   W odróżnieniu od **myVMVMNic** interfejs sieciowy **myVMVMNic2** interfejs sieciowy ma sieciową grupę zabezpieczeń skojarzoną do niego. Każdy interfejs sieciowy i podsieć może mieć zero lub jedną sieciową grupę zabezpieczeń powiązany. Każdy interfejs sieciowy skojarzonej sieciowej grupy zabezpieczeń lub podsieć może być taka sama lub innej. Podczas wybierania, można skojarzyć tej samej grupy zabezpieczeń sieci na dowolną liczbę interfejsów sieciowych i podsieci.

Chociaż obowiązujących reguł zabezpieczeń były wyświetlane za pomocą maszyny Wirtualnej, można również wyświetlić obowiązujących reguł zabezpieczeń przez osobę:
- **Interfejs sieciowy**: Dowiedz się, jak [wyświetlania interfejsu sieciowego](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Dowiedz się, jak [wyświetlić sieciowej grupy zabezpieczeń](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy modułu Azure PowerShell w wersji 1.0.0 lub nowszym. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, trzeba będzie również uruchomić `Connect-AzAccount` zalogować się przy użyciu konta które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions)].

Pobierz efektywne reguły zabezpieczeń dla interfejsu sieciowego z [Get AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). Poniższy przykład pobiera obowiązujących reguł zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic*, która jest w grupie zasobów o nazwie *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Dane wyjściowe są zwracane w formacie json. Aby zrozumieć dane wyjściowe, zobacz [interpretować dane wyjściowe polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, jeśli sieciowa grupa zabezpieczeń jest skojarzona z interfejsu sieciowego i/lub podsieci, w której znajduje się interfejs sieciowy. Maszyna wirtualna musi być w stanie uruchomienia. Maszyna wirtualna może mieć wiele interfejsów sieciowych, za pomocą różne sieciowe grupy zabezpieczeń, które są stosowane. Podczas rozwiązywania problemów, należy uruchomić polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z połączeniem, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwy interfejs sieciowy jest podłączony do maszyny wirtualnej, poniższe polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
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

Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` i zaloguj się do platformy Azure za pomocą konta mającego [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz efektywne reguły zabezpieczeń dla interfejsu sieciowego z [az network nic list — zacznie obowiązywać od — sieciowej grupy zabezpieczeń](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Poniższy przykład pobiera obowiązujących reguł zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic* jest w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Dane wyjściowe są zwracane w formacie json. Aby zrozumieć dane wyjściowe, zobacz [interpretować dane wyjściowe polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, jeśli sieciowa grupa zabezpieczeń jest skojarzona z interfejsu sieciowego i/lub podsieci, w której znajduje się interfejs sieciowy. Maszyna wirtualna musi być w stanie uruchomienia. Maszyna wirtualna może mieć wiele interfejsów sieciowych, za pomocą różne sieciowe grupy zabezpieczeń, które są stosowane. Podczas rozwiązywania problemów, należy uruchomić polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z połączeniem, zobacz [dodatkowe diagnostyki](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy karty sieciowej, ale znasz nazwy interfejs sieciowy jest podłączony do maszyny wirtualnej, poniższe polecenia Zwróć identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny Wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

W ramach zwróconych danych wyjściowych zostaną wyświetlone informacje podobne do poniższego przykładu:

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

W danych wyjściowych poprzedniej, nazwa interfejsu sieciowego jest *interfejsu myVMVMNic*.

## <a name="interpret-command-output"></a>Interpretowanie danych wyjściowych polecenia

Niezależnie od tego, czy używane [PowerShell](#diagnose-using-powershell), lub [wiersza polecenia platformy Azure](#diagnose-using-azure-cli) Aby zdiagnozować problem, otrzymasz dane wyjściowe, który zawiera następujące informacje:

- **NetworkSecurityGroup**: Identyfikator sieciowej grupy zabezpieczeń.
- **Skojarzenie**: Czy sieciowa grupa zabezpieczeń jest skojarzona z *interfejsu sieciowego* lub *podsieci*. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z obu, dane wyjściowe zostaną zwrócone z **NetworkSecurityGroup**, **skojarzenia**, i **EffectiveSecurityRules**, dla każdej sieciowej grupy zabezpieczeń. Jeśli sieciowa grupa zabezpieczeń jest skojarzony lub odłączyć od razu, przed uruchomieniem polecenia, aby wyświetlić obowiązujących reguł zabezpieczeń, może być konieczne odczekaj kilka sekund zmiany w celu uwzględnienia w danych wyjściowych polecenia.
- **EffectiveSecurityRules**: Wyjaśnienie każda właściwość została szczegółowo opisana w [Utwórz regułę zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Nazwy poprzedzone znakiem reguły *defaultSecurityRules /* są domyślne reguły zabezpieczeń, które istnieją w każdej sieciowej grupy zabezpieczeń. Nazwy poprzedzone znakiem reguły *securityRules /* to reguły, które zostały utworzone. Reguły określające [tag usługi](security-overview.md#service-tags), takich jak **Internet**, **VirtualNetwork**, i **AzureLoadBalancer** dla  **destinationAddressPrefix** lub **element sourceAddressPrefix** właściwości również mają wartości **expandedDestinationAddressPrefix** właściwości. **ExpandedDestinationAddressPrefix** właściwość zawiera listę wszystkich prefiksów adresów, reprezentowane przez tag usługi.

Jeśli widzisz zduplikowane reguły w wymienionych w danych wyjściowych, jest to, ponieważ sieciowa grupa zabezpieczeń jest skojarzona z interfejsu sieciowego i podsieci. Zarówno sieciowe grupy zabezpieczeń mają te same reguły domyślnej i może mieć dodatkowe reguły zduplikowany, jeśli utworzono własne reguły, które są takie same w obu sieciowych grup zabezpieczeń.

Reguły o nazwie **defaultSecurityRules/DenyAllInBound** to, co uniemożliwia komunikacji przychodzącej z maszyną wirtualną za pośrednictwem portu 80, przez Internet, zgodnie z opisem w [scenariusza](#scenario). Żadna inna reguła z wyższym priorytetem (niższym numerem) zezwala na użycie portu 80 dla ruchu przychodzącego z Internetu.

## <a name="resolve-a-problem"></a>Rozwiązywanie problemów

Czy korzystasz z platformy Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), lub [wiersza polecenia platformy Azure](#diagnose-using-azure-cli) Aby zdiagnozować problem, znajdujące się w [scenariusza](#scenario) w tym Artykuł rozwiązaniem jest utworzenie reguły zabezpieczeń sieci z następującymi właściwościami:

| Właściwość                | Wartość                                                                              |
|---------                |---------                                                                           |
| Element źródłowy                  | Dowolne                                                                                |
| Zakresy portów źródłowych      | Dowolne                                                                                |
| Element docelowy             | Adres IP maszyny Wirtualnej, zakres adresów IP lub wszystkie adresy w podsieci. |
| Zakresy portów docelowych | 80                                                                                 |
| Protocol (Protokół)                | TCP                                                                                |
| Akcja                  | Zezwalaj                                                                              |
| Priorytet                | 100                                                                                |
| Name (Nazwa)                    | Allow-HTTP-All                                                                     |

Po utworzeniu reguły, port 80 jest dozwolony dla ruchu przychodzącego z Internetu, ponieważ priorytet reguły jest wyższy niż domyślna reguła zabezpieczeń o nazwie *DenyAllInBound*, który nie zezwala na ruch. Dowiedz się, jak [Utwórz regułę zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Jeśli różne sieciowe grupy zabezpieczeń są skojarzone z interfejsu sieciowego i podsieci, należy utworzyć tę samą regułę w obu sieciowych grup zabezpieczeń.

Gdy ruch przychodzący przetwarzanych przez platformę Azure, są przetwarzane reguły w sieciowej grupie zabezpieczeń skojarzoną z podsiecią (jeśli istnieje skojarzonej sieciowej grupy zabezpieczeń), a następnie są przetwarzane reguły w sieciowej grupie zabezpieczeń skojarzonych z interfejsem sieciowym. W przypadku sieciowa grupa zabezpieczeń skojarzona interfejsie sieciowym i podsiecią, numer portu musi być otwarty w obu grupach zabezpieczeń sieci dla ruchu sieciowego do maszyny Wirtualnej. Działają nawet pod dużym problemów administracji i komunikacji, firma Microsoft zaleca, aby skojarzyć sieciową grupę zabezpieczeń do podsieci, a nie pojedynczymi interfejsami sieciowymi. Maszyny wirtualne w podsieci muszą reguł zabezpieczeń, można spowodować, że sieć interfejsów członkami grupy zabezpieczeń aplikacji (ASG) i określić ASG jako źródło i miejsce docelowe regułę zabezpieczeń. Dowiedz się więcej o [grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups).

Jeśli nadal występują problemy z komunikacją, zobacz [zagadnienia](#considerations) i dodatkowe diagnostyki.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z łącznością, należy wziąć pod uwagę następujące kwestie:

* Domyślnych reguł zabezpieczeń blokowana dla ruchu przychodzącego z Internetu, a tylko zezwalać na ruch przychodzący z siecią wirtualną. Aby zezwolić na ruch przychodzący z Internetu, należy dodać reguły zabezpieczeń o wyższym priorytecie niż reguły domyślne. Dowiedz się więcej o [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules), lub jak [Dodawanie reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule).
* Jeśli ma połączenia równorzędnego sieci wirtualne, domyślnie **VIRTUAL_NETWORK** tag usługi automatycznie rozszerza się, aby uwzględnić prefiksów dla wirtualnych sieciach równorzędnych. Aby rozwiązać problemy związane z wirtualnymi sieciami równorzędnymi, można wyświetlić prefiksy **ExpandedAddressPrefix** listy. Dowiedz się więcej o [wirtualne sieci równorzędne](virtual-network-peering-overview.md) i [tagów usług](security-overview.md#service-tags).
* Efektywne reguły zabezpieczeń są wyświetlane tylko dla interfejsu sieciowego, jeśli sieciowa grupa zabezpieczeń skojarzona z interfejsem sieciowym maszyny Wirtualnej, a lub podsieci, a Jeśli maszyna wirtualna jest w stanie uruchomienia.
* Jeśli nie ma żadnych grup zabezpieczeń sieci skojarzonych z interfejsem sieciowym lub podsieci i masz [publiczny adres IP](virtual-network-public-ip-address.md) przypisane do maszyny Wirtualnej, wszystkie porty są otwarte dla ruchu przychodzącego dostęp z i dostęp ruchu wychodzącego do dowolnego miejsca. Jeśli maszyna wirtualna ma publiczny adres IP, firma Microsoft zaleca stosowanie sieciowej grupy zabezpieczeń z podsiecią interfejsu sieciowego.

## <a name="additional-diagnosis"></a>Dodatkowe diagnostyki

* Aby uruchomić szybkie testowe, aby określić, jeśli ruch jest dozwolony do lub z maszyny Wirtualnej, użyj [weryfikowanie przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) możliwości usługi Azure Network Watcher. Weryfikowanie przepływu protokołu IP informuje, czy ruch jest dozwolony. Odmowy weryfikowanie przepływu protokołu IP, informujący o tym, reguły zabezpieczeń, które odmawia ruchu sieciowego.
* Jeśli nie istnieją żadne reguły zabezpieczeń, powodując łączności sieciowej maszyny Wirtualnej nie powiedzie się, problem może być ze względu na:
  * Oprogramowanie zapory, w ramach systemu operacyjnego maszyny Wirtualnej
  * Trasy skonfigurowane dla urządzeń wirtualnych lub ruch lokalny. Ruch internetowy może zostać przekierowany do sieci lokalnej za pośrednictwem [tunelowania wymuszanego](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli wymusisz tunelu ruch internetowy do urządzenia wirtualnego lub w środowisku lokalnym, nie można nawiązać połączenia z maszyną Wirtualną z Internetu. Aby dowiedzieć się, jak diagnozować problemy trasy, które mogą zakłócać przepływ ruchu z maszyny Wirtualnej, zobacz [diagnozowanie problemu routingu ruchu w sieci maszyny wirtualnej](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o wszystkich zadań, właściwości i ustawienia dla [sieciowej grupy zabezpieczeń](manage-network-security-group.md#work-with-network-security-groups) i [reguły zabezpieczeń](manage-network-security-group.md#work-with-security-rules).
- Dowiedz się więcej o [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules), [tagów usług](security-overview.md#service-tags), i [przetwarzaniu reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego Azure](security-overview.md#network-security-groups) dla maszyny Wirtualnej.
