---
title: Rozwiązywanie problemów z usługą równoważenia obciążenia Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z znane problemy z modułem równoważenia obciążenia Azure
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: genli
ms.openlocfilehash: 294bb6dd780d1df642d6e793b29267da1e8b8336
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774917"
---
# <a name="troubleshoot-azure-load-balancer"></a>Rozwiązywanie problemów z usługą Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ta strona zawiera informacje dotyczące rozwiązywania problemów dla często zadawane pytania usługi równoważenia obciążenia Azure. Gdy łączność usługi równoważenia obciążenia jest niedostępny, objawy najczęściej są następujące: 
- Maszyny wirtualne za usługą równoważenia obciążenia nie odpowiada na sondy kondycji 
- Maszyny wirtualne za usługą równoważenia obciążenia nie odpowiadają na ruch na skonfigurowanym porcie

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Objaw: Nie odpowiadają maszyn wirtualnych za usługą równoważenia obciążenia do badania kondycji
Dla serwerów wewnętrznej bazy danych do udziału w zestawie usługi równoważenia obciążenia muszą one przejść sprawdzanie sondowania. Aby uzyskać więcej informacji na temat sondy kondycji, zobacz [sondy modułu równoważenia obciążenia opis](load-balancer-custom-probe-overview.md). 

Puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych może nie odpowiadać sond z dowolnego z następujących powodów: 
- Puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej jest zła 
- Ładowanie puli zaplecza modułu równoważenia maszyny Wirtualnej nie nasłuchuje na porcie sondy 
- Zapora lub grupy zabezpieczeń sieci blokuje port w puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych 
- Inne błędy konfiguracji usługi równoważenia obciążenia

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Przyczyna 1: Puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej jest zła 

**Sprawdzanie poprawności i rozwiązania**

Aby rozwiązać ten problem, zaloguj się do uczestniczących maszyn wirtualnych, sprawdź, czy stan maszyny Wirtualnej jest w dobrej kondycji i mogą odpowiadać na **narzędzia PsPing** lub **TCPing** z innej maszyny Wirtualnej w puli. Jeśli maszyna wirtualna jest w złej kondycji lub nie może odpowiadać na sondy, należy rozwiązać problem i uzyskać maszynę Wirtualną do stanu dobrej kondycji, aby uczestniczyć w programie Równoważenie obciążenia.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Przyczyny 2: Puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej nie jest nasłuchuje na porcie sondy
Jeśli maszyna wirtualna jest w dobrej kondycji, ale nie odpowiada na żądania sondy, następnie jedną z możliwych przyczyn może być port sondy nie jest otwarty na udział maszyny Wirtualnej lub maszyny Wirtualnej nie nasłuchuje na tym porcie.

**Sprawdzanie poprawności i rozwiązania**

1. Zaloguj się do maszyny Wirtualnej wewnętrznej bazy danych. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie do sprawdzania poprawności jest aplikacja nasłuchuje na porcie sondowania:   
            polecenie netstat -
3. Jeśli stan portu nie jest wymieniony jako **LISTENING**, skonfigurować odpowiednie port. 
4. Można także wybrać innego portu, który jest wymieniony jako **LISTENING**i zaktualizuj odpowiednio załadować konfiguracji usługi równoważenia.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Przyczyny 3: Zapora lub grupy zabezpieczeń sieci jest zablokowanie portu w puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych  
Jeśli zapora na maszynie Wirtualnej blokuje port sondy lub co najmniej jedna sieć grupy zabezpieczeń skonfigurowane w podsieci lub na maszynie Wirtualnej, nie zezwala na sondowanie nawiązać połączenia z portem, maszyny Wirtualnej nie może odpowiadać na sondy kondycji.          

**Sprawdzanie poprawności i rozwiązania**

* Jeśli włączona jest zapora, sprawdź, czy skonfigurowano pozwoli port sondy. Jeśli nie, należy skonfigurować zaporę tak, aby zezwolić na ruch na porcie sondowania i ponownie przetestuj. 
* Z listy grup zabezpieczeń sieci Sprawdź, czy ruch przychodzący lub wychodzący na porcie sondowania został zakłóceń. 
* Sprawdź również, czy **Odmów wszystkich** grup zabezpieczeń sieci reguły na karcie Sieciowej maszyny Wirtualnej lub podsieci, która ma wyższy priorytet niż domyślna reguła, która umożliwia sond LB & ruchu (grup zabezpieczeń sieci Zezwalaj IP usługi równoważenia obciążenia z 168.63.129.16). 
* Jeśli żadnego z tych reguł blokują ruch sondowania, Usuń i ponownie skonfigurować reguły zezwalające na ruch sondowania.  
* Należy sprawdzić, czy maszyna wirtualna ma teraz uruchomione, odpowiada na żądania sondy kondycji. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Przyczyna 4: Inne błędy konfiguracji usługi równoważenia obciążenia
Jeśli wszystkie poprzednie przyczyny prawdopodobnie sprawdzane i poprawnie rozpoznać i wewnętrznej bazy danych maszyny Wirtualnej nadal nie odpowiada sondy kondycji, a następnie ręcznie przetestować połączenie z i zbierać ślady niektórych zrozumienie łączność.

**Sprawdzanie poprawności i rozwiązania**

* Użyj **narzędzia Psping** z jednego z innych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedzi port sondy (przykład:.\psping.exe 10.0.0.4:3389 -t) i zanotować wyniki. 
* Użyj **TCPing** z jednego z innych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedzi port sondy (przykład:.\tcping.exe 10.0.0.4 3389) i zanotować wyniki. 
* Jeśli odpowiedź nie zostanie odebrana w tych testów ping następnie
    - Uruchamiać równoczesnych Netsh trace w puli zaplecza docelowej maszyny Wirtualnej i innego testu maszyny Wirtualnej z tej samej sieci wirtualnej. Teraz Uruchamianie testu narzędzia PsPing przez pewien czas, zbierać dane śledzenia niektórych sieci, a następnie zatrzymać uruchomienie testu. 
    - Analizowanie przechwytywania ruchu sieciowego i czy istnieją pakietów przychodzących i wychodzących, powiązane z zapytania ping. 
        - Jeśli nie pakiety przychodzące są przestrzegane w puli zaplecza maszyny Wirtualnej, jest potencjalnie sieciowych grup zabezpieczeń lub niewłaściwa konfiguracja przez zablokować ruch. 
        - Jeśli nie pakiety wychodzące są przestrzegane w puli zaplecza maszyny Wirtualnej, maszyna wirtualna musi być sprawdzane pod kątem niepowiązanych problemy (eample, blokuje port sondy aplikacji). 
    - Upewnij się, jeśli pakiety sondy jest wymuszenie inną lokalizację docelową (prawdopodobnie przez ustawienia przez) przed osiągnięciem usługi równoważenia obciążenia. Może to spowodować, że ruch nie docierają do wewnętrznej bazy danych maszyny Wirtualnej. 
* Zmień typ sondowania (na przykład HTTP do TCP) i skonfigurować odpowiedni port w grup zabezpieczeń sieci listy kontroli dostępu i zapory, aby zweryfikować, czy problem z konfiguracją w odpowiedzi na sondę. Aby uzyskać więcej informacji na temat konfiguracji sondy kondycji, zobacz [równoważenia obciążenia punktu końcowego konfiguracji sondy kondycji](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Objaw: Maszyny wirtualne za usługą równoważenia obciążenia nie są odpowiada na ruch na porcie skonfigurowanym danych

Jeśli maszyna wirtualna znajduje się w dobrej kondycji i odpowiada sondy kondycji, ale nadal nie uczestniczy w usłudze równoważenia obciążenia lub nie odpowiada na ruch danych puli wewnętrznej bazy danych może to być spowodowane jedną z następujących powodów: 
* Maszyna wirtualna nie nasłuchuje na porcie danych puli zaplecza modułu równoważenia obciążenia 
* Grupy zabezpieczeń sieci jest zablokowanie portu w puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej  
* Uzyskiwanie dostępu do usługi równoważenia obciążenia z tej samej maszyny Wirtualnej i karty Sieciowej 
* Uzyskiwanie dostępu do Internetu VIP usługi równoważenia obciążenia z uczestniczących puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Przyczyna 1: Puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej nie jest nasłuchuje na porcie danych 
Jeśli maszyna wirtualna nie odpowiada na ruch danych, może to być spowodowane port docelowy nie jest otwarty w uczestniczących maszynie wirtualnej lub maszyny Wirtualnej nie nasłuchuje na tym porcie. 

**Sprawdzanie poprawności i rozwiązania**

1. Zaloguj się do maszyny Wirtualnej wewnętrznej bazy danych. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie do sprawdzania poprawności jest aplikacja nasłuchuje na porcie danych:  
            polecenie netstat - 
3. Jeśli port nie ma na liście ze stanem "NASŁUCHIWANIA" Konfigurowanie portu odbiornika prawidłowego 
4. Jeśli port jest oznaczona jako Listening, sprawdź w aplikacji docelowej na tym porcie w poszukiwaniu możliwych problemów. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Przyczyny 2: Grupy zabezpieczeń sieci blokuje port w puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej  

Jeśli co najmniej jedną grupę zabezpieczeń sieci skonfigurowane w podsieci lub na maszynie Wirtualnej, blokuje źródłowy adres IP lub portu, a następnie maszyna wirtualna jest w stanie odpowiedzieć.

* Lista grupy zabezpieczeń sieci skonfigurowane do wewnętrznej bazy danych maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md).
* Z listy grup zabezpieczeń sieci Sprawdź, czy:
    - przychodzący lub wychodzący ruch na porcie danych ma zakłóceń. 
    - **Odmów wszystkich** reguły grupy zabezpieczeń na karcie Sieciowej maszyny Wirtualnej lub podsieci, która ma wyższy priorytet, który sondy domyślna reguła, która umożliwia równoważenia obciążenia sieciowego i ruchu (grup zabezpieczeń sieci Zezwalaj IP usługi równoważenia obciążenia z 168.63.129.16, który jest port sondy) 
* Jeśli dowolne zasady blokują ruch, Usuń i ponownie skonfigurować te reguły zezwalające na ruch danych.  
* Sprawdź, czy maszyna wirtualna teraz rozpoczął się odpowiedzieć na sondy kondycji.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Przyczyny 3: Uzyskiwanie dostępu do usługi równoważenia obciążenia z tej samej maszyny Wirtualnej i interfejsu sieciowego 

Jeżeli aplikacji hostowanej w zapleczu wirtualna modułu równoważenia obciążenia próbuje uzyskać dostęp do innej aplikacji hostowanej w zapleczu tej samej maszyny Wirtualnej za pośrednictwem tego samego interfejsu sieciowego, jest to nieobsługiwany scenariusz i zakończy się niepowodzeniem. 

**Rozdzielczość** może rozwiązać ten problem, korzystając z jednej z następujących metod:
* Skonfiguruj pulę zaplecza oddzielne maszyn wirtualnych na aplikację. 
* Konfigurowanie aplikacji w dwóch kart interfejsu Sieciowego maszyn wirtualnych, więc każda aplikacja korzystał z własnego interfejsu sieciowego i adresu IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>Przyczyna 4: Uzyskiwanie dostępu do wewnętrznego adresu VIP usługi równoważenia obciążenia z uczestniczących puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej

Jeśli skonfigurowano ILB wirtualne adresy IP w sieci wirtualnej, a jednej z maszyn wirtualnych do uczestnika wewnętrznej bazy danych próbuje uzyskać dostęp wewnętrzny adres VIP usługi równoważenia obciążenia, który powoduje błąd. Jest to nieobsługiwany scenariusz.
**Rozdzielczość** oceny bramą aplikacji lub inne serwery proxy (na przykład nginx lub haproxy), do obsługi tego rodzaju scenariusza. Aby uzyskać więcej informacji na temat bramy aplikacji, zobacz [omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Przechwytywanie dodatkowe sieci
Jeśli zdecydujesz się otwieranie sprawy pomocy technicznej, należy zebrać następujące informacje dotyczące szybsze rozpoznawanie. Wybierz pojedynczy wewnętrznej bazy danych maszyny Wirtualnej do wykonywania następujących testów:
- Testowanie odpowiedzi port sondy przy użyciu narzędzia Psping z jednej z maszyn wirtualnych w ramach sieci wirtualnej wewnętrznej bazy danych (przykład: 10.0.0.4:3389 narzędzia psping) i zanotować wyniki. 
- Umożliwia testowanie odpowiedzi port sondy TCPing z jednej z maszyn wirtualnych w ramach sieci wirtualnej wewnętrznej bazy danych (przykład: 10.0.0.4:3389 narzędzia psping) i zanotować wyniki.
- Jeśli odpowiedź nie zostanie odebrana w tych testów ping, należy uruchomić jednoczesnych Netsh trace na wewnętrznej bazy danych maszyny Wirtualnej i testowej sieci wirtualnej maszyny Wirtualnej podczas uruchamiania narzędzia PsPing następnie Zatrzymaj śledzenie Netsh. 
  
## <a name="next-steps"></a>Kolejne kroki

Jeśli powyższe czynności nie rozwiązać ten problem, otwórz [obsługuje biletu](https://azure.microsoft.com/support/options/).

