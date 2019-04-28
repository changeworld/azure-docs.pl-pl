---
title: Reguły ruchu wychodzącego w module równoważenia obciążenia platformy Azure
titlesuffix: Azure Load Balancer
description: Użyj reguł dla ruchu wychodzącego do definiowania tłumaczenia adresu sieciowego ruchu wychodzącego
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: kumud
ms.openlocfilehash: 52fafa7e9dd46b6c78af3776797bae48b22ea8df
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736668"
---
# <a name="load-balancer-outbound-rules"></a>Reguły ruchu wychodzącego modułu równoważenia obciążenia

Usługa Azure Load Balancer zapewnia łączność wychodząca z sieci wirtualnej, oprócz ruchu przychodzącego.  Reguły ruchu wychodzącego ułatwiają konfigurowanie publicznych [Balancer w warstwie standardowa](load-balancer-standard-overview.md)firmy translatora adresów sieciowych wychodzących.  Masz deklaratywne pełną kontrolę nad łączności wychodzącej do skalowania i dostosować tę możliwość do konkretnych potrzeb.

![Reguły ruchu wychodzącego modułu równoważenia obciążenia](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Za pomocą reguł ruchu wychodzącego można użyć równoważenia obciążenia: 
- Zdefiniuj NAT dla ruchu wychodzącego od podstaw.
- Skalowanie i dostosowywanie zachowania istniejących wychodzących reguł NAT. 

Reguły ruchu wychodzącego umożliwiają kontrolowanie:
- maszyn wirtualnych powinny być przekonwertowana na której publiczne adresy IP. 
- jak [portów SNAT wychodzących](load-balancer-outbound-connections.md#snat) powinna zostać przydzielona.
- zapewnienie wychodzącego Translacja protokołów.
- jakie czas trwania dla limitu czasu bezczynności połączenia wychodzące (4-120 minut).
- Określa, czy wysłać TCP Reset przy użyciu limitu czasu bezczynności (w publicznej wersji zapoznawczej). 

Rozwiń węzeł reguł dla ruchu wychodzącego [Scenariusz 2](load-balancer-outbound-connections.md#lb) w opisanych w [połączeń wychodzących](load-balancer-outbound-connections.md) artykułu i pierwszeństwo scenariusz pozostaje jako — jest.

## <a name="outbound-rule"></a>Reguły ruchu wychodzącego

Podobnie jak wszystkie reguły modułu równoważenia obciążenia reguł dla ruchu wychodzącego postępuj zgodnie z tego samego dobrze znanej składni jako równoważenia obciążenia i reguł translatora adresów Sieciowych dla ruchu przychodzącego:

**Fronton** + **parametry** + **puli zaplecza**

Regułę dla ruchu wychodzącego konfiguruje NAT dla ruchu wychodzącego dla _wszystkie maszyny wirtualne identyfikowane przez pulę zaplecza_ mają być tłumaczone na _frontonu_.  I _parametry_ zapewniają dodatkowe bardziej szczegółową kontrolę nad algorytmu NAT ruchu wychodzącego.

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
>Skuteczne Konfiguracja translatora adresów Sieciowych wychodzących jest złożonego wszystkich reguł dla ruchu wychodzącego i reguły równoważenia obciążenia. Reguły ruchu wychodzącego są dodawane do reguły równoważenia obciążenia. Przegląd [wyłączenie NAT dla ruchu wychodzącego dla reguły równoważenia obciążenia](#disablesnat) do zarządzania skuteczne translacja NAT ruchu wychodzącego, gdy wiele reguł mają zastosowanie do maszyny Wirtualnej. Należy najpierw [wyłączyć SNAT wychodzących](#disablesnat) podczas definiowania reguły ruchu wychodzącego, która używa tego samego publicznego adresu IP jako reguły równoważenia obciążenia.

### <a name="scale"></a> Skala NAT dla ruchu wychodzącego z wieloma adresami IP

Chociaż regułę dla ruchu wychodzącego, mogą być używane z tylko jednego publicznego adresu IP, reguły ruchu wychodzącego do jej obsługi ułatwiają obciążenia konfiguracji skalowania wychodzących reguł NAT. Wiele adresów IP umożliwia zaplanowanie na dużą skalę scenariuszach i można użyć reguł dla ruchu wychodzącego, aby uniknąć [wyczerpania SNAT](load-balancer-outbound-connections.md#snatexhaust) podatne wzorców.  

Poszczególnych dodatkowych adresów IP, które są dostarczane przez frontonu zapewnia 51,200 portów tymczasowych dla modułu równoważenia obciążenia do użycia jako SNAT portów. Chociaż Równoważenie obciążenia i reguł translatora adresów Sieciowych dla ruchu przychodzącego pojedynczego serwera sieci Web, reguły ruchu wychodzącego rozwija pojęcie frontonu i zezwala na wiele frontonów dla każdej reguły.  Wiele frontonów dla każdej reguły ilość dostępnych portów SNAT jest mnożona każdy publiczny adres IP i dużych scenariusze, które mogą być obsługiwane.

Ponadto można użyć [publiczny prefiks IP](https://aka.ms/lbpublicipprefix) bezpośrednio za pomocą regułę dla ruchu wychodzącego.  Za pomocą publicznego adresu IP prefiksu umożliwia łatwiejsze skalowanie i uproszczone stosując białą listę przepływów, pochodzących z wdrożenie systemu Azure. Można skonfigurować konfigurację adresu IP frontonu w ramach zasobu modułu równoważenia obciążenia można przywoływać publiczny prefiks adresu IP bezpośrednio.  Dzięki temu moduł równoważenia obciążenia wyłączną kontrolę nad publiczny prefiks adresu IP i reguły ruchu wychodzącego automatycznie użyje wszystkich publicznych adresów IP zawartych w publiczny prefiks adresu IP dla połączeń wychodzących.  Każdy z adresów IP w zakresie publiczny prefiks IP zapewniają 51,200 portów tymczasowych dla adresu IP dla modułu równoważenia obciążenia do użycia jako SNAT portów.   

Nie może mieć poszczególne zasoby adresów publicznych adresów IP utworzone na podstawie publiczny prefiks adresu IP przy użyciu tej opcji, jak reguły ruchu wychodzącego musi mieć pełną kontrolę nad publiczny prefiks adresu IP.  Jeśli potrzebujesz bardziej szczegółową kontrolę poprawnie, można utworzyć indywidualne zasób publicznego adresu IP z publicznych adresów IP prefiksu i przypisać wiele publicznych adresów IP indywidualnie do frontonu regułę dla ruchu wychodzącego.

### <a name="snatports"></a> Dostosowywanie alokacją portów SNAT

Można użyć reguł dla ruchu wychodzącego, należy dostosować [automatyczna alokacja portu SNAT oparte na rozmiar puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) i przydzielić więcej lub mniej, niż zapewnia automatyczne przydzielanie portu SNAT.

Użyj następującego parametru przydzielić SNAT 10 000 portów dla maszyny Wirtualnej (konfiguracji adresów IP kart Sieciowych).
 

          "allocatedOutboundPorts": 10000

Każdy publiczny adres IP z frontonów wszystkie reguły ruchu wychodzącego przyczynia się do 51,200 portów tymczasowych do użycia jako SNAT porty.  Moduł równoważenia obciążenia przydziela SNAT porty wielokrotność liczby 8. Jeśli podasz wartość nie podzielna przez 8 operacja konfiguracji jest odrzucane.  Jeśli użytkownik spróbuje przydzielić więcej SNAT portów, niż jest dostępnych na podstawie liczby publicznych adresów IP, operacja konfiguracji zostanie odrzucone.  Na przykład jeśli przydzielisz 10 000 portów dla maszyny Wirtualnej i 7 maszyn wirtualnych w wewnętrznej bazie danych będzie udostępnianie jednego publicznego adresu IP puli, konfiguracja zostanie odrzucone (7 x 10 000 SNAT porty > 51,200 SNAT porty).  Możesz dodać więcej publiczne adresy IP do serwera sieci Web, reguły ruchu wychodzącego do scenariusza.

Można powrócić do [automatyczna alokacja portu SNAT oparte na rozmiar puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) , określając 0 dla liczby portów.

### <a name="idletimeout"></a> Limit czasu bezczynności przepływu wychodzącego kontroli

Reguły ruchu wychodzącego zapewniają parametru konfiguracji do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasować go do potrzeb aplikacji.  Domyślnie wychodzącego przekroczeń limitu czasu bezczynności 4 minuty.  Parametr przyjmuje wartość od 4 do 120 do określonych minutach limitu czasu bezczynności dla przepływów pasujące do tej określonej reguły.

Aby ustawić limit czasu bezczynności ruchu wychodzącego do 1 godziny, należy użyć następującego parametru:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Włącz Resetowanie TCP na limit czasu bezczynności (wersja zapoznawcza)

Domyślne zachowanie usługi równoważenia obciążenia jest dyskretnie pomijać przepływ, gdy został osiągnięty limit czasu bezczynności ruchu wychodzącego.  Za pomocą parametru enableTCPReset, można włączyć bardziej przewidywalne zachowanie aplikacji i kontrolować, czy wysłać dwukierunkowe TCP Reset (TCP RST) na limit czasu wychodzącego limitu czasu bezczynności. 

Następujący parametr umożliwia włączanie resetowania TCP na regułę dla ruchu wychodzącego:

           "enableTcpReset": true

Przegląd [TCP zresetowanie limitu czasu bezczynności (wersja zapoznawcza)](https://aka.ms/lbtcpreset) Aby uzyskać szczegółowe informacje, łącznie z dostępność w poszczególnych regionach.

### <a name="proto"></a> Obsługa protokołów transportu TCP i UDP, przy użyciu jednej reguły

Prawdopodobnie będziesz używać "All" protokołu transportowego reguły ruchu wychodzącego, ale można ją również zastosować reguły ruchu wychodzącego z protokołem określonego transportu Jeśli trzeba to zrobić.

Ustawić protokół na TCP i UDP, należy użyć następującego parametru:

          "protocol": "All"

### <a name="disablesnat"></a> Wyłącz NAT dla ruchu wychodzącego dla reguły równoważenia obciążenia

Jak wspomniano wcześniej, reguły równoważenia obciążenia zapewnia automatyczne programowanie wychodzących reguł NAT. Jednak niektóre scenariusze korzyści lub wymagają wyłączyć automatyczne programowanie NAT dla ruchu wychodzącego, reguły, które umożliwiają kontrolowanie lub dostosować zachowanie równoważenia obciążenia.  Reguły ruchu wychodzącego mają scenariusze, w których ważne jest, aby zatrzymać automatyczne programowania NAT ruchu wychodzącego.

Tego parametru można użyć na dwa sposoby:
- Opcjonalne pomijanie przy użyciu adresu IP dla ruchu przychodzącego wychodzących reguł NAT.  Reguły ruchu wychodzącego są dodawane do reguły równoważenia obciążenia i z tego zestawu parametru reguły ruchu wychodzącego jest w formancie.
  
- Dostosowywanie wychodzącego parametry translatora adresów Sieciowych adresu IP, jednocześnie używać dla ruchu przychodzącego i wychodzącego.  Automatyczne programowania translatora adresów Sieciowych wychodzących, należy wyłączyć umożliwia regułę dla ruchu wychodzącego na przejęcie kontroli.  Na przykład, aby zmienić alokacją portów użyć funkcji SNAT względem adresu również używany do ruchu przychodzącego, ten parametr musi być ustawiony na wartość true.  Jeśli spróbujesz użyć regułę dla ruchu wychodzącego do przedefiniowania parametry adresu IP również używane dla ruchu przychodzącego i nie zostały wydane wychodzących programowanie translatora adresów Sieciowych reguły równoważenia obciążenia, operacja, aby skonfigurować regułę dla ruchu wychodzącego zakończy się niepowodzeniem.

>[!IMPORTANT]
> Maszyna wirtualna nie będzie łączności wychodzącej, jeśli ten parametr jest ustawiony na wartość true, a nie ma reguły ruchu wychodzącego (lub [na poziomie wystąpienia publicznego adresu IP scenariusza](load-balancer-outbound-connections.md#ilpip) do definiowania łączności wychodzącej.  Niektóre operacje maszyny Wirtualnej lub aplikacji może zależeć od mające łączność wychodząca jest dostępna. Upewnij się, poznanie zależności w scenariusza i zostały uznane za wpływ wprowadzenie tej zmiany.

Aby wyłączyć SNAT wychodzących na regułę za pomocą tego parametru konfiguracji równoważenia obciążenia:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT wartość domyślna to false, co oznacza, że reguły równoważenia obciążenia **jest** zapewnia automatyczne NAT dla ruchu wychodzącego jako obraz lustrzany konfiguracji reguły równoważenia obciążenia.  

Jeśli disableOutboundSnat jest ustawiony na wartość true na reguły równoważenia obciążenia, reguły równoważenia obciążenia zwalnia kontrolki, w przeciwnym razie automatyczne programowania translatora adresów Sieciowych wychodzących.  SNAT wychodzących wyniku reguły równoważenia obciążenia jest wyłączona.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Ponowne użycie istniejących lub zdefiniuj nowe pule zaplecza

Reguły ruchu wychodzącego nie wprowadzają nowe pojęcie do definiowania grupy maszyn wirtualnych, do których stosuje się reguły.  Zamiast tego są ponownie użyć koncepcji puli zaplecza, która jest również używana dla reguł równoważenia obciążenia. Możesz użyć tego można uproszczenie konfiguracji przez ponowne użycie istniejącej definicji puli wewnętrznej bazy danych lub tworzeniu specjalnie dla regułę dla ruchu wychodzącego.

## <a name="scenarios"></a>Scenariusze

### <a name="groom"></a> Oczyść połączeń wychodzących do określonego zestawu publicznych adresów IP

Można użyć regułę dla ruchu wychodzącego do pielęgnacji połączeń wychodzących, aby były widoczne dla pochodzi z określonego zestawu publicznych adresów IP do jej obsługi ułatwiają realizację scenariuszy umieszczania na białej liście.  Ten publiczny adres IP źródła może być taka sama jak wartość używana przez reguły równoważenia obciążenia lub publiczny adres IP na inny zestaw adresów niż używane przez reguły równoważenia obciążenia.  

1. Tworzenie [publiczny prefiks IP](https://aka.ms/lbpublicipprefix) (lub publiczne adresy IP z publicznych adresów IP prefiksu)
2. Tworzenie publicznej usługi Load Balancer w warstwie Standardowa
3. Utwórz frontonów odwołuje się do publicznego prefiks adresu IP (lub publiczne adresy IP), których chcesz używać
4. Ponowne użycie puli wewnętrznej bazy danych lub Utwórz pulę zaplecza i umieszcza maszyny wirtualne w puli zaplecza publicznej usługi Load Balancer
5. Skonfiguruj regułę dla ruchu wychodzącego na publiczny moduł równoważenia obciążenia do programu NAT dla ruchu wychodzącego dla tych maszyn wirtualnych przy użyciu frontonów
   
Jeśli użytkownik nie chce dla reguły równoważenia obciążenia ma być używany dla ruchu wychodzącego, musisz [wyłączyć SNAT wychodzących](#disablesnat) na reguły równoważenia obciążenia.

### <a name="modifysnat"></a> Modyfikuj alokację portu SNAT

Można użyć reguł dla ruchu wychodzącego do dostrojenia [automatyczna alokacja portu SNAT oparte na rozmiar puli zaplecza](load-balancer-outbound-connections.md#preallocatedports).

Na przykład jeśli masz dwie maszyny wirtualne do udostępniania jednego publicznego adresu IP dla NAT dla ruchu wychodzącego, możesz zwiększyć liczbę portów SNAT przydzielony z domyślnych portów 1024, jeśli występują SNAT wyczerpania. Każdy publiczny adres IP może przyczynić się do 51,200 portów tymczasowych.  Jeśli skonfigurujesz regułę dla ruchu wychodzącego z jednym frontonu publiczny adres IP można rozpowszechniać w sumie 51,200 porty SNAT do maszyn wirtualnych w puli zaplecza.  Dla dwóch maszyn wirtualnych można przydzielić maksymalnie 25,600 porty SNAT za pomocą regułę dla ruchu wychodzącego (2 x 25,600 = 51,200).

Przegląd [połączeń wychodzących](load-balancer-outbound-connections.md) i szczegółowe informacje na temat [SNAT](load-balancer-outbound-connections.md#snat) porty są przydzielone i używane.

### <a name="outboundonly"></a> Włącz tylko ruchu wychodzącego

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
   4. Określ "Protokół": "All" w ramach reguły ruchu wychodzącego.

- Gdy używane są tylko reguły dla ruchu przychodzącego translatora adresów Sieciowych, znajduje się nie NAT dla ruchu wychodzącego.

   1. Umieść maszyny wirtualne w puli zaplecza.
   2. Należy zdefiniować co najmniej jeden konfiguracji adresów IP frontonu z publicznych adresów IP lub publiczny prefiks adresu IP.
   3. Skonfiguruj regułę dla ruchu wychodzącego w tym samym module równoważenia obciążenia.
   4. Określ "Protokół": "All" w ramach reguły ruchu wychodzącego

## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba można używać portów tymczasowych dla adresu IP frontonu jest 51,200.
- Zakres konfigurowalnych limitu czasu bezczynności ruchu wychodzącego jest 4 do 120 minut (240 do 7200 sekund).
- Moduł równoważenia obciążenia nie obsługuje protokołu ICMP dla wychodzących reguł NAT.
- Portal nie można skonfigurować lub wyświetlać reguły dla ruchu wychodzącego.  Zamiast tego użyj szablonów, interfejs API REST, Az interfejsu wiersza polecenia w wersji 2.0 lub programu PowerShell.
- Reguły ruchu wychodzącego jest stosowane tylko do podstawowej karty Sieciowej i podstawowa konfiguracja adresu IP.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o korzystaniu z [modułu równoważenia obciążenia dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md).
- Dowiedz się więcej o [dwukierunkowe TCP Reset na limit czasu bezczynności](load-balancer-tcp-reset.md).
- [Konfigurowanie reguł ruchu wychodzącego za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](configure-load-balancer-outbound-cli.md).
