---
title: Niestandardowe reguły zapory aplikacji sieci Web (WAF) v2 na Application Gateway
description: Ten artykuł zawiera omówienie reguł niestandardowych zapory aplikacji sieci Web (WAF) v2 na platformie Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 0ac37378797c59d79af5d026200b68154836c5ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585392"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Reguły niestandardowe dla zapory aplikacji sieci Web V2 na platformie Azure Application Gateway

Usługa Azure Application Gateway Web Application Firewall (WAF) v2 zawiera wstępnie skonfigurowane, zarządzane przez platform zestaw reguł, który oferuje ochronę wielu różnych typów ataków. Ataki te obejmują wykonywanie skryptów między lokacjami, iniekcja kodu SQL i inne. Jeśli jesteś administratorem WAF, możesz chcieć napisać własne reguły, aby rozszerzyć reguły podstawowego zestawu reguł (KSR). Reguły mogą blokować lub zezwalać na żądany ruch na podstawie kryteriów dopasowywania.

Reguły niestandardowe umożliwiają tworzenie własnych reguł, które są oceniane dla każdego żądania, które przechodzi przez WAF. Reguły te mają wyższy priorytet niż pozostałe reguły w zarządzanych zestawach reguł. Reguły niestandardowe zawierają nazwę reguły, priorytet reguły i tablicę pasujących warunków. Jeśli te warunki są spełnione, podejmowana jest akcja (do zezwalania lub blokowania).

Na przykład można zablokować wszystkie żądania z adresu IP w zakresie 192.168.5.4/24. W tej regule operator to *IPMatch*, matchValues jest zakresem adresów IP (192.168.5.4/24), a akcja polega na zablokowaniu ruchu. Należy również ustawić nazwę i priorytet reguły.

Reguły niestandardowe obsługują używanie logiki złożonej do bardziej zaawansowanych reguł, które zaspokajają potrzeby związane z bezpieczeństwem. Na przykład (warunek 1 **i** warunek 2) **lub** warunek 3).  Ten przykład oznacza, że jeśli spełniony jest warunek 1 **i** warunek 2 **lub** spełniony jest warunek 3, WAF powinien wykonać akcję określoną w regule niestandardowej.

Różne warunki dopasowywania w ramach tej samej reguły są zawsze składane przy użyciu **i**. Na przykład Zablokuj ruch z określonego adresu IP i tylko wtedy, gdy korzystają z określonej przeglądarki.

Jeśli chcesz **lub** dwa różne warunki, te dwa warunki muszą być w różnych regułach. Na przykład Zablokuj ruch z określonego adresu IP lub Zablokuj ruch, jeśli korzysta z określonej przeglądarki.

> [!NOTE]
> Maksymalna liczba reguł niestandardowych WAF to 100. Aby uzyskać więcej informacji na temat limitów Application Gateway, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-subscription-service-limits.md#application-gateway-limits).

Wyrażenia regularne są również obsługiwane w regułach niestandardowych, podobnie jak w zestawach reguł KSR. Aby zapoznać się z przykładami, zobacz przykłady 3 i 5 w temacie [Tworzenie i używanie niestandardowych reguł zapory aplikacji sieci Web](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Zezwalanie na a blokowanie

Zezwalanie i blokowanie ruchu jest proste z regułami niestandardowymi. Na przykład można zablokować cały ruch pochodzący z zakresu adresów IP. Można utworzyć kolejną regułę zezwalającą na ruch, jeśli żądanie pochodzi z określonej przeglądarki.

Aby zezwolić na coś, upewnij się, że parametr `-Action` jest ustawiony na **Zezwalaj**. Aby zablokować coś, upewnij się, że parametr `-Action` jest ustawiony na wartość **Block**.

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

Poprzednie `$BlockRule` są mapowane do następującej reguły niestandardowej w Azure Resource Manager:

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

- Określa kolejność wyceny. Im niższa wartość, tym wcześniejsza Ocena reguły. Dozwolony zakres to od 1-100. 
- Musi być unikatowa dla wszystkich reguł niestandardowych. Reguła z priorytetem 40 jest szacowana przed regułą z priorytetem 80.

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
- wyrażeń
- Geodopasowanie (wersja zapoznawcza)

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

## <a name="geomatch-custom-rules-preview"></a>Reguły niestandardowe geodopasowania (wersja zapoznawcza)

Reguły niestandardowe umożliwiają tworzenie dostosowanych reguł zgodnie z dokładnymi potrzebami aplikacji i zasadami zabezpieczeń. Teraz można ograniczyć dostęp do aplikacji sieci Web według kraju/regionu, który jest dostępny w publicznej wersji zapoznawczej. Podobnie jak w przypadku wszystkich reguł niestandardowych, ta logika może zostać złożona z innymi regułami, aby odpowiadały potrzebom aplikacji. 

   > [!NOTE]
   > Reguły niestandardowe geodopasowania są dostępne w regionach Południowo-środkowe stany USA i Europa Północna. Aby uzyskać dostęp do nich w portalu, użyj [tego linku](https://aka.ms/AppGWWAFGeoMatch) do momentu, gdy będzie on aktywny dla wszystkich użytkowników. 

Jeśli używasz operatora geodopasowania, selektory mogą być jednym z następujących kodów krajów dwucyfrowych. 

|Kod kraju | Nazwa kraju |
| ----- | ----- |
| AD | Andora |
| AE | Zjednoczone Emiraty Arabskie|
| AF | Afganistan|
| AG | Antigua i Barbuda|
| AL | Albania|
| AM | Armenia|
| AO | Angola|
| AR | Argentyna|
| AS | Samoa Amerykańskie|
| AT | Austria|
| AU | Australia|
| AZ | Azerbejdżan|
| BA | Bośnia i Hercegowina|
| BB | Barbados|
| BD | Bangladesz|
| BE | Belgia|
| BF | Burkina Faso|
| BG | Bułgaria|
| BH | Bahrajn|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Darussalam|
| BO | Boliwia|
| BR | Brazylia|
| BS | Bahamy|
| BT | Bhutan|
| BW | Botswana|
| BY | Białoruś|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratyczna Republika Konga|
| CF | Republika Środkowoafrykańska|
| CH | Szwajcaria|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Chiny|
| CO | Kolumbia|
| CR | Kostaryka|
| CU | Kuba|
| CV | Wyspy Zielonego Przylądka|
| CY | Cypr|
| CZ | Czechy|
| DE | Niemcy|
| DK | Dania|
| DO | Republika Dominikańska|
| DZ | Algieria|
| EC | Ekwador|
| EE | Estonia|
| EG | Egipt|
| ES | Hiszpania|
| ET | Etiopia|
| FI | Finlandia|
| FJ | Fidżi|
| FM | Federalne Stany Mikronezji|
| PW | Francja|
| GB | Wielka Brytania|
| GE | Gruzja|
| GF | Gujana Francuska|
| GH | Ghana|
| GN | Gwinea|
| GP | Gwadelupa|
| GR | Grecja|
| GT | Gwatemala|
| GY | Gujana|
| HK | Hongkong SAR|
| HN | Honduras|
| HR | Chorwacja|
| HT | Haiti|
| HU | Węgry|
| ID | Indonezja|
| IE | Irlandia|
| IL | Izrael|
| IN | Indie|
| IQ | Irak|
| IR | Islamska Republika Iranu|
| IS | Islandia|
| IT | Włochy|
| JM | Jamajka|
| JO | Jordania|
| JP | Japonia|
| KE | Kenia|
| KG | Kirgistan|
| KH | Kambodża|
| KI | Kiribati|
| KN | Saint Kitts i Nevis|
| KP | Koreańska Republika Ludowo-Demokratyczna|
| KR | Korea Południowa|
| KW | Kuwejt|
| KY | Kajmany|
| KZ | Kazachstan|
| LA | Laotańska Republika Ludowo-Demokratyczna|
| LB | Liban|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litwa|
| LU | Luksemburg|
| LV | Łotwa|
| LY | Libia |
| MA | Maroko|
| MD | Republika Mołdawii|
| MG | Madagaskar|
| MK | Północna Macedonia|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolia|
| MO | Makau SAR|
| MQ | Martynika|
| MR | Mauretania|
| MT | Malta|
| MV | Malediwy|
| MW | Malawi|
| MX | Meksyk|
| MY | Malezja|
| MZ | Mozambik|
| Nie dotyczy | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nikaragua|
| NL | Holandia|
| NO | Norwegia|
| NP | Nepal|
| NR | Nauru|
| NZ | Nowa Zelandia|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filipiny|
| PK | Pakistan|
| PL | Polska|
| PR | Portoryko|
| PT | Portugalia|
| PW | Palau|
| PY | Paragwaj|
| QA | Katar|
| RE | Reunion|
| RO | Rumunia|
| RS | Serbia|
| RU | Federacja Rosyjska|
| RW | Rwanda|
| SA | Arabia Saudyjska|
| SD | Sudan|
| SE | Szwecja|
| SG | Singapur|
| SI | Słowenia|
| SK | Słowacja|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sudan Południowy|
| SV | Salwador|
| SY | Arabska Republika Syryjska|
| SZ | Suazi|
| TC | Wyspy Turks i Caicos|
| TG | Togo|
| TH | Tajlandia|
| TN | Tunezja|
| TR | Turcja|
| TT | Trynidad i Tobago|
| TW | Tajwan|
| TZ | Zjednoczona Republika Tanzanii|
| UA | Ukraina|
| UG | Uganda|
| USA | Stany Zjednoczone|
| UY | Urugwaj|
| UZ | Uzbekistan|
| VC | Saint Vincent i Grenadyny|
| VE | Wenezuela|
| VG | Brytyjskie Wyspy Dziewicze|
| VI | Wyspy Dziewicze Stanów Zjednoczonych|
| VN | Wietnam|
| ZA | Republika Południowej Afryki|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Następne kroki

Po poznaniu reguł niestandardowych [Utwórz własne reguły niestandardowe](create-custom-waf-rules.md).
