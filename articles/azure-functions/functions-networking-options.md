---
title: Opcje sieci usługi Azure Functions
description: Omówienie wszystkich opcji sieciowych dostępnych w usłudze Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: a4ae2d8bad50a4103da6afaa0bee5cbb75c877aa
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545509"
---
# <a name="azure-functions-networking-options"></a>Usługa Azure Functions opcji sieciowych

W tym dokumencie opisano zestaw funkcji sieciowych, które są dostępne w usłudze Azure Functions opcji hostingu. Wszystkie z następujących opcji sieciowych zapewniają możliwość uzyskiwać dostęp do zasobów, bez korzystania z obsługą routingu adresów internetowych lub ograniczyć dostęp do Internetu z aplikacją funkcji. Modelach hostingu wszystkie mają różne poziomy izolacji sieci, które są dostępne, a następnie wybierając właściwą umożliwi wymagań izolacji sieci.

Funkcja aplikacji może być hostowana na kilka różnych sposobów.

* Istnieje zestaw opcji plan, które są uruchamiane w wielodostępnej infrastrukturze, z różnymi poziomami połączenie między SIECIAMI i opcje skalowania.
    1. Plan zużycia, która skaluje się dynamicznie w odpowiedzi na obciążenia i udostępnia opcje izolacji sieci minimalnej.
    1. Premium Plan, który również skaluje się dynamicznie, jednocześnie oferując bardziej kompleksowe izolacji sieci.
    1. Plan usługi App Service, który działa na skalę stały i oferuje podobne izolacji sieciowej plan w warstwie Premium.
* Funkcje można również uruchomić na App Service Environment (ASE) służy do wdrażania funkcji do sieci wirtualnej, która zapewnia kontrolę całej sieci i izolacji.

## <a name="networking-feature-matrix"></a>Tabela funkcji sieci

|                |[Plan zużycia](functions-scale.md#consumption-plan)|⚠ [Plan w warstwie Premium](functions-scale.md##premium-plan-public-preview)|[Plan usługi App Service](functions-scale.md#app-service-plan)|[Środowisko usługi App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Ograniczenia adresów IP dla ruchu przychodzącego**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**Integracja z siecią Wirtualną**](#vnet-integration)|❌No|❌No|✅Yes|✅Yes|
|[**Integracja z siecią Wirtualną (wersja zapoznawcza) (Expressroute i punktów końcowych usługi)**](#preview-vnet-integration)|❌No|⚠Tak|⚠Tak|✅Yes|
|[**Połączenia hybrydowe**](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[**Dostęp do witryn prywatne**](#private-site-access)|❌No| ❌No|❌No|✅Yes|

⚠ Funkcja w wersji zapoznawczej, nie do użytku produkcyjnego

## <a name="inbound-ip-restrictions"></a>Ograniczenia adresów IP dla ruchu przychodzącego

Ograniczenia adresów IP umożliwiają definiowanie priorytetu, uporządkowane zezwalania/niezezwalania na listę adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może zawierać adresów IPv4 i IPv6. W przypadku co najmniej jeden wpis istnieje, a następnie niejawna odmowa, wszystkie, znajdującą się na końcu listy. Funkcja ograniczenia adresów IP współpracuje ze wszystkich funkcji opcji hostingu.

> [!NOTE]
> Aby można było używać edytora portalu Azure, portalu muszą mieć możliwość uzyskania bezpośredniego dostępu do uruchomionej aplikacji funkcji, a urządzenie, używanym do korzystania z portalu musi mieć jego listy dozwolonych adresów IP. Bez ograniczeń sieci w miejscu, możesz uzyskiwać dostęp wszystkie funkcje w **funkcje platformy** kartę.

[Dowiedz się więcej tutaj](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>Integracja z siecią wirtualną

Integracja sieci Wirtualnej umożliwia aplikację funkcji, aby uzyskać dostęp do zasobów w sieci Wirtualnej. Integracja z siecią Wirtualną jest dostępna w plan w warstwie Premium i plan usługi App Service. Jeśli aplikacja znajduje się w środowisku usługi App Service jest już w sieci wirtualnej, a nie wymaga użycia funkcji integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej.

Integracja z siecią wirtualną daje aplikacji funkcji dostęp do zasobów w sieci wirtualnej, ale nie są przyznawane [dostęp do prywatnej witryny](#private-site-access) do aplikacji funkcji z sieci wirtualnej.

Integracja z siecią wirtualną jest często używane do włączenia dostęp do bazy danych z aplikacji i usług działających w sieci web. Integracja z siecią wirtualną nie wymagają do udostępnienia publicznego punktu końcowego dla aplikacji na maszynie Wirtualnej, ale można zamiast tego użyj prywatnych adresów Routing-internet.

Ogólnie dostępnej wersji Integracja z siecią Wirtualną opiera się na bramę sieci VPN, aby połączyć aplikacje funkcji do sieci wirtualnej. Jest ona dostępna w funkcjach hostowana w planie usługi app service. Aby dowiedzieć się, jak skonfigurować tę funkcję, zobacz [dokument usługi App Service na potrzeby tej samej funkcji](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Integracja z siecią Wirtualną (wersja zapoznawcza)

Istnieje nowa wersja funkcji integracji sieci wirtualnej, która jest dostępna w wersji zapoznawczej. Go nie są zależne od sieci VPN typu punkt lokacja i ExpressRoute lub punktów końcowych usługi obsługuje również dostęp do zasobów. Ta funkcja jest dostępna w ramach planu Premium, a w planach usługi App Service, skalowane do PremiumV2.

Integracja z siecią wirtualną, która jest obecnie dostępna w wersji zapoznawczej, nowa wersja ma następujące zalety:

* Brak bramy jest wymagana do używania nowej funkcji integracji sieci wirtualnej
* Bez żadnych dodatkowych konfiguracji oprócz integracji z usługą ExpressRoute połączona sieć wirtualna może dostęp do zasobów połączeń usługi ExpressRoute.
* Można korzystać z zasobów z działających funkcji zabezpieczonych punktu końcowego usługi. Aby to zrobić, należy włączyć punkty końcowe usługi w podsieci użyć do integracji sieci wirtualnej.
* Nie można skonfigurować wyzwalaczy, aby używać zasobów przy użyciu nowej funkcji integracji sieci wirtualnej zabezpieczonych punktu końcowego usługi. 
* Zarówno w aplikacji funkcji, jak i w sieci wirtualnej musi być w tym samym regionie.
* Nowa funkcja wymaga nieużywanej podsieci w sieci wirtualnej usługi Resource Manager.
* Obciążenia produkcyjne nie są obsługiwane na nową wersję Integracja z siecią wirtualną, gdy znajduje się w wersji zapoznawczej.
* Tabele tras i globalnej komunikacji równorzędnej nie są jeszcze dostępne w nowej integracji sieci wirtualnej.
* Jeden adres jest używany dla każdego potencjalnego wystąpienia aplikacji funkcji. Ponieważ rozmiar podsieci nie można zmienić po przypisaniu, należy użyć podsieci, która może łatwo obsługiwać rozmiaru skali maksymalnej. Na przykład, aby zapewnić obsługę plan w warstwie Premium, które mogą być skalowane do wystąpień 80, firma Microsoft zaleca `/25` podsieci, która udostępnia adresy 126 hosta.

Aby dowiedzieć się więcej na temat korzystania z wersji zapoznawczej Integracja sieci Wirtualnej, zobacz [integracji aplikacji funkcji przy użyciu usługi Azure Virtual Network](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Połączenia hybrydowe

[Połączenia hybrydowe](../service-bus-relay/relay-hybrid-connections-protocol.md) to funkcja usługi Azure Relay, którego można uzyskać dostęp do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie można uzyskać dostęp do Twojej aplikacji. Połączenia hybrydowe to dostępna do uruchamiania w funkcji [planu usługi App Service](functions-scale.md#app-service-plan) i [środowiska App Service Environment](../app-service/environment/intro.md).

W przypadku użycia w funkcje, każde połączenie hybrydowe skorelowany jednej kombinacji hosta i portu TCP. Oznacza to, że punkt końcowy połączenia hybrydowego może być w dowolnym systemie operacyjnym i każdej aplikacji, możesz podać uzyskują dostęp do portu nasłuchiwania protokołu TCP. Funkcja połączeń hybrydowych nie wiedzieć, ani nie obchodzi, jest protokół aplikacji lub uzyskujesz dostęp. Jest on po prostu zapewnienie dostępu do sieci.

Aby dowiedzieć się więcej, zobacz [dokumentacja usługi App Service dla połączeń hybrydowych](../app-service/app-service-hybrid-connections.md), który obsługuje zarówno funkcje, jak i aplikacji sieci Web.

## <a name="private-site-access"></a>Dostęp do witryn prywatnych

Dostęp do prywatnej witryny dotyczy tylko udostępnianie aplikacji z sieci prywatnej takich jak z w obrębie sieci wirtualnej platformy Azure. Dostęp do prywatnej witryny jest dostępna tylko dla środowiska ASE skonfigurowane za pomocą wewnętrznego obciążenia równoważenia (ILB). Aby uzyskać więcej informacji na temat korzystania z ASE z wewnętrznym modułem równoważenia obciążenia, zobacz [tworzenie i używanie środowisko ASE z wewnętrznym modułem równoważenia obciążenia](../app-service/environment/create-ilb-ase.md).

Istnieje wiele sposobów dostępu do zasobów sieci Wirtualnej w inne opcje hostingu, ale środowisko ASE jest jedynym sposobem, aby umożliwić wyzwalaczy, funkcji uzyskanie odbywa się za pośrednictwem sieci Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sieci i funkcje: 

* [Postępuj zgodnie z samouczkiem ułatwiającym integrację sieci Wirtualnej rozpoczęcie pracy](./functions-create-vnet.md)
* [Przeczytaj funkcje, w tym miejscu sieć — często zadawane pytania](./functions-networking-faq.md)
* [Dowiedz się więcej na temat integracji sieci Wirtualnej przy użyciu usługi App Service / funkcje w tym miejscu](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej na temat sieci wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączyć więcej sieciowych funkcji i kontrola przy użyciu środowisk usługi App Service](../app-service/environment/intro.md)
* [Łączenie z poszczególnych zasobów lokalnych bez zmian w zaporze przy użyciu połączeń hybrydowych](../app-service/app-service-hybrid-connections.md)
