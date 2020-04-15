---
title: Reguły niestandardowe zapory aplikacji sieci Azure (WAF) w wersji 2 w bramie aplikacji
description: Ten artykuł zawiera omówienie reguł niestandardowych zapory aplikacji sieci Web (WAF) w wersji 2 w usłudze Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: c0f802f5113e38e811c110ee913099e76fa7be0b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383809"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Niestandardowe reguły zapory aplikacji sieci Web w wersji 2 w bramie aplikacji platformy Azure

Zapora aplikacji azure gateway web application (WAF) w wersji 2 jest wyposażona w wstępnie skonfigurowany zestaw reguł zarządzanych przez platformę, który oferuje ochronę przed wieloma różnymi typami ataków. Ataki te obejmują skrypty między witrynami, iniekcji SQL i innych. Jeśli jesteś administratorem WAF, możesz napisać własne reguły, aby rozszerzyć podstawowe reguły zestawu reguł (CRS). Reguły mogą blokować lub zezwalać na żądany ruch na podstawie kryteriów dopasowania.

Reguły niestandardowe umożliwiają tworzenie własnych reguł, które są oceniane dla każdego żądania, który przechodzi przez WAF. Reguły te mają wyższy priorytet niż pozostałe reguły w zestawach reguł zarządzanych. Reguły niestandardowe zawierają nazwę reguły, priorytet reguły i tablicę pasujących warunków. Jeśli te warunki są spełnione, podejmowana jest akcja (aby zezwolić lub zablokować).

Na przykład można zablokować wszystkie żądania z adresu IP w zakresie 192.168.5.4/24. W tej regule operatorem jest *IPMatch*, matchValues to zakres adresów IP (192.168.5.4/24), a akcja polega na zablokowaniu ruchu. Można również ustawić nazwę i priorytet reguły.

Reguły niestandardowe obsługują używanie logiki mieszania, aby bardziej zaawansowane reguły, które odpowiadają twoim potrzebom zabezpieczeń. Na przykład (Warunek 1 **i** Warunek 2) **lub** Warunek 3). Oznacza to, że jeśli warunek 1 **i** warunek 2 są spełnione **lub** jeśli warunek 3 jest spełniony, WAF należy podjąć akcję określoną w regułie niestandardowej.

Różne warunki dopasowania w ramach tej samej reguły są zawsze potęgowane przy użyciu **i**. Na przykład zablokuj ruch z określonego adresu IP i tylko wtedy, gdy korzysta z określonej przeglądarki.

Jeśli chcesz **lub** dwa różne warunki, te dwa warunki muszą być w różnych zasadach. Na przykład zablokuj ruch z określonego adresu IP lub zablokuj ruch, jeśli używa określonej przeglądarki.

> [!NOTE]
> Maksymalna liczba reguł niestandardowych WAF wynosi 100. Aby uzyskać więcej informacji na temat limitów bramy aplikacji, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Wyrażenia regularne są również obsługiwane w regułach niestandardowych, podobnie jak w zestawy reguł KSR. Aby zapoznać się z przykładami, zobacz Przykłady 3 i 5 w [1944 r. Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Zezwalanie na blokowanie a blokowanie

Zezwalanie i blokowanie ruchu jest proste w niestandardowych regułach. Na przykład można zablokować cały ruch pochodzący z zakresu adresów IP. Można wprowadzić inną regułę, aby zezwolić na ruch, jeśli żądanie pochodzi z określonej przeglądarki.

Aby na coś zezwolić, upewnij się, że `-Action` parametr jest ustawiony na **Zezwalaj**. Aby coś zablokować, `-Action` upewnij się, że parametr jest ustawiony na **Block**.

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

Poprzednia `$BlockRule` jest mapowana na następującą regułę niestandardową w usłudze Azure Resource Manager:

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

Ta reguła niestandardowa zawiera nazwę, priorytet, akcję i tablicę pasujących warunków, które muszą być spełnione, aby akcja została zrealizowana. Aby uzyskać więcej informacji na temat tych pól, zobacz następujące opisy pól. Na przykład reguły niestandardowe zobacz [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Pola reguł niestandardowych

### <a name="name-optional"></a>Nazwa [opcjonalnie]

Nazwa reguły.  Pojawia się w dziennikach.

### <a name="priority-required"></a>Priorytet [wymagany]

- Określa kolejność wyceny reguły. Im niższa wartość, tym wcześniejsza ocena reguły. Dopuszczalny zakres wynosi od 1 do 100. 
- Musi być unikatowa we wszystkich regułach niestandardowych. Reguła o priorytecie 40 jest oceniana przed regułą o priorytecie 80.

### <a name="rule-type-required"></a>Typ reguły [wymagane]

Obecnie musi być **MatchRule**.

### <a name="match-variable-required"></a>Dopasuj zmienną [wymagane]

Musi być jedną ze zmiennych:

- RemoteAddr – adres IP/nazwa hosta połączenia komputera zdalnego
- RequestMethod — metoda żądania HTTP (GET, POST, PUT, DELETE itd.)
- QueryString — zmienna w identyfikatorze URI
- PostArgs — argumenty wysyłane w treści POST. Reguły niestandardowe używające tej zmiennej dopasowania są stosowane tylko wtedy, gdy nagłówek "Typ zawartości" jest ustawiony na "application/x-www-form-urlencoded" i "multipart/form-data".
- RequestUri — identyfikator URI żądania
- RequestHeaders — nagłówki żądania
- RequestBody — zawiera całą treść żądania jako całość. Reguły niestandardowe używające tej zmiennej dopasowania są stosowane tylko wtedy, gdy nagłówek "Typ zawartości" jest ustawiony na "application/x-www-form-urlencoded". 
- RequestCookies – Pliki cookie wniosku

### <a name="selector-optional"></a>Selektor [opcjonalnie]

Opisuje pole matchVariable kolekcji. Na przykład jeśli matchVariable jest RequestHeaders, selektor może znajdować się w nagłówku *user-agent.*

### <a name="operator-required"></a>Operator [wymagane]

Musi być jednym z następujących operatorów:

- IPMatch - używany tylko wtedy, gdy zmienna dopasowania jest *RemoteAddr*
- Równe — dane wejściowe są takie same jak matchvalue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (wersja zapoznawcza)

### <a name="negate-condition-optional"></a>Warunek zanegowania [opcjonalnie]

Neguje bieżący warunek.

### <a name="transform-optional"></a>Przekształcanie [opcjonalnie]

Lista ciągów z nazwami przekształceń do wykonania przed próbą dopasowania. Mogą to być następujące przekształcenia:

- Małe litery
- Trim
- Kod Url
- Urlencode 
- Usuńulki
- HtmlEntityDecode (Kod htmlentitydecode)

### <a name="match-values-required"></a>Wartości dopasowania [wymagane]

Lista wartości do dopasowania, które można uznać za *OR*'ed. Na przykład mogą to być adresy IP lub inne ciągi. Format wartości zależy od poprzedniego operatora.

### <a name="action-required"></a>Działanie [wymagane]

- Zezwalaj — autoryzuje transakcję, pomijając wszystkie inne reguły. Określone żądanie jest dodawane do listy dozwolonych i po dopasowaniu żądanie zatrzymuje dalszą ocenę i jest wysyłane do puli wewnętrznej bazy danych. Reguły, które znajdują się na liście dozwolonych, nie są oceniane pod kątem dalszych reguł niestandardowych ani reguł zarządzanych.
- Blok — blokuje transakcję na podstawie *SecDefaultAction* (tryb wykrywania/zapobiegania). Podobnie jak zezwalaj akcji, gdy żądanie jest oceniane i dodawane do listy zablokowanych, ocena jest zatrzymana i żądanie jest blokowane. Każde żądanie po tym spełnia te same warunki nie zostaną ocenione i po prostu zostaną zablokowane. 
- Log — umożliwia zapis reguły do dziennika, ale pozwala pozostałe reguły uruchomić do oceny. Inne reguły niestandardowe są oceniane w kolejności priorytetu, a następnie reguły zarządzane.

## <a name="geomatch-custom-rules-preview"></a>Reguły niestandardowe geomatchu (wersja zapoznawcza)

Reguły niestandardowe umożliwiają tworzenie dostosowanych reguł do potrzeb aplikacji i zasad zabezpieczeń. Możesz ograniczyć dostęp do aplikacji internetowych według kraju/regionu. Aby uzyskać więcej informacji, zobacz [Geomatch reguły niestandardowe (wersja zapoznawcza)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z regułami niestandardowymi [utwórz własne reguły niestandardowe](create-custom-waf-rules.md).
