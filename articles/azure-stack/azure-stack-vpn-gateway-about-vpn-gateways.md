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
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: 860f1181a84ef87fe0e66c2746b76b800a326354
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496195"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>O usłudze VPN gateway dla usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Aby można było wysłać ruchu sieciowego między siecią wirtualną platformy Azure i lokacją lokalną, należy utworzyć bramę sieci wirtualnej dla sieci wirtualnej.

Brama sieci VPN jest typem bramy sieci wirtualnej, który wysyła zaszyfrowany ruch sieciowy przez połączenie publiczne. Za pomocą bram sieci VPN do wysyłania ruchu bezpiecznie między sieci wirtualnej w usłudze Azure Stack i sieć wirtualną na platformie Azure. Można także wysyłać ruch bezpiecznie między siecią wirtualną i inną siecią, która jest połączona z urządzeniem sieci VPN.

Podczas tworzenia bramy sieci wirtualnej musisz wybrać typ bramy do utworzenia. Usługa Azure Stack obsługuje jeden typ bramy sieci wirtualnej **Vpn** typu.

Każda sieć wirtualna może mieć tylko dwie bramy sieci wirtualnych, ale może istnieć tylko jedna brama danego typu. W zależności od wybranych ustawień można utworzyć wiele połączeń z jedną bramą sieci VPN. Przykładem jest Konfiguracja połączenia obejmującego wiele lokacji.

Przed utworzeniem i konfigurowanie bramy sieci VPN dla usługi Azure Stack, przejrzyj [zagadnienia dotyczące sieci usługi Azure Stack](user/azure-stack-network-differences.md) Aby dowiedzieć się, jak konfiguracji dla usługi Azure Stack różni się od platformy Azure.

>[!NOTE]
>Na platformie Azure przepustowość dla bramy sieci VPN możesz wybrać jednostkę SKU musi być podzielona we wszystkich połączeniach, które są podłączone do bramy. Jednak w usłudze Azure Stack wartość przepustowości dla jednostki SKU bramy sieci VPN jest stosowany do każdego zasobu połączenia, która jest połączona z bramą.
>
> Na przykład:
> * Na platformie Azure z podstawowych jednostek SKU bramy sieci VPN może obsłużyć około 100 MB/s agregowaną przepływność. Jeśli utworzysz dwa połączenia tej bramy sieci VPN i jedno połączenie używa 50 MB/s przepustowości, następnie 50 MB/s jest dostępna dla innych połączeń.
> * W usłudze Azure Stack *każdego* połączenie z podstawowych jednostek SKU bramy sieci VPN, przydzielany 100 MB/s przepływności.

## <a name="configuring-a-vpn-gateway"></a>Konfigurowanie bramy sieci VPN

Połączenie bramy sieci VPN opiera się na kilka zasobów, które są skonfigurowane przy użyciu konkretnych ustawień. Większość tych zasobów, które mogą być konfigurowane niezależnie, ale w niektórych przypadkach muszą być skonfigurowane w określonej kolejności.

### <a name="settings"></a>Ustawienia

Ustawienia wybrane dla każdego zasobu są krytyczne znaczenie dla utworzenia prawidłowego połączenia.

Aby uzyskać informacje o poszczególnych zasobów i ustawień dla bramy sieci VPN, zobacz [ustawień bramy sieci VPN — informacje dla usługi Azure Stack](azure-stack-vpn-gateway-settings.md). Ten artykuł pomoże Ci zrozumieć:

* Typy bram, typów sieci VPN i typy połączeń.
* Podsieci bram, bram sieci lokalnych i innych ustawień zasobów, które warto wziąć pod uwagę.

### <a name="deployment-tools"></a>Narzędzia wdrażania

Można tworzyć i konfigurować zasoby za pomocą jednego narzędzia konfiguracji, takich jak witryny Azure portal. Później może przełączyć się do innego narzędzia, takie jak program PowerShell w celu skonfigurowania dodatkowych zasobów lub zmodyfikowania istniejących zasobów, jeśli ma to zastosowanie. Obecnie nie wszystkie zasoby i ustawienia zasobów można skonfigurować w witrynie Azure Portal. Instrukcje w artykułach dotyczących poszczególnych topologii połączeń określają, kiedy potrzebne jest konkretne narzędzie konfiguracji.

## <a name="connection-topology-diagrams"></a>Diagramy topologii połączeń

Dostępne są różne konfiguracje połączeń bramy sieci VPN. Określić, która Konfiguracja najlepiej odpowiada Twoim wymaganiom. W poniższych sekcjach możesz wyświetlić informacje i diagramy topologii dotyczące następujących połączeń bramy sieci VPN:

* Dostępny model wdrażania
* Dostępne narzędzia konfiguracji
* Linki prowadzące bezpośrednio do artykułu, jeśli jest dostępny

Diagramy i opisy w poniższych sekcjach ułatwią topologii połączenia dostosowanej do potrzeb użytkownika. Choć te diagramy przedstawiają podstawowe topologie, ale jest możliwe utworzenie bardziej złożonych konfiguracji z użyciem tych diagramów jako wskazówki.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Połączenia typu lokacja-lokacja i połączenia obejmujące wiele lokacji (tunel VPN protokołu IPsec/IKE)

### <a name="site-to-site"></a>Lokacja-lokacja

Połączenie bramy sieci VPN typu lokacja-lokacja to połączenie nawiązywane za pośrednictwem tunelu sieci VPN wykorzystującego protokół IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga urządzenia sieci VPN, który znajduje się w środowisku lokalnym i ma przypisany publiczny adres IP. To urządzenie nie może znajdować się za translatorem adresów sieciowych. Z połączeń typu lokacja-lokacja (S2S) można korzystać w ramach konfiguracji hybrydowych i obejmujących wiele lokalizacji.

![Przykład konfiguracji połączenia sieci VPN typu lokacja lokacja](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Obejmujące wiele lokacji

Ten typ połączenia jest odmianą połączenia typu lokacja-lokacja. W tym przypadku tworzysz więcej niż jedno połączenie VPN z bramy sieci wirtualnej — zwykle do nawiązywania połączenia z wieloma lokacjami lokalnymi. Pracując z wieloma połączeniami należy użyć typu sieci VPN typu RouteBased (znanych jako bramy o dynamicznym podczas pracy z klasycznymi sieciami wirtualnymi.) Ze względu na to, że każda sieć wirtualna może mieć tylko jedną bramę sieci VPN, wszystkie połączenia za pośrednictwem bramy współużytkują dostępną przepustowość. Ten typ konfiguracji jest często określany mianem połączenia „obejmującego wiele lokacji”.

![Przykład połączenia obejmującego wiele lokacji w usłudze Azure VPN Gateway](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Jednostki SKU bramy

Podczas tworzenia bramy sieci wirtualnej dla usługi Azure Stack można określić jednostkę SKU, którego chcesz używać bramy. Obsługiwane są następujące jednostki SKU bramy sieci VPN:

* Podstawowa
* Standardowa (Standard)
* Wysoka wydajność (HighPerformance)

Po wybraniu bramy wyższej jednostki SKU, takich jak standardowy podstawowa lub HighPerformance za pośrednictwem Standard lub Basic więcej procesorów CPU oraz przepustowość sieci są przydzielane do bramy. W rezultacie Brama może obsługiwać wyższą przepływność sieci do sieci wirtualnej.

Usługa Azure Stack nie obsługuje jednostki SKU bramy UltraPerformance, który jest używany wyłącznie z usługą Express Route.

Po wybraniu jednostki SKU należy wziąć pod uwagę następujące czynności:

* Usługa Azure Stack obsługuje tylko bramy oparte na zasadach.
* Protokół BGP (Border Gateway) nie jest obsługiwana w ramach podstawowej jednostki SKU.
* Brama sieci VPN usługi ExpressRoute współistnieją konfiguracje nie są obsługiwane w usłudze Azure Stack.
* Połączenia usługi VPN Gateway typu lokacja do lokacji (S2S) aktywne-aktywne można skonfigurować tylko w ramach jednostki SKU o wysokiej wydajności.

## <a name="estimated-aggregate-throughput-by-sku"></a>Szacowana agregowana przepływność według jednostki SKU

W poniższej tabeli przedstawiono typy bram i szacowaną agregowaną przepływność według jednostek SKU bramy.

|   | Przepływność bramy sieci VPN *(1)* | Tuneli protokołu IPsec max bramy sieci VPN *(2)* |
|-------|-------|-------|
|**Podstawowa jednostka SKU** ***(3)***    | 100 Mb/s  | 10    |
|**Standardowa jednostka SKU**       | 100 Mb/s  | 10    |
|**Jednostka SKU wysokiej wydajności** | 200 Mb/s    | 5 |

**Uwagi dotyczące tabeli:**

*Przypis 1* -przepływność sieci VPN nie jest to gwarantowana przepływność dla połączeń obejmujących wiele lokalizacji w Internecie. To pomiar maksymalna możliwa przepustowość.  
*Uwaga (2)* — maksymalna liczba tuneli to łączny na wdrożenie usługi Azure Stack dla wszystkich subskrypcji.  
*Należy pamiętać, (3)* -routing protokołu BGP nie jest obsługiwana w ramach podstawowej jednostki SKU.

>[!NOTE]
>Można utworzyć tylko jedno połączenie sieci VPN typu lokacja-lokacja między dwoma wdrożeniami w usłudze Azure Stack.  Jest to spowodowane ograniczenia na platformie, która umożliwi tylko jedno połączenie sieci VPN do tego samego adresu IP.  Ponieważ usługi Azure Stack korzysta z wielodostępną bramą, która używa jednego publicznego adresu IP dla wszystkich bram sieci VPN w systemie Azure Stack, między dwoma systemami usługi Azure Stack może istnieć tylko jedno połączenie sieci VPN.  To ograniczenie dotyczy także nawiązywania połączenia z więcej niż jednego połączenia sieci VPN typu lokacja-lokacja wszystkie bramy sieci VPN, który używa pojedynczego adresu IP.  Usługa Azure Stack nie pozwoli na więcej niż jednego zasobu Brama sieci lokalnej ma zostać utworzony przy użyciu tego samego adresu IP.


## <a name="next-steps"></a>Kolejne kroki

[Ustawienia konfiguracji bramy sieci VPN dla usługi Azure Stack](azure-stack-vpn-gateway-settings.md)
