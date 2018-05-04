---
title: Weryfikacja ruchu Azure sieci obserwatora IP przepływ weryfikacji - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób sprawdzania, jeśli ruch do i z maszyny wirtualnej jest dozwolona lub odmowa przy użyciu wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8681118e55d8ddddf17ebac3bae63486446c70e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Sprawdź, czy ruch jest dozwolony lub odmowa dostępu do lub z maszyny Wirtualnej z przepływem Sprawdź IP składnik Azure obserwatora sieciowego

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [Program PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Interfejs API Azure REST](network-watcher-check-ip-flow-verify-rest.md)


Przepływ IP Sprawdź jest funkcją obserwatora sieciowego, który pozwala sprawdzić, jeśli ruch jest dozwolony na lub z maszyny wirtualnej. Ten scenariusz przydaje się pobrać bieżący stan tego, czy maszyny wirtualne mogą komunikować się z zasobu zewnętrznego i wewnętrznej bazy danych. Sprawdź przepływ IP pozwala sprawdzić, czy reguł sieciowej grupy zabezpieczeń (NSG) są prawidłowo skonfigurowane i rozwiązywania problemów z przepływów, które są blokowane przez reguły NSG. Inną przyczyną przy użyciu protokołu IP przepływu Sprawdź jest zapewnienie ruchu, który ma być zablokowana jest prawidłowo blokowane przez grupy NSG.

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) do tworzenia obserwatora sieciowego lub mieć istniejące wystąpienie obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

W tym scenariuszu IP przepływu Sprawdź, aby sprawdzić, czy maszyna wirtualna może komunikować się znanego adresu IP usługi Bing. Jeśli ruch jest niedozwolone, zwraca zasady zabezpieczeń, która odrzuciła ten ruch. Aby dowiedzieć się więcej o Sprawdź przepływ IP, odwiedź stronę [przepływu IP Sprawdź — omówienie](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Przepływ IP Sprawdź testy ruchu do lub z adresu IP na maszynie wirtualnej lub zdalnego miejsca docelowego. Identyfikator maszyny wirtualnej jest wymagany dla polecenia cmdlet. Jeśli znasz już identyfikator maszyny wirtualnej do użycia, możesz pominąć ten krok.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Pobierz karty interfejsu Sieciowego

Wymagany jest adres IP karty sieciowej na maszynie wirtualnej. Pobrać kart sieciowych maszyny wirtualnej przy użyciu polecenia, który jest zgodny. Jeśli znasz już adres IP, który ma zostać przetestowana na maszynie wirtualnej, można pominąć ten krok.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Sprawdź uruchomienia przepływu IP

Uruchom `network watcher ip-flow-verify` polecenia cmdlet, aby przetestować ruchu. W tym przykładzie używany jest pierwszy adres IP z pierwszej karty Sieciowej:

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> Przepływ IP Sprawdź wymaga, aby zasobu maszyny Wirtualnej jest przydzielona do uruchomienia.

## <a name="review-results"></a>Przejrzyj wyniki

Po uruchomieniu `network watcher ip-flow-verify` wyniki są zwracane, w poniższym przykładzie jest wyników zwróconych z poprzedniego kroku.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli ruch jest blokowane i nie należy, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń zdefiniowane.

Dowiedz się, jak inspekcji ustawienia grupy NSG, odwiedzając [inspekcji sieci zabezpieczeń grupy (NSG) z obserwatora sieciowego](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
