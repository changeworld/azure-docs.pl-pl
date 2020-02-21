---
title: Projektowanie sieci wirtualnych z użyciem zasobów bramy translatora adresów sieciowych
titleSuffix: Azure Virtual Network NAT
description: Dowiedz się, jak projektować sieci wirtualne przy użyciu zasobów bramy translatora adresów sieciowych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/19/2020
ms.author: allensu
ms.openlocfilehash: a118d560541595e26c80547dd641968c518aa353
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485016"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Projektowanie sieci wirtualnych z użyciem zasobów bramy translatora adresów sieciowych (publiczna wersja zapoznawcza)

Zasoby bramy translatora adresów sieciowych są częścią [Virtual Network NAT](nat-overview.md) i zapewniają wychodzącą łączność z Internetem dla jednej lub kilku podsieci sieci wirtualnej. Podsieć stanów sieci wirtualnej, która będzie używana przez bramę translatora adresów sieciowych. Translator adresów sieciowych udostępnia translację adresów sieci (Resources) dla podsieci.  Zasoby bramy translatora adresów sieciowych określają, które statyczne adresy IP będą używane przez maszyny wirtualne podczas tworzenia przepływów wychodzących. Statyczne adresy IP pochodzą z zasobów publicznych adresów IP, zasobów prefiksu publicznego adresu IP lub obu. Zasób bramy NAT może korzystać z maksymalnie 16 statycznych adresów IP z obu.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu">
</p>

*Rysunek: Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu*


>[!NOTE] 
>Virtual Network translator adresów sieciowych jest w tej chwili dostępny jako publiczna wersja zapoznawcza. Jest ono obecnie dostępne tylko w ograniczonym zestawie [regionów](nat-overview.md#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

> [!IMPORTANT]
> Po [włączeniu](./nat-overview.md#enable-preview) Virtual Network translatora adresów sieciowych w ramach subskrypcji Użyj https://aka.ms/natportal, aby uzyskać dostęp do portalu.

## <a name="how-to-deploy-nat"></a>Jak wdrożyć translator adresów sieciowych

Konfigurowanie i używanie bramy translatora adresów sieciowych jest celowo proste:  

Zasób bramy translatora adresów sieciowych:
- Utwórz regionalną lub strefowo (izolowaną od strefy) zasób bramy translatora adresów sieciowych,
- Przypisywanie adresów IP,
- Modyfikuj limit czasu bezczynności (opcjonalnie).

Sieć wirtualna:
- Skonfiguruj podsieć sieci wirtualnej tak, aby korzystała z bramy translatora adresów sieciowych.

Trasy zdefiniowane przez użytkownika nie są wymagane.

## <a name="resource"></a>Zasób

Zasób został zaprojektowany tak, aby można było go zobaczyć w następującym Azure Resource Manager przykładzie w formacie przypominającym szablon.  Ten format podobny do szablonu jest przedstawiony tutaj, aby zilustrować koncepcje i strukturę.  Zmodyfikuj przykład swoich potrzeb.  Ten dokument nie jest przeznaczony dla samouczka.

Na poniższym diagramie przedstawiono zapisywalne odwołania między różnymi zasobami Azure Resource Manager.  Strzałka wskazuje kierunek odniesienia pochodzący z lokalizacji, w której jest zapisywalny. Przegląd 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Model obiektów Virtual Network NAT">
</p>

*Rysunek: Virtual Network model obiektów NAT*

Translacja adresów sieciowych jest zalecana w przypadku większości obciążeń, chyba że jest to zależne od [puli Load Balancer łączności wychodzącej](../load-balancer/load-balancer-outbound-connections.md).  

Można migrować ze standardowych scenariuszy równoważenia obciążenia, w tym [reguł wychodzących](../load-balancer/load-balancer-outbound-rules-overview.md), do bramy translatora adresów sieciowych. Aby przeprowadzić migrację, Przenieś publiczny adres IP i zasoby prefiksu publicznego adresu IP z frontonów modułu równoważenia obciążenia do bramy translatora adresów sieciowych. Nowe adresy IP dla bramy translatora adresów sieciowych nie są wymagane. Można ponownie użyć standardowego publicznego adresu IP i prefiksu, o ile łączna wartość nie przekracza 16 adresów IP. Zaplanuj migrację z przerwaniem działania usługi na uwadze podczas przejścia.  Możesz zminimalizować przerwy, automatyzując proces. Najpierw Przetestuj migrację w środowisku przejściowym.  W trakcie przejścia nie ma to wpływu na przepływy pochodzące przychodzące.

Poniższy przykład utworzy zasób bramy NAT o nazwie _myNATGateway_ jest tworzony w regionie _Wschodnie stany USA 2, AZ 1_ i _4-minutowe_ przekroczenie limitu czasu bezczynności. Podane adresy IP wychodzącego:
- Zbiór zasobów publicznych adresów IP _myIP1_ i _myIP2_ oraz 
- Zbiór publicznych adresów IP _myPrefix1_ i _myPrefix2_. 

Łączna liczba adresów IP dostarczonych przez wszystkie cztery zasoby adresów IP nie może przekroczyć 16 adresów IP. Dozwolona jest dowolna liczba adresów IP z zakresu od 1 do 16.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

Po utworzeniu zasobu bramy NAT można go użyć w jednej lub kilku podsieciach sieci wirtualnej. Określ, które podsieci używają tego zasobu bramy NAT. Brama translatora adresów sieciowych nie może obejmować więcej niż jednej sieci wirtualnej. Nie jest wymagane przypisanie tej samej bramy NAT do wszystkich podsieci sieci wirtualnej. Poszczególne podsieci można skonfigurować przy użyciu różnych zasobów bramy translatora adresów sieciowych.

Scenariusze, które nie korzystają ze stref dostępności, będą regionalne (nie określono strefy). Jeśli używasz stref dostępności, możesz określić strefę, aby odizolować translator adresów sieciowych do określonej strefy. Nadmiarowość stref nie jest obsługiwana. Przejrzyj [strefy dostępności](#availability-zones)translatora adresów sieciowych.


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
Bramy translatora adresów sieciowych są definiowane za pomocą właściwości w podsieci w sieci wirtualnej. Przepływy utworzone przez maszyny wirtualne w podsieci _mySubnet1_ sieci wirtualnej _myVNet_ będą korzystać z bramy translatora adresów sieciowych. Wszystkie połączenia wychodzące będą używać adresów IP skojarzonych z _myNatGateway_ jako źródłowego adresu IP.


## <a name="design-guidance"></a>Wskazówki dotyczące projektowania

Przejrzyj tę sekcję, aby zapoznać się z zagadnieniami dotyczącymi projektowania sieci wirtualnych przy użyciu translatora adresów sieciowych.  

1. [Optymalizacja kosztów](#cost-optimization)
1. [Współistnienie ruchu przychodzącego i wychodzącego](#coexistence-of-inbound-and-outbound)
2. [Zarządzanie zasobami podstawowymi](#managing-basic-resources)
3. [Strefy dostępności](#availability-zones)

### <a name="cost-optimization"></a>Optymalizacja kosztów

[Punkty końcowe usługi](virtual-network-service-endpoints-overview.md) i [linki prywatne](../private-link/private-link-overview.md) to dwie opcje, które należy wziąć pod uwagę w przypadku optymalizacji kosztów, gdy translator adresów sieciowych nie jest wymagany.  Każdy ruch kierowany do punktów końcowych usługi lub link prywatny nie jest przetwarzany przez translatora adresów sieciowych sieci wirtualnej.  

Punkty końcowe usługi wiążą zasoby usługi platformy Azure z siecią wirtualną i kontrolują dostęp do zasobów usługi platformy Azure. Na przykład podczas uzyskiwania dostępu do usługi Azure Storage należy użyć punktu końcowego dla magazynu, aby uniknąć naliczania opłat za dane dotyczące translatora adresów sieciowych. Punkty końcowe usługi są bezpłatne.

Link prywatny udostępnia usługę Azure PaaS Service (lub inne usługi hostowane za pomocą linku prywatnego) jako prywatny punkt końcowy wewnątrz sieci wirtualnej.  Połączenie prywatne jest rozliczane na podstawie czasu trwania i przetworzonych danych.

Oceń, czy jedno lub oba te podejścia są dobrym dopasowaniem do scenariusza i użycia w razie potrzeby.

### <a name="coexistence-of-inbound-and-outbound"></a>Współistnienie ruchu przychodzącego i wychodzącego

Brama translatora adresów sieciowych jest zgodna z:

 - Moduł równoważenia obciążenia w warstwie Standardowa
 - Standardowy publiczny adres IP
 - Standardowy prefiks publicznego adresu IP

Podczas tworzenia nowego wdrożenia Zacznij od standardowych jednostek SKU.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu">
</p>

*Rysunek: Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu*

Internetowy scenariusz tylko dla ruchu wychodzącego udostępniony przez bramę translatora adresów sieciowych można rozszerzyć przy użyciu ruchu przychodzącego z funkcji internetowych. Każdy zasób jest świadomy kierunku, w którym pochodzi przepływ. W podsieci z bramą NAT wszystkie scenariusze połączeń wychodzących do Internetu są zastępowane przez bramę translatora adresów sieciowych. Przychodzące ze scenariuszy internetowych są udostępniane przez odpowiednie zasoby.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>Translator adresów sieciowych i maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtual Network NAT i VM z publicznym adresem IP na poziomie wystąpienia">
</p>

*Ilustracja: Virtual Network NAT i VM z publicznym adresem IP na poziomie wystąpienia*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia |
| Wychodzący | Brama translatora adresów sieciowych |

Maszyna wirtualna będzie używać bramy translatora adresów sieciowych dla ruchu wychodzącego.  Nie ma to żadnego oddziaływania na przychodzące.

#### <a name="nat-and-vm-with-public-load-balancer"></a>Translator adresów sieciowych i maszyna wirtualna z Load Balancer publiczny

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtual Network translatora adresów sieciowych i maszyn wirtualnych z Load Balancerami publicznymi">
</p>

*Rysunek: Virtual Network translatora adresów sieciowych i maszyn wirtualnych z Load Balancerami publicznymi*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Load Balancer publiczny |
| Wychodzący | Brama translatora adresów sieciowych |

Każda konfiguracja wychodząca z reguły równoważenia obciążenia lub reguł wychodzących jest zastępowana przez bramę translatora adresów sieciowych.  Nie ma to żadnego oddziaływania na przychodzące.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT i VM z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtual Network translatora adresów sieciowych i maszyn wirtualnych z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer">
</p>

*Ilustracja: Virtual Network NAT i VM z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia i publicznym Load Balancer |
| Wychodzący | Brama translatora adresów sieciowych |

Każda konfiguracja wychodząca z reguły równoważenia obciążenia lub reguł wychodzących jest zastępowana przez bramę translatora adresów sieciowych.  Maszyna wirtualna będzie również używać bramy translatora adresów sieciowych dla ruchu wychodzącego.  Nie ma to żadnego oddziaływania na przychodzące.

### <a name="managing-basic-resources"></a>Zarządzanie zasobami podstawowymi

Standardowy moduł równoważenia obciążenia, publiczny adres IP i publiczny prefiks adresu IP są zgodne z bramą translatora adresów sieciowych. Bramy translatora adresów sieciowych działają w zakresie podsieci. Podstawowa jednostka SKU tych usług należy wdrożyć w podsieci bez bramy translatora adresów sieciowych. Ta separacja umożliwia współistnienie wariantów jednostki SKU w tej samej sieci wirtualnej.

Bramy translatora adresów sieciowych mają wyższy priorytet niż scenariusze wychodzące podsieci. Nie można dostosować modułu równoważenia obciążenia podstawowego lub publicznego adresu IP (i wszystkich utworzonych przez nie usług zarządzanych) przy użyciu prawidłowych tłumaczeń. Brama translatora adresów sieciowych przejmuje kontrolę nad ruchem internetowym w podsieci. Ruch przychodzący do podstawowego modułu równoważenia obciążenia i publiczny adres IP jest niedostępny. Ruch przychodzący do podstawowego modułu równoważenia obciążenia i lub publiczny adres IP skonfigurowany na maszynie wirtualnej nie będzie dostępny.

### <a name="availability-zones"></a>Strefy dostępności

Nawet bez stref dostępności translator adresów sieciowych jest odporny na awarie i może przetrwać wiele awarii składników infrastruktury. Gdy strefy dostępności są częścią tego scenariusza, należy skonfigurować translator adresów sieciowych dla określonej strefy.  Operacje płaszczyzny kontroli i płaszczyzny danych są ograniczone do określonej strefy. Niepowodzenie w strefie innej niż lokalizacja, w której istnieje scenariusz, nie ma wpływu na translatora adresów sieciowych. Ruch wychodzący z maszyn wirtualnych znajdujących się w tej samej strefie nie powiedzie się z powodu izolacji strefy.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtual Network translator adresów sieciowych ze strefami dostępności">
</p>

*Rysunek: Virtual Network translator adresów sieciowych ze strefami dostępności*

Izolowana od strefy Brama NAT wymaga, aby adresy IP odpowiadały strefie bramy translatora adresów sieciowych. Zasoby bramy translatora adresów sieciowych z adresami IP z innej strefy lub bez strefy nie są obsługiwane.

Sieci wirtualne i podsieci są regionalne i nie są wyrównane do warstwy. Maszyna wirtualna musi znajdować się w tej samej strefie co Brama translatora adresów sieciowych w celu zapewnienia strefowego obietnicy połączeń wychodzących. Izolacja strefy jest tworzona przez utworzenie zona "Stack" na strefę dostępności. Nie będzie można użyć obietnicy strefy w przypadku przekroczenia stref bramy translatora adresów sieciowych lub korzystania z regionalnej bramy NAT z strefami maszyn wirtualnych.

Wdrażając zestawy skalowania maszyn wirtualnych do użycia z translatorem adresów sieciowych, należy wdrożyć zestaw skalowania strefowego w jego własnej podsieci i dołączyć do tej podsieci zgodną z bramą translatora adresów sieciowych. W przypadku korzystania z zestawów skalowania obejmujących strefy (zestaw skalowania w co najmniej dwóch strefach) translator adresów sieciowych nie będzie zapewniać strefowej obietnicy.  Translator adresów sieciowych nie obsługuje nadmiarowości strefy.  Obsługiwane jest tylko izolacja regionalna lub strefy.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Łączenie Virtual Network translatora adresów sieciowych">
</p>

*Ilustracja: łączenie Virtual Network translatora adresów sieciowych*

Właściwość Zones nie jest modyfikowalna.  Wdróż ponownie zasób bramy translatora adresów sieciowych z zamierzonym preferencjami regionalnymi lub strefy.

>[!NOTE] 
>Adresy IP same przez siebie nie są strefowo nadmiarowe, jeśli nie określono żadnej strefy.  Fronton [Usługa Load Balancer w warstwie Standardowa jest strefowo nadmiarowy](../load-balancer/load-balancer-standard-availability-zones.md#frontend) , jeśli adres IP nie został utworzony w określonej strefie.  Nie dotyczy to translatora adresów sieciowych.  Obsługiwane jest tylko izolacja regionalna lub strefy.

## <a name="source-network-address-translation"></a>Translacja adresów sieci źródłowej

Translator adresów sieciowych (Resources Address Translation) ponownie zapisuje źródło przepływu, aby pochodził z innego adresu IP.  Zasoby bramy translatora adresów sieciowych wykorzystują odmianę przywoływaną zwykle do translacji adresów portu (źródła). Źródło ponownie zapisuje adres źródłowy i port źródłowy. Za pomocą elementu SubPrivate nie istnieje stała relacja między liczbą prywatnych adresów i ich przetłumaczonymi adresami publicznymi.  

### <a name="fundamentals"></a>Podstawy

Spójrzmy na przykład cztery przepływy, aby wyjaśnić podstawową koncepcję.  Brama translatora adresów sieciowych korzysta z 65.52.0.2 zasobów publicznego adresu IP.

| Ruch | Krotka źródłowa | Kolekcja docelowa |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Te przepływy mogą wyglądać następująco po przejściu z lokalizacji:

| Ruch | Krotka źródłowa | Spójna kolekcja Source SNAT'ed | Kolekcja docelowa | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

Lokalizacja docelowa będzie widziała źródło przepływu jako 65.52.0.2 (krotka źródłowa źródła danych) ze pokazywanym przypisanym portem.  Dane o pokazanej w powyższej tabeli nazywa się również przystawcy reportcy adresów sieciowych.  Wiele źródeł prywatnych jest zamaskowanych za adresem IP i portem.

Nie należy polegać na określonym sposobie przypisywania portów źródłowych.  Powyższym jest ilustracja wyłącznie koncepcji podstawowej.

Przystawka NAT określona przez translatora adresów sieciowych różni się od [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) w kilku aspektach.

### <a name="on-demand"></a>Na żądanie

Translacja adresów sieciowych na żądanie umożliwia obsługę nowych przepływów ruchu wychodzącego. Wszystkie dostępne porty protokołu Subnet w spisie są używane przez dowolną maszynę wirtualną w podsieciach skonfigurowanych przy użyciu translatora adresów sieciowych. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego na żądanie">
</p>

*Rysunek: Virtual Network translator adresów sieciowych na żądanie*

Dowolna konfiguracja adresu IP maszyny wirtualnej może tworzyć przepływy wychodzące na żądanie w razie potrzeby.  Alokacja wstępna, na wystąpienie w przypadku wystąpienia na najgorszy przypadek, nie jest wymagana.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Różnice w scenariuszach wyczerpania">
</p>

*Ilustracja: różnice w scenariuszach wyczerpania*

Po wydaniach portów dla tego elementu jest on dostępny do użycia przez dowolną maszynę wirtualną w podsieciach skonfigurowanych przy użyciu translatora adresów sieciowych.  Alokacja na żądanie umożliwia dynamiczne i niezależne obciążenia w podsieciach, w których są wymagane.  O ile jest dostępny spis portów współistnienia, przepływy adresów sieciowych będą się kończyć pomyślnie. Aktywne punkty dostępu portów przyłączania () mogą korzystać z większej ilości zasobów. Porty z przydziałami nie są pozostawiane niepotrzebnym maszynom wirtualnym.

### <a name="scaling"></a>Skalowanie

Translator adresów sieciowych wymaga wystarczającej ilości spisu portów dla pełnego scenariusza wychodzącego. Skalowanie translatora adresów sieciowych jest głównie funkcją zarządzania udostępnionym spisem portów przydziałów adresów sieciowych. Wystarczające zapasy muszą istnieć, aby zająć się szczytowym przepływem wychodzącym dla wszystkich podsieci podłączonych do zasobu bramy translatora adresów sieciowych.

Podzbiory adresów sieciowych mapują wiele adresów prywatnych na jeden adres publiczny i używają wielu publicznych adresów IP do skalowania.

Zasób bramy translatora adresów sieciowych będzie używać portów 64 000 (porty IP) dla publicznego adresu IP.  Te porty przydziałania są dostępne w ramach mapowania przepływów prywatnych do publicznych. Dodanie większej liczby publicznych adresów IP spowoduje zwiększenie dostępnych portów źródłowego translatora adresów sieciowych. Zasoby bramy translatora adresów sieciowych można skalować do maksymalnie 16 adresów IP i 1 – 1 portów.  Protokoły TCP i UDP są oddzielnymi spisami portów i niezwiązanych z nimi.

Zasoby bramy translatora adresów sieciowych odpowiednio Uzgodnij ponownie używać portów źródłowych. W celu skalowania należy założyć, że każdy przepływ wymaga nowego portu i przeskaluje łączną liczbę dostępnych adresów IP dla ruchu wychodzącego.

### <a name="protocols"></a>Protokoły

Zasoby bramy translatora adresów sieciowych współpracują z nagłówkami transportu IP i IP przepływów UDP i TCP oraz są niezależny od do ładunków warstwy aplikacji.  Inne protokoły IP nie są obsługiwane.

### <a name="timers"></a>Czasomierze

Limit czasu bezczynności można dostosować z 4 minut (domyślnie) do 120 minut (2 godziny) dla wszystkich przepływów.  Dodatkowo można zresetować czasomierz bezczynności z ruchem w przepływie.  Zalecany wzorzec odświeżania długich połączeń bezczynnych i wykrywania aktywności punktu końcowego to ruch TCP.  Utrzymywanie aktywności protokołu TCP jest wyświetlane jako zduplikowane potwierdzenia do punktów końcowych, są niskie obciążenie i niewidoczne dla warstwy aplikacji.

Następujące czasomierze są używane dla wydania portu reportów adresów sieciowych:

| Czasomierz | Wartość |
|---|---|
| PAKIET TCP FIN | 60 sekund |
| PARAMETR RST PROTOKOŁU TCP | 10 sekund |
| Otwarta połowa TCP | 30 sekund |

Port dla tego elementu jest dostępny do ponownego użycia w tym samym docelowym adresie IP i porcie docelowym po 5 sekundach.

>[!NOTE] 
>Te ustawienia czasomierza mogą ulec zmianie. Te wartości są dostarczane w celu ułatwienia rozwiązywania problemów i nie należy w tym momencie podejmować zależności od określonych czasomierzy.

## <a name="limitations"></a>Ograniczenia

- Translator adresów sieciowych jest zgodny z publicznym adresem IP jednostki SKU, publicznym prefiksem adresu IP i zasobami modułu równoważenia obciążenia.   Zasoby podstawowe (na przykład podstawowa usługa równoważenia obciążenia) i wszelkie produkty pochodzące z nich nie są zgodne z translatorem adresów sieciowych.  Zasoby podstawowe muszą być umieszczone w podsieci, która nie jest skonfigurowana przy użyciu translatora adresów sieciowych.
- Rodzina adresów IPv4 jest obsługiwana.  Translator adresów sieciowych nie współdziała z rodziną adresów IPv6.
- SIECIOWEJ grupy zabezpieczeń w podsieci lub karcie sieciowej nie jest uznawany za przepływy wychodzące do publicznych punktów końcowych przy użyciu translatora adresów sieciowych
- Rejestrowanie przepływu sieciowej grupy zabezpieczeń nie jest obsługiwane w przypadku korzystania z translatora adresów sieciowych.
- Translator adresów sieciowych nie może obejmować wielu sieci wirtualnych.

## <a name="preview-participation"></a>Podgląd udziału

Postępuj zgodnie [z instrukcjami, aby włączyć subskrypcję](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Opinia

Chcemy wiedzieć, jak możemy ulepszyć usługę. Udostępnij nam swoją [opinię na temat publicznej wersji zapoznawczej](https://aka.ms/natfeedback) .  Możesz zaproponować i zagłosować, co będziemy kompilować dalej w usłudze [UserVoice dla translatora adresów sieciowych](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [translatorze adresów sieciowych sieci wirtualnych](nat-overview.md).
- Samouczek dotyczący weryfikowania bramy translatora adresów sieciowych
  * [Interfejs wiersza polecenia platformy Azure](tutorial-create-validate-nat-gateway-cli.md),
  * Program [PowerShell](tutorial-create-validate-nat-gateway-cli.md),
  * [Portal](tutorial-create-validate-nat-gateway-cli.md)
- Przewodnik Szybki Start dotyczący wdrażania zasobu bramy translatora adresów sieciowych
  * [Interfejs wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md),
  * Program [PowerShell](./quickstart-create-nat-gateway-powershell.md),
  * [Portal](./quickstart-create-nat-gateway-portal.md).
- Dowiedz się więcej o [strefach dostępności](../availability-zones/az-overview.md).
- Dowiedz się więcej na temat usługi [równoważenia obciążenia w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md).
- Dowiedz się więcej na temat [stref dostępności i standardowego modułu równoważenia obciążenia](../load-balancer/load-balancer-standard-availability-zones.md).
- Dowiedz się więcej o interfejsie API zasobów bramy translatora adresów sieciowych
  * [interfejs API REST](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways),
  * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest),
  * [Program PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
- [Powiedz nam, co należy utworzyć w następnej kolejności w usłudze UserVoice](https://aka/natuservoice).
- [Prześlij opinię na temat publicznej wersji zapoznawczej](https://aka.ms/natfeedback).
