---
title: Usługa Azure CDN przy użyciu mechanizmu CORS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Content Delivery Network (CDN) do za pomocą udostępniania zasobów między źródłami (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3dbf0aea50f382a0b325bf068a200cde42098733
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547601"
---
# <a name="using-azure-cdn-with-cors"></a>Usługa Azure CDN przy użyciu mechanizmu CORS
## <a name="what-is-cors"></a>Co to jest mechanizm CORS?
Mechanizm CORS (Cross współużytkowanie zasobów) to funkcja protokołu HTTP, który umożliwia aplikacji sieci web uruchomionej w jednej domenie dostęp do zasobów w innej domenie. Aby zmniejszyć prawdopodobieństwo ataki z użyciem skryptów między witrynami, wszystkie współczesne przeglądarki internetowe obsługują wdrażają ograniczenie bezpieczeństwa nazywane [zasadami tego samego źródła](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Zapobiega to strony sieci web wywoływaniu interfejsów API w innej domenie.  Mechanizm CORS zapewnia bezpieczną metodę umożliwiania jednej pochodzenia (domenie źródłowej) wywoływania interfejsów API z innego źródła.

## <a name="how-it-works"></a>Jak to działa
Istnieją dwa typy żądań CORPS *prostych żądań* i *złożonych żądań.*

### <a name="for-simple-requests"></a>W przypadku prostych żądań:

1. Przeglądarka wysyła żądanie CORS przy użyciu dodatkowego **pochodzenia** nagłówek żądania HTTP. Wartość tego nagłówka origin, która obsłużyła stronę nadrzędną, która jest zdefiniowana jako kombinacja *protokołu* *domeny* i *portu.*  Gdy strona z https://www.contoso.com próbuje uzyskać dostęp do danych użytkownika w początek fabrikam.com, następujący nagłówek żądania, które będą wysyłane na fabrikam.com:

   `Origin: https:\//www.contoso.com`

2. Serwer może odpowiadać z dowolnymi z następujących czynności:

   * **Access-Control-Allow-Origin** nagłówka w swojej odpowiedzi wskazujący witrynę pochodzenia, w której jest dozwolona. Na przykład:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kod błędu HTTP 403, jeśli serwer nie zezwala na żądania między źródłami po sprawdzeniu nagłówek źródła np.

   * **Access-Control-Allow-Origin** nagłówka z symbolem wieloznacznym, który zezwala na wszystkie pochodzenia:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>W przypadku złożonych żądań:

Złożone żądanie jest żądaniem CORS, w którym przeglądarki jest wymagany do wysłania *żądania wstępnego* (czyli wstępne sondy) przed wysłaniem rzeczywiste żądanie CORS. Żądania wstępnego pyta uprawnienia serwera, jeśli oryginalny CORS żądania można kontynuować i jest `OPTIONS` żądania do tego samego adresu URL.

> [!TIP]
> Aby uzyskać szczegółowe informacje na temat przepływów mechanizmu CORS i typowych pułapek, Wyświetl [przewodnik CORS dla interfejsów API REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Symboli wieloznacznych lub scenariuszy pojedynczego źródła
Mechanizm CORS w usłudze Azure CDN będą działać automatycznie bez dodatkowej konfiguracji po **Access-Control-Allow-Origin** nagłówka ustawiono symbolu wieloznacznego (*) lub pojedynczego źródła.  Sieć CDN będzie buforować odpowiedzi pierwszy i kolejnych żądań będzie używać tego samego nagłówka.

Jeśli zostały już wprowadzone żądań do usługi CDN przed CORS zostanie ustawiony na źródło, konieczne będzie przeczyścić zawartość dla zawartości punktu końcowego ponowne załadowanie zawartości przy użyciu **Access-Control-Allow-Origin** nagłówka.

## <a name="multiple-origin-scenarios"></a>Scenariuszy z wieloma źródłami
Jeśli musisz zezwolić na konkretnej listy źródeł, które mają być dozwolone do obsługi mechanizmu CORS elementy Pobierz nieco bardziej skomplikowane. Ten problem występuje, gdy usługa CDN buforuje **Access-Control-Allow-Origin** nagłówek pierwszego źródła CORS.  Gdy różne źródła CORS wykonuje kolejne żądanie, sieć CDN będzie służyć buforowane **Access-Control-Allow-Origin** nagłówka, który nie będzie zgodne.  Istnieje kilka sposobów, aby rozwiązać ten problem.

### <a name="azure-cdn-premium-from-verizon"></a>Usługa Azure CDN w warstwie Premium firmy Verizon
Najlepszym sposobem, aby włączyć tę opcję, jest użycie **Azure CDN Premium from Verizon**, który przedstawia niektóre funkcje zaawansowane. 

Konieczne będzie [Utwórz regułę](cdn-rules-engine.md) do sprawdzenia **pochodzenia** nagłówka w żądaniu.  Jeśli jest to prawidłowy punkt początkowy, reguła ustawi **Access-Control-Allow-Origin** nagłówek o pochodzeniu podany w żądaniu.  Jeśli źródło jest określone w **pochodzenia** nagłówka jest niedozwolona, reguły należy pominąć znak **Access-Control-Allow-Origin** nagłówka, który spowoduje, że przeglądarkę, aby odrzucić żądanie. 

Istnieją dwa sposoby, w tym aparat reguł. W obu przypadkach **Access-Control-Allow-Origin** nagłówek z serwera pochodzenia pliku jest ignorowany i aparat reguł sieci CDN w pełni zarządza dozwolone źródła CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jednego wyrażenia regularnego z wszystkie prawidłowe źródła
W takim przypadku utworzysz wyrażeń regularnych, które obejmuje wszystkie źródła, które chcesz zezwolić na: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Usługa Azure CDN Premium from Verizon** używa [zgodne wyrażeń regularnych języka Perl](https://pcre.org/) jako jego aparatu wyrażeń regularnych.  Można użyć narzędzia, takiego jak [regularnych wyrażeń 101](https://regex101.com/) do sprawdzania poprawności wyrażenie regularne.  Pamiętaj, że znak "/" jest nieprawidłowe w wyrażeniach regularnych, a nie musi być poprzedzone znakiem zmiany znaczenia, jednak ten znak ucieczki jest uznawany za najlepszym rozwiązaniem i jest oczekiwane przez niektóre moduły weryfikacji wyrażenia regularnego.
> 
> 

Jeśli wyrażenie regularne dopasowuje, zastępuje reguły **Access-Control-Allow-Origin** nagłówka (jeśli istnieje), ze źródła przy użyciu źródła, który wysłał żądanie.  Możesz również dodać dodatkowych nagłówków CORS, takich jak **dostępu — kontrola-Allow-Methods**.

![Przykład reguły z wyrażeniem regularnym](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Reguła nagłówka żądania dla każdego źródła.
Zamiast wyrażeń regularnych, zamiast tego można utworzyć regułę osobne dla każdego źródła mają być dozwolone, za pomocą **wieloznaczny nagłówka żądania** [dopasować stan](/previous-versions/azure/mt757336(v=azure.100)#Anchor_1). Podobnie jak w przypadku metoda wyrażenia regularnego, aparat reguł samodzielnie ustawia nagłówków CORS. 

![Przykład reguły bez wyrażeń regularnych](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> W przykładzie powyżej użytkowania symbol wieloznaczny * informuje aparat reguł, aby dopasować protokołów HTTP i HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Usługa Azure profilów standardowe usługi CDN
W profilach standardowa usługi Azure CDN (**Azure CDN w warstwie standardowa firmy Microsoft**, **Azure CDN Standard from Akamai**, i **Azure CDN Standard from Verizon**), tylko mechanizm Zezwalaj na dla wielu źródeł, bez użycia pochodzenia symboli wieloznacznych jest użycie [buforowanie ciągu zapytania](cdn-query-string.md). Włącz ustawienie ciągu zapytania dla punktu końcowego usługi CDN, a następnie użyj ciągu zapytania unikatowy dla żądań z każdej domeny dozwolone. To spowoduje oddzielny obiekt dla każdego ciągu zapytania unikatowy buforowania usługi CDN. To podejście jest idealne rozwiązanie, ponieważ spowoduje wiele kopii tego samego pliku pamięci podręcznej w usłudze CDN.  

