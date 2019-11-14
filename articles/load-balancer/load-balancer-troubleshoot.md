---
title: Rozwiązywanie problemów z usługą Azure Load Balancer
description: Dowiedz się, jak rozwiązywać znane problemy z Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: chadmath
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: d1c10fa8267131f13d3148ace6c97218a18fd494
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076920"
---
# <a name="troubleshoot-azure-load-balancer"></a>Rozwiązywanie problemów z usługą Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ta strona zawiera informacje dotyczące rozwiązywania problemów dotyczących typowych pytań Azure Load Balancer. Gdy Load Balancer łączność jest niedostępna, Najczęstsze objawy są następujące: 
- Maszyny wirtualne znajdujące się za Load Balancer nie odpowiadają na sondy kondycji 
- Maszyny wirtualne znajdujące się za Load Balancer nie odpowiadają na ruch na skonfigurowanym porcie

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Objaw: maszyny wirtualne znajdujące się za Load Balancer nie reagują na sondy kondycji
W przypadku serwerów zaplecza, które mają być uwzględnione w zestawie modułu równoważenia obciążenia, muszą one przekazywać sprawdzanie sondy. Aby uzyskać więcej informacji o sondach kondycji, zobacz [Opis sond Load Balancer](load-balancer-custom-probe-overview.md). 

Maszyny wirtualne puli zaplecza Load Balancer mogą nie odpowiadać na sondy z jednego z następujących powodów: 
- Maszyna wirtualna puli zaplecza Load Balancer jest w złej kondycji 
- Maszyna wirtualna puli zaplecza Load Balancer nie nasłuchuje na porcie sondy 
- Zapora lub sieciowa Grupa zabezpieczeń blokuje port na maszynach wirtualnych puli zaplecza Load Balancer 
- Inne niezgodne konfiguracje w Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Przyczyna 1: Load Balancer maszyny wirtualnej puli zaplecza są w złej kondycji 

**Sprawdzanie poprawności i rozwiązywanie problemów**

Aby rozwiązać ten problem, zaloguj się do uczestniczących maszyn wirtualnych i sprawdź, czy stan maszyny wirtualnej jest w dobrej kondycji i czy można odpowiedzieć na **PsPing** lub **TCPing** z innej maszyny wirtualnej w puli. Jeśli maszyna wirtualna jest w złej kondycji lub nie może odpowiedzieć na sondę, należy rozwiązać problem i odzyskać maszynę wirtualną do stanu dobrej kondycji, zanim będzie ona mogła uczestniczyć w równoważeniu obciążenia.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Przyczyna 2: Load Balancer maszyna wirtualna puli zaplecza nie nasłuchuje na porcie sondy
Jeśli maszyna wirtualna jest w dobrej kondycji, ale nie odpowiada na sondę, może to oznaczać, że port sondy nie jest otwarty na uczestniczącej maszynie wirtualnej lub maszyna wirtualna nie nasłuchuje na tym porcie.

**Sprawdzanie poprawności i rozwiązywanie problemów**

1. Zaloguj się do maszyny wirtualnej zaplecza. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby sprawdzić, czy aplikacja nasłuchuje na porcie sondy:   
            netstat-an
3. Jeśli stan portu nie jest wyświetlany na liście jako **nasłuchiwanie**, skonfiguruj właściwy port. 
4. Alternatywnie możesz wybrać inny port, który jest wyświetlany jako **nasłuchiwanie**i odpowiednio zaktualizować konfigurację modułu równoważenia obciążenia.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Przyczyna 3: Zapora lub sieciowa Grupa zabezpieczeń blokująca port na maszynach wirtualnych puli zaplecza modułu równoważenia obciążenia  
Jeśli Zapora na maszynie wirtualnej blokuje port sondy lub co najmniej jedna sieciowa Grupa zabezpieczeń skonfigurowana w podsieci lub na maszynie wirtualnej, nie zezwala na sondowanie do połączenia z portem, maszyna wirtualna nie może odpowiedzieć na sondę kondycji.          

**Sprawdzanie poprawności i rozwiązywanie problemów**

* Jeśli Zapora jest włączona, sprawdź, czy jest ona skonfigurowana do zezwalania na port sondy. W przeciwnym razie Skonfiguruj zaporę tak, aby zezwalała na ruch na porcie sondowania, i ponownie przetestuj. 
* Z listy grup zabezpieczeń sieci Sprawdź, czy ruch przychodzący lub wychodzący na porcie sondy ma zakłócenia. 
* Sprawdź również, czy reguła **Odmów wszystkich** grup zabezpieczeń sieci na karcie sieciowej maszyny wirtualnej lub podsieci o wyższym priorytecie niż reguła domyślna zezwalająca na sondy lb & ruchem (sieciowe grupy zabezpieczeń muszą zezwalać na Load Balancer IP 168.63.129.16). 
* Jeśli dowolna z tych reguł blokuje ruch sondy, Usuń i ponownie skonfiguruj reguły, aby zezwolić na ruch sondy.  
* Sprawdź, czy maszyna wirtualna rozpoczęła teraz reagowanie na sondy kondycji. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Przyczyna 4: inne błędy konfiguracji w Load Balancer
Jeśli wszystkie poprzednie przyczyny są sprawdzane poprawnie i są prawidłowo rozwiązywane, a maszyna wirtualna zaplecza nadal nie odpowiada na sondę kondycji, należy ręcznie przetestować łączność i zebrać niektóre ślady w celu zrozumienia łączności.

**Sprawdzanie poprawności i rozwiązywanie problemów**

* Użyj **Psping** z jednej z pozostałych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: .\psping.exe-t 10.0.0.4:3389) i zapisać wyniki. 
* Użyj **TCPing** z jednej z pozostałych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: .\tcping.exe 10.0.0.4 3389) i zapisać wyniki. 
* Jeśli w tych testach ping nie otrzymano odpowiedzi,
    - Uruchom jednoczesne śledzenie netsh na maszynie wirtualnej docelowej puli zaplecza i innej testowej maszynie wirtualnej z tej samej sieci wirtualnej. Teraz uruchom test PsPing przez jakiś czas, Zbierz dane śledzenia sieci, a następnie Zatrzymaj test. 
    - Przeanalizuj przechwytywanie sieci i sprawdź, czy istnieją pakiety przychodzące i wychodzące powiązane z kwerendą ping. 
        - Jeśli na maszynie wirtualnej puli zaplecza nie zaobserwowano żadnych pakietów przychodzących, istnieje potencjalnie Grupa zabezpieczeń sieci lub UDR nieprawidłowo skonfigurowany ruch. 
        - Jeśli na maszynie wirtualnej puli zaplecza nie zaobserwowano żadnych pakietów wychodzących, maszyna wirtualna musi być sprawdzona pod kątem wszelkich niepowiązanych problemów (na przykład aplikacja blokująca port sondy). 
    - Sprawdź, czy pakiety sondy są wymuszane w innym miejscu docelowym (prawdopodobnie za pośrednictwem ustawień UDR) przed osiągnięciem modułu równoważenia obciążenia. Może to spowodować, że ruch nigdy nie dociera do maszyny wirtualnej zaplecza. 
* Zmień typ sondy (na przykład HTTP na TCP) i skonfiguruj odpowiedni port na listach ACL sieciowych grup zabezpieczeń i zaporze, aby sprawdzić, czy problem dotyczy konfiguracji odpowiedzi sondy. Aby uzyskać więcej informacji na temat konfiguracji sondowania kondycji, zobacz [Konfiguracja sondy kondycji równoważenia obciążenia punktu końcowego](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Objaw: maszyny wirtualne znajdujące się za Load Balancer nie odpowiadają na ruch na skonfigurowanym porcie danych

Jeśli maszyna wirtualna puli zaplecza jest wyświetlana jako dobra kondycja i reaguje na sondy kondycji, ale nadal nie uczestniczy w równoważeniu obciążenia lub nie odpowiada na ruch danych, może to być spowodowane jedną z następujących przyczyn: 
* Maszyna wirtualna puli zaplecza Load Balancer nie nasłuchuje na porcie danych 
* Sieciowa Grupa zabezpieczeń blokuje port na maszynie wirtualnej puli zaplecza Load Balancer  
* Uzyskiwanie dostępu do Load Balancer z tej samej maszyny wirtualnej i karty sieciowej 
* Uzyskiwanie dostępu do Internetu Load Balancer frontonu z poziomu maszyny wirtualnej puli zaplecza Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Przyczyna 1: Load Balancer maszyna wirtualna puli zaplecza nie nasłuchuje na porcie danych 
Jeśli maszyna wirtualna nie odpowiada na ruch danych, może to być spowodowane tym, że port docelowy nie jest otwarty na danej maszynie wirtualnej lub maszyna wirtualna nie nasłuchuje na tym porcie. 

**Sprawdzanie poprawności i rozwiązywanie problemów**

1. Zaloguj się do maszyny wirtualnej zaplecza. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby sprawdzić, czy aplikacja nasłuchuje na porcie danych:  netstat-a 
3. Jeśli port nie jest wyświetlany w stanie "nasłuchiwanie", skonfiguruj odpowiedni port odbiornika 
4. Jeśli port jest oznaczony jako nasłuch, Sprawdź aplikację docelową na tym porcie, aby uzyskać ewentualne problemy. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Przyczyna 2: sieciowa Grupa zabezpieczeń blokuje port na maszynie wirtualnej puli zaplecza Load Balancer  

Jeśli co najmniej jedna sieciowa Grupa zabezpieczeń skonfigurowana w podsieci lub na maszynie wirtualnej blokuje źródłowy adres IP lub port, maszyna wirtualna nie będzie mogła odpowiedzieć.

* Wyświetl listę grup zabezpieczeń sieci skonfigurowanych na maszynie wirtualnej zaplecza. Aby uzyskać więcej informacji, zobacz [Zarządzanie sieciowymi grupami zabezpieczeń](../virtual-network/manage-network-security-group.md).
* Z listy grup zabezpieczeń sieci Sprawdź, czy:
    - ruch przychodzący lub wychodzący na porcie danych ma zakłócenia. 
    - reguła **odmowa wszystkich** sieci sieciowej grupy zabezpieczeń na karcie sieciowej maszyny wirtualnej lub podsieci o wyższym priorytecie, która zezwala na Load Balancer sondy i ruch (sieciowe grupy zabezpieczeń muszą zezwalać na Load Balancer IP 168.63.129.16, który jest port sondy) 
* Jeśli którakolwiek z reguł blokuje ruch, Usuń i skonfiguruj ponownie te reguły, aby zezwolić na ruch danych.  
* Sprawdź, czy maszyna wirtualna została teraz uruchomiona, aby odpowiedzieć na sondy kondycji.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Przyczyna 3: uzyskiwanie dostępu do Load Balancer z tej samej maszyny wirtualnej i interfejsu sieciowego 

Jeśli aplikacja hostowana na maszynie wirtualnej zaplecza Load Balancer próbuje uzyskać dostęp do innej aplikacji hostowanej w ramach tej samej maszyny wirtualnej zaplecza za pośrednictwem tego samego interfejsu sieciowego, jest to nieobsługiwany scenariusz i zakończy się niepowodzeniem. 

**Rozwiązanie** Ten problem można rozwiązać, korzystając z jednej z następujących metod:
* Skonfiguruj oddzielne maszyny wirtualne puli zaplecza dla każdej aplikacji. 
* Skonfiguruj aplikację na maszynach wirtualnych z dwoma KARTAmi sieciowymi, aby każda aplikacja była używana przez własny interfejs sieciowy i adres IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Przyczyna 4: uzyskiwanie dostępu do wewnętrznego frontonu Load Balancer z poziomu maszyny wirtualnej puli zaplecza Load Balancer

Jeśli wewnętrzny Load Balancer jest skonfigurowany wewnątrz sieci wirtualnej, a jedna z maszyn wirtualnych zaplecza uczestnika próbuje uzyskać dostęp do wewnętrznej frontonu Load Balancer, mogą wystąpić błędy, gdy przepływ jest mapowany na źródłową maszynę wirtualną. Ten scenariusz nie jest obsługiwany. Przejrzyj [ograniczenia](load-balancer-overview.md#limitations) dotyczące szczegółowej dyskusji.

**Rozwiązanie** Istnieje kilka sposobów odblokowania tego scenariusza, w tym za pomocą serwera proxy. Oceń Application Gateway lub inne serwery proxy innych firm (na przykład Nginx lub haproxy). Aby uzyskać więcej informacji na temat Application Gateway, zobacz [omówienie Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Dodatkowe przechwycenia sieci
Jeśli zdecydujesz się otworzyć zgłoszenie do pomocy technicznej, Zbierz poniższe informacje w celu szybszego rozwiązania problemu. Wybierz pojedynczą maszynę wirtualną zaplecza, aby wykonać następujące testy:
- Użyj Psping z jednej z maszyn wirtualnych zaplecza w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: Psping 10.0.0.4:3389) i zapisać wyniki. 
- Jeśli nie otrzymasz odpowiedzi w tych testach ping, uruchom jednoczesne śledzenie netsh na maszynie wirtualnej zaplecza i testowej maszynie wirtualnej podczas uruchamiania PsPing, a następnie Zatrzymaj śledzenie netsh. 
  
## <a name="next-steps"></a>Następne kroki

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).

