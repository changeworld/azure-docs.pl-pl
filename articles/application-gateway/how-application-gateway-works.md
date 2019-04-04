---
title: Jak działa platformy Azure Application Gateway
description: Ten artykuł zawiera informacje na temat do działania bramy aplikacji
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: bbaf651233d4cebad3f45e5cf3823bcaf6ce38b6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905787"
---
# <a name="how-application-gateway-works"></a>Jak działa usługa Application Gateway

W tym artykule wyjaśniono, jak bramy application gateway akceptuje przychodzącego żądania i kieruje je do wewnętrznej bazy danych.

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>Jak żądanie zostało zaakceptowane

Zanim klient wysyła żądanie do Twojej bramy application gateway, jest rozpoznawany jako nazwy domeny bramy aplikacji przy użyciu serwera systemu nazw domen (DNS, Domain Name System). Wpis DNS jest kontrolowana przez platformę Azure, ponieważ bram aplikacji znajdują się w domenie azure.com. Usługi Azure DNS do klienta, który jest zwracany jest adres IP *adresu IP frontonu* usługi Application Gateway. Application gateway akceptuje ruch przychodzący na jednym lub kilku *odbiorników*. Odbiornik jest logicznej jednostki, która sprawdza, czy żądania połączenia. Jest ona skonfigurowana z fronted adresu IP, protokół i numer portu dla połączenia od klientów do usługi application gateway. Włączenie zapory aplikacji sieci Web (WAF) Application Gateway sprawdza nagłówki żądania i treści (jeśli istnieje) względem *reguł zapory aplikacji sieci Web* do określenia, czy żądanie jest prawidłowym żądaniem — w takim przypadku będą kierowane do backend — lub zagrożenie bezpieczeństwa, w którym zostanie zablokowane w przypadku żądania.  

Usługa Application gateway może służyć jako modułu równoważenia obciążenia wewnętrznego aplikacji lub z Internetu modułu równoważenia obciążenia aplikacji. Bramy aplikacji z Internetu ma publicznych adresów IP. Nazwy DNS bramy aplikacji z Internetu jest publicznie rozpoznawalnej do publicznego adresu IP. W związku z tym bramy aplikacji z Internetu może kierować żądania klientów przez Internet. Aplikacja wewnętrzna bramy mają tylko prywatny adres IP. Nazwy DNS bramy aplikacji wewnętrznej jest rozpoznania publicznie na jego prywatny adres IP. W związku z tym wewnętrzne usługi równoważenia obciążenia, tylko może kierować żądania klientów z dostępem do sieci Wirtualnej dla usługi application gateway. Bramy aplikacji zarówno z Internetu i wewnętrzne kierowania żądań do serwerów wewnętrznej bazy danych za pomocą prywatnych adresów IP. W związku z tym usługi serwerów wewnętrznej bazy danych nie są publiczne adresy IP do odbierania żądań od wewnętrznego lub bramy aplikacji z Internetu.

## <a name="how-a-request-is-routed"></a>Jak żądanie jest kierowane

Jeśli żądanie zostanie znaleziony ważność (lub nie włączono zapory aplikacji sieci Web), *reguła routingu żądania* skojarzone z *odbiornika* jest oceniany w celu określenia *puli zaplecza* do którego żądanie zostanie przesłana. Reguły są przetwarzane w kolejności, w jakiej znajdują się na liście w portalu. Na podstawie *reguła routingu żądania* konfiguracji bramy aplikacji określa, czy przekierowaniu wszystkich żądań odbiornik z pulą zaplecza określonych lub kierowania ich do pul różnych wewnętrznych baz danych, w zależności do ścieżki adresu URL lub do *Przekierowywanie żądań* inny numer portu lub zewnętrznej witryny

Gdy *zaplecza* *puli* została wybrana, usługa application gateway wysyła żądanie do jednego z serwerów wewnętrznej bazy danych skonfigurowanych w puli, która jest w dobrej kondycji (y.y.y.y). Kondycja serwera jest określana przez *sondy kondycji*. Jeśli pula zaplecza zawiera więcej niż jeden serwer, usługa application gateway używa algorytmu działanie okrężne kierować żądania między serwerami w dobrej kondycji, dlatego równoważenie żądań na serwerach obciążenia.

Po określeniu serwera wewnętrznej bazy danych usługa application gateway otwiera nową sesję protokołu TCP z serwera wewnętrznej bazy danych na podstawie konfiguracji w *ustawienia HTTP*. *Ustawienia HTTP* jest składnikiem, który określa protokół, port i innych routingu związane z ustawienia, co jest wymagane do utworzenia nowej sesji z serwera wewnętrznej bazy danych. Port i protokół używany w ustawieniach protokołu HTTP należy określić, czy ruchu między serwerami bramy i wewnętrznej bazy danych aplikacji są szyfrowane, więc wykonywanie kompleksowej usługi SSL lub bez szyfrowania. Podczas wysyłania oryginalne żądanie do serwera wewnętrznej bazy danych, usługa application gateway honoruje żadnej konfiguracji niestandardowej wprowadzone w ustawieniach protokołu HTTP, związane z zastępowanie ścieżki, nazwy hosta, protokołu; Obsługa koligacji sesji na podstawie pliku cookie, opróżniania połączenia pobrania nazwy hosta z wewnętrznej bazy danych itp.

Bramy aplikacji wewnętrznych ma tylko prywatny adres IP. Nazwy DNS bramy aplikacji wewnętrznej jest wewnętrznie możliwej do rozpoznania na jego prywatny adres IP. W związku z tym wewnętrzne usługi równoważenia obciążenia może kierować żądania klientów z dostępem do sieci Wirtualnej dla usługi Application Gateway.

Jeśli pula wewnętrznej bazy danych zawiera wewnętrznie rozpoznania nazwy FQDN lub prywatnego adresu IP, usługa Application Gateway kieruje żądanie do serwera wewnętrznej bazy danych przy użyciu prywatnych adresów IP jego wystąpienia. Jeśli w puli zaplecza zawiera zewnętrzny punkt końcowy lub zewnętrznie rozpoznawalną nazwą FQDN, usługa Application Gateway kieruje żądanie do serwera wewnętrznej bazy danych przy użyciu jego publiczny adres IP frontonu. Rozpoznawanie nazw DNS opiera się na prywatnej strefy DNS lub niestandardowy serwer DNS, jeśli skonfigurowane lub zajmuje domyślne DNS dostarczanego przez platformę Azure. Jeśli jeszcze nie przeprowadzono aprowizacji publicznego adresu IP frontonu, jeden jest przypisany dla ruchu wychodzącego łączność zewnętrzną.

### <a name="modifications-to-the-request"></a>Modyfikacje na żądanie

Usługa Application gateway wstawia 4 dodatkowe nagłówki do wszystkich żądań, zanim przekazuje żądania do zaplecza. Te nagłówki są X-forwarded dla X-forwarded-proto, X-forwarded-port i X-oryginalny host. Format dla nagłówka x-forwarded dla jest IP:port listę rozdzielonych przecinkami. Prawidłowe wartości dla x-forwarded-proto są HTTP lub HTTPS. X-forwarded-port Określa port, w którym żądanie osiągnięty w usłudze application gateway. Nagłówek X-oryginalny host zawiera oryginalnego nagłówka hosta, z którym odebrano żądanie. Tego pliku nagłówkowego jest przydatne w scenariuszach, takich jak integracja z witryny internetowej platformy Azure, gdzie przychodzącego nagłówka hosta jest modyfikowany, zanim ruch jest kierowany do wewnętrznej bazy danych. Opcjonalnie Jeśli włączona jest koligacja sesji, następnie plik cookie koligacji zarządzanych brama zostanie wstawiony. 

Może dodatkowo skonfigurować bramę aplikacji do modyfikowania przy użyciu nagłówków [nagłówków HTTP Nadpisz](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) lub zmodyfikować ścieżka identyfikatora URI za pomocą zastąpienia ścieżki ustawienie. Ale, o ile nie jest skonfigurowany, aby to zrobić, wszystkie żądania przychodzące są przekierowywane, ponieważ jest z zapleczem usługi.


## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się o tym, jak działa usługa application gateway, zobacz [składniki bramy aplikacji](application-gateway-components.md) zrozumienie jego składników, które bardziej szczegółowo.
