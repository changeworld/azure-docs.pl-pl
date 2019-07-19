---
title: Niestandardowa reguła zapory aplikacji sieci Web dla drzwi frontonu platformy Azure
description: Dowiedz się, jak używać niestandardowych reguł zapory aplikacji sieci Web (WAF), chroniąc aplikacje sieci Web przed złośliwymi atakami.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846257"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Reguły niestandardowe dla zapory aplikacji sieci Web z usługami frontonu platformy Azure
Zapora aplikacji sieci Web platformy Azure (WAF) z usługą front-drzwi umożliwia kontrolowanie dostępu do aplikacji sieci Web na podstawie zdefiniowanych warunków. Niestandardowa reguła WAF składa się z numeru priorytetu, typu reguły, warunków dopasowania i akcji. Istnieją dwa typy reguł niestandardowych: reguły dopasowania i reguły limitów szybkości. Reguła dopasowania kontroluje dostęp na podstawie warunków dopasowywania, natomiast reguła limitu szybkości kontroluje dostęp na podstawie zgodnych warunków i stawek żądań przychodzących. Można wyłączyć regułę niestandardową, aby zapobiec jej ocenie, ale nadal zachować konfigurację. W tym artykule omówiono reguły dopasowania, które są oparte na parametrach http.

## <a name="priority-match-conditions-and-action-types"></a>Priorytet, warunki dopasowania i typy akcji
Można kontrolować dostęp za pomocą niestandardowej reguły WAf, która definiuje numer priorytetu, typ reguły, warunki dopasowania i akcję. 

- **Priorytet:** jest unikatową liczbą całkowitą opisującą kolejność oceny reguł WAF. Reguły o niższych wartościach są oceniane przed regułami o wyższych wartościach.

- **Akcja:** definiuje sposób kierowania żądania w przypadku dopasowania reguły WAF. Można wybrać jedną z poniższych akcji do zastosowania, gdy żądanie jest zgodne z regułą niestandardową.

    - *Allow* -WAF przekazuje żądanie do zaplecza, rejestruje wpis w dziennikach WAF i kończy pracę.
    - Żądanie *blokowania* jest blokowane, WAF wysyła odpowiedź do klienta bez przesyłania dalej żądania do zaplecza. WAF rejestruje wpis w dziennikach WAF.
    - *Log* -WAF rejestruje wpis w dziennikach WAF i kontynuuje ocenę następnej reguły.
    - *Redirect* -WAF przekierowuje żądanie do określonego identyfikatora URI, rejestruje wpis w dziennikach WAF i kończy pracę.

- **Warunek dopasowania:** definiuje zmienną dopasowania, operatora i wartość Match. Każda reguła może zawierać wiele warunków dopasowywania. Warunek dopasowania może opierać się na poniższych *zmiennych dopasowania*:
    - RemoteAddr (adres IP klienta)
    - RequestMethod
    - Ciąg zapytania
    - PostArgs
    - RequestUri
    - RequestHeader
    - Elemencie requestbody

- **Operator:** lista zawiera następujące elementy:
    - Any: jest często używany do definiowania akcji domyślnej, jeśli nie są spełnione żadne reguły. Any jest operatorem Match ALL.
    - IPMatch: Zdefiniuj ograniczenie adresów IP dla zmiennej RemoteAddr
    - Geodopasowanie: Zdefiniuj filtrowanie geograficzne dla zmiennej RemoteAddr
    - Równa się
    - zawiera
    - LessThan: ograniczenie rozmiaru
    - GreaterThan: ograniczenie rozmiaru
    - LessThanOrEqual: ograniczenie rozmiaru
    - GreaterThanOrEqual: ograniczenie rozmiaru
    - Zaczyna się od
     - EndsWith

Warunek *Negate* można ustawić na wartość true, jeśli wynik warunku powinien być negacji.

*Wartość Match* definiuje listę możliwych do dopasowania wartości.
Obsługiwane wartości metod żądania HTTP to:
- GET
- POST
- PUT
- MTP
- DELETE
- SKRĘT
- ODBLOKOWANIA
- PROFILU
- OPCJE
- PROPFIND
- PROPPATCH
- MKCOL
- KOPIOWANE
- PRZENIEŚ

## <a name="examples"></a>Przykłady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Przykład WAF reguł niestandardowych opartych na parametrach http

Oto przykład, który pokazuje konfigurację niestandardowej reguły z dwoma warunkami dopasowania. Żądania pochodzą z określonej lokacji zdefiniowanej przez element odwołujący, a ciąg zapytania nie zawiera "hasła".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Przykładowa konfiguracja służąca do blokowania metody "PUT" jest pokazana poniżej:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Ograniczenie rozmiaru

Można utworzyć regułę niestandardową, która określa ograniczenie rozmiaru w części żądania przychodzącego. Na przykład niższa reguła blokuje adres URL dłuższy niż 100 znaków.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Następne kroki
- Informacje o [zaporze aplikacji sieci Web](waf-overview.md)
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).

