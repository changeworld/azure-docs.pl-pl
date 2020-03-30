---
title: Drzwi frontowe platformy Azure — przekierowanie adresu URL | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć, jak usługi Azure Front Door obsługuje przekierowanie adresów URL dla ich tras, jeśli jest skonfigurowany.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295475"
---
# <a name="url-redirect"></a>Przekierowywanie adresów URL
Za pomocą usługi Azure Front Door można przekierować ruch. Można przekierować ruch na wielu poziomach (protokół, nazwa hosta, ścieżka, ciąg zapytania) i wszystkie funkcje mogą być skonfigurowane dla poszczególnych mikrousług, ponieważ przekierowanie jest oparte na ścieżce. Upraszcza to konfigurację aplikacji, optymalizuje użycie zasobów i obsługuje nowe scenariusze przekierowania, w tym przekierowanie globalne i oparte na ścieżce.
</br>

![Przekierowanie adresu URL drzwiami frontowymi platformy Azure][1]

## <a name="redirection-types"></a>Typy przekierowania
Typ przekierowania ustawia kod stanu odpowiedzi dla klientów, aby zrozumieć cel przekierowania. Obsługiwane są następujące typy przekierowań:

- **301 (Przeniesiony na stałe)**: Wskazuje, że zasobu docelowego został przypisany nowy stały identyfikator URI i wszelkie przyszłe odwołania do tego zasobu powinny używać jednego z uris ujęte w załączeniu. Użyj kodu stanu 301 dla przekierowania HTTP do HTTPS. 
- **302 (Znaleziono)**: Wskazuje, że zasób docelowy znajduje się tymczasowo w ramach innego identyfikatora URI. Ponieważ przekierowanie może być zmieniane od czasu do czasu, klient powinien nadal używać identyfikatora URI żądania efektywnego dla przyszłych żądań.
- **307 (przekierowanie tymczasowe)**: Wskazuje, że zasób docelowy znajduje się tymczasowo w ramach innego identyfikatora URI, a agent użytkownika NIE MOŻE zmieniać metody żądania, jeśli wykonuje automatyczne przekierowanie do tego identyfikatora URI. Ponieważ przekierowanie może ulec zmianie w czasie, klient powinien kontynuować przy użyciu oryginalnego identyfikatora URI żądania efektywnego dla przyszłych żądań.
- **308 (stałe przekierowanie)**: Wskazuje, że zasobu docelowego został przypisany nowy stały identyfikator URI i wszelkie przyszłe odwołania do tego zasobu powinny używać jednego z ujętych identyfikatorów URI. Klienci z możliwością edycji łączy powinni automatycznie ponownie łączyć odwołania do identyfikatora URI żądania efektywnego z jednym lub kilkoma nowymi odwołaniami wysyłanymi przez serwer, jeśli to możliwe.

## <a name="redirection-protocol"></a>Protokół przekierowania
Można ustawić protokół, który będzie używany do przekierowania. Pozwala to na jeden z najczęstszych przypadków użycia funkcji przekierowania, czyli ustawienie przekierowania HTTP na HTTPS.

- **Tylko HTTPS**: Ustaw protokół tylko na HTTPS, jeśli chcesz przekierować ruch z HTTP do HTTPS. Usługa Azure Front Door zaleca, aby zawsze ustawiać przekierowanie tylko na HTTPS.
- **Tylko HTTP**: Spowoduje to przekierowanie żądania przychodzącego do protokołu HTTP. Tej wartości należy używać tylko wtedy, gdy chcesz zachować ruch HTTP, który jest niezaszyfrowany.
- **Żądanie dopasowania:** Ta opcja zachowuje protokół używany przez żądanie przychodzące. Tak więc żądanie HTTP pozostaje HTTP i żądanie HTTPS pozostaje przekierowanie postu HTTPS.

## <a name="destination-host"></a>Host docelowy
W ramach konfigurowania routingu przekierowania można również zmienić nazwę hosta lub domenę dla żądania przekierowania. To pole można ustawić tak, aby zmienić nazwa hosta w adresie URL przekierowania lub w inny sposób zachować nazwa hosta z żądania przychodzącego. Tak więc za pomocą tego pola `https://www.contoso.com/*` można `https://www.fabrikam.com/*`przekierować wszystkie żądania wysłane do programu .

## <a name="destination-path"></a>Ścieżka docelowa
W przypadkach, w których chcesz zastąpić segment ścieżki adresu URL jako część przekierowania, możesz ustawić to pole z nową wartością ścieżki. W przeciwnym razie można zachować wartość ścieżki jako część przekierowania. Tak więc, korzystając z tego pola, `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`można przekierować wszystkie żądania wysyłane do programu .

## <a name="query-string-parameters"></a>Parametry ciągu kwerendy
Można również zastąpić parametry ciągu zapytania w przekierowanym adresie URL. Aby zastąpić istniejący ciąg zapytania z adresu URL żądania przychodzącego, ustaw to pole na "Zamień", a następnie ustaw odpowiednią wartość. W przeciwnym razie można zachować oryginalny zestaw ciągów zapytań, ustawiając pole na "Zachowaj". Na przykład za pomocą tego pola można przekierować cały ruch wysyłany do `https://www.contoso.com/foo/bar` programu `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`. 

## <a name="destination-fragment"></a>Fragment docelowy
Fragment docelowy jest częścią adresu URL po "#", zwykle używanej przez przeglądarki do wylądowania w określonej sekcji na stronie. To pole można ustawić tak, aby dodać fragment do adresu URL przekierowania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png