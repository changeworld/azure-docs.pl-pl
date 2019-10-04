---
title: Reguły niestandardowe usługi Azure Web Application Firewall (WAF) v2
description: Ten artykuł zawiera omówienie reguł niestandardowych zapory aplikacji sieci Web (WAF) v2 w usłudze Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 154317e558c2c9a22f569f569684cced467900d5
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937477"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Reguły niestandardowe dla zapory aplikacji sieci Web v2

Usługa Azure Application Gateway Web Application Firewall (WAF) v2 zawiera wstępnie skonfigurowane, zarządzane przez platform zestaw reguł, który oferuje ochronę wielu różnych typów ataków. Ataki te obejmują wykonywanie skryptów między lokacjami, iniekcja kodu SQL i inne. Jeśli jesteś administratorem WAF, możesz chcieć napisać własne reguły, aby rozszerzyć reguły podstawowego zestawu reguł (KSR). Reguły mogą blokować lub zezwalać na żądany ruch na podstawie kryteriów dopasowywania.

Reguły niestandardowe umożliwiają tworzenie własnych reguł, które są oceniane dla każdego żądania, które przechodzi przez WAF. Reguły te mają wyższy priorytet niż pozostałe reguły w zarządzanych zestawach reguł. Reguły niestandardowe zawierają nazwę reguły, priorytet reguły i tablicę pasujących warunków. Jeśli te warunki są spełnione, podejmowana jest akcja (do zezwalania lub blokowania).

Na przykład można zablokować wszystkie żądania z adresu IP w zakresie 192.168.5.4/24. W tej regule operator to *IPMatch*, matchValues jest zakresem adresów IP (192.168.5.4/24), a akcja polega na zablokowaniu ruchu. Należy również ustawić nazwę i priorytet reguły.

Reguły niestandardowe obsługują używanie logiki złożonej do bardziej zaawansowanych reguł, które zaspokajają potrzeby związane z bezpieczeństwem. Na przykład (warunek 1 **i** warunek 2) **lub** warunek 3).  Ten przykład oznacza, że jeśli spełniony jest warunek 1 **i** warunek 2 **lub** spełniony jest warunek 3, WAF powinien wykonać akcję określoną w regule niestandardowej.

Różne warunki dopasowywania w ramach tej samej reguły są zawsze składane przy użyciu **i**. Na przykład Zablokuj ruch z określonego adresu IP i tylko wtedy, gdy korzystają z określonej przeglądarki.

Jeśli chcesz **lub** dwa różne warunki, te dwa warunki muszą być w różnych regułach. Na przykład Zablokuj ruch z określonego adresu IP lub Zablokuj ruch, jeśli korzysta z określonej przeglądarki.

> [!NOTE]
> Maksymalna liczba reguł niestandardowych WAF to 100. Aby uzyskać więcej informacji na temat limitów Application Gateway, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-subscription-service-limits.md#application-gateway-limits).

Wyrażenia regularne są również obsługiwane w regułach niestandardowych, podobnie jak w zestawach reguł KSR. Aby zapoznać się z przykładami, zobacz przykłady 3 i 5 w temacie [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Zezwalanie na a blokowanie

Zezwalanie i blokowanie ruchu jest proste z regułami niestandardowymi. Na przykład można zablokować cały ruch pochodzący z zakresu adresów IP. Można utworzyć kolejną regułę zezwalającą na ruch, jeśli żądanie pochodzi z określonej przeglądarki.

Aby zezwolić na coś, upewnij się, że parametr `-Action` ma ustawioną wartość **Zezwalaj**. Aby zablokować coś, upewnij się, że parametr `-Action` ma ustawioną wartość **Blokuj**.

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

Ta reguła niestandardowa zawiera nazwę, priorytet, akcję i tablicę pasujących warunków, które muszą zostać spełnione, aby akcja została wykonana. Aby uzyskać więcej informacji na temat tych pól, zobacz następujące opisy pól. Aby zapoznać się z przykładowymi regułami niestandardowymi, zobacz [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Pola dla reguł niestandardowych

### <a name="name-optional"></a>Nazwa [opcjonalnie]

To jest nazwa reguły. Ta nazwa jest wyświetlana w dziennikach.

### <a name="priority-required"></a>Priorytet [wymagany]

- Określa kolejność, w której reguły są oceniane. Im niższa wartość, tym wcześniejsza Ocena reguły. Dozwolony zakres to od 1-100. 
- Musi być unikatowa wśród wszystkich reguł niestandardowych. Reguła o priorytecie 40 zostanie oceniona przed regułą o priorytecie 80.

### <a name="rule-type-required"></a>Typ reguły [wymagane]

Obecnie musi być **MatchRule**.

### <a name="match-variable-required"></a>Match — zmienna [wymagana]

Musi być jedną z zmiennych:

- RemoteAddr — adres IP/nazwa hosta połączenia z komputerem zdalnym
- RequestMethod — Metoda żądania HTTP (GET, POST, PUT, DELETE itd.)
- QueryString — zmienna w identyfikatorze URI
- PostArgs — argumenty wysyłane w treści wpisu. Reguły niestandardowe używające tej zmiennej Match są stosowane tylko wtedy, gdy nagłówek "Content-Type" jest ustawiony na wartość "application/x-www-form-urlencoded" i "wieloczęściowy/form-Data".
- RequestUri — identyfikator URI żądania
- RequestHeaders — nagłówki żądania
- Elemencie requestbody — zawiera całą treść żądania jako całość. Reguły niestandardowe używające tej zmiennej Match są stosowane tylko wtedy, gdy nagłówek "Content-Type" jest ustawiony na wartość "application/x-www-form-urlencoded". 
- RequestCookies — pliki cookie żądania

### <a name="selector-optional"></a>Selektor [opcjonalny]

Opisuje pole kolekcji matchVariable. Na przykład jeśli matchVariable jest RequestHeaders, selektor może znajdować się w nagłówku *User-Agent* .

### <a name="operator-required"></a>Operator [Required]

Musi być jednym z następujących operatorów:

- IPMatch — używany tylko wtedy, gdy zmienna Match jest *RemoteAddr*
- Equals — wartość wejściowa jest taka sama jak MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Zaczyna się od
- EndsWith
- Wyrażeń

### <a name="negate-condition-optional"></a>Negate warunek [opcjonalny]

Wyklucza bieżący warunek.

### <a name="transform-optional"></a>Przekształć [opcjonalnie]

Lista ciągów z nazwami transformacji, które należy wykonać przed próbą dopasowania. Mogą to być następujące przekształcenia:

- Małe litery
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Dopasuj wartości [wymagane]

Lista wartości do dopasowania, które mogą być uważane za "jako" *lub*"Ed". Mogą to być na przykład adresy IP lub inne ciągi. Format wartości zależy od poprzedniego operatora.

### <a name="action-required"></a>Akcja [wymagane]

- Zezwalaj — autoryzuje transakcję, pomijając wszystkie kolejne reguły. Oznacza to, że określone żądanie jest dodawane do listy dozwolonych i po dopasowaniu, żądanie przestanie działać w dalszej ocenie i zostanie wysłane do puli zaplecza. Reguły, które znajdują się na liście dozwolonych, nie są oceniane dla żadnych dalszych reguł niestandardowych lub reguł zarządzanych.
- Block — blokuje transakcję na podstawie *SecDefaultAction* (tryb wykrywania/zapobiegania). Podobnie jak akcja zezwalania, gdy żądanie jest oceniane i dodawane do listy zablokowanych, szacowanie zostanie zatrzymane, a żądanie jest zablokowane. Każde żądanie, które spełnia te same warunki, nie zostanie ocenione i zostanie po prostu zablokowane. 
- Log — umożliwia zapis reguły w dzienniku, ale umożliwia wykonywanie pozostałych reguł na potrzeby oceny. Kolejne reguły niestandardowe są oceniane w kolejności priorytetu, po którym następuje reguła zarządzana.

## <a name="next-steps"></a>Następne kroki

Po poznaniu reguł niestandardowych [Utwórz własne reguły niestandardowe](create-custom-waf-rules.md).
