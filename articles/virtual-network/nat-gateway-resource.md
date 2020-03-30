---
title: Projektowanie sieci wirtualnych przy za pomocą zasobów bramy NAT
titleSuffix: Azure Virtual Network NAT
description: Dowiedz się, jak projektować sieci wirtualne za pomocą zasobów bramy NAT.
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
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 48fd4b0e6f0351cd46fc4063785d961867637e0c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80060647"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Projektowanie sieci wirtualnych z zasobami bramy NAT

Zasoby bramy TRANSLATOR są częścią [translatora adresów sieci wirtualnych](nat-overview.md) i zapewniają wychodzącą łączność z Internetem dla co najmniej jednej podsieci sieci wirtualnej. Podsieć sieci wirtualnej określa, która brama NAT będzie używana. Translator adresów sieciowych zapewnia translację adresów sieci źródłowych (SNAT) dla podsieci.  Zasoby bramy TRANSLATORa określają, które statyczne adresy IP są używane przez maszyny wirtualne podczas tworzenia przepływów wychodzących. Statyczne adresy IP pochodzą z publicznych zasobów adresów IP, publicznych zasobów prefiksu IP lub obu tych podstawowych. Zasób bramy NAT może używać maksymalnie 16 statycznych adresów IP z obu.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT dla wychodzących do Internetu">
</p>

*Rysunek: Virtual Network NAT dla wychodzących do Internetu*

## <a name="how-to-deploy-nat"></a>Jak wdrożyć translatora i odpowiedzi na temat

Konfigurowanie i używanie bramy NAT jest celowo proste:  

Zasób bramy TRANSLATOR:
- Tworzenie zasobów bramy NAT regionalnej lub strefowej (izolowanej strefą),
- Przypisywanie adresów IP,
- Modyfikowanie limitu czasu bezczynnego TCP (opcjonalnie).

Sieć wirtualna:
- Skonfiguruj podsieć sieci wirtualnej do używania bramy TRANSLATORA.

Trasy zdefiniowane przez użytkownika nie są konieczne.

## <a name="resource"></a>Zasób

Zasób został zaprojektowany tak, aby był prosty, jak widać na poniższym przykładzie usługi Azure Resource Manager w formacie przypominającym szablon.  Ten format podobny do szablonu jest pokazany tutaj, aby zilustrować pojęcia i strukturę.  Zmodyfikuj przykład dla swoich potrzeb.  Ten dokument nie jest przeznaczony jako samouczek.

Na poniższym diagramie przedstawiono zapisywalne odwołania między różnymi zasobami usługi Azure Resource Manager.  Strzałka wskazuje kierunek odniesienia, pochodzący z miejsca, z którego można go zapisać. Przegląd 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Model obiektu TRANSLATORA sieci wirtualnej">
</p>

*Rysunek: Model obiektu NAT sieci wirtualnej*

Translator z translatora i odpowiedzi jest zalecany dla większości obciążeń, chyba że istnieje określona zależność od [łączności wychodzącej modułu równoważenia obciążenia opartego](../load-balancer/load-balancer-outbound-connections.md)na puli.  

Można migrować ze standardowych scenariuszy modułu równoważenia obciążenia, w tym [reguł ruchu wychodzącego,](../load-balancer/load-balancer-outbound-rules-overview.md)do bramy NAT. Aby przeprowadzić migrację, przenieś publiczne zasoby prefiksu ip i publicznego adresu IP z frontendów modułu równoważenia obciążenia do bramy NAT. Nowe adresy IP bramy NAT nie są wymagane. Standardowe publiczne adresy IP i prefiks mogą być ponownie użyte, o ile suma nie przekracza 16 adresów IP. Planowanie migracji z przerwą w świadczeniu usług na uwadze w okresie przejściowym.  Przerwę można zminimalizować, automatyzując proces. Najpierw przetestuj migrację w środowisku przejściowym.  Podczas przejścia nie ma to wpływu na przychodzące przepływy pochodzące.

Poniższy przykład jest fragment z szablonu usługi Azure Resource Manager.  Ten szablon wdraża kilka zasobów, w tym bramę NAT.  Szablon ma następujące parametry w tym przykładzie:

- **natgatewayname** - Nazwa bramy NAT.
- **lokalizacja** — region platformy Azure, w którym znajduje się zasób.
- **publicipname** — nazwa wychodzącego publicznego adresu IP skojarzonego z bramą NAT.
- **publicipprefixname** — nazwa wychodzącego prefiksu publicznego adresu IP skojarzonego z bramą NAT.
- **vnetname** - Nazwa sieci wirtualnej.
- **podsieci** — nazwa podsieci skojarzonej z bramą NAT.

Całkowita liczba adresów IP podanych przez wszystkie zasoby adresów IP i prefiksów nie może przekroczyć 16 adresów IP. Dozwolona jest dowolna liczba adresów IP z zakresu od 1 do 16.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="256-281":::

Po utworzeniu zasobu bramy NAT można go używać w jednej lub kilku podsieciach sieci wirtualnej. Określ, które podsieci używają tego zasobu bramy TRANSLATORA. Brama NAT nie może obejmować więcej niż jednej sieci wirtualnej. Nie jest wymagane przypisanie tej samej bramy NAT do wszystkich podsieci sieci wirtualnej. Poszczególne podsieci można skonfigurować z różnymi zasobami bramy NAT.

Scenariusze, które nie używają stref dostępności będą regionalne (nie określono strefy). Jeśli używasz stref dostępności, możesz określić strefę, która ma być izolowana dla translatora z translatora i ciągłego ładowania do określonej strefy. Nadmiarowość stref nie jest obsługiwana. Przejrzyj [strefy dostępności](#availability-zones)NAT .

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="225-255" highlight="239-251":::

Bramy NAT są definiowane za pomocą właściwości w podsieci w sieci wirtualnej. Przepływy utworzone przez maszyny wirtualne **podsieci podsieci** nazwy **sieci wirtualnej** sieci wirtualnej będą używać bramy NAT. Wszystkie połączenia wychodzące będą używać adresów IP skojarzonych z **natgatewayname** jako źródłowego adresu IP.

Aby uzyskać więcej informacji na temat szablonu usługi Azure Resource Manager używanego w tym przykładzie, zobacz:

- [Szybki start: tworzenie bramy NAT — szablon Menedżera zasobów](quickstart-create-nat-gateway-template.md)
- [Nat sieci wirtualnej](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Wskazówki dotyczące projektu

Przejrzyj tę sekcję, aby zapoznać się z zagadnieniami związanymi z projektowaniem sieci wirtualnych za pomocą translatora adresów sieciowych.  

1. [Optymalizacja kosztów](#cost-optimization)
1. [Współistnienie przychodzących i wychodzących](#coexistence-of-inbound-and-outbound)
2. [Zarządzanie podstawowymi zasobami](#managing-basic-resources)
3. [Strefy dostępności](#availability-zones)

### <a name="cost-optimization"></a>Optymalizacja kosztów

[Punkty końcowe usługi](virtual-network-service-endpoints-overview.md) i [łącze prywatne](../private-link/private-link-overview.md) to opcje, które należy wziąć pod uwagę w celu optymalizacji kosztów. Translator z translatora i obsługi klienta nie jest potrzebny dla tych usług. Ruch kierowany do punktów końcowych usługi lub łącza prywatnego nie jest przetwarzany przez translatora adresów sieci wirtualnej.  

Punkty końcowe usługi wiążą zasoby usługi platformy Azure z siecią wirtualną i kontrolują dostęp do zasobów usługi platformy Azure. Na przykład podczas uzyskiwania dostępu do magazynu platformy Azure użyj punktu końcowego usługi dla magazynu, aby uniknąć opłat za przetworzone nat danych. Punkty końcowe usługi są bezpłatne.

Łącze prywatne udostępnia usługę PaaS platformy Azure (lub inne usługi hostowane przy łączu prywatnym) jako prywatny punkt końcowy w sieci wirtualnej.  Łącze prywatne jest rozliczane na podstawie czasu trwania i przetwarzanych danych.

Oceń, czy jedno lub oba z tych podejść są dobre dopasowanie do scenariusza i używać w razie potrzeby.

### <a name="coexistence-of-inbound-and-outbound"></a>Współistnienie przychodzących i wychodzących

Brama NAT jest zgodna z:

 - Standardowy moduł równoważenia obciążenia
 - Standardowy publiczny adres IP
 - Standardowy publiczny prefiks IP

Podczas opracowywania nowego wdrożenia, zacznij od standardowych jednostek SKU.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT dla wychodzących do Internetu">
</p>

*Rysunek: Virtual Network NAT dla wychodzących do Internetu*

Internet wychodzących tylko scenariusz dostarczony przez bramę NAT można rozszerzyć z przychodzących z internetu funkcji. Każdy zasób jest świadomy kierunku, w którym przepływ pochodzi. W podsieci z bramą NAT wszystkie scenariusze wychodzące do Internetu są zastępowane przez bramę NAT. Przychodzące ze scenariuszy internetowych są dostarczane przez odpowiedni zasób.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>Translator adresów sieciowych i maszyny wirtualnej z publicznym adresem IP na poziomie wystąpienia

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Translator adresów sieci wirtualnej i maszyny wirtualnej z publicznym adresem IP na poziomie wystąpienia">
</p>

*Rysunek: Translator adresów sieci wirtualnych i maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia |
| Wychodzący | Brama translatora adresów sieciowych |

Maszyna wirtualna użyje bramy NAT do ruchu wychodzącego.  Nie ma to wpływu na przychodzące pochodzi.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT i maszyna wirtualna z publicznym modułem równoważenia obciążenia

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Translator adresów sieci wirtualnej i maszyny wirtualnej z publicznym modułem równoważenia obciążenia">
</p>

*Rysunek: Translator adresów sieci wirtualnych i maszyna wirtualna z publicznym modułem równoważenia obciążenia*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | publiczny moduł równoważenia obciążenia |
| Wychodzący | Brama translatora adresów sieciowych |

Każda konfiguracja wychodząca z reguły równoważenia obciążenia lub reguł wychodzących jest zastępowana przez bramę NAT.  Nie ma to wpływu na przychodzące pochodzi.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>Translator adresów sieciowych i maszyn wirtualnych z publicznym ipem na poziomie wystąpienia i publicznym modułem równoważenia obciążenia

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Translator adresów sieci wirtualnej i maszyny wirtualnej z publicznym ipem na poziomie wystąpienia i publicznym modułem równoważenia obciążenia">
</p>

*Rysunek: Translator adresów sieci wirtualnych i maszyna wirtualna z publicznym ipem na poziomie wystąpienia i publicznym modułem równoważenia obciążenia*

| Kierunek | Zasób |
|:---:|:---:|
| Przychodzący | Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia i publicznym modułem równoważenia obciążenia |
| Wychodzący | Brama translatora adresów sieciowych |

Każda konfiguracja wychodząca z reguły równoważenia obciążenia lub reguł wychodzących jest zastępowana przez bramę NAT.  Maszyna wirtualna będzie również używać bramy NAT dla ruchu wychodzącego.  Nie ma to wpływu na przychodzące pochodzi.

### <a name="managing-basic-resources"></a>Zarządzanie podstawowymi zasobami

Standardowy moduł równoważenia obciążenia, publiczny adres IP i publiczny prefiks IP są zgodne z bramą NAT. Bramy TRANSLATORA działają w zakresie podsieci. Podstawowa jednostka SKU tych usług musi zostać wdrożona w podsieci bez bramy TRANSLATORA. Ta separacja umożliwia obu wariantów SKU współistnieć w tej samej sieci wirtualnej.

Bramy TRANSLATORA mają pierwszeństwo przed wychodzącymi scenariuszami podsieci. Podstawowy moduł równoważenia obciążenia lub publiczny adres IP (i żadna usługa zarządzana zbudowana za ich pomocą) nie może być dostosowana za pomocą prawidłowych tłumaczeń. Brama NAT przejmuje kontrolę nad ruchem wychodzącym w podsieci. Ruch przychodzący do podstawowego modułu równoważenia obciążenia i publicznego adresu IP jest niedostępny. Ruch przychodzący do podstawowego modułu równoważenia obciążenia lub publiczne ip skonfigurowane na maszynie wirtualnej nie będą dostępne.

### <a name="availability-zones"></a>Strefy dostępności

Nawet bez stref dostępności translator adresów sieciowych jest odporny i może przetrwać wiele awarii składników infrastruktury. Gdy strefy dostępności są częścią scenariusza, należy skonfigurować translatora i ciągłego opisu dla określonej strefy.  Operacje płaszczyzny sterowania i płaszczyzny danych są powiązane z określoną strefą. Błąd w strefie innej niż istnieje scenariusz oczekuje się bez wpływu na TRANSLATORA. Ruch wychodzący z maszyn wirtualnych w tej samej strefie zakończy się niepowodzeniem z powodu izolacji strefy.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtual Network NAT ze strefami dostępności">
</p>

*Rysunek: Virtual Network NAT ze strefami dostępności*

Brama NAT izolowana w strefie wymaga adresów IP, aby dopasować go do strefy bramy NAT. Zasoby bramy TRANSLATORa z adresami IP z innej strefy lub bez strefy nie są obsługiwane.

Sieci wirtualne i podsieci są regionalne i nie są wyrównane. Maszyna wirtualna musi znajdować się w tej samej strefie co brama NAT dla obietnicy strefowej połączeń wychodzących. Izolacja strefy jest tworzona przez utworzenie strefowego "stosu" na strefę dostępności. Obietnica strefowa nie będzie istnieć podczas przekraczania stref bramy strefowej nat lub przy użyciu regionalnej bramy NAT z maszynami wirtualnymi strefowymi.

Podczas wdrażania zestawów skalowania maszyny wirtualnej do użycia z translatorem adresów sieciowych można wdrożyć zestaw skalowania strefowego we własnej podsieci i dołączyć pasującą bramę NAT strefy do tej podsieci. Jeśli używasz zestawów skalowania obejmujących strefę (skala ustawiona w dwóch lub więcej strefach), translator wartości nat nie zapewni obietnicy strefowej.  Translator z translatora i wydajności nie obsługuje nadmiarowości strefy.  Obsługiwane jest tylko izolacja regionalna lub strefa.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="strefa obejmująca virtual network NAT">
</p>

*Rysunek: Strefa obejmująca virtual network NAT*

Właściwość zones nie jest modyfikowalna.  Ponowne wdrożenie zasobu bramy NAT z zamierzonymi preferencjami regionalnymi lub strefami.

>[!NOTE] 
>Adresy IP same w sobie nie są nadmiarowe strefy, jeśli nie określono strefy.  Frontend [standardowego modułu równoważenia obciążenia jest nadmiarowy strefowo,](../load-balancer/load-balancer-standard-availability-zones.md#frontend) jeśli adres IP nie jest tworzony w określonej strefie.  Nie dotyczy to translatora i odpowiedzi na dzieci.  Obsługiwane jest tylko izolacja regionalna lub strefa.

## <a name="source-network-address-translation"></a>Translacja źródłowego adresu sieciowego

Źródłowe translacji adresów sieciowych (SNAT) przepisuje źródło przepływu, aby pochodzić z innego adresu IP.  Zasoby bramy NAT używają wariantu SNAT powszechnie odwoływanego do tłumaczenia adresów portów (PAT). PAT przepisuje adres źródłowy i port źródłowy. W przypadku SNAT nie ma stałej relacji między liczbą prywatnych adresów a ich przetłumaczonymi adresami publicznymi.  

### <a name="fundamentals"></a>Podstawy

Spójrzmy na przykład czterech przepływów, aby wyjaśnić podstawową koncepcję.  Brama NAT używa zasobu publicznego adresu IP 65.52.0.2.

| Ruch | Krotka źródła | Krotka docelowa |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Przepływy te mogą wyglądać następująco po pat miało miejsce:

| Ruch | Krotka źródła | Krotka źródła SNAT | Krotka docelowa | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

Miejsce docelowe zobaczy źródło przepływu jako 65.52.0.2 (krotka źródła SNAT) z pokazanym przypisanym portem.  PAT, jak pokazano w powyższej tabeli jest również nazywany port maskarady SNAT.  Wiele źródeł prywatnych jest maskowanych za adresem IP i portem.

Nie należy przyjmować zależności od określonego sposobu portów źródłowych są przypisane.  Powyższe jest jedynie ilustracją pojęcia fundamentalnego.

SNAT dostarczone przez NAT różni się od [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) w kilku aspektach.

### <a name="on-demand"></a>Na żądanie

TRANSLATOR IT udostępnia porty SNAT na żądanie dla nowych przepływów ruchu wychodzącego. Wszystkie dostępne porty SNAT w magazynie są używane przez dowolną maszynę wirtualną w podsieciach skonfigurowanych z translatorem adresów sieciowych. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Sieć wirtualna NAT na żądanie wychodzących SNAT">
</p>

*Rysunek: Sieć wirtualna NAT na żądanie wychodzących SNAT*

Każda konfiguracja IP maszyny wirtualnej może tworzyć przepływy wychodzące na żądanie zgodnie z potrzebami.  Alokacja wstępna, na planowanie wystąpienia, w tym na wystąpienie najgorszego przypadku nadmiernego obsługi administracyjnej, nie jest wymagane.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Różnice w scenariuszach wyczerpania">
</p>

*Rysunek: Różnice w scenariuszach wyczerpania*

Po wydaniu portu SNAT jest on dostępny do użycia przez dowolną maszynę wirtualną w podsieciach skonfigurowanych z translatorem adresów sieciowych.  Alokacja na żądanie umożliwia dynamiczne i rozbieżne obciążenia w podsieci do używania portów SNAT zgodnie z potrzebami.  Tak długo, jak nie ma zapasów portów SNAT dostępne, przepływy SNAT zakończy się pomyślnie. Zamiast tego punkty hot spoty portów SNAT korzystają z większych zapasów. Porty SNAT nie są nieużywane dla maszyn wirtualnych, które nie są aktywnie ich potrzebujące.

### <a name="scaling"></a>Skalowanie

NAT potrzebuje wystarczającej ilości zapasów portów SNAT dla pełnego scenariusza wychodzącego. Skalowanie NAT jest przede wszystkim funkcją zarządzania udostępnionym, dostępnym zapasem portów SNAT. Musi istnieć wystarczająca ilość zapasów, aby zaradzić szczytowy przepływ wychodzący dla wszystkich podsieci dołączonych do zasobu bramy TRANSLATORA.

SNAT mapuje wiele adresów prywatnych na jeden adres publiczny i używa wielu publicznych adresów IP do skalowania.

Zasób bramy NAT użyje 64 000 portów (portów SNAT) publicznego adresu IP.  Te porty SNAT stają się dostępnym zapasem dla mapowania przepływu prywatnego i publicznego. A dodanie większej liczby publicznych adresów IP zwiększa dostępne porty SNAT zapasów. Zasoby bramy TRANSLATORA mogą skalować do 16 adresów IP i portów 1M SNAT.  TCP i UDP są oddzielnymi zapasami portów SNAT i niepowiązanych.

Zasoby bramy NAT oportunistycznie ponownie użyć portów źródłowych. Do celów skalowania należy założyć, że każdy przepływ wymaga nowego portu SNAT i skalować całkowitą liczbę dostępnych adresów IP dla ruchu wychodzącego.

### <a name="protocols"></a>Protokoły

Zasoby bramy NAT współdziałają z nagłówkami transportu IP i IP przepływów UDP i TCP i są niezależni od ładunków warstw aplikacji.  Inne protokoły IP nie są obsługiwane.

### <a name="timers"></a>Czasomierze

Limit czasu bezczynnego TCP można dostosować z 4 minut (domyślnie) do 120 minut (2 godziny) dla wszystkich przepływów.  Ponadto można zresetować czasomierz bezczynny z ruchem w przepływie.  Zalecanym wzorcem do odświeżania długich bezczynności połączeń i wykrywania żywotności punktu końcowego jest tcp keepalives.  TCP keepalives są wyświetlane jako zduplikowane punkty połączenia sieciowego do punktów końcowych, są niskie obciążenie i niewidoczne dla warstwy aplikacji.

Następujące czasomierze są używane do zwalniania portów SNAT:

| Czasomierz | Wartość |
|---|---|
| PŁETŻNIE TCP | 60 sekund |
| TCP RST | 10 sekund |
| TCP pół otwarty | 30 sekund |

Port SNAT jest dostępny do ponownego użycia do tego samego docelowego adresu IP i portu docelowego po 5 sekundach.

>[!NOTE] 
>Te ustawienia czasomierza mogą ulec zmianie. Wartości są dostarczane w celu pomocy w rozwiązywaniu problemów i nie należy przyjmować zależności od określonych czasomierzy w tej chwili.

## <a name="limitations"></a>Ograniczenia

- Translator adresów sieciowych jest zgodny ze standardowymi publicznymi zasobami ip, publicznymi adresami IP i modułem równoważenia obciążenia.   Podstawowe zasoby (na przykład podstawowy moduł równoważenia obciążenia) i wszystkie produkty z nich uzyskane nie są zgodne z translatorem z treścią nat.  Podstawowe zasoby muszą być umieszczone w podsieci, która nie jest skonfigurowana z translatorem z napisem NAT.
- Rodzina adresów IPv4 jest obsługiwana.  Translator adresów nawigacyjnej nie wchodzi w interakcje z rodziną adresów IPv6.  Nie można wdrożyć translatora i uporządkowanej maryi w podsieci z prefiksem IPv6.
- Rejestrowanie przepływu nsg nie jest obsługiwane podczas korzystania z translatora z translatorem ludzi.
- Translator adresów sieciowych nie może obejmować wielu sieci wirtualnych.


## <a name="feedback"></a>Opinia

Chcemy wiedzieć, w jaki sposób możemy ulepszyć usługę. Zaproponować i głosować na to, co powinniśmy budować dalej w [UserVoice dla NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [sieci wirtualnej NAT](nat-overview.md).
* Dowiedz się więcej o [metrykach i alertach dotyczących zasobów bramy TRANSLATORA](nat-metrics.md).
* Dowiedz się więcej o [rozwiązywaniu problemów z zasobami bramy NAT](troubleshoot-nat.md).
* Samouczek sprawdzania poprawności bramy NAT
  - [Interfejs wiersza polecenia platformy Azure](tutorial-create-validate-nat-gateway-cli.md)
  - [Powershell](tutorial-create-validate-nat-gateway-cli.md)
  - [Portal](tutorial-create-validate-nat-gateway-cli.md)
* Szybki start do wdrażania zasobu bramy NAT
  - [Interfejs wiersza polecenia platformy Azure](./quickstart-create-nat-gateway-cli.md)
  - [Powershell](./quickstart-create-nat-gateway-powershell.md)
  - [Portal](./quickstart-create-nat-gateway-portal.md)
  - [Szablonu](./quickstart-create-nat-gateway-template.md)
* Dowiedz się więcej o interfejsie API zasobów bramy NAT
  - [INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [Powershell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)

* Dowiedz się więcej o [strefach dostępności](../availability-zones/az-overview.md).
* Dowiedz się więcej o [standardowym równoważącym obciążenia](../load-balancer/load-balancer-standard-overview.md).
* Dowiedz się więcej o [strefach dostępności i standardowym równoważącym obciążenia](../load-balancer/load-balancer-standard-availability-zones.md).


