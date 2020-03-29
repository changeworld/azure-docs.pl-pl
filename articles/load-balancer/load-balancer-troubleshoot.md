---
title: Rozwiązywanie problemów z usługą Azure Load Balancer
description: Dowiedz się, jak rozwiązywać znane problemy z modułem równoważenia obciążenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: ca9b70bd71a618f8e3d5f4fe9504ba66a9f14c6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935481"
---
# <a name="troubleshoot-azure-load-balancer"></a>Rozwiązywanie problemów z usługą Azure Load Balancer

Ta strona zawiera informacje dotyczące rozwiązywania problemów z podstawowymi i standardowymi typowymi pytaniami dotyczącymi równoważenia obciążenia platformy Azure. Aby uzyskać więcej informacji na temat standardowego modułu równoważenia obciążenia, zobacz [omówienie standardowego modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics).

Gdy łączność modułu równoważenia obciążenia jest niedostępna, najczęstsze objawy są następujące: 

- Maszyny wirtualne za modułem równoważenia obciążenia nie reagują na sondy kondycji 
- Maszyny wirtualne za modułem równoważenia obciążenia nie reagują na ruch na skonfigurowanym porcie

Gdy klienci zewnętrzni do maszyn wirtualnych wewnętrznej bazy danych przejść przez moduł równoważenia obciążenia, adres IP klientów będą używane do komunikacji. Upewnij się, że adres IP klientów są dodawane do listy dozwolonych sieciowej sieciowej. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: Maszyny wirtualne za modułem równoważenia obciążenia nie reagują na sondy kondycji
Aby serwery zaplecza mogły uczestniczyć w zestawie modułu równoważenia obciążenia, muszą one przejść kontrolę sondy. Aby uzyskać więcej informacji na temat sond kondycji, zobacz [Opis sondy równoważenia obciążenia](load-balancer-custom-probe-overview.md). 

Maszyna wirtualna wewnętrznej bazy danych modułu równoważenia obciążenia może nie odpowiadać na sondy z następujących powodów: 
- Maszyna wirtualna wewnętrznej bazy danych modułu równoważenia obciążenia jest w złej kondycji 
- Moduł równoważenia obciążenia zaplecza puli maszyny Wirtualnej nie nasłuchuje na porcie sondy 
- Zapora sieciowa lub sieciowa grupa zabezpieczeń blokuje port na maszynach wirtualnych wewnętrznej bazy modułów równoważenia obciążenia 
- Inne błędy konfiguracji w modułze równoważenia obciążenia

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Przyczyna 1: Pula wewnętrznej bazy danych modułu równoważenia obciążenia jest w złej kondycji 

**Walidacja i rozwiązywanie problemów**

Aby rozwiązać ten problem, zaloguj się do uczestniczących maszyn wirtualnych i sprawdź, czy stan maszyny Wirtualnej jest w dobrej kondycji i może odpowiadać na **psping** lub **TCPing** z innej maszyny wirtualnej w puli. Jeśli maszyna wirtualna jest w złej kondycji lub nie jest w stanie odpowiedzieć na sondę, należy rozwiązać problem i uzyskać maszynę wirtualną z powrotem do stanu dobrej kondycji, zanim będzie mogła uczestniczyć w równoważeniu obciążenia.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Przyczyna 2: Maszyna wirtualna wewnętrznej bazy danych modułu równoważenia obciążenia nie nasłuchuje na porcie sondy
Jeśli maszyna wirtualna jest w dobrej kondycji, ale nie odpowiada na sondę, jedną z możliwych przyczyn może być to, że port sondy nie jest otwarty na uczestniczącej maszynie wirtualnej lub maszyna wirtualna nie nasłuchuje na tym porcie.

**Walidacja i rozwiązywanie problemów**

1. Zaloguj się do wewnętrznej bazy wirtualnej. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby sprawdzić, czy na porcie sondy znajduje się aplikacja nasłuchująca:   
            netstat -an
3. Jeśli stan portu nie jest wymieniony jako **SŁUCHANIE,** skonfiguruj odpowiedni port. 
4. Alternatywnie wybierz inny port, który jest wymieniony jako **SŁUCHANIE**, i odpowiednio zaktualizować konfigurację modułu równoważenia obciążenia.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Przyczyna 3: Zapora lub sieciowa grupa zabezpieczeń blokuje port na maszynach wirtualnych wewnętrznej bazy modułów równoważenia obciążenia  
Jeśli zapora na maszynie wirtualnej blokuje port sondy lub co najmniej jedną grupę zabezpieczeń sieci ową skonfigurowaną w podsieci lub na maszynie wirtualnej, nie zezwala na dotarcie sondy do portu, maszyna wirtualna nie może odpowiedzieć na sondę kondycji.          

**Walidacja i rozwiązywanie problemów**

* Jeśli zapora jest włączona, sprawdź, czy jest skonfigurowana do zezwalania na port sondy. Jeśli nie, skonfiguruj zaporę, aby zezwolić na ruch na porcie sondy i ponownie przetestuj. 
* Z listy sieciowych grup zabezpieczeń sprawdź, czy ruch przychodzący lub wychodzący na porcie sondy ma zakłócenia. 
* Sprawdź również, czy reguła grup zabezpieczeń **Odmów wszystkie** sieci na karcie sieciowej maszyny Wirtualnej lub podsieci, która ma wyższy priorytet niż domyślna reguła umożliwiająca sondy LB & ruchu (sieciowe grupy zabezpieczeń muszą zezwalać na adres IP modułu równoważenia obciążenia 168.63.129.16). 
* Jeśli którakolwiek z tych reguł blokuje ruch sondy, usuń i ponownie skonfiguruj reguły, aby zezwolić na ruch sondy.  
* Sprawdź, czy maszyna wirtualna zaczęła odpowiadać na sondy kondycji. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Przyczyna 4: Inne błędy konfiguracji w modułze równoważenia obciążenia
Jeśli wszystkie powyższe przyczyny wydają się być sprawdzane i rozwiązane poprawnie, a maszyna wirtualna wewnętrznej bazy danych nadal nie odpowiada na sondę kondycji, a następnie ręcznie przetestować łączność i zebrać pewne ślady, aby zrozumieć łączność.

**Walidacja i rozwiązywanie problemów**

* Użyj **psping** z jednej z innych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: .\psping.exe -t 10.0.0.4:3389) i rejestrować wyniki. 
* Użyj **TCPing** z jednej z innych maszyn wirtualnych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: .\tcping.exe 10.0.0.4 3389) i rejestrować wyniki. 
* Jeśli w tych testach pingu nie zostanie otrzymana żadna odpowiedź,
    - Uruchom jednoczesne śledzenie Netsh na docelowej puli wewnętrznej bazy danych maszyny Wirtualnej i innego testu maszyny Wirtualnej z tej samej sieci wirtualnej. Teraz uruchom test PsPing przez jakiś czas, zbierz kilka śladów sieci, a następnie zatrzymaj test. 
    - Przeanalizuj przechwytywanie sieci i sprawdź, czy istnieją zarówno pakiety przychodzące, jak i wychodzące związane z kwerendą ping. 
        - Jeśli na maszynie wirtualnej puli wewnętrznej bazy danych nie są obserwowane żadne pakiety przychodzące, potencjalnie istnieje sieciowa grupa zabezpieczeń lub nieprawidłowa konfiguracja UDR blokująca ruch. 
        - Jeśli na maszynie wirtualnej puli wewnętrznej bazy danych nie są obserwowane żadne pakiety wychodzące, maszyna wirtualna musi zostać sprawdzona pod kątem niepowiązanych problemów (na przykład aplikacja blokująca port sondy). 
    - Sprawdź, czy pakiety sondy są wymuszane do innego miejsca docelowego (ewentualnie za pomocą ustawień UDR) przed osiągnięciem modułu równoważenia obciążenia. Może to spowodować, że ruch nigdy nie osiągnie wewnętrznej bazy wirtualnej. 
* Zmień typ sondy (na przykład HTTP na TCP) i skonfiguruj odpowiedni port w sieciowych grupach zabezpieczeń Listy ACL i zapora, aby sprawdzić, czy problem dotyczy konfiguracji odpowiedzi sondy. Aby uzyskać więcej informacji na temat konfiguracji sondy kondycji, zobacz [Konfiguracja sondy kondycji równoważenia obciążenia punktu końcowego](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptom: Maszyny wirtualne za modułem równoważenia obciążenia nie reagują na ruch na skonfigurowanym porcie danych

Jeśli maszyna wirtualna puli wewnętrznej bazy danych jest wymieniona jako w dobrej kondycji i odpowiada na sondy kondycji, ale nadal nie uczestniczy w równoważeniu obciążenia lub nie odpowiada na ruch danych, może to być spowodowane dowolną z następujących przyczyn: 
* Maszyna wirtualna wewnętrznej bazy danych modułu równoważenia obciążenia nie nasłuchuje na porcie danych 
* Sieciowa grupa zabezpieczeń blokuje port na pulę wewnętrznej bazy danych Modułu Równoważenia obciążenia  
* Uzyskiwanie dostępu do modułu równoważenia obciążenia z tej samej maszyny wirtualnej i karty sieciowej 
* Uzyskiwanie dostępu do frontu modułu równoważenia obciążenia internetowego z uczestniczącej puli wewnętrznej bazy danych Modułu Równoważenia obciążenia 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Przyczyna 1: Maszyna wirtualna wewnętrznej bazy danych modułu równoważenia obciążenia nie nasłuchuje na porcie danych 
Jeśli maszyna wirtualna nie odpowiada na ruch danych, może to być spowodowane tym, że port docelowy nie jest otwarty na uczestniczącej maszynie wirtualnej lub maszyna wirtualna nie nasłuchuje na tym porcie. 

**Walidacja i rozwiązywanie problemów**

1. Zaloguj się do wewnętrznej bazy wirtualnej. 
2. Otwórz wiersz polecenia i uruchom następujące polecenie, aby sprawdzić, czy  na porcie danych znajduje się aplikacja nasłuchiwania: netstat -an 
3. Jeśli port nie jest wymieniony z "SŁUCHANIEM" stanu, skonfiguruj odpowiedni port odbiornika 
4. Jeśli port jest oznaczony jako nasłuchiwanie, sprawdź aplikację docelową na tym porcie, aby uzyskać wszelkie możliwe problemy.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Przyczyna 2: Grupa zabezpieczeń sieci blokuje port na pulę wewnętrznej bazy danych Modułu Równoważenia obciążenia  

Jeśli co najmniej jedna sieciowa grupa zabezpieczeń skonfigurowana w podsieci lub na maszynie wirtualnej blokuje źródłowy adres IP lub port, maszyna wirtualna nie może odpowiedzieć.

W przypadku modułu równoważenia obciążenia publicznego adres IP klientów internetowych będzie używany do komunikacji między klientami a maszynami wirtualnymi wewnętrznej bazy danych modułu równoważenia obciążenia. Upewnij się, że adres IP klientów jest dozwolony w sieciowej grupie zabezpieczeń maszyny Wirtualnej wewnętrznej bazy danych.

1. Wyświetl listę sieciowych grup zabezpieczeń skonfigurowanych na wewnętrznej wynajęciowej maszynie wirtualnej. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md)
1. Z listy grup zabezpieczeń sieci sprawdź, czy:
    - ruch przychodzący lub wychodzący na porcie danych ma zakłócenia. 
    - a **Reguła Odmów wszystkie** sieciowe grupy zabezpieczeń na karcie sieciowej maszyny Wirtualnej lub podsieci o wyższym priorytecie niż domyślna reguła umożliwiająca sondowanie i ruch modułu równoważenia obciążenia (sieciowe grupy zabezpieczeń muszą zezwalać na adres IP modułu równoważenia obciążenia 168.63.129.16, czyli port sondy)
1. Jeśli którakolwiek z reguł blokuje ruch, usuń i skonfiguruj ponownie te reguły, aby zezwolić na ruch danych.  
1. Sprawdź, czy maszyna wirtualna zaczęła teraz odpowiadać na sondy kondycji.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Przyczyna 3: Uzyskiwanie dostępu do modułu równoważenia obciążenia z tej samej maszyny wirtualnej i interfejsu sieciowego 

Jeśli aplikacja hostowana w wewnętrznej bazy wirtualnej modułu równoważenia obciążenia próbuje uzyskać dostęp do innej aplikacji hostowanej w tej samej wewnętrznej maszynie wirtualnej bazy danych za pośrednictwem tego samego interfejsu sieciowego, jest to nieobsługinięty scenariusz i zakończy się niepowodzeniem. 

**Rozdzielczość** Ten problem można rozwiązać za pomocą jednej z następujących metod:
* Konfigurowanie oddzielnych maszyn wirtualnych puli wewnętrznej bazy danych dla aplikacji. 
* Skonfiguruj aplikację w dwóch maszynach wirtualnych kart sieciowych, aby każda aplikacja używała własnego interfejsu sieciowego i adresu IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Przyczyna 4: Uzyskiwanie dostępu do wewnętrznej wewnętrznej wewnętrznej bazy danych modułu równoważenia obciążenia z uczestniczącej puli wewnętrznej bazy danych Modułu Równoważenia obciążenia

If an internal Load Balancer is configured inside a VNet, and one of the participant backend VMs is trying to access the internal Load Balancer frontend, failures can occur when the flow is mapped to the originating VM. Ten scenariusz nie jest obsługiwany. Przejrzyj [ograniczenia](concepts-limitations.md#limitations) szczegółowej dyskusji.

**Rozdzielczość** Istnieje kilka sposobów, aby odblokować ten scenariusz, w tym przy użyciu serwera proxy. Oceń bramę aplikacji lub inne serwery proxy innych firm (na przykład nginx lub haproxy). Aby uzyskać więcej informacji na temat bramy aplikacji, zobacz [Omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Symptom: Nie można zmienić portu wewnętrznej bazy danych dla istniejącej reguły LB modułu równoważenia obciążenia, który ma zestaw skalowania maszyny Wirtualnej wdrożony w puli wewnętrznej bazy danych. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Przyczyna: Nie można zmodyfikować portu wewnętrznej bazy danych dla reguły równoważenia obciążenia, która jest używana przez sondę kondycji dla modułu równoważenia obciążenia, do którego odwołuje się zestaw skalowania maszyny Wirtualnej.
**Rozdzielczość** Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyny Wirtualnej, zaktualizować port, a następnie ponownie skonfigurować sondę kondycji.

## <a name="additional-network-captures"></a>Dodatkowe przechwytywanie sieci
Jeśli zdecydujesz się otworzyć sprawę pomocy technicznej, zbierz następujące informacje, aby szybciej rozwiązać problem. Wybierz jedną maszynę wirtualną wewnętrznej bazy danych, aby wykonać następujące testy:
- Użyj psping z jednej z maszyn wirtualnych wewnętrznej bazy danych w sieci wirtualnej, aby przetestować odpowiedź portu sondy (przykład: psping 10.0.0.4:3389) i rejestrować wyniki. 
- Jeśli w tych testach ping nie zostanie odebrana żadna odpowiedź, uruchom jednoczesną śledzenie netsh na maszynie wirtualnej wewnętrznej bazy danych i maszynie wirtualnej podczas uruchamiania protokołu PsPing, a następnie zatrzymaj śledzenie netsh. 
  
## <a name="next-steps"></a>Następne kroki

Jeśli poprzednie kroki nie rozwiążą problemu, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).

