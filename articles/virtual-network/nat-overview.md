---
title: Co to jest Azure Virtual Network translator adresów sieciowych?
description: Omówienie funkcji Virtual Network translatora adresów sieciowych, zasobów, architektury i implementacji. Dowiedz się, jak działa usługa NAT Virtual Network i jak używać zasobów bramy translatora adresów sieciowych w chmurze.
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
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: b27baed20d8e36bf5790036e2fdc0804a94a4ea1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589440"
---
# <a name="what-is-virtual-network-nat-public-preview"></a>Co to jest Virtual Network translator adresów sieciowych (publiczna wersja zapoznawcza)?

Virtual Network NAT (translator adresów sieciowych) upraszcza połączenia z Internetem tylko w ruchu wychodzącym dla sieci wirtualnych. W przypadku skonfigurowania w podsieci wszystkie połączenia wychodzące korzystają z określonych statycznych publicznych adresów IP.  Łączność wychodząca jest możliwa bez usługi równoważenia obciążenia lub publicznych adresów IP podłączonych bezpośrednio do maszyn wirtualnych. Translator adresów sieciowych jest w pełni zarządzany i wysoce odporny.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Virtual Network translator adresów sieciowych">
</p>



*Rysunek: Virtual Network translator adresów sieciowych*


>[!NOTE] 
>Virtual Network translator adresów sieciowych jest w tej chwili dostępny jako publiczna wersja zapoznawcza. Jest ono obecnie dostępne tylko w ograniczonym zestawie [regionów](#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

## <a name="static-ip-addresses-for-outbound-only"></a>Statyczne adresy IP tylko dla ruchu wychodzącego

Łączność wychodząca może być zdefiniowana dla każdej podsieci z translatorem adresów sieciowych.  Wiele podsieci w tej samej sieci wirtualnej może mieć różne NAT. Podsieć jest konfigurowana przez określenie, który [zasób bramy NAT](./nat-gateway-resource.md) ma być używany. Wszystkie przepływy wychodzące UDP i TCP z dowolnego wystąpienia maszyny wirtualnej będą korzystać z translatora adresów sieciowych. 

Translator adresów sieciowych jest zgodny z [zasobami publicznych adresów IP](./virtual-network-ip-addresses-overview-arm.md#standard) jednostki SKU lub [publicznymi zasobami prefiksu adresu IP](./public-ip-address-prefix.md) lub połączeniem obu tych elementów.  Publicznego prefiksu adresu IP można używać bezpośrednio lub rozpowszechniać publiczne adresy IP prefiksu w wielu zasobach bramy translatora adresów sieciowych. Translator adresów sieciowych będzie oczyszczał cały ruch do zakresu adresów IP prefiksu.  Wszystkie listy dozwolonych IP wdrożeń są teraz łatwe.

Cały ruch wychodzący dla podsieci jest przetwarzany automatycznie przy użyciu translatora adresów sieciowych bez żadnej konfiguracji klienta.  Trasy zdefiniowane przez użytkownika nie są wymagane. Translator adresów sieciowych ma wyższy priorytet niż inne [scenariusze wychodzące](../load-balancer/load-balancer-outbound-connections.md) i zastępuje domyślne miejsce docelowe w podsieci.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>Protokół integracyjny na żądanie z wieloma adresami IP na potrzeby skalowania

Translator adresów sieciowych korzysta z "translacji adresów sieci (PNAT lub") i jest zalecany w przypadku większości obciążeń. Dynamiczne lub rozbieżne obciążenia mogą być łatwo dostosowane do alokacji przepływów wychodzących na żądanie. Unika się rozbudowanego wstępnego planowania, wstępnego przydzielania i ostatecznego udostępniania zasobów wychodzących. Zasoby portów protokołu przesyłania adresów sieciowych są udostępniane i dostępne we wszystkich podsieciach przy użyciu określonego zasobu bramy NAT i są udostępniane w razie konieczności.

Publiczny adres IP dołączony do translatora adresów sieciowych zapewnia do 64 000 współbieżnych przepływów dla protokołów UDP i TCP. Możesz zacząć od pojedynczego adresu IP i skalować do 16 publicznych adresów IP.

Translator adresów sieciowych umożliwia tworzenie przepływów z sieci wirtualnej do Internetu. Ruch powrotny z Internetu jest dozwolony tylko w odpowiedzi na aktywny przepływ.

W przeciwieństwie do wychodzącego ruchu przychodzącego modułu równoważenia obciążenia translator adresów sieciowych nie ma żadnych ograniczeń dotyczących tego, który prywatny adres IP wystąpienia maszyny wirtualnej może nakonywać połączeń wychodzących.  Dodatkowe konfiguracje protokołu IP mogą tworzyć wychodzące połączenie internetowe z translatorem adresów sieciowych.

## <a name="coexistence-of-inbound-and-outbound"></a>Współistnienie ruchu przychodzącego i wychodzącego

Translator adresów sieciowych jest zgodny z następującymi zasobami standardowej jednostki SKU:

- [Moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)
- [Publiczny adres IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Prefiks publicznego adresu IP](../virtual-network/public-ip-address-prefix.md)

Te zasoby są używane razem z translatorem adresów sieciowych w celu zapewnienia przychodzącej łączności z Internetem w podsieciach. Translator adresów sieciowych udostępnia wszystkie wychodzące połączenia z Internetem z podsieci.

Funkcja NAT i zgodne standardowe funkcje jednostki SKU są świadome kierunku, w którym przepływ został uruchomiony. Scenariusze przychodzące i wychodzące mogą współistnieć. Te scenariusze będą otrzymywać poprawne tłumaczenia adresów sieciowych, ponieważ te funkcje są świadome kierunku przepływu. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Kierunek przepływu Virtual Network NAT">
</p>

*Rysunek: Virtual Network kierunek przepływu NAT*

## <a name="fully-managed-highly-resilient"></a>W pełni zarządzana, wysoce odporna

Translacja adresów sieciowych jest w pełni skalowana od początku. Nie jest wymagana żadna operacja skalowania w górę lub w poziomie.  Platforma Azure zarządza operacją translatora adresów sieciowych.  Translator adresów sieciowych zawsze ma wiele domen błędów i może utrzymywać wiele awarii bez przestoju usługi.

## <a name="tcp-reset-for-unrecognized-flows"></a>Resetowanie protokołu TCP dla nierozpoznanych przepływów

Po stronie prywatnej translatora adresów sieciowych wysyła pakiety resetowania protokołu TCP, aby próbować komunikować się z nieistniejącym połączeniem TCP. Przykładem są połączenia, które osiągnęły limit czasu bezczynności. Następny odebrany pakiet zwróci Reset TCP do prywatnego adresu IP, aby sygnalizować i wymusić zamknięcie połączenia.

Publiczna strona translatora adresów sieciowych nie generuje pakietów resetowania protokołu TCP ani żadnego innego ruchu.  Emitowany jest tylko ruch generowany przez sieć wirtualną klienta.

## <a name="configurable-idle-timeout"></a>Konfigurowalny limit czasu bezczynności

Używany jest domyślny limit czasu bezczynności wynoszący 4 minuty i można go zwiększyć do 120 minut. Wszystkie działania w przepływie mogą również zresetować czasomierz bezczynności, w tym utrzymywanie aktywności protokołu TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Izolacja regionalna lub strefa z strefami dostępności

Domyślnie jest to ustawienie regionalne. Podczas tworzenia scenariuszy [strefy dostępności](../availability-zones/az-overview.md) translator adresów sieciowych może być odizolowany w określonej strefie (wdrożenie strefowe).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Virtual Network translator adresów sieciowych ze strefami dostępności">
</p>

*Rysunek: Virtual Network translator adresów sieciowych ze strefami dostępności*

## <a name="multi-dimensional-metrics-for-observability"></a>Wielowymiarowe metryki do zaobserwowania

Można monitorować operacje NAT za pomocą wielowymiarowych metryk uwidocznionych w Azure Monitor. Te metryki mogą służyć do obserwowania użycia i rozwiązywania problemów.  Zasoby bramy translatora adresów sieciowych uwidaczniają następujące metryki:
- Bajty
- SPI
- Pakiety opuszczone
- Łączna liczba połączeń z translatorem adresów sieciowych
- Przejścia stanu połączenia z przyłączaniem adresów sieciowych na interwał.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>Umowa SLA

Przy ogólnej dostępności ścieżka danych NAT jest dostępna co najmniej 99,9%.

## <a name = "region-availability"></a>Dostępność regionów

Translator adresów sieciowych jest obecnie dostępny w następujących regionach:

- Europa Zachodnia
- Japonia Wschodnia
- Wschodnie stany USA 2
- Zachodnie stany USA
- Zachodnie stany USA 2
- Zachodnio-środkowe stany USA

## <a name = "enable-preview"></a>Udział w publicznej wersji zapoznawczej

Subskrypcje muszą być zarejestrowane, aby zezwolić na uczestnictwo w publicznej wersji zapoznawczej.  Uczestnictwo wymaga dwuetapowego procesu i instrukcje są podane poniżej dla interfejsu wiersza polecenia platformy Azure i Azure PowerShell.  Aktywacja może potrwać kilka minut.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Zarejestruj subskrypcję publicznej wersji zapoznawczej

    ```azurecli-interactive
      az feature register --namespace Microsoft.Network --name AllowNatGateway
    ```

2. Aktywuj rejestrację

    ```azurecli-interactive
      az provider register --namespace Microsoft.Network
    ```

### <a name="azure-powershell"></a>Azure PowerShell

1. Zarejestruj subskrypcję publicznej wersji zapoznawczej

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowNatGateway
    ```

2. Aktywuj rejestrację

    ```azurepowershell-interactive
      Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ```

## <a name="pricing"></a>Ceny

Brama translatora adresów sieciowych jest rozliczana przy użyciu dwóch oddzielnych liczników:

| Miernik | Stawka |
| --- | --- |
| Godziny zasobów | $0.045/godz. |
| Przetworzone dane | $0.045/GB |

Godziny zasobów dla czasu trwania, w którym znajduje się zasób bramy translatora adresów sieciowych.
Przetworzone dane kont dla całego ruchu przetworzonego przez zasób bramy translatora adresów sieciowych.

W publicznej wersji zapoznawczej Cennik ma rabat w wysokości 50%.

## <a name="support"></a>Pomoc techniczna

Translator adresów sieciowych jest obsługiwany za pomocą zwykłych kanałów pomocy technicznej.

## <a name="feedback"></a>Opinia

Chcemy wiedzieć, jak możemy ulepszyć usługę. Udostępnij nam swoją [opinię na temat publicznej wersji zapoznawczej](https://aka.ms/natfeedback) .  Możesz zaproponować i zagłosować, co będziemy kompilować dalej w usłudze [UserVoice dla translatora adresów sieciowych](https://aka.ms/natuservoice).

## <a name="limitations"></a>Ograniczenia

- Translator adresów sieciowych jest zgodny z publicznym adresem IP jednostki SKU, publicznym prefiksem adresu IP i zasobami modułu równoważenia obciążenia.   Zasoby podstawowe (na przykład podstawowa usługa równoważenia obciążenia) i wszelkie produkty pochodzące z nich nie są zgodne z translatorem adresów sieciowych.  Zasoby podstawowe muszą być umieszczone w podsieci, która nie jest skonfigurowana przy użyciu translatora adresów sieciowych.
- Rodzina adresów IPv4 jest obsługiwana.  Translator adresów sieciowych nie współdziała z rodziną adresów IPv6.
- Rejestrowanie przepływu sieciowej grupy zabezpieczeń nie jest obsługiwane w przypadku korzystania z translatora adresów sieciowych.
- Translator adresów sieciowych nie może obejmować wielu sieci wirtualnych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zasobach bramy translatora adresów sieciowych](./nat-gateway-resource.md).
- [Powiedz nam, co należy utworzyć w następnej kolejności w usłudze UserVoice](https://aka/natuservoice).
- [Prześlij opinię na temat publicznej wersji zapoznawczej](https://aka.ms/natfeedback).
