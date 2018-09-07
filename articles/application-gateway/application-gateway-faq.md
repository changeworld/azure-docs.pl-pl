---
title: Często zadawane pytania dotyczące usługi Azure Application Gateway
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Application Gateway
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 9/6/2018
ms.author: victorh
ms.openlocfilehash: e180f0912bdb9145f3d36492fb9bcdcb551037f0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022920"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Często zadawane pytania dotyczące usługi Application Gateway

## <a name="general"></a>Ogólne

**PYTANIA I ODPOWIEDZI. Co to jest usługa Application Gateway?**

Usługa Azure Application Gateway to kontroler dostarczania aplikacji (ADC) as a service, oferuje różnorodne możliwości dla aplikacji dla równoważenia obciążenia warstwy 7. Oferuje wysoko dostępne i skalowalne usługi, która jest w pełni zarządzana przez platformę Azure.

**PYTANIA I ODPOWIEDZI. Jakie funkcje obsługuje usługa Application Gateway?**

Usługa Application Gateway obsługuje odciążanie protokołu SSL i kompleksowej usługi SSL, zapory aplikacji sieci Web, koligacja sesji na podstawie plików cookie, adres url oparty na ścieżkach routingu, hostingu wielu witryn i innych. Aby uzyskać pełną listę obsługiwanych funkcji, odwiedź stronę [wprowadzenie do usługi Application Gateway](application-gateway-introduction.md)

**PYTANIA I ODPOWIEDZI. Jaka jest różnica między bramą aplikacji i modułu równoważenia obciążenia platformy Azure?**

Application Gateway to moduł równoważenia obciążenia warstwy 7, co oznacza, że działa z tylko ruch w sieci web (HTTP/HTTPS/WebSocket). Obsługuje funkcje takie jak kończenia żądań SSL, koligacja sesji na podstawie pliku cookie i działanie okrężne do Równoważenie obciążenia ruchu. Moduł równoważenia obciążenia, należy załadować równoważy ruch w warstwie 4 (TCP/UDP).

**PYTANIA I ODPOWIEDZI. Jakie protokoły obsługuje usługa Application Gateway?**

Usługa Application Gateway obsługuje HTTP, HTTPS i HTTP/2 oraz protokołu WebSocket.

**PYTANIA I ODPOWIEDZI. Jak usługa Application Gateway obsługuje protokołu HTTP/2**

Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się tylko obiekty nasłuchujące Application Gateway. Komunikacja do pul serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. 

Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy przykład fragmentu kodu programu Azure PowerShell pokazuje, jak możesz je włączyć:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**PYTANIA I ODPOWIEDZI. Jakie zasoby są obecnie obsługiwane w ramach puli wewnętrznej bazy danych?**

Pule zaplecza może składać się z kartami sieciowymi, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowaną nazwę (FQDN), a wielodostępne zaplecza takich jak usługa Azure Web Apps. Składowych puli zaplecza bramy aplikacji nie są związane z zestawu dostępności. Elementami członkowskimi pul zaplecza może być w klastrach, centrów danych lub spoza platformy Azure, tak długo, jak długo mają łączność adresów IP.

**PYTANIA I ODPOWIEDZI. Jakie regiony usługa jest dostępna w?**

Usługa Application Gateway jest dostępna we wszystkich regionach, globalnej platformy Azure. Jest również dostępna w [Azure (Chiny)](https://www.azure.cn/) i [Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/)

**PYTANIA I ODPOWIEDZI. Jest to dedykowane wdrożenia dla mojej subskrypcji lub jest udostępniana wielu odbiorców?**

Usługa Application Gateway to dedykowane wdrożenie w sieci wirtualnej.

**PYTANIA I ODPOWIEDZI. HTTP jest -> przekierowania protokołu HTTPS, obsługiwane?**

Przekierowywanie jest obsługiwane. Odwiedź stronę [Application Gateway redirect overview](application-gateway-redirect-overview.md) Aby dowiedzieć się więcej.

**PYTANIA I ODPOWIEDZI. W jakiej kolejności odbiorników przetwarzania?**

Odbiorniki są przetwarzane w kolejności, w której są wyświetlane. Dlatego jeśli podstawowy odbiornik pasuje do przychodzącego żądania przetwarza je najpierw.  Odbiorników obejmujących wiele lokacji, należy skonfigurować przed odbiornika podstawowego, aby upewnić się, że ruch jest kierowany do poprawne zaplecza.

**PYTANIA I ODPOWIEDZI. Gdzie znaleźć adres IP bramy aplikacji i DNS**

Korzystając z publicznego adresu IP jako punkt końcowy, te informacje można znaleźć na zasób publicznego adresu IP lub na stronie przeglądu usługi Application Gateway w portalu. Dla wewnętrznych adresów IP to można znaleźć na stronie Przegląd.

**PYTANIA I ODPOWIEDZI. Adres IP lub DNS zmienia się w okresie istnienia usługi Application Gateway?**

Wirtualne adresy IP można zmienić, jeśli brama zostanie zatrzymana i uruchomiona przez klienta. DNS skojarzone z usługą Application Gateway nie zmienia się z cyklem bramy. Z tego powodu zaleca się użycie aliasów CNAME i wskaż adresu DNS bramy aplikacji.

**PYTANIA I ODPOWIEDZI. Usługa Application Gateway obsługuje statyczny adres IP?**

Nie, usługa Application Gateway obsługuje tylko statyczne publiczne adresy IP, ale obsługuje ona statyczne wewnętrzne adresy IP.

**PYTANIA I ODPOWIEDZI. Usługa Application Gateway obsługuje wiele publicznych adresów IP w ramach bramy?**

Tylko jeden publiczny adres IP jest obsługiwany w bramie aplikacji.

**PYTANIA I ODPOWIEDZI. Jak duże powinny utworzyć mojej podsieci dla bramy Application Gateway?**

Bramy Application Gateway zużywa jeden prywatny adres IP dla każdego wystąpienia, a także innym prywatnym adresem IP, jeśli konfiguracji IP frontonu prywatnego jest skonfigurowany. Ponadto platforma Azure rezerwuje pierwsze cztery i ostatniego adresu IP w każdej podsieci, do wewnętrznego użycia.
Na przykład, jeśli usługa Application Gateway jest ustawiona na trzy wystąpienia i nie adresu IP frontonu prywatnych, następnie wartość/29 podsieć, rozmiar lub nowszej jest wymagana. W takim przypadku usługa Application Gateway używa trzech adresów IP. Jeśli masz trzy wystąpienia i adres IP dla prywatnych konfiguracji adresu IP frontonu, następnie o rozmiarze/28 podsieć, rozmiar lub nowszej jest wymagany, ponieważ cztery adresy IP są wymagane.

**PYTANIA I ODPOWIEDZI. Usługa Application Gateway obsługuje nagłówków x-forwarded dla?**

Tak, usługa Application Gateway wstawia nagłówków x-forwarded dla x-forwarded-proto i x-forwarded-port na żądanie przekazywane do zaplecza. Format dla nagłówka x-forwarded dla jest IP:Port listę rozdzielonych przecinkami. Prawidłowe wartości dla x-forwarded-proto są http lub https. X-forwarded-port Określa port, w którym żądanie osiągnięty w usłudze Application Gateway.

Usługa Application Gateway są wstawiane nagłówek X-oryginalny-Host, zawierający oryginalnego nagłówka hosta, z którym odebrano żądanie. Tego pliku nagłówkowego jest przydatne w scenariuszach, takich jak integracja z witryny internetowej platformy Azure, gdzie przychodzącego nagłówka hosta jest modyfikowany, zanim ruch jest kierowany do wewnętrznej bazy danych.

**PYTANIA I ODPOWIEDZI. Jak długo trwa wdrażanie bramy aplikacji? Moje bramy Application Gateway nadal działa podczas aktualizowania?**

Nowe wdrożenia bramy aplikacji może potrwać do 20 minut do aprowizowania. Zmiany rozmiaru/liczba wystąpień nie są naturalnymi, a brama pozostaje aktywna, w tym czasie.

## <a name="configuration"></a>Konfigurowanie

**PYTANIA I ODPOWIEDZI. Usługa Application Gateway zawsze wdrożono w sieci wirtualnej?**

Tak, usługa Application Gateway jest zawsze wdrażane w podsieci sieci wirtualnej. Ta podsieć może zawierać tylko bramy Application Gateway.

**PYTANIA I ODPOWIEDZI. Usługa Application Gateway skontaktować się z wystąpieniami poza siecią wirtualną?**

Usługa Application Gateway skontaktować się z wystąpieniami spoza sieci wirtualnej, która jest tak długo, jak istnieje łączność IP. Jeśli planujesz używać wewnętrznych adresów IP jako składowych puli zaplecza, a następnie wymaga [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) lub [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**PYTANIA I ODPOWIEDZI. Czy mogę wdrożyć niczego więcej w podsieci usługi Application Gateway?**

Nie, ale można wdrożyć inne bramy aplikacji w podsieci.

**PYTANIA I ODPOWIEDZI. Sieciowe grupy zabezpieczeń obsługujące w podsieci bramy Application Gateway?**

Sieciowe grupy zabezpieczeń są obsługiwane w podsieci bramy aplikacji z następującymi zastrzeżeniami:

* Wyjątki, należy je umieścić w przychodzący ruch na portach 65503 65534. Zakres portów jest wymagany do komunikacji infrastruktury platformy Azure. Są one zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez prawidłowych certyfikatów podmioty zewnętrzne, w tym klienci tych bram, nie będzie można zainicjować wszelkie zmiany w tych punktach końcowych.

* Nie można zablokować wychodzące połączenie z Internetem.

* Ruch z AzureLoadBalancer tag musi być zablokowany.

**PYTANIA I ODPOWIEDZI. Czy trasy zdefiniowane przez użytkownika są obsługiwane w podsieci bramy aplikacji?**

Trasy zdefiniowane przez użytkownika (Udr) są obsługiwane w podsieci bramy aplikacji, tak długo, jak nie wpływają one komunikacji end-to-end żądania/odpowiedzi.

Na przykład można skonfigurować trasy zdefiniowanej przez użytkownika w podsieci bramy aplikacji, aby wskazywał urządzenie zapory dla inspekcję pakietów, ale należy upewnić się, że pakiet może osiągnąć jego przeznaczenia wpis inspekcji. Niewykonanie tej czynności może spowodować nieprawidłowe kondycji sondy lub ruch zachowania routingu. Obejmuje to zapamiętane trasy lub domyślne trasy 0.0.0.0/0 propagowane przez usługi ExpressRoute lub bram sieci VPN w sieci wirtualnej.

**PYTANIA I ODPOWIEDZI. Jakie są limity dla usługi Application Gateway? Czy można zwiększyć te limity?**

Odwiedź stronę [limitów usługi Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits) wyświetlić limitów.

**PYTANIA I ODPOWIEDZI. Do czego służy usługa Application Gateway dla wewnętrznych i zewnętrznych ruchu jednocześnie?**

Tak, usługa Application Gateway obsługuje jeden wewnętrzny adres IP i jeden zewnętrzny adres IP dla bramy Application Gateway.

**PYTANIA I ODPOWIEDZI. Jest komunikacja równorzędna sieci wirtualnych obsługiwane?**

Tak, komunikacja równorzędna sieci wirtualnych jest obsługiwana i jest korzystne dla Równoważenie obciążenia ruchu w innych sieciach wirtualnych.

**PYTANIA I ODPOWIEDZI. Może mi odpowiedzieć na lokalnych serwerach łączenia tuneli ExpressRoute lub sieci VPN?**

Tak, tak długo, jak ruch jest dozwolony.

**PYTANIA I ODPOWIEDZI. Może mieć jednej puli wewnętrznej bazy danych obsługująca wiele aplikacji na różnych portów?**

Architektura Micro usługi jest obsługiwany. Będziesz potrzebować wiele ustawień protokołu http skonfigurowany do sondowania w różnych portów.

**PYTANIA I ODPOWIEDZI. Niestandardowe sondy obsługują symbole wieloznaczne/wyrażeń regularnych na dane odpowiedzi?**

Niestandardowe sondy nie obsługują symboli wieloznacznych lub wyrażenie regularne na dane odpowiedzi. 

**PYTANIA I ODPOWIEDZI. Jak są przetwarzane reguły**

Reguły są przetwarzane w kolejności, w której są one konfigurowane. Zalecane jest, czy połączenia obejmujące wiele lokacji skonfigurowanych reguł przed podstawowych reguł, aby zmniejszyć prawdopodobieństwo, że ruch jest kierowany do zaplecza nieodpowiedni jako podstawowa zasada będzie odpowiadać na ruch na podstawie portu przed reguły obejmujące wiele lokacji oceniane.

**PYTANIA I ODPOWIEDZI. Co to oznaczającego pole Host dla sond niestandardowych?**

Pole host Określa nazwę aby wysłać sondy. Dotyczy tylko wtedy, gdy połączenia obejmujące wiele lokacji jest skonfigurowany w usłudze Application Gateway, w przeciwnym razie użyj "127.0.0.1". Ta wartość jest inna niż nazwa hosta maszyny Wirtualnej i jest w formacie \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\>.

**PYTANIA I ODPOWIEDZI. Czy można umieścić na liście dozwolonych bramy aplikacji dostęp do kilku źródłowych adresów IP?**

W tym scenariuszu można zrobić za pomocą sieciowych grup zabezpieczeń w podsieci bramy aplikacji. Następujące ograniczenia powinny znajdować się w podsieci w określonej kolejności priorytetu:

* Zezwalaj na ruch przychodzący ze źródłowego zakresu adresów IP/adresu IP.

* Zezwalaj na przychodzące żądania pochodzące ze wszystkich źródeł porty 65503 65534 [komunikacji kondycji zaplecza](application-gateway-diagnostics.md). Zakres tego portu jest wymagany do komunikacji infrastruktury platformy Azure. Są one zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez prawidłowych certyfikatów podmioty zewnętrzne, w tym klienci tych bram, nie będzie można zainicjować wszelkie zmiany w tych punktach końcowych.

* Zezwalaj na przychodzące sondy usługi Azure Load Balancer (AzureLoadBalancer tag) oraz dla ruchu przychodzącego ruchu w sieci wirtualnej (znacznik VirtualNetwork) na [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md).

* Zablokuj wszystkie inne ruch przychodzący z odmowy wszystkie reguły.

* Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

**PYTANIA I ODPOWIEDZI. Można użyć tego samego portu dla prywatnych i publicznych detektorów mające połączenie z Internetem?**

Nie. Takie rozwiązanie nie jest obsługiwane.

## <a name="performance"></a>Wydajność

**PYTANIA I ODPOWIEDZI. Jak usługa Application Gateway obsługuje wysoką dostępność i skalowalność?**

Usługa Application Gateway obsługuje scenariuszy wysokiej dostępności, gdy masz co najmniej dwóch wystąpieniach wdrożonych. Platforma Azure dystrybuuje te wystąpienia między domenami aktualizacji i błędów, aby upewnić się, że wszystkie wystąpienia nie zakończy się niepowodzeniem w tym samym czasie. Usługa Application Gateway obsługuje skalowalności, dodając wiele wystąpień tej samej bramy, aby udostępnić obciążenia.

**PYTANIA I ODPOWIEDZI. Jak osiągnąć scenariusza odzyskiwania po awarii w centrach danych z usługą Application Gateway?**

Klientów można użyć usługi Traffic Manager do rozdzielenia ruchu między wiele bram aplikacji w różnych centrach danych.

**PYTANIA I ODPOWIEDZI. Czy automatyczne skalowanie jest obsługiwane?**

Nie, ale usługa Application Gateway ma pomiar przepływności, który może służyć do wyzwalania alertu, po osiągnięciu wartości progowej. Ręczne dodawanie wystąpień i zmienianie rozmiaru nie jest ponownie uruchamiany bramy i nie ma wpływu na istniejące ruchu.

**PYTANIA I ODPOWIEDZI. Skalowanie ręczne odbywa się w górę/dół przestój Przyczyna?**

Brak przestojów, wystąpienia są rozproszone między domeny błędów i domenach uaktualniania.

**PYTANIA I ODPOWIEDZI. Czy opróżniania połączenia Obsługa bramy aplikacji?**

Tak. Można skonfigurować połączenia opróżniania, aby zmienić członków w ramach puli zaplecza, bez przerw w działaniu. Pozwoli to na istniejące połączenia przejść do wysłania do ich poprzedniego miejsca docelowego, dopóki nie upłynie limit czasu można skonfigurować albo to połączenie jest zamykane. Należy pamiętać, opróżniania oczekuje tylko dla bieżącego połączenia śledząc do ukończenia tego połączenia. Usługa Application Gateway nie ma informacji o stanie sesji w aplikacji.

**PYTANIA I ODPOWIEDZI. Co to są rozmiarów bramy aplikacji?**

Usługa Application Gateway jest obecnie oferowana w trzech rozmiarach: małym (**Small**), średnim (**Medium**) i dużym (**Large**). Rozmiary małych wystąpień są przeznaczone na potrzeby programowania i scenariuszy testowania.

Można utworzyć maksymalnie 50 bram aplikacji na subskrypcję, a każda brama aplikacji może mieć maksymalnie 10 wystąpień. Każda brama aplikacji może składać się z 20 odbiorników HTTP. Pełna lista limitów usługi Application Gateway znajduje się na stronie [ograniczeń usługi Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

W poniższej tabeli przedstawiono przepływność przy średniej wydajności dla każdego wystąpienia bramy aplikacji z włączonym obciążeniem SSL:

| Średni rozmiar odpowiedź strony zaplecza | Small | Medium | Large |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Są to przybliżone wartości przepływności bramy aplikacji. Rzeczywista przepływność zależy od różnorodnych szczegółów środowiska, takich jak średni rozmiar strony, lokalizacja wystąpień zaplecza i czas przetwarzania potrzebny do obsługi strony. Aby uzyskać dokładne wartości wydajności, należy przeprowadzić własne testy. Te wartości są podane tylko jako wskazówki na potrzeby planowania pojemności.

**PYTANIA I ODPOWIEDZI. Czy mogę zmienić rozmiar wystąpienia średnie dużych bez zakłóceń?**

Tak, platforma Azure rozdzieli wystąpień w domenach aktualizacji i błędów, aby upewnić się, że wszystkie wystąpienia nie zakończy się niepowodzeniem w tym samym czasie. Usługa Application Gateway obsługuje skalowanie, dodając wiele wystąpień tej samej bramy, aby udostępnić obciążenia.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL

**PYTANIA I ODPOWIEDZI. Jakie certyfikaty są obsługiwane w usłudze Application Gateway?**

Z podpisem własnym, certyfikatów i certyfikaty urzędu certyfikacji i certyfikatów z symbolami wieloznacznymi są obsługiwane. Weryfikacją certyfikatów nie są obsługiwane.

**PYTANIA I ODPOWIEDZI. Jakie są bieżące mechanizmów szyfrowania obsługiwanych przez usługę Application Gateway?**

Poniżej przedstawiono bieżący mechanizmów szyfrowania obsługiwanych przez usługę application gateway. Odwiedź stronę: [Konfigurowanie protokołu SSL wersje zasad i mechanizmów szyfrowania w usłudze Application Gateway](application-gateway-configure-ssl-policy-powershell.md) dowiesz się, jak dostosować opcje protokołu SSL.

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

**PYTANIA I ODPOWIEDZI. Usługa Application Gateway również obsługuje ponownie szyfrować ruchu do wewnętrznej bazy danych?**

Tak, usługa Application Gateway obsługuje odciążanie protokołu SSL i kompleksowej usługi SSL, który ponownie szyfruje ruch do wewnętrznej bazy danych.

**PYTANIA I ODPOWIEDZI. Można skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL?**

Tak, można skonfigurować bramę aplikacji do odmowy TLS1.0 TLS1.1 i TLS1.2. Protokół SSL 2.0 i 3.0 już są domyślnie wyłączone i nie można konfigurować.

**PYTANIA I ODPOWIEDZI. Mechanizmy szyfrowania i kolejności zasad można skonfigurować?**

Tak, [konfiguracji mechanizmów szyfrowania](application-gateway-ssl-policy-overview.md) jest obsługiwana. Podczas definiowania zasad niestandardowych, należy włączyć co najmniej jeden z następujących mechanizmów szyfrowania. Usługa Application gateway używa SHA256 do wewnętrznej bazy danych zarządzania.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**PYTANIA I ODPOWIEDZI. Jak wiele certyfikatów SSL są obsługiwane?**

Maksymalnie 20 SSL certyfikaty są obsługiwane.

**PYTANIA I ODPOWIEDZI. Jak wiele certyfikatów uwierzytelniania dla wewnętrznej bazy danych ponownie szyfrować są obsługiwane?**

Maksymalnie 10 uwierzytelniania certyfikatów są obsługiwane z domyślną 5.

**PYTANIA I ODPOWIEDZI. Czy usługa Application Gateway można zintegrować z usługą Azure Key Vault natywnie?**

Nie, nie jest on zintegrowany z usługą Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Konfiguracja zapory aplikacji sieci Web

**PYTANIA I ODPOWIEDZI. Jednostka SKU zapory aplikacji sieci Web oferuje wszystkie funkcje dostępne w wersji Standard?**

Tak, zapory aplikacji sieci Web obsługuje wszystkie funkcje standardowej jednostki SKU.

**PYTANIA I ODPOWIEDZI. Co to jest wersja CRS Application Gateway obsługuje?**

Usługa Application Gateway obsługuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**PYTANIA I ODPOWIEDZI. Jak monitorować zapory aplikacji sieci Web?**

Zapora aplikacji sieci Web jest monitorowany za pośrednictwem rejestrowania diagnostycznego, więcej informacji na temat rejestrowania diagnostycznego można znaleźć w folderze [rejestrowanie diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md)

**PYTANIA I ODPOWIEDZI. Tryb wykrywania blokować ruch?**

Nie, Tryb wykrywania dzienniki ruchu, która wywołała regułę zapory aplikacji sieci Web.

**PYTANIA I ODPOWIEDZI. Jak dostosować reguły zapory aplikacji sieci Web?**

Tak, można dostosowywać, aby uzyskać więcej informacji na temat sposobu ich dostosowywania, odwiedź stronę są reguły zapory aplikacji sieci Web [zapory aplikacji sieci Web dostosowywanie grup reguł i reguł](application-gateway-customize-waf-rules-portal.md)

**PYTANIA I ODPOWIEDZI. Jakie zasady są aktualnie dostępne?**

Zapora aplikacji sieci Web obsługuje obecnie CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), które zapewniają linii bazowej zabezpieczeń przed większością z 10 najważniejszych luk w zabezpieczeniach, zidentyfikowane przez Otwórz sieci Web aplikacji Security Project (OWASP) znaleźć tutaj [ Luk w zabezpieczeniach OWASP top 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Ochrona przed atakami polegającymi na iniekcji SQL

* Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

* Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

* Ochrona przed naruszeniami protokołu HTTP

* Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

* Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

 * Wykrywanie typowych błędów konfiguracji aplikacji (czyli Apache, usługi IIS itp.)

**PYTANIA I ODPOWIEDZI. Zapora aplikacji sieci Web również obsługuje zapobiegania przed atakami DDoS?**

Tak. Można włączyć ochrony przed atakami DDos w sieci wirtualnej wdrożonym bramy aplikacji. Gwarantuje to, że brama aplikacji, który adres VIP jest również chroniony za pomocą usługi Azure DDos Protection.

## <a name="diagnostics-and-logging"></a>Rejestrowanie i Diagnostyka

**PYTANIA I ODPOWIEDZI. Jakie rodzaje dzienniki są dostępne z usługą Application Gateway?**

Istnieją trzy dzienniki usługi Application Gateway. Aby uzyskać więcej informacji na temat tych dzienników i innych funkcji diagnostycznych, odwiedź stronę [zaplecza kondycji, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog** — dziennik dostępu zawiera każde żądanie przesyłane do serwera sieci Web bramy aplikacji. Dane obejmują IP obiektu wywołującego, adresu URL żądanego, opóźnienie odpowiedzi, pomniejszać i zwracają kod, w bajtach. Dziennik dostępu są gromadzone co 300 sekund. Ten dziennik zawiera jeden rekord dla każdego wystąpienia bramy aplikacji.
- **ApplicationGatewayPerformanceLog** — dziennik wydajności przechwytuje informacje dotyczące wydajności na na podstawie wystąpień w tym całkowity żądania obsłużonego, przepływność w bajtach, łączna liczba żądań one obsługiwane, a liczba nieudanych żądań zdrowe i niezdrowe zaplecza Liczba wystąpień.
- **ApplicationGatewayFirewallLog** — dziennik zapory zawiera żądań, które są rejestrowane w trybie wykrywania i zapobiegania usługi application gateway, który jest skonfigurowany przy użyciu zapory aplikacji sieci web.

**PYTANIA I ODPOWIEDZI. Jak sprawdzić, jeśli Moja składowych puli zaplecza są w dobrej kondycji?**

Można użyć polecenia cmdlet programu PowerShell `Get-AzureRmApplicationGatewayBackendHealth` lub sprawdzać kondycję, za pośrednictwem portalu, odwiedzając [diagnostyki bramy aplikacji](application-gateway-diagnostics.md)

**PYTANIA I ODPOWIEDZI. Jakie są zasady przechowywania dzienników diagnostycznych?**

Dzienniki diagnostyczne usługi flow do konta magazynu klientów i klienci mogą ustawić zasady przechowywania na podstawie jego preferencji. Dzienniki diagnostyczne mogą być również wysyłane do usługi Log Analytics lub Centrum zdarzeń. Odwiedź stronę [diagnostyki bramy aplikacji](application-gateway-diagnostics.md) Aby uzyskać więcej informacji.

**PYTANIA I ODPOWIEDZI. Jak uzyskać dzienniki inspekcji w usłudze Application Gateway?**

Dzienniki inspekcji są dostępne w usłudze Application Gateway. W portalu, kliknij przycisk **dziennika aktywności** w bloku menu bramy aplikacji dostępu do dziennika inspekcji. 

**PYTANIA I ODPOWIEDZI. Można ustawić alerty z usługą Application Gateway?**

Tak, usługa Application Gateway obsługuje alerty, alerty są konfigurowane poza metryki. Usługa Application Gateway ma obecnie metryki "przepływności", którego można skonfigurować alert. Aby dowiedzieć się więcej o alertach, odwiedź stronę [otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**PYTANIA I ODPOWIEDZI. Kondycja wewnętrznej bazy danych zwraca stan nieznany w poznaniu przyczyny tego stanu?**

Najbardziej typową przyczyną jest dostęp do wewnętrznej bazy danych jest blokowane przez sieciową grupę zabezpieczeń lub niestandardowe DNS. Odwiedź stronę [zaplecza kondycji, rejestrowanie diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md) Aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat bramy Application Gateway, odwiedź stronę [co to jest Azure Application Gateway?](overview.md)
