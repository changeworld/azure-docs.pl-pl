---
title: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
description: Połącz się prywatnie z aplikacją internetową przy użyciu prywatnego punktu końcowego platformy Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136676"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Używanie prywatnych punktów końcowych dla usługi Azure Web App (wersja zapoznawcza)

Możesz użyć prywatnego punktu końcowego dla aplikacji sieci Web platformy Azure, aby umożliwić klientom znajdującym się w sieci prywatnej bezpieczny dostęp do aplikacji za pośrednictwem prywatnego linku. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ruch sieciowy między klientem w sieci prywatnej a aplikacją sieci Web przechodzi przez sieć wirtualną oraz prywatny link do sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

Korzystanie z prywatnego punktu końcowego dla aplikacji sieci Web umożliwia:

- Zabezpiecz swoją aplikację sieci Web przez skonfigurowanie punktu końcowego usługi, eliminując publiczne ujawnianie
- Bezpiecznie łącz się z aplikacją internetową z sieci lokalnych, które łączą się z siecią wirtualną za pomocą sieci VPN lub prywatnej komunikacji równorzędnej ExpressRoute.

Jeśli potrzebujesz bezpiecznego połączenia między siecią wirtualną a aplikacją sieci Web, punkt końcowy usługi jest najprostszym rozwiązaniem. Jeśli trzeba również skontaktować się z aplikacją internetową z poziomu lokalnego za pośrednictwem bramy platformy Azure, z regionalną równorzędną siecią wirtualną lub z wirtualną siecią równorzędną, prywatny punkt końcowy to rozwiązanie.  

Aby uzyskać więcej informacji na temat [punktu końcowego usługi][serviceendpoint]

## <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy to specjalny interfejs sieciowy (nic) dla aplikacji sieci Web platformy Azure w podsieci w Virtual Network (VNET).
Utworzenie prywatnego punktu końcowego dla aplikacji sieci Web zapewnia bezpieczną łączność między klientami w sieci prywatnej a aplikacją sieci Web. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej.
Połączenie między prywatnym punktem końcowym a aplikacją sieci Web używa bezpiecznego [linku prywatnego][privatelink]. Prywatny punkt końcowy jest używany tylko w przypadku przepływów przychodzących do aplikacji sieci Web. Przepływy wychodzące nie będą używać tego prywatnego punktu końcowego, ale można wstrzyknąć przepływy wychodzące do sieci w innej podsieci za pomocą [funkcji integracji sieci wirtualnej][vnetintegrationfeature].

Podsieć, w której jest podłączony prywatny punkt końcowy, może zawierać inne zasoby, nie jest potrzebna dedykowana pusta podsieć.
Prywatny punkt końcowy można wdrożyć w innym regionie niż aplikacja internetowa. 

> [!Note]
>Funkcja integracji sieci wirtualnej nie może używać tej samej podsieci niż prywatny punkt końcowy. jest to ograniczenie funkcji integracji sieci wirtualnej.

Z perspektywy zabezpieczeń:

- Po włączeniu punktu końcowego usługi do aplikacji sieci Web należy wyłączyć dostęp publiczny
- Możesz włączyć wiele prywatnych punktów końcowych w innych sieci wirtualnych i podsieciach, w tym sieci wirtualnych w innych regionach
- Adres IP karty sieciowej prywatnego punktu końcowego musi być dynamiczny, ale pozostanie taki sam do momentu usunięcia prywatnego punktu końcowego
- Karta sieciowa prywatnego punktu końcowego nie może mieć skojarzonej sieciowej grupy zabezpieczeń
- W podsieci, w której znajduje się prywatny punkt końcowy, może być skojarzona sieciowej grupy zabezpieczeń, ale należy wyłączyć wymuszanie zasad sieciowych dla prywatnego punktu końcowego, zobacz [ten artykuł][disablesecuritype]. W związku z tym nie można filtrować według sieciowej grupy zabezpieczeń dostępu do prywatnego punktu końcowego
- Po włączeniu prywatnego punktu końcowego w aplikacji sieci Web konfiguracja [ograniczeń dostępu][accessrestrictions] aplikacji sieci Web nie jest szacowana.
- Możesz zmniejszyć ryzyko związane z eksfiltracji danych z sieci wirtualnej, usuwając wszystkie reguły sieciowej grupy zabezpieczeń, w których miejsce docelowe jest znacznikiem internetowym lub usług platformy Azure. Jednak dodanie punktu końcowego App Service sieci Web w podsieci spowoduje nawiązanie połączenia z aplikacją sieci Web hostowaną w tej samej sygnaturze i uwidocznioną w Internecie.

Prywatny punkt końcowy dla aplikacji sieci Web jest dostępny dla warstwy PremiumV2 i izolowany z zewnętrznym środowiskiem ASE.

W dziennikach http sieci Web aplikacji sieci Web znajduje się adres IP źródła klienta. Protokół proxy protokołu TCP został wdrożony, przesyłając dalej do aplikacji sieci Web Właściwość IP klienta. Więcej informacji znajduje się w [tym artykule][tcpproxy].

![Przegląd globalny][1]


## <a name="dns"></a>DNS

Ponieważ ta funkcja jest dostępna w wersji zapoznawczej, nie zmieniamy wpisu DNS w trakcie okresu zapoznawczego. Musisz zarządzać własnym wpisem DNS na prywatnym serwerze DNS lub Azure DNS strefie prywatnej. Jeśli musisz użyć niestandardowej nazwy DNS, musisz dodać nazwę niestandardową w aplikacji sieci Web. W trakcie korzystania z wersji zapoznawczej Nazwa niestandardowa musi być zweryfikowana, podobnie jak nazwa niestandardowa, przy użyciu publicznego rozpoznawania DNS. [Informacje techniczne dotyczące niestandardowego sprawdzania poprawności nazw DNS][dnsvalidation]

## <a name="pricing"></a>Ceny

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link][pricing].

## <a name="limitations"></a>Ograniczenia

Regularnie udoskonalamy funkcję prywatnego linku i prywatnego punktu końcowego. Zapoznaj się z [tym artykułem][pllimitations] , aby uzyskać aktualne informacje o ograniczeniach.

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web za pomocą portalu, zobacz [jak połączyć się prywatnie z aplikacją sieci Web][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
