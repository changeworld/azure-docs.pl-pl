---
title: Rozwiązywanie problemów z zapory aplikacji sieci Web w usłudze Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla zapory aplikacji sieci Web (WAF) w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082445"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Rozwiązywanie problemów z zapory aplikacji sieci Web (WAF) w usłudze Azure Application Gateway

Istnieje kilka rzeczy, które można zrobić w przypadku zablokowania żądania, które należy przekazać za pośrednictwem swojej aplikacji sieci Web zapory (WAF).

Najpierw upewnij się, użytkownik przeczytał [Omówienie zapory aplikacji sieci Web](waf-overview.md) i [konfiguracji zapory aplikacji sieci Web](application-gateway-waf-configuration.md) dokumentów. Ponadto upewnij się, zostało włączone [Monitorowanie zapory aplikacji sieci Web](application-gateway-diagnostics.md) artykuły te wyjaśniają, jak zapory aplikacji internetowych funkcje, jak zestawy reguł zapory aplikacji sieci Web na pracy oraz sposób dostępu do dzienników zapory aplikacji sieci Web.

## <a name="understanding-waf-logs"></a>Dzienniki Omówienie zapory aplikacji sieci Web

Dzienniki zapory aplikacji sieci Web ma na celu Pokaż każde żądanie dopasowane lub blokowane przez zaporę aplikacji sieci Web. Jest rejestr wszystkich ocenianych żądań, które są dopasowywane lub zablokowane. Jeśli okaże się, że zapory aplikacji internetowych blokuje żądania, że nie należy (wynik fałszywie dodatni), możesz zrobić kilka rzeczy. Najpierw należy zawęzić i znaleźć określonego żądania. Przejrzyj dzienniki Aby znaleźć określony identyfikator URI, sygnatura czasowa lub transakcji żądania. Po znalezieniu wpisy dziennika skojarzonej, możesz rozpocząć zajmującym się wyników fałszywie dodatnich.

Załóżmy na przykład masz potrzebnego ruchu zawierający ciąg *1 = 1* , którą chcesz przekazać za pośrednictwem zapory aplikacji internetowych. Przy próbie żądania zapory aplikacji internetowych blokuje ruch, który zawiera Twoje *1 = 1* ciąg w polu lub parametr. Jest to ciąg, często skojarzony ataku polegającego na iniekcji SQL. Możesz przeglądać dzienniki i zobaczyć sygnatura czasowa żądania i reguł, które zablokowane dopasowane.

W poniższym przykładzie widać, że cztery reguły są wyzwalane podczas tego samego żądania (przy użyciu pól Identyfikator transakcji). Pierwsza z nich mówi był zgodny, ponieważ użytkownik użył liczbowych/adres IP adres URL żądania, które zwiększa wyniku anomalii przez trzy, ponieważ jest to ostrzeżenie. Następną regułę, który jest zgodny jest 942130, czyli ten, którego szukasz. Możesz zobaczyć *1 = 1* w `details.data` pola. To jeszcze bardziej podkreśla wyniku anomalii przez trzy ponownie, ponieważ pełni on również ostrzeżenie. Ogólnie rzecz biorąc, każdej reguły, która ma akcji **dopasowane** wyniku anomalii rośnie, i w tym momencie wyniku anomalii będzie sześć. Aby uzyskać więcej informacji, zobacz [tryb oceniania anomalii](waf-overview.md#anomaly-scoring-mode).

Żądanie zostało zablokowane, ponieważ wyniku anomalii jest wystarczająco wysoka, Pokaż końcowego wpisy dziennika dwa. Te wpisy mają różne akcje niż pozostałe dwa. Pokazują one faktycznie *zablokowane* żądania. Te reguły są obowiązkowe i nie może być wyłączony. One nie należy traktować jako zasady, ale innych korzyści w infrastrukturze podstawowej o elementach wewnętrznych zapory aplikacji sieci Web.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Naprawianie wyników fałszywie dodatnich

Za pomocą tych informacji i wiedzy, która reguła 942130 jest ten, który jest zgodny *1 = 1* ciąg, możesz zrobić kilka rzeczy, aby rozwiązać ten problem, na blokowanie ruchu:

- Użyj listy wykluczeń

   Zobacz [konfiguracji zapory aplikacji sieci Web](application-gateway-waf-configuration.md#waf-exclusion-lists) Aby uzyskać więcej informacji na temat wykluczania listy.
- Wyłącz regułę.

### <a name="using-an-exclusion-list"></a>Za pomocą listy wykluczeń

Podjąć świadomą decyzję o obsłudze fałszywy dodatnią, jest zapoznania się z technologiami, używanych przez aplikację. Na przykład załóżmy, że nie ma programu SQL server w stosie technologii, i otrzymujesz fałszywych alarmów związanych z tymi zasadami. Wyłączenie tych zasad nie zawsze osłabienie zabezpieczeń.

Jedną z zalet za pomocą listy wykluczeń jest określonej części żądania jest wyłączone. Jednak oznacza, że określone wyłączenia mające zastosowanie do całego ruchu przechodzącego przez zapory aplikacji internetowych, ponieważ jest to ustawienie globalne. Na przykład może to prowadzić do problemu, jeśli *1 = 1* jest prawidłowym żądaniem w treści niektórych aplikacji, ale nie dla innych użytkowników. Kolejną korzyścią jest to, że można wybrać treści, nagłówki i plików cookie do wykluczenia, jeśli określony warunek jest spełniony, a nie z wyłączeniem całego żądania.

Od czasu do czasu istnieją przypadki, w którym określone parametry zostały przekazane do zapory aplikacji sieci Web w taki sposób, który może nie być intuicyjne. Na przykład jest token, który przekazywane podczas uwierzytelniania przy użyciu usługi Azure Active Directory. Ten token *__RequestVerificationToken*, zwykle uzyskać przekazany jako plik Cookie żądania. Jednak w niektórych przypadkach, w których pliki cookie są wyłączone, token ten jest również przekazywany jako atrybutu żądania lub "arg". Jeśli tak się stanie, należy upewnić się, że *__RequestVerificationToken* zostanie dodany do listy wykluczeń jako **nazwy atrybutu żądania** także.

![Wykluczenia](media/waf-tshoot/exclusion-list.png)

W tym przykładzie, które chcesz wykluczyć **nazwy atrybutu żądania** , jest równa *text1*. Jest to oczywiste, ponieważ możesz zobaczyć nazwę atrybutu w dziennikach zapory: **danych: Matched Data: 1 = 1 można znaleźć w ARGS:text1: 1=1**. Ten atrybut jest **text1**. Możesz również znaleźć ta nazwa atrybutu kilka innych sposobów, zobacz [znajdowanie żądania nazwy atrybutów](#finding-request-attribute-names).

![Listy wykluczeń zapory aplikacji sieci Web](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Wyłączanie reguły

Innym sposobem obejściu dodatnią wartość false jest, można wyłączyć reguły, który jest zgodny na myśli danych wejściowych zapory aplikacji sieci Web był złośliwy. Ponieważ został przeanalizowany dzienniki zapory aplikacji sieci Web i mieć zawężony reguły do 942130, można ją wyłączyć w witrynie Azure portal. Zobacz [Dostosowywanie reguł zapory aplikacji sieci web za pośrednictwem witryny Azure portal](application-gateway-customize-waf-rules-portal.md).

Jedną z zalet wyłączanie reguły jest, że należy o tym, że cały ruch, który zawiera niektóre warunek, który zazwyczaj jest blokowana jest prawidłowy ruchem, można wyłączyć tej reguły dla całej aplikacji internetowych. Jednakże jeśli jest tylko prawidłowy ruch w przypadku użycia określonych, możesz otworzyć się luka w zabezpieczeniach przez wyłączenie tej reguły dla całej aplikacji internetowych, ponieważ jest to ustawienie globalne.

Jeśli chcesz użyć programu Azure PowerShell, zobacz [Dostosowywanie reguł zapory aplikacji sieci web za pomocą programu PowerShell](application-gateway-customize-waf-rules-powershell.md). Jeśli chcesz użyć wiersza polecenia platformy Azure, zobacz [Dostosowywanie reguł zapory aplikacji sieci web za pomocą wiersza polecenia platformy Azure](application-gateway-customize-waf-rules-cli.md).

![Reguły zapory aplikacji sieci Web](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Nazwy atrybutów żądania wyszukiwania

Za pomocą [Fiddler](https://www.telerik.com/fiddler), zbadaj poszczególnych żądań i określić, jakie określonych pól strony sieci web są wywoływane. Może to ułatwić, aby wykluczyć niektóre pola z inspekcji przy użyciu listy wykluczeń.

W tym przykładzie możesz zobaczyć, że pole gdzie *1 = 1* został wprowadzony ciąg jest nazywany **text1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

To pole, które można wykluczyć. Aby dowiedzieć się więcej na temat listy wykluczeń, zobacz [limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md#waf-exclusion-lists). W tym przypadku oceny można wykluczyć, konfigurując następujące wykluczenia:

![Wyłączenie zapory aplikacji sieci Web](media/waf-tshoot/waf-exclusion-02.png)

Można także sprawdzić dzienniki zapory, aby uzyskać informacje, aby zobaczyć, co jest potrzebne dodać do listy wykluczeń. Aby włączyć rejestrowanie, zobacz [kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).

Sprawdź dziennika zapory, aby wyświetlić plik PT1H.json za godzinę wystąpienia żądania, które chcesz sprawdzić.

W tym przykładzie widać, że masz cztery reguły za pomocą tego samego identyfikator transakcji i że wszystkie wystąpienia w dokładnie tym samym czasie:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Za pomocą swojej wiedzy na temat zestawów reguł CRS na pracy, i reguł CRS 3.0 współpracuje z anomalii oceniania systemu (zobacz [zapory aplikacji sieci Web w usłudze Azure Application Gateway](waf-overview.md)) wiadomo, że dolnej dwóch reguł z  **Akcja: Zablokowane** blokują właściwości na podstawie wyniku anomalii całkowitej. Reguły aby skoncentrować się na są dwa u góry.

Pierwszy wpis jest rejestrowane, ponieważ użytkownik użył adresu IP liczbowe można przejść do bramy aplikacji, która może być ignorowane w tym przypadku.

Drugi (reguła 942130) jest jednym interesujące. Widoczne szczegóły, aby był zgodny z wzorcem (1 = 1), a pole nosi **text1**. Postępuj zgodnie z tym samym poprzednie kroki, aby wykluczyć **nazwa atrybutu żądania** , **jest równa** **1 = 1**.

## <a name="finding-request-header-names"></a>Nazwy nagłówków żądania wyszukiwania

Jest przydatne narzędzie jeszcze raz, aby znaleźć nazwy nagłówków żądania. Na poniższym zrzucie ekranu widać nagłówki dla tego żądania GET, które obejmują *Content-Type*, *User-Agent*i tak dalej.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Innym sposobem wyświetlenia nagłówki żądania i odpowiedzi jest do wyszukiwania wewnątrz narzędzia deweloperskie programu Chrome. Możesz nacisnąć klawisz F12 lub kliknij prawym przyciskiem myszy -> **Sprawdź** -> **narzędzi deweloperskich**i wybierz **sieci** kartę. Ładowanie strony sieci web, a następnie kliknij przycisk żądania, które chcesz sprawdzić.

![F12 w przeglądarce Chrome](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Nazwy plików cookie żądania wyszukiwania

Jeśli żądanie zawiera pliki cookie, **plików cookie** można wybrać kartę, aby wyświetlić je w narzędziu Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Ograniczenia parametrów globalnych w celu wyeliminowania wyników fałszywie dodatnich

- Wyłącz kontrolę treść żądania

   Ustawiając **Sprawdź treść żądania** wyłączony, treści żądania całego ruchu nie będzie oceniany przez zapory aplikacji internetowych. Może to być przydatne, jeśli wiesz, że treści żądania nie są złośliwe do aplikacji.

   Po wyłączeniu tej opcji, sprawdzana jest nie tylko treści żądania. Nagłówki i pliki cookie pozostają kontrolowanego, chyba że poszczególne z nich są wyłączone, korzystając z funkcji listy wykluczeń.

- Limity rozmiaru plików

   Poprzez ograniczenie rozmiaru pliku dla zapory aplikacji internetowych, jest ograniczenie możliwości ataku zdarza się serwerów sieci web. Zezwolenie na dużych plików do przekazania, zwiększa ryzyko związane z wewnętrzną bazą danych przeciążeniu. Ograniczenie rozmiaru pliku do przypadku normalnego użytkowania aplikacji jest po prostu inny sposób, aby zapobiec atakom.

   > [!NOTE]
   > Jeśli wiesz, że aplikacja nigdy nie będzie żadnych przekazywanie pliku ponad podanej wielkości, można ograniczyć, ustawiając limit.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [jak skonfigurować zaporę aplikacji sieci web w usłudze Application Gateway](tutorial-restrict-web-traffic-powershell.md).
