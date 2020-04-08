---
title: Funkcje usługi Azure Application Gateway
description: Dowiedz się więcej o funkcjach usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: victorh
ms.openlocfilehash: f021eed959ef88a1ef3671e1d0ace8080710c92a
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810228"
---
# <a name="azure-application-gateway-features"></a>Funkcje usługi Azure Application Gateway

[Usługa Azure Application Gateway](overview.md) to moduł równoważenia obciążenia ruchu sieciowego, który umożliwia zarządzanie ruchem do aplikacji sieci web.

![Koncepcja modułu Application Gateway](media/overview/figure1-720.png)

Brama aplikacji zawiera następujące funkcje:

- [Zakończenie subskrypcji warstwy bezpiecznych gniazd (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Automatyczne skalowanie](#autoscaling)
- [Nadmiarowość stref](#zone-redundancy)
- [Statyczny VIP](#static-vip)
- [Zapora aplikacji internetowej](#web-application-firewall)
- [Kontroler ruchu przychodzącego dla usługi AKS](#ingress-controller-for-aks)
- [Routing oparty na adresie URL](#url-based-routing)
- [Hostowanie wielu witryn](#multiple-site-hosting)
- [Przekierowania](#redirection)
- [Koligacja sesji](#session-affinity)
- [Ruch protokołów WebSocket i HTTP/2](#websocket-and-http2-traffic)
- [Opróżnianie połączeń](#connection-draining)
- [Niestandardowe strony błędów](#custom-error-pages)
- [Ponowne zapisywanie nagłówków HTTP](#rewrite-http-headers)
- [Ustalanie rozmiaru](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Zakończenie subskrypcji warstwy bezpiecznych gniazd (SSL/TLS)

Brama aplikacji obsługuje zakończenie protokołu SSL/TLS na bramie, po czym ruch zazwyczaj przepływa niezaszyfrowany do serwerów wewnętrznej bazy danych. Ta funkcja umożliwia odciążenie serwerów sieci Web z nadmiaru kosztownych operacji szyfrowania i odszyfrowywania. Ale czasami niezaszyfrowana komunikacja z serwerami nie jest akceptowalną opcją. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń, wymaganiami dotyczącymi zgodności lub aplikacja może akceptować tylko bezpieczne połączenie. W przypadku tych aplikacji brama aplikacji obsługuje szyfrowanie SSL/TLS od końca do końca.

Aby uzyskać więcej informacji, zobacz [Omówienie zakończenia ssl i end to end SSL z bramą aplikacji](ssl-overview.md)

## <a name="autoscaling"></a>Skalowanie automatyczne

Brama aplikacji Standard_v2 obsługuje skalowanie automatyczne i może skalować w górę lub w dół na podstawie zmieniających się wzorców obciążenia ruchu. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. 

Aby uzyskać więcej informacji na temat funkcji Standard_v2 bramy aplikacji, zobacz [Skalowanie automatyczne w wersji 2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Nadmiarowość stref

Brama aplikacji Standard_v2 może obejmować wiele stref dostępności, oferując lepszą odporność na uszkodzenia i eliminując konieczność aprowizowania oddzielnych bram aplikacji w każdej strefie.

## <a name="static-vip"></a>Statyczny VIP

Brama aplikacji Standard_v2 jednostka SKU obsługuje wyłącznie statyczny typ adresu VIP. Gwarantuje to, że adres VIP skojarzony z bramą aplikacji nie zmienia się nawet przez cały okres istnienia bramy aplikacji.

## <a name="web-application-firewall"></a>Zapora aplikacji internetowej

Zapora aplikacji sieci Web (WAF) to usługa zapewniająca scentralizowaną ochronę aplikacji internetowych przed typowymi exploitami i lukami w zabezpieczeniach. WAF opiera się na regułach z [podstawowych reguł owasp (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (tylko WAF_v2), 3.0 i 2.2.9. 

Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy aplikacji można łatwo przekonwertować na bramę aplikacji włączoną w Zaporę aplikacji sieci Web.

Aby uzyskać więcej informacji, zobacz [Co to jest Zapora aplikacji sieci Web platformy Azure?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Kontroler ruchu przychodzącego dla usługi AKS
Kontroler transferu danych przychodzących bramy aplikacji (AGIC) umożliwia używanie bramy aplikacji jako transferu danych przychodzących dla klastra [usługi Azure Kubernetes Service (AKS).](https://azure.microsoft.com/services/kubernetes-service/) 

Kontroler transferu danych przychodzących działa jako zasobnik w klastrze AKS i zużywa [zasoby przychodzące Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) i konwertuje je na konfigurację bramy aplikacji, która umożliwia bramie równoważenie obciążenia ruchu do zasobników kubernetes. Kontroler transferu danych przychodzących obsługuje tylko Standard_v2 bramy aplikacji i WAF_v2 jednostek SKU. 

Aby uzyskać więcej informacji, zobacz [Kontroler transferu danych przychodzących bramy aplikacji (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Routing oparty na adresach URL

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pul.

Na przykład żądania dotyczące adresu `http://contoso.com/video/*` są kierowane do puli VideoServerPool, a żądania dotyczące adresu `http://contoso.com/images/*` — do puli ImageServerPool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

Aby uzyskać więcej informacji, zobacz [Omówienie routingu opartego na ścieżce adresów URL](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Hostowanie wielu witryn

Hostowanie wielu witryn pozwala na skonfigurowanie więcej niż jednej witryny internetowej w tym samym wystąpieniu bramy aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie do 100 witryn sieci web do jednej bramy aplikacji (w celu uzyskania optymalnej wydajności). Każdą witrynę internetową można skierować do jej własnej puli. Na przykład brama aplikacji może obsługiwać ruch dla witryn `contoso.com` i `fabrikam.com` z dwóch pul serwerów o nazwie ContosoServerPool i FabrikamServerPool.

Żądania dotyczące adresu `http://contoso.com` są kierowane do puli ContosoServerPool, a żądania dotyczące adresu `http://fabrikam.com` — do puli FabrikamServerPool.

Podobnie dwie domeny podrzędne tej samej domeny nadrzędnej mogą być hostowane w ramach tego samego wdrożenia usługi Application Gateway. Przykłady użycia domen podrzędnych mogą obejmować domeny `http://blog.contoso.com` i `http://app.contoso.com` hostowane w jednym wdrożeniu bramy Application Gateway.

Aby uzyskać więcej informacji, zobacz [Brama aplikacji hosting wielu lokacji](multiple-site-overview.md).

## <a name="redirection"></a>Przekierowania

Typowy scenariusz dla wielu aplikacji internetowych obejmuje obsługę automatycznego przekierowania protokołu HTTP do HTTPS, aby zagwarantować, że cała komunikacja między aplikacją a jej użytkownikami odbywa się za pośrednictwem ścieżki szyfrowanej.

W przeszłości być może były używane techniki, takie jak tworzenie dedykowanej puli, których jedynym celem jest przekierowanie żądań otrzymywanych na HTTP do HTTPS. Usługa Application Gateway obsługuje możliwość przekierowywania ruchu sieciowego w tej usłudze. Upraszcza to konfigurację aplikacji, optymalizuje wykorzystanie zasobów i umożliwia obsługę nowych scenariuszy przekierowania, w tym przekierowania globalnego i opartego na ścieżce. Obsługa przekierowania bramy aplikacji nie jest ograniczona do samego przekierowania HTTP do HTTPS. Jest to ogólny mechanizm przekierowania, dzięki czemu możliwe jest przekierowanie z i do dowolnego portu zdefiniowanego przy użyciu reguł. Obsługiwane jest również przekierowanie do zewnętrznej witryny.

Obsługa przekierowania dla usługi Application Gateway oferuje następujące możliwości:

- Globalne przekierowanie z jednego portu do innego portu w bramie. Umożliwia to przekierowanie protokołu HTTP do HTTPS w witrynie.
- Przekierowanie na podstawie ścieżki. Ten typ przekierowania umożliwia przekierowanie protokołu HTTP do HTTPS tylko w określonym obszarze witryny, na przykład obszarze koszyka określonym przez element `/cart/*`.
- Przekierowanie do zewnętrznej witryny.

Aby uzyskać więcej informacji, zobacz [Omówienie przekierowania bramy aplikacji](redirect-overview.md).

## <a name="session-affinity"></a>Koligacja sesji

Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

Aby uzyskać więcej informacji, zobacz [Jak działa brama aplikacji](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Ruch protokołów WebSocket i HTTP/2

Usługa Application Gateway zapewnia natywną obsługę protokołów WebSocket i HTTP/2. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket.

Protokoły WebSocket i HTTP/2 umożliwiają pełnodupleksową komunikację między serwerem i klientem przez długotrwałe połączenie TCP. Pozwala to na bardziej interaktywną komunikację między serwerem internetowym a klientem, która może być dwukierunkowa bez konieczności sondowania, co jest wymagane w implementacjach opartych na protokole HTTP. Protokoły te mają niskie obciążenie, w przeciwieństwie do protokołu HTTP i można ponownie użyć tego samego połączenia TCP dla wielu żądań/odpowiedzi w wyniku bardziej efektywnego wykorzystania zasobów. Te protokoły są przeznaczone do pracy z użyciem tradycyjnych portów HTTP, tj. 80 i 443.

Aby uzyskać więcej informacji, zobacz [Pomoc techniczna aplikacji WebSocket](application-gateway-websocket.md) i [obsługa protokołu HTTP/2](configuration-overview.md#http2-support).

## <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń umożliwia bezproblemowe usunięcie członków puli zaplecza podczas planowanych aktualizacji usługi. To ustawienie jest włączane za pośrednictwem ustawienia http zaplecza i można je zastosować do wszystkich członków puli zaplecza podczas tworzenia reguły. Po włączeniu brama aplikacji zapewnia, że wszystkie wystąpienia wyrejestrowania puli wewnętrznej bazy danych nie otrzymują żadnych nowych żądań, umożliwiając jednocześnie wykonanie istniejących żądań w skonfigurowanym terminie. Dotyczy to zarówno wystąpień wewnętrznej bazy danych, które są jawnie usuwane z puli wewnętrznej bazy danych przez zmianę konfiguracji użytkownika, jak i wystąpień wewnętrznej bazy danych, które są zgłaszane jako złej kondycji, zgodnie z ustaleniami sond kondycji. Jedynym wyjątkiem od tego są żądania związane z wyrejestrowywania wystąpień, które zostały wyrejestrowane jawnie, z powodu koligacji sesji zarządzanej przez bramę i nadal są proxied do wystąpień wyrejestrowania.

Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji bramy aplikacji](configuration-overview.md#connection-draining).

## <a name="custom-error-pages"></a>Niestandardowe strony błędów

Usługa Application Gateway umożliwia tworzenie niestandardowych stron błędów wyświetlanych zamiast domyślnych strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.

Aby uzyskać więcej informacji, zobacz [Błędy niestandardowe](custom-error.md).

## <a name="rewrite-http-headers"></a>Ponowne zapisywanie nagłówków HTTP

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji wraz z żądaniem lub odpowiedzią. Przepisywanie tych nagłówków HTTP pomaga wykonać kilka ważnych scenariuszy, takich jak:

- Dodawanie pól nagłówka związanych z zabezpieczeniami, takich jak HSTS / X-XSS-Protection.
- Usuwanie pól nagłówka odpowiedzi, które mogą ujawniać poufne informacje.
- Usuwanie informacji o portach z nagłówków X-Forwarded-For.

Brama aplikacji obsługuje możliwość dodawania, usuwania lub aktualizowania nagłówków żądań i odpowiedzi HTTP, podczas gdy pakiety żądań i odpowiedzi są przesuwają się między pulami klienta i zaplecza. Zapewnia również możliwość dodawania warunków, aby upewnić się, że określone nagłówki są przepisywane tylko wtedy, gdy spełnione są pewne warunki.

Aby uzyskać więcej informacji, zobacz [Przepisywanie nagłówków HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Ustalanie rozmiaru

Brama aplikacji Standard_v2 można skonfigurować do skalowania automatycznego lub wdrożeń o stałym rozmiarze. Ta jednostka SKU nie oferuje różnych rozmiarów wystąpień. Aby uzyskać więcej informacji na temat wydajności i cen w wersji 2, zobacz [Skalowanie automatyczne w wersji 2 SKU](application-gateway-autoscaling-zone-redundant.md#pricing).

Standard bramy aplikacji jest oferowany w trzech rozmiarach: **Mały,** **Średni**i **Duży.** Rozmiary małych wystąpień są przeznaczone na potrzeby programowania i scenariuszy testowania.

Pełna lista limitów usługi Application Gateway znajduje się na stronie [ograniczeń usługi Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

W poniższej tabeli przedstawiono średnią przepływność wydajności dla każdego wystąpienia bramy aplikacji w wersji 1 z włączonym odciążeniem SSL:

| Średni rozmiar odpowiedzi strony zaplecza | Small | Medium | Large |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Są to przybliżone wartości przepływności bramy aplikacji. Rzeczywista przepływność zależy od różnorodnych szczegółów środowiska, takich jak średni rozmiar strony, lokalizacja wystąpień zaplecza i czas przetwarzania potrzebny do obsługi strony. Aby uzyskać dokładne wartości wydajności, należy przeprowadzić własne testy. Te wartości są podane tylko jako wskazówki na potrzeby planowania pojemności.

## <a name="version-feature-comparison"></a>Porównanie funkcji wersji

Aby zapoznać się z porównaniem funkcji bramy aplikacji w wersji 1-2, zobacz [Skalowanie automatyczne i brama aplikacji nadmiarowa strefowa w wersji 2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak działa brama aplikacji — [jak działa brama aplikacji](how-application-gateway-works.md)