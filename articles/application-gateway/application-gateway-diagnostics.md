---
title: Monitorowanie dzienników dostępu, dzienników wydajności, kondycji zaplecza i metryk dla Application Gateway platformy Azure
description: Dowiedz się, jak włączyć dzienniki dostępu i Dzienniki wydajności dla systemu Azure Application Gateway i zarządzać nimi
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: victorh
ms.openlocfilehash: 3acae8f7d34bb02905e6e8d479b7de5ccab1bb7a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850982"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Kondycja zaplecza, dzienniki diagnostyczne i metryki dla Application Gateway

Za pomocą usługi Azure Application Gateway można monitorować zasoby w następujący sposób:

* [Kondycja zaplecza](#back-end-health): Application Gateway zapewnia możliwość monitorowania kondycji serwerów w pulach zaplecza za pomocą Azure Portal i za pomocą programu PowerShell. Kondycję pul zaplecza można również znaleźć za pomocą dzienników diagnostycznych wydajności.

* [Dzienniki](#diagnostic-logging): Dzienniki umożliwiają zapisywanie lub zużywanie danych z zasobów na potrzeby monitorowania, a także korzystanie z nich.

* [Metryki](#metrics): Application Gateway obecnie ma siedem metryk do wyświetlania liczników wydajności.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Kondycja zaplecza

Application Gateway zapewnia możliwość monitorowania kondycji poszczególnych członków pul zaplecza za pomocą portalu, programu PowerShell i interfejsu wiersza polecenia (CLI). Zagregowane podsumowanie kondycji pul zaplecza można również znaleźć za pomocą dzienników diagnostycznych wydajności. 

Raport kondycji zaplecza odzwierciedla dane wyjściowe sondy kondycji Application Gateway do wystąpień zaplecza. Po pomyślnym przeprowadzeniu sondowania i zaplecze może odbierać ruch, jest on traktowany jako zdrowy. W przeciwnym razie jest uznawane za złej kondycji.

> [!IMPORTANT]
> Jeśli w podsieci Application Gateway istnieje sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń), Otwórz zakres portów 65503-65534 w podsieci Application Gateway dla ruchu przychodzącego. Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Są one zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez odpowiednich certyfikatów, jednostki zewnętrzne, w tym klienci tych bram, nie będą mogły inicjować żadnych zmian w tych punktach końcowych.


### <a name="view-back-end-health-through-the-portal"></a>Wyświetlanie kondycji zaplecza za pomocą portalu

W portalu kondycja zaplecza jest dostarczana automatycznie. W istniejącej bramie aplikacji wybierz pozycję **monitorowanie** > **kondycja zaplecza**. 

Każdy element członkowski w puli zaplecza znajduje się na tej stronie (niezależnie od tego, czy jest to karta sieciowa, adres IP czy nazwa FQDN). Wyświetlana jest Nazwa puli zaplecza, port, nazwa ustawień protokołu HTTP zaplecza i stan kondycji. Prawidłowe wartości kondycji są **zdrowe**, **złej kondycji**i nieznane.

> [!NOTE]
> Jeśli widzisz stan kondycji zaplecza **nieznany**, upewnij się, że dostęp do zaplecza nie jest blokowany przez regułę sieciowej grupy zabezpieczeń, zdefiniowaną przez użytkownika trasę (UDR) lub niestandardową usługę DNS w sieci wirtualnej.

![Kondycja zaplecza][10]

### <a name="view-back-end-health-through-powershell"></a>Wyświetlanie kondycji zaplecza za poorednictwem programu PowerShell

Poniższy kod programu PowerShell przedstawia sposób wyświetlania kondycji zaplecza przy użyciu `Get-AzApplicationGatewayBackendHealth` polecenia cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Wyświetlanie kondycji zaplecza za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Wyniki

Poniższy fragment kodu przedstawia przykład odpowiedzi:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Dzienniki diagnostyczne

Za pomocą różnych typów dzienników na platformie Azure można zarządzać bramami aplikacji i rozwiązywać problemy. Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z usługi Azure Blob Storage i przeglądać je w różnych narzędziach, takich jak [dzienniki Azure monitor](../azure-monitor/insights/azure-networking-analytics.md), Excel i Power BI. Więcej informacji o różnych typach dzienników można znaleźć na poniższej liście:

* **Dziennik aktywności**: Aby wyświetlić wszystkie operacje przesyłane do subskrypcji platformy Azure i ich stan, można użyć [dzienników aktywności platformy Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (dawniej nazywanych dziennikami operacyjnymi i dziennikami inspekcji). Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.
* **Dziennik dostępu**: Ten dziennik służy do wyświetlania wzorców dostępu Application Gateway i analizowania ważnych informacji. Dotyczy to adresu IP wywołującego, żądanego adresu URL, opóźnienia odpowiedzi, kodu powrotu i liczby bajtów. Dziennik dostępu jest zbierany co 300 sekund. Ten dziennik zawiera jeden rekord na wystąpienie Application Gateway. Wystąpienie Application Gateway jest identyfikowane przez właściwość instanceId.
* **Dziennik wydajności**: Ten dziennik służy do wyświetlania sposobu wykonywania wystąpień Application Gateway. Ten dziennik przechwytuje informacje o wydajności dla każdego wystąpienia, w tym łączne obsłużone żądania, przepływność w bajtach, Łączna Liczba obsłużonych żądań, liczbę żądań zakończonych niepowodzeniem oraz liczbę wystąpień zaplecza w złej kondycji i złej jakości. Dziennik wydajności jest zbierany co 60 sekund.
* **Dziennik zapory**: Ten dziennik służy do wyświetlania żądań, które są rejestrowane w trybie wykrywania lub zapobiegania bramie aplikacji skonfigurowanej za pomocą zapory aplikacji sieci Web.

> [!NOTE]
> Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Azure Resource Manager. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby lepiej zrozumieć dwa modele, zapoznaj się z artykułem dotyczącym [wdrażania i wdrażania klasycznego Menedżer zasobów](../azure-resource-manager/resource-manager-deployment-model.md) .

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: Konta magazynu najlepiej używać w przypadku dzienników, gdy dzienniki są przechowywane przez dłuższy czas i są przeglądane w razie potrzeby.
* **Centra zdarzeń**: Centra zdarzeń to doskonałe rozwiązanie umożliwiające integrację z innymi narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SEIM) w celu uzyskania alertów dotyczących zasobów.
* **Azure monitor dzienników**: Dzienniki Azure Monitor najlepiej sprawdzają się w czasie rzeczywistym monitorowania aplikacji lub patrząc na trendy.

### <a name="enable-logging-through-powershell"></a>Włączanie rejestrowania za poorednictwem programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych w tych dziennikach, należy włączyć funkcję rejestrowania dostępu i wydajności. Aby włączyć rejestrowanie, wykonaj następujące czynności:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać\<:/subscriptions/\>identyfikator grupy\>zasobów/resourceGroups/\</Providers/Microsoft.Storage/storageAccounts/\<nazwakontamagazynu\>. Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal.

    ![Portal: Identyfikator zasobu dla konta magazynu](./media/application-gateway-diagnostics/diagnostics1.png)

2. Zanotuj identyfikator zasobu bramy aplikacji, dla którego jest włączone rejestrowanie. Ta wartość ma postać\<:/subscriptions/\>identyfikator grupy\>zasobów/resourceGroups/\</Providers/Microsoft.Network/applicationGateways/\<nazwa bramy aplikacji \>. Te informacje możesz znaleźć w portalu.

    ![Portal: Identyfikator zasobu dla usługi Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Włącz rejestrowanie diagnostyczne przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Dzienniki aktywności nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

### <a name="enable-logging-through-the-azure-portal"></a>Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W Azure Portal Znajdź zasób i wybierz pozycję **Ustawienia diagnostyczne**.

   W przypadku Application Gateway dostępne są trzy dzienniki:

   * Dziennik dostępu
   * Dziennik wydajności
   * Dziennik zapory

2. Aby rozpocząć zbieranie danych, wybierz pozycję **Włącz diagnostykę**.

   ![Włączanie diagnostyki][1]

3. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych. W tym przykładzie Log Analytics przechowuje dzienniki. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.

   ![Uruchamianie procesu konfiguracji][2]

5. Wpisz nazwę ustawień, Potwierdź ustawienia, a następnie wybierz pozycję **Zapisz**.

### <a name="activity-log"></a>Dziennik aktywności

Platforma Azure domyślnie generuje dziennik aktywności. Dzienniki są przechowywane przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Dowiedz się więcej o tych dziennikach, odczytując artykuł [Wyświetlanie zdarzeń i dziennika aktywności](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

### <a name="access-log"></a>Dziennik dostępu

Dziennik dostępu jest generowany tylko wtedy, gdy włączono go na każdym wystąpieniu Application Gateway, zgodnie z opisem w poprzednich krokach. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Każdy dostęp do Application Gateway jest rejestrowany w formacie JSON, jak pokazano w następującym przykładzie dla wersji 1:

|Value  |Opis  |
|---------|---------|
|instanceId     | Application Gateway wystąpienie, które obsłużyło żądanie.        |
|clientIP     | Adres IP pochodzący od żądania.        |
|clientPort     | Port źródłowy żądania.       |
|httpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebranego żądania.        |
|RequestQuery     | **Serwer — kierowany**: Wystąpienie puli zaplecza, które wysłało żądanie.</br>**X-AzureApplicationGateway-log-ID**: Identyfikator korelacji używany dla żądania. Może służyć do rozwiązywania problemów z ruchem na serwerach zaplecza. </br>**SERWER — STAN**: Kod odpowiedzi HTTP otrzymany Application Gateway od zaplecza.       |
|UserAgent     | Agent użytkownika z nagłówka żądania HTTP.        |
|httpStatus     | Kod stanu HTTP zwrócony do klienta z Application Gateway.       |
|httpVersion     | Wersja protokołu HTTP żądania.        |
|Hmaster     | Rozmiar odebranego pakietu w bajtach.        |
|Hmaster| Rozmiar wysłanego pakietu, w bajtach.|
|timeTaken| Czas (w milisekundach), przez jaki trwa przetwarzanie żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako interwał od momentu, gdy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że pole czas wykonania zazwyczaj obejmuje czas, w którym żądania i pakiety odpowiedzi są przesyłane przez sieć. |
|sslEnabled| Czy komunikacja z pulami zaplecza korzysta z protokołu SSL. Prawidłowe wartości są włączone i wyłączone.|
|host| Nazwa hosta, za pomocą którego żądanie zostało wysłane do serwera wewnętrznej bazy danych. Jeśli nazwa hosta zaplecza jest przesłonięta, to będzie odzwierciedlać tę nazwę.|
|originalHost| Nazwa hosta, za pomocą którego żądanie zostało odebrane przez Application Gateway od klienta.|
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
W przypadku Application Gateway i WAF v2 dzienniki zawierają nieco więcej informacji:

|Wartość  |Opis  |
|---------|---------|
|instanceId     | Application Gateway wystąpienie, które obsłużyło żądanie.        |
|clientIP     | Adres IP pochodzący od żądania.        |
|clientPort     | Port źródłowy żądania.       |
|httpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebranego żądania.        |
|UserAgent     | Agent użytkownika z nagłówka żądania HTTP.        |
|httpStatus     | Kod stanu HTTP zwrócony do klienta z Application Gateway.       |
|httpVersion     | Wersja protokołu HTTP żądania.        |
|Hmaster     | Rozmiar odebranego pakietu w bajtach.        |
|Hmaster| Rozmiar wysłanego pakietu, w bajtach.|
|timeTaken| Czas (w milisekundach), przez jaki trwa przetwarzanie żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako interwał od momentu, gdy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że pole czas wykonania zazwyczaj obejmuje czas, w którym żądania i pakiety odpowiedzi są przesyłane przez sieć. |
|sslEnabled| Czy komunikacja z pulami zaplecza korzysta z protokołu SSL. Prawidłowe wartości są włączone i wyłączone.|
|sslCipher| Mechanizm szyfrowania używany do komunikacji SSL (jeśli jest włączony protokół SSL).|
|sslProtocol| Używany protokół SSL (jeśli jest włączony protokół SSL).|
|serverRouted| Serwer zaplecza, do którego Brama aplikacji kieruje żądanie.|
|serverStatus| Kod stanu HTTP serwera wewnętrznej bazy danych.|
|serverResponseLatency| Opóźnienie odpowiedzi z serwera wewnętrznej bazy danych.|
|host| Adres podany w nagłówku hosta żądania.|
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

Dziennik wydajności jest generowany tylko wtedy, gdy włączono go na każdym wystąpieniu Application Gateway, zgodnie z opisem w poprzednich krokach. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Dane dziennika wydajności są generowane w 1-minutowych interwałach. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     |  Application Gateway wystąpienie, dla którego są generowane dane wydajności. W przypadku bramy aplikacji o wielu wystąpieniach istnieje jeden wiersz dla każdego wystąpienia.        |
|healthyHostCount     | Liczba hostów w dobrej kondycji w puli zaplecza.        |
|unHealthyHostCount     | Liczba hostów w złej kondycji w puli zaplecza.        |
|requestCount     | Liczba obsłużonych żądań.        |
|opóźnienie | Średnie opóźnienie (w milisekundach) żądań z wystąpienia do zaplecza, który obsługuje żądania. |
|failedRequestCount| Liczba żądań zakończonych niepowodzeniem.|
|throughput| Średnia przepływność od ostatniego dziennika, mierzona w bajtach na sekundę.|

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
> Opóźnienie jest obliczane od momentu otrzymania pierwszego bajtu żądania HTTP do momentu wysłania ostatniego bajtu odpowiedzi HTTP. Jest to suma Application Gateway czasu przetwarzania oraz koszt sieci do zaplecza oraz czas przetwarzania żądania przez zaplecze.

### <a name="firewall-log"></a>Dziennik zapory

Dziennik zapory jest generowany tylko wtedy, gdy włączono go dla każdej bramy aplikacji, zgodnie z opisem w poprzednich krokach. Ten dziennik wymaga również, aby zapora aplikacji sieci Web została skonfigurowana na bramie aplikacji. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Rejestrowane są następujące dane:


|Value  |Opis  |
|---------|---------|
|instanceId     | Wystąpienie Application Gateway, dla którego są generowane dane zapory. W przypadku bramy aplikacji o wielu wystąpieniach istnieje jeden wiersz dla każdego wystąpienia.         |
|clientIp     |   Adres IP pochodzący od żądania.      |
|clientPort     |  Port źródłowy żądania.       |
|requestUri     | Adres URL otrzymanego żądania.       |
|ruleSetType     | Typ zestawu reguł. Dostępna wartość to OWASP.        |
|Element rulesetversion     | Używana wersja zestawu reguł. Dostępne wartości to 2.2.9 i 3,0.     |
|ruleId     | Identyfikator reguły zdarzenia wyzwalającego.        |
|message     | Przyjazny dla użytkownika komunikat dla zdarzenia wyzwalającego. Więcej szczegółowych informacji znajduje się w sekcji Szczegóły.        |
|Akcja     |  Akcja podjęta na żądaniu. Dostępne wartości są blokowane i dozwolone.      |
|witryna     | Lokacja, dla której został wygenerowany dziennik. Obecnie tylko globalne są wyświetlane, ponieważ reguły są globalne.|
|details informacje     | Szczegóły zdarzenia wyzwalającego.        |
|details. Message     | Opis reguły.        |
|details.data     | Określone dane Znalezione w żądaniu, które pasują do reguły.         |
|details. File     | Plik konfiguracji, który zawiera regułę.        |
|Szczegóły. wiersz     | Numer wiersza w pliku konfiguracji, który wyzwolił zdarzenie.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
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

### <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można wyświetlać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure**: Pobierz informacje z dziennika aktywności przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub witryny Azure Portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w artykule [Activity operations with Resource Manager (Operacje działań przy użyciu usługi Resource Manager)](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Jeśli nie masz jeszcze konta usługi [Power BI](https://powerbi.microsoft.com/pricing), możesz ją wypróbować bezpłatnie. Korzystając z [pakietu zawartości dzienników aktywności platformy Azure dla usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), możesz analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych, których możesz używać bez zmian lub po dostosowaniu.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Wyświetlanie i analizowanie dzienników dostępu, wydajności i zapory

[Dzienniki Azure monitor](../azure-monitor/insights/azure-networking-analytics.md) mogą zbierać pliki liczników i dzienników zdarzeń z konta usługi BLOB Storage. Obejmuje ona wizualizacje oraz zaawansowane możliwości wyszukiwania na potrzeby analizowania dzienników.

Ponadto możesz połączyć się z kontem magazynu i pobrać wpisy dziennika JSON dotyczące dostępu i wydajności. Po pobraniu plików JSON możesz je przekonwertować do formatu CSV i wyświetlać w programie Excel, usłudze Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analizowanie dzienników dostępu za poorednictwem GoAccess

Opublikowano szablon Menedżer zasobów, który służy do instalowania i uruchamiania popularnych analizatora dzienników [GoAccess](https://goaccess.io/) na potrzeby Application Gateway dzienników dostępu. GoAccess zapewnia cenne dane statystyczne dotyczące ruchu HTTP, takie jak unikatowych odwiedzających, żądanych plików, hostów, systemów operacyjnych, przeglądarek, kodów stanu HTTP i nie tylko. Aby uzyskać więcej informacji, zobacz [plik Readme w folderze szablonów Menedżer zasobów w witrynie GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Metryki

Metryki to funkcja dla niektórych zasobów platformy Azure, w której można wyświetlić liczniki wydajności w portalu. W przypadku Application Gateway dostępne są następujące metryki:

- **Bieżące połączenia**
- **Nieudane żądania**
- **Liczba hostów w dobrej kondycji**

   Można filtrować według jednej puli zaplecza, aby pokazać hosty zdrowe/złej kondycji w określonej puli zaplecza.


- **Stan odpowiedzi**

   Rozkład kodu stanu odpowiedzi można dodatkowo podzielić na odpowiedzi w kategoriach 2xx, 3xx, 4xx i 5xx.

- **Przepływność**
- **Łączna liczba żądań**
- **Liczba hostów w złej kondycji**

   Można filtrować według jednej puli zaplecza, aby pokazać hosty zdrowe/złej kondycji w określonej puli zaplecza.

Przejdź do bramy aplikacji, w obszarze **monitorowanie** wybierzpozycję metryki. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

Na poniższej ilustracji przedstawiono przykład z trzema metrykami wyświetlonymi w ciągu ostatnich 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Widok metryki")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Aby wyświetlić bieżącą listę metryk, zobacz temat [obsługiwane metryki z Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Reguły alertów

Reguły alertów można uruchomić na podstawie metryk dla zasobu. Na przykład alert może wywołać element webhook lub wysłać wiadomość e-mail do administratora, jeśli przepływność bramy aplikacji jest powyżej, poniżej lub w określonym przedziale czasu.

Poniższy przykład przeprowadzi Cię przez proces tworzenia reguły alertu, która wysyła wiadomość e-mail do administratora po naruszeniu przez przepływność wartości progowej:

1. Wybierz pozycję **Dodaj alert metryki** , aby otworzyć stronę **Dodawanie reguły** . Możesz również uzyskać dostęp do tej strony ze strony metryki.

   ![Przycisk "Dodaj alert dotyczący metryki"][6]

2. Na stronie **Dodawanie reguły** Wypełnij sekcje nazwa, warunek i powiadomienie, a następnie wybierz **przycisk OK**.

   * W selektorze **warunku** wybierz jedną z czterech wartości: **Większe**niż, **większe niż lub równe**, **mniejsze**niż lub **mniejsze niż lub równe**.

   * W selektorze **okresu** Wybierz okres z pięciu minut do 6 godzin.

   * W przypadku wybrania opcji właściciele, współautorzy **i czytelnicy poczty**e-mail wiadomość e-mail może być dynamiczna na podstawie użytkowników, którzy mają dostęp do tego zasobu. W przeciwnym razie można podać listę użytkowników z rozdzieloną przecinkami w polu **dodatkowe adresy e-mail administratora** .

   ![Dodaj stronę reguły][7]

W przypadku naruszenia progu wiadomości e-mail podobnej do tej na poniższej ilustracji przychodzą:

![Adres e-mail pod kątem naruszenia progu][8]

Po utworzeniu alertu dotyczącego metryki pojawia się lista alertów. Zawiera przegląd wszystkich reguł alertów.

![Lista alertów i reguł][9]

Aby dowiedzieć się więcej na temat powiadomień o alertach, zobacz [Odbieranie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Aby dowiedzieć się więcej o elementach webhook i sposobach ich użycia z alertami, odwiedź stronę [Konfigurowanie elementu webhook w ramach alertu dotyczącego metryki platformy Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki liczników i zdarzeń przy użyciu [dzienników Azure monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj dziennik aktywności platformy Azure przy użyciu wpisu w blogu Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) .
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w Power BI i więcej wpisów w](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogu.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
