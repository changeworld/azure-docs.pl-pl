---
title: Reguły ruchu wychodzącego — Azure Load Balancer
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij korzystanie z reguł ruchu wychodzącego w celu definiowania translacji adresów sieciowych dla ruchu wychodzącego.
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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304195"
---
# <a name="load-balancer-outbound-rules"></a>Reguły ruchu wychodzącego modułu równoważenia obciążenia

Usługa Azure Load Balancer zapewnia łączność wychodząca z sieci wirtualnej, oprócz ruchu przychodzącego.  Reguły ruchu wychodzącego ułatwiają konfigurowanie wychodzącej translacji adresów sieciowych [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)publicznej.  Masz deklaratywne pełną kontrolę nad łączności wychodzącej do skalowania i dostosować tę możliwość do konkretnych potrzeb.

![Reguły ruchu wychodzącego modułu równoważenia obciążenia](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Za pomocą reguł ruchu wychodzącego można użyć równoważenia obciążenia: 
- Zdefiniuj NAT dla ruchu wychodzącego od podstaw.
- Skalowanie i dostosowywanie zachowania istniejących wychodzących reguł NAT. 

Reguły ruchu wychodzącego umożliwiają kontrolowanie:
- maszyn wirtualnych powinny być przekonwertowana na której publiczne adresy IP. 
- sposób przydzielenia [portów wychodzącego ruchu źródłowego](load-balancer-outbound-connections.md#snat) .
- zapewnienie wychodzącego Translacja protokołów.
- czas trwania okresu bezczynności połączenia wychodzącego (4-120 minut).
- Czy należy wysyłać Resetowanie protokołu TCP po upływie limitu czasu bezczynności

Reguły ruchu wychodzącego rozszerzają [Scenariusz 2](load-balancer-outbound-connections.md#lb) opisany w artykule [połączenia wychodzące](load-balancer-outbound-connections.md) , a priorytet scenariusza pozostaje taki, jak jest.

## <a name="outbound-rule"></a>Reguły ruchu wychodzącego

Podobnie jak wszystkie reguły modułu równoważenia obciążenia reguł dla ruchu wychodzącego postępuj zgodnie z tego samego dobrze znanej składni jako równoważenia obciążenia i reguł translatora adresów Sieciowych dla ruchu przychodzącego:

**parametry** + **frontonu** + **puli zaplecza**

Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla _wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza_ , które mają zostać przetłumaczone na _fronton_.  I _Parametry_ zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

Wersja "2018-07-01" interfejsu API pozwala na definicję reguły ruchu wychodzącego ze strukturą w następujący sposób:

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
>Skuteczne Konfiguracja translatora adresów Sieciowych wychodzących jest złożonego wszystkich reguł dla ruchu wychodzącego i reguły równoważenia obciążenia. Reguły ruchu wychodzącego są dodawane do reguły równoważenia obciążenia. Zapoznaj się z tematem [wyłączanie wychodzącego NAT dla reguły równoważenia obciążenia](#disablesnat) , aby zarządzać efektywnym wychodzącym TRANSLACJą NAT w przypadku zastosowania wielu reguł do maszyny wirtualnej. Podczas definiowania reguły ruchu wychodzącego, która używa tego samego publicznego adresu IP jako reguły równoważenia obciążenia, należy [wyłączyć wychodzące](#disablesnat) obiektu zasad sieciowych.

### <a name="scale"></a>Skalowanie ruchu wychodzącego NAT z wieloma adresami IP

Chociaż regułę dla ruchu wychodzącego, mogą być używane z tylko jednego publicznego adresu IP, reguły ruchu wychodzącego do jej obsługi ułatwiają obciążenia konfiguracji skalowania wychodzących reguł NAT. Można użyć wielu adresów IP do zaplanowania scenariuszy o dużej skali i można użyć reguł ruchu wychodzącego, aby wyeliminować wzorce podatności na ruch [wydechowy](load-balancer-outbound-connections.md#snatexhaust) .  

Każdy dodatkowy adres IP dostarczony przez fronton zapewnia 64 000 portów tymczasowych Load Balancer do użycia jako portów protokołu integracyjnego. Chociaż Równoważenie obciążenia i reguł translatora adresów Sieciowych dla ruchu przychodzącego pojedynczego serwera sieci Web, reguły ruchu wychodzącego rozwija pojęcie frontonu i zezwala na wiele frontonów dla każdej reguły.  Wiele frontonów dla każdej reguły ilość dostępnych portów SNAT jest mnożona każdy publiczny adres IP i dużych scenariusze, które mogą być obsługiwane.

Ponadto można użyć [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą.  Za pomocą publicznego adresu IP prefiksu umożliwia łatwiejsze skalowanie i uproszczone stosując białą listę przepływów, pochodzących z wdrożenie systemu Azure. Można skonfigurować konfigurację adresu IP frontonu w ramach zasobu modułu równoważenia obciążenia można przywoływać publiczny prefiks adresu IP bezpośrednio.  Dzięki temu moduł równoważenia obciążenia wyłączną kontrolę nad publiczny prefiks adresu IP i reguły ruchu wychodzącego automatycznie użyje wszystkich publicznych adresów IP zawartych w publiczny prefiks adresu IP dla połączeń wychodzących.  Każdy adres IP należący do zakresu publicznego prefiksu adresu IP zapewnia 64 000 portów tymczasowych na adres IP, aby Load Balancer używać jako portów podzestawów adresów.   

Nie może mieć poszczególne zasoby adresów publicznych adresów IP utworzone na podstawie publiczny prefiks adresu IP przy użyciu tej opcji, jak reguły ruchu wychodzącego musi mieć pełną kontrolę nad publiczny prefiks adresu IP.  Jeśli potrzebujesz bardziej szczegółową kontrolę poprawnie, można utworzyć indywidualne zasób publicznego adresu IP z publicznych adresów IP prefiksu i przypisać wiele publicznych adresów IP indywidualnie do frontonu regułę dla ruchu wychodzącego.

### <a name="snatports"></a>Dostrajanie alokacji portów dla współdzielonego

Możesz użyć reguł ruchu wychodzącego, aby dostroić [automatyczne przydzielanie portów adresów sieciowych w oparciu o rozmiar puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) i przydzielić więcej lub mniej niż jest to automatyczna alokacja portu.

Użyj następującego parametru przydzielić SNAT 10 000 portów dla maszyny Wirtualnej (konfiguracji adresów IP kart Sieciowych).
 

          "allocatedOutboundPorts": 10000

Każdy publiczny adres IP ze wszystkich frontonów reguły wychodzącej współużytkuje do 64 000 tymczasowych portów do użycia jako porty protokołu reportowego.  Moduł równoważenia obciążenia przydziela SNAT porty wielokrotność liczby 8. Jeśli podasz wartość nie podzielna przez 8 operacja konfiguracji jest odrzucane.  Jeśli użytkownik spróbuje przydzielić więcej SNAT portów, niż jest dostępnych na podstawie liczby publicznych adresów IP, operacja konfiguracji zostanie odrzucone.  Na przykład w przypadku przydzielenia 10 000 portów na maszynę wirtualną i 7 maszyn wirtualnych w puli zaplecza współużytkują jeden publiczny 64 000 > 10 000 adres IP  Możesz dodać więcej publiczne adresy IP do serwera sieci Web, reguły ruchu wychodzącego do scenariusza.

Można przywrócić [automatyczną alokację portu na podstawie rozmiaru puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) , określając 0 dla liczby portów. W takim przypadku pierwsze wystąpienia maszyn wirtualnych 50 będą mieć 1024 portów, a 51-100 wystąpienia maszyn wirtualnych otrzymają 512 i tak dalej, zgodnie z tabelą.

### <a name="idletimeout"></a>Kontrola limitu czasu bezczynności przepływu wychodzącego

Reguły ruchu wychodzącego zapewniają parametru konfiguracji do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasować go do potrzeb aplikacji.  Domyślnie wychodzącego przekroczeń limitu czasu bezczynności 4 minuty.  Parametr przyjmuje wartość od 4 do 120 do określonej liczby minut dla limitu czasu bezczynności dla przepływów pasujących do tej konkretnej reguły.

Aby ustawić limit czasu bezczynności ruchu wychodzącego do 1 godziny, należy użyć następującego parametru:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> Włącz Resetowanie limitu czasu bezczynności dla protokołu TCP

Domyślne zachowanie usługi równoważenia obciążenia jest dyskretnie pomijać przepływ, gdy został osiągnięty limit czasu bezczynności ruchu wychodzącego.  Za pomocą parametru enableTCPReset, można włączyć bardziej przewidywalne zachowanie aplikacji i kontrolować, czy wysłać dwukierunkowe TCP Reset (TCP RST) na limit czasu wychodzącego limitu czasu bezczynności. 

Następujący parametr umożliwia włączanie resetowania TCP na regułę dla ruchu wychodzącego:

           "enableTcpReset": true

Przejrzyj informacje [o limicie czasu bezczynności na potrzeby resetowania protokołu TCP](https://aka.ms/lbtcpreset) , co obejmuje dostępność regionów.

### <a name="proto"></a>Obsługa protokołów transportu TCP i UDP przy użyciu jednej reguły

Prawdopodobnie będziesz używać "All" protokołu transportowego reguły ruchu wychodzącego, ale można ją również zastosować reguły ruchu wychodzącego z protokołem określonego transportu Jeśli trzeba to zrobić.

Ustawić protokół na TCP i UDP, należy użyć następującego parametru:

          "protocol": "All"

### <a name="disablesnat"></a>Wyłącz wychodzące NAT dla reguły równoważenia obciążenia

Jak wspomniano wcześniej, reguły równoważenia obciążenia zapewnia automatyczne programowanie wychodzących reguł NAT. Jednak niektóre scenariusze korzyści lub wymagają wyłączyć automatyczne programowanie NAT dla ruchu wychodzącego, reguły, które umożliwiają kontrolowanie lub dostosować zachowanie równoważenia obciążenia.  Reguły ruchu wychodzącego mają scenariusze, w których ważne jest, aby zatrzymać automatyczne programowania NAT ruchu wychodzącego.

Tego parametru można użyć na dwa sposoby:
- Opcjonalne pomijanie przy użyciu adresu IP dla ruchu przychodzącego wychodzących reguł NAT.  Reguły ruchu wychodzącego są dodawane do reguły równoważenia obciążenia i z tego zestawu parametru reguły ruchu wychodzącego jest w formancie.
  
- Dostosowywanie wychodzącego parametry translatora adresów Sieciowych adresu IP, jednocześnie używać dla ruchu przychodzącego i wychodzącego.  Automatyczne programowania translatora adresów Sieciowych wychodzących, należy wyłączyć umożliwia regułę dla ruchu wychodzącego na przejęcie kontroli.  Na przykład, aby zmienić alokacją portów użyć funkcji SNAT względem adresu również używany do ruchu przychodzącego, ten parametr musi być ustawiony na wartość true.  Jeśli spróbujesz użyć regułę dla ruchu wychodzącego do przedefiniowania parametry adresu IP również używane dla ruchu przychodzącego i nie zostały wydane wychodzących programowanie translatora adresów Sieciowych reguły równoważenia obciążenia, operacja, aby skonfigurować regułę dla ruchu wychodzącego zakończy się niepowodzeniem.

>[!IMPORTANT]
> Jeśli ustawisz ten parametr na wartość true, maszyna wirtualna nie będzie mogła mieć łączności wychodzącej (lub [scenariusza publicznego adresu IP na poziomie wystąpienia](load-balancer-outbound-connections.md#ilpip) ), aby zdefiniować łączność wychodzącą.  Niektóre operacje maszyny Wirtualnej lub aplikacji może zależeć od mające łączność wychodząca jest dostępna. Upewnij się, poznanie zależności w scenariusza i zostały uznane za wpływ wprowadzenie tej zmiany.

Aby wyłączyć SNAT wychodzących na regułę za pomocą tego parametru konfiguracji równoważenia obciążenia:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT ma wartość domyślną false, co oznacza, że reguła równoważenia **obciążenia zapewnia automatyczne** wychodzące NAT jako obraz lustrzany konfiguracji reguły równoważenia obciążenia.  

Jeśli disableOutboundSnat jest ustawiony na wartość true na reguły równoważenia obciążenia, reguły równoważenia obciążenia zwalnia kontrolki, w przeciwnym razie automatyczne programowania translatora adresów Sieciowych wychodzących.  SNAT wychodzących wyniku reguły równoważenia obciążenia jest wyłączona.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Ponowne użycie istniejących lub zdefiniuj nowe pule zaplecza

Reguły ruchu wychodzącego nie wprowadzają nowe pojęcie do definiowania grupy maszyn wirtualnych, do których stosuje się reguły.  Zamiast tego są ponownie użyć koncepcji puli zaplecza, która jest również używana dla reguł równoważenia obciążenia. Możesz użyć tego można uproszczenie konfiguracji przez ponowne użycie istniejącej definicji puli wewnętrznej bazy danych lub tworzeniu specjalnie dla regułę dla ruchu wychodzącego.

## <a name="scenarios"></a>Scenariusze

### <a name="groom"></a>Oczyść połączenia wychodzące do określonego zestawu publicznych adresów IP

Można użyć regułę dla ruchu wychodzącego do pielęgnacji połączeń wychodzących, aby były widoczne dla pochodzi z określonego zestawu publicznych adresów IP do jej obsługi ułatwiają realizację scenariuszy umieszczania na białej liście.  Ten publiczny adres IP źródła może być taka sama jak wartość używana przez reguły równoważenia obciążenia lub publiczny adres IP na inny zestaw adresów niż używane przez reguły równoważenia obciążenia.  

1. Tworzenie [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) (lub publicznych adresów IP z prefiksu publicznego adresu IP)
2. Tworzenie publicznej usługi Load Balancer w warstwie Standardowa
3. Utwórz frontonów odwołuje się do publicznego prefiks adresu IP (lub publiczne adresy IP), których chcesz używać
4. Ponowne użycie puli wewnętrznej bazy danych lub Utwórz pulę zaplecza i umieszcza maszyny wirtualne w puli zaplecza publicznej usługi Load Balancer
5. Skonfiguruj regułę dla ruchu wychodzącego na publiczny moduł równoważenia obciążenia do programu NAT dla ruchu wychodzącego dla tych maszyn wirtualnych przy użyciu frontonów
   
Jeśli nie chcesz, aby reguła równoważenia obciążenia była używana dla ruchu wychodzącego, należy [wyłączyć wychodzące elementu reźródłowego](#disablesnat) dla reguły równoważenia obciążenia.

### <a name="modifysnat"></a>Modyfikuj przydział portu dla współdzielonego

Korzystając z reguł ruchu wychodzącego, można dostroić [automatyczną alokację portu z użyciem puli zaplecza](load-balancer-outbound-connections.md#preallocatedports).

Na przykład jeśli masz dwie maszyny wirtualne do udostępniania jednego publicznego adresu IP dla NAT dla ruchu wychodzącego, możesz zwiększyć liczbę portów SNAT przydzielony z domyślnych portów 1024, jeśli występują SNAT wyczerpania. Każdy publiczny adres IP może współtworzyć maksymalnie 64 000 portów tymczasowych.  W przypadku skonfigurowania reguły wychodzącej z jednym frontonem publicznego adresu IP można rozpowszechnić łącznie 64 000 portów do maszyn wirtualnych w puli zaplecza.  W przypadku dwóch maszyn wirtualnych maksymalnie 32 000 portów przydziałów adresów sieciowych można przydzielyć za pomocą reguły ruchu wychodzącego (2x 32 000 = 64 000).

Przejrzyj [połączenia wychodzące](load-balancer-outbound-connections.md) i szczegółowe informacje dotyczące sposobu przydzielania i używania portów podłączania [adresów sieciowych](load-balancer-outbound-connections.md#snat) .

### <a name="outboundonly"></a>Włącz tylko wychodzące

Publiczny moduł równoważenia obciążenia standardowego służy do zapewnienia NAT dla ruchu wychodzącego dla grupy maszyn wirtualnych. W tym scenariuszu można użyć regułę dla ruchu wychodzącego przez siebie, bez konieczności stosowania żadnych dodatkowych reguł.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT dla ruchu wychodzącego dla maszyn wirtualnych tylko (nie ruch przychodzący)

Zdefiniuj publiczny moduł równoważenia obciążenia standardowego, umieść maszyny wirtualne w puli zaplecza i skonfigurować regułę dla ruchu wychodzącego do programu NAT dla ruchu wychodzącego i pielęgnowania połączenia wychodzące tak, jakby pochodził z określonym publicznym adresem IP. Możesz również użyć publiczny prefiks IP uprościć stosując białą listę źródło połączenia wychodzące.

1. Tworzenie publicznego modułu równoważenia obciążenia standardowego.
2. Tworzenie puli wewnętrznej bazy danych i umieść maszyny wirtualne w puli zaplecza publicznej usługi Load Balancer.
3. Skonfiguruj regułę dla ruchu wychodzącego na publiczny moduł równoważenia obciążenia do programu NAT dla ruchu wychodzącego dla tych maszyn wirtualnych.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT dla ruchu wychodzącego w scenariuszach standardowego modułu równoważenia obciążenia wewnętrznego

Korzystając z wewnętrznego standardowego modułu równoważenia obciążenia, NAT dla ruchu wychodzącego nie jest dostępna, aż jawnie zadeklarowane łączności wychodzącej. Można zdefiniować łączności wychodzącej za pomocą regułę dla ruchu wychodzącego do utworzenia połączenia ruchu wychodzącego dla maszyn wirtualnych za modułem wewnętrznego standardowego modułu równoważenia obciążenia za pomocą tych kroków:

1. Tworzenie publicznego modułu równoważenia obciążenia standardowego.
2. Tworzenie puli wewnętrznej bazy danych i umieść maszyny wirtualne w puli zaplecza modułu równoważenia obciążenia publiczny oprócz wewnętrznego modułu równoważenia obciążenia.
3. Skonfiguruj regułę dla ruchu wychodzącego na publiczny moduł równoważenia obciążenia do programu NAT dla ruchu wychodzącego dla tych maszyn wirtualnych.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Włącz protokoły zarówno TCP i UDP dla NAT dla ruchu wychodzącego z publicznych standardowego modułu równoważenia obciążenia

- Korzystając z publicznych standardowego modułu równoważenia obciążenia, automatycznego programowania translatora adresów Sieciowych wychodzących podane pasuje do protokołu transportu reguły równoważenia obciążenia.  

   1. Wyłącz SNAT wychodzących na reguły równoważenia obciążenia.
   2. Skonfiguruj regułę dla ruchu wychodzącego w tym samym module równoważenia obciążenia.
   3. Ponowne użycie puli zaplecza, już używane przez maszyny wirtualne.
   4. Należy określić "Protokół": "All" w ramach reguły ruchu wychodzącego.

- Gdy używane są tylko reguły dla ruchu przychodzącego translatora adresów Sieciowych, znajduje się nie NAT dla ruchu wychodzącego.

   1. Umieść maszyny wirtualne w puli zaplecza.
   2. Należy zdefiniować co najmniej jeden konfiguracji adresów IP frontonu z publicznych adresów IP lub publiczny prefiks adresu IP.
   3. Skonfiguruj regułę dla ruchu wychodzącego w tym samym module równoważenia obciążenia.
   4. Należy określić "Protokół": "All" w ramach reguły ruchu wychodzącego

## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu to 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Moduł równoważenia obciążenia nie obsługuje protokołu ICMP dla wychodzących reguł NAT.
- Reguły ruchu wychodzącego mogą być stosowane tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Obsługiwane są wiele kart sieciowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat korzystania z [Load Balancer dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej na temat [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Informacje o [dwukierunkowym resetowaniu protokołu TCP przy bezczynności](load-balancer-tcp-reset.md).
- [Skonfiguruj reguły ruchu wychodzącego za pomocą interfejsu wiersza polecenia platformy Azure 2,0](configure-load-balancer-outbound-cli.md).
