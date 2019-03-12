---
title: O usłudze VPN gateway dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o i konfigurowanie bramy sieci VPN, którego używasz z usługą Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: sethm
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 83d5215049976b67d22e29c2e4b75ec63a505b36
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776618"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>O usłudze VPN gateway dla usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Aby można było wysłać ruchu sieciowego między siecią wirtualną platformy Azure i lokacją lokalną, należy utworzyć bramę sieci wirtualnej dla sieci wirtualnej.

Brama sieci VPN jest typem bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy przez połączenie publiczne. Za pomocą bram sieci VPN do wysyłania ruchu bezpiecznie między sieci wirtualnej w usłudze Azure Stack i sieć wirtualną na platformie Azure. Można także wysyłać ruch bezpiecznie między siecią wirtualną i inną siecią, która jest połączona z urządzeniem sieci VPN.

Podczas tworzenia bramy sieci wirtualnej musisz wybrać typ bramy do utworzenia. Usługa Azure Stack obsługuje jeden typ bramy sieci wirtualnej: **Vpn** typu.

Każda sieć wirtualna może mieć tylko dwie bramy sieci wirtualnych, ale może istnieć tylko jedna brama danego typu. W zależności od wybranych ustawień można utworzyć wiele połączeń z jedną bramą sieci VPN. Przykładem jest Konfiguracja połączenia obejmującego wiele lokacji.

Przed utworzeniem i konfigurowanie bramy sieci VPN dla usługi Azure Stack, przejrzyj [zagadnienia dotyczące sieci usługi Azure Stack](azure-stack-network-differences.md) Aby dowiedzieć się, jak konfiguracji dla usługi Azure Stack różni się od platformy Azure.

>[!NOTE]
>Na platformie Azure przepustowość dla wybranej jednostki SKU bramy sieci VPN musi być podzielona we wszystkich połączeniach, które są podłączone do bramy. W usłudze Azure Stack, wartość przepustowości dla jednostki SKU bramy sieci VPN jest stosowany do każdego zasobu połączenia, która jest połączona z bramą.
>
> Na przykład:
> * Na platformie Azure podstawowej jednostki SKU bramy sieci VPN może obsłużyć około 100 MB/s agregowaną przepływność. Jeśli utworzysz dwa połączenia tej bramy sieci VPN i jedno połączenie używa 50 MB/s przepustowości, następnie 50 MB/s jest dostępna dla innych połączeń.
> * W usłudze Azure Stack *każdego* połączenie z podstawowej jednostki SKU bramy sieci VPN, przydzielany 100 MB/s przepływności.

## <a name="configuring-a-vpn-gateway"></a>Konfigurowanie bramy VPN gateway

Połączenie bramy sieci VPN opiera się na kilka zasobów, które są skonfigurowane przy użyciu konkretnych ustawień. Większość tych zasobów, które mogą być konfigurowane niezależnie, ale w niektórych przypadkach muszą być skonfigurowane w określonej kolejności.

### <a name="settings"></a>Ustawienia

Ustawienia wybrane dla każdego zasobu są krytyczne znaczenie dla utworzenia prawidłowego połączenia.

Aby uzyskać informacje o poszczególnych zasobów i ustawień dla bramy sieci VPN, zobacz [ustawień bramy sieci VPN — informacje dla usługi Azure Stack](azure-stack-vpn-gateway-settings.md). Ten artykuł pomoże Ci zrozumieć:

* Typy bram, typów sieci VPN i typy połączeń.
* Podsieci bram, bram sieci lokalnych i innych ustawień zasobów, które warto wziąć pod uwagę.

### <a name="deployment-tools"></a>Narzędzia wdrażania

Można tworzyć i konfigurować zasoby za pomocą jednego narzędzia konfiguracji, takich jak witryny Azure portal. Później może przełączyć się do innego narzędzia, takiego jak program PowerShell w celu skonfigurowania dodatkowych zasobów lub zmodyfikowania istniejących zasobów, jeśli ma to zastosowanie. Obecnie nie można skonfigurować wszystkie zasoby i ustawienia zasobów w witrynie Azure portal. Instrukcje w artykułach dotyczących poszczególnych topologii połączeń określają, kiedy potrzebne jest konkretne narzędzie konfiguracji.

## <a name="connection-topology-diagrams"></a>Diagramy topologii połączeń

Istnieją różne konfiguracje połączeń bramy sieci VPN. Określić, która Konfiguracja najlepiej odpowiada Twoim wymaganiom. W poniższych sekcjach możesz wyświetlić informacje i diagramy topologii dotyczące następujących połączeń bramy sieci VPN:

* Dostępny model wdrażania
* Dostępne narzędzia konfiguracji
* Linki prowadzące bezpośrednio do artykułu, jeśli jest dostępny

Diagramy i opisy w poniższych sekcjach ułatwią topologii połączenia dostosowanej do potrzeb użytkownika. Choć te diagramy przedstawiają podstawowe topologie, ale jest możliwe utworzenie bardziej złożonych konfiguracji z użyciem tych diagramów jako wskazówki.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Lokacja lokacja i połączenia obejmujące wiele lokacji (tunel VPN protokołu IPsec/IKE)

### <a name="site-to-site"></a>Lokacja-lokacja

A *site-to-site* (S2S) połączenia bramy sieci VPN to połączenie nawiązywane za pośrednictwem tunelu VPN IPsec/IKE (IKEv2). Ten typ połączenia wymaga urządzenia sieci VPN jest zlokalizowany w środowisku lokalnym, który jest przypisany publiczny adres IP. To urządzenie nie może znajdować się za translatorem adresów sieciowych. Z połączeń typu lokacja-lokacja (S2S) można korzystać w ramach konfiguracji hybrydowych i obejmujących wiele lokalizacji.

![Przykład konfiguracji połączenia sieci VPN typu lokacja lokacja](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Wiele lokacji

A *obejmujące wiele lokacji* połączenia jest odmianą połączenia lokacja lokacja. W tym przypadku tworzysz więcej niż jedno połączenie VPN z bramy sieci wirtualnej — zwykle do nawiązywania połączenia z wieloma lokacjami lokalnymi. Pracując z wieloma połączeniami należy użyć typu sieci VPN opartej na trasach (znanych jako bramy o dynamicznym podczas pracy z klasycznymi sieciami wirtualnymi). Ze względu na to, że każda sieć wirtualna może mieć tylko jedną bramę sieci VPN, wszystkie połączenia za pośrednictwem bramy współużytkują dostępną przepustowość.

![Przykład połączenia obejmującego wiele lokacji w usłudze Azure VPN Gateway](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Jednostki SKU bramy

Podczas tworzenia bramy sieci wirtualnej dla usługi Azure Stack można określić jednostkę SKU, którego chcesz używać bramy. Obsługiwane są następujące jednostki SKU bramy sieci VPN:

* Podstawowa
* Standardowa (Standard)
* Wysoka wydajność

Po wybraniu bramy wyższej jednostki SKU, np. Standard podstawowa lub o wysokiej wydajności za pośrednictwem Standard lub Basic więcej procesorów CPU oraz przepustowość sieci są przydzielane do bramy. W rezultacie Brama może obsługiwać wyższą przepływność sieci do sieci wirtualnej.

Usługa Azure Stack nie obsługuje największa wydajność jednostki SKU bramy, który jest używany wyłącznie z usługą Express Route.

Po wybraniu jednostki SKU należy wziąć pod uwagę następujące czynności:

* Usługa Azure Stack obsługuje tylko bramy oparte na zasadach.
* Protokół BGP (Border Gateway) nie jest obsługiwana w ramach podstawowej jednostki SKU.
* Konfiguracje współistniejących bramy sieci VPN usługi ExpressRoute nie są obsługiwane w usłudze Azure Stack.
* Połączenia bramy sieci VPN S2S aktywne aktywne można skonfigurować na jednostki SKU wysokiej wydajności tylko.

## <a name="estimated-aggregate-throughput-by-sku"></a>Szacowana agregowana przepływność według jednostki SKU

W poniższej tabeli przedstawiono typy bram i szacowaną agregowaną przepływność według jednostki SKU bramy:

|   | Przepływność bramy sieci VPN *(1)* | Tuneli protokołu IPsec max bramy sieci VPN *(2)* |
|-------|-------|-------|
|**Podstawowa jednostka SKU** ***(3)***    | 100 Mb/s  | 20    |
|**Standardowa jednostka SKU**       | 100 Mb/s  | 20    |
|**Jednostka SKU wysokiej wydajności** | 200 Mb/s    | 10    |

**Uwagi dotyczące tabeli:**

*Przypis 1* -przepływność sieci VPN nie jest to gwarantowana przepływność dla połączeń obejmujących wiele lokalizacji w Internecie. Jest to maksymalna możliwa przepustowość.  
*Uwaga (2)* — maksymalna liczba tuneli to łączny na wdrożenie usługi Azure Stack dla wszystkich subskrypcji.  
*Należy pamiętać, (3)* -routing protokołu BGP nie jest obsługiwane dla podstawowych jednostek SKU.

>[!NOTE]
>Można utworzyć tylko jedno połączenie sieci VPN typu lokacja lokacja między dwoma wdrożeniami w usłudze Azure Stack. Jest to spowodowane ograniczenia na platformie, która pozwala tylko jedno połączenie sieci VPN do tego samego adresu IP. Ponieważ brama wielodostępna używa jednego publicznego adresu IP dla wszystkich bram sieci VPN w systemie Azure Stack, korzysta z usługi Azure Stack może istnieć tylko jedno połączenie sieci VPN między dwoma systemami usługi Azure Stack. Powyższe ograniczenie ma zastosowanie także do łączenia z więcej niż jedno połączenie sieci VPN typu lokacja lokacja do dowolnej bramy sieci VPN, który używa pojedynczego adresu IP. Usługa Azure Stack nie zezwala na więcej niż jednego zasobu bramy sieci lokalnej ma zostać utworzony przy użyciu tego samego adresu IP.

## <a name="next-steps"></a>Kolejne kroki

[Ustawienia konfiguracji bramy sieci VPN dla usługi Azure Stack](azure-stack-vpn-gateway-settings.md)
