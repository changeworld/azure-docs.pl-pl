---
title: Niestandardowe reguły zapory aplikacji sieci Web Azure drzwiami frontowymi
description: Dowiedz się, jak używać reguł zapory aplikacji internetowych (WAF) niestandardowych chroni aplikacje sieci web przed złośliwymi atakami.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459712"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Niestandardowych reguł zapory aplikacji sieci web za pomocą usługi Azure drzwi
Zapory aplikacji sieci web platformy Azure (WAF) w usłudze drzwiami frontowymi umożliwia kontrolowanie dostępu do aplikacji sieci web na podstawie warunków, jaką zdefiniujesz. Niestandardowe reguły zapory aplikacji sieci Web składa się z liczbą priorytetu, typu reguły, warunki dopasowań i akcji. Istnieją dwa typy niestandardowych reguł: dopasowanie reguł i zasad limitu szybkości. Regułę dopasowania steruje dostępem, w oparciu o dopasowanie warunków, gdy reguła limitu szybkości kontroluje dostęp, w oparciu o dopasowanie warunków i stopień żądań przychodzących. Może wyłączyć regułę niestandardową, aby uniemożliwić oceniane, ale nadal utrzymuje konfiguracji. W tym artykule omówiono reguł dopasowania, które są oparte na parametry http.

## <a name="priority-match-conditions-and-action-types"></a>Priorytet, warunki dopasowań i typy akcji
Możesz kontrolować dostęp za pomocą niestandardową regułę zapory aplikacji sieci Web, definiujący numer priorytetu, typu reguły, warunki dopasowań i akcji. 

- **Priorytet:** jest unikatowa liczba całkowita, która opisuje kolejność oceny reguł zapory aplikacji sieci Web. Reguły o niższych wartościach, są sprawdzane przed reguły o wyższych wartościach

- **Akcja:** Określa, jak kierować żądania, jeśli reguły zapory aplikacji sieci Web jest zgodny. Można wybrać jedną z poniższych akcji do zastosowania, gdy żądanie pasuje do reguły niestandardowej.

    - *Zezwalaj na* — Zapora aplikacji sieci Web przesyła dalej żądanie odnowy na serwer zaplecza, dzienniki wpis w dzienniki zapory aplikacji sieci Web i umożliwia zamknięcie.
    - *Blok* -zablokowania żądania, zapory aplikacji sieci Web wysyła odpowiedź do klienta bez przekazywania żądania do zaplecza. Zapora aplikacji sieci Web rejestruje wpis w dziennikach zapory aplikacji sieci Web.
    - *Dziennik* — dzienniki zapory aplikacji sieci Web do wpisu w zapory aplikacji sieci Web rejestruje i kontynuuje oceny następną regułę.
    - *Przekieruj* — Zapora aplikacji sieci Web przekierowuje żądanie do określonego identyfikatora URI, rejestruje wpis w dziennikach zapory aplikacji sieci Web i kończy działanie.

- **Warunek dopasowania:** definiuje zmienną dopasowanie, operator i odpowiada wartości. Każda reguła może zawierać wiele warunków dopasowania. Warunek dopasowania mogą być oparte na poniżej *dopasowania zmienne*:
    - RemoteAddr (adres IP klienta)
    - requestMethod
    - Ciąg zapytania
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operator:** lista obejmuje następujące elementy:
    - Dowolny: jest często używana do definiowania domyślnej akcji, jeśli żadne reguły nie są spełnione. Wszelkie jest operator wszystkie dopasowania.
    - IPMatch: Definiowanie ograniczenia adresów IP dla zmiennej RemoteAddr
    - GeoMatch: Definiowanie geograficznie filtrowania dla zmiennej RemoteAddr
    - równe
    - zawiera
    - Mniejsze: ograniczenie rozmiaru
    - Większe: ograniczenie rozmiaru
    - Równe: ograniczenie rozmiaru
    - Równe: ograniczenie rozmiaru
    - Zaczyna się od
     - endsWith

Możesz ustawić *zanegowania* aby warunek miał wartość true, jeśli wynikiem warunku powinna być ujemna.

*Odpowiada wartości* definiuje listę wartości możliwe dopasowanie.
Obsługiwana metoda żądania HTTP, których wartości obejmują:
- GET
- POST
- PUT
- GŁÓWNY
- DELETE
- BLOKADY
- ODBLOKOWYWANIE
- PROFIL
- OPCJE
- PROPFIND
- PROPPATCH
- MKCOL
- KOPIUJ
- PRZENIEŚ

## <a name="examples"></a>Przykłady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Przykład niestandardowych reguł zapory aplikacji sieci Web na podstawie parametrów http

Oto przykład pokazujący konfigurację niestandardową regułę z dwóch warunków dopasowania. Żądania pochodzą z określonej lokacji, zgodnie z definicją odwołania, a ciąg zapytania nie zawiera "password".

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
Przykładowa konfiguracja do blokowania metody "PUT" jest wyświetlana poniżej:

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

Może tworzyć niestandardowe regułę, która określa ograniczenie rozmiaru część przychodzącego żądania. Na przykład poniżej reguła blokuje adres Url, który jest dłuższa niż 100 znaków.

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

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [zapory aplikacji sieci web](waf-overview.md)
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).

