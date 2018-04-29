---
title: Nagłówki HTTP Verizon specyficzne dla aparatu zasad Azure CDN | Dokumenty Microsoft
description: W tym artykule opisano jak używać nagłówków HTTP specyficzne Verizon z aparat reguł Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: v-deasim
ms.openlocfilehash: 1a4bb48efe2a91c85b773341bb38b0f3ce4c7d9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Nagłówki HTTP Verizon specyficzne dla aparatu zasad Azure CDN

Dla **Azure CDN Premium z Verizon** produktów, gdy żądanie HTTP jest wysyłana do serwera pochodzenia, serwer punkt obecności (POP) można dodać jeden lub więcej nagłówki zarezerwowane (lub proxy specjalnych nagłówków) w żądaniu klienta do punktu POP. Oprócz standardowej przesyłania dalej odebrane nagłówki są te nagłówki. Szczegółowe informacje o nagłówkach żądań standardowych, [żądania pola](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Jeśli chcesz zapobiec dodawaniu w żądaniu POP Azure CDN (Content Delivery Network) z serwerem źródłowym przez jeden z tych nagłówków zarezerwowane, należy utworzyć regułę za pomocą [funkcji specjalnych nagłówków serwera Proxy](cdn-rules-engine-reference-features.md#proxy-special-headers) w aparat reguł. W tej regule należy wykluczyć nagłówka, który chcesz usunąć z listy domyślnego nagłówków w polu nagłówki. Jeśli została włączona opcja [funkcji debugowania pamięci podręcznej nagłówków odpowiedzi](cdn-rules-engine-reference-features.md#debug-cache-response-headers), należy dodać niezbędne `X-EC-Debug` nagłówków. 

Na przykład, aby usunąć `Via` nagłówek pola nagłówków reguły powinny zawierać następującą listę nagłówków: *X przekazane dla, X przekazywane-Proto, X-, X-Midgress X-brama-lista hostów, nazwa-X-WE, Host*. 

![Reguła specjalnych nagłówków serwera proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

W poniższej tabeli opisano nagłówki, które mogą być dodawane przez POP CDN Verizon w żądaniu:

Nagłówek żądania | Opis | Przykład
---------------|-------------|--------
[za pomocą](#via-request-header) | Identyfikuje serwer POP, to kierowanych na serwer proxy żądań do serwera źródłowego. | HTTP/1.1 ECS (dca/1A2B)
X przekazywane do | Wskazuje adres IP żądającego.| 10.10.10.10
X-Forwarded-Proto | Wskazuje żądanie protokołu. | http
X-Host | Wskazuje żądanie nazwy hosta. | CDN.myDomain.com
X Midgress | Wskazuje, czy wniosek został za pośrednictwem serwera proxy za pośrednictwem serwera usługi dodatkowe CDN. Na przykład serwer serwera do punktu początkowego tarcza POP lub serwer bramy serwera ADN POP. <br />Ten nagłówek jest dodawany do żądania tylko wtedy, gdy odbywa się ruch midgress. W tym przypadku nagłówek jest równa 1 do wskazania, że wniosek został za pośrednictwem serwera proxy za pośrednictwem serwera usługi dodatkowe CDN.| 1
[Host](#host-request-header) | Identyfikuje host i port, gdzie można znaleźć żądanej zawartości. | Marketing.myDomain.com:80
[X bramy listy](#x-gateway-list-request-header) | ADN: Identyfikuje pracy awaryjnej listę serwerów bramy ADN przypisany do pochodzenia odbiorcy. <br />Tarcza pochodzenia: Wskazuje zestaw serwerów tarcza pochodzenia przypisane do pochodzenia odbiorcy. | `icn1,hhp1,hnd1`
X-we -_&lt;nazwy&gt;_ | Nagłówków żądania, które zaczyna się od *X-we* (na przykład X-WE-Tag, [X-WE-Debug](cdn-http-debug-headers.md)) są zarezerwowane do użytku przez CDN.| waf produkcji

## <a name="via-request-header"></a>Za pomocą nagłówka żądania
Format, za pomocą którego `Via` żądania nagłówek identyfikuje serwer POP jest określona przy użyciu następującej składni:

`Via: Protocol from Platform (POP/ID)` 

Terminy używane w składni są zdefiniowane w następujący sposób:
- Protokół: Wskazuje, że wersja protokołu (na przykład HTTP/1.1) używana do serwera proxy żądania. 

- Platforma: Wskazuje platformy, na którego zażądano zawartości. Prawidłowa dla tego pola są następujące kody: 
    Kod | Platforma
    -----|---------
    ECAcc | Duże HTTP
    ECS   | Małych HTTP
    ECD   | Sieci dostarczania aplikacji (ADN)

- Punkt POP: Wskazuje [POP](cdn-pop-abbreviations.md) że obsłużone żądanie. 

- Identyfikator: tylko do użytku wewnętrznego.

### <a name="example-via-request-header"></a>Przykład za pomocą nagłówka żądania

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Nagłówek hosta żądania
Zastąpi serwerów POP `Host` nagłówka, gdy są spełnione oba z następujących warunków:
- Źródło dla żądanej zawartości jest serwer pochodzenia odbiorcy.
- Opcja Nagłówek hosta HTTP odpowiedniego pochodzenia odbiorcy nie jest puste.

`Host` Nagłówek żądania zostaną zastąpione w celu odzwierciedlenia wartości zdefiniowane w opcji Nagłówek hosta protokołu HTTP.
Jeśli ustawiono opcję Nagłówek hosta HTTP pochodzenia odbiorcy będzie puste, następnie `Host` nagłówka żądania, który jest przesyłany przez zgłaszającego żądanie zostanie przekazany do serwera źródłowego klienta.

## <a name="x-gateway-list-request-header"></a>Nagłówek X-brama-lista żądania
Serwer POP dodać/zastąpi "X-brama-List nagłówka żądania, gdy jest spełniony jeden z następujących warunków:
- Wniosek wskazuje na platformie ADN.
- Żądanie jest przekazywane do serwera pochodzenia odbiorcy, który jest chroniony przez funkcję tarczy pochodzenia.

