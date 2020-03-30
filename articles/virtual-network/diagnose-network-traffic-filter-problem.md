---
title: Diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej | Dokumenty firmy Microsoft
description: Dowiedz się, jak zdiagnozować problem z filtrem ruchu sieciowego maszyny wirtualnej, wyświetlając skuteczne reguły zabezpieczeń dla maszyny wirtualnej.
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
ms.openlocfilehash: 6939ea2497a9f12321e1a6dfb9bf9fbb353bc7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240775"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej

W tym artykule dowiesz się, jak zdiagnozować problem z filtrem ruchu sieciowego, wyświetlając reguły zabezpieczeń sieciowej grupy zabezpieczeń(NSG), które są skuteczne dla maszyny wirtualnej (VM).

Sieci zabezpieczeń umożliwiają kontrolowanie typów ruchu, które przepływają do i z maszyny Wirtualnej. Sieć sieciowej sieciowej sieciowej można skojarzyć z podsiecią w sieci wirtualnej platformy Azure, interfejsie sieciowym dołączonym do maszyny Wirtualnej lub obu. Skuteczne reguły zabezpieczeń stosowane do interfejsu sieciowego są agregacją reguł, które istnieją w sieciowej sieciowej sieciowej skojarzonej z interfejsem sieciowym i podsieci, w których znajduje się interfejs sieciowy. Reguły w różnych grupach sieciowych mogą czasami kolidować ze sobą i wpływać na łączność sieciową maszyny Wirtualnej. Można wyświetlić wszystkie skuteczne reguły zabezpieczeń z sieciowych grup zabezpieczeń, które są stosowane w interfejsach sieciowych maszyny Wirtualnej. Jeśli nie znasz pojęcia dotyczące sieci wirtualnej, interfejsu sieciowego lub sieciowej grupy zabezpieczeń, zobacz [Omówienie sieci wirtualnej](virtual-networks-overview.md), [Interfejs sieci](virtual-network-network-interface.md)i [Omówienie grup zabezpieczeń Sieć](security-overview.md).

## <a name="scenario"></a>Scenariusz

Próbujesz połączyć się z maszyną wirtualną za pośrednictwem portu 80 z Internetu, ale połączenie nie powiedzie się. Aby ustalić, dlaczego nie można uzyskać dostępu do portu 80 z Internetu, można wyświetlić skuteczne reguły zabezpieczeń dla interfejsu sieciowego za pomocą witryny Azure [portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)lub [interfejsu wiersza polecenia platformy Azure.](#diagnose-using-azure-cli)

Kroki, które należy wykonać założyć, że masz istniejącą maszynę wirtualną, aby wyświetlić skuteczne reguły zabezpieczeń dla. Jeśli nie masz istniejącej maszyny Wirtualnej, najpierw wdrożyć [maszynę](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) wirtualną z systemem Linux lub [Windows,](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby wykonać zadania w tym artykule. Przykłady w tym artykule są dla maszyny Wirtualnej o nazwie *myVM* z interfejsem sieciowym o nazwie *myVMVMNic*. Maszyna wirtualna i interfejs sieciowy znajdują się w grupie zasobów o nazwie *myResourceGroup*i znajdują się w regionie *Wschodnie stany USA.* Zmień wartości w krokach, w stosownych przypadkach, dla maszyny Wirtualnej, dla której diagnozujesz problem.

## <a name="diagnose-using-azure-portal"></a>Diagnozowanie przy użyciu witryny Azure portal

1. Zaloguj się do [witryny](https://portal.azure.com) Azure portal przy użyciu konta platformy Azure, które ma [niezbędne uprawnienia.](virtual-network-network-interface.md#permissions)
2. U góry witryny Azure portal wprowadź nazwę maszyny Wirtualnej w polu wyszukiwania. Gdy w wynikach wyszukiwania pojawi się nazwa maszyny Wirtualnej, zaznacz ją.
3. W obszarze **USTAWIENIA**wybierz pozycję **Sieć**, jak pokazano na poniższej ilustracji:

   ![Wyświetlanie reguł zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Reguły widoczne na poprzednim zdjęciu są dla interfejsu sieciowego o nazwie **myVMVMNic**. Zobaczysz, że istnieją **reguły portu przychodzącego** dla interfejsu sieciowego z dwóch różnych grup zabezpieczeń sieci:
   
   - **mySubnetNSG**: Skojarzone z podsiecią, w których znajduje się interfejs sieciowy.
   - **myVMNSG**: Skojarzony z interfejsem sieciowym w maszynie wirtualnej o nazwie **myVMVMNic**.

   Reguła o nazwie **DenyAllInBound** jest to, co uniemożliwia komunikację przychodzącą do maszyny Wirtualnej za pośrednictwem portu 80, z Internetu, zgodnie z opisem w [scenariuszu](#scenario). Reguła zawiera *listę 0.0.0.0/0* dla **SOURCE**, która obejmuje internet. Żadna inna reguła o wyższym priorytecie (niższa liczba) nie zezwala na przychodzące porty 80. Aby zezwolić na przychodzące do portu 80 do maszyny Wirtualnej z Internetu, zobacz [Rozwiązywanie problemu](#resolve-a-problem). Aby dowiedzieć się więcej o regułach zabezpieczeń i sposobie ich zastosowania przez platformę Azure, zobacz [Sieciowe grupy zabezpieczeń](security-overview.md).

   U dołu obrazu znajdują się również **reguły portu wychodzącego**. W obszarze tych znajdują się reguły portów wychodzących dla interfejsu sieciowego. Chociaż obraz pokazuje tylko cztery reguły przychodzące dla każdej sieciowej sieciowej grup płciowych, sieciowe sieciowe mogą mieć wiele więcej niż cztery reguły. Na zdjęciu w **obszarze** **ŹRÓDŁO** i **MIEJSCE DOCELOWE** oraz **AzureLoadBalancer** w obszarze **SOURCE**. **VirtualNetwork** i **AzureLoadBalancer** są [tagami usług.](security-overview.md#service-tags) Tagi usługi reprezentują grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguł zabezpieczeń.

4. Upewnij się, że maszyna wirtualna jest w stanie uruchomionym, a następnie wybierz **opcję Skuteczne reguły zabezpieczeń**, jak pokazano na poprzednim zdjęciu, aby wyświetlić skuteczne reguły zabezpieczeń, pokazane na poniższym rysunku:

   ![Wyświetlanie skutecznych reguł zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Reguły wymienione są takie same, jak w kroku 3, chociaż istnieją różne karty dla sieciowej sieciowej sieciowej skojarzonej z interfejsem sieciowym i podsiecią. Jak widać na zdjęciu, wyświetlane są tylko pierwsze 50 reguł. Aby pobrać plik csv zawierający wszystkie reguły, wybierz opcję **Pobierz**.

   Aby zobaczyć, które prefiksy reprezentuje każdy tag usługi, wybierz regułę, taką jak reguła o nazwie **AllowAzureLoadBalancerInbound**. Na poniższej ilustracji przedstawiono prefiksy tagu usługi **AzureLoadBalancer:**

   ![Wyświetlanie skutecznych reguł zabezpieczeń](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Chociaż tag usługi **AzureLoadBalancer** reprezentuje tylko jeden prefiks, inne tagi usługi reprezentują kilka prefiksów.

5. Poprzednie kroki pokazały reguły zabezpieczeń dla interfejsu sieciowego o nazwie **myVMVMNic**, ale w niektórych poprzednich obrazach był również interfejs sieciowy o nazwie **myVMVMNic2.** Maszyna wirtualna w tym przykładzie ma dwa interfejsy sieciowe dołączone do niego. Skuteczne reguły zabezpieczeń mogą być różne dla każdego interfejsu sieciowego.

   Aby wyświetlić reguły interfejsu sieciowego **myVMVMNic2,** wybierz go. Jak pokazano na poniższym rysunku, interfejs sieciowy ma te same reguły skojarzone z jego podsiecią co interfejs **sieciowy myVMVMNic,** ponieważ oba interfejsy sieciowe znajdują się w tej samej podsieci. Po skojarzeniu sieciowej sieciowej sieciowej z podsieci jej reguły są stosowane do wszystkich interfejsów sieciowych w podsieci.

   ![Wyświetlanie reguł zabezpieczeń](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   W przeciwieństwie do interfejsu **sieciowego myVMVMNic** interfejs **sieciowy myVMVMNic2** nie ma skojarzonej z nim sieciowej grupy zabezpieczeń. Każdy interfejs sieciowy i podsieć mogą mieć zero lub jedną z sieciowej sieciowej skojarzonej z nią. Grupa sieciowych skojarzona z każdym interfejsem sieciowym lub podsiecią może być taka sama lub inna. Tę samą grupę zabezpieczeń sieci można skojarzyć z dowolną ą wiąza siecią sieci.

Chociaż skuteczne reguły zabezpieczeń były wyświetlane za pośrednictwem maszyny Wirtualnej, można również wyświetlić skuteczne reguły zabezpieczeń za pośrednictwem osoby:
- **Interfejs sieciowy**: Dowiedz się, jak [wyświetlić interfejs sieciowy](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Dowiedz się, jak [wyświetlić nsg](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli program PowerShell jest uruchamiany z komputera, potrzebny jest moduł programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, `Connect-AzAccount` musisz również uruchomić, aby zalogować się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia].](virtual-network-network-interface.md#permissions)

Uzyskaj skuteczne reguły zabezpieczeń dla interfejsu sieciowego za pomocą [get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). W poniższym przykładzie pobiera się skuteczne reguły zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic*, który znajduje się w grupie zasobów o nazwie *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Dane wyjściowe są zwracane w formacie json. Aby zrozumieć dane wyjściowe, zobacz [interpretuj wyjście polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, gdy grupa sieciowa jest skojarzona z interfejsem sieciowym, podsiecią, w, w załączeniu lub w obu tych elementach. Maszyna wirtualna musi być w stanie uruchomionym. Maszyna wirtualna może mieć wiele interfejsów sieciowych z różnych sieciowychgów zastosowanych. Podczas rozwiązywania problemów uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zapoznaj się z [dodatkową diagnozą](#additional-diagnosis) i [zagadnieniami.](#considerations)

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny Wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych podłączonych do maszyny Wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymujesz dane wyjściowe podobne do następującego przykładu:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

W poprzednim wyjściu nazwa interfejsu sieciowego to *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnozowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, musisz również uruchomić i zalogować się na platformę Azure przy użyciu konta, które ma [niezbędne uprawnienia.](virtual-network-network-interface.md#permissions)

Uzyskaj skuteczne reguły zabezpieczeń dla interfejsu sieciowego z [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg). W poniższym przykładzie przedstawiono skuteczne reguły zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic,* który znajduje się w grupie zasobów o nazwie *myResourceGroup:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Dane wyjściowe są zwracane w formacie json. Aby zrozumieć dane wyjściowe, zobacz [interpretuj wyjście polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, gdy grupa sieciowa jest skojarzona z interfejsem sieciowym, podsiecią, w, w załączeniu lub w obu tych elementach. Maszyna wirtualna musi być w stanie uruchomionym. Maszyna wirtualna może mieć wiele interfejsów sieciowych z różnych sieciowychgów zastosowanych. Podczas rozwiązywania problemów uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zapoznaj się z [dodatkową diagnozą](#additional-diagnosis) i [zagadnieniami.](#considerations)

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny Wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych podłączonych do maszyny Wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

W obrębie zwróconego danych wyjściowych są widoczne informacje podobne do następującego przykładu:

```output
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

W poprzednim wyjściu nazwa interfejsu sieciowego to *interfejs myVMVMNic*.

## <a name="interpret-command-output"></a>Interpretowanie wyjścia polecenia

Niezależnie od tego, czy do zdiagnozowania problemu użyto programu [PowerShell](#diagnose-using-powershell), czy [interfejsu wiersza polecenia platformy Azure,](#diagnose-using-azure-cli) otrzymasz dane wyjściowe zawierające następujące informacje:

- **NetworkSecurityGroup**: Identyfikator sieciowej grupy zabezpieczeń.
- **Skojarzenie**: Czy grupa zabezpieczeń sieci jest skojarzona z *sieciąInterface* lub *podsieci*. Jeśli sieciowej grupy zabezpieczeń jest skojarzony z obu, dane wyjściowe są zwracane z **NetworkSecurityGroup**, **Association**i **EffectiveSecurityRules**, dla każdego NSG. Jeśli sieciowej sieciowej sieciowej jest skojarzony lub odłączony bezpośrednio przed uruchomieniem polecenia, aby wyświetlić skuteczne reguły zabezpieczeń, może być konieczne odczekanie kilku sekund, aby zmiana odzwierciedliła się w danych wyjściowych polecenia.
- **EffectiveSecurityRules:** Wyjaśnienie każdej właściwości jest szczegółowo opisane w [tworzenie reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Nazwy reguł poprzedzone *defaultSecurityRules/* są domyślnymi regułami zabezpieczeń, które istnieją w każdej sieci nsg. Nazwy reguł poprzedzone *securityRules/* są regułami, które zostały utworzone. Reguły określające [tag usługi](security-overview.md#service-tags), takich jak **Internet**, **VirtualNetwork**i **AzureLoadBalancer** dla **destinationAddressPrefix** lub **sourceAddressPrefix** właściwości, mają również wartości dla **expandedDestinationAddressPrefix** właściwości. Właściwość **expandedDestinationAddressPrefix** zawiera listę wszystkich prefiksów adresów reprezentowanych przez tag usługi.

Jeśli widzisz zduplikowane reguły wymienione w danych wyjściowych, to dlatego, że sieciowej listy sieciowej jest skojarzony zarówno do interfejsu sieciowego i podsieci. Obie sieci zabezpieczeń sieciowych mają te same reguły domyślne i mogą mieć dodatkowe reguły duplikatów, jeśli utworzono własne reguły, które są takie same w obu grupach sieciowych.

Reguła o nazwie **defaultSecurityRules/DenyAllInBound** jest tym, co uniemożliwia komunikację przychodzącą do maszyny Wirtualnej za pośrednictwem portu 80 z Internetu, zgodnie z opisem w [scenariuszu](#scenario). Żadna inna reguła o wyższym priorytecie (niższa liczba) nie zezwala na przychodzące do portu 80 z Internetu.

## <a name="resolve-a-problem"></a>Rozwiązywanie problemu

Niezależnie od tego, czy używasz witryny Azure [portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)lub [interfejsu wiersza polecenia platformy Azure](#diagnose-using-azure-cli) do diagnozowania problemu przedstawionego w [scenariuszu](#scenario) w tym artykule, rozwiązaniem jest utworzenie reguły zabezpieczeń sieciowych z następującymi właściwościami:

| Właściwość                | Wartość                                                                              |
|---------                |---------                                                                           |
| Element źródłowy                  | Dowolne                                                                                |
| Zakresy portów źródłowych      | Dowolne                                                                                |
| Element docelowy             | Adres IP maszyny Wirtualnej, zakres adresów IP lub wszystkie adresy w podsieci. |
| Zakresy portów docelowych | 80                                                                                 |
| Protocol (Protokół)                | TCP                                                                                |
| Akcja                  | Zezwalaj                                                                              |
| Priorytet                | 100                                                                                |
| Nazwa                    | Zezwalaj-HTTP-Wszystko                                                                     |

Po utworzeniu reguły port 80 jest dozwolony przychodzących z Internetu, ponieważ priorytet reguły jest wyższy niż domyślna reguła zabezpieczeń o nazwie *DenyAllInBound*, która odmawia ruchu. Dowiedz się, jak [utworzyć regułę zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Jeśli różne sieciowe sieciowe sieciowe są skojarzone zarówno z interfejsem sieciowym, jak i z podsiecią, należy utworzyć tę samą regułę w obu sieciach zabezpieczeń.

Gdy platforma Azure przetwarza ruch przychodzący, przetwarza reguły w sieciowej grupowej skojarzonej z podsiecią (jeśli istnieje skojarzona sieć sieciowa), a następnie przetwarza reguły w sieciowej sieciowej grupce skojarzonej z interfejsem sieciowym. Jeśli istnieje sieciowej sieciowej grupki zabezpieczeń skojarzone z interfejsem sieciowym i podsieci, port musi być otwarty w obu sieciowych grup zabezpieczeń, aby ruch dotarł do maszyny Wirtualnej. Aby ułatwić zarządzanie i problemy z komunikacją, zaleca się skojarzenie sieciowej sieciowej sieciowej z podsiecią, a nie z poszczególnymi interfejsami sieciowymi. Jeśli maszyny wirtualne w podsieci wymagają różnych reguł zabezpieczeń, można wprowadzić interfejsy sieciowe do członków grupy zabezpieczeń aplikacji (ASG) i określić asg jako źródło i miejsce docelowe reguły zabezpieczeń. Dowiedz się więcej o [grupach zabezpieczeń aplikacji](security-overview.md#application-security-groups).

Jeśli nadal występują problemy z komunikacją, zobacz [Zagadnienia](#considerations) i Dodatkowa diagnoza.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z łącznością należy wziąć pod uwagę następujące kwestie:

* Domyślne reguły zabezpieczeń blokują dostęp przychodzący z Internetu i zezwalają tylko na ruch przychodzący z sieci wirtualnej. Aby zezwolić na ruch przychodzący z Internetu, dodaj reguły zabezpieczeń o wyższym priorytecie niż reguły domyślne. Dowiedz się więcej o [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules)lub o [dodaniu reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule).
* Jeśli sieci wirtualne są równorzędne, domyślnie VIRTUAL_NETWORK tag **usługi** automatycznie rozszerza się, aby uwzględnić prefiksy dla sieci wirtualnych równorzędnych. Aby rozwiązać wszelkie problemy związane z komunikacją równorzędową sieci wirtualnej, można wyświetlić prefiksy na liście **ExpandedAddressPrefix.** Dowiedz się więcej o [komunikacji równorzędnej](virtual-network-peering-overview.md) sieci wirtualnej i [tagach usług](security-overview.md#service-tags).
* Skuteczne reguły zabezpieczeń są wyświetlane tylko dla interfejsu sieciowego, jeśli istnieje sieciowej ochrony sieciowej skojarzonej z interfejsem sieciowym maszyny Wirtualnej i lub podsieci, a jeśli maszyna wirtualna jest w stanie uruchomionym.
* Jeśli z interfejsem sieciowym lub podsiecią nie są skojarzone żadne sieciowe sieciowe, a do maszyny Wirtualnej jest przypisany [publiczny adres IP,](virtual-network-public-ip-address.md) wszystkie porty są otwarte dla dostępu przychodzącego z dowolnego miejsca i dostępu wychodzącego. Jeśli maszyna wirtualna ma publiczny adres IP, zaleca się zastosowanie sieciowej sieciowej do podsieci interfejsu sieciowego.

## <a name="additional-diagnosis"></a>Dodatkowa diagnoza

* Aby uruchomić szybki test, aby ustalić, czy ruch jest dozwolony do lub z maszyny Wirtualnej, należy użyć możliwości [weryfikacji przepływu ip](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) usługi Azure Network Watcher. Weryfikacja przepływu IP informuje, czy ruch jest dozwolony lub zabroniony. Jeśli odmowa, weryfikacja przepływu IP informuje, która reguła zabezpieczeń odmawia ruchu.
* Jeśli nie ma żadnych reguł zabezpieczeń powodujących niepowodzenie łączności sieciowej maszyny Wirtualnej, problem może być spowodowany:
  * Oprogramowanie zapory uruchomione w systemie operacyjnym maszyny Wirtualnej
  * Trasy skonfigurowane dla urządzeń wirtualnych lub ruchu lokalnego. Ruch internetowy można przekierować do sieci lokalnej za pośrednictwem [wymuszonego tunelowania.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Jeśli wymusisz ruch internetowy tunelu do urządzenia wirtualnego lub lokalnego, może nie być możliwe połączenie z maszyną wirtualną z Internetu. Aby dowiedzieć się, jak zdiagnozować problemy z trasą, które mogą utrudniać przepływ ruchu z maszyny Wirtualnej, zobacz [Diagnozowanie problemu routingu ruchu sieciowego maszyny wirtualnej](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wszystkich zadaniach, właściwościach i ustawieniach [sieciowej grupy zabezpieczeń](manage-network-security-group.md#work-with-network-security-groups) i [reguł zabezpieczeń](manage-network-security-group.md#work-with-security-rules).
- Dowiedz się więcej o [domyślnych regułach zabezpieczeń,](security-overview.md#default-security-rules) [tagach usług](security-overview.md#service-tags)i [o tym, jak platforma Azure przetwarza reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego](security-overview.md#network-security-groups) dla maszyny Wirtualnej.
