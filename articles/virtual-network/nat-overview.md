---
title: Co to jest translator adresów sieci wirtualnej platformy Azure?
titlesuffix: Azure Virtual Network
description: Omówienie funkcji, zasobów, architektury i implementacji sieci wirtualnej. Dowiedz się, jak działa translator adresów sieci wirtualnej i jak korzystać z zasobów bramy NAT w chmurze.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 50fc8b9cefe88a80f3f954ce363139b6a4a38589
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548384"
---
# <a name="what-is-virtual-network-nat"></a>Co to jest nat sieci wirtualnej?

Virtual Network NAT (translacja adresów sieciowych) upraszcza połączenie internetowe tylko wychodzące dla sieci wirtualnych. Po skonfigurowaniu w podsieci wszystkie połączenia wychodzące używają określonych statycznych publicznych adresów IP.  Łączność wychodząca jest możliwa bez modułu równoważenia obciążenia lub publicznych adresów IP bezpośrednio podłączonych do maszyn wirtualnych. Nat jest w pełni zarządzany i wysoce odporny.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Nat sieci wirtualnej">
</p>



*Rysunek: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statyczne adresy IP dla wychodzących

Łączność wychodzącą można zdefiniować dla każdej podsieci za pomocą translatora z napisem NAT.  Wiele podsieci w tej samej sieci wirtualnej może mieć różne translatory adresów sieciowych. Podsieć jest konfigurowana przez określenie zasobu bramy NAT do użycia. Wszystkie przepływy wychodzące UDP i TCP z dowolnego wystąpienia maszyny wirtualnej będą używać translatora adresów sieciowych. 

Translator adresów sieciowych jest zgodny ze standardowymi zasobami publicznego adresu IP jednostki SKU lub publicznymi zasobami prefiksów IP lub kombinacją obu tych zasobów.  Publiczny prefiks IP można używać bezpośrednio lub rozpowszechniać publiczne adresy IP prefiksu w wielu zasobach bramy TRANSLATORA. NAT będzie oczyszczenie całego ruchu do zakresu adresów IP prefiksu.  Wszystkie białe listy adresów IP wdrożeń jest teraz łatwe.

Cały ruch wychodzący dla podsieci jest przetwarzany przez translatora zabezpieczeń automatycznie bez konfiguracji klienta.  Trasy zdefiniowane przez użytkownika nie są konieczne. Translator adresów sieciowych ma pierwszeństwo przed innymi scenariuszami wychodzącymi i zastępuje domyślne miejsce docelowe w Sieci w podsieci.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT na żądanie z wieloma adresami IP do skalowania

Funkcja NAT używa "translacji adresów sieciowych portów" (PNAT lub PAT) i jest zalecana dla większości obciążeń. Dynamiczne lub rozbieżne obciążenia mogą być łatwo dostosowane do alokacji przepływu wychodzącego na żądanie. Unika się intensywnego planowania wstępnego, wstępnej alokacji, a ostatecznie nadmiernej obsługi zasobów wychodzących. Zasoby portów SNAT są współużytkowane i dostępne we wszystkich podsieciach przy użyciu określonego zasobu bramy TRANSLATORA i są dostarczane w razie potrzeby.

Publiczny adres IP dołączony do translatora adresów sieciowych zapewnia do 64 000 równoczesnych przepływów dla protokołu UDP i TCP. Możesz zacząć od jednego adresu IP i skalować do 16 publicznych adresów IP.

Translator adresów sieciowych umożliwia tworzenie przepływów z sieci wirtualnej do Internetu. Ruch zwrotny z Internetu jest dozwolony tylko w odpowiedzi na aktywny przepływ.

W przeciwieństwie do wychodzącego modułu SNAT modułu równoważenia obciążenia, translator adresów sieciowych nie ma żadnych ograniczeń, co do tego, na który prywatny adres IP wystąpienia maszyny wirtualnej może nawiązywać połączenia wychodzące.  Dodatkowe konfiguracje adresów IP mogą tworzyć wychodzące połączenie internetowe z translatorem adresów sieciowych.

## <a name="coexistence-of-inbound-and-outbound"></a>Współistnienie przychodzących i wychodzących

Translator z artykułu nd.

- Moduł równoważenia obciążenia
- Publiczny adres IP
- Prefiks publicznego adresu IP

W połączeniu z translatorem adresów sieciowych zasoby te zapewniają przychodzące połączenie z Internetem do podsieci. Translator adresów sieciowych zapewnia wszystkie wychodzące połączenia internetowe z podsieci.

Translator z translatora i zgodne standardowe funkcje SKU są świadome kierunku, w którym uruchomiono przepływ. Scenariusze przychodzące i wychodzące mogą współistnieć. Te scenariusze otrzymają poprawne translacji adresów sieciowych, ponieważ te funkcje są świadome kierunku przepływu. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Kierunek przepływu NAT sieci wirtualnej">
</p>

*Rysunek: Kierunek przepływu NAT sieci wirtualnej*

## <a name="fully-managed-highly-resilient"></a>W pełni zarządzane, wysoce odporne

Translator z translatora i dużych wartości jest w pełni skalowany w poziomie od samego początku. Nie jest wymagana operacja zwiększania ani skalowania w poziomie.  Platforma Azure zarządza działaniem translatora i obsługi nat dla Ciebie.  TRANSLATOR ADRES ZAWSZE ma wiele domen błędów i może wytrzymać wiele awarii bez awarii usługi.

## <a name="tcp-reset-for-unrecognized-flows"></a>Resetowanie TCP dla przepływów nierozpoznanych

Prywatna strona translatora adresów sieciowych wysyła pakiety resetowania TCP w celu uzyskania prób komunikowania się na połączenie TCP, które nie istnieje. Jednym z przykładów są połączenia, które osiągnęły limit czasu bezczynnego. Następny odebrany pakiet zwróci reset TCP do prywatnego adresu IP, aby zasygnalizować i wymusić zamknięcie połączenia.

Strona publiczna translatora i odpowiedzi nie generuje pakietów resetowania TCP ani żadnego innego ruchu.  Emitowany jest tylko ruch wytwarzany przez sieć wirtualną klienta.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurowalny limit czasu bezczynności TCP

Domyślny limit czasu bezczynności TCP 4 minut jest używany i może być zwiększona do 120 minut. Każda aktywność na przepływie można również zresetować czasomierz bezczynny, w tym TCP keepalives.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Izolacja regionalna lub strefa ze strefami dostępności

Translator z translatora i rozwoju jest domyślnie regionalny. Podczas tworzenia [scenariuszy stref dostępności,](../availability-zones/az-overview.md) NAT mogą być izolowane w określonej strefie (wdrożenia strefowego).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Virtual Network NAT ze strefami dostępności">
</p>

*Rysunek: Virtual Network NAT ze strefami dostępności*

## <a name="multi-dimensional-metrics-for-observability"></a>Metryk wielowymiarowych dla obserwowalności

Można monitorować działanie translatora i odpowiedzi na usługi, za pomocą wielowymiarowych metryk ujawnionych w usłudze Azure Monitor. Te metryki mogą służyć do obserwowania użycia i rozwiązywania problemów.  Zasoby bramy TRANSLATORa ujawniają następujące metryki:
- Bajty
- Pakiety
- Porzucone pakiety
- Całkowita liczba połączeń SNAT
- Przejścia stanu połączenia SNAT na interwał.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Umowa SLA

Przy ogólnej dostępności ścieżka danych NAT jest dostępna w co najmniej 99,9%.


## <a name="pricing"></a>Cennik

Brama NAT jest rozliczana z dwoma oddzielnymi licznikami:

| Miernik | Stawka |
| --- | --- |
| Godziny zasobów | $0.045/godz. |
| Przetwarzane dane | zł/GB |

Godziny zasobów są kontem czas trwania, w którym istnieje zasób bramy NAT.
Dane przetworzone konta dla całego ruchu przetwarzanego przez zasób bramy NAT.

## <a name="availability"></a>Dostępność

Translator adresów sieci wirtualnej i zasób bramy NAT są dostępne we wszystkich [regionach](https://azure.microsoft.com/global-infrastructure/regions/)chmury publicznej platformy Azure.

## <a name="support"></a>Pomoc techniczna

Translator z kanałów jest obsługiwany przez normalne kanały wsparcia.

## <a name="feedback"></a>Opinia

Chcemy wiedzieć, w jaki sposób możemy ulepszyć usługę. Zaproponować i głosować na to, co powinniśmy budować dalej w [UserVoice dla NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Ograniczenia

* Translator adresów sieciowych jest zgodny ze standardowymi publicznymi zasobami ip, publicznymi adresami IP i modułem równoważenia obciążenia. Podstawowe zasoby, takie jak podstawowy moduł równoważenia obciążenia i wszystkie produkty z nich uzyskane, nie są zgodne z translatorem z translatorem ludzi.  Podstawowe zasoby muszą być umieszczone w podsieci, która nie jest skonfigurowana z translatorem z napisem NAT.
* Rodzina adresów IPv4 jest obsługiwana.  Translator adresów nawigacyjnej nie wchodzi w interakcje z rodziną adresów IPv6.  Nie można wdrożyć translatora i uporządkowanej maryi w podsieci z prefiksem IPv6.
* Rejestrowanie przepływu nsg nie jest obsługiwane podczas korzystania z translatora z translatorem ludzi.
* Translator adresów sieciowych nie może obejmować wielu sieci wirtualnych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zasobie bramy NAT](./nat-gateway-resource.md).
* [Powiedz nam, co zbudować dalej dla Virtual Network NAT w UserVoice](https://aka.ms/natuservoice).

