---
title: Składniki bramy aplikacji
description: Ten artykuł zawiera informacje o różnych składnikach w bramie aplikacji
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133096"
---
# <a name="application-gateway-components"></a>Składniki bramy aplikacji

 Brama aplikacji służy jako pojedynczy punkt kontaktowy dla klientów. Dystrybuuje ruch aplikacji przychodzących w wielu pulach zaplecza, które obejmują maszyny wirtualne platformy Azure, zestawy skalowania maszyny wirtualnej, usługę Azure App Service i serwery lokalne/zewnętrzne. Aby rozpowszechniać ruch, brama aplikacji używa kilku składników opisanych w tym artykule.

![Składniki używane w bramie aplikacji](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Adresy IP frontu

Adres IP frontendu to adres IP skojarzony z bramą aplikacji. Bramę aplikacji można skonfigurować tak, aby miała publiczny adres IP, prywatny adres IP lub oba te elementy. Brama aplikacji obsługuje jeden publiczny lub jeden prywatny adres IP. Sieć wirtualna i publiczny adres IP muszą znajdować się w tej samej lokalizacji co brama aplikacji. Po jego utworzeniu adres IP wewnętrznej bazy jest skojarzony z odbiornikiem.

### <a name="static-versus-dynamic-public-ip-address"></a>Statyczny a dynamiczny publiczny adres IP

Jednostka SKU usługi Azure Application Gateway v2 może być skonfigurowana do obsługi zarówno statycznego wewnętrznego adresu IP, jak i statycznego publicznego adresu IP lub tylko statycznego publicznego adresu IP. Nie można skonfigurować do obsługi tylko statycznego wewnętrznego adresu IP.

Jednostka SKU w wersji 1 może być skonfigurowana do obsługi statycznego lub dynamicznego wewnętrznego adresu IP i dynamicznego publicznego adresu IP. Dynamiczny adres IP bramy aplikacji nie zmienia się w uruchomionej bramie. Można go zmienić tylko po zatrzymaniu lub uruchomieniu bramy. Nie zmienia się w przypadku awarii systemu, aktualizacji, aktualizacji hosta platformy Azure itp. 

Nazwa DNS skojarzona z bramą aplikacji nie zmienia się w cyklu życia bramy. W związku z tym należy użyć aliasu CNAME i wskazać go na adres DNS bramy aplikacji.

## <a name="listeners"></a>Odbiorniki

Odbiornik jest jednostką logiczną, która sprawdza, czy przychodzące żądania połączenia. Odbiornik akceptuje żądanie, jeśli protokół, port, nazwa hosta i adres IP skojarzone z żądaniem są zgodne z tymi samymi elementami skojarzonymi z konfiguracją odbiornika.

Przed użyciem bramy aplikacji należy dodać co najmniej jeden odbiornik. Może być wielu odbiorników dołączonych do bramy aplikacji i mogą być używane dla tego samego protokołu.

Po wykryciu przez odbiornik przychodzących żądań od klientów brama aplikacji kieruje te żądania do członków w puli wewnętrznej bazy danych skonfigurowanych w regule.

Detektory obsługują następujące porty i protokoły.

### <a name="ports"></a>Porty

Port jest, gdy odbiornik nasłuchuje żądania klienta. Można skonfigurować porty od 1 do 65502 dla jednostki SKU w wersji 1 i od 1 do 65199 dla jednostki SKU w wersji 2.

### <a name="protocols"></a>Protokoły

Brama aplikacji obsługuje cztery protokoły: HTTP, HTTPS, HTTP/2 i WebSocket:
>[!NOTE]
>Obsługa protokołu HTTP/2 jest dostępna tylko dla klientów łączących się z detektorami bramy aplikacji. Komunikacja do pul serwerów wewnętrznej bazy danych jest zawsze za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Można go włączyć.

- Określ między protokołami HTTP i HTTPS w konfiguracji odbiornika.
- Obsługa [protokołów WebSockets i HTTP/2](features.md#websocket-and-http2-traffic) jest dostępna natywnie, a [obsługa WebSocket](application-gateway-websocket.md) jest domyślnie włączona. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. Użyj WebSockets zarówno http i https słuchaczy.

Użyj odbiornika HTTPS do zakończenia protokołu TLS. Odbiornik HTTPS odciąża pracę szyfrowania i odszyfrowywania do bramy aplikacji, dzięki czemu serwery sieci web nie są obciążone obciążeniem.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Brama aplikacji umożliwia tworzenie niestandardowych stron błędów zamiast wyświetlania domyślnych stron błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu. Brama aplikacji wyświetla niestandardową stronę błędu, gdy żądanie nie może dotrzeć do wewnętrznej bazy danych.

Aby uzyskać więcej informacji, zobacz [Niestandardowe strony błędów bramy aplikacji](custom-error.md).

### <a name="types-of-listeners"></a>Rodzaje słuchaczy

Istnieją dwa typy słuchaczy:

- **Podstawowe**. Ten typ odbiornika nasłuchuje jednej lokacji domeny, gdzie ma jedno mapowanie DNS na adres IP bramy aplikacji. Ta konfiguracja odbiornika jest wymagana podczas hosta pojedynczej lokacji za bramą aplikacji.

- **Wielozadaniowe**. Ta konfiguracja odbiornika jest wymagana podczas konfigurowania więcej niż jednej aplikacji sieci web w tym samym wystąpieniu bramy aplikacji. Umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie do 100 witryn sieci Web do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład trzy poddomeny, abc.contoso.com, xyz.contoso.com i pqr.contoso.com wskazują adres IP bramy aplikacji. Należy utworzyć trzy detektory wielu lokacji i skonfigurować każdy odbiornik dla odpowiedniego ustawienia portu i protokołu.

    Aby uzyskać więcej informacji, zobacz [Hosting wielu witryn](application-gateway-web-app-overview.md).

Po utworzeniu odbiornika należy skojarzyć go z regułą routingu żądań. Ta reguła określa, jak żądanie odebrane na odbiorniku powinny być kierowane do wewnętrznej bazy danych.

Brama aplikacji przetwarza detektory w [podanej kolejności](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Żądania reguł routingu

Reguła routingu żądań jest kluczowym składnikiem bramy aplikacji, ponieważ określa sposób kierowania ruchu na odbiorniku. Reguła wiąże odbiornika, pulę serwerów zaplecza i ustawienia HTTP wewnętrznej bazy danych.

Gdy odbiornik zaakceptuje żądanie, reguła routingu żądania przekazuje żądanie do wewnętrznej bazy danych lub przekierowuje go w inne miejsce. Jeśli żądanie jest przekazywane do wewnętrznej bazy danych, reguła routingu żądań określa, do której puli serwerów wewnętrznej bazy danych należy go przekazywać dalej. Reguła routingu żądań określa również, czy nagłówki w żądaniu mają zostać przepisane. Jeden odbiornik może być dołączony do jednej reguły.

Istnieją dwa typy reguł routingu żądań:

- **Podstawowe**. Wszystkie żądania w skojarzonym odbiorniku (na przykład blog.contoso.com/*) są przekazywane do skojarzonej puli wewnętrznej bazy danych przy użyciu skojarzonego ustawienia HTTP.

- **Oparte na ścieżce**. Ta reguła routingu umożliwia kierowanie żądań w skojarzonym odbiorniku do określonej puli wewnętrznej bazy danych, na podstawie adresu URL w żądaniu. Jeśli ścieżka adresu URL w żądaniu pasuje do wzorca ścieżki w regule opartej na ścieżce, reguła kieruje to żądanie. Stosuje wzorzec ścieżki tylko do ścieżki adresu URL, a nie do jego parametrów zapytania. Jeśli ścieżka adresu URL w żądaniu odbiornika nie pasuje do żadnej z reguł opartych na ścieżce, kieruje żądanie do domyślnej puli wewnętrznej bazy danych i ustawień HTTP.

Aby uzyskać więcej informacji, zobacz [Routing oparty na adresie URL](url-route-overview.md).

### <a name="redirection-support"></a>Obsługa przekierowania

Reguła routingu żądań umożliwia również przekierowanie ruchu na bramę aplikacji. Jest to ogólny mechanizm przekierowania, dzięki czemu można przekierować do iz dowolnego portu zdefiniowanego przy użyciu reguł.

Możesz wybrać cel przekierowania jako inny odbiornik (który może pomóc w włączeniu automatycznego przekierowania HTTP do HTTPS) lub witrynę zewnętrzną. Można również wybrać, aby przekierowanie było tymczasowe lub stałe lub dołączyć ścieżkę URI i ciąg zapytania do przekierowanej wiadomości URL.

Aby uzyskać więcej informacji, zobacz [Przekierowywanie ruchu na bramie aplikacji](redirect-overview.md).

### <a name="rewrite-http-headers"></a>Ponowne zapisywanie nagłówków HTTP

Korzystając z reguł routingu żądań, można dodawać, usuwać lub aktualizować nagłówki żądań i odpowiedzi HTTP(S), gdy pakiety żądań i odpowiedzi są przesuwające się między pulami klienta i wewnętrznej bazy danych za pośrednictwem bramy aplikacji.

Nagłówki można ustawić na wartości statyczne lub na inne nagłówki i zmienne serwera. Pomaga to w ważnych przypadkach użycia, takich jak wyodrębnianie adresów IP klienta, usuwanie poufnych informacji o wewnętrznej bazy danych, dodawanie większej ilości zabezpieczeń i tak dalej.

Aby uzyskać więcej informacji, zobacz [Przepisywanie nagłówków HTTP w bramie aplikacji](rewrite-http-headers.md).

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Brama aplikacji kieruje ruch do serwerów wewnętrznej bazy danych (określonych w regule routingu żądań, które zawierają ustawienia HTTP) przy użyciu numeru portu, protokołu i innych ustawień wyszczególnionych w tym składniku.

Port i protokół używany w ustawieniach HTTP określają, czy ruch między bramą aplikacji a serwerami wewnętrznej bazy danych jest szyfrowany (zapewniając kompleksowy protokół TLS) czy niezaszyfrowany.

Składnik ten jest również używany do:

- Określ, czy sesja użytkownika ma być przechowywana na tym samym serwerze przy użyciu [koligacji sesji opartej na plikach cookie](features.md#session-affinity).

- Bezpiecznie usuń elementy członkowskie puli wewnętrznej bazy danych przy użyciu [opróżniania połączenia](features.md#connection-draining).

- Skojarzyć niestandardową sondę do monitorowania kondycji wewnętrznej bazy danych, ustawić interwał limitu czasu żądania, zastąpić nazwę hosta i ścieżkę w żądaniu i zapewnić łatwość jednego kliknięcia, aby określić ustawienia zaplecza usługi aplikacji.

## <a name="backend-pools"></a>Pule zaplecza

Pula wewnętrznej bazy danych kieruje żądanie do serwerów wewnętrznej bazy danych, które obsługują żądanie. Pule zaplecza mogą zawierać:

- Karty interfejsów sieciowych
- Zestawy skalowania maszyn wirtualnych
- Publiczne adresy IP
- Wewnętrzne adresy IP
- Nazwa FQDN
- Wielodostępne zaplecze (takie jak usługa app service)

Elementy członkowskie puli wewnętrznej bazy danych bramy aplikacji nie są powiązane z zestawem dostępności. Brama aplikacji może komunikować się z wystąpieniami poza siecią wirtualną, w których się znajduje. W rezultacie członkowie pul zaplecza mogą znajdować się w klastrach, w centrach danych lub poza platformą Azure, o ile istnieje łączność IP.

Jeśli używasz wewnętrznych adresów IP jako członków puli wewnętrznej bazy danych, musisz użyć [komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) lub [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Komunikacja równorzędna sieci wirtualnej jest obsługiwana i korzystna dla ruchu równoważenia obciążenia w innych sieciach wirtualnych.

Brama aplikacji może również komunikować się z serwerami lokalnymi, gdy są połączone przez tunele usługi Azure ExpressRoute lub SIECI VPN, jeśli ruch jest dozwolony.

Można utworzyć różne pule wewnętrznej bazy danych dla różnych typów żądań. Na przykład utwórz jedną pulę wewnętrznej bazy danych dla żądań ogólnych, a następnie inną pulę wewnętrznej bazy danych dla żądań do mikrousług dla aplikacji.

## <a name="health-probes"></a>Sondy kondycji

Domyślnie brama aplikacji monitoruje kondycję wszystkich zasobów w puli wewnętrznej bazy danych i automatycznie usuwa w złej kondycji. Następnie monitoruje niezdrowe wystąpienia i dodaje je z powrotem do puli wewnętrznej bazy danych w dobrej kondycji, gdy staną się dostępne i reagują na sondy kondycji.

Oprócz używania domyślnego monitorowania sondy kondycji, można również dostosować sondę kondycji do wymagań aplikacji. Niestandardowe sondy umożliwiają bardziej szczegółową kontrolę nad monitorowaniem kondycji. Korzystając z niestandardowych sond, można skonfigurować interwał sondy, adres URL i ścieżkę do testowania i ile odpowiedzi nie powiodło się do zaakceptowania przed wystąpieniem puli wewnętrznej bazy danych jest oznaczony jako w złej kondycji. Zaleca się skonfigurowanie sond niestandardowych do monitorowania kondycji każdej puli zaplecza.

Aby uzyskać więcej informacji, zobacz [Monitorowanie kondycji bramy aplikacji](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Następne kroki

Tworzenie bramy aplikacji:

* [W witrynie Azure Portal](quick-create-portal.md)
* [Przy użyciu programu Azure PowerShell](quick-create-powershell.md)
* [Przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
