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
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107997"
---
# <a name="what-is-ip-address-1686312916"></a>Co to jest adres IP 168.63.129.16?

Adres IP 168.63.129.16 jest wirtualnej publiczny adres IP, która jest używana do przyspieszania kanał komunikacyjny z zasobów platformy Azure. Klientów można zdefiniować dowolną przestrzeń adresową dla swojej prywatnej sieci wirtualnej na platformie Azure. W związku z zasobów platformy Azure muszą być przedstawione jako unikatowy publiczny adres IP. Ten wirtualny publiczny adres IP umożliwia następujące czynności:

- Włącza agenta maszyny Wirtualnej do komunikowania się z platformą Azure w celu sygnalizowania, że jest w stanie "Gotowe".
- Umożliwia komunikację z serwerem wirtualnym DNS, aby zapewnić rozpoznawanie nazw filtrowane do zasobów (np. maszyn wirtualnych), które nie mają niestandardowego serwera DNS. Filtrowanie sprawia, że się upewnić, że klienci mogą rozwiązać tylko nazwy hostów z zasobami.
- Umożliwia sond kondycji usługi równoważenia obciążenia do określenia kondycji maszyn wirtualnych w zestawie z równoważeniem obciążenia.
- Umożliwia komunikatów pulsu agenta gościa dla roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Zakres adresów IP 168.63.129.16

Wirtualnej publiczny adres IP 168.63.129.16 jest używany we wszystkich regionach i wszystkich chmur krajowych. Ten specjalny publiczny adres IP nie ulegnie zmianie. Jest ona dozwolona przez domyślne reguły sieciowej grupy zabezpieczeń. Zaleca się zezwolić na ten adres IP w żadnych zasadach zapory lokalnej. Komunikacja między ten specjalny adres IP i zasobów jest bezpieczne, ponieważ źródeł wiadomości z tego adresu IP mogą być tylko wewnętrzne platformy Azure. Jeśli ten adres jest zablokowany lub nieoczekiwane zachowanie mogą wystąpić w różnych scenariuszach.

Ponadto można oczekiwać, że ten przepływ ruchu z wirtualnej publiczny adres IP 168.63.129.16 do punktu końcowego, który jest skonfigurowany dla [sondy kondycji modułu równoważenia obciążenia](../load-balancer/load-balancer-custom-probe-overview.md). W przypadku sieci niewirtualnej sondy kondycji pochodzi z prywatnym adresem IP. 

## <a name="next-steps"></a>Kolejne kroki

- [Grupy zabezpieczeń](security-overview.md)
- [Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń](manage-network-security-group.md)
