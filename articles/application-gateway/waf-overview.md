---
title: Wprowadzenie do zapory aplikacji sieci web dla usługi Azure Application Gateway
description: Ten artykuł zawiera omówienie zapory aplikacji sieci web (WAF) w usłudze Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518188"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Zapora aplikacji sieci Web dla usługi Azure Application Gateway

Usługa Azure Application Gateway oferuje zapory aplikacji sieci web (WAF), która zapewnia scentralizowaną ochronę aplikacji sieci web przed typowymi programami wykorzystującymi luki i lukami w zabezpieczeniach. Aplikacje internetowe coraz częściej są objęci złośliwych ataków wykorzystujących powszechnie znane luki w zabezpieczeniach. Wstrzykiwanie kodu SQL i skrypty międzywitrynowe znajdują się wśród najbardziej typowymi atakami.

Zapobieganie atakom na takie w kodzie aplikacji staje się wyzwaniem. Może ona wymagać rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji. Zapora aplikacji sieci web scentralizowane ułatwia zarządzanie zabezpieczeniami znacznie prostsze. Zapora aplikacji sieci Web oferuje również administratorów aplikacji zapewnienia lepszej ochrony przed zagrożeniami i intruzami.

Rozwiązanie zapory aplikacji sieci Web może reagować na zagrożenia bezpieczeństwa szybciej przez centralnie poprawki znanych luk w zabezpieczeniach, zamiast zabezpieczanie każdej aplikacji sieci web indywidualnych. Istniejące bramy application Gateway łatwo można przekonwertować na bram aplikacji wykorzystujących ścian ognia.

Brama aplikacji zapory aplikacji internetowych opiera się na [zestawu reguł Core (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) wersji 3.0 lub 2.2.9 z Otwórz sieci Web aplikacji Security Project (OWASP). Zapora aplikacji sieci Web automatycznie aktualizuje obejmujący ochronę przed nowymi zagrożeniami dzięki nie wykonywania dodatkowych czynności konfiguracyjnych.

![Diagram zapory aplikacji Internetowej bramy aplikacji](./media/waf-overview/WAF1.png)

Usługa Application Gateway działa jak kontroler dostarczania aplikacji (ADC). Oferuje ona, kończenie żądań protokołu Secure Sockets Layer (SSL), koligacja sesji na podstawie plików cookie, dystrybucji obciążenia z działaniem okrężnym, routingu opartego na zawartości, możliwość hostowania wielu witryn sieci Web i usprawnienia zabezpieczeń.

Rozszerzenia zabezpieczeń bramy aplikacji obejmują zarządzanie zasadami protokołu SSL i obsługi protokołu SSL end-to-end. Zabezpieczenia aplikacji jest silniejsze dzięki integracji zapory aplikacji sieci Web bramy aplikacji. Kombinacja chroni aplikacje sieci web dla powszechnych luk w zabezpieczeniach. I zapewnia łatwe konfigurowanie centralną lokalizację do zarządzania.

## <a name="benefits"></a>Korzyści

W tej sekcji opisano podstawowych korzyści, które zapewnia usługa Application Gateway i zapory aplikacji internetowych.

### <a name="protection"></a>Ochrona

* Ochrona aplikacji sieci web z sieci web luk w zabezpieczeniach oraz atakami niewymagającymi modyfikacji kodu zaplecza.

* Ochrona wielu aplikacji sieci web, w tym samym czasie. Wystąpienie usługi Application Gateway może obsługiwać maksymalnie 20 witryn sieci Web, które są chronione przez zaporę aplikacji sieci web.

### <a name="monitoring"></a>Monitorowanie

* Monitorowanie ataków na aplikacje sieci web przy użyciu dziennika zapory aplikacji sieci Web w czasie rzeczywistym. Dziennik jest zintegrowany z [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) do śledzenia alertów zapory aplikacji sieci Web oraz łatwego monitorowania trendów.

* Brama aplikacji zapory aplikacji internetowych jest zintegrowana z usługą Azure Security Center. Usługa Security Center zapewnia pełny widok stanu bezpieczeństwa wszystkich Twoich zasobów platformy Azure.

### <a name="customization"></a>Dostosowywanie

* Można dostosować reguły zapory aplikacji sieci Web i grup reguł, aby odpowiadał wymaganiom Twojej aplikacji i eliminowanie wyników fałszywie dodatnich.

## <a name="features"></a>Funkcje

- Ochrona wstrzyknięcie kodu SQL.
- Ochrona skryptów między witrynami.
- Ochrona przed innych typowych ataków w sieci web, na przykład polegające na iniekcji poleceń, przemycaniu odpowiedzi HTTP, rozdzielaniu żądań HTTP i dołączenie pliku zdalnego.
- Ochrona przed naruszeniami protokołu HTTP.
- Ochrona przed anomalii protokołu HTTP, takich jak brakujące hosta agenta użytkownika ani nagłówków accept.
- Ochrona przed robotów, przeszukiwarek i skanerów.
- Wykrywanie typowych błędów konfiguracji aplikacji (na przykład, Apache i IIS).
- Ogranicza rozmiar żądania można skonfigurować za pomocą dolną i górną granicę.
- Listy wykluczeń pozwalają na pominięcie niektórych atrybutów żądania oceny zapory aplikacji sieci Web. Typowym przykładem jest tokenów z wstawione w usłudze Active Directory, które są używane do uwierzytelniania lub pola hasła.

### <a name="core-rule-sets"></a>Podstawowe zestawy reguł

Usługa Application Gateway obsługuje dwa zestawy reguł: CRS 3.0 i CRS 2.2.9. Te zasady chronią aplikacje sieci web ze złośliwych działań.

Brama aplikacji zapory aplikacji internetowych ma wstępnie skonfigurowany CRS 3.0 domyślnie. Ale możesz zamiast tego użyj CRS 2.2.9. Zestaw CRS 3.0 oferuje zmniejszyć liczbę fałszywych alarmów w porównaniu z CRS 2.2.9. Możesz również [Dostosowywanie reguł w zależności od potrzeb](application-gateway-customize-waf-rules-portal.md).

Zapora aplikacji sieci Web chroni przed następujących luk w zabezpieczeniach sieci web:

- Ataki polegające na iniekcji SQL
- Ataki z użyciem skryptów między witrynami
- Inne typowe ataków, na przykład polegające na iniekcji poleceń, HTTP żądania przemycie, dzielenie odpowiedzi HTTP i dołączenie pliku zdalnego
- Naruszeniami protokołu HTTP
- Anomalie protokołu HTTP, takich jak brakujące hosta agenta użytkownika ani nagłówków accept
- Robotów, przeszukiwarek i skanerów
- Typowych błędów konfiguracji aplikacji (na przykład, Apache i IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 zawiera 13 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które mogą zostać wyłączone.

|Grupa reguł|Opis|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Blokowanie metod (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Ochrona przed skanerami portów i środowiska|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Ochrona przed protokołami i kodowaniem problemów|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Ochrona przed nagłówków, przemycaniem żądań i rozdzielaniem odpowiedzi|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Ochrona przed atakami pliku i ścieżki|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Ochronę przed atakami dołączania (RFI) pliku zdalnego|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Ochrony przed atakami wykonywania kodu zdalnego|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Ochrona przed atakami polegającymi na iniekcji PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Ochrona przed atakami skryptów między witrynami|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Ochrona przed atakami polegającymi na iniekcji SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Ochronę przed atakami utrwalanie sesji|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 zawiera 10 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które mogą zostać wyłączone.

|Grupa reguł|Opis|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Chroń przed naruszeniami protokołu (np. nieprawidłowe znaki lub GET z treścią żądania)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Ochrona przed nieprawidłowymi informacjami nagłówka|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Ochrona przed argumentami lub plikami przekraczającymi ograniczenia|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Ochrona przed ograniczonymi metodami, nagłówkami i typów plików|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Ochrona przed web przeszukiwarkami i skanerami|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Ochrona przed atakami ogólnymi (na przykład utrwalanie sesji, dołączenie pliku zdalnego i iniekcją PHP)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Ochrona przed atakami polegającymi na iniekcji SQL|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Ochrona przed atakami skryptów między witrynami|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Ochrona przed atakami polegającymi ścieżkę na|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Ochrona przed Tylne wejście do systemu to konie trojańskie|

### <a name="waf-modes"></a>Tryby zapory aplikacji sieci Web

Brama aplikacji zapory aplikacji internetowych można skonfigurować do uruchamiania w następujących dwóch trybach:

* **Tryb wykrywania**: Monitoruje i rejestruje wszystkie alerty zagrożeń. Włącz rejestrowanie danych diagnostycznych usługi Application Gateway w **diagnostyki** sekcji. Ponadto należy się upewnić, że dziennika zapory aplikacji sieci Web jest zaznaczona i włączona. Zapora aplikacji sieci Web nie blokuje żądań przychodzących, działającego w trybie wykrywania.
* **Tryb zapobiegania**: Blokuje próby włamań i ataków, które wykrywają reguły. Osoba atakująca odbiera wyjątek "403 nieautoryzowanego dostępu", a następnie połączenie zostanie przerwane. Tryb zapobiegania rejestruje takich ataków w dziennikach zapory aplikacji sieci Web.

### <a name="anomaly-scoring-mode"></a>Tryb Punktacja anomalii
 
OWASP ma dwa tryby decydujące o tym, czy blokować ruch: Tradycyjne trybu ani oceniania anomalii.

W trybie tradycyjnych ruchu, który pasuje do dowolnej reguły uznaje się niezależnie od innych dopasowań reguły. Ten tryb jest łatwa do zrozumienia. Ale brak informacji o jak wiele zasad pasuje do określonego żądania jest to ograniczenie. Dlatego oceniania anomalii trybu została wprowadzona. Jest ustawieniem domyślnym dla OWASP 3. *x*.

W trybie oceniania anomalii ruchu, który pasuje do dowolnej reguły nie jest od razu blokowane Zapora jest w tym trybie. Reguły mają pewne ważności: *Krytyczne*, *błąd*, *ostrzeżenie*, lub *powiadomienie*. Czy ważność wpływa na wartość liczbową dla żądania, które jest wywoływane wyniku anomalii. Na przykład jeden *ostrzeżenie* reguły dopasowania przyczynia się 3 na wynik. Jeden *krytyczny* reguły dopasowania wspiera 5.

Jest równy 5 dla wyniku anomalii do blokuje ruch. Tak, jeden *krytyczny* reguły dopasowania jest wystarczająca dla aplikacji bramy zapory aplikacji sieci Web do blokowania żądania, nawet w tym trybie. Oprócz jednego *ostrzeżenie* reguły dopasowania jedynie zwiększa anomalii wynik przez 3, który nie jest wystarczająco dużo, aby blokowała ruch.

> [!NOTE]
> Komunikat, który jest rejestrowane, gdy ruch jest zgodna z reguł zapory aplikacji sieci Web zawiera wartość "Blocked". Jednak ruch jest faktycznie tylko zablokowanych dla wyniku anomalii 5 lub nowszej.  

### <a name="waf-monitoring"></a>Monitorowanie zapory aplikacji sieci Web

Monitorowanie kondycji bramy Application Gateway jest ważne. Monitorowanie kondycji zapory aplikacji internetowych i aplikacji, które chroni jest obsługiwana przez integrację z usługą Azure Security Center, usługi Azure Monitor i dzienniki usługi Azure Monitor.

![Diagram przedstawiający diagnostykę brama aplikacji zapory aplikacji internetowych](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Dzienniki bramy aplikacji są zintegrowane z usługą [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). Dzięki temu można śledzić informacje diagnostyczne, w tym zapory aplikacji sieci Web, alerty i dzienniki. Dostęp do tej funkcji na **diagnostyki** karcie zasobu usługi Application Gateway w portalu lub bezpośrednio za pomocą usługi Azure Monitor. Aby dowiedzieć się więcej na temat włączania dzienników, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Usługa Security Center](../security-center/security-center-intro.md) pomaga zapobiec, wykrywania i reagowania na zagrożenia. Zapewnia lepszy wgląd w i kontroli nad ich zabezpieczeniami zasobów platformy Azure. Usługa Application Gateway jest [zintegrowane z usługą Security Center](application-gateway-integration-security-center.md). Usługa Security Center skanuje środowisko w celu wykrycia niechronionych aplikacji internetowych. Jego zaleca, aby brama aplikacji zapory aplikacji internetowych do ochrony tych narażonych na ataki zasobów. Zapory możesz utworzyć bezpośrednio w usłudze Security Center. Te wystąpienia zapory aplikacji sieci Web są zintegrowane z usługą Security Center. One wysyłać alerty i informacje o kondycji do usługi Security Center dla raportowania.

![Usługa Security Center — Przegląd okna](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Rejestrowanie

Brama aplikacji zapory aplikacji internetowych udostępnia szczegółowe raporty na każde zagrożenie wykryte. Rejestrowanie jest zintegrowane z dziennikami diagnostycznymi platformy Azure. Alerty są zapisywane w formacie JSON. Te dzienniki można zintegrować z [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![Dzienniki diagnostyczne bramy aplikacji systemu windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Cena jednostki SKU zapory aplikacji sieci Web w usłudze Application Gateway

Brama aplikacji zapory aplikacji internetowych jest dostępna w ramach nowej jednostki SKU. Ta jednostka SKU jest dostępna tylko w modelu aprowizacji usługi Azure Resource Manager, a nie w klasycznym modelu wdrażania. Ponadto jednostki SKU zapory aplikacji sieci Web jest dostępna tylko w średnich i dużych rozmiarów wystąpień bramy Application Gateway. Wszystkie limity dotyczące usługi Application Gateway dotyczą również jednostki SKU zapory aplikacji sieci Web.

Cennik zależy od godzinowa opłata za wystąpienie bramy oraz opłata za przetwarzanie danych. [Ceny usługi Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) dla jednostki SKU zapory aplikacji sieci Web różni się od opłat za jednostkę SKU standardowa. Opłaty za przetwarzanie danych są takie same. Nie będą naliczane opłaty za regułę lub grupę reguł. Możesz chronić wiele aplikacji sieci web za tą samą zaporą aplikacji sieci web. Brak opłat do obsługi wielu aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [jak skonfigurować zaporę aplikacji sieci web w usłudze Application Gateway](tutorial-restrict-web-traffic-powershell.md).
