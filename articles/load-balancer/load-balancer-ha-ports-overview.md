---
title: Omówienie portów wysokiej dostępności na platformie Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się więcej o równoważeniu obciążenia portów o wysokiej dostępności w wewnętrznym modułze równoważenia obciążenia.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 5ada709350802344bfa65cce269735baa416edf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234447"
---
# <a name="high-availability-ports-overview"></a>Omówienie portów o wysokiej dostępności

Azure Standard Load Balancer ułatwia równoważenie obciążenia przepływów TCP i UDP na wszystkich portach jednocześnie, gdy używasz wewnętrznego modułu równoważenia obciążenia. 

Reguła równoważenia obciążenia portów wysokiej dostępności (HA) jest wariantem reguły równoważenia obciążenia, skonfigurowanej na wewnętrznym standardowym modułem równoważenia obciążenia. Można uprościć użycie modułu równoważenia obciążenia, udostępniając jedną regułę równoważenia obciążenia wszystkich przepływów TCP i UDP, które docierają do wszystkich portów wewnętrznego standardowego modułu równoważenia obciążenia. Decyzja równoważenia obciążenia jest podejmowana na przepływ. Ta akcja jest oparta na następujących połączeniach z pięcioma krotami: źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół

Reguły równoważenia obciążenia portów wysokiej dostępności pomagają w krytycznych scenariuszach, takich jak wysoka dostępność i skala dla sieciowych urządzeń wirtualnych (NVA) w sieciach wirtualnych. Funkcja może również pomóc, gdy duża liczba portów musi być równoważenia obciążenia. 

Reguły równoważenia obciążenia portów wysokiej klasy są konfigurowane po ustawieniu portów front-end i back-end na **0,** a protokół na **Wszystkie**. Wewnętrzny zasób modułu równoważenia obciążenia wszystkie przepływy TCP i UDP, niezależnie od numeru portu

## <a name="why-use-ha-ports"></a>Dlaczego warto korzystać z portów wysokiej łączy?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Wirtualne urządzenia sieciowe

Urządzenia Wewnętrznego umożliwiają zabezpieczenie obciążenia platformy Azure przed wieloma typami zagrożeń bezpieczeństwa. Korzystając z usług WUW w tych scenariuszach, muszą być niezawodne i wysoce dostępne i muszą być skalowane w poziomie dla popytu.

Można osiągnąć te cele po prostu dodając wystąpienia urządzenia WUS do puli zaplecza modułu wewnętrznego modułu równoważenia obciążenia i konfigurowanie reguły modułu równoważenia obciążenia portów wysokiej klasy.

W przypadku scenariuszy wysokiej analizy wysokiej analizy portów wysokiej analizy porty oferują następujące zalety:
- Zapewnia szybkie przejście w tryb failover do zdrowych wystąpień, z sondami kondycji na wystąpienie
- Zapewnij wyższą wydajność dzięki wystąpieniom skalowanie w poziomie do *n-active*
- Podaj *n*-aktywne i aktywne-pasywne scenariusze
- Wyeliminuj potrzebę stosowania złożonych rozwiązań, takich jak węzły Apache ZooKeeper do monitorowania urządzeń

Na poniższym diagramie przedstawiono wdrożenie sieci wirtualnej i koncentratora. Szprychy wymuszają ruch do sieci wirtualnej centrum i za pośrednictwem urządzenia WUS, przed opuszczeniem zaufanej przestrzeni. Elementy NVA znajdują się za wewnętrznym standardowym modułem równoważenia obciążenia z konfiguracją portów wysokiej jakości. Cały ruch może być przetwarzany i przekazywał odpowiednio. Po skonfigurowaniu jako pokazano na poniższym diagramie, wiele do wiersza reguły równoważenia obciążenia portów ponadto zapewnia symetrię przepływu dla ruchu przychodzącego i wychodzącego.

<a node="diagram"></a>
![Diagram sieci wirtualnej typu hub-and-spoke z urządzeniami NVA wdrożonymi w trybie wysokiej klasy](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Jeśli używasz usług WUW, potwierdź z ich dostawcami, jak najlepiej korzystać z portów wysokiej analizy i dowiedzieć się, które scenariusze są obsługiwane.

### <a name="load-balancing-large-numbers-of-ports"></a>Równoważenie obciążenia dużej liczby portów

Można również użyć portów wysokiej informacji o wysokiej informacji dla aplikacji, które wymagają równoważenia obciążenia dużej liczby portów. Można uprościć te scenariusze przy użyciu wewnętrznego [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md) z portami wysokiej jakości. Pojedyncza reguła równoważenia obciążenia zastępuje wiele indywidualnych reguł równoważenia obciążenia, po jednej dla każdego portu.

## <a name="region-availability"></a>Dostępność w danym regionie

Funkcja portów wysokiej dostępności jest dostępna we wszystkich globalnych regionach platformy Azure.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedyncza, nieprzecinaczana konfiguracja portów HA IP (nieprzesuwowego) na wewnętrznym modułze równoważenia obciążenia standardowego

Ta konfiguracja jest podstawową konfiguracją portów wysokiej kondycji. Regułę równoważenia obciążenia portów wysokiej klasy można skonfigurować na jednym adresie IP front-endu, wykonując następujące czynności:
1. Podczas konfigurowania standardowego modułu równoważenia obciążenia zaznacz pole wyboru **Porty wysokiej jakości** w konfiguracji reguły równoważenia obciążenia.
2. W przypadku **przestawnego adresu IP**wybierz pozycję **Wyłączone**.

Ta konfiguracja nie zezwala na inną konfigurację reguł równoważenia obciążenia dla bieżącego zasobu modułu równoważenia obciążenia. Umożliwia również żadną inną konfigurację zasobów modułu wewnętrznego równoważenia obciążenia dla danego zestawu wystąpień zaplecza.

Można jednak skonfigurować publiczny standardowy moduł równoważenia obciążenia dla wystąpień zaplecza oprócz tej reguły portów wysokiej jakości.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedyncza, zmienna konfiguracja portów HA IP (Direct Server Return) na wewnętrznym standardowym modułie równoważenia obciążenia

Podobnie można skonfigurować moduł równoważenia obciążenia tak, aby używał reguły równoważenia obciążenia z **portem HA** z jednym frontonem, ustawiając **zmienny adres IP** na **Włączone**. 

Za pomocą tej konfiguracji można dodać więcej pływających reguł równoważenia obciążenia IP i/lub publicznego modułu równoważenia obciążenia. Nie można jednak użyć nieprzecinkowej konfiguracji równoważenia obciążenia IP i portów WYSOKIEJ klasy na tej konfiguracji.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Wiele konfiguracji portów HA na wewnętrznym standardowym równoważku obciążenia

Jeśli scenariusz wymaga skonfigurowania więcej niż jednego frontonia portu wysokiej klasy dla tej samej puli zaplecza, można wykonać następujące czynności: 
- Skonfiguruj więcej niż jeden prywatny adres IP frontu dla pojedynczego wewnętrznego zasobu standardowego modułu równoważenia obciążenia.
- Skonfiguruj wiele reguł równoważenia obciążenia, w których każda reguła ma wybrany jeden unikatowy adres IP front-end.
- Wybierz opcję **Porty wysokiej klasy,** a następnie ustaw **zmiennoprzecinkowany adres IP** na **Włączone** dla wszystkich reguł równoważenia obciążenia.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Wewnętrzny moduł równoważenia obciążenia z portami wysokiej klasy i publicznym modułem równoważenia obciążenia w tym samym wystąpieniu zaplecza

Można skonfigurować *jeden* publiczny zasób standardowego modułu równoważenia obciążenia dla zasobów zaplecza, wraz z jednym wewnętrznym standardowym modułem równoważenia obciążenia z portami wysokiej jakości.

>[!NOTE]
>Ta funkcja jest obecnie dostępna za pośrednictwem szablonów usługi Azure Resource Manager, ale nie jest dostępna za pośrednictwem witryny Azure portal.

## <a name="limitations"></a>Ograniczenia

- Reguły równoważenia obciążenia portów wysokiej dostępności są dostępne tylko dla wewnętrznego standardowego modułu równoważenia obciążenia.
- Nie jest obsługiwane łączenie reguły równoważenia obciążenia portów wysokiej klasy i reguły równoważenia obciążenia portów innych niż wiele ha.
- Istniejące fragmenty adresów IP będą przekazywane przez reguły równoważenia obciążenia portów wysokiej klasy do tego samego miejsca docelowego co pierwszy pakiet.  Ip fragmentowanie pakietu UDP lub TCP nie jest obsługiwane.
- Symetria przepływu (głównie dla scenariuszy wywołujących wartość wyuswania NVA) jest obsługiwana za pomocą wystąpienia wewnętrznej bazy danych i pojedynczej karty sieciowej (i pojedynczej konfiguracji IP) tylko wtedy, gdy jest używana, jak pokazano na powyższym diagramie i przy użyciu reguł równoważenia obciążenia portów wysokiej klasy. Nie jest to przewidziane w żadnym innym scenariuszu. Oznacza to, że dwa lub więcej zasobów modułu równoważenia obciążenia i ich odpowiednich reguł podejmują niezależne decyzje i nigdy nie są koordynowane. Zobacz opis i diagram [dla wirtualnych urządzeń sieciowych](#nva). W przypadku korzystania z wielu kart sieciowych lub nakładania urządzenia WUS między publicznym i wewnętrznym modułem równoważenia obciążenia symetria przepływu jest niedostępna.  Możesz obejść ten problem przez źródło NAT'ing przepływu transferu danych przychodzących do ip urządzenia, aby umożliwić odpowiedzi, aby uzyskać dostęp do tej samej sieci WUS.  Jednak zdecydowanie zaleca się przy użyciu pojedynczej karty sieciowej i przy użyciu architektury odniesienia pokazano na powyższym diagramie.


## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie portów wysokiej jakości na wewnętrznym standardowym modułze równoważenia obciążenia](load-balancer-configure-ha-ports.md)
- [Dowiedz się więcej o standardowym równoważącym obciążenia](load-balancer-standard-overview.md)
