---
title: Sprawdź ruchu z adresem IP obserwatora sieci Azure przepływ weryfikacji - portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób sprawdzania, jeśli ruch do i z maszyny wirtualnej jest dozwolona lub odmowa
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b6d929f025c8b95709b7c0eb28ee78310e5f12a5
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


Sprawdź przepływ IP jest funkcją obserwatora sieciowego, który pozwala sprawdzić, jeśli ruch jest dozwolony na lub z maszyny wirtualnej. Sprawdzanie poprawności może zostać uruchomione dla ruchu przychodzącego lub wychodzącego. Ten scenariusz przydaje się pobrać bieżący stan tego, czy maszyna wirtualna może komunikować się zasób zewnętrzny lub innego zasobu. Sprawdź przepływ IP pozwala sprawdzić, czy reguł sieciowej grupy zabezpieczeń (NSG) są prawidłowo skonfigurowane i rozwiązywania problemów z przepływów, które są blokowane przez reguły NSG. Inną przyczyną przy użyciu protokołu IP przepływu Sprawdź jest zapewnienie ruchu, który ma być zablokowana jest prawidłowo blokowane przez grupy NSG.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) do tworzenia obserwatora sieciowego lub mieć istniejące wystąpienie obserwatora sieciowego. Scenariusz również założono, że grupa zasobów o prawidłową maszynę wirtualną istnieje ma być używany.

## <a name="scenario"></a>Scenariusz

W tym scenariuszu IP przepływu Sprawdź, aby sprawdzić, jeśli maszyny wirtualnej mogą komunikować się z innego komputera za pośrednictwem portu 443. Jeśli ruch jest niedozwolone, zwraca zasady zabezpieczeń, która odrzuciła ten ruch. Aby dowiedzieć się więcej o Sprawdź przepływ IP, odwiedź stronę [przepływu IP Sprawdź — omówienie](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Sprawdź uruchomienia przepływu IP

Przejdź do Twojej obserwatora sieciowego i kliknij **Sprawdź przepływ IP**. Wybierz maszynę wirtualną i chcesz sprawdzić ruch z interfejsu sieciowego. Wprowadź wszelkie dodatkowe informacje filtrowania, a następnie kliknij przycisk **Sprawdź**.

Po kliknięciu **Sprawdź**, zaznaczono przepływem na podstawie podanych kryteriów. Wynik jest **dozwolony dostęp do** lub **odmowa dostępu**. W przypadku odmowy dostępu jest dostarczany zasady grupy zabezpieczeń sieci (NSG) i zabezpieczeń, która blokowania ruchu. Jeśli odmowa ruchu jest oczekiwane zachowanie, reguła zakończyło się pomyślnie.

> [!NOTE]
> Sprawdź przepływ IP wymaga, aby zasobu maszyny Wirtualnej jest przydzielona.

Jak widać na poniższej ilustracji, zezwolono na ruch wychodzący protokołu HTTPS.

![Przepływ IP Sprawdź — omówienie][1]

Jak pokazano na poniższej ilustracji, ruch jest zmieniana na ruchu przychodzącego i portu wejściowego do 123. Ruch teraz odmówiono, komunikat "Odmowa dostępu" jest dostępne oraz sieci zabezpieczeń grupy i zabezpieczeń reguła odmowy ruchu.

![wyniki przepływu IP][2]

## <a name="next-steps"></a>Kolejne kroki

Jeśli ruch jest blokowane i nie należy, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń zdefiniowane.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













