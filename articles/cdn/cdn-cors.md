---
title: Przy użyciu usługi Azure CDN z CORS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure sieci dostarczania zawartości (CDN) do z udostępniania zasobów między źródłami (CORS).
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
ms.openlocfilehash: f9429e88525e27c0b6bad29d1927d53d05dfbcc8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="using-azure-cdn-with-cors"></a>Przy użyciu usługi Azure CDN z CORS
## <a name="what-is-cors"></a>Co to jest CORS?
CORS (Cross źródła Resource Sharing) to funkcja HTTP, która umożliwia aplikacja sieci web w jednej domenie dostęp do zasobów w innej domenie. Aby zmniejszyć ryzyko ataków skryptów między witrynami, wszystkie nowoczesne przeglądarki zaimplementować ograniczenia zabezpieczeń znany jako [zasad samego pochodzenia](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Zapobiega to strony sieci web na podstawie wywoływania interfejsów API w innej domenie.  Mechanizm CORS zapewnia bezpieczny sposób umożliwić jedno źródło (domeny pochodzenia) do wywoływania interfejsów API w inny początek.

## <a name="how-it-works"></a>Jak to działa
Istnieją dwa typy żądań CORPS *prostych żądań* i *złożonych żądań.*

### <a name="for-simple-requests"></a>Proste żądań:

1. Przeglądarka wysyła żądanie CORS z dodatkowymi **pochodzenia** nagłówek żądania HTTP. Wartość tego nagłówka jest punkt początkowy, który obsłużył Strona nadrzędna, który jest zdefiniowany jako kombinacja *protokołu,* *domeny,* i *portu.*  Gdy strony z https://www.contoso.com próbuje uzyskać dostęp do danych użytkownika w początek fabrikam.com, następujący nagłówek żądania wysłania do fabrikam.com:

   `Origin: https://www.contoso.com`

2. Serwer może odpowiadać za pomocą dowolnego z następujących czynności:

   * **Access-Control-Allow-Origin** nagłówka w swojej odpowiedzi wskazujący lokacji pochodzenia, do której jest dozwolona. Na przykład:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kod błędu HTTP, takie jak 403, jeśli serwer nie zezwala na żądania cross-origin po sprawdzeniu nagłówka źródła

   * **Access-Control-Allow-Origin** nagłówka z symbolem wieloznacznym, który zezwala na wszystkie pochodzenia:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Złożonych żądań:

Złożone żądanie jest żądaniem CORS, w którym przeglądarki jest wymagany do wysłania *żądania wstępnego* (to znaczy wstępne badanie) przed wysłaniem rzeczywiste żądanie CORS. Żądania wstępnego żąda uprawnienia serwera, jeśli oryginalny CORS żądanie może kontynuować i jest `OPTIONS` żądania do tego samego adresu URL.

> [!TIP]
> Więcej informacji dotyczących przepływów mechanizmu CORS i typowych problemów, można wyświetlić [przewodnik CORS dla interfejsów API REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Symbol wieloznaczny lub scenariuszy pojedynczego źródła
Mechanizm CORS w sieci CDN w warstwie Azure będą działać automatycznie bez konieczności dodatkowej konfiguracji po **Access-Control-Allow-Origin** nagłówka jest ustawiony na symbolu wieloznacznego (*) lub jednego źródła.  CDN będą buforowane pierwszej odpowiedzi i kolejne żądania będą używały tego samego nagłówka.

Jeśli wprowadzono już żądań do sieci CDN przed CORS ustawiania źródła, konieczne będzie przeczyścić zawartości na zawartość ponownie załaduj zawartość z punktu końcowego **Access-Control-Allow-Origin** nagłówka.

## <a name="multiple-origin-scenarios"></a>Wiele scenariuszy źródła
Jeśli musisz zezwolić określonej listy źródeł, które mogą być dla CORS, rzeczy uzyskać nieco bardziej skomplikowane. Problem występuje, gdy sieć CDN ma buforować **Access-Control-Allow-Origin** nagłówek dla pierwszego źródło CORS.  Gdy różne źródło CORS sprawia, że kolejne żądania, sieć CDN będzie służyć zapisane w pamięci podręcznej **Access-Control-Allow-Origin** nagłówek, który nie odpowiada.  Istnieje kilka sposobów, aby rozwiązać ten problem.

### <a name="azure-cdn-premium-from-verizon"></a>Usługa Azure CDN w warstwie Premium firmy Verizon
Najlepszym sposobem, aby je włączyć, jest użycie **Azure CDN Premium from Verizon**, który ujawnia niektóre zaawansowane funkcje. 

Konieczne będzie [utworzyć regułę](cdn-rules-engine.md) do sprawdzenia **pochodzenia** nagłówka w żądaniu.  Jeśli istnieje prawidłowy punkt początkowy reguły ustawi **Access-Control-Allow-Origin** nagłówek z pochodzenia podany w żądaniu.  Jeśli punkt początkowy określony w **pochodzenia** nagłówka nie jest dozwolona, należy pominąć reguły **Access-Control-Allow-Origin** nagłówka, co spowoduje przeglądarkę, aby odrzucić żądanie. 

Istnieją dwa sposoby, w tym celu z aparatu reguł. W obu przypadkach **Access-Control-Allow-Origin** nagłówka z serwera pochodzenia pliku jest ignorowany i aparatu reguł w sieci CDN w pełni zarządza dozwolonych źródeł CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jednego wyrażenia regularnego z wszystkie prawidłowe źródła
W takim przypadku utworzysz wyrażenie regularne, które zawiera wszystkie źródła, które chcesz zezwolić na: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium from Verizon** używa [zgodne wyrażeń regularnych języka Perl](http://pcre.org/) jako jego aparat wyrażeń regularnych.  Można użyć narzędzia, takiego jak [101 wyrażeń regularnych](https://regex101.com/) do sprawdzania poprawności z wyrażeniem regularnym.  Należy pamiętać, że znak "/" jest nieprawidłowe w wyrażeniach regularnych i nie należy wstawić jednak anulowanie ten znak jest traktowane jako najlepsze rozwiązanie i jest oczekiwane przez niektóre moduły weryfikacji wyrażenia regularnego.
> 
> 

Jeśli pasuje do wyrażenia regularnego, zastąpi reguły **Access-Control-Allow-Origin** nagłówek (jeśli istnieje), od źródła z pochodzenia, który wysłał żądanie.  Możesz także dodać dodatkowe nagłówki CORS, takich jak **dostępu-formant-Allow-Methods**.

![Przykład reguły z wyrażeniem regularnym](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Reguła nagłówka żądania dla każdego źródła.
Zamiast wyrażeń regularnych, zamiast tego można utworzyć regułę osobne dla każdego źródła mają być dozwolone, za pomocą **wieloznaczny nagłówka żądania** [dopasować stan](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Podobnie jak w przypadku metody wyrażenia regularnego samego silnika reguły ustawia nagłówki CORS. 

![Przykład reguły bez wyrażeń regularnych](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> W przykładzie powyżej stosowania symbol wieloznaczny * informuje aparatu reguł do dopasowania protokołów HTTP i HTTPS.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Azure CDN profile standardowe
Na Azure CDN standard profile (**Azure CDN Standard from Microsoft**, **Azure CDN Standard from Akamai**, i **Azure CDN Standard from Verizon**), tylko mechanizm Zezwalaj na wiele źródeł bez użycia pochodzenia symboli wieloznacznych jest użycie [buforowanie ciągu zapytania](cdn-query-string.md). Ustawienie ciągu zapytania dla punktu końcowego CDN, a następnie użyj ciągu zapytania unikatowy dla żądań z każdej domeny dozwolonych. W ten sposób spowoduje CDN buforowanie oddzielny obiekt dla każdego ciągu zapytania unikatowy. Ta metoda nie jest idealne, jednak zgodnie z spowoduje powstanie wielu kopii tego samego pliku pamięci podręcznej w sieci CDN.  

