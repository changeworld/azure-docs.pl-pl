---
title: Co to jest adres IP 168.63.129.16? | Microsoft Docs
description: Zapoznaj się z adresu 168.63.129.16 i jak współdziałają z Twoich zasobów.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: acfd5230d1bd572ea5179651558e3f736a8570af
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833122"
---
# <a name="what-is-ip-address-1686312916"></a>Co to jest adres IP 168.63.129.16?

Adres IP 168.63.129.16 jest wirtualnej publiczny adres IP, która jest używana do przyspieszania kanał komunikacyjny z zasobów platformy Azure. Klientów można zdefiniować dowolną przestrzeń adresową dla swojej prywatnej sieci wirtualnej na platformie Azure. W związku z zasobów platformy Azure muszą być przedstawione jako unikatowy publiczny adres IP. Ten wirtualny publiczny adres IP umożliwia następujące czynności:

- Włącza agenta maszyny Wirtualnej do komunikowania się z platformą Azure w celu sygnalizowania, że jest w stanie "Gotowe".
- Umożliwia komunikację z serwerem wirtualnym DNS, aby zapewnić rozpoznawanie nazw filtrowane do zasobów (np. maszyn wirtualnych), które nie mają niestandardowego serwera DNS. Filtrowanie sprawia, że się upewnić, że klienci mogą rozwiązać tylko nazwy hostów z zasobami.
- Włącza [sondy kondycji z modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-custom-probe-overview.md) do określenia kondycji maszyn wirtualnych.
- Umożliwia maszynie Wirtualnej można uzyskać dynamicznego adresu IP z usługi DHCP w systemie Azure.
- Umożliwia komunikatów pulsu agenta gościa dla roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Zakres adresów IP 168.63.129.16

Publiczny adres IP 168.63.129.16 jest używany we wszystkich regionach i wszystkich chmur krajowych. Ten specjalny publiczny adres IP jest własnością firmy Microsoft i nie ulegnie zmianie. Jest ona dozwolona przez domyślne reguły sieciowej grupy zabezpieczeń. Zaleca się zezwolić na ten adres IP w żadnych zasadach zapory lokalnej. Komunikacja między ten specjalny adres IP i zasobów jest bezpieczne, ponieważ źródeł wiadomości z tego adresu IP mogą być tylko wewnętrzne platformy Azure. Jeśli ten adres jest zablokowany lub nieoczekiwane zachowanie mogą wystąpić w różnych scenariuszach.

[Sondy kondycji usługi Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) pochodzi z tego adresu IP. Blokowanie tego adresu IP sondy usługi zakończy się niepowodzeniem.

W scenariuszu sieci niewirtualnej (wersja klasyczna) sondy kondycji pochodzi z prywatnym adresem IP i nie zastosowano 168.63.129.16.

## <a name="next-steps"></a>Kolejne kroki

- [Grupy zabezpieczeń](security-overview.md)
- [Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń](manage-network-security-group.md)
