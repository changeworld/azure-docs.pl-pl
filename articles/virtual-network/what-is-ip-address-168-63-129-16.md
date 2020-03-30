---
title: Co to jest adres IP 168.63.129.16? | Microsoft Docs
description: Dowiedz się więcej o adresie IP 168.63.129.16 i o tym, jak działa z Twoimi zasobami.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114362"
---
# <a name="what-is-ip-address-1686312916"></a>Co to jest adres IP 168.63.129.16?

Adres IP 168.63.129.16 to wirtualny publiczny adres IP, który jest używany w celu ułatwienia kanału komunikacji do zasobów platformy Azure. Klienci mogą definiować dowolną przestrzeń adresową dla swojej prywatnej sieci wirtualnej na platformie Azure. W związku z tym zasoby platformy Azure muszą być prezentowane jako unikatowy publiczny adres IP. Ten wirtualny publiczny adres IP ułatwia następujące czynności:

- Umożliwia agentowi maszyny Wirtualnej komunikowanie się z platformą Azure, aby zasygnalizować, że jest w stanie "Gotowy".
- Umożliwia komunikację z serwerem wirtualnym DNS w celu zapewnienia rozpoznawania nazw filtrowanych do zasobów (takich jak maszyna wirtualna), które nie mają niestandardowego serwera DNS. To filtrowanie zapewnia, że klienci mogą rozpoznać tylko nazwy hostów swoich zasobów.
- Umożliwia [sondy kondycji z modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-custom-probe-overview.md) w celu określenia stanu kondycji maszyn wirtualnych.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.
- Włącza komunikaty pulsu agenta gościa dla roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Zakres adresu IP 168.63.129.16

Publiczny adres IP 168.63.129.16 jest używany we wszystkich regionach i we wszystkich chmurach krajowych. Ten specjalny publiczny adres IP jest własnością firmy Microsoft i nie ulegnie zmianie. Zaleca się zezwolenie na ten adres IP w dowolnej lokalnej (w polityce zapory maszyny Wirtualnej) (kierunek wychodzący). Komunikacja między tym specjalnym adresem IP a zasobami jest bezpieczna, ponieważ tylko wewnętrzna platforma Azure może zaopatryować się w komunikat z tego adresu IP. Jeśli ten adres jest zablokowany, nieoczekiwane zachowanie może wystąpić w różnych scenariuszach. 168.63.129.16 jest [wirtualnym IP węzła hosta](../virtual-network/security-overview.md#azure-platform-considerations) i jako taki nie podlega trasom zdefiniowanym przez użytkownika.

- Agent maszyny Wirtualnej wymaga komunikacji wychodzącej przez porty 80, 443, 32526 z WireServer (168.63.129.16). Powinny one być otwarte w zaporze lokalnej na maszynie Wirtualnej. Komunikacja na tych portach z 168.63.129.16 nie podlega skonfigurowanym grupom zabezpieczeń sieci.
- 168.63.129.16 może świadczyć usługi DNS na maszynie wirtualnej. Jeśli nie jest to pożądane, ten ruch może być zablokowany w zaporze lokalnej na maszynie Wirtualnej. Domyślnie komunikacja DNS nie podlega skonfigurowanym grupom zabezpieczeń sieci, chyba że specjalnie ukierunkowane przy wykorzystaniu tagu usługi [AzurePlatformDNS.](../virtual-network/service-tags-overview.md#available-service-tags)
- Gdy maszyna wirtualna jest częścią puli wewnętrznej bazy danych modułu równoważenia obciążenia, [komunikacja sondy kondycji](../load-balancer/load-balancer-custom-probe-overview.md) powinna mieć możliwość pochodzić z 168.63.129.16. Domyślna konfiguracja sieciowej grupy zabezpieczeń ma regułę, która umożliwia tę komunikację. Ta reguła korzysta z tagu usługi [AzureLoadBalancer.](../virtual-network/service-tags-overview.md#available-service-tags) W razie potrzeby ruch ten można zablokować, konfigurując sieciową grupę zabezpieczeń, jednak spowoduje to sondy, które nie powiodą się.

W scenariuszu sieci niewirtuańskiej (Classic) sonda kondycji jest pozyskiwana z prywatnego adresu IP i 168.63.129.16 nie jest używany.

## <a name="next-steps"></a>Następne kroki

- [Grupy zabezpieczeń](security-overview.md)
- [Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń](manage-network-security-group.md)
