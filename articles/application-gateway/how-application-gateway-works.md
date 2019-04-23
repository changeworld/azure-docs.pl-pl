---
title: Jak działa bramy aplikacji
description: Ten artykuł zawiera informacje dotyczące sposobu działania bramy aplikacji
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008317"
---
# <a name="how-an-application-gateway-works"></a>Jak działa bramy aplikacji

W tym artykule wyjaśniono, jak usługa application gateway akceptuje żądania przychodzące i kieruje je do wewnętrznej bazy danych.

![Jak akceptuje żądania przez bramę aplikacji](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Jak akceptuje żądania przez bramę aplikacji

1. Zanim klient wysyła żądanie do usługi application gateway, rozpoznaje nazwę domeny usługi application gateway przy użyciu serwera systemu nazw domen (DNS, Domain Name System). Azure Określa wpis DNS, ponieważ wszystkich bram aplikacji znajdują się w domenie azure.com.

2. System DNS Azure zwracany jest adres IP klienta, czyli adresu IP frontonu bramy aplikacji.

3. Application gateway akceptuje ruch przychodzący na co najmniej jednego odbiornika. Odbiornik jest logicznej jednostki, która sprawdza, czy żądania połączenia. Jest ona skonfigurowana z fronted adresu IP, protokół i numer portu dla połączenia od klientów do usługi application gateway.

4. Jeśli zapory aplikacji sieci web (WAF) jest używany, bramy application gateway sprawdza nagłówki żądania i treści, jeśli jest obecny, względem reguły zapory aplikacji sieci Web. Ta akcja określa, czy żądanie jest prawidłowym żądaniem lub zagrożenie bezpieczeństwa. Jeśli żądanie jest prawidłowe, odbywa się do wewnętrznej bazy danych. Jeśli żądanie nie jest prawidłowy, jest zablokowany jako zagrożenie bezpieczeństwa.

Usługa Azure Application Gateway może służyć jako aplikacja wewnętrzna usługa równoważenia obciążenia lub z Internetu modułu równoważenia obciążenia aplikacji. Bramy aplikacji z Internetu używa publicznych adresów IP. Nazwy DNS bramy aplikacji z Internetu jest publicznie rozpoznawalnej do publicznego adresu IP. W wyniku bram aplikacji z Internetu może kierować żądania klientów w Internecie.

Aplikacja wewnętrzna bramy używają tylko prywatnych adresów IP. Nazwy DNS bramy aplikacji wewnętrznej jest rozpoznania publicznie na jego prywatny adres IP. W związku z tym wewnętrznego równoważenia obciążenia, tylko może kierować żądania klientów z dostępem do sieci wirtualnej dla usługi application gateway.

Zarówno z Internetu, jak i wewnętrznych aplikacji bramy kierowania żądań do serwerów wewnętrznej bazy danych przy użyciu prywatnych adresów IP. Serwerów wewnętrznej bazy danych nie ma potrzeby publicznych adresów IP do odbierania żądań od wewnętrznego lub bramy aplikacji z Internetu.

## <a name="how-an-application-gateway-routes-a-request"></a>Jak usługa application gateway kieruje żądania

Jeśli żądanie jest nieprawidłowy lub nie jest używana Zapora aplikacji sieci Web, bramy application gateway ocenia reguły routingu żądania skojarzonego z odbiornika. Ta akcja określa, które puli zaplecza, aby skierować żądanie.

Reguły są przetwarzane w kolejności, w której są wyświetlane w portalu. Oparte na regułę routingu żądań, bramy application gateway Określa, czy przekierowaniu wszystkich żądań odbiornik z pulą zaplecza określonej trasy żądania do pul różnych wewnętrznych baz danych na podstawie ścieżki adresu URL, lub Przekieruj żądania do innego portu lub zewnętrznej witryny.

Gdy bramy application gateway wybierze puli zaplecza, wysyła żądanie do jednego z serwerów zaplecza w dobrej kondycji w puli (y.y.y.y). Kondycja serwera jest określany przez sondy kondycji. Jeśli pula wewnętrznej bazy danych zawiera wiele serwerów, bramy application gateway używa algorytmu okrężnym do kierowania żądań między serwerami w dobrej kondycji. Równoważy żądań na serwerach.

Po bramy application gateway określa serwera wewnętrznej bazy danych, otwiera nową sesję protokołu TCP z serwerem wewnętrznej bazy danych na podstawie ustawień HTTP. Ustawienia HTTP Określ protokół, port i innych ustawień związanych z routingiem, które są wymagane do ustanowienia nowej sesji z serwera wewnętrznej bazy danych.

Port i protokół używany w ustawieniach protokołu HTTP należy określić, czy ruchu między serwerami bramy i wewnętrznej bazy danych aplikacji są szyfrowane (ten sposób wykonywania end-to-end SSL), czy jest niezaszyfrowany.

Bramy aplikacji wysyła oryginalne żądanie do serwera wewnętrznej bazy danych, będzie używana żadnej konfiguracji niestandardowej wprowadzone w ustawieniach protokołu HTTP, związane z zastępowanie nazwy hosta, ścieżki i protokołu. Ta akcja zachowuje koligacji sesji na podstawie pliku cookie, wybór opróżniania, nazwy hosta połączenia z wewnętrznej bazy danych i tak dalej.

Bramy aplikacji wewnętrznych używa tylko prywatnych adresów IP. Nazwy DNS bramy aplikacji wewnętrznej jest rozpoznawany na jego prywatny adres IP. W rezultacie wewnętrznego równoważenia obciążenia tylko może kierować żądania klientów z dostępem do sieci wirtualnej dla usługi application gateway.

 >[!NOTE]
 >Obie bramy aplikacji internetowych i wewnętrznych kierowania żądań do serwerów wewnętrznej bazy danych przy użyciu prywatnych adresów IP. Ta akcja występuje, gdy zasób puli wewnętrznej bazy danych zawiera prywatny adres IP, konfiguracja karty Sieciowej maszyny Wirtualnej lub wewnętrznie możliwej do rozpoznania adresu. Jeśli pula zaplecza:
> - **Jest to publiczny punkt końcowy**, brama aplikacji używa publiczny adres IP jego frontonu do serwera. Jeśli nie ma publicznego adresu IP frontonu, jeden jest przypisany dla ruchu wychodzącego łączność zewnętrzną.
> - **Zawiera wewnętrznie rozpoznania nazwy FQDN lub prywatnego adresu IP**, usługa application gateway kieruje żądanie do serwera wewnętrznej bazy danych przy użyciu prywatnych adresów IP jego wystąpienia.
> - **Zawiera zewnętrzny punkt końcowy lub zewnętrznie rozpoznawalną nazwą FQDN**, usługa application gateway kieruje żądanie do serwera wewnętrznej bazy danych za pomocą frontonu publicznego adresu IP. Rozpoznawanie nazw DNS opiera się na prywatnej strefy DNS lub niestandardowego serwera DNS, jeśli skonfigurowane lub używa domyślnej DNS platformy Azure. Jeśli nie ma publicznego adresu IP frontonu, jeden jest przypisany dla ruchu wychodzącego łączność zewnętrzną.

### <a name="modifications-to-the-request"></a>Modyfikacje na żądanie

Bramy aplikacji wstawia cztery dodatkowe nagłówki do wszystkich żądań, zanim przekazuje żądania do zaplecza. Tych nagłówków są x-forwarded dla, x-forwarded-proto, x-forwarded-port i x oryginalny host. Format dla nagłówka x-forwarded dla jest IP:port listę rozdzielonych przecinkami.

Prawidłowe wartości dla x-forwarded-proto są HTTP lub HTTPS. X-forwarded-port Określa port, w którym żądanie osiągnięto bramy application gateway. Nagłówek X-oryginalny host zawiera oryginalnego nagłówka hosta, z którym odebrano żądanie. Tego pliku nagłówkowego przydaje się w witrynie internetowej platformy Azure, integracja, gdzie przychodzącego nagłówka hosta jest modyfikowany, zanim ruch jest kierowany do wewnętrznej bazy danych. Jeśli koligacja sesji jest włączony jako opcja, następnie dodaje plik cookie koligacji zarządzanych przez bramę.

Można skonfigurować bramy aplikacji można modyfikować nagłówki za pomocą [nagłówków HTTP Nadpisz](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) lub zmodyfikować składnik path identyfikatora URI za pomocą ustawienia zastąpienie ścieżki. Jeśli jednak skonfigurowany, aby to zrobić, wszystkie żądania przychodzące są przekazywane do zaplecza.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o składniki bramy aplikacji](application-gateway-components.md)
