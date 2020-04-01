---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: e0db3ce7d31b838ca6f7d566083a33ee215d3399
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419516"
---
Korzystanie z regionalnej integracji sieci wirtualnej umożliwia dostęp aplikacji:

* Zasoby w sieci wirtualnej w tym samym regionie, z którego można zintegrować.
* Zasoby w sieciach wirtualnych równorzędne z siecią wirtualną, które znajdują się w tym samym regionie.
* Usługi zabezpieczone punktu końcowego usługi.
* Zasoby w połączeniach usługi Azure ExpressRoute.
* Zasoby w sieci wirtualnej, z którą masz połączenie.
* Zasoby między połączeniami równorzędnym, które obejmują połączenia Usługi Azure ExpressRoute.
* Prywatne punkty końcowe.

Korzystając z integracji sieci wirtualnej z sieciami wirtualnymi w tym samym regionie, można użyć następujących funkcji sieci platformy Azure:

* **Sieciowe grupy zabezpieczeń (NSG)**: Można zablokować ruch wychodzący za pomocą sieciowej grupy zabezpieczeń umieszczonej w podsieci integracji. Reguły ruchu przychodzącego nie mają zastosowania, ponieważ nie można użyć integracji sieci wirtualnej, aby zapewnić dostęp przychodzący do aplikacji.
* **Tabele tras (UDR)**: Tabelę tras można umieścić w podsieci integracji, aby wysyłać ruch wychodzący w odpowiedni sposób.

Domyślnie aplikacja kieruje tylko ruch RFC1918 do sieci wirtualnej. Jeśli chcesz przekierować cały ruch wychodzący do sieci wirtualnej, zastosuj ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL do aplikacji. Aby skonfigurować ustawienie aplikacji:

1. Przejdź do interfejsu użytkownika **konfiguracji** w portalu aplikacji. Wybierz **pozycję Nowe ustawienie aplikacji**.
1. Wprowadź **WEBSITE_VNET_ROUTE_ALL** w polu **Nazwa** i wprowadź **1** w polu **Wartość.**

   ![Podaj ustawienie aplikacji][4]

1. Kliknij przycisk **OK**.
1. Wybierz **pozycję Zapisz**.

Jeśli rozsyłasz cały ruch wychodzący do sieci wirtualnej, podlega on grupom zabezpieczeń i udr, które są stosowane do podsieci integracji. Podczas kierowania całego ruchu wychodzącego do sieci wirtualnej adresy wychodzące są nadal adresy wychodzące, które są wymienione we właściwościach aplikacji, chyba że udostępniasz trasy do wysyłania ruchu w innym miejscu.

Istnieją pewne ograniczenia dotyczące korzystania z integracji sieci wirtualnej z sieciami wirtualnymi w tym samym regionie:

* Nie można dotrzeć do zasobów w globalnych połączeniach komunikacji równorzędnej.
* Ta funkcja jest dostępna tylko z nowszych jednostek skalowania usługi Azure App Service, które obsługują plany usługi aplikacji PremiumV2.
* Podsieć integracji może być używana tylko przez jeden plan usługi app service.
* Tej funkcji nie można używać przez aplikacje izolowanego planu, które znajdują się w środowisku usługi aplikacji.
* Ta funkcja wymaga nieużywanej podsieci,która jest /27 z 32 adresami lub większymi w sieci wirtualnej usługi Azure Resource Manager.
* Aplikacja i sieć wirtualna muszą znajdować się w tym samym regionie.
* Nie można usunąć sieci wirtualnej za pomocą zintegrowanej aplikacji. Usuń integrację przed usunięciem sieci wirtualnej.
* Można zintegrować tylko z sieciami wirtualnymi w tej samej subskrypcji co aplikacja.
* Możesz mieć tylko jedną regionalną integrację sieci wirtualnej na plan usługi app service. Wiele aplikacji w tym samym planie usługi App Service można użyć tej samej sieci wirtualnej.
* Nie można zmienić subskrypcji aplikacji lub planu, gdy istnieje aplikacja, która korzysta z regionalnej integracji sieci wirtualnej.

Jeden adres jest używany dla każdego wystąpienia planu. Jeśli skalujesz aplikację do pięciu wystąpień, zostanie użytych pięć adresów. Ponieważ rozmiaru podsieci nie można zmienić po przypisaniu, należy użyć podsieci, która jest wystarczająco duża, aby pomieścić dowolną skalę, do której może dotrzeć aplikacja. A /26 z 64 adresami jest zalecanym rozmiarem. /26 z 64 adresami obsługuje plan Premium z 30 wystąpieniami. Podczas skalowania planu w górę lub w dół, trzeba dwa razy więcej adresów na krótki okres czasu.

Jeśli chcesz, aby aplikacje w innym planie osiągnęły sieć wirtualną, z którą są już połączone aplikacje w innym planie, wybierz inną podsieć niż ta używana przez istniejącą wcześniej integrację sieci wirtualnej.

Funkcja jest w wersji zapoznawczej dla Linuksa. Forma linuksa tej funkcji obsługuje tylko wykonywanie połączeń na adresy RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Aplikacja sieci Web lub funkcja dla kontenerów

Jeśli hostujesz aplikację w systemie Linux z wbudowanymi obrazami, regionalna integracja sieci wirtualnej działa bez dodatkowych zmian. Jeśli używasz aplikacji sieci Web lub funkcji dla kontenerów, należy zmodyfikować obraz platformy docker, aby użyć integracji sieci wirtualnej. W obrazie platformy docker użyj zmiennej środowiskowej PORT jako portu nasłuchiwania głównego serwera sieci Web, zamiast numeru portu zakodowanego. Zmienna środowiskowa PORT jest automatycznie ustawiana przez platformę w czasie uruchamiania kontenera. Jeśli używasz SSH, demon SSH musi być skonfigurowany do nasłuchiwać na numer portu określony przez zmienną środowiskową SSH_PORT podczas korzystania z regionalnej integracji sieci wirtualnej. Nie ma obsługi integracji sieci wirtualnej wymaganej przez bramę w systemie Linux.

### <a name="service-endpoints"></a>Punkty końcowe usługi

Z regionalnej integracji sieci wirtualnej, można użyć punktów końcowych usługi. Aby użyć punktów końcowych usługi z aplikacją, użyj regionalnej integracji sieci wirtualnej, aby połączyć się z wybraną siecią wirtualną. Następnie skonfiguruj punkty końcowe usługi w podsieci używanej do integracji.

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowych grup zabezpieczeń można używać do blokowania ruchu przychodzącego i wychodzącego do zasobów w sieci wirtualnej. Aplikacja korzystająca z regionalnej integracji sieci wirtualnej może używać [sieciowej grupy zabezpieczeń][VNETnsg] do blokowania ruchu wychodzącego do zasobów w sieci wirtualnej lub w Internecie. Aby zablokować ruch na adresy publiczne, musisz mieć ustawienie aplikacji WEBSITE_VNET_ROUTE_ALL ustawiona na 1. Reguły ruchu przychodzącego w sieciowej sieciowej nie mają zastosowania do aplikacji, ponieważ integracja sieci wirtualnej wpływa tylko na ruch wychodzący z aplikacji.

Aby kontrolować ruch przychodzący do aplikacji, użyj funkcji Ograniczenia dostępu. Sieciowej sieciowej, która jest stosowana do podsieci integracji jest w życie niezależnie od tras stosowanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1 i nie masz żadnych tras, które wpływają na ruch adresu publicznego w podsieci integracji, cały ruch wychodzący jest nadal przedmiotem sieciowej grup zabezpieczeń przypisanych do podsieci integracji. Jeśli WEBSITE_VNET_ROUTE_ALL nie jest ustawiona, nsgs są stosowane tylko do ruchu RFC1918.

### <a name="routes"></a>Trasy

Tabele tras umożliwiają kierowanie ruchu wychodzącego z aplikacji do dowolnego miejsca. Domyślnie tabele tras mają wpływ tylko na ruch docelowy RFC1918. Jeśli ustawisz WEBSITE_VNET_ROUTE_ALL na 1, dotyczy to wszystkich połączeń wychodzących. Trasy ustawione w podsieci integracji nie będą miały wpływu na odpowiedzi na przychodzące żądania aplikacji. Typowe miejsca docelowe mogą obejmować urządzenia zapory lub bramy.

Jeśli chcesz rozsyłać cały ruch wychodzący lokalnie, możesz użyć tabeli tras, aby wysłać cały ruch wychodzący do bramy usługi ExpressRoute. Jeśli kierujesz ruch do bramy, należy ustawić trasy w sieci zewnętrznej, aby wysyłać odpowiedzi z powrotem.

Trasy protokołu BGP (Border Gateway Protocol) również wpływają na ruch w aplikacji. Jeśli masz trasy BGP z czegoś takiego jak brama usługi ExpressRoute, ruch wychodzący aplikacji będzie miał wpływ. Domyślnie trasy BGP mają wpływ tylko na ruch docelowy RFC1918. Jeśli WEBSITE_VNET_ROUTE_ALL jest ustawiona na 1, wszystkie trasy BGP mogą mieć wpływ na cały ruch wychodzący.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/