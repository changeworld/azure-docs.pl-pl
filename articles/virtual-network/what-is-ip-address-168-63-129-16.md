---
title: Co to jest adres IP 168.63.129.16? | Microsoft Docs
description: Dowiedz się więcej o 168.63.129.16 adresów IP i sposobach współdziałania z zasobami.
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
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067783"
---
# <a name="what-is-ip-address-1686312916"></a>Co to jest adres IP 168.63.129.16?

Adres IP 168.63.129.16 to wirtualny publiczny adres IP, który służy do ułatwienia kanału komunikacyjnego z zasobami platformy Azure. Klienci mogą definiować dowolną przestrzeń adresową prywatnej sieci wirtualnej na platformie Azure. W związku z tym zasoby platformy Azure muszą być prezentowane jako unikatowy publiczny adres IP. Ten wirtualny publiczny adres IP umożliwia wykonywanie następujących czynności:

- Umożliwia agentowi maszyny wirtualnej komunikowanie się z platformą Azure w celu zasygnalizowania, że jest w stanie "gotowe".
- Umożliwia komunikację z serwerem wirtualnym DNS w celu zapewnienia przefiltrowanego rozpoznawania nazw do zasobów (takich jak maszyna wirtualna), które nie mają niestandardowego serwera DNS. To filtrowanie gwarantuje, że klienci mogą rozpoznać tylko nazwy hostów swoich zasobów.
- Włącza [sondy kondycji z modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-custom-probe-overview.md) w celu określenia stanu kondycji maszyn wirtualnych.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.
- Włącza komunikaty pulsu agenta gościa dla roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Zakres adresów IP 168.63.129.16

Publiczny adres IP 168.63.129.16 jest używany we wszystkich regionach i wszystkich chmurach narodowych. Ten specjalny publiczny adres IP należy do firmy Microsoft i nie zmieni się. Jest to dozwolone przez domyślną regułę sieciowej grupy zabezpieczeń. Zalecamy Zezwalanie na ten adres IP w ramach wszelkich lokalnych zasad zapory w obu kierunkach ruchu przychodzącego i wychodzącego. Komunikacja między tym specjalnym adresem IP a zasobami jest bezpieczna, ponieważ tylko wewnętrzna platforma platformy Azure może uzyskać komunikat z tego adresu IP. Jeśli ten adres jest zablokowany, może wystąpić nieoczekiwane zachowanie w różnych scenariuszach.

[Sondy kondycji Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) pochodzą z tego adresu IP. Jeśli zablokujesz ten adres IP, sondy zakończą się niepowodzeniem.

W scenariuszu sieci niewirtualnej (klasyczny) sonda kondycji pochodzi z prywatnego adresu IP, a 168.63.129.16 nie jest używana.

## <a name="next-steps"></a>Następne kroki

- [Grupy zabezpieczeń](security-overview.md)
- [Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń](manage-network-security-group.md)
