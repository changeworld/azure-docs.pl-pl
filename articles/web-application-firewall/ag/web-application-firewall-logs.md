---
title: Monitorowanie dzienników zapory aplikacji sieci Web platformy Azure
description: Dowiedz się, jak włączać dzienniki i zarządzać nimi oraz zaporą aplikacji sieci Web platformy Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: cb1af86e04c0b4ba0b59398161fa111fd8065042
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310051"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Dzienniki diagnostyczne zapory aplikacji sieci Web platformy Azure

Zasoby Zapory aplikacji sieci Web można monitorować za pomocą dzienników. Można zapisać wydajność, dostęp i inne dane lub zużywać je z zasobu do celów monitorowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Można używać różnych typów dzienników na platformie Azure do zarządzania bramami aplikacji i rozwiązywania problemów z nimi. Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z magazynu obiektów blob platformy Azure i wyświetlać w różnych narzędziach, takich jak [dzienniki usługi Azure Monitor,](../../azure-monitor/insights/azure-networking-analytics.md)Excel i Power BI. Więcej informacji na temat różnych typów dzienników można dowiedzieć się z poniższej listy:

* **Dziennik aktywności:** Można użyć [dzienników aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md) (dawniej nazywanych dziennikami operacyjnymi i dziennikami inspekcji), aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure i ich stan. Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.
* **Dziennik dostępu:** Można użyć tego dziennika, aby wyświetlić wzorce dostępu bramy aplikacji i analizować ważne informacje. Obejmuje to adres IP wywołującego, żądany adres URL, opóźnienie odpowiedzi, kod zwrotny oraz bajty w i na zewnątrz. Dziennik dostępu jest zbierany co 300 sekund. Ten dziennik zawiera jeden rekord na wystąpienie bramy aplikacji. Wystąpienie bramy aplikacji jest identyfikowane przez właściwość instanceId.
* **Dziennik wydajności:** Można użyć tego dziennika, aby wyświetlić, jak działają wystąpienia bramy aplikacji. Ten dziennik przechwytuje informacje o wydajności dla każdego wystąpienia, w tym całkowitą liczbę obsługiwanych żądań, przepływność w bajtach, całkowitą liczbę obsługiwanych żądań, liczbę żądań nie powiodło się oraz w dobrej kondycji i złej liczbie wystąpień zaplecza. Dziennik wydajności jest zbierany co 60 sekund. Dziennik wydajności jest dostępny tylko dla jednostki SKU w wersji 1. Dla jednostki SKU w wersji 2 użyj [metryki](../../application-gateway/application-gateway-metrics.md) dla danych wydajności.
* **Dziennik zapory:** Można użyć tego dziennika do wyświetlania żądań, które są rejestrowane w trybie wykrywania lub zapobiegania bramy aplikacji skonfigurowanej z zaporą aplikacji sieci web.

> [!NOTE]
> Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania usługi Azure Resource Manager. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby uzyskać lepsze zrozumienie dwóch modeli, zobacz [opis wdrażania Menedżera zasobów i klasycznego wdrażania](../../azure-resource-manager/management/deployment-models.md) artykułu.

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: konta magazynu najlepiej sprawdzają się w przypadku dzienników przechowywanych przez dłuższy czas i przeglądanych w razie potrzeby.
* **Centra zdarzeń:** Centra zdarzeń są doskonałym rozwiązaniem do integracji z innymi narzędziami do zarządzania informacjami o zabezpieczeniach i zdarzeniami (SIEM), aby otrzymywać alerty dotyczące zasobów.
* **Dzienniki usługi Azure Monitor:** dzienniki usługi Azure Monitor są najlepiej używane do ogólnego monitorowania aplikacji w czasie rzeczywistym lub analizowania trendów.

### <a name="enable-logging-through-powershell"></a>Włączanie rejestrowania za pośrednictwem programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Należy włączyć rejestrowanie dostępu i wydajności, aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników. Aby włączyć rejestrowanie, należy wykonać następujące czynności:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać: /subscriptions/\<identyfikatorSubskrypcji\>/resourceGroups/\<nazwa grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<nazwa konta magazynu\>. Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal.

    ![Portal: identyfikator zasobu dla konta magazynu](../media/web-application-firewall-logs/diagnostics1.png)

2. Zanotuj identyfikator zasobu bramy aplikacji, dla którego rejestrowanie jest włączone. Ta wartość jest w formularzu:\</subscriptions/ subscriptionId\>\</resourceGroups/ nazwa\>grupy zasobów /providers/Microsoft.Network/applicationGateways/\<nazwa\>bramy aplikacji . Te informacje możesz znaleźć w portalu.

    ![Portal: identyfikator zasobu bramy aplikacji](../media/web-application-firewall-logs/diagnostics2.png)

3. Włącz rejestrowanie diagnostyczne przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Dzienniki aktywności nie wymagają osobnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

### <a name="enable-logging-through-the-azure-portal"></a>Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W witrynie Azure portal znajdź swój zasób i wybierz **pozycję Ustawienia diagnostyczne**.

   W przypadku bramy aplikacji dostępne są trzy dzienniki:

   * Dziennik dostępu
   * Dziennik wydajności
   * Dziennik zapory

2. Aby rozpocząć zbieranie danych, wybierz **pozycję Włącz diagnostykę**.

   ![Włączanie diagnostyki][1]

3. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych. W tym przykładzie usługa Log Analytics przechowuje dzienniki. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.

   ![Rozpoczynanie procesu konfiguracji][2]

5. Wpisz nazwę ustawień, potwierdź ustawienia i wybierz pozycję **Zapisz**.

### <a name="activity-log"></a>Dziennik aktywności

Platforma Azure domyślnie generuje dziennik aktywności. Dzienniki są zachowywane przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Dowiedz się więcej o tych dziennikach, czytając [artykuł Wyświetl zdarzenia i dziennik aktywności.](../../azure-resource-manager/management/view-activity-logs.md)

### <a name="access-log"></a>Dziennik dostępu

Dziennik dostępu jest generowany tylko wtedy, gdy włączono go w każdym wystąpieniu bramy aplikacji, jak opisano w poprzednich krokach. Dane są przechowywane na koncie magazynu określonym po włączeniu rejestrowania. Każdy dostęp bramy aplikacji jest rejestrowany w formacie JSON, jak pokazano w poniższym przykładzie dla wersji 1:

|Wartość  |Opis  |
|---------|---------|
|instanceId     | Wystąpienie bramy aplikacji, które obsługiwał żądanie.        |
|clientIP     | Źródłowy adres IP dla żądania.        |
|clientPort (port klienta)     | Pochodzący port dla żądania.       |
|HttpMethod     | Metoda HTTP używana przez żądanie.       |
|Requesturi     | Identyfikator URI odebranego żądania.        |
|Kwerenda żądań     | **Serwer-Routed:** Wystąpienie puli zaplecza, które zostało wysłane żądanie.</br>**X-AzureApplicationGateway-LOG-ID:** Identyfikator korelacji używany dla żądania. Może służyć do rozwiązywania problemów z ruchem na serwerach zaplecza. </br>**STATUS SERWERA:** Kod odpowiedzi HTTP odebrany przez bramę aplikacji z zaplecza.       |
|Useragent     | Agent użytkownika z nagłówka żądania HTTP.        |
|httpStatus (Stan)     | Kod stanu HTTP zwrócony do klienta z bramy aplikacji.       |
|wersja httpVersion     | Wersja HTTP żądania.        |
|odebrane Bajty     | Rozmiar odebranego pakietu w bajtach.        |
|wysłane Bajty| Rozmiar wysyłanego pakietu w bajtach.|
|czasTaken| Czas (w milisekundach), który zajmuje przetworzenie żądania i wysłanie jego odpowiedzi. Jest to obliczane jako interwał od momentu, gdy brama aplikacji odbiera pierwszy bajt żądania HTTP do czasu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że pole Czas—Zrobione zwykle zawiera czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć. |
|sslEnabled| Czy komunikacja z pulami zaplecza używała protokołu TLS/SSL. Prawidłowe wartości są włączone i wyłączone.|
|host| Nazwa hosta, za pomocą której żądanie zostało wysłane do serwera wewnętrznej bazy danych. Jeśli nazwa hosta wewnętrznej bazy danych jest zastępowane, ta nazwa będzie odzwierciedlać.|
|originalHost| Nazwa hosta, za pomocą której żądanie zostało odebrane przez bramę aplikacji od klienta.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
W przypadku bramy aplikacji i WAF v2 dzienniki wyświetlą nieco więcej informacji:

|Wartość  |Opis  |
|---------|---------|
|instanceId     | Wystąpienie bramy aplikacji, które obsługiwał żądanie.        |
|clientIP     | Źródłowy adres IP dla żądania.        |
|clientPort (port klienta)     | Pochodzący port dla żądania.       |
|HttpMethod     | Metoda HTTP używana przez żądanie.       |
|Requesturi     | Identyfikator URI odebranego żądania.        |
|Useragent     | Agent użytkownika z nagłówka żądania HTTP.        |
|httpStatus (Stan)     | Kod stanu HTTP zwrócony do klienta z bramy aplikacji.       |
|wersja httpVersion     | Wersja HTTP żądania.        |
|odebrane Bajty     | Rozmiar odebranego pakietu w bajtach.        |
|wysłane Bajty| Rozmiar wysyłanego pakietu w bajtach.|
|czasTaken| Czas (w milisekundach), który zajmuje przetworzenie żądania i wysłanie jego odpowiedzi. Jest to obliczane jako interwał od momentu, gdy brama aplikacji odbiera pierwszy bajt żądania HTTP do czasu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że pole Czas—Zrobione zwykle zawiera czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć. |
|sslEnabled| Czy komunikacja z pulami zaplecza używane TLS. Prawidłowe wartości są włączone i wyłączone.|
|sslCipher (sslCipher)| Pakiet szyfrowania używany do komunikacji TLS (jeśli TLS jest włączony).|
|sslProtocol| Używany protokół TLS (jeśli protokół TLS jest włączony).|
|serverRouted| Serwer wewnętrznej bazy danych, do których brama aplikacji kieruje żądanie.|
|status serwera| Kod stanu HTTP serwera wewnętrznej bazy danych.|
|serverResponseLatency| Opóźnienie odpowiedzi z serwera wewnętrznej bazy danych.|
|host| Adres wymieniony w nagłówku hosta żądania.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Dziennik wydajności

Dziennik wydajności jest generowany tylko wtedy, gdy włączono go w każdym wystąpieniu bramy aplikacji, jak opisano w poprzednich krokach. Dane są przechowywane na koncie magazynu określonym po włączeniu rejestrowania. Dane dziennika wydajności są generowane w odstępach 1-minutowych. Jest on dostępny tylko dla jednostki SKU w wersji 1. Dla jednostki SKU w wersji 2 użyj [metryki](../../application-gateway/application-gateway-metrics.md) dla danych wydajności. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     |  Wystąpienie bramy aplikacji, dla którego są generowane dane wydajności. W przypadku bramy aplikacji wielu wystąpień istnieje jeden wiersz na wystąpienie.        |
|healthyHostCount     | Liczba zdrowych hostów w puli zaplecza.        |
|UnHealthyHostCount (niezdrowe konto     | Liczba niezdrowych hostów w puli zaplecza.        |
|requestCount (Liczba żądań)     | Liczba doręczonych żądań.        |
|Opóźnienie | Średnie opóźnienie (w milisekundach) żądań z wystąpienia do zaplecza, który obsługuje żądania. |
|failedRequestCount| Liczba żądań, które nie powiodły się.|
|danych| Średnia przepływność od ostatniego dziennika, mierzona w bajtach na sekundę.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Opóźnienie jest obliczane od momentu odebraniem pierwszego bajtu żądania HTTP do momentu wysłania ostatniego bajtu odpowiedzi HTTP. Jest to suma czasu przetwarzania bramy aplikacji plus koszt sieci do zaplecza, a także czas, który zaplecza zajmuje do przetworzenia żądania.

### <a name="firewall-log"></a>Dziennik zapory

Dziennik zapory jest generowany tylko wtedy, gdy włączono go dla każdej bramy aplikacji, jak opisano w poprzednich krokach. Ten dziennik wymaga również skonfigurowania zapory aplikacji sieci web na bramie aplikacji. Dane są przechowywane na koncie magazynu określonym po włączeniu rejestrowania. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     | Wystąpienie bramy aplikacji, dla którego są generowane dane zapory. W przypadku bramy aplikacji wielu wystąpień istnieje jeden wiersz na wystąpienie.         |
|clientIp (pw.     |   Źródłowy adres IP dla żądania.      |
|clientPort (port klienta)     |  Pochodzący port dla żądania.       |
|Requesturi     | Adres URL odebranego żądania.       |
|Typ zestawu reguł     | Typ zestawu reguł. Dostępną wartością jest OWASP.        |
|ruleSetVersion     | Użyta wersja zestawu reguł. Dostępne wartości to 2.2.9 i 3.0.     |
|Ruleid     | Identyfikator reguły zdarzenia wyzwalającego.        |
|message     | Przyjazny dla użytkownika komunikat o zdarzeniu wyzwalającym. Więcej szczegółów znajduje się w sekcji szczegółów.        |
|action     |  Działania podjęte na wniosek. Dostępne wartości są zablokowane i dozwolone.      |
|lokacja     | Lokacja, dla której został wygenerowany dziennik. Obecnie na liście jest tylko Global, ponieważ reguły są globalne.|
|Szczegóły     | Szczegóły zdarzenia wyzwalającego.        |
|details.message     | Opis reguły.        |
|details.data     | Określone dane znalezione w żądaniu, które pasowały do reguły.         |
|details.file     | Plik konfiguracyjny zawierający regułę.        |
|details.line     | Numer wiersza w pliku konfiguracyjnym, który wyzwolił zdarzenie.       |
|Nazwa hosta   | Nazwa hosta lub adres IP bramy aplikacji.    |
|identyfikator transakcji  | Unikatowy identyfikator dla danej transakcji, który pomaga grupować wiele naruszeń reguł, które wystąpiły w ramach tego samego żądania.   |
|policyId   | Unikatowy identyfikator zasad zapory skojarzonych z bramą aplikacji, odbiornikiem lub ścieżką.   |
|policyScope    | Lokalizacja zasad - wartości mogą być "Globalne", "Odbiornik" lub "Lokalizacja".   |
|policyScopeName   | Nazwa obiektu, w którym stosowana jest zasada.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
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
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można wyświetlać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure**: pobierz informacje z dziennika aktywności przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub witryny Azure Portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w artykule [Activity operations with Resource Manager (Operacje działań przy użyciu usługi Resource Manager)](../../azure-resource-manager/management/view-activity-logs.md).
* **Usługa Power BI**: jeśli nie masz jeszcze konta usługi [Power BI](https://powerbi.microsoft.com/pricing), możesz ją wypróbować bezpłatnie. Korzystając z [aplikacji szablonów usługi Power BI,](https://docs.microsoft.com/power-bi/service-template-apps-overview)można analizować dane.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Wyświetlanie i analizowanie dzienników dostępu, wydajności i zapory

[Dzienniki usługi Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md) mogą zbierać pliki dziennika liczników i zdarzeń z konta magazynu obiektów Blob. Obejmuje ona wizualizacje oraz zaawansowane możliwości wyszukiwania na potrzeby analizowania dzienników.

Ponadto możesz połączyć się z kontem magazynu i pobrać wpisy dziennika JSON dotyczące dostępu i wydajności. Po pobraniu plików JSON możesz je przekonwertować do formatu CSV i wyświetlać w programie Excel, usłudze Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analizowanie dzienników programu Access za pośrednictwem programu GoAccess

Opublikowaliśmy szablon Menedżera zasobów, który instaluje i uruchamia popularny analizator dziennika [GoAccess](https://goaccess.io/) dla dzienników dostępu bramy aplikacji. GoAccess zapewnia cenne statystyki ruchu HTTP, takie jak unikatowe osoby odwiedzające, żądane pliki, hosty, systemy operacyjne, przeglądarki, kody stanu HTTP i inne. Aby uzyskać więcej informacji, zobacz [plik Readme w folderze szablonu Menedżera zasobów w usłudze GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki liczników i zdarzeń przy użyciu [dzienników usługi Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj swój dziennik aktywności platformy Azure za pomocą](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) wpisu w blogu usługi Power BI.
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i innych wpisach](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) w blogu.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
