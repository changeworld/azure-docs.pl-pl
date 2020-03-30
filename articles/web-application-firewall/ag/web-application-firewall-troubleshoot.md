---
title: Rozwiązywanie problemów — Zapora aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z zaporą aplikacji sieci Web (WAF) dla bramy aplikacji platformy Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046190"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Rozwiązywanie problemów z zaporą aplikacji sieci Web (WAF) dla bramy aplikacji platformy Azure

Istnieje kilka czynności, które można zrobić, jeśli żądania, które powinny przechodzić przez zaporę aplikacji sieci Web (WAF) są blokowane.

Najpierw upewnij się, że przeczytałeś [przegląd WAF](ag-overview.md) i dokumenty [konfiguracji WAF.](application-gateway-waf-configuration.md) Ponadto upewnij się, że włączono [monitorowanie WAF](../../application-gateway/application-gateway-diagnostics.md) Te artykuły wyjaśniają, jak działa WAF, jak działają reguły WAF i jak uzyskać dostęp do dzienników WAF.

## <a name="understanding-waf-logs"></a>Opis dzienników WAF

Celem dzienników WAF jest pokazanie każdego żądania, które jest dopasowane lub zablokowane przez WAF. Jest to księga wszystkich ocenianych żądań, które są dopasowane lub zablokowane. Jeśli zauważysz, że WAF blokuje żądanie, które nie powinno (fałszywie dodatnie), możesz wykonać kilka czynności. Najpierw zawęź i znajdź konkretne żądanie. Przejrzyj dzienniki, aby znaleźć określony identyfikator URI, sygnatury czasowej lub identyfikator transakcji żądania. Po znalezieniu skojarzonych wpisów dziennika, można rozpocząć działanie na fałszywych alarmów.

Na przykład załóżmy, że masz legalny ruch zawierający ciąg *1=1,* który chcesz przekazać za pośrednictwem waf. Jeśli spróbujesz żądania, WAF blokuje ruch, który zawiera *ciąg 1 =1* w dowolnym parametrze lub polu. Jest to ciąg często skojarzony z atakiem iniekcji SQL. Można przeglądać dzienniki i zobaczyć sygnaturę czasowa żądania i reguły, które zablokowane /dopasowane.

W poniższym przykładzie widać, że cztery reguły są wyzwalane podczas tego samego żądania (przy użyciu transactionid pola). Pierwszy mówi, że dopasowane, ponieważ użytkownik użył numeryczne/IP URL dla żądania, co zwiększa wynik anomalii o trzy, ponieważ jest to ostrzeżenie. Następna reguła, która pasuje do 942130, czyli tej, której szukasz. Możesz zobaczyć *1 =1* `details.data` w polu. To dodatkowo zwiększa wynik anomalii o trzy ponownie, ponieważ jest to również ostrzeżenie. Ogólnie rzecz biorąc, każda reguła, która ma akcję **Dopasowana** zwiększa wynik anomalii i w tym momencie wynik anomalii będzie sześć. Aby uzyskać więcej informacji, zobacz [Tryb oceniania anomalii](ag-overview.md#anomaly-scoring-mode).

Ostatnie dwa wpisy dziennika pokazują, że żądanie zostało zablokowane, ponieważ wynik anomalii był wystarczająco wysoki. Te wpisy mają inną akcję niż pozostałe dwa. Pokazują, że faktycznie *zablokowali* wniosek. Zasady te są obowiązkowe i nie można ich wyłączyć. Nie powinny być traktowane jako zasady, ale bardziej jako podstawowa infrastruktura wewnętrznych WAF.

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

## <a name="fixing-false-positives"></a>Ustalanie fałszywych alarmów

Dzięki tym informacjom i wiedzy, że reguła 942130 jest tym, który pasuje do ciągu *1 = 1,* możesz zrobić kilka rzeczy, aby zatrzymać to od blokowania ruchu:

- Korzystanie z listy wykluczeń

   Zobacz [konfigurację WAF, aby](application-gateway-waf-configuration.md#waf-exclusion-lists) uzyskać więcej informacji na temat list wykluczeń.
- Wyłącz regułę.

### <a name="using-an-exclusion-list"></a>Korzystanie z listy wykluczeń

Aby podjąć świadomą decyzję o obsłudze fałszywie dodatnich, należy zapoznać się z technologiami używanymi przez aplikację. Załóżmy na przykład, że w stosie technologii nie ma serwera SQL, a otrzymasz fałszywe alarmy związane z tymi regułami. Wyłączenie tych reguł niekoniecznie osłabia twoje bezpieczeństwo.

Jedną z zalet korzystania z listy wykluczeń jest to, że tylko określona część żądania jest wyłączona. Oznacza to jednak, że określone wykluczenie ma zastosowanie do całego ruchu przechodzącego przez WAF, ponieważ jest to ustawienie globalne. Na przykład może to prowadzić do problemu, jeśli *1 = 1* jest prawidłowym żądaniem w treści dla określonej aplikacji, ale nie dla innych. Inną korzyścią jest to, że można wybrać między treści, nagłówki i pliki cookie, które mają być wykluczone, jeśli określony warunek jest spełniony, w przeciwieństwie do wykluczania całego żądania.

Czasami zdążeń, w których określone parametry są przekazywane do WAF w sposób, który może nie być intuicyjny. Na przykład istnieje token, który jest przekazywany podczas uwierzytelniania przy użyciu usługi Azure Active Directory. Ten token, *__RequestVerificationToken*, zwykle są przekazywane jako plik cookie żądania. Jednak w niektórych przypadkach, gdy pliki cookie są wyłączone, ten token jest również przekazywany jako atrybut żądania lub "arg". W takim przypadku należy upewnić się, że *__RequestVerificationToken* jest dodawany do listy wykluczeń jako **nazwa atrybutu Request.**

![Wykluczenia](../media/web-application-firewall-troubleshoot/exclusion-list.png)

W tym przykładzie chcesz wykluczyć **nazwę atrybutu Żądanie,** która jest równa *text1*. Jest to widoczne, ponieważ w dziennikach zapory widać nazwę atrybutu: **dane: Dopasowane dane: 1=1 znalezione w args:text1: 1=1**. Atrybutem jest **text1**. Tę nazwę atrybutu można również znaleźć na kilka innych sposobów, zobacz [Znajdowanie nazw atrybutów żądania](#finding-request-attribute-names).

![Listy wykluczeń WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Wyłączanie reguł

Innym sposobem, aby ominąć fałszywie dodatnie jest wyłączenie reguły, która dopasowana na dane wejściowe WAF myśli był złośliwy. Ponieważ przeanalizowałeś dzienniki WAF i zawęziłeś regułę do 942130, można ją wyłączyć w witrynie Azure portal. Zobacz [Dostosowywanie reguł zapory aplikacji sieci Web za pośrednictwem portalu Azure](application-gateway-customize-waf-rules-portal.md).

Jedną z zalet wyłączenia reguły jest to, że jeśli znasz cały ruch, który zawiera określony warunek, który będzie normalnie zablokowany jest prawidłowy ruch, można wyłączyć tę regułę dla całego WAF. Jeśli jednak jest to tylko prawidłowy ruch w określonym przypadku użycia, można otworzyć lukę, wyłączając tę regułę dla całej WAF, ponieważ jest to ustawienie globalne.

Jeśli chcesz używać programu Azure PowerShell, zobacz [Dostosowywanie reguł zapory aplikacji sieci Web za pośrednictwem programu PowerShell](application-gateway-customize-waf-rules-powershell.md). Jeśli chcesz używać interfejsu wiersza polecenia platformy Azure, zobacz [Dostosowywanie reguł zapory aplikacji sieci Web za pośrednictwem interfejsu wiersza polecenia platformy Azure](application-gateway-customize-waf-rules-cli.md).

![Zasady WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Znajdowanie nazw atrybutów żądania

Za pomocą [Fiddler](https://www.telerik.com/fiddler)sprawdzasz poszczególne żądania i określasz, jakie konkretne pola strony internetowej są nazywane. Może to pomóc w wykluczeniu niektórych pól z inspekcji przy użyciu list wykluczeń.

W tym przykładzie widać, że pole, w którym wprowadzono ciąg *1=1,* nosi nazwę **text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Jest to pole, które można wykluczyć. Aby dowiedzieć się więcej o listach wykluczeń, zobacz [Limity rozmiaru zapory żądań aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md#waf-exclusion-lists). W takim przypadku można wykluczyć ocenę, konfigurując następujące wykluczenie:

![Wykluczenie WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Można również sprawdzić dzienniki zapory, aby uzyskać informacje, aby zobaczyć, co należy dodać do listy wykluczeń. Aby włączyć rejestrowanie, zobacz [Kondycja zaplecza, dzienniki diagnostyczne i metryki bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md).

Sprawdź dziennik zapory i wyświetl plik PT1H.json dla godziny, którą ma zostać wyświetlona prośba, którą chcesz sprawdzić.

W tym przykładzie widać, że masz cztery reguły z tym samym TransactionID i że wszystkie one wystąpiły dokładnie w tym samym czasie:

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

Dzięki twojej wiedzy na temat sposobu działania reguły CRS i tego, że zestaw reguł CRS 3.0 współpracuje z systemem oceniania anomalii (zobacz [Zapora aplikacji sieci Web dla bramy aplikacji platformy Azure)](ag-overview.md)wiesz, że dwie najniższe reguły z **akcją: Właściwość zablokowana** są blokowane na podstawie całkowitego wyniku anomalii. Zasady, na których należy się skupić, to dwie najlepsze zasady.

Pierwszy wpis jest rejestrowany, ponieważ użytkownik użył numerycznego adresu IP, aby przejść do bramy aplikacji, co w tym przypadku można zignorować.

Drugi (zasada 942130) jest interesujący. W szczegółach widać, że pasuje do wzorca (1=1), a pole nosi nazwę **text1**. Wykonaj te same poprzednie kroki, aby wykluczyć **nazwę atrybutu żądania,** która **jest równa** **1 =1**.

## <a name="finding-request-header-names"></a>Znajdowanie nazw nagłówków żądań

Skrzypek jest przydatnym narzędziem po raz kolejny, aby znaleźć nazwy nagłówka żądania. Na poniższym zrzucie ekranu można zobaczyć nagłówki dla tego żądania GET, które obejmują *typ zawartości,* *agent użytkownika*i tak dalej.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Innym sposobem wyświetlania nagłówków żądań i odpowiedzi jest zajrzenie do narzędzi programistycznych Chrome. Możesz nacisnąć klawisz F12 lub kliknąć prawym przyciskiem myszy -> **Sprawdź** -> **narzędzia deweloperskie,** a następnie wybrać kartę **Sieć.** Załaduj stronę sieci web i kliknij żądanie, które chcesz sprawdzić.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Znajdowanie nazw plików cookie żądań

Jeśli żądanie zawiera pliki cookie, można wybrać kartę **Pliki cookie,** aby wyświetlić je w Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Ograniczanie parametrów globalnych w celu wyeliminowania fałszywych alarmów

- Wyłącz inspekcję jednostki żądań

   Ustawiając **inspect treści żądania** wyłączyć, organy żądania całego ruchu nie będą oceniane przez WAF. Może to być przydatne, jeśli wiesz, że jednostki żądania nie są złośliwe dla aplikacji.

   Wyłączając tę opcję, tylko treść żądania nie jest kontrolowana. Nagłówki i pliki cookie pozostają kontrolowane, chyba że poszczególne z nich są wykluczone za pomocą funkcji listy wykluczeń.

- Limity rozmiaru pliku

   Ograniczając rozmiar pliku dla WAF, ograniczasz możliwość ataku na serwery sieci Web. Zezwalając na przekazywanie dużych plików, zwiększa się ryzyko przytłoczenia wewnętrznej bazy danych. Ograniczenie rozmiaru pliku do normalnego przypadku użycia aplikacji jest tylko inny sposób, aby zapobiec atakom.

   > [!NOTE]
   > Jeśli wiesz, że aplikacja nigdy nie będzie potrzebować żadnego przekazywania plików powyżej danego rozmiaru, możesz to ograniczyć, ustawiając limit.

## <a name="firewall-metrics-waf_v1-only"></a>Metryki zapory (tylko WAF_v1)

W przypadku zapór aplikacji sieci Web w wersji 1 w portalu są teraz dostępne następujące metryki: 

1. Liczba zablokowanych żądań zapory aplikacji sieci Web Liczba żądań Liczba żądań, które zostały zablokowane
2. Zapora aplikacji sieci Web Zablokowana liczba reguł Wszystkie reguły, które zostały dopasowane **i** żądanie zostało zablokowane
3. Całkowita dystrybucja reguł zapory aplikacji sieci Web Wszystkie reguły, które zostały dopasowane podczas oceny
     
Aby włączyć metryki, wybierz kartę **Metryki** w portalu i wybierz jedną z trzech metryk.

## <a name="next-steps"></a>Następne kroki

Zobacz [Jak skonfigurować zaporę aplikacji sieci Web w bramie aplikacji](tutorial-restrict-web-traffic-powershell.md).
