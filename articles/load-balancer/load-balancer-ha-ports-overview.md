---
title: Omówienie portów wysokiej dostępności na platformie Azure
titlesuffix: Azure Load Balancer
description: Więcej informacji o wysokiej dostępności portów równoważenia obciążenia na wewnętrznego modułu równoważenia obciążenia.
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
ms.openlocfilehash: 350c6ae2e62a88477ce67132b56d9253166d13ec
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130444"
---
# <a name="high-availability-ports-overview"></a>Omówienie portów wysokiej dostępności

Standardowa usługa równoważenia obciążenia Azure pomaga w przepływy TCP i UDP Równoważenie obciążenia na wszystkich portach jednocześnie podczas korzystania z wewnętrznego modułu równoważenia obciążenia. 

Reguła równoważenia obciążenia portów o wysokiej dostępności (HA) jest wariantem reguły równoważenia obciążenia skonfigurowanej w wewnętrznej usługa Load Balancer w warstwie Standardowa. Korzystanie z usługi równoważenia obciążenia można uprościć, podając jedną regułę równoważenia obciążenia, wszystkie protokoły TCP i UDP przepływy, pojawiające się na wszystkich portach wewnętrznego standardowego modułu równoważenia obciążenia. Decyzja równoważenia obciążenia składa się na przepływ. Ta akcja jest oparta na następującym połączeniu z pięcioma kolekcjami: źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i protokół

Reguły równoważenia obciążenia portów HA ułatwiają scenariusze krytyczne, takie jak wysoka dostępność i skalowanie dla wirtualnych urządzeń sieciowych (urządzeń WUS) w sieciach wirtualnych. Ta funkcja może również pomóc w przypadku dużej liczby portów muszą być ze zrównoważonym obciążeniem. 

Reguły równoważenia obciążenia portów HA są konfigurowane podczas ustawiania portów frontonu i zaplecza na **0** i protokołu do **wszystkich**. Następnie zasób wewnętrznego modułu równoważenia obciążenia równoważy wszystkie przepływy TCP i UDP niezależnie od numeru portu

## <a name="why-use-ha-ports"></a>Dlaczego warto używać portów wysokiej dostępności?

### <a name="nva"></a>Wirtualne urządzenia sieciowe

Urządzenia WUS można użyć do zabezpieczania obciążeń platformy Azure, przed wieloma rodzajami zagrożenia bezpieczeństwa. Użycie urządzeń WUS w tych scenariuszach, muszą one być niezawodne i wysoko dostępnych, i należy skalować na żądanie.

Tych celów można osiągnąć przez dodanie wystąpień urządzeń WUS w puli zaplecza modułu równoważenia obciążenia wewnętrznego i Konfigurowanie wysokiej dostępności portów reguły modułu równoważenia obciążenia.

W przypadku scenariuszy urządzeń WUS wysokiej dostępności portów HA następujące zalety:
- Udostępnianie szybkiego trybu failover do dobrej kondycji wystąpień sond kondycji poszczególnych wystąpień
- Upewnij się, wyższej wydajności za pomocą skalowalnego w poziomie do *n*-aktywnych wystąpień
- Podaj *n*— scenariusze aktywnych i aktywny / pasywny
- Eliminuje potrzebę stosowania złożonych rozwiązań, takich jak Apache ZooKeeper węzłów na potrzeby monitorowania urządzeń

Na poniższym diagramie przedstawiono wdrożenie Gwiazda — sieci wirtualnej. Wymuś tunelowanie szprychy ruch w sieci wirtualnej koncentratora i przez urządzenie WUS przed opuszczeniem zaufanych miejsca. Urządzenia WUS są za wewnętrznego standardowego modułu równoważenia obciążenia z konfiguracją portów wysokiej dostępności. Cały ruch można przetwarzane i przesyłane dalej odpowiednio. Gdy program zostanie skonfigurowany jako widoczny na poniższym diagramie, reguła równoważenia obciążenia portów HA również zapewnia symetrię przepływu dla ruchu przychodzącego i wychodzącego.

<a node="diagram"></a>
![Diagram sieci wirtualnej typu Hub i szprych z urządzeń WUS wdrożonym w trybie HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Jeśli używasz urządzenia WUS, upewnij się, z ich dostawcami jak najlepiej wykorzystać portów wysokiej dostępności i dowiedzieć się, jakie scenariusze są obsługiwane.

### <a name="load-balancing-large-numbers-of-ports"></a>Równoważenie obciążenia dużej liczby portów

Umożliwia także portów wysokiej dostępności dla aplikacji, które wymagają równoważenia obciążenia z dużą liczbą portów. Te scenariusze można uprościć za pomocą wewnętrznego [Balancer w warstwie standardowa](load-balancer-standard-overview.md) przy użyciu portów wysokiej dostępności. Pojedynczą regułę równoważenia obciążenia zastępuje wiele poszczególnych reguł równoważenia obciążenia, jeden dla każdego portu.

## <a name="region-availability"></a>Dostępność w danym regionie

Funkcja portów wysokiej dostępności jest dostępna we wszystkich regionach platformy Azure globalnego.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedynczy, niezmienny konfiguracji portów wysokiej dostępności adresu IP (inne niż - bezpośredniego zwrotu serwera) na wewnętrznej Balancer w warstwie standardowa

Ta konfiguracja jest podstawową konfigurację portów wysokiej dostępności. Można skonfigurować HA porty równoważenia obciążenia reguła pojedynczego adresu IP frontonu, wykonując następujące czynności:
1. Podczas konfigurowania standardowego modułu równoważenia obciążenia, wybierz **portów HA** pole wyboru w konfiguracji reguły modułu równoważenia obciążenia.
2. Aby uzyskać **pływającego adresu IP**, wybierz opcję **wyłączone**.

Ta konfiguracja nie zezwala na innych konfiguracji reguły równoważenia obciążenia w bieżącym zasobu modułu równoważenia obciążenia. Umożliwia także nie innych wewnętrznych konfigurację modułu równoważenia obciążenia zasobu dla danego zestawu wystąpień zaplecza.

Można jednak skonfigurować publiczny standardowego modułu równoważenia obciążenia dla wystąpień zaplecza, oprócz tej reguły portów o wysokiej dostępności.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Pojedynczy, zmiennoprzecinkowy konfiguracji portów wysokiej dostępności adresu IP (bezpośredniego zwrotu serwera) na wewnętrznego standardowego modułu równoważenia obciążenia

Podobnie można skonfigurować moduł równoważenia obciążenia do użycia regułę równoważenia obciążenia za pomocą **portów HA** za pomocą pojedynczego frontonu, ustawiając **pływającego adresu IP** do **włączone**. 

Za pomocą tej konfiguracji, można dodać więcej zmiennoprzecinkowy IP równoważenia obciążenia reguł i/lub publiczny moduł równoważenia obciążenia. Jednak nie można używać niezmienny adres IP, portów HA konfiguracji równoważenia obciążenia na podstawie tej konfiguracji.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Wiele konfiguracji portów wysokiej dostępności dla wewnętrznego standardowego modułu równoważenia obciążenia

Jeśli dany scenariusz wymaga, aby skonfigurować więcej niż jeden HA port frontonu na potrzeby tej samej puli zaplecza, można wykonać następujące czynności: 
- Skonfiguruj więcej niż jeden prywatny adres IP frontonu do jednego zasobu wewnętrznej standardowego modułu równoważenia obciążenia.
- Skonfiguruj wiele reguł równoważenia obciążenia, w którym każda reguła ma pojedynczej unikatowy adres IP frontonu wybrane.
- Wybierz **portów HA** opcji, a następnie ustaw **pływającego adresu IP** do **włączone** dla wszystkich reguł równoważenia obciążenia.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Wewnętrzny moduł równoważenia obciążenia przy użyciu portów wysokiej dostępności i publiczny moduł równoważenia obciążenia dla tego samego wystąpienia zaplecza

Można skonfigurować *jeden* zasób publicznego standardowego modułu równoważenia obciążenia dla zasobów zaplecza, wraz z jednego wewnętrznego standardowego modułu równoważenia obciążenia przy użyciu portów wysokiej dostępności.

>[!NOTE]
>Ta funkcja jest obecnie dostępna za pośrednictwem usługi Azure Resource Manager, ale nie jest dostępna za pośrednictwem witryny Azure portal.

## <a name="limitations"></a>Ograniczenia

- Reguły równoważenia obciążenia portów HA są dostępne tylko dla wewnętrznych usługa Load Balancer w warstwie Standardowa.
- Łączenie z wysokiej dostępności portów reguły równoważenia obciążenia i porty bez wysokiej dostępności, reguły równoważenia obciążenia nie jest obsługiwane.
- Istniejące fragmenty adresów IP będą przekazywane przez reguły równoważenia obciążenia portów HA do tego samego miejsca docelowego, co pierwszy pakiet.  Fragmentacja IP pakietu UDP lub TCP nie jest obsługiwana.
- Reguły równoważenia obciążenia portów HA nie są dostępne dla protokołu IPv6.
- Mechanizm symetrii przepływów (głównie dla scenariuszy urządzenie WUS) jest obsługiwany z wystąpieniem zaplecza i jedną kartą sieciową (i konfiguracją pojedynczego adresu IP) tylko wtedy, gdy jest używana jak pokazano na powyższym diagramie i przy użyciu zasad równoważenia obciążenia portów HA. Nie jest ona dostępna w żadnym innym scenariuszu. Oznacza to, że co najmniej dwa zasoby Load Balancer i ich odpowiednie zasady podejmują niezależne decyzje i nigdy nie są koordynowane. Zobacz opis i diagram dla [sieciowych urządzeń wirtualnych](#nva). W przypadku korzystania z wielu kart sieciowych lub urządzenie WUSi między publiczną i wewnętrzną Load Balancer, symetria przepływu jest niedostępna.  Możliwe jest obejście tego problemu ze źródłem NAT'ing przepływ transferu danych przychodzących do adresu IP urządzenia, aby umożliwić odpowiedzi na te same urządzenie WUS.  Jednak zdecydowanie zalecamy korzystanie z jednej karty sieciowej i używanie architektury referencyjnej pokazanej na powyższym diagramie.


## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie portów wysokiej dostępności na wewnętrznego standardowego modułu równoważenia obciążenia](load-balancer-configure-ha-ports.md)
- [Dowiedz się więcej o standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md)
