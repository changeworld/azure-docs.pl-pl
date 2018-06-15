---
title: Omówienie portów wysokiej dostępności na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat równoważenia obciążenia wewnętrznego modułu równoważenia obciążenia portów wysokiej dostępności.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 53e09498324f802ce1839d262999657d37ee973b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169122"
---
# <a name="high-availability-ports-overview"></a>Omówienie portów wysokiej dostępności

Azure standardowy moduł równoważenia obciążenia pomaga przepływy TCP i UDP Równoważenie obciążenia na wszystkie porty jednocześnie podczas korzystania z wewnętrznego modułu równoważenia obciążenia. 

Reguła portów wysokiej dostępności (HA) jest wariant reguły równoważenia obciążenia, skonfigurowane na wewnętrzny standardowy moduł równoważenia obciążenia. Korzystanie z usługi równoważenia obciążenia można uprościć zapewnienie jednej reguły równoważenia obciążenia wszystkie przepływy TCP i UDP, pojawiające się na wszystkie porty wewnętrzny standardowy moduł równoważenia obciążenia. Równoważenie obciążenia decyzji staje się na przepływ. Ta akcja jest oparta na następujące połączenia 5 elementowej: źródłowy adres IP, portu źródłowego, docelowego adresu IP, port docelowy i protokołu.

Funkcja porty HA pomaga scenariuszy o kluczowym znaczeniu, takie jak wysokiej dostępności i skalowalności dla sieci wirtualnych urządzeń (NVAs) w sieciach wirtualnych. Funkcja może również pomóc w przypadku dużej liczby portów musi być równoważeniem obciążenia. 

Funkcję wysokiej dostępności portów jest konfigurowane podczas ustawioną portów frontonu i zaplecza **0** i protokół **wszystkich**. Zasób usługi równoważenia obciążenia wewnętrznego równoważy następnie wszystkie przepływy TCP i UDP, niezależnie od tego, numer portu.

## <a name="why-use-ha-ports"></a>Dlaczego warto używać portów wysokiej dostępności?

### <a name="nva"></a>Urządzenie wirtualne sieci

NVAs można użyć do zabezpieczania obciążenie platformy Azure z różnego rodzaju zagrożenia bezpieczeństwa. Gdy używasz NVAs w tych scenariuszach muszą być niezawodnych i wysokiej dostępności, a musi skalowanie na żądanie.

Można osiągnięcie tych celów poprzez dodanie wystąpienia NVA do puli zaplecza z wewnętrznego modułu równoważenia obciążenia i konfigurowanie HA porty reguły modułu równoważenia obciążenia.

W przypadku scenariuszy NVA HA porty HA następujące zalety:
- Zapewnić szybkie trybu failover do dobrej kondycji wystąpień sondy kondycji poszczególnych wystąpień
- Upewnij się, większą wydajność, ale skalowalnego w poziomie do *n*-aktywnych wystąpień
- Podaj *n*-scenariuszy aktywnej i aktywny / pasywny
- Eliminuje potrzebę stosowania złożonych rozwiązań, takich jak Apache ZooKeeper węzły do monitorowania urządzeń

Na poniższym diagramie przedstawiono wdrożenia sieci wirtualnej-gwiazdy. Wymuś tunelu partnerzy ruch do sieci wirtualnej koncentratora i za pośrednictwem NVA, przed opuszczeniem zaufanych miejsca. NVAs znajdują się za wewnętrzny standardowy moduł równoważenia obciążenia z konfiguracji portów wysokiej dostępności. Cały ruch może być przetwarzane i przekazywane w związku z tym.

![Diagram sieci wirtualnej-gwiazdy, o NVAs wdrożone w trybie wysokiej dostępności](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Jeśli używasz NVAs, potwierdź u dostawców jak najlepiej użyć portów wysokiej dostępności i Dowiedz się, jakie scenariusze są obsługiwane.

### <a name="load-balancing-large-numbers-of-ports"></a>Równoważenie obciążenia dużej liczby portów

Umożliwia także porty wysokiej dostępności dla aplikacji, które wymagają dużej liczby portów równoważenia obciążenia. Te scenariusze można uprościć przy użyciu wewnętrznego [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md) z portami wysokiej dostępności. Jednej reguły równoważenia obciążenia zastępuje wiele poszczególne równoważenia obciążenia reguły, po jednej dla każdego portu.

## <a name="region-availability"></a>Dostępność w danym regionie

Funkcja porty HA jest dostępna we wszystkich regionach platformy Azure globalnego.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedynczy, niezmienny konfiguracji adresu IP (z systemem innym niż — bezpośredniego zwrotu serwera) HA-porty na wewnętrzny standardowy moduł równoważenia obciążenia

Ta konfiguracja jest podstawową konfigurację portów wysokiej dostępności. Można skonfigurować HA portów równoważenia obciążenia reguły pojedynczego adresu IP frontonu wykonaj następujące czynności:
1. Podczas konfigurowania usługi równoważenia obciążenia standardowego, wybierz **porty HA** pole wyboru w konfiguracji reguły modułu równoważenia obciążenia.
2. Aby uzyskać **pływający adres IP**, wybierz pozycję **wyłączone**.

Ta konfiguracja nie zezwala na innych konfiguracji reguły równoważenia obciążenia dla bieżącego zasobu usługi równoważenia obciążenia. Pozwala to też nie innych wewnętrzny konfiguracji usługi równoważenia obciążenia zasobów dla danego zestawu wystąpień zaplecza.

Można jednak skonfigurować publicznego standardowego modułu równoważenia obciążenia dla wystąpień zaplecza oprócz tej reguły portów wysokiej dostępności.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedynczy, zmiennoprzecinkowych konfiguracji adresu IP (bezpośredniego zwrotu serwera) HA-porty na wewnętrzny standardowy moduł równoważenia obciążenia

Podobnie można skonfigurować przez moduł równoważenia obciążenia, aby zastosować regułę równoważenia obciążenia z **HA portu** z jednym frontonu przez ustawienie **pływający adres IP** do **włączone**. 

Za pomocą tej konfiguracji, można dodać więcej przestawne IP równoważenia obciążenia reguł i/lub publiczny moduł równoważenia obciążenia. Jednak nie można używać z systemem innym niż — pływającego adresu IP, portów HA konfiguracji równoważenia obciążenia na podstawie tej konfiguracji.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Wielu portów HA konfiguracji na wewnętrzny standardowy moduł równoważenia obciążenia

Jeśli dany scenariusz wymaga, aby skonfigurować więcej niż jeden HA portów frontonu dla tej samej puli zaplecza, można wykonać następujące czynności: 
- Konfigurowanie więcej niż jeden prywatny adres IP frontonu do jednego zasobu wewnętrznego modułu równoważenia obciążenia standardowego.
- Konfigurowanie wielu reguł równoważenia obciążenia, w którym każda reguła ma jeden unikatowy adres IP frontonu wybrane.
- Wybierz **porty HA** opcji, a następnie ustaw **pływający adres IP** do **włączone** dla wszystkich reguł równoważenia obciążenia.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Wewnętrzny moduł równoważenia obciążenia z portami wysokiej dostępności i publiczny moduł równoważenia obciążenia dla tego samego wystąpienia zaplecza

Można skonfigurować *jeden* publicznego zasobów standardowy moduł równoważenia obciążenia dla zasobów wewnętrznych, wraz z jednego wewnętrznego standardowego modułu równoważenia obciążenia z portami wysokiej dostępności.

>[!NOTE]
>Ta funkcja jest obecnie dostępna za pośrednictwem szablonów usługi Azure Resource Manager, ale nie jest dostępny za pośrednictwem portalu Azure.

## <a name="limitations"></a>Ograniczenia

- HA konfiguracji portów jest dostępna tylko w przypadku wewnętrzne moduły równoważenia obciążenia. Nie jest dostępna dla publiczne moduły równoważenia obciążenia.

- Łączenia HA porty reguły równoważenia obciążenia i porty bez wysokiej dostępności, reguła równoważenia obciążenia nie jest obsługiwane.

- Funkcja porty wysokiej dostępności jest niedostępna dla protokołu IPv6.

- Przepływ symetrycznego dla scenariuszy NVA jest obsługiwana z tylko jedną kartę Sieciową. Zobacz opis i diagram dla [sieci wirtualnych urządzeń](#nva). Jednak jeśli miejsce docelowe pracować translatora adresów Sieciowych dla danego scenariusza, można umożliwia ona upewnij się, że wewnętrznego modułu równoważenia obciążenia wysyła zwracany ruch do tej samej NVA.


## <a name="next-steps"></a>Kolejne kroki

- [Skonfiguruj porty wysokiej dostępności na wewnętrzny standardowy moduł równoważenia obciążenia](load-balancer-configure-ha-ports.md)
- [Więcej informacji na temat usługi równoważenia obciążenia standardowego](load-balancer-standard-overview.md)
