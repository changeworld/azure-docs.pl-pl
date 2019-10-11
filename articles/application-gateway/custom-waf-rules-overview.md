---
title: Reguły niestandardowe usługi Azure Web Application Firewall (WAF) v2
description: Ten artykuł zawiera omówienie reguł niestandardowych zapory aplikacji sieci Web (WAF) v2 w usłudze Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263561"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Przegląd: Reguły niestandardowe dla zapory aplikacji sieci Web v2

Usługa Azure Application Gateway Web Application Firewall (WAF) v2 udostępnia wstępnie skonfigurowany zestaw reguł zarządzany przez platformę, który oferuje ochronę wielu różnych typów ataków. Te ataki obejmują skrypty między lokacjami, iniekcję kodu SQL i inne. Jeśli jesteś administratorem WAF, możesz chcieć napisać własne reguły, aby rozszerzyć reguły podstawowego zestawu reguł. Reguły mogą blokować lub zezwalać na żądany ruch na podstawie kryteriów dopasowywania.

Za pomocą reguł niestandardowych można utworzyć własne reguły, które są oceniane dla każdego żądania, które przechodzi przez WAF. Reguły te mają wyższy priorytet niż pozostałe reguły w zarządzanych zestawach reguł. Reguły niestandardowe zawierają nazwę reguły, priorytet reguły i tablicę pasujących warunków. Jeśli te warunki są spełnione, podejmowana jest akcja zezwalająca na użycie lub zablokowanie.

Na przykład można utworzyć regułę, która będzie blokować wszystkie żądania z adresu IP w zakresie 192.168.5.4/24. W tej regule operator to *IPMatch*, *matchValues* jest zakresem adresów IP (192.168.5.4/24), a *Akcja* polega na zablokowaniu ruchu. Należy również ustawić nazwę i priorytet reguły.

Reguły niestandardowe obsługują używanie logiki złożonej do bardziej zaawansowanych reguł, które zaspokajają potrzeby związane z bezpieczeństwem. Na przykład "(warunek 1 *i* warunek 2) *lub* warunek 3)" oznacza, że jeśli spełniony jest warunek 1 *i* warunek 2 *lub* spełniony jest warunek 3, WAF powinien wykonać akcję określoną w regule niestandardowej.

Różne warunki dopasowywania w ramach tej samej reguły są zawsze składane przy użyciu *i*. Na przykład reguła, która używa *i* może określić, aby blokować ruch z określonego adresu IP i tylko wtedy, gdy jest używana określona przeglądarka.

Jeśli chcesz użyć *lub* dla dwóch różnych warunków, te dwa warunki muszą być w różnych regułach. Na przykład reguła, która używa *lub* może blokować ruch z określonego adresu IP lub blokować ruch, jeśli jest używana określona przeglądarka.

> [!NOTE]
> Maksymalna liczba reguł niestandardowych WAF to 100. Aby uzyskać więcej informacji na temat limitów Application Gateway, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-subscription-service-limits.md#application-gateway-limits).

Wyrażenia regularne są również obsługiwane w regułach niestandardowych, podobnie jak w podstawowych zestawach reguł. Aby zapoznać się z przykładami tych reguł, zobacz sekcję "Przykładowe 3" i "przykład 5" w temacie [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="allowing-or-blocking-traffic"></a>Zezwalanie lub blokowanie ruchu

Zezwalanie lub blokowanie ruchu jest proste z regułami niestandardowymi. Na przykład można zablokować cały ruch pochodzący z zakresu adresów IP. Można utworzyć kolejną regułę zezwalającą na ruch, jeśli żądanie pochodzi z określonej przeglądarki.

Aby zezwolić na coś, upewnij się, że parametr `-Action` ma ustawioną wartość **Zezwalaj**. Aby zablokować coś, upewnij się, że parametr `-Action` jest ustawiony jako **Block**, jak pokazano w poniższym kodzie:

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Poprzednie `$BlockRule` mapuje do następującej reguły niestandardowej w Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Ta reguła niestandardowa zawiera nazwę, priorytet, akcję i tablicę pasujących warunków, które muszą zostać spełnione, aby akcja została wykonana. Opisy pól reguł niestandardowych można znaleźć w poniższych sekcjach. Przykłady reguł niestandardowych można znaleźć w temacie [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="custom-rule-fields"></a>Pola reguły niestandardowej

### <a name="name-optional"></a>Nazwa (opcjonalnie)

To jest nazwa reguły. Nazwa zostanie wyświetlona w dziennikach.

### <a name="priority-required"></a>Priorytet (wymagane)

- Priorytet określa kolejność, w jakiej reguły są oceniane. Im niższa wartość, tym wcześniejsza Ocena reguły. Dozwolony zakres to od 1 do 100.
- Priorytet musi być unikatowy wśród wszystkich reguł niestandardowych. Reguła o priorytecie 40 jest szacowana przed regułą o priorytecie 80.

### <a name="rule-type-required"></a>Typ reguły (wymagane)

Obecnie typem reguły musi być **MatchRule**.

### <a name="match-variable-required"></a>Zmienna dopasowania (wymagana)

Zmienna Match musi mieć jedną z następujących wartości:

- RemoteAddr: adres IP lub nazwa hosta połączenia z komputerem zdalnym
- RequestMethod: metoda żądania HTTP (GET, POST, PUT, DELETE itd.).
- QueryString: zmienna w identyfikatorze URI.
- PostArgs: argumenty, które są wysyłane w treści wpisu. Reguły niestandardowe, które używają tej zmiennej dopasowania są stosowane tylko wtedy, gdy nagłówek Content-Type ma wartość "application/x-www-form-urlencoded" i "wieloczęściowy/form-Data".
- RequestUri: identyfikator URI żądania.
- RequestHeaders: nagłówki żądania.
- Elemencie requestbody: zmienna, która zawiera całą treść żądania jako całość. Reguły niestandardowe używające tej zmiennej Match są stosowane tylko wtedy, gdy nagłówek Content-Type jest ustawiony na wartość "application/x-www-form-urlencoded". 
- RequestCookies: pliki cookie żądania.

### <a name="selector-optional"></a>Selektor (opcjonalnie)

Selektor zawiera opis pola kolekcji matchVariable. Na przykład jeśli matchVariable ma wartość "RequestHeaders", selektor może znajdować się w nagłówku User-Agent.

### <a name="operator-required"></a>Operator (wymagane)

Operator musi być jednym z następujących:

- IPMatch: Ten operator jest używany tylko wtedy, gdy zmienna Match to *RemoteAddr*.
- Równa się: dane wejściowe są takie same jak MatchValue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Zaczyna się od
- EndsWith
- wyrażeń

### <a name="negate-condition-optional"></a>Warunek Negate (opcjonalny)

Wyklucza bieżący warunek.

### <a name="transform-optional"></a>Przekształć (opcjonalnie)

Lista ciągów z nazwami przekształceń do zakończenia przed próbą dopasowania. Transformacje obejmują:

- Małe litery
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Dopasuj wartości (wymagane)

Pole matchValues jest listą wartości do dopasowania, co może być uważane za *lub*"Ed". Na przykład wartości mogą być adresami IP lub innymi ciągami. Format wartości zależy od poprzedniego operatora.

### <a name="action-required"></a>Akcja (wymagana)

W polu akcja dostępne są następujące opcje: 

- Zezwalaj: autoryzuje transakcję i pomija wszystkie kolejne reguły. Oznacza to, że określone żądanie jest dodawane do listy dozwolonych, a po dopasowaniu żądanie przestanie być kontynuowane i jest wysyłane do puli zaplecza. Reguły, które znajdują się na liście dozwolonych, nie są oceniane pod kątem dalszych reguł niestandardowych lub zarządzanych reguł.

- Blokuj: blokuje transakcję na podstawie *SecDefaultAction* (tryb wykrywania/zapobiegania). Podobnie jak akcja zezwalania, gdy żądanie jest oceniane i dodawane do listy zablokowanych, szacowanie zostanie zatrzymane, a żądanie jest zablokowane. Kolejne żądania, które spełniają te same warunki, nie są oceniane. Są one blokowane. 

- Dziennik: umożliwia zapisanie reguły w dzienniku i umożliwia wykonywanie pozostałych reguł w celu oceny. Kolejne reguły niestandardowe są oceniane w kolejności priorytetu, po którym następuje reguła zarządzana.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już reguły niestandardowe, możesz [utworzyć własne reguły niestandardowe](create-custom-waf-rules.md).
