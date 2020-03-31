---
title: Reguła niestandardowa zapory aplikacji sieci Web dla drzwi ami frontowymi platformy Azure
description: Dowiedz się, jak używać reguł niestandardowych Zapory aplikacji sieci Web (WAF) chroniących aplikacje internetowe przed złośliwymi atakami.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475828"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Niestandardowe reguły zapory aplikacji sieci Web z drzwiami frontowymi platformy Azure

Zapora aplikacji sieci Web azure (WAF) z drzwiami frontowymi umożliwia kontrolowanie dostępu do aplikacji sieci web na podstawie warunków, które definiujesz. Niestandardowa reguła WAF składa się z numeru priorytetu, typu reguły, warunków dopasowania i akcji. Istnieją dwa typy reguł niestandardowych: dopasowyw reguły i reguły limitu szybkości. Reguła dopasowania kontroluje dostęp na podstawie zestawu pasujących warunków, podczas gdy reguła limitu szybkości kontroluje dostęp na podstawie pasujących warunków i stawek przychodzących żądań. Można wyłączyć regułę niestandardową, aby zapobiec jej ocenie, ale nadal zachować konfigurację. 

## <a name="priority-match-conditions-and-action-types"></a>Priorytet, warunki dopasowania i typy akcji

Dostęp można kontrolować za pomocą niestandardowej reguły WAf, która definiuje numer priorytetu, typ reguły, tablicę warunków dopasowania i akcję. 

- **Priorytet:** jest unikatową całkowitej liczby, która opisuje kolejność oceny reguł WAF. Reguły o niższym priorytecie są oceniane przed regułami z wyższymi wartościami. Numery priorytetów muszą być unikatowe dla wszystkich reguł niestandardowych.

- **Akcja:** określa sposób kierowania żądania, jeśli reguła WAF jest dopasowana. Możesz wybrać jedną z poniższych akcji do zastosowania, gdy żądanie pasuje do reguły niestandardowej.

    - *Zezwól* — WAF przekazuje zadanie do zaplecza, rejestruje wpis w dziennikach i wyjściach WAF.
    - *Blok* — żądanie jest zablokowane, WAF wysyła odpowiedź do klienta bez przekazywania żądania do zaplecza. WAF rejestruje wpis w dziennikach WAF.
    - *Dziennik* — WAF rejestruje wpis w dziennikach WAF i kontynuuje ocenę następnej reguły.
    - *Przekierowanie* — WAF przekierowuje żądanie do określonego identyfikatora URI, rejestruje wpis w dziennikach WAF i kończy pracę.

- **Warunek dopasowania:** definiuje zmienną dopasowania, operator i wartość dopasowania. Każda reguła może zawierać wiele warunków dopasowania. Warunek dopasowania może być oparty na lokalizacji geograficznej, adresach IP klienta (CIDR), rozmiar lub dopasowanie ciągu. Dopasowanie ciąg może być przeciwko liście zmiennych dopasowania.
  - **Dopasuj zmienną:**
    - RequestMetoda
    - QueryString
    - PostArgs (PostArgs)
    - Requesturi
    - RequestHeader (Żądajgłęcza)
    - RequestBody (Ciało żądania)
    - Pliki cookie
  - **Operator:**
    - Dowolny: jest często używany do definiowania akcji domyślnej, jeśli żadne reguły nie są dopasowane. Każdy jest dopasowanie wszystkich operatorów.
    - Równa się
    - Contains
    - LessThan: ograniczenie rozmiaru
    - GreaterThan: ograniczenie rozmiaru
    - LessThanOrEqual: ograniczenie rozmiaru
    - GreaterThanOrEqual: ograniczenie rozmiaru
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Program Regex** nie obsługuje następujących operacji: 
    - Wnioski wsteczne i przechwytywanie podwyrażenia
    - Dowolne potwierdzenia o zerowej szerokości
    - Odwołania do podprogramu i wzorce cykliczne
    - Wzorce warunkowe
    - Czasowniki sterujące wycofywaniem
    - Dyrektywa jedno bajtowa \C
    - Dyrektywa \R newline match
    - Dyrektywa \K start resetowania meczu
    - Objaśnienia i kod osadzony
    - Grupowanie atomowe i kwantyfikatory dzierżawcze

  - **Negata [opcjonalnie]:** Warunek *negacji* można ustawić na true, jeśli wynik warunku powinien zostać zanegowany.
      
  - **Przekształć [opcjonalnie]:** Lista ciągów z nazwami przekształceń do wykonania przed próbą dopasowania. Mogą to być następujące przekształcenia:
     - Wielkie litery 
     - Małe litery
     - Trim
     - Usuńulki
     - Kod Url
     - Urlencode
     
   - **Wartość dopasowania:** Obsługiwane wartości metody żądania HTTP obejmują:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - Blokady
     - Odblokować
     - PROFIL
     - Opcje
     - PROPFIND
     - Proppatch
     - Mkcol
     - Kopii
     - Przenieść

## <a name="examples"></a>Przykłady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Przykład reguł niestandardowych WAF opartych na parametrach http

Oto przykład, który pokazuje konfigurację reguły niestandardowej z dwóch warunków dopasowania. Żądania pochodzą z określonej witryny zdefiniowanej przez stronę odsyłającą, a ciąg zapytania nie zawiera "hasła".

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
Przykładowa konfiguracja blokowania metody "PUT" jest wyświetlana poniżej:

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

Można utworzyć regułę niestandardową, która określa ograniczenie rozmiaru dla części żądania przychodzącego. Na przykład poniższa reguła blokuje adres URL dłuższy niż 100 znaków.

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
- [Konfigurowanie zasad zapory aplikacji sieci Web przy użyciu programu Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Dowiedz się więcej o [zapory aplikacji sieci Web z drzwiami przednimi](afds-overview.md)
- Dowiedz się, jak [utworzyć usługę Front Door](../../frontdoor/quickstart-create-front-door.md).

