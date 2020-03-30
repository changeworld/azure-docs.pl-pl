---
title: Reguły ruchu wychodzącego — równoważenie obciążenia platformy Azure
description: Dzięki tej ścieżce szkoleniowej zacznij używać reguł ruchu wychodzącego do definiowania wychodzących transtacji adresów sieciowych.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304195"
---
# <a name="load-balancer-outbound-rules"></a>Reguły przy balance balancer ruchu wychodzącego

Moduł równoważenia obciążenia platformy Azure zapewnia łączność wychodzącą z sieci wirtualnej oprócz ruchu przychodzącego.  Reguły ruchu wychodzącego ułatwiają konfigurowanie publicznego [standardowego równoważenia obciążenia](load-balancer-standard-overview.md)wychodzącego translacji adresów sieciowych.  Masz pełną kontrolę deklaratywną nad łącznością wychodzącą, aby skalować i dostroić tę możliwość do określonych potrzeb.

![Reguły przy balance balancer ruchu wychodzącego](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

W przypadku reguł ruchu wychodzącego można użyć modułu równoważenia obciążenia, aby: 
- definiować wychodzący NAT od podstaw.
- skalować i dostrajać zachowanie istniejącego wychodzącego translatora treści. 

Reguły ruchu wychodzącego umożliwiają kontrolowanie:
- które maszyny wirtualne powinny być tłumaczone, na które publiczne adresy IP. 
- jak [należy przydzielić wychodzące porty SNAT.](load-balancer-outbound-connections.md#snat)
- protokoły, dla których można zapewnić tłumaczenie wychodzące.
- jaki czas trwania ma być używany dla limitu czasu bezczynnego połączenia wychodzącego (4-120 minut).
- czy wysłać reset TCP przy limit czasu bezczynnego

Reguły wychodzące rozwiń [scenariusz 2](load-balancer-outbound-connections.md#lb) w opisane w [artykule połączenia wychodzące](load-balancer-outbound-connections.md) i pierwszeństwo scenariusza pozostaje tak, jak jest.

## <a name="outbound-rule"></a>Reguła ruchu wychodzącego

Podobnie jak wszystkie reguły modułu równoważenia obciążenia, reguły ruchu wychodzącego są zgodne z tą samą znaną składnią, co równoważenie obciążenia i przychodzące reguły translatora nat:

**frontend** + **Pula** **parametrów** + wewnętrznej bazy danych

Reguła ruchu wychodzącego konfiguruje wychodzący translator adresów sieciowych dla _wszystkich maszyn wirtualnych zidentyfikowanych przez pulę wewnętrznej bazy danych,_ które mają zostać przetłumaczone na _fronton_.  A _parametry_ zapewniają dodatkową szczegółową kontrolę nad wychodzącym algorytmem NAT.

Wersja interfejsu API "2018-07-01" zezwala na definicję reguły ruchu wychodzącego w następujący sposób:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Skuteczna konfiguracja wychodzącego translatora i sieci na rynku jest złożona ze wszystkich reguł wychodzących i równoważenia obciążenia. Reguły ruchu wychodzącego są przyrostowe do reguł równoważenia obciążenia. Przejrzyj [wyłączenie wychodzącego translatora adresów sieciowych dla reguły równoważenia obciążenia,](#disablesnat) aby zarządzać skutecznym wychodzącym tłumaczeniem NAT, gdy do maszyny wirtualnej stosuje się wiele reguł. Należy [wyłączyć wychodzące SNAT](#disablesnat) podczas definiowania reguły ruchu wychodzącego, który używa tego samego publicznego adresu IP jako reguły równoważenia obciążenia.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skalowanie wychodzącego translatora adresów SIECIowych przy użyciu wielu adresów IP

Podczas gdy reguły ruchu wychodzącego można używać tylko z jednym publicznym adresem IP, reguły wychodzące ułatwiają obciążenie konfiguracji skalowania wychodzącego translatora adresów sieciowych. Można użyć wielu adresów IP do planowania scenariuszy na dużą skalę i można użyć reguł wychodzących w celu [złagodzenia wzorców podatnych na wyczerpanie SNAT.](load-balancer-outbound-connections.md#snatexhaust)  

Każdy dodatkowy adres IP dostarczony przez fronton zapewnia 64 000 portów tymczasowych dla modułu równoważenia obciążenia do użycia jako porty SNAT. Podczas gdy równoważenie obciążenia lub przychodzące reguły NAT mają pojedynczy frontend, reguła wychodząca rozszerza pojęcie frontend i umożliwia wiele frontendów na regułę.  Z wielu frontonów na regułę, ilość dostępnych portów SNAT jest mnożona z każdego publicznego adresu IP i duże scenariusze mogą być obsługiwane.

Ponadto można użyć [publicznego prefiksu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą.  Za pomocą publicznego prefiksu IP zapewnia łatwiejsze skalowanie i uproszczoną białą listę przepływów pochodzących z wdrożenia platformy Azure. Konfigurację ip wewnętrznej bazy danych można skonfigurować w zasobie Modułu równoważenia obciążenia, aby bezpośrednio odwoływać się do prefiksu publicznego adresu IP.  Dzięki temu wyłączna kontrola modułu równoważenia obciążenia nad publicznym prefiksem IP i reguła wychodząca automatycznie użyje wszystkich publicznych adresów IP zawartych w publicznym prefiksie IP dla połączeń wychodzących.  Każdy z adresów IP w zakresie publicznego prefiksu IP zapewnia 64 000 portów efemerycznych na adres IP dla modułu równoważenia obciążenia używanego jako porty SNAT.   

Nie można utworzyć pojedynczych publicznych zasobów adresów IP utworzonych na podstawie publicznego prefiksu IP podczas korzystania z tej opcji, ponieważ reguła wychodząca musi mieć pełną kontrolę nad publicznym prefiksem IP.  Jeśli potrzebujesz bardziej szczegółowej kontroli, można utworzyć pojedynczy zasób publicznego adresu IP z publicznego prefiksu IP i przypisać wiele publicznych adresów IP indywidualnie do frontendu reguły wychodzącej.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Dostrajanie alokacji portów SNAT

Reguły ruchu wychodzącego umożliwiają dostrojenie [automatycznej alokacji portów SNAT na podstawie rozmiaru puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) i przydzielenie więcej lub mniej niż zapewnia automatyczna alokacja portów SNAT.

Poniższy parametr służy do przydzielenia 10 000 portów SNAT na maszynę wirtualną (konfiguracja karty sieciowej.
 

          "allocatedOutboundPorts": 10000

Każdy publiczny adres IP ze wszystkich frontonów reguły ruchu wychodzącego współtworzy do 64 000 portów tymczasowych do użycia jako porty SNAT.  Moduł równoważenia obciążenia przydziela porty SNAT w wielokrotności 8. Jeśli podasz wartość nie podzielną przez 8, operacja konfiguracji zostanie odrzucona.  Jeśli spróbujesz przydzielić więcej portów SNAT niż są dostępne na podstawie liczby publicznych adresów IP, operacja konfiguracji zostanie odrzucona.  Na przykład jeśli przydzielić 10 000 portów na maszynę wirtualną i 7 maszyn wirtualnych w puli wewnętrznej bazy danych będzie współużytkować jeden publiczny adres IP, konfiguracja zostanie odrzucona (7 x 10 000 portów SNAT > 64 000 portów SNAT).  Można dodać więcej publicznych adresów IP do frontend reguły ruchu wychodzącego, aby włączyć scenariusz.

Można przywrócić do [automatycznej alokacji portów SNAT na podstawie rozmiaru puli wewnętrznej bazy danych,](load-balancer-outbound-connections.md#preallocatedports) określając 0 dla liczby portów. W takim przypadku pierwsze 50 wystąpień maszyny Wirtualnej otrzyma 1024 porty, 51-100 wystąpień maszyn wirtualnych otrzyma 512 i tak dalej zgodnie z tabelą.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Sterowanie limitem czasu bezczynności przepływu wychodzącego

Reguły ruchu wychodzącego zapewniają parametr konfiguracji, aby kontrolować limit czasu bezczynności przepływu wychodzącego i dopasować go do potrzeb aplikacji.  Limity czasu bezczynnego ruchu wychodzącego domyślnie 4 minuty.  Parametr akceptuje wartość od 4 do 120 do określonej liczby minut dla limitu czasu bezczynności dla przepływów pasujących do tej konkretnej reguły.

Użyj następującego parametru, aby ustawić limit czasu bezczynność ruchu wychodzącego na 1 godzinę:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> Włączanie resetowania protokołu TCP przy przekroju czasu bezczynnego

Domyślnym zachowaniem modułu równoważenia obciążenia jest dyskretne upuszczenie przepływu po osiągnięciu limitu czasu bezczynności wychodzącej.  Za pomocą parametru enableTCPReset można włączyć bardziej przewidywalne zachowanie aplikacji i kontrolować, czy w momencie braku limitu czasu bezczynności wychodzącego można wysłać dwukierunkowy reset TCP Reset (TCP RST). 

Użyj następującego parametru, aby włączyć resetowanie TCP w regule ruchu wychodzącego:

           "enableTcpReset": true

Przejrzyj [resetowanie TCP przy limitie czasu bezczynności,](https://aka.ms/lbtcpreset) aby uzyskać szczegółowe informacje, w tym dostępność regionu.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Obsługa protokołów transportu TCP i UDP za pomocą jednej reguły

Prawdopodobnie będzie można użyć "Wszystkie" dla protokołu transportu reguły wychodzącej, ale można również zastosować regułę ruchu wychodzącego do określonego protokołu transportu, jak również, jeśli istnieje taka potrzeba.

Użyj następującego parametru, aby ustawić protokół na TCP i UDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Wyłączanie wychodzącego translatora przechyłowego dla reguły równoważenia obciążenia

Jak wspomniano wcześniej, reguły równoważenia obciążenia zapewniają automatyczne programowanie wychodzącego translatora nat. Jednak niektóre scenariusze korzyści lub wymagają, aby wyłączyć automatyczne programowanie wychodzącego TRANSLATORA przez regułę równoważenia obciążenia, aby umożliwić kontrolę lub uściślać zachowanie.  Reguły ruchu wychodzącego mają scenariusze, w których ważne jest, aby zatrzymać automatyczne wychodzące programowanie NAT.

Tego parametru można użyć na dwa sposoby:
- Opcjonalne pomijanie przy użyciu przychodzącego adresu IP dla wychodzącego translatora adresów sieciowych.  Reguły ruchu wychodzącego są przyrostowe do równoważenia obciążenia reguł i z tym zestawem parametrów reguła wychodząca jest w kontroli.
  
- Dostrajanie wychodzących parametrów NAT adresu IP używanego jednocześnie do przychodzących i wychodzących.  Automatyczne wychodzące programowanie NAT musi być wyłączone, aby umożliwić przejęcie kontroli nad regułą ruchu wychodzącego.  Na przykład w celu zmiany alokacji portu SNAT adresu używanego również dla ruchu przychodzącego, ten parametr musi być ustawiony na true.  Jeśli spróbujesz użyć reguły ruchu wychodzącego do ponownego zdefiniowania parametrów adresu IP używanego również dla ruchu przychodzącego i nie zwolnisz wychodzącego programowania NAT reguły równoważenia obciążenia, operacja konfigurowania reguły ruchu wychodzącego zakończy się niepowodzeniem.

>[!IMPORTANT]
> Maszyna wirtualna nie będzie miała łączności wychodzącej, jeśli ten parametr zostanie ustawiony na true i nie będzie miał reguły wychodzącej (lub [scenariusza publicznego adresu IP na poziomie wystąpienia](load-balancer-outbound-connections.md#ilpip) w celu zdefiniowania łączności wychodzącej.  Niektóre operacje maszyny Wirtualnej lub aplikacji może zależeć od dostępności łączności wychodzącej. Upewnij się, że rozumiesz zależności scenariusza i rozważyłeś wpływ wprowadzania tej zmiany.

Przychodzących SNAT można wyłączyć w regule równoważenia obciążenia za pomocą tego parametru konfiguracji:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT domyślnie ma wartość false, co oznacza, że **reguła** równoważenia obciążenia zapewnia automatyczny wychodzący nat jako lustrzane odbicie konfiguracji reguł równoważenia obciążenia.  

Jeśli ustawisz disableOutboundSnat true w regule równoważenia obciążenia, reguła równoważenia obciążenia zwalnia kontrolę nad automatycznym wychodzącym programowaniem NAT.  Wychodzące SNAT w wyniku reguły równoważenia obciążenia jest wyłączona.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Ponowne używanie istniejących lub definiowanie nowych pul zaplecza

Reguły ruchu wychodzącego nie wprowadzają nowej koncepcji definiowania grupy maszyn wirtualnych, do których reguła powinna mieć zastosowanie.  Zamiast tego ponownie użyć koncepcji puli wewnętrznej bazy danych, która jest również używana dla reguł równoważenia obciążenia. Można użyć tego, aby uprościć konfigurację, ponownie korzystając z istniejącej definicji puli wewnętrznej bazy danych lub tworząc ją specjalnie dla reguły wychodzącej.

## <a name="scenarios"></a>Scenariusze

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Uwodzenie połączeń wychodzących z określonym zestawem publicznych adresów IP

Reguła wychodząca służy do tworzenia funkcji wyboru połączeń wychodzących, które mają pochodzić z określonego zestawu publicznych adresów IP w celu ułatwienia scenariuszy umieszczania na białej liście.  Ten publiczny adres IP źródła może być taki sam, jak używany przez regułę równoważenia obciążenia lub inny zestaw publicznych adresów IP niż używany przez regułę równoważenia obciążenia.  

1. Tworzenie [publicznego prefiksu IP](https://aka.ms/lbpublicipprefix) (lub publicznych adresów IP z publicznego prefiksu IP)
2. Tworzenie publicznej usługi Load Balancer w warstwie Standardowa
3. Tworzenie frontendów odwołujących się do publicznego prefiksu IP (lub publicznych adresów IP), którego chcesz użyć
4. Ponowne używanie puli zaplecza lub tworzenie puli zaplecza i umieszczanie maszyn wirtualnych w puli wewnętrznej bazy danych publicznego modułu równoważenia obciążenia
5. Konfigurowanie reguły ruchu wychodzącego na publicznym równoważku obciążenia do programowania wychodzącego translatora treści dla tych maszyn wirtualnych przy użyciu frontendów
   
Jeśli nie chcesz, aby reguła równoważenia obciążenia była używana dla ruchu wychodzącego, należy [wyłączyć wychodzącą funkcję SNAT](#disablesnat) w regule równoważenia obciążenia.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Modyfikowanie alokacji portów SNAT

Reguły ruchu wychodzącego umożliwiają dostrojenie [automatycznej alokacji portów SNAT na podstawie rozmiaru puli zaplecza](load-balancer-outbound-connections.md#preallocatedports).

Na przykład jeśli masz dwie maszyny wirtualne współużytkujące jeden publiczny adres IP dla wychodzącego translatora adresów sieciowych, można zwiększyć liczbę portów SNAT przydzielonych z domyślnych portów 1024, jeśli występują wyczerpanie SNAT. Każdy publiczny adres IP może przyczynić się do 64 000 portów tymczasowych.  Jeśli skonfigurujesz regułę ruchu wychodzącego z pojedynczym frontendem publicznego adresu IP, możesz dystrybuować łącznie 64 000 portów SNAT do maszyn wirtualnych w puli wewnętrznej bazy danych.  W przypadku dwóch maszyn wirtualnych można przydzielić maksymalnie 32 000 portów SNAT z regułą wychodzącą (2x 32 000 = 64 000).

Przejrzyj [połączenia wychodzące](load-balancer-outbound-connections.md) i szczegóły dotyczące przydzielania i korzystania z portów [SNAT.](load-balancer-outbound-connections.md#snat)

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Włącz tylko ruch wychodzący

Za pomocą publicznego standardowego modułu równoważenia obciążenia można zapewnić wychodzący translator z osią wirtualną dla grupy maszyn wirtualnych. W tym scenariuszu można użyć reguły ruchu wychodzącego przez siebie, bez konieczności żadnych dodatkowych reguł.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Wychodzący translator z o.o. tylko dla maszyn wirtualnych (bez ruchu przychodzącego)

Zdefiniuj publiczny standardowy moduł równoważenia obciążenia, umieść maszyny wirtualne w puli wewnętrznej bazy danych i skonfiguruj regułę wychodzącą, aby zaprogramować wychodzący translator adresów NAT i oczyszczenie połączeń wychodzących pochodzących z określonego publicznego adresu IP. Można również użyć publicznego prefiksu IP upraszczaj białe listy źródła połączeń wychodzących.

1. Tworzenie publicznego standardowego modułu równoważenia obciążenia.
2. Utwórz pulę wewnętrznej bazy danych i umieść maszyny wirtualne w puli wewnętrznej bazy danych publicznego modułu równoważenia obciążenia.
3. Skonfiguruj regułę ruchu wychodzącego na publicznym równoważku obciążenia, aby zaprogramować wychodzący translator z prawa nawigacyjnego dla tych maszyn wirtualnych.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Wychodzący translator z osią i nat dla wewnętrznych standardowych scenariuszy równoważenia obciążenia

W przypadku korzystania z wewnętrznego standardowego modułu równoważenia obciążenia wychodzący translator z treścią nie jest dostępny, dopóki łączność wychodząca nie zostanie jawnie zadeklarowana. Łączność wychodzącą można zdefiniować przy użyciu reguły ruchu wychodzącego w celu utworzenia łączności wychodzącej dla maszyn wirtualnych za wewnętrznym standardowym modułem równoważenia obciążenia za pomocą następujących kroków:

1. Tworzenie publicznego standardowego modułu równoważenia obciążenia.
2. Utwórz pulę wewnętrznej bazy danych i umieść maszyny wirtualne w puli wewnętrznej bazy danych publicznego modułu równoważenia obciążenia oprócz wewnętrznego modułu równoważenia obciążenia.
3. Skonfiguruj regułę ruchu wychodzącego na publicznym równoważku obciążenia, aby zaprogramować wychodzący translator z prawa nawigacyjnego dla tych maszyn wirtualnych.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Włączanie zarówno protokołów TCP, jak & UDP dla wychodzącego translatora adresów nat za pomocą publicznego standardowego modułu równoważenia obciążenia

- W przypadku korzystania z publicznego standardowego modułu równoważenia obciążenia automatyczne wychodzące programowanie NAT jest zgodne z protokołem transportu reguły równoważenia obciążenia.  

   1. Wyłącz wychodzący SNAT w regule równoważenia obciążenia.
   2. Skonfiguruj regułę ruchu wychodzącego na tym samym modułze równoważenia obciążenia.
   3. Ponownie użyć puli wewnętrznej bazy danych już używane przez maszyny wirtualne.
   4. Określ "protocol": "All" jako część reguły ruchu wychodzącego.

- Gdy używane są tylko przychodzące reguły NAT, nie podano wychodzącego translatora nat.

   1. Umieść maszyny wirtualne w puli wewnętrznej bazy danych.
   2. Zdefiniuj jedną lub więcej konfiguracji IP frontoneka z publicznym adresem IP lub publicznym prefiksem IP.
   3. Skonfiguruj regułę ruchu wychodzącego na tym samym modułze równoważenia obciążenia.
   4. Określ "protokół": "Wszystkie" jako część reguły ruchu wychodzącego

## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu wynosi 64 000.
- Zakres konfigurowalny wychodzący limit czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Moduł równoważenia obciążenia nie obsługuje icmp dla wychodzącego translatora nat.
- Reguły ruchu wychodzącego można zastosować tylko do podstawowej konfiguracji adresów IP karty sieciowej.  Obsługiwanych jest wiele kart sieciowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [używaniu modułu równoważenia obciążenia dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [standardowym równoważącym obciążenia](load-balancer-standard-overview.md).
- Dowiedz się więcej o [dwukierunkowym resetu TCP przy pomiełtwie limitu czasu](load-balancer-tcp-reset.md).
- [Konfigurowanie reguł ruchu wychodzącego za pomocą interfejsu wiersza polecenia platformy Azure 2.0](configure-load-balancer-outbound-cli.md).
