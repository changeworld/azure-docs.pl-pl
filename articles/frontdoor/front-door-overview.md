---
title: Azure Front Door Service | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Front Door. Sprawdź, czy jest ona dobrym rozwiązaniem w przypadku równoważenia obciążenia ruchu użytkownika w aplikacji.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: e92e51e8aabf24f1c5c4db31e2e203f391620ecc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423475"
---
# <a name="what-is-azure-front-door-service"></a>Co to jest usługa Azure Front Door Service?
Usługa Azure Front Door Service umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim przez optymalizowanie pod kątem uzyskiwania najlepszej wydajności i natychmiastowego przechodzenia w tryb failover w celu zapewnienia wysokiej dostępności. Dzięki usłudze Front Door można przekształcić globalne usługi dla konsumentów (wiele regionów) i aplikacji w niezawodne, spersonalizowane, nowoczesne, wysokowydajne aplikacje, interfejsy API i zawartość, które dotrą do odbiorców globalnych za pomocą platformy Azure.

Usługa Front Door działa w warstwie 7 lub warstwie protokołu HTTP/HTTPS i używa protokołu emisji dowolnej z podzielonym protokołem TCP i globalną siecią firmy Microsoft w celu poprawienia łączności globalnej. Dlatego przez wybór metody routingu w konfiguracji można zagwarantować, że usługa Front Door będzie kierować żądania klienta do najszybszego i najbardziej dostępnego zaplecza aplikacji. Zaplecze aplikacji to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Usługa Front Door udostępnia szereg [metod routingu ruchu](front-door-routing-methods.md) oraz [opcji monitorowania kondycji zaplecza](front-door-health-probes.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Podobnie jak usługa [Traffic Manager](../traffic-manager/traffic-manager-overview.md), usługa Front Door jest odporna na awarie, w tym awarię całego regionu platformy Azure.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli potrzebujesz opartego na systemie DNS routingu globalnego i **nie** masz wymagań dotyczących zakończenia protokołu zabezpieczeń TLS (Transport Layer Security) („odciążanie protokołu SSL”) ani przetwarzania poszczególnych żądań dotyczących protokołu HTTP/HTTPS na poziomie warstwy aplikacji, zapoznaj się z tematem dotyczącym usługi [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Jeśli chcesz równoważyć obciążenie między serwerami w regionie na potrzeby warstwy aplikacji, zapoznaj się z omówieniem usługi [Application Gateway](../application-gateway/application-gateway-introduction.md). W przypadku równoważenia obciążenia w warstwie sieci zapoznaj się z omówieniem usługi [Load Balancer](../load-balancer/load-balancer-overview.md). Scenariusze kompleksowe mogą w razie potrzeby korzystać z zalet łączenia tych rozwiązań.
>
> For an Azure load-balancing options comparison, see [Overview of load-balancing options in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

W usłudze Front Door są dostępne następujące funkcje:

## <a name="accelerate-application-performance"></a>Zwiększanie wydajności aplikacji
Używając podzielonego protokołu emisji dowolnej opartego na protokole TCP, usługa Front Door gwarantuje, że użytkownicy końcowi mogą szybko łączyć się z najbliższym punktem POP (point of presence, punkt obecności) usługi Front Door. Używając globalnej sieci firmy Microsoft do łączenia się z zapleczami aplikacji z poziomu punktów POP, można zapewnić większą dostępność i niezawodność przy zachowaniu wydajności. Ta łączność z zapleczem opiera się również na najniższym opóźnieniu sieci. Dowiedz się więcej o technikach routingu usługi Front Door, takich jak [podzielony protokół TCP](front-door-routing-architecture.md#splittcp) i [protokół emisji dowolnej](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Zwiększanie dostępności aplikacji za pomocą inteligentnych sond kondycji

Usługa Front Door zapewnia aplikacjom o krytycznym znaczeniu wysoką dostępność, używając inteligentnych sond kondycji, monitorowania zaplecza pod kątem opóźnienia i dostępności oraz zapewniając możliwość natychmiastowego przejścia w tryb failover w przypadku awarii zaplecza. Dlatego można uruchamiać operacje planowanej konserwacji aplikacji bez przestojów. W czasie konserwacji usługa Front Door kieruje ruch do alternatywnych zapleczy.

## <a name="url-based-routing"></a>Routing oparty na adresach URL
Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul zaplecza na podstawie ścieżek URL żądania. Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pól zaplecza.

Na przykład żądania dotyczące adresu `http://www.contoso.com/users/*` są kierowane do puli UserProfilePool, a żądania dotyczące adresu `http://www.contoso.com/products/*` — do puli ProductInventoryPool.  Usługa Front Door umożliwia obsługę nawet bardziej złożonych scenariuszy dopasowywania tras za pomocą najlepszego algorytmu dopasowania. Jeśli żaden z wzorców ścieżki nie pasuje, jest wybierana domyślna reguła rozsyłania dla adresu `http://www.contoso.com/*`, a ruch jest kierowany do domyślnej reguły routingu przechwytującej wszystkie elementy. Dalsze informacje można znaleźć w temacie [Route Matching](front-door-route-matching.md) (Dopasowywanie tras).

## <a name="multiple-site-hosting"></a>Hostowanie wielu witryn
Hostowanie wielu witryn pozwala na skonfigurowanie więcej niż jednej witryny internetowej w tej samej konfiguracji usługi Front Door. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie różnych witryn internetowych do pojedynczej konfiguracji usługi Front Door. W oparciu o architekturę aplikacji można skonfigurować usługę Azure Front Door Service tak, aby kierowała każdą witrynę internetową do jej puli zaplecza albo aby różne witryny internetowe były kierowane do tej samej puli zaplecza. Na przykład usługa Front Door może obsługiwać ruch na potrzeby witryn `images.contoso.com` i `videos.contoso.com` z dwóch pul zaplecza o nazwie ImagePool i VideoPool. Alternatywnie można skonfigurować hosty frontonu tak, aby kierowały ruch do pojedynczej puli zaplecza o nazwie MediaPool.

Podobnie można mieć dwie różne domeny `www.contoso.com` i `www.fabrikam.com` skonfigurowane w tej samej usłudze Front Door.

## <a name="session-affinity"></a>Koligacja sesji
Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika w tym samym zapleczu aplikacji. Dzięki plikom cookie zarządzanym przez usługę Front Door dalszy ruch z sesji użytkownika jest kierowany do tego samego zaplecza aplikacji na potrzeby przetwarzania. Ta funkcja jest ważna w przypadkach, w których stan sesji jest zapisywany lokalnie w zapleczu dla sesji użytkownika.

## <a name="secure-sockets-layer-ssl-termination"></a>Kończenie żądań protokołu Secure Sockets Layer (SSL)
Usługa Front Door obsługuje kończenie żądań SSL na brzegu sieci. Oznacza to, że poszczególni użytkownicy mogą skonfigurować połączenie SSL przy użyciu środowisk usługi Front Door, zamiast ustanawiać je za pośrednictwem połączeń o dalekim zasięgu w zapleczu aplikacji. Ponadto usługa Front Door obsługuje łączność za pośrednictwem protokołów HTTP i HTTPS między środowiskami usługi Front Door i zapleczami. Dlatego można również skonfigurować kompleksowe szyfrowanie za pośrednictwem protokołu SSL. Jeśli na przykład usługa Front Door dla obciążenia aplikacji otrzymuje ponad 5000 żądań w ciągu minuty, ze względu na ponowne użycie połączenia bez wyłączania zasilania, w przypadku aktywnych usług zostanie ustanowionych tylko około 500 połączeń z zapleczem aplikacji, co znacznie zmniejszy obciążenie z zapleczy.

## <a name="custom-domains-and-certificate-management"></a>Zarządzanie domenami niestandardowymi i certyfikatami
W przypadku dostarczania zawartości przy użyciu usługi Front Door domena niestandardowa jest niezbędna, jeśli chcesz, aby nazwa Twojej domeny była widoczna w adresie URL usługi Front Door. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem.
Usługa Front Door obsługuje również protokół HTTPS w przypadku nazw domen niestandardowych. Użyj tej funkcji, wybierając certyfikaty zarządzane przez usługę Front Door dla ruchu lub przekazując własny niestandardowy certyfikat protokołu SSL.

## <a name="application-layer-security"></a>Zabezpieczenia warstwy aplikacji
Azure Front Door allows you to author custom Web Application Firewall (WAF) rules for access control to protect your HTTP/HTTPS workload from exploitation based on client IP addresses, country code, and http parameters. Ponadto usługa Front Door umożliwia również tworzenie reguł ograniczania szybkości, które pomagają zapobiegać ruchowi złośliwych botów. For more information about Web Application Firewall, see [What is Azure Web Application Firewall?](../web-application-firewall/overview.md)

Sama platforma Front Door jest chroniona przez usługę [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) w warstwie Podstawowa. W celu zastosowania dalszej ochrony przez atakami w warstwie sieci (protokół TCP/UDP) za pośrednictwem funkcji automatycznego dostrajania i ograniczania ryzyka można włączyć usługę Azure DDoS Protection w warstwie Standardowa w sieciach wirtualnych i zasobach zabezpieczeń. Usługa Front Door to zwrotny serwer proxy warstwy 7, który umożliwia tylko przechodzenie ruchu internetowego do zapleczy i domyślnie blokuje inne typy ruchu.

## <a name="url-redirection"></a>Przekierowywanie adresu URL
With the strong industry push on supporting only secure communication, web applications are expected to automatically redirect any HTTP traffic to HTTPS. This ensures that all communication between the users and the application occurs over an encrypted path. 

Traditionally, application owners have dealt with this requirement by creating a dedicated service, whose sole purpose was to redirect requests it receives on HTTP to HTTPS. Azure Front Door Service supports the ability to redirect traffic from HTTP to HTTPS. Upraszcza to konfigurację aplikacji, optymalizuje wykorzystanie zasobów i umożliwia obsługę nowych scenariuszy przekierowania, w tym przekierowania globalnego i opartego na ścieżce. URL redirection from Azure Front Door Service is not limited to HTTP to HTTPS redirection alone, but also to redirect to a different hostname, redirecting to a different path, or even redirecting to a new query string in the URL.

For more information, see [redirecting traffic](front-door-url-redirect.md) with Azure Front Door Service.

## <a name="url-rewrite"></a>Regenerowanie adresów URL
Usługa Front Door obsługuje [ponowne zapisywanie adresów URL](front-door-url-rewrite.md), pozwalając na konfigurowanie opcjonalnej niestandardowej ścieżki przekazywania dalej podczas konstruowania żądania do przekazania do zaplecza. Dodatkowo usługa Front Door umożliwia konfigurowanie nagłówka hosta do wysłania w przypadku przekazywania żądania dalej do zaplecza.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Obsługa protokołu — ruch IPv6 i HTTP/2
Usługa Azure Front Door natywnie obsługuje kompleksową łączność w protokole IPv6, a także protokół HTTP/2. 

Protokół HTTP/2 umożliwia pełnodupleksową komunikację między zapleczami aplikacji i klientem przez długotrwałe połączenie TCP. Protokół HTTP/2 pozwala na bardziej interaktywną komunikację między zapleczem a klientem, która może być dwukierunkowa bez konieczności sondowania, co jest wymagane w implementacjach opartych na protokole HTTP. Protokół HTTP/2 ma niskie obciążenie, w odróżnieniu od protokołu HTTP, i może ponownie używać tego samego połączenia TCP dla wielu żądań lub odpowiedzi, co zapewnia bardziej efektywne wykorzystanie zasobów. Dowiedz się więcej na temat [obsługi protokołu HTTP/2 w usłudze Azure Front Door Service](front-door-http2.md).

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [cennik usługi Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
