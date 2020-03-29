---
title: Specyficzne dla Verizon nagłówki HTTP dla aparatu reguł usługi Azure CDN | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania nagłówków HTTP specyficznych dla verizon z aparatem reguł usługi Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593261"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Specyficzne dla verizon nagłówki HTTP dla aparatu reguł usługi Azure CDN

W przypadku **produktów usługi Azure CDN Premium z** produktów Verizon, gdy żądanie HTTP jest wysyłane do serwera pochodzenia, serwer punktu obecności (POP) może dodać jeden lub więcej zastrzeżonych nagłówków (lub specjalnych nagłówków serwera proxy) w żądaniu klienta do punktu obecności. Te nagłówki są dodatkiem do standardowych nagłówków przekazywania otrzymanych. Aby uzyskać informacje o standardowych nagłówkach żądań, zobacz [Żądania pól](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Jeśli chcesz zapobiec dodawaniu jednego z tych zastrzeżonych nagłówków w żądaniu POP usługi Azure CDN (Content Delivery Network) do serwera pochodzenia, należy utworzyć regułę z [funkcją nagłówki specjalne proxy](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) w silniku reguł. W tej regule wyklucz nagłówek, który chcesz usunąć z domyślnej listy nagłówków w polu nagłówków. Jeśli [włączono funkcję Nagłówki odpowiedzi debugowania pamięci podręcznej,](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) `X-EC-Debug` należy dodać niezbędne nagłówki. 

Na przykład, aby `Via` usunąć nagłówek, pole nagłówków reguły powinno zawierać następującą listę nagłówków: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Reguła nagłówków specjalnych serwera proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

W poniższej tabeli opisano nagłówki, które mogą być dodawane przez verizon CDN POP w żądaniu:

Nagłówek żądania | Opis | Przykład
---------------|-------------|--------
[Via](#via-request-header) | Identyfikuje serwer POP, który przesunął żądanie do serwera pochodzenia. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | Wskazuje adres IP żądający.| 10.10.10.10
X-Forwarded-Proto | Wskazuje protokół żądania. | http
X-Host | Wskazuje nazwa hosta żądania. | cdn.mydomain.com
X-Midgress (X-Midgress) | Wskazuje, czy żądanie zostało przesunięty przez dodatkowy serwer SIECI CDN. Na przykład serwer osłony typu pop typu origin lub serwer POP-do-ADN gateway server. <br />Ten nagłówek jest dodawany do żądania tylko wtedy, gdy odbywa się ruch średni. W takim przypadku nagłówek jest ustawiony na 1, aby wskazać, że żądanie zostało przypisane przez dodatkowy serwer sieci CDN.| 1
[Host](#host-request-header) | Identyfikuje hosta i port, w którym można znaleźć żądaną zawartość. | marketing.mydomain.com:80
[X-Gateway-Lista](#x-gateway-list-request-header) | ADN: Identyfikuje listę trybu failover serwerów bramy ADN przypisanych do źródła klienta. <br />Osłona początkowa: wskazuje zestaw serwerów osłony początkowej przypisanych do źródła klienta. | `icn1,hhp1,hnd1`
X-EC-_&lt;nazwa&gt;_ | Nagłówki żądań, które zaczynają się od *X-EC* (na przykład X-EC-Tag, [X-EC-Debug)](cdn-http-debug-headers.md)są zarezerwowane do użycia przez sieć CDN.| produkcja waf

## <a name="via-request-header"></a>Za pomocą nagłówka żądania
Format, w `Via` którym nagłówek żądania identyfikuje serwer POP, jest określony przez następującą składnię:

`Via: Protocol from Platform (POP/ID)` 

Terminy używane w składni są zdefiniowane w następujący sposób:
- Protokół: wskazuje wersję protokołu (na przykład HTTP/1.1) używaną do proxy żądania. 

- Platforma: wskazuje platformę, na której zażądano zawartości. Dla tego pola obowiązują następujące kody: 

    Code | Platforma
    -----|---------
    EcAcc (europejski wypadek 201 | Duży protokół HTTP
    Ecs   | HTTP Mały
    Ecd   | Sieć dostarczania aplikacji (ADN)

- POP: Wskazuje [pop,](cdn-pop-abbreviations.md) który obsługiwał żądanie. 

- Identyfikator: Tylko do użytku wewnętrznego.

### <a name="example-via-request-header"></a>Przykład za pośrednictwem nagłówka żądania

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Nagłówek żądania hosta
Serwery POP zastąpią `Host` nagłówek, gdy oba poniższe warunki są spełnione:
- Źródłem żądanej zawartości jest serwer pochodzenia klienta.
- Odpowiednia opcja nagłówka hosta HTTP odpowiedniego źródła klienta nie jest pusta.

Nagłówek `Host` żądania zostanie zastąpiony w celu odzwierciedlenia wartości zdefiniowanej w opcji Nagłówek hosta HTTP.
Jeśli opcja nagłówka hosta HTTP pochodzenia klienta jest `Host` ustawiona na pustą, nagłówek żądania przesłany przez zwerytę zostanie przekazany do serwera pochodzenia klienta.

## <a name="x-gateway-list-request-header"></a>Nagłówek żądania x-gateway-list
Serwer POP doda/zastąpi nagłówek żądania X-Gateway-List, gdy spełniony jest jeden z następujących warunków:
- Żądanie wskazuje na platformę ADN.
- Żądanie jest przekazywane do serwera pochodzenia klienta, który jest chroniony przez funkcję Origin Shield.

