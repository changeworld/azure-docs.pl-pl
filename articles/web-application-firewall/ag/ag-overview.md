---
title: Wprowadzenie do Zapory aplikacji sieci Web platformy Azure
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web (WAF) w bramie aplikacji
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 8d43851a6a546bd23881c4d0a72185ca03e63092
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983623"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Zapora aplikacji sieci Web platformy Azure w bramie aplikacji platformy Azure

Zapora aplikacji sieci Web azure (WAF) w usłudze Azure Application Gateway zapewnia scentralizowaną ochronę aplikacji sieci web przed typowymi exploitami i lukami w zabezpieczeniach. Aplikacje sieci Web są coraz częściej celem złośliwych ataków, które wykorzystują powszechnie znane luki w zabezpieczeniach. Iniekcji SQL i skryptów między witrynami są jednymi z najczęstszych ataków.

WAF na bramie aplikacji opiera się na [core rule set (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1, 3.0 lub 2.2.9 z projektu open web application security project (OWASP). WAF automatycznie aktualizuje się, aby uwzględnić ochronę przed nowymi lukami w zabezpieczeniach, bez konieczności dodatkowej konfiguracji. 

Wszystkie funkcje WAF wymienione poniżej istnieją wewnątrz zasad WAF. Można utworzyć wiele zasad i mogą być skojarzone z bramą aplikacji, do poszczególnych odbiorników lub reguł routingu opartych na ścieżce w bramie aplikacji. W ten sposób można mieć oddzielne zasady dla każdej lokacji za bramą aplikacji, jeśli to konieczne. Aby uzyskać więcej informacji na temat zasad WAF, zobacz [Tworzenie zasad WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > Zasady WAF dla witryn i identyfikatorów URI są w publicznej wersji zapoznawczej. Oznacza to, że ta funkcja podlega dodatkowym Warunkom użytkowania firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Diagram WAF bramy aplikacji](../media/ag-overview/waf1.png)

Brama aplikacji działa jako kontroler dostarczania aplikacji (ADC). Oferuje zakończenie ssl (Secure Sockets Layer), koligacja sesji opartą na plikach cookie, dystrybucję obciążenia okrężną, routing oparty na zawartości, możliwość hostowania wielu witryn sieci Web i ulepszenia zabezpieczeń.

Ulepszenia zabezpieczeń bramy aplikacji obejmują zarządzanie zasadami SSL i kompleksową obsługę SSL. Bezpieczeństwo aplikacji jest wzmacniane przez integrację WAF z bramą aplikacji. Połączenie chroni aplikacje internetowe przed typowymi lukami w zabezpieczeniach. Zapewnia łatwą do skonfigurowania centralną lokalizację do zarządzania.

## <a name="benefits"></a>Zalety

W tej sekcji opisano podstawowe korzyści, które zapewnia w u portalu aplikacji w udziale sieci WAF.

### <a name="protection"></a>Ochrona

* Chroń swoje aplikacje internetowe przed lukami w zabezpieczeniach sieci Web i atakami bez modyfikacji kodu zaplecza.

* Chroń wiele aplikacji internetowych w tym samym czasie. Wystąpienie bramy aplikacji może obsługiwać do 40 witryn sieci Web chronionych przez zaporę aplikacji sieci web.

* Tworzenie niestandardowych zasad WAF dla różnych witryn za tym samym WAF 

* Chroń swoje aplikacje internetowe przed złośliwymi botami za pomocą ip reputation ruleset (wersja zapoznawcza)

### <a name="monitoring"></a>Monitorowanie

* Monitoruj ataki na aplikacje internetowe za pomocą dziennika WAF w czasie rzeczywistym. Dziennik jest zintegrowany z [usługą Azure Monitor,](../../azure-monitor/overview.md) aby śledzić alerty WAF i łatwo monitorować trendy.

* Brama aplikacji WAF jest zintegrowana z usługą Azure Security Center. Usługa Security Center zapewnia centralny widok stanu zabezpieczeń wszystkich zasobów platformy Azure.

### <a name="customization"></a>Dostosowywanie

* Dostosuj reguły WAF i grupy reguł, aby dostosować je do wymagań aplikacji i wyeliminować fałszywe alarmy.

* Skojarz zasady WAF dla każdej witryny za WAF, aby umożliwić konfigurację specyficzną dla lokacji

* Tworzenie reguł niestandardowych dostosowanych do potrzeb aplikacji

## <a name="features"></a>Funkcje

- Ochrona wtrysku SQL.
- Ochrona skryptów między witrynami.
- Ochrona przed innymi typowymi atakami internetowymi, takimi jak iniekcja poleceń, przemycanie żądań HTTP, dzielenie odpowiedzi HTTP i zdalne dołączanie plików.
- Ochrona przed naruszeniami protokołu HTTP.
- Ochrona przed anomaliami protokołu HTTP, takimi jak brakujący agent użytkownika-agenta hosta i nagłówki akceptowania.
- Ochrona przed robotami i skanerami.
- Wykrywanie typowych błędnych konfiguracji aplikacji (na przykład Apache i IIS).
- Konfigurowalne limity rozmiaru żądania z dolną i górną granicą.
- Listy wykluczeń umożliwiają pominięcie niektórych atrybutów żądania z oceny WAF. Typowym przykładem są tokeny wstawione w usłudze Active Directory, które są używane do uwierzytelniania lub pól haseł.
- Tworzenie reguł niestandardowych w zależności od konkretnych potrzeb aplikacji.
- Ruch z filtrem geograficznym umożliwia lub blokuje dostęp do aplikacji w niektórych krajach. (wersja zapoznawcza)
- Chroń swoje aplikacje przed botami za pomocą bot mitigation ruleset. (wersja zapoznawcza)

## <a name="waf-policy"></a>Polityka WAF

Aby włączyć Zaporę aplikacji sieci Web w bramie aplikacji, należy utworzyć zasady WAF. Ta zasada dotyczy wszystkich reguł zarządzanych, reguł niestandardowych, wykluczeń i innych dostosowań, takich jak limit przekazywania plików. 

### <a name="core-rule-sets"></a>Podstawowe zestawy reguł

Brama aplikacji obsługuje trzy zestawy reguł: CRS 3.1, CRS 3.0 i CRS 2.2.9. Te reguły chronią aplikacje internetowe przed złośliwą aktywnością.

Aby uzyskać więcej informacji, zobacz [Grupy reguł i reguły zapory aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Reguły niestandardowe

Brama aplikacji obsługuje również reguły niestandardowe. Za pomocą reguł niestandardowych można utworzyć własne reguły, które są oceniane dla każdego żądania, które przechodzi przez WAF. Reguły te mają wyższy priorytet niż pozostałe reguły w zestawach reguł zarządzanych. Jeśli zestaw warunków jest spełniony, podejmowana jest akcja zezwalania lub blokowania. 

Operator geomatchu jest teraz dostępny w publicznej wersji zapoznawczej reguł niestandardowych. Więcej informacji można znaleźć w [niestandardowych regułach geomatch.](custom-waf-rules-overview.md#geomatch-custom-rules-preview)

> [!NOTE]
> Operator geomatch dla reguł niestandardowych jest obecnie w publicznej wersji zapoznawczej i jest wyposażony w umowę dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aby uzyskać więcej informacji na temat reguł niestandardowych, zobacz [Reguły niestandardowe dla bramy aplikacji.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot Mitigation (wersja zapoznawcza)

Zarządzany zestaw reguł ochrony bota można włączyć dla waf do blokowania lub rejestrowania żądań ze znanych złośliwych adresów IP, obok zestawu reguł zarządzanych. Adresy IP pochodzą z źródła danych firmy Microsoft Analizy zagrożeń. Inteligentny wykres zabezpieczeń zasila analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Usługę Azure Security Center.

> [!NOTE]
> Zestaw reguł ochrony bota jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli ochrona botów jest włączona, przychodzące żądania pasujące do adresów IP klienta złośliwego bota są rejestrowane w dzienniku zapory, zobacz więcej informacji poniżej. Możesz uzyskać dostęp do dzienników WAF z konta magazynu, centrum zdarzeń lub analizy dzienników. 

### <a name="waf-modes"></a>Tryby WAF

WAF bramy aplikacji można skonfigurować tak, aby działała w następujących dwóch trybach:

* **Tryb wykrywania:** monitoruje i rejestruje wszystkie alerty o zagrożeniach. Diagnostyka rejestrowania bramy aplikacji jest wcienana w sekcji **Diagnostyka.** Należy również upewnić się, że dziennik WAF jest zaznaczony i włączony. Zapora aplikacji sieci Web nie blokuje przychodzących żądań, gdy działa w trybie wykrywania.
* **Tryb zapobiegania:** Blokuje włamania i ataki, które wykrywają reguły. Osoba atakująca otrzymuje wyjątek "403 nieautoryzowany dostęp", a połączenie zostanie zamknięte. Tryb zapobiegania rejestruje takie ataki w dziennikach WAF.

> [!NOTE]
> Zaleca się uruchomienie nowo wdrożonego narzędzia WAF w trybie wykrywania przez krótki okres czasu w środowisku produkcyjnym. Zapewnia to możliwość uzyskania [dzienników zapory](../../application-gateway/application-gateway-diagnostics.md#firewall-log) i aktualizacji wszelkich wyjątków lub [reguł niestandardowych](./custom-waf-rules-overview.md) przed przejściem do trybu zapobiegania. Może to pomóc zmniejszyć występowanie nieoczekiwanego zablokowanego ruchu.

### <a name="anomaly-scoring-mode"></a>Tryb oceniania anomalii

OWASP ma dwa tryby podejmowania decyzji, czy zablokować ruch: tryb tradycyjny i tryb oceniania anomalii.

W trybie tradycyjnym ruch zgodny z dowolną regułą jest uważany za niezależny od innych dopasowań reguły. Ten tryb jest łatwy do zrozumienia. Ale brak informacji o tym, ile reguł odpowiada określonej żądaniu, jest ograniczeniem. Tak, Tryb punktacji anomalii został wprowadzony. Jest to wartość domyślna dla OWASP 3. *x*.

W trybie oceniania anomalii ruch zgodny z dowolną regułą nie jest natychmiast blokowany, gdy zapora jest w trybie zapobiegania. Reguły mają pewną ważność: *Krytyczny,* *Błąd*, *Ostrzeżenie*lub *Zawiadomienie*. Ta ważność wpływa na wartość liczbową żądania, która jest nazywana Wynik anomalii. Na przykład jeden *mecz reguły ostrzeżenie* przyczynia się 3 do wyniku. Jeden mecz reguł *krytycznych* przyczynia się 5.

|Ważność  |Wartość  |
|---------|---------|
|Krytyczny     |5|
|Błąd        |4|
|Ostrzeżenie      |3|
|Zauważyć       |2|

Istnieje próg 5 dla Wynik anomalii, aby zablokować ruch. Tak więc pojedynczy *krytyczny* dopasowania reguły wystarczy dla bramy aplikacji WAF, aby zablokować żądanie, nawet w trybie zapobiegania. Ale jedna *reguła ostrzeżenie* pasuje tylko zwiększa wynik anomalii o 3, co samo w sobie nie wystarcza do zablokowania ruchu.

> [!NOTE]
> Komunikat, który jest rejestrowany, gdy reguła WAF dopasowuje ruch zawiera wartość akcji "Zablokowane". Ale ruch jest w rzeczywistości tylko zablokowany dla Wynik Anomaly 5 lub wyższy.  

### <a name="waf-monitoring"></a>Monitorowanie WAF

Monitorowanie kondycji bramy Application Gateway jest ważne. Monitorowanie kondycji sieci WAF i aplikacji, które chroni, są obsługiwane przez integrację z dziennikami Usługi Azure Security Center, Azure Monitor i Azure Monitor.

![Diagram diagnostyki WAF bramy aplikacji](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Dzienniki bramy aplikacji są zintegrowane z [usługą Azure Monitor](../../azure-monitor/overview.md). Dzięki temu można śledzić informacje diagnostyczne, w tym alerty WAF i dzienniki. Dostęp do tej funkcji można uzyskać na karcie **Diagnostyka** w zasobie bramy aplikacji w portalu lub bezpośrednio za pośrednictwem usługi Azure Monitor. Aby dowiedzieć się więcej o włączaniu dzienników, zobacz [Diagnostyka bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Usługa Security Center](../../security-center/security-center-intro.md) pomaga zapobiegać zagrożeniom, wykrywać ich i reagować na nie. Zapewnia lepszy wgląd i kontrolę nad bezpieczeństwem zasobów platformy Azure. Brama aplikacji jest [zintegrowana z usługą Security Center](../../application-gateway/application-gateway-integration-security-center.md). Usługa Security Center skanuje środowisko w celu wykrycia niechronionych aplikacji internetowych. Można zalecić WAF bramy aplikacji, aby chronić te zasoby podlegające uspośnień. Zapory są tworzone bezpośrednio z centrum zabezpieczeń. Te wystąpienia WAF są zintegrowane z centrum zabezpieczeń. Wysyłają alerty i informacje o kondycji do Centrum zabezpieczeń w celu raportowania.

![Okno omówienie centrum zabezpieczeń](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Usługa Azure Sentinel

Microsoft Azure Sentinel to skalowalne, natywne dla chmury rozwiązanie do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM) i automatycznej odpowiedzi aranżacji zabezpieczeń (SOAR). Usługa Azure Sentinel zapewnia inteligentną analizę zabezpieczeń i analizę zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, proaktywnego polowania i reagowania na zagrożenia.

Dzięki wbudowanemu skoroszycie zdarzeń zapory usługi Azure WAF możesz uzyskać przegląd zdarzeń zabezpieczeń w ramach usługi WAF. Obejmuje to zdarzenia, dopasowane i zablokowane reguły i wszystko inne, co jest rejestrowane w dziennikach zapory. Zobacz więcej na temat logowania poniżej. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Rejestrowanie

Usługa WAF bramy aplikacji zapewnia szczegółowe raportowanie każdego wykrytego zagrożenia. Rejestrowanie jest zintegrowane z dziennikami diagnostyki platformy Azure. Alerty są rejestrowane w formacie .json. Te dzienniki można zintegrować z [dziennikami usługi Azure Monitor.](../../azure-monitor/insights/azure-networking-analytics.md)

![Diagnostyka bramy aplikacji dzienniki systemu Windows](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Cena jednostki SKU zapory aplikacji sieci Web w usłudze Application Gateway

Modele cenowe są różne dla jednostek SKU WAF_v1 i WAF_v2. Więcej informacji można znaleźć na stronie [cennik bramy aplikacji.](https://azure.microsoft.com/pricing/details/application-gateway/) 

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do [tworzenia zasad WAF](create-waf-policy-ag.md)
- Dowiedz się więcej o [regułach zarządzanych WAF](application-gateway-crs-rulegroups-rules.md)
- Dowiedz się więcej o [regułach niestandardowych](custom-waf-rules-overview.md)
- Dowiedz się więcej o [Zaporze aplikacji sieci Web w drzwiach frontowych platformy Azure](../afds/afds-overview.md)

