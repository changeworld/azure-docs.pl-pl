---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671522"
---
Korzystanie z regionalnej integracji sieci wirtualnej umożliwia dostęp aplikacji:

* zasobów w sieci wirtualnej w tym samym regionie, z którego 
* zasoby w sieciach wirtualnych równorzędnych do sieci wirtualnej, które znajdują się w tym samym regionie
* usługi zabezpieczone za punktu końcowego usługi
* zasobów w połączeniach usługi ExpressRoute
* zasobów w sieci wirtualnej, z którą jest połączony
* zasobów między połączeniami równorzędnym, w tym połączeniami usługi ExpressRoute
* prywatne punkty końcowe 

Korzystając z integracji sieci wirtualnej z sieciami wirtualnymi w tym samym regionie, można użyć następujących funkcji sieci platformy Azure:

* Sieciowe grupy zabezpieczeń (nsg) — ruch wychodzący można blokować za pomocą sieciowej grupy zabezpieczeń umieszczonej w podsieci integracji. Reguły ruchu przychodzącego nie mają zastosowania, ponieważ nie można użyć integracji sieci wirtualnej, aby zapewnić dostęp przychodzący do aplikacji.
* Tabele tras (UDR) — tabelę marszruty można umieścić w podsieci integracji, aby wysyłać ruch wychodzący w odpowiedni sposób. 

Domyślnie aplikacja będzie kierować tylko ruch RFC1918 do sieci wirtualnej. Jeśli chcesz rozsyłać cały ruch wychodzący do sieci wirtualnej, zastosuj ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL do aplikacji. Aby skonfigurować ustawienie aplikacji:

1. Przejdź do interfejsu użytkownika konfiguracji w portalu aplikacji. Wybierz **nowe ustawienie aplikacji**
1. Typ **WEBSITE_VNET_ROUTE_ALL** w polu Nazwa i **1** w polu Wartość

   ![Podaj ustawienie aplikacji][4]

1. Wybierz **przycisk OK**
1. Wybierz pozycję **Zapisz**.

Jeśli trasa całego ruchu wychodzącego do sieci wirtualnej, a następnie będzie podlegać nsgs i UDRs, które są stosowane do podsieci integracji. Podczas kierowania całego ruchu wychodzącego do sieci wirtualnej, adresy wychodzące będą nadal adresy wychodzące, które są wymienione we właściwościach aplikacji, chyba że udostępniasz trasy do wysyłania ruchu w innym miejscu. 

Istnieją pewne ograniczenia dotyczące korzystania z integracji sieci wirtualnej z sieciami wirtualnymi w tym samym regionie:

* Nie można dotrzeć do zasobów w globalnych połączeniach komunikacji równorzędnej
* Ta funkcja jest dostępna tylko w nowszych jednostkach skalowania usługi App Service, które obsługują plany usługi aplikacji PremiumV2.
* Podsieci integracji może być używana tylko przez jeden plan usługi app service
* Funkcja nie może być używana przez aplikacje planu izolowanego, które znajdują się w środowisku usługi aplikacji
* Ta funkcja wymaga nieużywanej podsieci o rozmiarze /27 z 32 adresami lub większymi w sieci wirtualnej Menedżera zasobów
* Aplikacja i sieci wirtualnej muszą znajdować się w tym samym regionie
* Nie można usunąć sieci wirtualnej za pomocą zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej. 
* Możesz zintegrować się z sieciami wirtualnymi tylko w tej samej subskrypcji co aplikacja
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na plan usługi app service. Wiele aplikacji w tym samym planie usługi app service można użyć tej samej sieci wirtualnej. 
* Nie można zmienić subskrypcji aplikacji lub planu, gdy istnieje aplikacja, która korzysta z regionalnej integracji sieci wirtualnej

Jeden adres jest używany dla każdego wystąpienia planu. Jeśli skalujesz aplikację do pięciu wystąpień, zostanie użytych pięć adresów. Ponieważ rozmiar podsieci nie można zmienić po przypisaniu, należy użyć podsieci, która jest wystarczająco duża, aby pomieścić niezależnie od skali aplikacji może osiągnąć. A /26 z 64 adresami jest zalecanym rozmiarem. /26 z 64 adresami pomieści plan Premium z 30 wystąpieniami. Podczas skalowania planu w górę lub w dół, trzeba dwa razy więcej adresów na krótki okres czasu. 

Jeśli chcesz, aby aplikacje w innym planie osiągnęły sieci wirtualnej, która jest już połączona przez aplikacje w innym planie, musisz wybrać inną podsieć niż ta używana przez istniejącą integrację sieci wirtualnej.  

Funkcja jest w wersji zapoznawczej dla Linuksa. Forma linuksa tej funkcji obsługuje tylko wykonywanie połączeń na adresy RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Aplikacja Web / Function dla kontenerów

Jeśli hostujesz aplikację w systemie Linux z wbudowanymi obrazami, regionalna integracja sieci wirtualnej działa bez dodatkowych zmian. Jeśli używasz aplikacji Web / Function dla kontenerów, należy zmodyfikować obraz docker w celu korzystania z integracji sieci wirtualnej. W obrazie platformy docker użyj zmiennej środowiskowej PORT jako portu nasłuchiwania głównego serwera sieci Web, zamiast numeru portu zakodowanego. Zmienna środowiskowa PORT jest automatycznie ustawiana przez platformę w czasie uruchamiania kontenera. Jeśli używasz SSH, demon SSH musi być skonfigurowany do nasłuchiwać na numer portu określony przez zmienną środowiskową SSH_PORT podczas korzystania z regionalnej integracji sieci wirtualnej.  Nie ma obsługi wymaganej integracji sieci wirtualnej bramy w systemie Linux. 

### <a name="service-endpoints"></a>Punkty końcowe usługi

Regionalna integracja sieci wirtualnej umożliwia korzystanie z punktów końcowych usługi.  Aby użyć punktów końcowych usługi z aplikacją, użyj regionalnej integracji sieci wirtualnej, aby połączyć się z wybraną siecią wirtualną, a następnie skonfiguruj punkty końcowe usługi w podsieci używanej do integracji. 

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowe grupy zabezpieczeń umożliwiają blokowanie ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej. Aplikacja korzystająca z regionalnej integracji sieci wirtualnej może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch na adresy publiczne, musisz mieć ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL ustawiona na 1. Reguły ruchu przychodzącego w sieciowej sieciowej nie mają zastosowania do aplikacji, ponieważ integracja sieci wirtualnej wpływa tylko na ruch wychodzący z aplikacji. Aby kontrolować ruch przychodzący do aplikacji, użyj funkcji Ograniczenia dostępu. Sieciowej sieciowej, która jest stosowana do podsieci integracji będzie obowiązywać niezależnie od tras stosowanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL została ustawiona na 1 i nie masz żadnych tras wpływających na ruch adresu publicznego w podsieci integracji, cały ruch wychodzący nadal będzie podlegał grupom zabezpieczeń przypisanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL nie został ustawiony, nsgs będą stosowane tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Tabele tras umożliwiają kierowanie ruchu wychodzącego z aplikacji do dowolnego miejsca. Domyślnie tabele tras będą miały wpływ tylko na ruch docelowy RFC1918.  Jeśli ustawisz WEBSITE_VNET_ROUTE_ALL na 1, wpłynie to na wszystkie połączenia wychodzące. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na przychodzące żądania aplikacji. Typowe miejsca docelowe mogą obejmować urządzenia zapory lub bramy. Jeśli chcesz rozsyłać cały ruch wychodzący lokalnie, możesz użyć tabeli tras, aby wysłać cały ruch wychodzący do bramy usługi ExpressRoute. Jeśli kierujesz ruch do bramy, należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy protokołu BGP (Border Gateway Protocol) również wpłyną na ruch w aplikacji. Jeśli masz trasy BGP z czegoś takiego jak brama usługi ExpressRoute, ruch wychodzący aplikacji będzie miał wpływ. Domyślnie trasy BGP będą miały wpływ tylko na ruch docelowy RFC1918. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, trasy BGP mogą mieć wpływ na cały ruch wychodzący. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/