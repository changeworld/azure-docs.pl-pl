---
title: Usługa Azure niestandardowych reguł zapory aplikacji sieci Web (WAF)
description: Ten artykuł zawiera omówienie zapory aplikacji sieci web (WAF) reguły niestandardowe w usłudze Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: d35e1dc96f65e51ab14c4962d1824334cbdb1616
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752032"
---
# <a name="custom-rules-for-web-application-firewall"></a>Niestandardowych reguł zapory aplikacji sieci Web

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) jest powiązana z wstępnie skonfigurowane, zarządzane przez platformę reguł, który zapewnia ochronę z wielu różnych rodzajów ataków. Te ataki to site scripting, wstrzykiwanie kodu SQL i innych. Jeśli jesteś administratorem zapory aplikacji sieci Web, warto napisania własne reguły, aby rozszerzyć reguły core ustawiać reguły (CRS). Reguł można zablokować lub zezwolić na żądany ruchu w oparciu o kryteria dopasowywania.

Reguły niestandardowe umożliwiają tworzenie własnych reguł, które są obliczane dla każdego żądania, które przechodzą przez zaporę aplikacji sieci Web. Te reguły trzymaj wyższy priorytet niż pozostałe reguły zestawów reguł zarządzanego. Niestandardowe reguły zawierają nazwę reguły, priorytet reguły i szereg warunków dopasowania. Jeśli te warunki są spełnione, działania są podejmowane (w celu zezwalania lub blokowania).

Na przykład można zablokować wszystkich żądań z adresu IP w 192.168.5.4/24 zakresu. W tej regule, operator jest *IPMatch*, matchValues jest zakresem adresów IP (192.168.5.4/24), a Akcja odnosi się do blokowania ruchu. Można również ustawić nazwę reguły i priorytet.

Niestandardowe reguły obsługi przy użyciu logiki łączenia się z bardziej zaawansowanych reguł ten adres, który w zakresie zabezpieczeń. Adapterem (1 warunek **i** warunek 2) **lub** warunek 3).  W tym przykładzie oznacza, że jeśli warunek 1 **i** warunek 2 są spełnione, **lub** jeśli spełniony jest warunek 3, zapory aplikacji sieci Web powinno zająć akcja określona w regule niestandardowej.

Różne warunki pasujących w ramach tej samej reguły są zawsze rozliczana przy użyciu **i**. Na przykład, blokować ruch z określonego adresu IP, i tylko wtedy, gdy korzystająca z niektórych przeglądarki.

Jeśli chcesz **lub** dwóch różnych warunków, dwa warunki musi znajdować się w różnych zasad. Na przykład należy zablokować ruch z określonego adresu lub blokowanie ruchu IP, w przypadku korzystania z przeglądarki.

> [!NOTE]
> Maksymalna liczba niestandardowych reguł zapory aplikacji sieci Web to 100. Aby uzyskać więcej informacji na temat limitów bramy Application Gateway, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md#application-gateway-limits).

Wyrażenia regularne są również obsługiwane w reguły niestandardowe, podobnie jak w zestawy reguł CRS. Przykłady te można znaleźć przykłady 3 i 5 w [tworzenia i używania reguł zapory aplikacji sieci web niestandardowego](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Zezwalanie i blokowanie

Zezwalanie i blokowanie ruchu jest prostym, zawierającym niestandardowe reguły. Na przykład możesz zablokować cały ruch pochodzące z zakresu adresów IP. Możesz wprowadzać inną regułę, aby zezwolić na ruch, jeśli żądanie pochodzi z określonego przeglądarki.

Aby zezwolić na coś, upewnij się, że `-Action` parametr ma wartość **Zezwalaj**. Aby zablokować coś, upewnij się, że `-Action` parametr ma wartość **bloku**.

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

Poprzedni `$BlockRule` mapuje następujące reguły niestandardowej w usłudze Azure Resource Manager:

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

Tej reguły niestandardowej zawiera nazwę, priorytetu, akcję i tablicy zgodnych warunki, które muszą zostać spełnione dla akcji została wykonana. Aby uzyskać dokładniejsze objaśnienie tych pól zobacz poniższe opisy pól. Na przykład niestandardowe reguły, zobacz [tworzenia i używania reguł zapory aplikacji sieci web niestandardowego](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Pola niestandardowe reguły

### <a name="name-optional"></a>[Opcjonalnie] Nazwa

Jest to nazwa reguły. Nazwa ta pojawia się w dziennikach.

### <a name="priority-required"></a>Priorytet [wymagane]

- Określa kolejność, w jakiej są przetwarzane reguły. Niższa wartość, wcześniejszej oceny reguły.
-Musi być unikatowa wśród wszystkich reguł niestandardowych. Reguła z priorytetem 100 jest oceniana przed regułą z priorytetem 200.

### <a name="rule-type-required"></a>Typ reguły [wymagane]

Obecnie musi być **MatchRule**.

### <a name="match-variable-required"></a>Zmienna dopasowania [wymagane]

Musi to być jedna ze zmiennych:

- RemoteAddr — IP adres/nazwa hosta połączenie z komputerem zdalnym
- RequestMethod — metoda żądania HTTP (GET, POST, PUT, DELETE itd.)
- Ciąg zapytania — zmienna w identyfikatorze URI
- PostArgs — argumenty wysyłane w treści wpisu
- RequestUri — identyfikator URI żądania
- RequestHeaders — nagłówki żądania
- RequestBody — treść żądania
- RequestCookies — pliki cookie żądania

### <a name="selector-optional"></a>Selektor [opcjonalnie]

W tym artykule opisano pola kolekcji matchVariable. Na przykład, jeżeli matchVariable jest RequestHeaders, selektor może to być na *User-Agent* nagłówka.

### <a name="operator-required"></a>[Wymagana] — operator

Musi mieć jedną z następujących operatorów:

- IPMatch - używane tylko w przypadku dopasowania zmiennej jest *RemoteAddr*
- Equals — w danych wejściowych jest taka sama jak MatchValue
- zawiera
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Zaczyna się od
- endsWith
- Regex

### <a name="negate-condition-optional"></a>Negate — warunku [opcjonalnie]

Neguje bieżącego stanu.

### <a name="transform-optional"></a>Przekształcenie [opcjonalnie]

Lista ciągów z nazwami przekształcenia zrobić przed dopasowaniem zostanie podjęta. Mogą to być następujące przekształcenia:

- Małe litery
- TRIM
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Pasuje do wartości [wymagane]

Lista wartości do dopasowywania, który można traktować jako *lub*"ed. Na przykład może być adresy IP lub innych ciągów. Format wartości zależy od poprzedniego operatora.

### <a name="action-required"></a>Akcja [wymagane]

- Zezwalaj — zezwala transakcji, pomijanie wszystkie kolejne reguły. Oznacza to, że określonego żądania został dodany do listy dozwolonych, a po dopasowane dalszej oceny zatrzymuje żądania i są wysyłane do puli zaplecza. Reguły, które znajdują się na liście dozwolonych nie są oceniane pod kątem wszelkich dalszych niestandardowe reguły lub reguły zarządzanych.
- Blokuj — blokuje transakcji na podstawie *SecDefaultAction* (Tryb wykrywania/zapobiegania). Podobnie jak Akcja zezwalania gdy żądanie jest obliczane i dodany do listy zablokowanych, oceny zostaje zatrzymana, a żądanie jest zablokowany. Każde żądanie po spełniające te same warunki nie zostaną ocenione i po prostu zostanie zablokowane. 
- Log — umożliwia reguły zapisu w dzienniku, ale umożliwia rest zasad uruchamiania oceny. Kolejne reguły niestandardowe są obliczane w kolejności priorytetów, następuje zarządzanych reguły.

## <a name="next-steps"></a>Kolejne kroki

Po informacje o niestandardowych reguł [utworzyć własne reguły niestandardowe](create-custom-waf-rules.md).
