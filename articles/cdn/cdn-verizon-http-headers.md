---
title: Nagłówki HTTP specyficzne dla firmy Verizon dla aparatu zasad usługi Azure CDN | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak nagłówki HTTP specyficzne dla firmy Verizon za pomocą usługi Azure CDN aparat reguł.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: 7ce845fb272cea1d621e8ccc18203e3a071e8c29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60323289"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Nagłówki HTTP specyficzne dla firmy Verizon dla usługi Azure CDN aparatu reguł

Aby uzyskać **Azure CDN Premium from Verizon** produktów, gdy żądania HTTP są wysyłane do serwera pochodzenia serwera (POP) typu "punktu obecność" dodać co najmniej jeden zastrzeżony nagłówki (lub serwera proxy specjalnych nagłówków) w żądaniu klienta do POP. Tych nagłówków w niniejszym dokumencie stanowią standardowy przekazywania odebrane nagłówki. Aby uzyskać informacji o nagłówkach żądań standardowych, zobacz [żądania pola](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Jeśli chcesz uniemożliwić dodanie w żądaniu POP usługi Azure CDN (Content Delivery Network) do serwera pochodzenia przez jeden z tych nagłówków zarezerwowane, należy utworzyć regułę za pomocą [funkcji specjalnych nagłówków Proxy](cdn-rules-engine-reference-features.md#proxy-special-headers) przez aparat reguł. W tej regule Wyklucz nagłówkiem którego chcesz usunąć z domyślną listę nagłówków w polu nagłówków. Po włączeniu [funkcja Debuguj pamięć podręczna nagłówki odpowiedzi](cdn-rules-engine-reference-features.md#debug-cache-response-headers), pamiętaj dodać niezbędne `X-EC-Debug` nagłówków. 

Na przykład, aby usunąć `Via` nagłówka, pola nagłówków reguły powinien zawierać następującą listę nagłówków: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Reguła specjalnych nagłówków serwera proxy](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

W poniższej tabeli opisano nagłówki, które mogą być dodawane przez POP sieci CDN firmy Verizon, w żądaniu:

Nagłówek żądania | Opis | Przykład
---------------|-------------|--------
[za pomocą](#via-request-header) | Identyfikuje serwer protokołu POP, który przekazywane żądanie do serwera pochodzenia. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | Wskazuje adres IP żądającego.| 10.10.10.10
X-Forwarded-Proto | Wskazuje żądanie protokołu. | http
X-Host | Wskazuje nazwy hosta żądania. | cdn.mydomain.com
X Midgress | Wskazuje, czy żądanie zostało przekazywane za pośrednictwem dodatkowy serwer usługi CDN. Na przykład serwer protokołu POP tarczy źródła serwera lub serwer bramy z serwera do sieci ADN POP. <br />Tego pliku nagłówkowego jest dodawana do żądania tylko wtedy, gdy ruch midgress ma miejsce. W tym przypadku nagłówek jest równa 1, aby wskazać, że żądanie zostało przekazywane za pośrednictwem dodatkowy serwer usługi CDN.| 1
[Host](#host-request-header) | Identyfikuje hosta i port, gdzie można znaleźć żądanej zawartości. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: Umożliwia określenie listy trybu failover serwerów bramy sieci ADN przypisanych do źródłem klienta. <br />Tarczy źródła: Wskazuje zestaw serwerów tarczy pochodzenia przypisanych do źródłem klienta. | `icn1,hhp1,hnd1`
X-EC-_&lt;name&gt;_ | Nagłówki żądania, które zaczynają się od *WE X* (na przykład X-WE-Tag [X-WE-Debug](cdn-http-debug-headers.md)) są zarezerwowane do użytku przez usługę CDN.| Zapora aplikacji sieci Web produkcji

## <a name="via-request-header"></a>Za pomocą nagłówka żądania
Format za pomocą którego `Via` żądania nagłówek identyfikuje serwer protokołu POP określono za pomocą następującej składni:

`Via: Protocol from Platform (POP/ID)` 

Terminy używane w składni są zdefiniowane w następujący sposób:
- Protokół: Wskazuje, że używana wersja protokołu (na przykład HTTP/1.1) do serwera proxy żądania. 

- Platforma: Wskazuje platformy, na którym żądano zawartości. Następujące kody są prawidłowe dla tego pola: 

    Kod | Platforma
    -----|---------
    ECAcc | Duże HTTP
    ECS   | Mała liczba godzin HTTP
    ECD   | Sieć dostarczania aplikacji (sieci ADN)

- POP: Wskazuje [POP](cdn-pop-abbreviations.md) , obsługi żądania. 

- Identyfikator: Tylko do użytku wewnętrznego.

### <a name="example-via-request-header"></a>Przykład za pomocą nagłówka żądania

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Nagłówek żądania hosta
Serwery punktu POP spowoduje zastąpienie `Host` nagłówka, gdy są spełnione oba poniższe warunki:
- Źródło dla żądanej zawartości jest serwer pochodzenia klienta.
- Opcja nagłówka hosta HTTP odpowiedniego źródłem klienta nie jest pusta.

`Host` Nagłówek żądania zostaną zastąpione w celu odzwierciedlenia wartości zdefiniowanej w opcji nagłówka hosta HTTP.
Jeśli źródłem klienta nagłówka hosta HTTP opcji jest ustawiona na pustą następnie `Host` nagłówek żądania, który jest przesyłany przez osoby żądającej zostaną przekazane do serwera pochodzenia przez klienta.

## <a name="x-gateway-list-request-header"></a>Nagłówek żądania X-Gateway-List
Serwer protokołu POP Dodaj/zastąpi "nagłówek żądania X-Gateway-List po spełnieniu jednego z następujących warunków:
- Wskazuje żądanie platformę sieci ADN.
- Żądanie jest przekazywane do serwera pochodzenia klienta, który jest chroniony przez funkcję tarczy źródła.

