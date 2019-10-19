---
title: Opcje sieci Azure Functions
description: Przegląd wszystkich opcji sieciowych dostępnych w Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 967988d802a1b3d33ff50f578650e44794015583
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550864"
---
# <a name="azure-functions-networking-options"></a>Opcje sieci Azure Functions

W tym artykule opisano funkcje sieciowe dostępne w obszarze Opcje hostingu dla Azure Functions. Wszystkie poniższe opcje sieciowe zapewniają pewne możliwości uzyskiwania dostępu do zasobów bez używania adresów sieci internetowej lub ograniczają dostęp do Internetu do aplikacji funkcji. 

Modele hostingu mają różne poziomy izolacji sieci. Wybranie odpowiedniej opcji pomoże Ci spełnić wymagania dotyczące izolacji sieci.

Aplikacje funkcji można hostować na kilka sposobów:

* Istnieje zestaw opcji planu, które działają w infrastrukturze wielodostępnej, z różnymi poziomami łączności sieci wirtualnej i skalowania:
    * [Plan zużycia](functions-scale.md#consumption-plan), który jest skalowany dynamicznie w odpowiedzi na obciążenie i oferuje minimalne opcje izolacji sieci.
    * [Plan Premium](functions-scale.md#premium-plan), który również skaluje się dynamicznie, jednocześnie oferując bardziej kompleksową izolację sieci.
    * [Plan App Service](functions-scale.md#app-service-plan)platformy Azure, który działa ze stałą skalą i oferuje podobną izolację sieci do planu Premium.
* Funkcje można uruchamiać w [App Service Environment](../app-service/environment/intro.md). Ta metoda wdraża funkcję w sieci wirtualnej i oferuje pełną kontrolę sieci i izolację.

## <a name="matrix-of-networking-features"></a>Macierz funkcji sieciowych

|                |[Plan zużycia](functions-scale.md#consumption-plan)|[Plan Premium (wersja zapoznawcza)](functions-scale.md#premium-plan)|[Plan usługi App Service](functions-scale.md#app-service-plan)|[Środowisko usługi App Service](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Ograniczenia przychodzącego adresu IP & dostęp do lokacji prywatnej](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integracja sieci wirtualnej](#virtual-network-integration)|❌No|✅Yes (regionalny)|✅Yes (regionalne i bramy)|✅Yes|
|[Wyzwalacze sieci wirtualnej (bez protokołu HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[Połączenia hybrydowe](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[Ograniczenia wychodzącego adresu IP](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>Ograniczenia przychodzącego adresu IP

Za pomocą ograniczeń adresów IP można zdefiniować uporządkowaną według priorytetu listę adresów IP, które mają dostęp/odmowę dostępu do aplikacji. Lista może zawierać adresy IPv4 i IPv6. Gdy istnieje co najmniej jeden wpis, na końcu listy istnieje niejawne "odmowa wszystkich". Ograniczenia adresów IP działają ze wszystkimi opcjami hostingu funkcji.

> [!NOTE]
> W przypadku ograniczeń sieci można używać tylko edytora portalu z poziomu sieci wirtualnej lub listy dozwolonych adres IP komputera, na którym korzystasz, aby uzyskać dostęp do Azure Portal. Można jednak nadal uzyskiwać dostęp do dowolnych funkcji na karcie **funkcje platformy** z dowolnego komputera.

Aby dowiedzieć się więcej, zobacz [Azure App Service ograniczenia dostępu statycznego](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Dostęp do witryn prywatnych

Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. 
* Dostęp do lokacji prywatnej jest dostępny w wersji [Premium](./functions-premium-plan.md), [zużycie], (Functions. MD # zużycie-plan) i [App Service plan](functions-scale.md#app-service-plan) podczas konfigurowania **punktów końcowych usługi** . 
    * Punkty końcowe usługi można skonfigurować dla poszczególnych aplikacji w obszarze funkcje platformy > sieci > skonfigurować ograniczenia dostępu > Dodaj regułę. Sieci wirtualne można teraz wybrać jako "typ" reguły.
    * Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md)
        * Należy pamiętać, że dzięki punktom końcowym usługi funkcja nadal ma pełny dostęp wychodzący do Internetu, nawet z skonfigurowaną integracją sieci wirtualnej.
* Dostęp do lokacji prywatnej jest również dostępny dla App Service Environment skonfigurowany przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB). Aby uzyskać więcej informacji, zobacz [Tworzenie i używanie wewnętrznego modułu równoważenia obciążenia z App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integracja sieci wirtualnej

Integracja z siecią wirtualną umożliwia aplikacji funkcji dostęp do zasobów w sieci wirtualnej. Ta funkcja jest dostępna zarówno w planie Premium, jak i w planie App Service. Jeśli aplikacja znajduje się w App Service Environment, jest już w sieci wirtualnej i nie wymaga użycia integracji sieci wirtualnej w celu uzyskania dostępu do zasobów w tej samej sieci wirtualnej.

Integracja z siecią wirtualną umożliwia dostęp z aplikacji do baz danych i usług sieci Web działających w sieci wirtualnej. Dzięki integracji z siecią wirtualną nie trzeba ujawniać publicznego punktu końcowego dla aplikacji na maszynie wirtualnej. Zamiast tego możesz użyć prywatnych adresów z obsługą routingu bez Internetu.

Istnieją dwa formularze funkcji integracji sieci wirtualnej

1. Integracja regionalnej sieci wirtualnej umożliwia integrację z sieciami wirtualnymi w tym samym regionie. Ta forma funkcji wymaga podsieci w sieci wirtualnej w tym samym regionie. Ta funkcja jest nadal w wersji zapoznawczej, ale jest obsługiwana w przypadku obciążeń produkcyjnych aplikacji systemu Windows z pewnymi zastrzeżeniami wymienionymi poniżej.
2. Integracja sieci wirtualnej wymagana przez bramę umożliwia integrację z sieciami wirtualnymi w regionach zdalnych lub z klasycznymi sieciami wirtualnymi. Ta wersja funkcji wymaga wdrożenia bramy Virtual Network w sieci wirtualnej. Jest to funkcja oparta na sieci VPN typu punkt-lokacja i jest obsługiwana tylko w aplikacjach systemu Windows.

Aplikacja może jednocześnie korzystać z jednej formy funkcji integracji sieci wirtualnej. Następnie pytanie to funkcja, której należy użyć. Możesz użyć dowolnej dla wielu rzeczy. Jasne odróżniające są następujące:

| Problem  | Rozwiązanie | 
|----------|----------|
| Chcesz uzyskać dostęp do adresu RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) w tym samym regionie | Integracja z regionalną siecią wirtualną |
| Chcesz uzyskać dostęp do zasobów w klasycznej sieci wirtualnej lub sieci wirtualnej w innym regionie | Integracja sieci wirtualnej wymagana przez bramę |
| Chcesz uzyskać dostęp do punktów końcowych RFC 1918 w całym ExpressRoute | Integracja z regionalną siecią wirtualną |
| Chcesz uzyskać dostęp do zasobów między punktami końcowymi usługi | Integracja z regionalną siecią wirtualną |

Żadna funkcja nie pozwoli na dostęp do adresów innych niż RFC 1918 w ramach ExpressRoute. Aby to zrobić, musisz teraz użyć środowiska ASE.

Użycie integracji regionalnej sieci wirtualnej nie łączy sieć wirtualną z lokalnymi lub konfiguruje punkty końcowe usługi. Jest to osobna konfiguracja sieci. Integracja regionalnej sieci wirtualnej po prostu umożliwia aplikacji wykonywanie wywołań w ramach tych typów połączeń.

Niezależnie od używanej wersji integracja z siecią wirtualną zapewnia aplikacji funkcji dostęp do zasobów w sieci wirtualnej, ale nie udziela dostępu do aplikacji funkcji z sieci wirtualnej. Dostęp do lokacji prywatnej dotyczy udostępniania aplikacji tylko z sieci prywatnej, takiej jak z poziomu sieci wirtualnej platformy Azure. Integracja z siecią wirtualną dotyczy tylko wykonywania połączeń wychodzących z aplikacji do sieci wirtualnej. 

Funkcja integracji sieci wirtualnej:

* Wymaga planu App Service w warstwie Standardowa, Premium lub PremiumV2
* obsługuje protokoły TCP i UDP
* współpracuje z aplikacjami App Service i aplikacjami funkcji

Istnieje kilka rzeczy, które nie są obsługiwane przez integrację sieci wirtualnej, w tym:

* Instalowanie dysku
* Integracja usługi AD 
* NetBios

Integracja sieci wirtualnej w usłudze Functions używa udostępnionej infrastruktury z App Service Web Apps. Aby dowiedzieć się więcej o dwóch typach integracji z siecią wirtualną, zobacz:
* [Integracja z siecią wirtualną regionalny](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integracja sieci wirtualnej wymagana przez bramę](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Aby dowiedzieć się więcej o korzystaniu z integracji z siecią wirtualną, zobacz [Integrowanie aplikacji funkcji z siecią wirtualną platformy Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Łączenie z zabezpieczonymi zasobami punktu końcowego usługi

> [!note] 
> Czasowo nowe punkty końcowe usługi staną się dostępne w aplikacji funkcji dopiero po upływie 12 godzin. W tym czasie zasób będzie całkowicie niedostępny dla aplikacji.

Aby zapewnić wyższy poziom zabezpieczeń, można ograniczyć liczbę usług platformy Azure do sieci wirtualnej za pomocą punktów końcowych usługi. Następnie należy zintegrować aplikację funkcji z tą siecią wirtualną, aby uzyskać dostęp do zasobu. Ta konfiguracja jest obsługiwana we wszystkich planach, które obsługują integrację z siecią wirtualną.

[Więcej informacji na temat punktów końcowych usługi sieci wirtualnej można znaleźć tutaj.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Ograniczanie konta magazynu do sieci wirtualnej
Podczas tworzenia aplikacji funkcji należy utworzyć konto usługi Azure Storage ogólnego przeznaczenia lub połączyć się z nim, które obsługuje magazyn obiektów blob, kolejek i tabel. Nie jest obecnie możliwe używanie żadnych ograniczeń sieci wirtualnej na tym koncie. W przypadku skonfigurowania punktu końcowego usługi sieci wirtualnej na koncie magazynu używanym przez aplikację funkcji aplikacja zostanie przerwana.

[Przeczytaj więcej na temat wymagań dotyczących konta magazynu tutaj.](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>Wyzwalacze sieci wirtualnej (bez protokołu HTTP)

Obecnie aby można było korzystać z wyzwalaczy funkcji innych niż HTTP z sieci wirtualnej, należy uruchomić aplikację funkcji w planie App Service lub w App Service Environment.

Aby podać przykład, jeśli chcesz skonfigurować Azure Cosmos DB tak, aby akceptował tylko ruch z sieci wirtualnej, musisz wdrożyć aplikację funkcji w planie usługi App Service z integracją sieci wirtualnej z tą siecią wirtualną, aby skonfigurować wyzwalacze Azure Cosmos DB z tego zasobu. W wersji zapoznawczej skonfigurowanie integracji sieci wirtualnej nie zezwoli na wyzwolenie planu Premium w celu wyłączenia tego zasobu Azure Cosmos DB.

Zaznacz [tę listę dla wszystkich wyzwalaczy innych niż http](./functions-triggers-bindings.md#supported-bindings) , aby dokładnie sprawdzić, co jest obsługiwane.

## <a name="hybrid-connections"></a>Hybrydowe

[Połączenia hybrydowe](../service-bus-relay/relay-hybrid-connections-protocol.md) to funkcja Azure Relay, z której można korzystać w celu uzyskania dostępu do zasobów aplikacji w innych sieciach. Zapewnia dostęp z aplikacji do punktu końcowego aplikacji. Nie można używać go do uzyskiwania dostępu do aplikacji. Połączenia hybrydowe jest dostępny dla funkcji uruchomionych w [planie App Service](functions-scale.md#app-service-plan) i [App Service Environment](../app-service/environment/intro.md).

Jak w Azure Functions każde połączenie hybrydowe jest skorelowane z pojedynczym hostem TCP i kombinacją portów. Oznacza to, że punkt końcowy połączenia hybrydowego może znajdować się w dowolnym systemie operacyjnym i dowolnej aplikacji, o ile uzyskuje się dostęp do portu nasłuchiwania protokołu TCP. Funkcja Połączenia hybrydowe nie wie ani nie posługuje się z protokołem aplikacji lub dostępem do niego. Po prostu zapewnia dostęp do sieci.

Aby dowiedzieć się więcej, zobacz [dokumentację App Service dotyczącą połączenia hybrydowe](../app-service/app-service-hybrid-connections.md), która obsługuje funkcje w planie App Service.

## <a name="outbound-ip-restrictions"></a>Ograniczenia wychodzącego adresu IP

Ograniczenia wychodzącego adresu IP są dostępne tylko dla funkcji wdrożonych w App Service Environment. Można skonfigurować ograniczenia ruchu wychodzącego dla sieci wirtualnej, w której wdrożono App Service Environment.

W przypadku integrowania aplikacji funkcji w planie Premium lub App Service planie z siecią wirtualną aplikacja nadal będzie mogła nawiązywać połączenia wychodzące do Internetu.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sieci i Azure Functions: 

* [Postępuj zgodnie z samouczkiem dotyczącym rozpoczynania pracy z integracją z siecią wirtualną](./functions-create-vnet.md)
* [Przeczytaj informacje o usłudze Functions — często zadawane pytania](./functions-networking-faq.md)
* [Dowiedz się więcej o integracji z siecią wirtualną za pomocą App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Dowiedz się więcej o sieciach wirtualnych na platformie Azure](../virtual-network/virtual-networks-overview.md)
* [Włączanie większej liczby funkcji sieciowych i kontroli w środowiskach App Service](../app-service/environment/intro.md)
* [Nawiąż połączenie z lokalnymi zasobami bez zmian w zaporze przy użyciu Połączenia hybrydowe](../app-service/app-service-hybrid-connections.md)
