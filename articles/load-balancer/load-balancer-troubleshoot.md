---
title: Rozwiązywanie problemów z usługą Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Rozwiązywanie znanych problemów z usługą Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: c5f92d564a93823fd9c0f932fa95f20d4e827761
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60734479"
---
# <a name="troubleshoot-azure-load-balancer"></a>Rozwiązywanie problemów z usługą Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ta strona zawiera informacje dotyczące rozwiązywania problemów dla typowych pytań usługi Azure Load Balancer. Gdy łączność z modułu równoważenia obciążenia są niedostępne, objawy najczęściej są następujące: 
- Maszyny wirtualne za modułem równoważenia obciążenia nie odpowiadają sond kondycji 
- Maszyny wirtualne za modułem równoważenia obciążenia nie odpowiadają na ruch na porcie skonfigurowanym

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Objaw: Maszyny wirtualne za modułem równoważenia obciążenia nie odpowiadają sond kondycji
W przypadku serwerów wewnętrznej bazy danych do udziału w zestawu modułu równoważenia obciążenia muszą przekazać wyboru sondowania. Aby uzyskać więcej informacji na temat sond kondycji, zobacz [sondy modułu równoważenia obciążenia w interpretacji](load-balancer-custom-probe-overview.md). 

Puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych może nie odpowiadać na sondy ze względu na jedną z następujących powodów: 
- Puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej jest w złej kondycji 
- Ładowanie puli zaplecza modułu równoważenia, których maszyna wirtualna nie nasłuchuje na porcie sondowania 
- Zapora lub grupę zabezpieczeń sieci blokuje port w puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych 
- Inne błędy konfiguracji modułu równoważenia obciążenia

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Przyczyny 1: Puli zaplecza modułu równoważenia obciążenia maszyny Wirtualnej jest w złej kondycji 

**Sprawdzanie poprawności i rozwiązania**

Aby rozwiązać ten problem, zaloguj się do uczestniczących w programie maszyn wirtualnych, sprawdź, czy stan maszyny Wirtualnej jest w dobrej kondycji i mogą odpowiadać na **PsPing** lub **TCPing** z innej maszyny Wirtualnej w puli. Jeśli maszyna wirtualna jest w złej kondycji lub jest w stanie odpowiadać na sondy, należy rozwiązać problem i uzyskać maszynę Wirtualną do stanu dobrej kondycji, zanim będą mogły uczestniczyć w programie Równoważenie obciążenia.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Przyczyny 2: Ładowanie puli zaplecza modułu równoważenia, których maszyna wirtualna nie nasłuchuje na porcie sondowania
Jeśli maszyna wirtualna jest w dobrej kondycji, ale nie odpowiada na żądania sondowania, następnie jedną z możliwych przyczyn może być, że port sondy nie jest otwarty na udział maszyny Wirtualnej lub maszyny Wirtualnej nie nasłuchuje na tym porcie.

**Sprawdzanie poprawności i rozwiązania**

1. Zaloguj się do wewnętrznej bazy danych maszyny Wirtualnej. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie do sprawdzania poprawności jest aplikacja nasłuchuje na porcie sondowania:   
            polecenie netstat -
3. Jeśli stan portu nie jest wymieniony jako **LISTENING**, konfigurowania właściwej portu. 
4. Można także wybrać inny port, który jest wymieniony jako **LISTENING**i zaktualizuj odpowiednio załadować konfiguracji modułu równoważenia.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Przyczyny 3: Zapora lub grupę zabezpieczeń sieci blokuje port w puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych  
Jeśli zapora na maszynie Wirtualnej blokuje port sondy lub co najmniej jeden sieciowe grupy zabezpieczeń skonfigurowane w tej podsieci lub na maszynie Wirtualnej, nie zezwala na sondy dotrzeć do portu, maszyna wirtualna jest w stanie odpowiadać na sondy kondycji.          

**Sprawdzanie poprawności i rozwiązania**

* Jeśli jest włączona zapora, sprawdź, czy skonfigurowano zezwalająca na porcie sondowania. Jeśli nie, należy skonfigurować zaporę do zezwalania na ruch na porcie sondowania i ponownie przetestuj. 
* Z listy grup zabezpieczeń sieci Sprawdź, czy ruch przychodzący lub wychodzący na porcie sondowania ma zakłóceń. 
* Ponadto sprawdź, czy **Odmów wszystkiego** reguły sieciowych grup zabezpieczeń, na karcie interfejsu Sieciowego maszyny Wirtualnej lub podsieci, który ma wyższy priorytet niż domyślna reguła umożliwiająca sondy modułu równoważenia obciążenia i ruchu (sieciowe grupy zabezpieczeń muszą zezwalać na adres IP modułu równoważenia obciążenia 168.63.129.16). 
* Jeśli dowolny z tych reguł blokuje ruch sondowania, Usuń i ponownie skonfigurować reguły zezwalające na ruch sondowania.  
* Należy sprawdzić, czy maszyna wirtualna ma teraz uruchomiona, odpowiadać na sondy kondycji. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Przyczyna 4: Inne błędy konfiguracji modułu równoważenia obciążenia
Jeśli poprzedni przyczyny wydaje się, że sprawdzane i poprawnie rozpoznać i na maszynie Wirtualnej zaplecza nadal nie odpowie na sondę kondycji, a następnie ręcznie przetestować łączność i zbierać ślady, niektóre zrozumienie łączność.

**Sprawdzanie poprawności i rozwiązania**

* Użyj **Psping** z jednej z maszyn wirtualnych w ramach sieci wirtualnej, aby przetestować odpowiedzi port sondy (przykład: 10.0.0.4:3389 -t.\psping.exe) i Zarejestruj wyniki. 
* Użyj **TCPing** z jednej z maszyn wirtualnych w ramach sieci wirtualnej, aby przetestować odpowiedzi port sondy (przykład:.\tcping.exe 10.0.0.4 3389) i Zarejestruj wyniki. 
* Jeśli odpowiedź nie zostanie odebrana w tych testów ping następnie
    - Uruchom jednoczesnych Netsh trace w puli zaplecza docelowej maszyny Wirtualnej oraz innej, testowej maszyny Wirtualnej z tej samej sieci wirtualnej. Teraz uruchom test PsPing przez pewien czas, zbieranie niektórych danych śledzenia sieci, a następnie Zatrzymaj test. 
    - Analizowanie przechwytywania sieci i sprawdzić, czy są pakietów przychodzących i wychodzących, związane z zapytaniem ping. 
        - Nie pakiety przychodzące są przestrzegane na maszynie Wirtualnej w puli zaplecza, czy potencjalnie sieciowych grup zabezpieczeń lub niewłaściwa konfiguracja trasy zdefiniowanej przez użytkownika blokuje ruch. 
        - Jeśli nie wychodzących pakietów na maszynie Wirtualnej w puli zaplecza, maszyna wirtualna musi być sprawdzane pod kątem problemów niepowiązane (na przykład aplikacja blokuje port sondy). 
    - Upewnij się, jeśli pakiety sondy są być zmuszonym do innego miejsca docelowego (prawdopodobnie przez ustawienia trasy zdefiniowanej przez użytkownika) przed dotarciem do modułu równoważenia obciążenia. Może to spowodować, że ruch nigdy nie dotrzeć do wewnętrznej bazy danych maszyny Wirtualnej. 
* Zmień typ sondowania, na przykład HTTP (TCP), a następnie skonfiguruj odpowiedni port w sieciowych grup zabezpieczeń listy kontroli dostępu i zapory, aby sprawdzić, czy problem dotyczy konfiguracji sondy odpowiedzi. Aby uzyskać więcej informacji na temat konfiguracji sondy kondycji, zobacz [równoważenia obciążenia punktu końcowego konfiguracji sondy kondycji](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Objaw: Maszyny wirtualne za modułem równoważenia obciążenia nie odpowiadają na ruch na porcie skonfigurowanym danych

Jeśli pula zaplecza maszyny Wirtualnej znajduje się w dobrej kondycji i odpowiada sond kondycji, ale nadal nie uczestniczy w równoważeniu obciążenia lub nie odpowiada na ruch danych, może to być spowodowane jedną z następujących powodów: 
* Których maszyna wirtualna nie nasłuchuje na porcie danych w puli zaplecza modułu równoważenia obciążenia 
* Sieciowa grupa zabezpieczeń blokuje port w puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych  
* Uzyskiwanie dostępu do modułu równoważenia obciążenia z tej samej maszyny Wirtualnej i karty Sieciowej 
* Uzyskiwanie dostępu do frontonu internetowego modułu równoważenia obciążenia z uczestniczących w programie puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Przyczyny 1: Ładowanie puli zaplecza modułu równoważenia, których maszyna wirtualna nie nasłuchuje na porcie danych 
Jeśli maszyny Wirtualnej nie odpowiada na ruch danych, może to być spowodowane port docelowy nie jest otwarty na uczestnicząca maszyna wirtualna lub maszyna wirtualna nie nasłuchuje na tym porcie. 

**Sprawdzanie poprawności i rozwiązania**

1. Zaloguj się do wewnętrznej bazy danych maszyny Wirtualnej. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby zweryfikować istnieje, to aplikacja nasłuchuje na porcie danych:  netstat - 
3. Jeśli port nie ma na liście ze stanem "NASŁUCHIWANIA" Konfigurowanie portu odpowiedniego odbiornika 
4. Jeśli port jest oznaczony jako Listening, sprawdź aplikacji docelowej, na tym porcie wszelkie problemy. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Przyczyny 2: Sieciowa grupa zabezpieczeń blokuje port w puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych  

Jeśli co najmniej jedną grupę zabezpieczeń sieci skonfigurowane w tej podsieci lub na maszynie Wirtualnej, blokuje źródłowy adres IP lub portu, a następnie maszyna wirtualna jest w stanie udzielić odpowiedzi.

* Lista grupy zabezpieczeń sieci skonfigurowane na maszynie Wirtualnej zaplecza. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md).
* Z listy grup zabezpieczeń sieci Sprawdź, czy:
    - przychodzący lub wychodzący ruch na porcie danych ma zakłóceń. 
    - **Odmów wszystkiego** reguły grupy zabezpieczeń na karcie interfejsu Sieciowego maszyny Wirtualnej lub podsieci, która ma wyższy priorytet, który sondy reguły domyślnej, który umożliwia modułu równoważenia obciążenia sieciowego i ruchu (sieciowe grupy zabezpieczeń muszą zezwalać na IP modułu równoważenia obciążenia z adresu 168.63.129.16, jest to port sondy) 
* Jeśli dowolne zasady blokują ruch, Usuń i ponownie skonfigurować te reguły zezwalające na ruch danych.  
* Sprawdź, czy maszyna wirtualna zostało teraz odpowiadać na sondy kondycji.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Przyczyny 3: Uzyskiwanie dostępu do modułu równoważenia obciążenia z tego samego interfejsu maszyny Wirtualnej i sieci 

Jeśli aplikacja hostowana w wewnętrznej bazie danych maszyny Wirtualnej z modułu równoważenia obciążenia próbuje uzyskać dostęp innej aplikacji hostowanej w tej samej maszynie Wirtualnej zaplecza za pośrednictwem tego samego interfejsu sieciowego, jest to nieobsługiwany scenariusz i zakończy się niepowodzeniem. 

**Rozpoznawanie** może rozwiązać ten problem, przy użyciu jednej z następujących metod:
* Konfigurowanie puli zaplecza osobne maszyny wirtualne na aplikację. 
* Konfigurowanie aplikacji na maszynach wirtualnych z dwóch kart Sieciowych, więc każda aplikacja używała swój własny interfejs sieciowy i adres IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Przyczyna 4: Uzyskiwanie dostępu do wewnętrznych frontonu modułu równoważenia obciążenia z uczestniczących w programie puli zaplecza modułu równoważenia obciążenia maszyn wirtualnych

Jeśli wewnętrznego modułu równoważenia obciążenia jest konfigurowana wewnątrz sieci wirtualnej, a jedną z maszyn wirtualnych zaplecza uczestnika próbuje uzyskać dostęp wewnętrznych frontonu modułu równoważenia obciążenia, błędów może wystąpić, gdy przepływ jest mapowany na źródłowej maszynie Wirtualnej. Ten scenariusz nie jest obsługiwany. Przegląd [ograniczenia](load-balancer-overview.md#limitations) szczegółowe omówienie.

**Rozpoznawanie** istnieje kilka sposobów, aby odblokować ten scenariusz, w tym o korzystaniu z serwera proxy. Ocena bramy aplikacji lub inne 3 firm serwery proxy (na przykład serwer nginx lub haproxy). Aby uzyskać więcej informacji na temat usługi Application Gateway, zobacz [Przegląd bramy Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Dodatkowe sieci
Jeśli zdecydujesz otworzyć zgłoszenie do pomocy technicznej, Zbierz następujące informacje dotyczące rozpoznawanie szybciej. Wybierz pojedynczego zaplecza maszyny Wirtualnej w celu wykonania następujących testów:
- Użycie programu psping od jednego z wewnętrznej bazy danych maszyn wirtualnych w sieci wirtualnej testowanie odpowiedzi port sondy (przykład: psping 10.0.0.4:3389) i Zarejestruj wyniki. 
- Jeśli odpowiedź nie zostanie odebrana w tych testów ping, należy uruchomić jednoczesnych Netsh trace na maszynie Wirtualnej zaplecza i testowej sieci wirtualnej maszyny Wirtualnej podczas uruchamiania PsPing następnie Zatrzymaj śledzenie Netsh. 
  
## <a name="next-steps"></a>Kolejne kroki

Jeśli poprzednie kroki nie rozwiązują ten problem, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).

