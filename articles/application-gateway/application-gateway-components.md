---
title: Składniki bramy aplikacji
description: Ten artykuł zawiera informacje na temat różnych składników w usłudze application gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 49b6b49d908a7426e7cfd1bae5260ff399d9953b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273219"
---
# <a name="application-gateway-components"></a>Składniki bramy aplikacji

 Bramy aplikacji służy jako pojedynczy punkt kontaktu dla klientów. Je dystrybuuje ruch przychodzący aplikacji dla wielu pul zaplecza, które obejmują maszyny wirtualne platformy Azure, zestawy skalowania maszyn wirtualnych, usługi Azure App Service i w lokalnych/zewnętrznych serwerów. Aby dystrybuować ruch, bramy aplikacji używa kilka składników, które opisano w tym artykule.

![Składniki używane w usłudze application gateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Adresy IP frontonu

Adres IP frontonu jest adres IP skojarzony z bramą aplikacji. Można skonfigurować bramę aplikacji do publicznego adresu IP i/lub prywatnego adresu IP. Usługa application gateway obsługuje jedną publiczną lub jeden prywatny adres IP. Sieć wirtualna i publiczny adres IP musi być w tej samej lokalizacji co Twoja brama application gateway. Po jego utworzeniu adresu IP frontonu jest skojarzony z odbiornika.

### <a name="static-versus-dynamic-public-ip-address"></a>Statycznej i dynamicznej publiczny adres IP

Jednostki SKU usługi Azure Application Gateway w wersji 2 można skonfigurować do obsługi zarówno statycznego adresu IP wewnętrznej i statyczny publiczny adres IP albo tylko statyczny publiczny adres IP. Nie można skonfigurować do obsługi tylko statyczne wewnętrzny adres IP.

Jednostki SKU V1 można skonfigurować w celu obsługi statycznych wewnętrzny adres IP i dynamicznego publicznego adresu IP tylko statyczne wewnętrzny adres IP lub tylko dynamicznego publicznego adresu IP. Dynamiczny adres IP bramy Application Gateway nie zmienia się na uruchamianie bramy. Można go zmienić tylko wtedy, gdy zatrzymasz lub uruchomisz bramy. Nie powoduje zmiany na awarie systemu, aktualizacji i hosta platformy Azure aktualizacje itp. 

Nazwa DNS skojarzone z usługą application gateway nie zmienia się z cyklem bramy. W rezultacie należy Użycie aliasów CNAME i wskaż adresu DNS bramy aplikacji.

## <a name="listeners"></a>Odbiorniki

Odbiornik jest logicznej jednostki, która sprawdza, czy przychodzące żądania połączenia. Odbiornik akceptuje żądania, jeśli protokół, port, hosta i adres IP skojarzony z żądaniem odpowiadają te same elementy, które są skojarzone z konfiguracji odbiornika.

Przed skorzystaniem z bramy aplikacji, należy dodać co najmniej jednego odbiornika. Może istnieć wiele odbiorników dołączonego do bramy aplikacji i mogą być używane dla tego samego protokołu.

Po odbiornik wykryje żądań przychodzących od klientów, usługa application gateway kieruje żądania do elementów członkowskich w puli zaplecza. Bramy aplikacji przy użyciu reguł routingu żądania zdefiniowane dla odbiornika, który otrzymał żądanie przychodzące.

Odbiorniki obsługuje następujących portów i protokołów.

### <a name="ports"></a>Porty

Port jest, gdzie odbiornik nasłuchuje żądań klienta. Można skonfigurować porty z zakresu od 1 do 65502 dla jednostek SKU v1 i 1 do 65199, w ramach jednostki SKU w wersji 2.

### <a name="protocols"></a>Protokoły

Usługa Application Gateway obsługuje cztery Protokoły: HTTP, HTTPS i HTTP/2 oraz WebSocket:

- Określ wartość HTTP lub HTTPS protokołów w konfiguracji odbiornika.
- Obsługa [WebSockets i protokołu HTTP/2 protokołów](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) jest udostępnianych w sposób natywny, i [Obsługa protokołu WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) jest domyślnie włączona. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. Za pomocą funkcji WebSockets odbiorników HTTP i HTTPS.
- Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się tylko odbiorników bramy aplikacji. Komunikacja do pul serwerów zaplecza jest za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Można ją włączyć.

Odbiornika protokołu HTTPS na użytek kończenia żądań SSL. Odbiornik HTTPS mniejsze szyfrowania i odszyfrowywania pracy Twojej bramy application gateway, więc serwerów sieci web nie są obciążać przez obciążenie. Twoje aplikacje mogą następnie skoncentrować się na logice biznesowej.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Usługa Application Gateway umożliwia tworzenie strony błędów niestandardowych zamiast domyślnej strony błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu. Usługa Application Gateway Wyświetla niestandardowej strony błędu, jeśli żądanie nie może nawiązać połączenia wewnętrznej bazy danych.

Aby uzyskać więcej informacji, zobacz [strony błędów niestandardowych dla bramy application gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typy obiektów nasłuchujących

Istnieją dwa rodzaje odbiorniki:

- **Podstawowe**. Ten typ odbiornik nasłuchuje do lokacji jednej domeny, który ma jedno mapowanie DNS na adres IP bramy aplikacji. Ta konfiguracja odbiornika jest wymagana, gdy hosta jednej witryny za bramą aplikacji.

- **Połączenia obejmujące wiele lokacji**. Ta konfiguracja odbiornika jest wymagana, gdy konfigurujesz więcej niż jednej aplikacji sieci web na tym samym wystąpieniu bramy aplikacji. Umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn sieci Web do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład trzech poddomeny, abc.contoso.com, xyz.contoso.com i pqr.contoso.com, wskazywać adres IP bramy aplikacji. Czy utworzyć trzy odbiorników obejmujących wiele lokacji i skonfigurować każdego odbiornika dla odpowiednich portów i ustawienia protokołu.

    Aby uzyskać więcej informacji, zobacz [hostingu wielu witryn](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Po utworzeniu odbiornika, należy ją skojarzyć z reguły routingu żądania. Ta zasada ustala, jak żądanie odebrane na odbiorniku powinien kierowane do zaplecza.

Usługa Application Gateway przetwarza słuchaczy w podanej kolejności. Jeśli podstawowy odbiornik pasuje do przychodzącego żądania, jest przetwarzana najpierw. Aby przekierować ruch na poprawne wewnętrznej bazy danych, należy skonfigurować odbiornika wielu lokacji, przed podstawowego odbiornika.

## <a name="request-routing-rules"></a>Żądanie reguły rozsyłania

Reguła routingu żądania jest kluczowym składnikiem bramy aplikacji, ponieważ określa sposób kierowania ruchu na odbiorniku. Reguła wiąże odbiornik, puli serwerów zaplecza i ustawienia HTTP zaplecza.

W przypadku odbiornika akceptuje żądania, reguły routingu żądań przekazuje żądania do zaplecza lub przekierowuje go w innym miejscu. Jeśli żądanie jest przekazywane do zaplecza, reguły routingu żądań definiuje której puli serwerów zaplecza na udostępnianie dalej, aby. Również reguły routingu żądań określa również, czy mają zostać przepisany z nagłówków w żądaniu. Jednego odbiornika można dołączyć do jednej reguły.

Istnieją dwa typy reguł rozsyłania żądań:

- **Podstawowe**. Wszystkie żądania na skojarzony odbiornik (na przykład blog.contoso.com/*) są przekazywane do puli zaplecza skojarzone przy użyciu skojarzone ustawienia protokołu HTTP.

- **Oparty na ścieżkach**. Ta reguła routingu umożliwia kierowanie żądań na skojarzony odbiornik z pulą zaplecza określone na podstawie adresu URL w żądaniu. Jeśli ścieżka adresu URL w żądaniu pasuje wzorzec ścieżki w regule opartego na ścieżkach, reguła kieruje tego żądania. Wzorzec ścieżki dotyczy tylko do ścieżki adresu URL, a nie do jego parametry zapytania. Jeśli ścieżka adresu URL żądania odbiornik nie pasuje do żadnego z reguł opartych na ścieżkach, kieruje żądanie do domyślna pula zaplecza i ustawienia protokołu HTTP.

Aby uzyskać więcej informacji, zobacz [routingu opartego na adresach URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Obsługa przekierowania

Reguła routingu żądania umożliwia przekierowywanie ruchu w usłudze application gateway. To mechanizm ogólnego przekierowania, dzięki czemu można przekierować do i z dowolnego portu definiowane za pomocą reguł.

Możesz wybrać miejsce docelowe przekierowania inny odbiornik, (które mogą pomóc włączyć automatyczne HTTP do przekierowania protokołu HTTPS) lub zewnętrznej witryny. Możesz również wybrać do przekierowania jest tymczasowy lub stały lub dołączyć ciąg identyfikatora URI ścieżkę i zapytanie do adresu URL.

Aby uzyskać więcej informacji, zobacz [przekierowywanie ruchu na Twojej bramy application gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Ponowne zapisywanie nagłówków HTTP

Za pomocą reguł routingu żądania, można dodać, usunąć lub zaktualizować żądania HTTP (S) i nagłówki odpowiedzi jako pakiety żądań i odpowiedzi Przenieś między klientem a zapleczem pule za pośrednictwem bramy aplikacji.

Nagłówki można ustawić wartości statyczne lub innych nagłówków i zmiennych serwera. Dzięki temu przy użyciu ważnych zastosowań, takich jak wyodrębnianie adresów IP klientów, usunięcie poufnych informacji o wewnętrznej bazy danych, dodając lepsze zabezpieczenia i tak dalej.

Aby uzyskać więcej informacji, zobacz [nagłówków HTTP ponownego zapisywania w Twojej bramy application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Ustawienia HTTP

Usługa application gateway kieruje ruch do serwerów wewnętrznej bazy danych (określone w regule routingu żądania, który zawiera ustawienia protokołu HTTP) przy użyciu numeru portu, protokołu i inne ustawienia, które szczegółowo opisane w tej części.

Port i protokół używany w ustawieniach protokołu HTTP określają, czy ruch między serwerami bramy i wewnętrznej bazy danych aplikacji są szyfrowane (udostępniane end-to-end SSL) lub bez szyfrowania.

Ten składnik jest również używana do:

- Określić, czy sesja użytkownika jest mają być przechowywane na tym samym serwerze przy użyciu [koligacji sesji na podstawie pliku cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Bezpiecznie usunąć składowych puli zaplecza przy użyciu [opróżniania połączenia](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Kojarzenie niestandardowej sondy monitorować kondycję wewnętrznej bazy danych, ustaw interwał limitu czasu żądania, zastąp nazwę hosta i ścieżkę w żądaniu i zapewnia łatwe jednym kliknięciem do określania ustawień dla wewnętrznej bazy danych usługi App Service.

## <a name="backend-pools"></a>Pule zaplecza

Pula zaplecza kieruje żądanie do serwerów wewnętrznej bazy danych, które obsłużyć żądania. Pule zaplecza mogą zawierać:

- Karty interfejsów sieciowych
- Zestawy skalowania maszyn wirtualnych
- Publiczne adresy IP
- Wewnętrzne adresy IP
- NAZWA FQDN
- Wielodostępne zaplecza (takich jak usługa App Service)

Zestaw dostępności nie są powiązane składowych puli zaplecza bramy aplikacji. Bramy aplikacji może komunikować się z wystąpieniami poza siecią wirtualną, która znajduje się w. W rezultacie członkowie pule zaplecza może być w klastrach, między centrami danych lub spoza platformy Azure, tak długo, jak istnieje łączność IP.

Jeśli używasz wewnętrznych adresów IP jako składowych puli zaplecza, należy użyć [wirtualne sieci równorzędne](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) lub [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikacja równorzędna sieci wirtualnych jest obsługiwane i korzystne dla ruchu równoważenia obciążenia w innych sieciach wirtualnych.

Bramy aplikacji można również przekazać za pomocą lokalnych serwerów gdy są one połączone przez tunele do usługi Azure ExpressRoute lub sieci VPN, jeśli ruch jest dozwolony.

Można tworzyć pule zaplecza różne dla różnych typów żądań. Na przykład utworzyć jedną pulę zaplecza dla ogólnych żądań, a następnie innej puli zaplecza dla żądań kierowanych do mikrousług dla aplikacji.

## <a name="health-probes"></a>Sondy kondycji

Domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza bramy aplikacji i automatycznie usuwa te złej kondycji. Następnie monitoruje wystąpienia i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i odpowiadać na sondy kondycji.

Oprócz używania, sondowanie kondycji domyślnej funkcji monitorowania, można również dostosować sondę kondycji, aby odpowiadał wymaganiom Twojej aplikacji. Niestandardowe sondy umożliwiają bardziej szczegółową kontrolę nad monitorowanie kondycji. Użycie sond niestandardowych, można skonfigurować interwał sondowania, adres URL i ścieżki do testowania i jak wiele odpowiedzi nie powiodło się, aby zaakceptować wystąpienie puli serwerów zaplecza jest oznaczony jako w złej kondycji. Firma Microsoft zaleca, aby skonfigurować niestandardowe sondy do monitorowania kondycji każdej puli wewnętrznej bazy danych.

Aby uzyskać więcej informacji, zobacz [monitorowania kondycji bramy application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Kolejne kroki

Tworzenie bramy aplikacji:

* [W witrynie Azure portal](quick-create-portal.md)
* [Za pomocą programu Azure PowerShell](quick-create-powershell.md)
* [Przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md)
