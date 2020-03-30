---
title: Jak działa brama aplikacji
description: Ten artykuł zawiera informacje o tym, jak brama aplikacji akceptuje przychodzące żądania i kieruje je do wewnętrznej bazy danych.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132980"
---
# <a name="how-an-application-gateway-works"></a>Jak działa brama aplikacji

W tym artykule wyjaśniono, jak brama aplikacji akceptuje przychodzące żądania i kieruje je do wewnętrznej bazy danych.

![Jak brama aplikacji akceptuje żądanie](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Jak brama aplikacji akceptuje żądanie

1. Zanim klient wyśle żądanie do bramy aplikacji, rozpozna nazwę domeny bramy aplikacji przy użyciu serwera DNS (Domain Name System). Platforma Azure kontroluje wpis DNS, ponieważ wszystkie bramy aplikacji znajdują się w domenie azure.com.

2. Usługa Azure DNS zwraca adres IP do klienta, który jest adresem IP frontendu bramy aplikacji.

3. Brama aplikacji akceptuje ruch przychodzący na co najmniej jednym odbiorniku. Odbiornik jest jednostką logiczną, która sprawdza żądania połączenia. Jest skonfigurowany z frontendowym adresem IP, protokołem i numerem portu dla połączeń z klientami z bramą aplikacji.

4. Jeśli zapora aplikacji sieci web (WAF) jest w użyciu, brama aplikacji sprawdza nagłówki żądań i treści, jeśli są obecne, względem reguł WAF. Ta akcja określa, czy żądanie jest prawidłowe żądanie lub zagrożenie bezpieczeństwa. Jeśli żądanie jest prawidłowe, jest kierowane do wewnętrznej bazy danych. Jeśli żądanie nie jest prawidłowe, a usługa WAF jest w trybie zapobiegania, jest zablokowana jako zagrożenie bezpieczeństwa. Jeśli jest w trybie wykrywania, żądanie jest oceniane i rejestrowane, ale nadal przekazywane do serwera wewnętrznej bazy danych.

Usługa Azure Application Gateway może służyć jako wewnętrzny moduł równoważenia obciążenia aplikacji lub jako moduł równoważenia obciążenia aplikacji z dostępem do Internetu. Brama aplikacji z dostępem do Internetu używa publicznych adresów IP. Nazwa DNS bramy aplikacji skierowanej do Internetu jest publicznie rozpoznawana na jej publiczny adres IP. W rezultacie bramy aplikacji internetowych mogą kierować żądania klientów do Internetu.

Bramy aplikacji wewnętrznych używają tylko prywatnych adresów IP. Jeśli używasz niestandardowej lub [prywatnej strefy DNS,](https://docs.microsoft.com/azure/dns/private-dns-overview)nazwa domeny powinna być wewnętrznie rozpoznawana na prywatny adres IP bramy aplikacji. W związku z tym wewnętrzne moduły równoważenia obciążenia mogą kierować tylko żądania od klientów z dostępem do sieci wirtualnej dla bramy aplikacji.

## <a name="how-an-application-gateway-routes-a-request"></a>Jak brama aplikacji kieruje żądanie

Jeśli żądanie jest prawidłowe i nie jest blokowane przez WAF, brama aplikacji ocenia regułę routingu żądań skojarzoną z odbiornikiem. Ta akcja określa, która pula wewnętrznej bazy danych do kierowania żądania do.

Na podstawie reguły routingu żądań brama aplikacji określa, czy wszystkie żądania w odbiorniku mają być kierowane do określonej puli wewnętrznej bazy danych, rozsyłać żądania do różnych pul wewnętrznej bazy danych na podstawie ścieżki adresu URL, czy przekierowywać żądania do innego portu lub lokacji zewnętrznej.
>[!NOTE]
>Reguły są przetwarzane w kolejności, w jakiej są wymienione w portalu dla jednostki SKU w wersji 1. 

Gdy brama aplikacji wybierze pulę wewnętrznej bazy danych, wysyła żądanie do jednego z serwerów zaplecza w dobrej kondycji w puli (y.y.y.y.y). Kondycja serwera jest określana przez sondę kondycji. Jeśli pula wewnętrznej bazy danych zawiera wiele serwerów, brama aplikacji używa algorytmu okrężnego do kierowania żądań między serwerami w dobrej kondycji. To obciążenie równoważy żądania na serwerach.

Po tym, jak brama aplikacji określi serwer wewnętrznej bazy danych, otwiera nową sesję TCP z serwerem wewnętrznej bazy danych na podstawie ustawień HTTP. Ustawienia HTTP określają protokół, port i inne ustawienia związane z routingiem, które są wymagane do ustanowienia nowej sesji z serwerem wewnętrznej bazy danych.

Port i protokół używany w ustawieniach HTTP określają, czy ruch między bramą aplikacji a serwerami wewnętrznej bazy danych jest szyfrowany (w ten sposób wykonuje end-to-end TLS) lub jest niezaszyfrowany.

Gdy brama aplikacji wysyła oryginalne żądanie do serwera wewnętrznej bazy danych, honoruje dowolną konfigurację niestandardową wykonaną w ustawieniach HTTP związaną z zastępowanie nazwy hosta, ścieżki i protokołu. Ta akcja zachowuje koligacji sesji opartej na plikach cookie, opróżnianie połączenia, wybór nazwy hosta z wewnętrznej bazy danych i tak dalej.

 >[!NOTE]
>Jeśli pula wewnętrznej bazy danych:
> - **Jest publicznym punktem końcowym,** brama aplikacji używa swojego publicznego adresu IP frontonijnego, aby dotrzeć do serwera. Jeśli nie ma publicznego adresu IP frontendu, jeden jest przypisany do wychodzącej łączności zewnętrznej.
> - **Zawiera wewnętrznie rozpoznawaną sieć FQDN lub prywatny adres IP**, brama aplikacji kieruje żądanie do serwera wewnętrznej bazy danych przy użyciu prywatnych adresów IP jego wystąpienia.
> - **Zawiera zewnętrzny punkt końcowy lub zewnętrznie rozpoznawaną sieć FQDN**, brama aplikacji kieruje żądanie do serwera wewnętrznej bazy danych przy użyciu jego publicznego adresu IP frontonu. Rozpoznawanie DNS jest oparte na prywatnej strefie DNS lub niestandardowym serwerze DNS, jeśli jest skonfigurowane lub używa domyślnego systemu DNS dostarczonego przez platformę Azure. Jeśli nie ma publicznego adresu IP frontendu, jeden jest przypisany do wychodzącej łączności zewnętrznej.

### <a name="modifications-to-the-request"></a>Zmiany we wniosku

Brama aplikacji wstawia cztery dodatkowe nagłówki do wszystkich żądań, zanim przekaże żądania do wewnętrznej bazy danych. Te nagłówki są x-forwarded-for, x-forwarded-proto, x-forwarded-port i x-original-host. Format nagłówka x-forwarded-for jest oddzieloną przecinkami listą IP:port.

Prawidłowe wartości dla x-forwarded-proto to HTTP lub HTTPS. X-forwarded-port określa port, w którym żądanie dotarło do bramy aplikacji. Nagłówek X-original-host zawiera oryginalny nagłówek hosta, z którym dotarło żądanie. Ten nagłówek jest przydatny w integracji witryny sieci Web platformy Azure, gdzie przychodzące hosta nagłówka jest modyfikowany przed ruch jest kierowany do wewnętrznej bazy danych. Jeśli koligacja sesji jest włączona jako opcja, następnie dodaje plik cookie koligacji zarządzanych przez bramę.

Bramę aplikacji można skonfigurować do modyfikowania nagłówków przy użyciu [funkcji Przepisanie nagłówków HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) lub modyfikowania ścieżki URI przy użyciu ustawienia zastępowania ścieżki. Jednak jeśli nie jest skonfigurowany do tego, wszystkie przychodzące żądania są proporcjonalne do wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o składnikach bramy aplikacji](application-gateway-components.md)
