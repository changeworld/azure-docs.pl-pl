---
title: Usługa Azure drzwiami frontowymi — nagłówki HTTP obsługuje | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć obsługiwanych protokołów nagłówka HTTP przy wejściu
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 40bfdfc3837da12f62864433508482a65def291c
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407321"
---
# <a name="azure-front-door-service---http-headers-support"></a>Usługa Azure drzwiami frontowymi — Obsługa nagłówków HTTP
W tym dokumencie przedstawiono protokołu, obsługującego usługi drzwiami frontowymi platformy Azure przy użyciu różnych części ścieżki wywołanie przedstawione na poniższej ilustracji. Poniższe sekcje zawierają więcej szczegółowych informacji nagłówków HTTP, który obsługuje drzwiami frontowymi.

![Azure protokołu nagłówki HTTP usługi drzwiami frontowymi][1]

>[!WARNING]
>Usługa drzwiami frontowymi nie zapewniają gwarancji na wszystkie nagłówki HTTP, które nie są opisane tutaj.

## <a name="1-client-to-front-door"></a>1. Klient drzwi
Drzwiami frontowymi akceptuje większość nagłówków z żądania przychodzącego (bez modyfikacji), jednak istnieją pewne zarezerwowanych nagłówki, które zostaną usunięte z żądania przychodzącego, jeśli są one wysyłane. W tym nagłówki z prefiksami następujące:
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Drzwi do wewnętrznej bazy danych

Drzwi wejściowe będą zawierać nagłówki z żądania przychodzącego, chyba że zostały one usunięte ze względu na ograniczenia, o których wspomniano powyżej. Drzwi zostaną dodane również następujące nagłówki:

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| Przez |  *Za pomocą: 1.1 azure* </br> Drzwiami frontowymi dodaje następuje "Azure", jako wartość pozycji za pomocą nagłówka wersji HTTP klienta. Jest ona dodawana do wskazania wersji HTTP klienta i tego drzwi Azure była pośrednich adresata dla żądania między klientem i wewnętrznej bazy danych.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Reprezentuje adres protokołu internetowego "client" skojarzony z żądaniem przetwarzany. Na przykład żądanie pochodzące z serwera proxy może dodać nagłówek X-Forwarded-dla do wskazywania adresu IP, oryginalnym obiektu wywołującego. |
| X-Azure-SocketIP | *X-Azure-SocketIP: 127.0.0.1* </br> Reprezentuje adres protokołu internetowego gniazda skojarzonego z połączeniem TCP, bieżące żądanie pochodzi z. Adres IP klienta z żądania nie może być równa jego adres IP gniazda, ponieważ może zostać arbitralnie zastąpiona przez użytkownika końcowego.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jest to ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwi wejściowe. Koniecznie do rozwiązywania problemów, ponieważ jest używany do wyszukiwania dzienników dostępu.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: przeskoki = 1* </br> Jest to nagłówek, w którym wejściu do wykrywania pętli żądania i użytkownicy nie powinna przyjmować zależności na nim. |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> Pola nagłówka X-Forwarded-For (XFF) HTTP jest powszechnie używaną metodą do identyfikowania źródłowy adres IP klienta, nawiązywania połączenia z serwerem sieci web za pośrednictwem protokołu HTTP serwera proxy lub moduł równoważenia obciążenia. Jeśli było istniejący nagłówek XFF, a następnie drzwiami frontowymi dołącza IP gniazda klienta do niej inne dodaje nagłówek XFF z adresem IP gniazda klienta. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> Pola nagłówka HTTP X-Forwarded-Host jest powszechnie używaną metodą do identyfikowania pierwotny host zażądane przez klienta w nagłówku żądania HTTP hosta, ponieważ nazwa hosta z drzwiami frontowymi mogą się różnić dla serwera wewnętrznej bazy danych, obsługa żądania. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Pola nagłówka HTTP X-Forwarded-Proto jest typowa metoda identyfikowania protokołu zainicjowanego żądania HTTP, ponieważ w zależności od konfiguracji drzwiami frontowymi mogą komunikować się z wewnętrznej bazy danych przy użyciu protokołu HTTPS, nawet w przypadku żądania zwrotnego serwera proxy HTTP. |

## <a name="3-front-door-to-client"></a>3. Drzwi do klienta

Poniżej przedstawiono nagłówki, które są wysyłane z wejściu do klientów. Wszelkie nagłówki wysyłane do drzwi z wewnętrznej bazy danych są przekazywane do klienta, jak również.

| Nagłówek  | Przykład |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jest to ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwi wejściowe. Koniecznie do rozwiązywania problemów, ponieważ jest używany do wyszukiwania dzienników dostępu.|

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png