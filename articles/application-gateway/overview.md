---
title: Co to jest Azure Application Gateway
description: Dowiedz się, jak za pomocą usługi Azure Application Gateway zarządzać ruchem internetowym kierowanym do aplikacji.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 91e94c70444430725ffec836d1663aef99eb5496
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003364"
---
# <a name="what-is-azure-application-gateway"></a>Co to jest Azure Application Gateway?

Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Tradycyjne moduły równoważenia obciążenia działają w warstwie transportu (warstwie OSI 4 — TCP i UDP) i kierują ruch na podstawie źródłowego adresu IP i portu do docelowego adresu IP i portu.

![Koncepcja modułu Application Gateway](media/overview/figure1-720.png)

Jednak usługa Application Gateway umożliwia bardziej szczegółowe zarządzanie ruchem. Na przykład można kierować ruch na podstawie przychodzącego adresu URL. Jeśli w przychodzącym adresie URL jest element `/images`, można kierować ruch do określonego zestawu serwerów (nazywanego pulą) skonfigurowanego na potrzeby obrazów. Jeśli w adresie URL jest element `/video`, ruch jest kierowany do innej puli zoptymalizowanej pod kątem wideo.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ten typ routingu jest nazywany równoważeniem obciążenia warstwy aplikacji (warstwy OSI 7). Usługa Azure Application Gateway może wykonywać routing oparty na adresach URL i nie tylko. 

W usłudze Azure Application Gateway są dostępne następujące funkcje:

## <a name="autoscaling-public-preview"></a>Skalowanie automatyczne w publicznej wersji zapoznawczej

Oprócz funkcji opisanych w tym artykule usługa Application Gateway oferuje również nową jednostkę SKU [Standard_V2] w publicznej wersji zapoznawczej, oferującą skalowanie automatyczne i inne udoskonalenia kluczowe dla poprawy wydajności.

- **Skalowanie automatyczne** — wdrożenie usługi Application Gateway lub WAF w ramach skalowania automatycznego jednostki SKU umożliwia skalowanie w górę lub dół na podstawie zmieniających się wzorców obciążenia ruchu. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. 

- **Nadmiarowość strefy** — wdrożenie usługi Application Gateway lub WAF może obejmować kilka stref dostępności, dzięki czemu nie trzeba już aprowizować i uruchamiać oddzielnych wystąpień usługi Application Gateway w każdej strefie przy użyciu usługi Traffic Manager.

- **Statyczny adres VIP** — adres VIP usługi Application Gateway teraz obsługuje wyłącznie typ statyczny adresu VIP. Dzięki temu adres VIP skojarzony z usługą Application Gateway nie zmienia się nawet po ponownym uruchomieniu.

- **Krótszy czas wdrożenia i aktualizacji** w porównaniu z ogólnie dostępną jednostką SKU. 

- **Pięć razy lepsza wydajność odciążenia protokołu SSL** w porównaniu z ogólnie dostępną jednostką SKU.

Aby uzyskać więcej informacji na temat funkcji usługi Application Gateway w publicznej wersji zapoznawczej, zobacz [Autoskalowana i strefowo nadmiarowa usługa Application Gateway (publiczna wersja zapoznawcza)](application-gateway-autoscaling-zone-redundant.md).

## <a name="secure-sockets-layer-ssl-termination"></a>Kończenie żądań protokołu Secure Sockets Layer (SSL)

Usługa Application Gateway obsługuje przerywanie połączenia SSL na bramie, po którym ruch na ogół płynie niezaszyfrowany do serwerów zaplecza. Ta funkcja umożliwia odciążenie serwerów sieci Web z nadmiaru kosztownych operacji szyfrowania i odszyfrowywania. Jednak czasami nieszyfrowana komunikacja z serwerami jest opcją niemożliwą do zaakceptowania. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. Na potrzeby takich aplikacji usługa Application Gateway obsługuje kompleksowe szyfrowanie SSL.

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Kontroler ruchu przychodzącego w usłudze Azure Kubernetes Service (AKS) — wersja zapoznawcza 

Kontroler ruchu przychodzącego w usłudze Application Gateway jest uruchamiany jako zasobnik w ramach klastra usługi AKS i umożliwia usłudze Application Gateway obsługę ruchu przychodzącego do klastra usługi AKS. Jest to obsługiwane za pomocą tylko v2 Application Gateway.

Aby uzyskać więcej informacji, zobacz [Azure Application Gateway Ingress Controller (Kontroler ruchu przychodzącego w usłudze Azure Application Gateway)](https://azure.github.io/application-gateway-kubernetes-ingress/).

## <a name="connection-draining"></a>Opróżnianie połączeń

Opróżnianie połączeń umożliwia bezproblemowe usunięcie członków puli zaplecza podczas planowanych aktualizacji usługi. To ustawienie jest włączane za pośrednictwem ustawienia http zaplecza i można je zastosować do wszystkich członków puli zaplecza podczas tworzenia reguły. Po włączeniu tego ustawienia usługa Application Gateway zapewnia, że żadne wyrejestrowane wystąpienia puli zaplecza nie otrzymują nowych żądań, ale jednocześnie umożliwia wykonanie istniejących żądań w ramach skonfigurowanego limitu czasu. Dotyczy to zarówno wystąpień zaplecza, które są jawnie usuwane z puli zaplecza przez wywołanie interfejsu API, jak i wystąpień zaplecza, które zostały zgłoszone jako niepoprawne według oceny sond kondycji.

## <a name="custom-error-pages"></a>Niestandardowe strony błędów
Usługa Application Gateway umożliwia tworzenie niestandardowych stron błędów wyświetlanych zamiast domyślnych strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu.

Aby uzyskać więcej informacji, zobacz [Create Application Gateway custom error pages (Tworzenie niestandardowych stron błędów w usłudze Application Gateway)](custom-error.md).

## <a name="web-application-firewall"></a>Zapora aplikacji internetowej

Zapora aplikacji internetowej (WAF) to funkcja usługi Application Gateway, która zapewnia scentralizowaną ochronę aplikacji internetowych przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach. Zapora aplikacji internetowych zapewnia ochronę na podstawie reguł z [podstawowych zestawów reguł OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) w wersji 3.0 lub 2.2.9. 

Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Wśród nich często zdarzają się np. ataki polegające na iniekcji SQL i ataki z użyciem skryptów wykorzystywanych w wielu witrynach. Zapobieganie takim atakom z poziomu kodu aplikacji może być trudne. Może też wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana zapora aplikacji internetowej ułatwia zarządzanie zabezpieczeniami oraz zapewnia lepszą ochronę administratorów aplikacji przed zagrożeniami i intruzami. Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

Aby uzyskać więcej informacji, zobacz [zapory aplikacji sieci Web (WAF) w usłudze Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview)).

## <a name="url-based-routing"></a>Routing oparty na adresach URL

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pul.

Na przykład żądania dotyczące adresu `http://contoso.com/video/*` są kierowane do puli VideoServerPool, a żądania dotyczące adresu `http://contoso.com/images/*` — do puli ImageServerPool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

Aby uzyskać więcej informacji, zobacz [routingu opartego na adres URL z usługą Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Hostowanie wielu witryn

Hostowanie wielu witryn pozwala na skonfigurowanie więcej niż jednej witryny internetowej w tym samym wystąpieniu bramy aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę internetową można skierować do jej własnej puli. Na przykład brama aplikacji może obsługiwać ruch dla witryn `contoso.com` i `fabrikam.com` z dwóch pul serwerów o nazwie ContosoServerPool i FabrikamServerPool.

Żądania dotyczące adresu `http://contoso.com` są kierowane do puli ContosoServerPool, a żądania dotyczące adresu `http://fabrikam.com` — do puli FabrikamServerPool.

Podobnie dwie domeny podrzędne tej samej domeny nadrzędnej mogą być hostowane w ramach tego samego wdrożenia usługi Application Gateway. Przykłady użycia domen podrzędnych mogą obejmować domeny podrzędne `http://blog.contoso.com` i `http://app.contoso.com` hostowane w ramach jednego wdrożenia usługi Application Gateway.

Aby uzyskać więcej informacji, zobacz [wielu lokacjach hostingu z usługą Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Przekierowania

Typowy scenariusz dla wielu aplikacji internetowych obejmuje obsługę automatycznego przekierowania protokołu HTTP do HTTPS, aby zagwarantować, że cała komunikacja między aplikacją a jej użytkownikami odbywa się za pośrednictwem ścieżki szyfrowanej. 

W przeszłości być może zdarzyło Ci się korzystać z technik takich jak tworzenie dedykowanej puli, której jedynym celem jest przekierowywanie żądań otrzymywanych przez nią w protokole HTTP do protokołu HTTPS. Usługa Application Gateway obsługuje możliwość przekierowywania ruchu sieciowego w tej usłudze. Upraszcza to konfigurację aplikacji, optymalizuje wykorzystanie zasobów i umożliwia obsługę nowych scenariuszy przekierowania, w tym przekierowania globalnego i opartego na ścieżce. Obsługa przekierowania w usłudze Application Gateway nie jest ograniczona tylko do przekierowania protokołu HTTP do protokołu HTTPS. Jest to ogólny mechanizm przekierowania, dzięki czemu możliwe jest przekierowanie z i do dowolnego portu zdefiniowanego przy użyciu reguł. Obsługiwane jest również przekierowanie do zewnętrznej witryny.

Obsługa przekierowania dla usługi Application Gateway oferuje następujące możliwości:

- Globalne przekierowanie z jednego portu do innego portu w bramie. Umożliwia to przekierowanie protokołu HTTP do HTTPS w witrynie.
- Przekierowanie na podstawie ścieżki. Ten typ przekierowania umożliwia przekierowanie protokołu HTTP do HTTPS tylko w określonym obszarze witryny, na przykład obszarze koszyka określonym przez element `/cart/*`.
- Przekierowanie do zewnętrznej witryny.

Aby uzyskać więcej informacji, zobacz [przekierowywanie ruchu](https://docs.microsoft.com/azure/application-gateway/redirect-overview) z usługą Application Gateway.

## <a name="session-affinity"></a>Koligacja sesji

Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz zachować sesję użytkownika na tym samym serwerze. Używając plików cookie zarządzanych przez bramę, usługa Application Gateway może kierować dalszy ruch z sesji użytkownika na ten sam serwer w celu przetwarzania. Jest to ważne w przypadkach, w których stan sesji jest zapisywany lokalnie na serwerze dla sesji użytkownika.

## <a name="websocket-and-http2-traffic"></a>Ruch protokołów WebSocket i HTTP/2

Usługa Application Gateway zapewnia natywną obsługę protokołów WebSocket i HTTP/2. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket.

Protokoły WebSocket i HTTP/2 umożliwiają pełnodupleksową komunikację między serwerem i klientem przez długotrwałe połączenie TCP. Pozwala to na bardziej interaktywną komunikację między serwerem internetowym a klientem, która może być dwukierunkowa bez konieczności sondowania, co jest wymagane w implementacjach opartych na protokole HTTP. Te protokoły mają niskie obciążenie, w odróżnieniu od protokołu HTTP, i mogą ponownie używać tego samego połączenia TCP dla wielu żądań/odpowiedzi, co zapewnia bardziej efektywne wykorzystanie zasobów. Te protokoły są przeznaczone do pracy z użyciem tradycyjnych portów HTTP, tj. 80 i 443.

Aby uzyskać więcej informacji, zobacz [Obsługa protokołu WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) i [Obsługa protokołu HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support). 

## <a name="rewrite-http-headers-public-preview"></a>Ponowne zapisywanie nagłówków HTTP (publiczna wersja zapoznawcza)

Zezwalaj na nagłówki HTTP, klienta i serwera przekazać dodatkowe informacje z żądania lub odpowiedzi. Ponowne napisanie tych nagłówków HTTP pomaga osiągnąć kilku ważnych scenariuszy, takich jak:
- Dodawanie pola nagłówka związanych z zabezpieczeniami, takich jak HSTS / X XSS ochrony.
- Usuwanie pola nagłówka odpowiedzi, które może ujawnić poufne informacje.
- Usuwanie z nagłówków X-Forwarded-Aby uzyskać informacje o porcie.

Usługa Application Gateway obsługuje możliwość dodać, usunąć lub zaktualizować nagłówki żądania i odpowiedzi HTTP, gdy pakiety żądań i odpowiedzi, przenoszenie między klientem a pule zaplecza. On również zapewnia możliwość dodawania warunków, aby upewnić się, że określone nagłówki są przepisany tylko wtedy, gdy zostaną spełnione określone warunki.

Aby uzyskać więcej informacji na temat tej funkcji w publicznej wersji zapoznawczej, zobacz artykuł [Ponowne zapisywanie nagłówków HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Ustalanie rozmiaru

Usługa Application Gateway jest obecnie oferowana w trzech rozmiarach: małym (**Small**), średnim (**Medium**) i dużym (**Large**). Rozmiary małych wystąpień są przeznaczone na potrzeby programowania i scenariuszy testowania.

Pełna lista limitów usługi Application Gateway znajduje się na stronie [ograniczeń usługi Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

W poniższej tabeli przedstawiono przepływność przy średniej wydajności dla każdego wystąpienia bramy aplikacji z włączonym obciążeniem SSL:

| Średni rozmiar odpowiedzi strony zaplecza | Small | Medium | Large |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Są to przybliżone wartości przepływności bramy aplikacji. Rzeczywista przepływność zależy od różnorodnych szczegółów środowiska, takich jak średni rozmiar strony, lokalizacja wystąpień zaplecza i czas przetwarzania potrzebny do obsługi strony. Aby uzyskać dokładne wartości wydajności, należy przeprowadzić własne testy. Te wartości są podane tylko jako wskazówki na potrzeby planowania pojemności.

## <a name="next-steps"></a>Kolejne kroki

W zależności od wymagań i środowiska możesz utworzyć testową usługę Application Gateway przy użyciu witryny Azure Portal, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure:

- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal](quick-create-portal.md).
- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure PowerShell](quick-create-powershell.md)
- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure](quick-create-cli.md)
