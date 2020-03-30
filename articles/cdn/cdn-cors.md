---
title: Korzystanie z usługi Azure CDN z usługą CORS | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z usługi Azure Content Delivery Network (CDN) w przypadku udostępniania zasobów między źródłami (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 169de21b6dbdafaaeff64e315daa104f3b6faadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278097"
---
# <a name="using-azure-cdn-with-cors"></a>Korzystanie z usługi Azure CDN z usługą CORS
## <a name="what-is-cors"></a>Co to jest mechanizm CORS?
CORS (Cross Origin Resource Sharing) to funkcja HTTP, która umożliwia aplikacji sieci web działającej w jednej domenie dostęp do zasobów w innej domenie. Aby ograniczyć możliwość ataków skryptów między witrynami, wszystkie nowoczesne przeglądarki internetowe implementują ograniczenie bezpieczeństwa znane jako [zasady tego samego pochodzenia.](https://www.w3.org/Security/wiki/Same_Origin_Policy)  Uniemożliwia to stronie sieci Web wywoływanie interfejsów API w innej domenie.  Cors zapewnia bezpieczny sposób, aby umożliwić jednego pochodzenia (domeny pochodzenia) do wywoływania interfejsów API w innym źródle.

## <a name="how-it-works"></a>Jak to działa
Istnieją dwa typy żądań CORS, *proste żądania* i *złożone żądania.*

### <a name="for-simple-requests"></a>W przypadku prostych żądań:

1. Przeglądarka wysyła żądanie CORS z dodatkowym nagłówkiem żądania **HTTP pochodzenia.** Wartość tego nagłówka jest źródłem, który służył stronie nadrzędnej, która jest zdefiniowana jako kombinacja *protokołu,* *domeny* i *portu.*  Gdy strona https\://www.contoso.com próbuje uzyskać dostęp do danych użytkownika w fabrikam.com źródła, do fabrikam.com zostanie wysłany następujący nagłówek żądania:

   `Origin: https://www.contoso.com`

2. Serwer może odpowiedzieć, wykonując następujące czynności:

   * Nagłówek **Access-Control-Allow-Origin** w odpowiedzi wskazującej, która lokacja pochodzenia jest dozwolona. Przykład:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kod błędu HTTP, taki jak 403, jeśli serwer nie zezwala na żądanie cross-origin po sprawdzeniu nagłówka Origin

   * Nagłówek **Access-Control-Allow-Origin** z symbolem wieloznacznym, który umożliwia wszystkie źródła:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>W przypadku złożonych żądań:

Złożone żądanie jest żądaniem CORS, w którym przeglądarka jest wymagana do wysłania *żądania inspekcji wstępnej* (czyli wstępnej sondy) przed wysłaniem rzeczywistego żądania CORS. Żądanie inspekcji wstępnej pyta o uprawnienie serwera, jeśli oryginalne `OPTIONS` żądanie CORS można kontynuować i jest żądaniem do tego samego adresu URL.

> [!TIP]
> Aby uzyskać więcej informacji na temat przepływów CORS i typowych pułapek, zobacz [Przewodnik po CORS dla interfejsów API REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Wieloznaczne lub scenariusze pojedynczego pochodzenia
Usługa CORS w usłudze Azure CDN będzie działać automatycznie bez dodatkowej konfiguracji, gdy nagłówek **Access-Control-Allow-Origin** jest ustawiony na symbol wieloznaczny (*) lub pojedyncze źródło.  Sieć CDN będzie buforować pierwszą odpowiedź, a kolejne żądania będą używać tego samego nagłówka.

Jeśli żądania zostały już złożone do sieci CDN przed cors jest ustawiony na źródła, należy przeczyścić zawartość do zawartości punktu końcowego, aby ponownie załadować zawartość z **nagłówka Access-Control-Allow-Origin.**

## <a name="multiple-origin-scenarios"></a>Wiele scenariuszy pochodzenia
Jeśli chcesz zezwolić na określoną listę pochodzenia, aby umożliwić CORS, sprawy stają się nieco bardziej skomplikowane. Ten problem występuje, gdy sieć CDN buforuje nagłówek **Access-Control-Allow-Origin** dla pierwszego źródła CORS.  Gdy inne źródło CORS złoży kolejne żądanie, sieć CDN będzie obsługiwać buforowany nagłówek **Access-Control-Allow-Origin,** który nie będzie zgodny.  Istnieje kilka sposobów, aby to poprawić.

### <a name="azure-cdn-standard-profiles"></a>Profile standardowe usługi Azure CDN
W usłudze Azure CDN Standard firmy Microsoft można utworzyć regułę w [silniku reguł standardowych,](cdn-standard-rules-engine-reference.md) aby sprawdzić nagłówek **Pochodzenia** w żądaniu. Jeśli jest to prawidłowe pochodzenie, reguła ustawi nagłówek **Access-Control-Allow-Origin** z żądaną wartością. W takim przypadku nagłówek **Access-Control-Allow-Origin** z serwera pochodzenia pliku jest ignorowany, a aparat reguł sieci CDN całkowicie zarządza dozwolonymi źródłami CORS.

![Przykład reguł z aparatem reguł standardowych](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Do reguły można dodać dodatkowe akcje, aby zmodyfikować dodatkowe nagłówki odpowiedzi, takie jak **Access-Control-Allow-Methods**.
> 

W **usłudze Azure CDN Standard firmy Akamai**jedynym mechanizmem umożliwiającym korzystanie z wielu źródeł bez użycia symbolu wieloznacznego jest użycie [buforowania ciągów zapytań.](cdn-query-string.md) Włącz ustawienie ciągu zapytania dla punktu końcowego usługi CDN, a następnie użyj unikatowego ciągu zapytania dla żądań z każdej dozwolonej domeny. Spowoduje to buforowanie sieci CDN oddzielny obiekt dla każdego unikatowego ciągu zapytania. Takie podejście nie jest jednak idealne, ponieważ spowoduje to wiele kopii tego samego pliku buforowanego w sieci CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Usługa Azure CDN Premium firmy Verizon
Korzystając z aparatu reguł Verizon Premium, musisz [utworzyć regułę,](cdn-rules-engine.md) aby sprawdzić nagłówek **Origin** w żądaniu.  Jeśli jest to prawidłowe pochodzenie, reguła ustawi nagłówek **Access-Control-Allow-Origin** z początkiem podanym w żądaniu.  Jeśli źródło określone w nagłówku **Pochodzenia** nie jest dozwolone, reguła powinna pominąć nagłówek **Access-Control-Allow-Origin,** co spowoduje, że przeglądarka odrzuci żądanie. 

Istnieją dwa sposoby, aby to zrobić za pomocą silnika reguł premium. W obu przypadkach nagłówek **Access-Control-Allow-Origin** z serwera pochodzenia pliku jest ignorowany, a aparat reguł sieci CDN całkowicie zarządza dozwolonymi źródłami CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jedno wyrażenie regularne ze wszystkimi prawidłowymi źródłami
W takim przypadku utworzysz wyrażenie regularne, które zawiera wszystkie źródła, na które chcesz zezwolić: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Usługa Azure CDN Premium firmy Verizon** używa [wyrażeń regularnych zgodnych z perl](https://pcre.org/) jako aparatu dla wyrażeń regularnych.  Można użyć narzędzia, takiego jak [wyrażenia regularne 101,](https://regex101.com/) aby sprawdzić poprawność wyrażenia regularnego.  Należy zauważyć, że znak "/" jest prawidłowy w wyrażeniach regularnych i nie musi być zmieniany, jednak ucieczka tego znaku jest uważana za najlepszą praktykę i jest oczekiwana przez niektórych modułów sprawdzania poprawności.
> 
> 

Jeśli wyrażenie regularne jest zgodne, reguła zastąpi nagłówek **Access-Control-Allow-Origin** (jeśli istnieje) od źródła pochodzenia źródłem, który wysłał żądanie.  Można również dodać dodatkowe nagłówki CORS, takie jak **Metody kontroli dostępu.**

![Przykład reguł z wyrażeniem regularnym](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Zażądaj reguły nagłówka dla każdego źródła.
Zamiast wyrażeń regularnych można zamiast tworzyć osobną regułę dla każdego źródła, na które chcesz zezwolić przy użyciu [warunku dopasowania](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) **symboli wieloznacznych nagłówka żądania** . Podobnie jak w przypadku metody wyrażenia regularnego, aparat reguł sam ustawia nagłówki CORS. 

![Przykład reguł bez wyrażenia regularnego](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> W powyższym przykładzie użycie symbolu wieloznacznego * informuje aparat reguł, aby dopasować zarówno HTTP i HTTPS.
> 
> 



