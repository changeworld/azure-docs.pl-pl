---
title: Usługa Azure Functions opcji sieciowych
description: Omówienie wszystkich opcji sieciowych dostępnych w usłudze Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: f13e498859986d5ee697cbd67907fd344147ed0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492836"
---
# <a name="azure-functions-networking-options"></a>Usługa Azure Functions opcji sieciowych

W tym artykule opisano funkcje sieciowe dostępne w opcji hostingu dla usługi Azure Functions. Wszystkie z następujących opcji sieciowych zapewniają możliwość uzyskiwać dostęp do zasobów, bez korzystania z obsługą routingu adresów internetowych lub ograniczyć dostęp do Internetu z aplikacją funkcji. 

Modelach hostingu mieć różne poziomy izolacji sieci, które są dostępne. Wybieranie odpowiedniego pomoże Ci spełnić wymagań izolacji sieci.

Możesz hostować aplikacje funkcji na kilka sposobów:

* Istnieje zestaw opcji plan, które są uruchamiane w wielodostępnej infrastrukturze, z różnymi poziomami sieciami wirtualnymi i opcje skalowania:
    * [Planu zużycie](functions-scale.md#consumption-plan), która skaluje się dynamicznie w odpowiedzi na obciążenia i oferuje opcje izolacji sieci minimalnej.
    * [Plan w warstwie Premium](functions-scale.md#premium-plan-public-preview), które również skaluje się dynamicznie, jednocześnie oferując bardziej kompleksowe izolacji sieci.
    * Azure [planu usługi App Service](functions-scale.md#app-service-plan), który działa stały skalę i oferuje podobne izolacji sieciowej plan w warstwie Premium.
* Możesz uruchamiać funkcje w [środowiska App Service Environment](../app-service/environment/intro.md). Ta metoda służy do wdrażania funkcji do sieci wirtualnej i zapewnia kontrolę całej sieci i izolacji.

## <a name="matrix-of-networking-features"></a>Macierz funkcji sieciowych

|                |[Plan zużycia](functions-scale.md#consumption-plan)|[Plan w warstwie Premium (wersja zapoznawcza)](functions-scale.md#premium-plan-public-preview)|[Plan usługi App Service](functions-scale.md#app-service-plan)|[Środowisko usługi App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Ograniczenia adresów IP dla ruchu przychodzącego](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Ograniczenia adresów IP ruchu wychodzącego](#private-site-access)|❌No| ❌No|❌No|✅Yes|
|[Integracja sieci wirtualnej](#virtual-network-integration)|❌No|❌No|✅Yes|✅Yes|
|[Integracja sieci wirtualnej (usługi Azure ExpressRoute i ruchu wychodzącego punktów końcowych usługi) w wersji zapoznawczej](#preview-version-of-virtual-network-integration)|❌No|✅Yes|✅Yes|✅Yes|
|[Połączenia hybrydowe](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[dostęp do prywatnej witryny](#private-site-access)|❌No| ✅Yes|✅Yes|✅Yes|

## <a name="inbound-ip-restrictions"></a>Ograniczenia adresów IP dla ruchu przychodzącego

Ograniczenia adresów IP umożliwia definiowanie listy uporządkowane priorytet adresów IP, które są dozwolony/odmowa dostępu do aplikacji. Lista może zawierać adresów IPv4 i IPv6. Po co najmniej jeden wpis ukrytego "Odmów wszystkim" występuje na końcu listy. Ograniczenia adresów IP pracować wszystkie opcje hostingu funkcji.

> [!NOTE]
> Aby użyć edytorze portalu Azure, portalu należy bezpośrednio uzyskać dostępu do uruchomionej aplikacji funkcji. Ponadto urządzenie, używanym do korzystania z portalu, musi mieć jego listy dozwolonych adresów IP. Bez ograniczeń sieci w miejscu, możesz uzyskiwać dostęp wszystkie funkcje na **funkcje platformy** kartę.

Aby dowiedzieć się więcej, zobacz [ograniczenia dostępu statycznych w usłudze Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="outbound-ip-restrictions"></a>Ograniczenia adresów IP ruchu wychodzącego

Ograniczenia ruchu wychodzącego IP są dostępne tylko dla funkcji wdrożony w środowisku usługi App Service. Można skonfigurować ograniczenia ruchu wychodzącego dla sieci wirtualnej, w której wdrożono środowisko App Service Environment.

## <a name="virtual-network-integration"></a>Integracja sieci wirtualnej

Integracja sieci wirtualnej umożliwia aplikację funkcji, aby uzyskać dostęp do zasobów w sieci wirtualnej. Ta funkcja jest dostępna zarówno w przypadku plan w warstwie Premium, jak i plan usługi App Service. Jeśli aplikacja znajduje się w środowisku usługi App Service, jest już w sieci wirtualnej i nie wymaga użycia integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej.

Integracja sieci wirtualnej zapewnia aplikacji funkcji dostęp do zasobów w sieci wirtualnej, ale nie są przyznawane [dostęp do prywatnej witryny](#private-site-access) do aplikacji funkcji z sieci wirtualnej.

Integracja sieci wirtualnej można użyć, aby umożliwić dostęp z aplikacji do bazy danych i usług sieci web działających w sieci wirtualnej. Integracja sieci wirtualnej nie wymagają do udostępnienia publicznego punktu końcowego dla aplikacji na maszynie Wirtualnej. Zamiast tego można użyć prywatnych, -internet Routing adresów.

Ogólnie dostępnej wersji Integracja sieci wirtualnej zależy od bramy sieci VPN, aby połączyć aplikacje funkcji do sieci wirtualnej. Jest ona dostępna w funkcjach hostowanych w ramach planu usługi App Service. Aby dowiedzieć się, jak skonfigurować tę funkcję, zobacz [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md).

### <a name="preview-version-of-virtual-network-integration"></a>Integracja sieci wirtualnej w wersji zapoznawczej

Nowa wersja funkcji integracji sieci wirtualnej jest w wersji zapoznawczej. Go nie są zależne od sieci VPN typu punkt lokacja. Obsługuje ona dostęp do zasobów dla usługi ExpressRoute lub punkty końcowe usługi. Jest ona dostępna w plan w warstwie Premium i planów usługi App Service, skalowane do PremiumV2.

Poniżej przedstawiono niektóre właściwości tej wersji:

* Nie potrzebujesz bramy z niego korzystać.
* Zasoby są dostępne między połączeniami usługi ExpressRoute bez żadnych dodatkowych zmian konfiguracji poza zintegrowaniem z siecią wirtualną połączoną z usługą ExpressRoute.
* Mogą wykorzystywać zabezpieczonych punktu końcowego usługi zasobów, od działających funkcji. Aby to zrobić, należy włączyć punkty końcowe usługi w podsieci użyć do integracji sieci wirtualnej.
* Nie można skonfigurować wyzwalaczy, aby korzystać z zasobów zabezpieczonych punktu końcowego usługi. 
* Zarówno aplikacja funkcji, jak i sieć wirtualna musi być w tym samym regionie.
* Nowa funkcja wymaga nieużywanej podsieci w sieci wirtualnej, która została wdrożona za pomocą usługi Azure Resource Manager.
* Obciążenia produkcyjne są nieobsługiwane w przypadku, gdy ta funkcja jest dostępna w wersji zapoznawczej.
* Tabele tras i globalnej komunikacji równorzędnej nie są jeszcze dostępne za pomocą funkcji.
* Jeden adres jest używany dla każdego potencjalnego wystąpienia aplikacji funkcji. Ponieważ rozmiar podsieci nie można zmienić po przypisaniu, należy użyć podsieci, która może łatwo obsługiwać rozmiaru skali maksymalnej. Na przykład, aby zapewnić obsługę plan w warstwie Premium, które mogą być skalowane do wystąpień 80, firma Microsoft zaleca `/25` podsieci, która udostępnia adresy 126 hosta.

Aby dowiedzieć się więcej na temat korzystania z wersji zapoznawczej Integracja sieci wirtualnej, zobacz [zintegrować aplikację funkcji z siecią wirtualną platformy Azure](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Połączenia hybrydowe

[Połączenia hybrydowe](../service-bus-relay/relay-hybrid-connections-protocol.md) to funkcja usługi Azure Relay, która umożliwia dostęp do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie można go użyć, uzyskiwanie dostępu do aplikacji. Połączenia hybrydowe to dostępna do uruchamiania w funkcji [planu usługi App Service](functions-scale.md#app-service-plan) i [środowiska App Service Environment](../app-service/environment/intro.md).

W przypadku użycia w usłudze Azure Functions, każde połączenie hybrydowe skorelowany jednej kombinacji hosta i portu TCP. Oznacza to, że punkt końcowy połączenia hybrydowego może być w dowolnym systemie operacyjnym i każdej aplikacji, tak długo, jak uzyskujesz dostęp do portu nasłuchiwania protokołu TCP. Funkcja połączeń hybrydowych nie wiedzieć, ani nie obchodzi, jest protokół aplikacji lub uzyskiwany jest dostęp. Po prostu zapewnia dostęp do sieci.

Aby dowiedzieć się więcej, zobacz [dokumentacja usługi App Service dla połączeń hybrydowych](../app-service/app-service-hybrid-connections.md), który obsługuje funkcje w ramach planu usługi App Service.

## <a name="private-site-access"></a>Dostęp do witryn prywatnych

Dostęp do prywatnej witryny odnosi się do wprowadzania aplikacji jest dostępny tylko w sieci prywatnej takich jak z w obrębie sieci wirtualnej platformy Azure. 
* Dostęp do prywatnej witryny jest dostępna w wersji Premium i usługi App Service podczas planowania **punktów końcowych usługi** są skonfigurowane. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md)
    * Należy pamiętać, że z punktami końcowymi usługi, funkcji nadal ma pełne wychodzący dostęp do Internetu, nawet w przypadku skonfigurowano integrację z siecią Wirtualną.
* Dostęp do prywatnej witryny jest dostępna tylko w środowisku usługi App Service skonfigurowana przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB). Aby uzyskać więcej informacji, zobacz [tworzenia i używania wewnętrznego modułu równoważenia obciążenia w środowisku usługi App Service](../app-service/environment/create-ilb-ase.md).

Istnieje wiele sposobów dostępu do zasobów sieci wirtualnej w inne opcje hostingu. Ale w środowisku usługi App Service jest jedynym sposobem, aby umożliwić wyzwalaczy, funkcji uzyskanie odbywa się za pośrednictwem sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat funkcji platformy Azure i siecią: 

* [Postępuj zgodnie z samouczkiem o wprowadzenie do Integracja sieci wirtualnej](./functions-create-vnet.md)
* [Przeczytaj funkcje sieć — często zadawane pytania](./functions-networking-faq.md)
* [Dowiedz się więcej na temat integracji sieci wirtualnej za pomocą aplikacji/funkcje usługi](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej na temat sieci wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączyć więcej sieciowych funkcji i kontrola przy użyciu środowisk usługi App Service](../app-service/environment/intro.md)
* [Łączenie z poszczególnych zasobów lokalnych bez zmian w zaporze za pomocą połączeń hybrydowych](../app-service/app-service-hybrid-connections.md)
