---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671522"
---
Użycie integracji regionalnej sieci wirtualnej umożliwia aplikacji dostęp do:

* zasoby w sieci wirtualnej w tym samym regionie, w którym integrujesz się z usługą 
* zasoby w sieci wirtualnych komunikacji równorzędnej z siecią wirtualną znajdującą się w tym samym regionie
* zabezpieczone usługi punktu końcowego usługi
* zasoby w ramach połączeń ExpressRoute
* zasoby w sieci wirtualnej, z którą nawiązano połączenie
* zasoby między połączeniami równorzędnymi, w tym połączenia ExpressRoute
* Prywatne punkty końcowe 

W przypadku korzystania z integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie można korzystać z następujących funkcji sieciowych platformy Azure:

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) — można zablokować ruch wychodzący z sieciową grupą zabezpieczeń umieszczoną w podsieci integracji. Reguły ruchu przychodzącego nie są stosowane, ponieważ nie można używać integracji sieci wirtualnej w celu zapewnienia dostępu przychodzącego do aplikacji.
* Tabele tras (UDR) — tabelę tras można umieścić w podsieci integracji w celu wysłania ruchu wychodzącego w dowolnym miejscu. 

Domyślnie aplikacja będzie kierować ruchem RFC1918 do sieci wirtualnej. Jeśli chcesz skierować cały ruch wychodzący do sieci wirtualnej, Zastosuj ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL do aplikacji. Aby skonfigurować ustawienie aplikacji:

1. Przejdź do interfejsu użytkownika konfiguracji w portalu aplikacji. Wybierz **nowe ustawienie aplikacji**
1. Wpisz **WEBSITE_VNET_ROUTE_ALL** w polu Nazwa i **1** w polu wartość

   ![Podaj ustawienie aplikacji][4]

1. Kliknij przycisk **OK**
1. Wybierz pozycję **Zapisz**.

Jeśli cały ruch wychodzący zostanie rozesłany do sieci wirtualnej, będzie on podlegać sieciowych grup zabezpieczeń i UDR, które są stosowane do podsieci integracji. Gdy przekierowujesz cały ruch wychodzący do sieci wirtualnej, adresy wychodzące nadal będą adresami wychodzącymi wymienionymi we właściwościach aplikacji, chyba że zostaną podane trasy do wysyłania ruchu w innym miejscu. 

Istnieją pewne ograniczenia dotyczące używania integracji sieci wirtualnej z usługą sieci wirtualnych w tym samym regionie:

* Nie można uzyskać dostępu do zasobów w ramach globalnych połączeń komunikacji równorzędnej
* Funkcja jest dostępna tylko w nowszych App Service jednostkach skalowania, które obsługują plany App Service PremiumV2.
* Podsieć integracji może być używana tylko przez jeden plan App Service
* Funkcja nie może być używana przez aplikacje planu izolowanego, które znajdują się w App Service Environment
* Ta funkcja wymaga nieużywanej podsieci, która jest/27 z co najmniej 32 adresów w sieci wirtualnej Menedżer zasobów
* Aplikacja i Sieć wirtualna muszą znajdować się w tym samym regionie
* Nie można usunąć sieci wirtualnej przy użyciu zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej. 
* Integrację z usługą sieci wirtualnych można zintegrować tylko w ramach tej samej subskrypcji, w której znajduje się aplikacja
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na App Service plan. Wiele aplikacji w tym samym planie App Service może korzystać z tej samej sieci wirtualnej. 
* Nie można zmienić subskrypcji aplikacji lub planu, gdy istnieje aplikacja, która korzysta z integracji regionalnej sieci wirtualnej.

Jeden adres jest używany dla każdego wystąpienia planu. W przypadku skalowania aplikacji do pięciu wystąpień są używane pięć adresów. Ponieważ nie można zmienić rozmiaru podsieci po przypisaniu, należy użyć podsieci, która jest wystarczająco duża, aby można było dowolnie skalować dostęp do aplikacji. Zalecany rozmiar to/26 z 64 adresami. Wartość A/26 z 64mi adresów będzie uwzględniać plan Premium z 30 wystąpieniami. W przypadku skalowania planu w górę lub w dół wystarczy kilka adresów w krótkim czasie. 

Jeśli chcesz, aby aplikacje w innym planie miały dostęp do sieci wirtualnej, która jest już połączona z aplikacjami w innym planie, należy wybrać inną podsieć niż ta, która jest używana przez istniejącą integrację sieci wirtualnej.  

Ta funkcja jest dostępna w wersji zapoznawczej dla systemu Linux. Formularz systemu Linux funkcji obsługuje tylko wykonywanie wywołań do adresów RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Sieć Web/aplikacja funkcji dla kontenerów

Jeśli aplikacja jest hostowana w systemie Linux przy użyciu wbudowanych obrazów, integracja z regionalną siecią wirtualną działa bez dodatkowych zmian. Jeśli używasz sieci Web/aplikacja funkcji dla kontenerów, musisz zmodyfikować obraz platformy Docker, aby można było korzystać z integracji z siecią wirtualną. W obrazie platformy Docker Użyj zmiennej środowiskowej portu jako portu nasłuchu głównego serwera sieci Web, zamiast używać numeru portu stałe. Zmienna środowiskowa portu jest automatycznie ustawiana przez platformę w czasie uruchamiania kontenera. Jeśli używasz protokołu SSH, demona SSH musi być skonfigurowana do nasłuchiwania numeru portu określonego przez zmienną środowiskową SSH_PORT podczas korzystania z integracji regionalnej sieci wirtualnej.  W systemie Linux nie jest obsługiwana integracja z siecią wirtualną wymagana przez bramę. 

### <a name="service-endpoints"></a>Punkty końcowe usługi

Integracja z regionalną siecią wirtualną umożliwia korzystanie z punktów końcowych usługi.  Aby korzystać z punktów końcowych usługi w aplikacji, należy użyć regionalnej integracji sieci wirtualnej w celu nawiązania połączenia z wybraną siecią wirtualną, a następnie skonfigurowania punktów końcowych usługi w podsieci użytej do integracji. 

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowe grupy zabezpieczeń umożliwiają zablokowanie ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej. Aplikacja korzystająca z integracji regionalnej sieci wirtualnej może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch do adresów publicznych, musisz mieć ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL ustawione na 1. Reguły ruchu przychodzącego w sieciowej grupy zabezpieczeń nie mają zastosowania do Twojej aplikacji, ponieważ integracja z siecią wirtualną ma wpływ tylko na ruch wychodzący z aplikacji. Aby kontrolować ruch przychodzący do aplikacji, użyj funkcji ograniczenia dostępu. SIECIOWEJ grupy zabezpieczeń zastosowana do podsieci integracji będzie obowiązywać niezależnie od wszelkich tras zastosowanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL została ustawiona na 1 i nie ma żadnych tras wpływających na ruch publiczny w podsieci integracji, cały ruch wychodzący nadal będzie podlegać sieciowych grup zabezpieczeń przypisanemu do podsieci integracji. Jeśli nie ustawiono WEBSITE_VNET_ROUTE_ALL, sieciowych grup zabezpieczeń będzie stosowany tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Tabele tras umożliwiają kierowanie ruchu wychodzącego z aplikacji do wszędzie tam, gdzie chcesz. Domyślnie tabele tras mają wpływ tylko na ruch docelowy RFC1918.  Jeśli ustawisz wartość WEBSITE_VNET_ROUTE_ALL na 1, wpłynie to na wszystkie wywołania wychodzące. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na żądania aplikacji przychodzących. Wspólne miejsca docelowe mogą obejmować urządzenia zapory lub bramy. Jeśli chcesz skierować cały ruch wychodzący lokalnie, możesz użyć tabeli tras do wysłania całego ruchu wychodzącego do bramy ExpressRoute. W przypadku kierowania ruchu do bramy należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy Border Gateway Protocol (BGP) wpłyną również na ruch aplikacji. Jeśli masz trasy BGP ze względu na bramę ExpressRoute, wpłynie to na ruch wychodzący aplikacji. Domyślnie trasy protokołu BGP mają wpływ tylko na ruch docelowy RFC1918. Jeśli wartość WEBSITE_VNET_ROUTE_ALL jest równa 1, trasy protokołu BGP mogą mieć wpływ na cały ruch wychodzący. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/