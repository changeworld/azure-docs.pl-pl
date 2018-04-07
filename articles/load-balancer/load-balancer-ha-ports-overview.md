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
ms.openlocfilehash: f6e9dd09558a3485629d5b70dd8b68b292427b18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="high-availability-ports-overview"></a>Omówienie portów wysokiej dostępności

Azure standardowy moduł równoważenia obciążenia pomaga załadować przepływy TCP i UDP Saldo na wszystkich portach jednocześnie, gdy jest używany do wewnętrznego modułu równoważenia obciążenia. 

Reguła portów HA jest wariant skonfigurowano na wewnętrzny standardowy moduł równoważenia obciążenia reguły równoważenia obciążenia. Korzystanie z usługi równoważenia obciążenia można uprościć przez zapewnienie jednej reguły równoważenia obciążenia wszystkie protokoły TCP i UDP przepływów przychodzących na wszystkich portach wewnętrzny standardowy moduł równoważenia obciążenia. Decyzja równoważenia obciążenia jest wykonywana na przepływ. Jest oparta na następujące połączenia 5 elementowej: źródłowy adres IP, portu źródłowego, docelowego adresu IP, port docelowy i protokołu.

Funkcja porty HA pomaga scenariuszy o kluczowym znaczeniu, takie jak wysokiej dostępności i skalowalności w przypadku urządzeń wirtualnych sieci (NVA) w sieciach wirtualnych. Może również pomóc, podczas dużej liczby portów muszą pochodzić ze zrównoważonym obciążeniem. 

Funkcję wysokiej dostępności portów jest konfigurowane podczas ustawioną portów frontonu i zaplecza **0**i protokół **wszystkich**. Zasobu wewnętrznego modułu równoważenia obciążenia równoważy następnie wszystkie przepływy TCP i UDP, niezależnie od tego, numer portu.

## <a name="why-use-ha-ports"></a>Dlaczego warto używać portów wysokiej dostępności?

### <a name="nva"></a>Urządzenie wirtualne sieci

NVAs służy do zabezpieczania obciążenie platformy Azure z różnego rodzaju zagrożenia bezpieczeństwa. W przypadku używania NVAs w tych scenariuszach muszą być niezawodnych i wysokiej dostępności, a musi skalowanie na żądanie.

Za osiągnięcie tych celów poprzez dodanie wystąpienia NVA do puli zaplecza Azure wewnętrznego modułu równoważenia obciążenia i skonfigurowaniu reguły modułu równoważenia obciążenia porty wysokiej dostępności.

HA portów zapewniają wiele korzyści dla scenariuszy NVA HA:
- Szybkie trybu failover do dobrej kondycji wystąpień, sondy kondycji poszczególnych wystąpień
- Wyższa wydajność skalowania w poziomie do *n*-aktywnych wystąpień
- *N*-scenariuszy aktywnej i aktywny / pasywny
- Wyeliminowanie konieczności złożonych rozwiązań, takich jak Apache ZooKeeper węzły do monitorowania urządzeń

Na poniższym diagramie przedstawiono wdrożenia sieci wirtualnej-gwiazdy. Wymuś tunelu partnerzy ruch do sieci wirtualnej koncentratora i za pośrednictwem NVA, przed opuszczeniem zaufanych miejsca. NVAs znajdują się za wewnętrzny standardowy moduł równoważenia obciążenia z konfiguracji portów wysokiej dostępności. Cały ruch może być przetwarzane i przekazywane w związku z tym.

![Diagram sieci wirtualnej-gwiazdy, o NVAs wdrożone w trybie wysokiej dostępności](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Jeśli używasz NVAs Potwierdź u dostawcy odpowiednich jak najlepiej użyć portów wysokiej dostępności i jakie scenariusze są obsługiwane.

### <a name="load-balancing-large-numbers-of-ports"></a>Duża liczba portów Równoważenie obciążenia

Umożliwia także porty wysokiej dostępności dla aplikacji, które wymagają dużej liczby portów równoważenia obciążenia. Te scenariusze można uprościć przy użyciu wewnętrznego [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md) z portami wysokiej dostępności. Reguły równoważenia obciążenia pojedynczego zastępuje wiele obciążenia poszczególnych reguł, po jednej dla każdego portu równoważenia.

## <a name="region-availability"></a>Dostępność w danym regionie

Funkcja porty HA jest dostępna we wszystkich regionach platformy Azure globalnego.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Jeden pojedynczy niezmienny HA IP (z systemem innym niż — bezpośredniego zwrotu serwera) porty konfiguracji wewnętrznego modułu równoważenia obciążenia standardowego

To jest podstawowa konfiguracja HA portów. Następująca konfiguracja pozwala na skonfigurowanie równoważenia we frontonie pojedynczy adres IP — obciążenia HA portów
- Podczas konfigurowania standardowy moduł równoważenia obciążenia, wybierz **HA porty** wyboru w konfiguracji reguły modułu równoważenia obciążenia 
- Zapewnienie **pływający adres IP** ustawiono **wyłączone**.

Ta konfiguracja nie zezwala na innych równoważenia obciążenia reguły konfiguracji dla bieżącego zasobu usługi równoważenia obciążenia, a także nie innych wewnętrzny konfiguracji usługi równoważenia obciążenia zasobów dla danego zestawu wystąpień wewnętrznej bazy danych.

Można jednak skonfigurować publicznego standardowego modułu równoważenia obciążenia dla wystąpień wewnętrznej bazy danych oprócz tej reguły HA portów.

### <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>Jeden zmiennoprzecinkowych (bezpośredniego zwrotu serwera) HA portów IP konfigurację jednej wewnętrznego modułu równoważenia obciążenia standardowego

Podobnie można skonfigurować przez moduł równoważenia obciążenia, aby użyć reguły z równoważeniem obciążenia **HA portu** z jednego serwera sieci Web i **pływający adres IP** ustawioną **włączone**. 

Ta konfiguracja umożliwia dodanie więcej przestawne obciążenia IP równoważenia reguł i / lub publiczny moduł równoważenia obciążenia. Jednak nie można używać bez pływającego adresu IP HA portu boad równoważenia konfiguracji na podstawie tej konfiguracji.

### <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Wiele konfiguracji portów HA wewnętrznego modułu równoważenia obciążenia standardowego

Jeśli dany scenariusz wymaga, aby skonfigurować więcej niż jeden frontends portu wysokiej dostępności dla tej samej puli wewnętrznej bazy danych, można osiągnąć to przez: 
- Konfigurowanie więcej niż jeden frontonu prywatnych adresów IP, do jednego zasobu wewnętrznego modułu równoważenia obciążenia standardowego.
- Konfigurowanie reguł, w którym każda reguła ma jeden równoważenia obciążenia wielu frontonu unikatowy adres IP jest zaznaczone.
- Wybierz **HA porty** opcji i ustaw **pływający adres IP** do **włączone** dla wszystkich reguł równoważenia obciążenia.

### <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Wewnętrzny moduł równoważenia obciążenia z portów HA & publiczny moduł równoważenia obciążenia na tego samego wystąpienia wewnętrznej bazy danych

Można skonfigurować **jeden** publicznego zasobów standardowy moduł równoważenia obciążenia dla zasobów w wewnętrznej bazie danych wraz z jednego wewnętrznego standardowego modułu równoważenia obciążenia z portami wysokiej dostępności.

>[!NOTE]
>Ta funkcja jest dostępna za pośrednictwem dzisiaj szablonów usługi Azure Resource Manager, ale nie za pomocą portalu Azure.

## <a name="limitations"></a>Ograniczenia

- HA konfiguracji portów jest dostępne tylko dla wewnętrznego modułu równoważenia obciążenia, nie jest dostępna dla publiczny moduł równoważenia obciążenia.

- Kombinacja reguły HA portów równoważenia obciążenia i reguły z systemem innym niż — HA portów równoważenia obciążenia nie jest obsługiwane.

- Funkcja HA portów jest niedostępna dla protokołu IPv6.

- Przepływ symetrycznego dla scenariuszy NVA jest obsługiwana z tylko jedną kartę Sieciową. Zobacz opis i diagram dla [sieci wirtualnych urządzeń](#nva). Jednak jeśli NAT docelowego pracować dla danego scenariusza, należy można używać, aby upewnij się, że wewnętrzny moduł równoważenia obciążenia wysyła zwracany ruch do tej samej NVA.


## <a name="next-steps"></a>Kolejne kroki

- [Skonfiguruj porty HA na wewnętrzny standardowy moduł równoważenia obciążenia](load-balancer-configure-ha-ports.md)
- [Więcej informacji na temat usługi równoważenia obciążenia standardowego](load-balancer-standard-overview.md)
