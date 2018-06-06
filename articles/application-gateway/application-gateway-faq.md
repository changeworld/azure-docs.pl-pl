---
title: Często zadawane pytania dotyczące bramy aplikacji Azure
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące bramy aplikacji Azure
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 5/21/2018
ms.author: victorh
ms.openlocfilehash: bf4e92636424e7d8f4a1bc2eb5ee9ba7e97667c6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699907"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Często zadawane pytania dotyczące bramy aplikacji

## <a name="general"></a>Ogólne

**Q. Co to jest brama aplikacji?**

Bramy aplikacji Azure jest kontrolera dostarczania aplikacji (ADC) jako usługa, oferty różne obciążenia warstwy 7 równoważenia możliwości dla aplikacji. Zapewnia wysoką dostępność i skalowalność usługi, która jest w pełni zarządzana przez Azure.

**Q. Jakie funkcje obsługuje bramy aplikacji?**

Brama aplikacji w obsługuje odciążanie protokołu SSL i pełnego SSL, Zapora aplikacji sieci Web, koligacji na podstawie plików cookie sesji, adres url na podstawie ścieżki routingu, wielu lokacji hosting i inne. Aby uzyskać pełną listę obsługiwanych funkcji, odwiedź stronę [wprowadzenie do bramy aplikacji](application-gateway-introduction.md)

**Q. Jaka jest różnica między bramą aplikacji i usługi równoważenia obciążenia Azure?**

Brama aplikacji jest modułem równoważenia obciążenia warstwy 7, co oznacza, że w przypadku ruchu w sieci web tylko (WebSocket-HTTP/HTTPS). Obsługuje ona możliwości, takie jak zakończenie protokołu SSL, koligacji na podstawie plików cookie sesji i okrężnego dla ruchu równoważenia obciążenia. Moduł równoważenia obciążenia, załadowanie salda ruchu na poziomie warstwy 4 (TCP/UDP).

**Q. Jakie protokoły obsługuje bramy aplikacji?**

Brama aplikacji w obsługuje HTTP, HTTPS HTTP/2 i protokołu WebSocket.

**Q. W jaki sposób Application Gateway obsługuje HTTP/2**

Obsługa protokołu HTTP/2 jest dostępny dla klientów łączących się tylko odbiorników bramy aplikacji. Komunikacja z pul serwerów wewnętrznej bazy danych jest za pośrednictwem protokołu HTTP/1.1. 

Domyślnie obsługa HTTP/2 jest wyłączona. W poniższym przykładzie wstawki kodu programu Azure PowerShell pokazuje, jak możesz ją włączyć:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**Q. Jakie zasoby są obsługiwane obecnie częścią puli wewnętrznej bazy danych?**

Pul zaplecza może składać się z kart sieciowych, zestawy skalowania maszyny wirtualnej, publiczne adresy IP, wewnętrzny adresów IP, w pełni kwalifikowaną nazwę (FQDN) i zapleczy wielodostępne takich jak aplikacje sieci Web platformy Azure. Członków puli zaplecza bramy aplikacji nie są związane z zestawu dostępności. Elementami członkowskimi pul zaplecza może być między klastrami i centrami danych, lub poza platformą Azure, jak długo mają połączenia IP.

**Q. Jakich regionach jest dostępna w usłudze?**

Brama aplikacji jest dostępna we wszystkich regionach Azure globalnego. Jest również dostępna w [chińskiej wersji platformy Azure](https://www.azure.cn/) i [Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/)

**Q. Jest to dedykowane wdrożenia dla mojej subskrypcji lub jest on udostępniony przez klientów?**

Brama aplikacji jest dedykowany wdrażania w sieci wirtualnej.

**Q. Jest HTTP -> obsługiwanych przekierowanie protokołu HTTPS**

Przekierowanie jest obsługiwane. Odwiedź stronę [omówienie przekierowania aplikacji bramy](application-gateway-redirect-overview.md) Aby dowiedzieć się więcej.

**Q. W jakiej kolejności odbiorników przetwarzania?**

Obiekty nasłuchujące są przetwarzane w kolejności, które są wyświetlane. Dlatego jeśli odbiornik podstawowe pasuje do przychodzącego żądania przetwarza je najpierw.  Odbiorniki obejmujący wiele lokacji należy skonfigurować przed podstawowe odbiornika do upewnij się, że ruch jest kierowany do poprawne zaplecza.

**Q. Gdzie znaleźć adresów IP i DNS bramy aplikacji**

Korzystając z publicznym adresem IP jako punktu końcowego, te informacje można znaleźć zasobu publicznego adresu IP lub na stronie Przegląd bramy aplikacji w portalu. Dla adresów IP to można znaleźć na stronie przeglądu.

**Q. Czy adres IP lub DNS zmienia się w okresie istnienia bramy aplikacji?**

Jeśli brama zostanie zatrzymana i uruchomiona przez klienta, można zmienić adresu VIP. Z cyklem bramy nie zmienia DNS skojarzone z bramą aplikacji. Z tego powodu zaleca się użyć tego aliasu CNAME, a następnie wskaż adresu DNS bramy aplikacji.

**Q. Brama aplikacji w obsługuje statyczny adres IP?**

Nie, bramy aplikacji nie obsługuje statyczne publiczne adresy IP, ale obsługuje statyczne wewnętrzne adresy IP.

**Q. Brama aplikacji w obsługuje wiele publicznych adresów IP w bramie?**

Dotyczy tylko jeden publiczny adres IP bramy aplikacji.

**Q. Jak duże utworzyć mojej podsieci bramy aplikacji?**

Brama aplikacji w zużywa jednego prywatnego adresu IP dla każdego wystąpienia, a także innego prywatnego adresu IP, jeśli skonfigurowano konfiguracji IP frontonu prywatnych. Ponadto Azure rezerwuje pierwsze cztery i ostatni adres IP w każdej podsieci do użytku wewnętrznego.
Na przykład, jeśli ustawiono brama aplikacji w trzech przypadkach i adres IP nie prywatnego serwera sieci Web, następnie /29 podsieci rozmiar lub nowszej jest wymagana. W takim przypadku aplikacji brama używa trzech adresów IP. Jeśli masz trzy wystąpienia i adres IP dla konfiguracji IP frontonu prywatne, następnie /28 podsieci rozmiar lub nowszego jest wymagany, ponieważ cztery adresy IP są wymagane.

**Q. Brama aplikacji w obsługuje x przekazywane dla nagłówków?**

Tak, brama aplikacji w wstawia nagłówków x przekazywane do, x przekazywane proto i x przekazywane — port na żądanie przekazywane do wewnętrznej bazy danych. Format x przekazywane do nagłówka jest rozdzielana przecinkami lista IP:Port. Prawidłowe wartości x przekazywane proto są http lub https. X-przekazywane — port Określa port, jaką na bramie aplikacji osiągnięty żądania.

**Q. Jak długo trwa wdrażanie bramy aplikacji? Brama mojej aplikacji nadal działa podczas aktualizowana?**

Nowe wdrożenia bramy aplikacji może potrwać maksymalnie 20 minut do udostępniania. Zmiany liczby rozmiar wystąpień nie są zakłócenie i brama pozostaje aktywna w tym czasie.

## <a name="configuration"></a>Konfigurowanie

**Q. Brama aplikacji zawsze wdrożonej w sieci wirtualnej?**

Tak, bramy aplikacji zawsze jest wdrażana w podsieci sieci wirtualnej. Ta podsieć może zawierać tylko bramy aplikacji.

**Q. Brama aplikacji może komunikować się wystąpień poza jego sieci wirtualnej?**

Brama aplikacji może komunikować się wystąpień poza siecią wirtualną, który jest tak długo, jak brak połączenia IP. Jeśli planujesz używać wewnętrznych adresów IP jako elementy członkowskie puli wewnętrznej bazy danych, a następnie wymaga [sieci Wirtualnej komunikacji równorzędnej](../virtual-network/virtual-network-peering-overview.md) lub [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**Q. Można wdrożyć niczego więcej w podsieci bramy aplikacji?**

Nie, ale można wdrożyć inne bramy aplikacji w podsieci.

**Q. Grupy zabezpieczeń sieci są obsługiwane w tej podsieci bramy aplikacji?**

Grup zabezpieczeń sieci są obsługiwane w tej podsieci bramy aplikacji z następującymi ograniczeniami:

* Wyjątki musi być włączony dla ruchu przychodzącego na portach 65503-65534 wewnętrznej bazy danych kondycji działał prawidłowo.

* Nie można zablokować wychodzące połączenie z Internetem.

* Wymagane jest zezwolenie ruch z znacznik AzureLoadBalancer.

**Q. Jakie są limity dla bramy aplikacji? Można zwiększyć te limity?**

Odwiedź stronę [limitów bramy aplikacji](../azure-subscription-service-limits.md#application-gateway-limits) można wyświetlić limitów.

**Q. Czy można użyć bramy aplikacji wewnętrznych i zewnętrznych ruchu równocześnie?**

Tak, Application Gateway obsługuje jeden adres IP wewnętrzny i jeden zewnętrznego adresu IP dla bramy aplikacji.

**Q. Czy sieci wirtualnej komunikacji równorzędnej obsługiwane?**

Tak, w sieci wirtualnej komunikacji równorzędnej jest obsługiwany i jest przydatne w przypadku ruchu w innych sieciach wirtualnych równoważenia obciążenia.

**Q. Może I komunikować się z serwerami lokalnymi po nawiązaniu przez tunele ExpressRoute lub sieci VPN?**

Tak, jak długo ruch jest dozwolony.

**Q. Czy można mieć jedną pulę zaplecza obsługujący wiele aplikacji w różnych portów**

Architektura usługi Micro jest obsługiwana. Będzie potrzebny wiele ustawień protokołu http skonfigurowany do sondowania w różnych portów.

**Q. Niestandardowe sond obsługują symboli wieloznacznych/regex na odpowiedź na żądanie danych?**

Niestandardowe sond nie obsługują symboli wieloznacznych i wyrażeń regularnych na dane odpowiedzi. 

**Q. Jak są przetwarzane reguły**

Reguły są przetwarzane w kolejności ich konfiguracji. Zaleca się, że obejmujący wiele lokacji skonfigurowanych reguł przed podstawowych reguł, aby zmniejszyć prawdopodobieństwo, że ruch jest kierowany do nieodpowiednie wewnętrznej bazy danych jako podstawowe reguły będzie zgodny z ruchu na podstawie portu przed Trwa obliczanie reguły obejmujący wiele lokacji.

**Q. Co to oznaczającego pole hosta dla niestandardowych sond?**

Pole host Określa nazwę do badania do wysłania. Dotyczy tylko wtedy, gdy obejmujący wiele lokacji jest skonfigurowany dla bramy aplikacji, w przeciwnym razie użyj '127.0.0.1'. Ta wartość jest inna niż nazwa hosta maszyny Wirtualnej i jest w formacie \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\>.

**Q. Czy mogę dozwolonych bramy aplikacji dostęp do źródła kilka adresów IP?**

W tym scenariuszu można zrobić za pomocą grup NSG podsieci bramy aplikacji. Następujące ograniczenia powinien znajdować się w podsieci, wymienionych według priorytetu:

* Zezwalaj na ruch przychodzący z zakresu adresów IP/IP źródła.

* Zezwalaj na przychodzące żądania ze wszystkich źródeł do portów 65503 65534 dla [zaplecza kondycji komunikacji](application-gateway-diagnostics.md).

* Zezwalaj na przychodzące sondy modułu równoważenia obciążenia Azure (znacznik AzureLoadBalancer) i przychodzącego ruchu w sieci wirtualnej (znacznik VirtualNetwork) na [NSG](../virtual-network/security-overview.md).

* Blokuj wszystkie pozostałe ruch przychodzący z odmowy wszystkie reguły.

* Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

## <a name="performance"></a>Wydajność

**Q. Jak bramy aplikacji obsługuje wysoką dostępność i skalowalność?**

Brama aplikacji w obsługuje realizację scenariuszy wysokiej dostępności, jeśli masz co najmniej dwa wystąpienia wdrożone. Azure dystrybuuje te wystąpienia domen aktualizacji i odporności, aby upewnić się, że wszystkie wystąpienia nie zakończyć się niepowodzeniem w tym samym czasie. Brama aplikacji w obsługuje skalowalność, dodając wiele wystąpień tej samej bramy w celu udostępniania obciążenia.

**Q. Jak uzyskać scenariusza odzyskiwania po awarii między centrami danych z bramą aplikacji?**

Klientów można użyć Menedżera ruchu rozłożenie ruchu sieciowego między wiele bram aplikacji w różnych centrach danych.

**Q. Czy automatyczne skalowanie obsługiwane?**

Nie, ale brama aplikacji ma Metryka przepływności, który może służyć do alert po osiągnięciu progu. Ręczne dodawanie wystąpień lub zmiana rozmiaru nie jest ponownie uruchamiany bramy i nie ma wpływu na istniejące ruchu.

**Q. Czy ręczne skalowania w górę/dół Przyczyna Przestój?**

Nie istnieje bez przestojów, wystąpienia są rozproszone na uaktualnienia domen i domen błędów.

**Q. Czy opróżnianie połączenia obsługi bramy aplikacji?**

Tak. Można skonfigurować połączenia opróżnianie zmiany elementów członkowskich w puli zaplecza bez zakłóceń. Pozwoli to istniejących połączeń kontynuować do wysłania do ich poprzedniego miejsca docelowego, dopóki to połączenie jest zamykane albo można skonfigurować limit czasu wygaśnięcia. Należy pamiętać, opróżnianie oczekuje tylko dla bieżącego połączenia locie do ukończenia tego połączenia. Brama aplikacji nie są znane stanu sesji aplikacji.

**Q. Co to są rozmiary bramy aplikacji?**

Usługa Application Gateway jest obecnie oferowana w trzech rozmiarach: małym (**Small**), średnim (**Medium**) i dużym (**Large**). Rozmiary małych wystąpień są przeznaczone na potrzeby programowania i scenariuszy testowania.

Można utworzyć maksymalnie 50 bram aplikacji na subskrypcję, a każda brama aplikacji może mieć maksymalnie 10 wystąpień. Każda brama aplikacji może składać się z 20 odbiorników HTTP. Pełna lista limitów usługi Application Gateway znajduje się na stronie [ograniczeń usługi Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

W poniższej tabeli przedstawiono przepływność przy średniej wydajności dla każdego wystąpienia bramy aplikacji z włączonym obciążeniem SSL:

| Średni rozmiar odpowiedzi strony zaplecza | Small | Medium | Large |
| --- | --- | --- | --- |
| 6KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Są to przybliżone wartości przepływności bramy aplikacji. Rzeczywista przepływność zależy od różnorodnych szczegółów środowiska, takich jak średni rozmiar strony, lokalizacja wystąpień zaplecza i czas przetwarzania potrzebny do obsługi strony. Aby uzyskać dokładne wartości wydajności, należy przeprowadzić własne testy. Te wartości są podane tylko jako wskazówki na potrzeby planowania pojemności.

**Q. Czy mogę zmienić rozmiar wystąpienia z nośnika dużych bez zakłóceń**

Tak, Azure dystrybuuje wystąpień domen aktualizacji i odporności, aby upewnić się, że wszystkie wystąpienia nie zakończyć się niepowodzeniem w tym samym czasie. Brama aplikacji w obsługuje skalowania, dodając wiele wystąpień tej samej bramy w celu udostępniania obciążenia.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL

**Q. Jakie certyfikaty są obsługiwane w bramie aplikacji?**

Z podpisem własnym certyfikatami, certyfikaty urzędu certyfikacji i certyfikatów — symbol wieloznaczny są obsługiwane. Weryfikacją certyfikaty nie są obsługiwane.

**Q. Jakie są bieżące mechanizmów szyfrowania obsługiwanych przez bramę aplikacji?**

Poniżej przedstawiono bieżące mechanizmów szyfrowania obsługiwanych przez bramę aplikacji. Odwiedź stronę: [Konfigurowanie SSL wersji zasad i mechanizmów szyfrowania w bramie aplikacji](application-gateway-configure-ssl-policy-powershell.md) Aby dowiedzieć się, jak dostosować opcje protokołu SSL.

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

**Q. Brama aplikacji w również obsługuje ponownego szyfrowania ruchu do wewnętrznej bazy danych?**

Tak, Application Gateway obsługuje odciążanie protokołu SSL i SSL pełnego, który ponownie szyfruje ruch do wewnętrznej bazy danych.

**Q. Można skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL?**

Tak, można skonfigurować bramę aplikacji odrzucanie TLS1.0, TLS1.1 i TLS1.2. Protokół SSL 2.0 i 3.0 już są domyślnie wyłączone i nie są konfigurowalne.

**Q. Można skonfigurować mechanizmów szyfrowania i kolejność zasad?**

Tak, [konfiguracji mechanizmów szyfrowania](application-gateway-ssl-policy-overview.md) jest obsługiwana. Podczas definiowania zasad niestandardowych, należy włączyć co najmniej jeden z następujących mechanizmów szyfrowania. Brama aplikacji w używa SHA256 do zarządzania wewnętrznej bazy danych.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**Q. Jak wiele certyfikatów SSL są obsługiwane?**

Maksymalnie 20 SSL certyfikaty są obsługiwane.

**Q. Jak wiele certyfikatów uwierzytelniania w celu ponownego szyfrowania wewnętrznej bazy danych są obsługiwane?**

Maksymalnie 10 uwierzytelniania certyfikatów są obsługiwane z domyślną 5.

**Q. Czy brama aplikacji można zintegrować z usługą Azure Key Vault natywnie?**

Nie, nie jest zintegrowany z usługą Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfiguracja zapory (WAF) dla aplikacji sieci Web

**Q. Jednostka SKU zapory aplikacji sieci Web oferuje wszystkie funkcje dostępne w wersji Standard?**

Tak, zapory aplikacji sieci Web obsługuje wszystkie funkcje w wersji Standard.

**Q. Co to jest wersja CRS Application Gateway obsługuje?**

Brama aplikacji w obsługuje znaki CR [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**Q. Jak monitorować zapory aplikacji sieci Web?**

Zapory aplikacji sieci Web jest monitorowany za pośrednictwem rejestrowania diagnostycznego, więcej informacji na temat rejestrowania diagnostycznego można znaleźć w folderze [rejestrowania diagnostyki i metryki bramy aplikacji](application-gateway-diagnostics.md)

**Q. Tryb wykrywania blokowania ruchu?**

Nie, Tryb wykrywania rejestruje tylko ruch, który jest wyzwalana reguła zapory aplikacji sieci Web.

**Q. Jak dostosować reguły zapory aplikacji sieci Web?**

Tak, można dostosowywać, aby uzyskać więcej informacji na temat sposobu Dostosuj je, odwiedź stronę są reguły zapory aplikacji sieci Web [grup reguł zapory aplikacji sieci Web dostosować i reguł](application-gateway-customize-waf-rules-portal.md)

**Q. Jakie reguły są obecnie dostępne?**

Zapory aplikacji sieci Web obsługuje obecnie CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), które zapewniają planu bazowego zabezpieczeń przed większość 10 pierwszych luk w zabezpieczeniach przez otwarty sieci Web aplikacji zabezpieczeń projektu (OWASP) znaleźć tutaj [ OWASP 10 pierwszych luk w zabezpieczeniach](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Ochrona przed atakami polegającymi na iniekcji SQL

* Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

* Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

* Ochrona przed naruszeniami protokołu HTTP

* Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

* Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

 * Wykrycie typowych błędów konfiguracji aplikacji (czyli Apache usług IIS, itp.)

**Q. Czy zapory aplikacji sieci Web obsługuje również zapobiegania DDoS?**

Nie, zapory aplikacji sieci Web nie ma DDoS zapobiegania.

## <a name="diagnostics-and-logging"></a>Rejestrowanie i Diagnostyka

**Q. Jakie rodzaje dzienniki są dostępne z bramy aplikacji?**

Istnieją trzy dzienniki dostępne dla bramy aplikacji. Aby uzyskać więcej informacji na te dzienniki i inne funkcje diagnostyczne, odwiedź stronę [zaplecza kondycji, dzienników diagnostyki i metryki bramy aplikacji](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** — dziennik dostępu zawiera każdego żądania przesłane do serwera sieci Web Application Gateway. Dane obejmują wywołującego IP, adres URL żądanego, czas oczekiwania na odpowiedź, i wylogowywanie zwracają kod, bajtów. Dziennik dostępu są gromadzone co 300 sekund. Ten dziennik zawiera jeden rekord dla każdego wystąpienia bramy aplikacji.
- **ApplicationGatewayPerformanceLog** — dziennik wydajności znajdują się informacje o wydajności dla poszczególnych wystąpień całkowita liczba żądań podawana, w tym przepływność w bajtach, całkowita liczba żądań obsłużonych, liczba nieudanych żądań, zaplecza dobrej kondycji i złej kondycji Liczba wystąpień.
- **ApplicationGatewayFirewallLog** — dziennik zapory zawiera żądań, które są rejestrowane za pomocą wykrywania i zapobiegania tryb bramę aplikacji, który jest skonfigurowany z zapory aplikacji sieci web.

**Q. Jak sprawdzić, czy Moje elementy członkowskie puli zaplecza są w dobrej kondycji?**

Można użyć polecenia cmdlet programu PowerShell `Get-AzureRmApplicationGatewayBackendHealth` lub Sprawdź kondycję za pośrednictwem portalu, odwiedzając [diagnostyki bramy aplikacji](application-gateway-diagnostics.md)

**Q. Co to jest zasady przechowywania dzienników diagnostyki?**

Dzienniki diagnostyczne przepływu do konta magazynu klientów i klientów można ustawić zasad przechowywania na podstawie swoich preferencji. Dzienniki diagnostyczne mogą być również wysyłane do Centrum zdarzeń lub analizy dzienników. Odwiedź stronę [diagnostyki bramy aplikacji](application-gateway-diagnostics.md) więcej szczegółów.

**Q. Jak uzyskać dzienniki inspekcji dla bramy aplikacji?**

Dzienniki inspekcji są dostępne dla bramy aplikacji. W portalu kliknij **dziennik aktywności** w bloku menu bramę aplikacji uzyskać dostępu do dziennika inspekcji. 

**Q. Można ustawić alerty z bramy aplikacji?**

Tak, bramy aplikacji obsługi alertów, alerty są konfigurowane poza metryki. Brama aplikacji w aktualnie ma metrykę "przepływności", która może być skonfigurowana do alertu. Aby dowiedzieć się więcej o alertach, odwiedź stronę [otrzymywać powiadomienia o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**Q. Kondycja wewnętrznej bazy danych zwraca nieznany stan, w poznaniu przyczyny tego stanu?**

Najczęstszą przyczyną jest dostęp do wewnętrznej bazy danych jest blokowana przez NSG lub niestandardowe DNS. Odwiedź stronę [zaplecza kondycji, rejestrowanie danych diagnostycznych i metryki bramy aplikacji](application-gateway-diagnostics.md) Aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat bramy aplikacji, skorzystaj [co to jest brama aplikacji w usłudze Azure?](overview.md)
