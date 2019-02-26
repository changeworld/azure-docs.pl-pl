---
title: Jak działa platformy Azure Application Gateway
description: Ten artykuł zawiera informacje na temat do działania bramy aplikacji
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 06206ececcb1a51da402c4232f19801793c1cd4a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807341"
---
# <a name="how-application-gateway-works"></a>Jak działa usługa Application Gateway

Usługa Application Gateway to kontroler dostarczania aplikacji w chmurze warstwy 7 (HTTP) równoważenia obciążenia, które umożliwiają zarządzanie ruchem w sieci web na serwerach. Ponadto zapewnia również możliwości zapory aplikacji sieci Web (WAF), które zapewnia scentralizowaną ochronę usługi sieci web z typowych internetowymi programami wykorzystującymi luki i lukami w zabezpieczeniach.

Gdy klient wysyła żądanie HTTP do usługi Application Gateway, działa jako zwrotny serwer proxy i przekazuje ruch do serwerów zaplecza na podstawie konfiguracji. Ponadto bramy aplikacji również monitoruje kondycję swoich serwerów wewnętrznej bazy danych i zapewnia kieruje ruch tylko do zaplecza w dobrej kondycji.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Jak żądanie zostało zaakceptowane

Zanim klient wysyła żądanie do Twojej bramy Application Gateway, jest rozpoznawany jako nazwy domeny bramy aplikacji przy użyciu serwera systemu nazw domen (DNS, Domain Name System). Wpis DNS jest kontrolowana przez platformę Azure, ponieważ bram aplikacji znajdują się w domenie azure.com. Usługi Azure DNS do klienta, który jest zwracany jest adres IP *adresu IP frontonu* usługi Application Gateway. Application Gateway akceptuje ruch przychodzący na jednym lub kilku *odbiorników*. Odbiornik to proces, który sprawdza, czy żądania połączenia. Jest ona skonfigurowana z fronted adresu IP, protokół i numer portu dla połączenia od klientów do usługi Application Gateway. Włączenie zapory aplikacji sieci Web bramy aplikacji sprawdza nagłówki żądania i treści (jeśli istnieje) względem *reguł zapory aplikacji sieci Web* do określenia, czy żądanie jest prawidłowym żądaniem — w takim przypadku zostanie przesłany do wewnętrznej bazy danych — lub na zagrożenia bezpieczeństwa w które zamierzone, Zapisz żądanie zostanie zablokowane.  

## <a name="how-request-is-routed"></a>Jak żądanie jest kierowane

Jeśli żądanie zostanie znaleziony ważność (lub nie włączono zapory aplikacji sieci Web), *reguła routingu żądania* skojarzone z *odbiornika* jest oceniany w celu określenia *puli zaplecza* do którego żądanie zostanie przesłana. Reguły są przetwarzane w kolejności, w jakiej znajdują się na liście w portalu. Na podstawie *reguła routingu żądania* konfiguracji bramy aplikacji określa, czy przekierowaniu wszystkich żądań odbiornik z pulą zaplecza określonych lub kierowania ich do pul różnych wewnętrznych baz danych, w zależności do ścieżki adresu URL lub do *Przekierowywanie żądań* inny numer portu lub zewnętrznej witryny

Gdy *zaplecza* *puli* została wybrana, usługa Application Gateway wysyła żądanie do jednego z serwerów wewnętrznej bazy danych skonfigurowanych w puli, która jest w dobrej kondycji (y.y.y.y). Kondycja serwera jest określana przez *sondy kondycji*. Jeśli pula zaplecza zawiera więcej niż jeden serwer, usługa Application Gateway używa algorytmu działanie okrężne kierować żądania między serwerami w dobrej kondycji, dlatego równoważenie żądań na serwerach obciążenia.

Po określeniu serwera wewnętrznej bazy danych Application Gateway otwiera nową sesję protokołu TCP z serwera wewnętrznej bazy danych na podstawie konfiguracji w *ustawienia HTTP*. *Ustawienia HTTP* jest składnikiem, który określa protokół, port i innych routingu związane z ustawienia, co jest wymagane do utworzenia nowej sesji z serwera wewnętrznej bazy danych. Port i protokół używany w ustawieniach protokołu HTTP należy określić, czy ruch między bramy aplikacji i serwerów wewnętrznej bazy danych, są szyfrowane, więc wykonywanie kompleksowej usługi SSL lub bez szyfrowania. Podczas wysyłania oryginalne żądanie do serwera wewnętrznej bazy danych, usługa Application Gateway honoruje żadnej konfiguracji niestandardowej wprowadzone w ustawieniach protokołu HTTP, związane z zastępowanie ścieżki, nazwy hosta, protokołu; Obsługa koligacji sesji na podstawie pliku cookie, opróżniania połączenia pobrania nazwy hosta z wewnętrznej bazy danych itp.

Application gateway są wstawiane tych nagłówków HTTP * trzy domyślne: x-forwarded dla x-forwarded-proto i x przekazywane — port na żądanie przekazywane do zaplecza.

Gdy serwera wewnętrznej bazy danych przetwarza żądanie i wysyła odpowiedź HTTP z zawartość strony do usługi Application Gateway, brama aplikacji przekazuje odpowiedzi do klienta, w którym ta strona jest wyświetlana w przeglądarce.

## <a name="application-load-balancing-type"></a>Typ równoważenia obciążenia aplikacji

Application Gateway można użyć jako modułu równoważenia obciążenia wewnętrznego aplikacji lub z Internetu modułu równoważenia obciążenia aplikacji. Bramy aplikacji z Internetu ma publicznych adresów IP. Nazwy DNS bramy aplikacji z Internetu jest publicznie rozpoznawalnej do publicznego adresu IP. W związku z tym bramy aplikacji z Internetu może kierować żądania klientów przez Internet.

Bramy aplikacji wewnętrznych ma tylko prywatny adres IP. Nazwy DNS bramy aplikacji wewnętrznej jest wewnętrznie możliwej do rozpoznania na jego prywatny adres IP. W związku z tym wewnętrzne usługi równoważenia obciążenia może kierować żądania klientów z dostępem do sieci Wirtualnej dla usługi Application Gateway.

Należy pamiętać, że zarówno z Internetu i wewnętrzne bramy Application Gateway kierowania żądań do serwerów wewnętrznej bazy danych za pomocą prywatnych adresów IP. Jeśli zasób puli wewnętrznej bazy danych zawiera prywatny adres IP, konfiguracja karty Sieciowej maszyny Wirtualnej lub wewnętrznie możliwej do rozpoznania adresu i puli zaplecza jest publiczny punkt końcowy, usługa Application Gateway używa publiczny adres IP jego frontonu do serwera. Jeśli jeszcze nie przeprowadzono aprowizacji publicznego adresu IP frontonu, jeden jest przypisany dla ruchu wychodzącego łączność zewnętrzną.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się o tym, jak działa usługa Application Gateway, przejdź do [składniki bramy aplikacji](application-gateway-components.md) zrozumienie jego składników, które bardziej szczegółowo.
