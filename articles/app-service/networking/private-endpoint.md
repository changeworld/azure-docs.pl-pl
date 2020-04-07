---
title: Łączenie się prywatnie z aplikacją sieci Web przy użyciu prywatnego punktu końcowego platformy Azure
description: Łączenie się prywatnie z aplikacją sieci Web przy użyciu prywatnego punktu końcowego platformy Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756278"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Korzystanie z prywatnych punktów końcowych dla aplikacji Azure Web App (Wersja zapoznawcza)

> [!Note]
> Wersja zapoznawcza jest dostępna w regionach wschodnich stanów USA i zachodnie stany USA 2 dla wszystkich aplikacji PremiumV2 Windows i Linux Web Apps oraz elastycznych funkcji premium. 

Za pomocą prywatnego punktu końcowego dla aplikacji Azure Web App można zezwolić klientom znajdującym się w sieci prywatnej na bezpieczny dostęp do aplikacji za pośrednictwem łącza prywatnego. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej platformy Azure. Ruch sieciowy między klientem w sieci prywatnej a aplikacją sieci Web przechodzi przez sieć wirtualną i łącze prywatne w sieci szkieletowej firmy Microsoft, eliminując narażenie z publicznego Internetu.

Korzystanie z prywatnego punktu końcowego dla aplikacji sieci Web umożliwia:

- Zabezpiecz aplikację sieci Web, konfigurując prywatny punkt końcowy, eliminując publiczne narażenie.
- Bezpiecznie połącz się z aplikacją Sieci Web z sieci lokalnych łączących się z siecią wirtualną przy użyciu prywatnej komunikacji równorzędnej sieci VPN lub usługi ExpressRoute.

Jeśli potrzebujesz bezpiecznego połączenia między siecią wirtualną a aplikacją sieci Web, punkt końcowy usługi jest najprostszym rozwiązaniem. Jeśli musisz również dotrzeć do aplikacji sieci web z lokalnego za pośrednictwem bramy platformy Azure, regionalnie równorzędnej sieci wirtualnej lub globalnie równorzędnej sieci wirtualnej, rozwiązaniem jest prywatny punkt końcowy.  

Aby uzyskać więcej informacji, zobacz [Punkty końcowe usługi][serviceendpoint].

## <a name="conceptual-overview"></a>Omówienie pojęć

Prywatny punkt końcowy to specjalny interfejs sieciowy (NIC) dla aplikacji sieci Web platformy Azure w podsieci w sieci wirtualnej(VNet).
Podczas tworzenia prywatnego punktu końcowego dla aplikacji sieci Web zapewnia bezpieczną łączność między klientami w sieci prywatnej i aplikacji sieci Web. Prywatny punkt końcowy jest przypisywany adres IP z zakresu adresów IP sieci wirtualnej.
Połączenie między prywatnym punktem końcowym a aplikacją sieci Web korzysta z bezpiecznego [łącza prywatnego][privatelink]. Prywatny punkt końcowy jest używany tylko dla przepływów przychodzących do aplikacji sieci Web. Przepływy wychodzące nie będą używać tego prywatnego punktu końcowego, ale przepływy wychodzące można wstrzyknąć do sieci w innej podsieci za pośrednictwem [funkcji integracji sieci wirtualnej][vnetintegrationfeature].

Podsieć, w której można podłączyć prywatny punkt końcowy może mieć inne zasoby w nim, nie trzeba dedykowane puste podsieci.
Można również wdrożyć prywatny punkt końcowy w innym regionie niż aplikacja sieci Web. 

> [!Note]
>Funkcja integracji sieci wirtualnej nie może używać tej samej podsieci niż prywatny punkt końcowy, jest to ograniczenie funkcji integracji sieci wirtualnej.

Z punktu widzenia zabezpieczeń:

- Po włączeniu prywatnych punktów końcowych w aplikacji sieci Web, należy wyłączyć dostęp publiczny.
- Można włączyć wiele prywatnych punktów końcowych w innych sieciach wirtualnych i podsieciach, w tym sieci wirtualnych w innych regionach.
- Adres IP karty sieciowej prywatnego punktu końcowego musi być dynamiczny, ale pozostanie taki sam, dopóki nie usuniesz prywatnego punktu końcowego.
- Karta sieciowa prywatnego punktu końcowego nie może mieć skojarzonej sieciowej sieciowej sieciowej.
- Podsieć, która obsługuje prywatny punkt końcowy może mieć skojarzone sieciowej grupy ściągniętej, ale należy wyłączyć wymuszanie zasad sieciowych dla prywatnego punktu końcowego: zobacz [Wyłączanie zasad sieci dla prywatnych punktów końcowych][disablesecuritype]. W rezultacie nie można filtrować przez nsg dostęp do prywatnego punktu końcowego.
- Po włączeniu prywatnego punktu końcowego do aplikacji sieci Web, konfiguracja [ograniczeń dostępu][accessrestrictions] aplikacji sieci Web nie jest oceniana.
- Można zmniejszyć ryzyko eksfiltracji danych z sieci wirtualnej, usuwając wszystkie reguły sieciowej listy użytkowników sieciowych, w których miejscem docelowym jest tag Internet lub usługi platformy Azure. Jednak dodanie prywatnego punktu końcowego aplikacji sieci Web w podsieci umożliwi dotarcie do dowolnej aplikacji sieci Web hostowanej w tym samym sygnaturze wdrożenia i udostępnianej w Internecie.

W dziennikach HTTP w sieci Web aplikacji Web App znajdziesz źródłowy adres IP klienta. Jest to realizowane przy użyciu protokołu serwera proxy TCP, przesyłając dalej właściwość IP klienta do aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie informacji o połączeniu przy użyciu serwera proxy TCP w wersji 2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Omówienie globalnego prywatnego punktu końcowego aplikacji sieci Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Ponieważ ta funkcja jest w wersji zapoznawczej, nie zmieniamy wpisu DNS podczas podglądu. Musisz samodzielnie zarządzać wpisem DNS na prywatnym serwerze DNS lub w strefie prywatnej usługi Azure DNS.
Jeśli chcesz użyć niestandardowej nazwy DNS, musisz dodać nazwę niestandardową w aplikacji sieci Web. Podczas podglądu nazwa niestandardowa musi zostać zweryfikowana jak każda nazwa niestandardowa przy użyciu publicznej rozdzielczości DNS. Aby uzyskać więcej informacji, zobacz [niestandardowe sprawdzanie poprawności dns.][dnsvalidation]

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure][pricing].

## <a name="limitations"></a>Ograniczenia

Regularnie ulepszamy funkcję Private Link i prywatny punkt końcowy, sprawdź [ten artykuł,][pllimitations] aby uzyskać aktualne informacje o ograniczeniach.

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć prywatny punkt końcowy aplikacji sieci Web za pośrednictwem portalu, zobacz [Jak połączyć się prywatnie z aplikacją sieci Web][howtoguide]




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
