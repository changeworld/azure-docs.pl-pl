---
title: Monitor uzyskiwanie dostępu do dzienników, Dzienniki wydajności, kondycja zaplecza i metryki dla usługi Azure Application Gateway
description: Dowiedz się, jak włączyć i zarządzać uzyskiwanie dostępu do dzienników i dzienników wydajności dla usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: victorh
ms.openlocfilehash: 39317c0448168bc2ed8fdd0455a210254887d496
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655388"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Kondycja zaplecza, dzienniki diagnostyczne i metryki dla usługi Application Gateway

Za pomocą usługi Azure Application Gateway, można monitorować zasoby w następujący sposób:

* [Kondycja zaplecza](#back-end-health): Usługa Application Gateway zapewnia możliwość monitorowania kondycji serwerów w puli zaplecza za pośrednictwem witryny Azure portal i za pomocą programu PowerShell. Można również znaleźć kondycję pul zaplecza za pomocą dzienników diagnostycznych wydajności.

* [Dzienniki](#diagnostic-logging): Dzienniki umożliwiają wydajności, dostępu i inne dane, które mają być zapisane lub używane z zasobu do celów monitorowania.

* [Metryki](#metrics): Usługa Application Gateway obecnie ma siedem metryki, aby wyświetlić liczniki wydajności.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Kondycja zaplecza

Usługa Application Gateway zapewnia możliwość monitorowania kondycji poszczególnych elementów członkowskich pul zaplecza za pośrednictwem portalu, programu PowerShell i interfejsu wiersza polecenia (CLI). Możesz również znaleźć kondycji zagregowane podsumowanie pule zaplecza za pomocą dzienników diagnostycznych wydajności. 

Raport o kondycji zaplecza odzwierciedla dane wyjściowe sondę kondycji bramy Application Gateway do wystąpień zaplecza. Podczas badania zakończy się pomyślnie i zaplecza mogą odbierać dane, jest uznawane za dobrej kondycji. W przeciwnym razie jego jest określana jako zła.

> [!IMPORTANT]
> Jeśli w podsieci bramy Application Gateway istnieje sieciowa grupa zabezpieczeń (NSG), otwórz zakresów portów 65503 65534 w podsieci bramy Application Gateway dla ruchu przychodzącego. Zakres tego portu jest wymagany do komunikacji infrastruktury platformy Azure. Są one zabezpieczone (zablokowane) z użyciem certyfikatów Azure. Bez prawidłowych certyfikatów podmioty zewnętrzne, w tym klienci tych bram, nie będzie można zainicjować wszelkie zmiany w tych punktach końcowych.


### <a name="view-back-end-health-through-the-portal"></a>Wyświetl kondycję zaplecza za pośrednictwem portalu

W witrynie portal kondycja zaplecza znajduje się automatycznie. W istniejącej bramie aplikacji, wybierz **monitorowanie** > **kondycję wewnętrznej bazy danych**. 

Każdy element członkowski w puli zaplecza znajduje się na tej stronie (czy jest kart Sieciowych, adresów IP lub nazwa FQDN). Nazwa puli zaplecza, portów, Nazwa ustawienia HTTP zaplecza i stan kondycji są wyświetlane. Prawidłowe wartości dla stanu kondycji **dobra kondycja**, **zła**, i **nieznany**.

> [!NOTE]
> Jeśli zostanie wyświetlony stan kondycji zaplecza **nieznany**, upewnij się, że dostęp do zaplecza nie jest blokowany przez reguły sieciowej grupy zabezpieczeń, trasa zdefiniowana przez użytkownika (UDR) lub niestandardowego systemu DNS w sieci wirtualnej.

![Kondycja zaplecza][10]

### <a name="view-back-end-health-through-powershell"></a>Wyświetl kondycję zaplecza za pomocą programu PowerShell

Następujący kod programu PowerShell pokazuje, jak wyświetlić kondycja zaplecza przy użyciu `Get-AzApplicationGatewayBackendHealth` polecenia cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Wyświetl kondycję zaplecza za pośrednictwem wiersza polecenia platformy Azure

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

Różne typy dzienników platformy Azure umożliwia zarządzanie i rozwiązywanie problemów z bram application Gateway. Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z usługi Azure Blob storage i wyświetlane w różnych narzędzi, takich jak [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), Excel i Power BI. Możesz dowiedzieć się więcej o różnych typach dzienniki z następującej listy:

* **Dziennik aktywności**: Możesz użyć [Dzienniki aktywności platformy Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (znanego wcześniej pod nazwą operacyjne dzienniki i dzienników inspekcji) aby wyświetlić wszystkie operacje, które są przesyłane do Twojej subskrypcji platformy Azure i ich stan. Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlać w witrynie Azure Portal.
* **Dziennik dostępu**: Ten dziennik służy do wyświetlania wzorce dostępu do bramy aplikacji i analizować istotne informacje. Obejmuje to IP obiektu wywołującego, żądany adres URL, opóźnienie odpowiedzi, kod powrotny i bajtów wewnątrz i na zewnątrz. Dziennik dostępu są gromadzone co 300 sekund. Ten dziennik zawiera jeden rekord dla każdego wystąpienia bramy aplikacji. Wystąpienie bramy Application Gateway jest identyfikowany przez właściwość instanceId.
* **Dziennik wydajności**: Ten dziennik służy do wyświetlania, jak działają wystąpień bramy Application Gateway. Ten dziennik zawiera informacje o wydajności dla każdego wystąpienia, łączna liczba żądań obsługiwanych, w tym przepływność w bajtach, łączna liczba żądań obsługiwana liczba żądań zakończonych niepowodzeniem, a liczba zdrowe i niezdrowe wystąpień zaplecza. Dziennik wydajności są zbierane co 60 sekund.
* **Dziennik zapory**: Ten dziennik służy do wyświetlania żądań, które są rejestrowane w trybie wykrywania i zapobiegania usługi application gateway, który jest skonfigurowany przy użyciu zapory aplikacji sieci web.

> [!NOTE]
> Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania usługi Azure Resource Manager. Nie można używać dzienników zasobów w klasycznym modelu wdrażania. W celu lepszego zrozumienia dwóch modeli, zobacz [wdrażania Understanding Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md) artykułu.

Masz trzy opcje przechowywania dzienników:

* **Konto magazynu**: Konta magazynu są używane dla dzienników najlepiej, gdy dzienniki są przechowywane przez dłuższy czas i sprawdzone w razie.
* **Usługa Event hubs**: Usługa Event hubs to doskonałe rozwiązanie umożliwiające integrację z innymi informacjami i zdarzeniami (SEIM) narzędzia do zarządzania zabezpieczeniami, aby otrzymywać alerty dotyczące zasobów.
* **Dzienniki platformy Azure Monitor**: Dzienniki platformy Azure Monitor najlepiej nadaje się do ogólnego monitorowania w czasie rzeczywistym, w aplikacji lub przyglądanie się trendom.

### <a name="enable-logging-through-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Należy włączyć dostępu i logowania do rozpoczęcia zbierania danych dostępne za pośrednictwem tych dzienników wydajności. Aby włączyć rejestrowanie, wykonaj następujące kroki:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać: /subscriptions/\<subscriptionId\>/resourceGroups/\<nazwy grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<Nazwakontausługistorage\>. Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal.

    ![Portalu: identyfikator zasobu dla konta magazynu](./media/application-gateway-diagnostics/diagnostics1.png)

2. Należy pamiętać, dla której jest włączone rejestrowanie identyfikator zasobu bramy aplikacji. Ta wartość ma postać: /subscriptions/\<subscriptionId\>/resourceGroups/\<nazwy grupy zasobów\>/providers/Microsoft.Network/applicationGateways/\<nazwa bramy aplikacji \>. Te informacje możesz znaleźć w portalu.

    ![Portal: identyfikator zasobu usługi application gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Włącz rejestrowanie diagnostyczne przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Dzienniki aktywności nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

### <a name="enable-logging-through-the-azure-portal"></a>Włączanie rejestrowania za pośrednictwem witryny Azure Portal

1. W witrynie Azure portal Znajdź zasób i wybierz **ustawień diagnostycznych**.

   W usłudze Application Gateway dostępne są trzy dzienniki:

   * Dziennik dostępu
   * Dziennik wydajności
   * Dziennik zapory

2. Aby rozpocząć zbieranie danych, wybierz pozycję **Włącz diagnostykę**.

   ![Włączanie diagnostyki][1]

3. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych. W tym przykładzie usługi Log Analytics są przechowywane dzienniki. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.

   ![Uruchamianie procesu konfiguracji][2]

5. Wpisz nazwę dla ustawienia, Potwierdź ustawienia, a następnie wybierz **Zapisz**.

### <a name="activity-log"></a>Dziennik aktywności

Domyślnie Azure generuje dziennik aktywności. Dzienniki są zachowywane przez 90 dni w magazynie Azure dzienniki zdarzeń. Dowiedz się więcej na temat tych dzienników, zapoznając [wyświetlanie zdarzeń i dzienników aktywności](../monitoring-and-diagnostics/insights-debugging-with-events.md) artykułu.

### <a name="access-log"></a>Dziennik dostępu

Dziennik dostępu jest generowany tylko wtedy, gdy włączono w każdym wystąpieniu bramy aplikacji, zgodnie z opisem w poprzednich krokach. Dane są przechowywane na koncie magazynu, który określiłeś, gdy włączono rejestrowanie. Każdy dostęp do usługi Application Gateway jest rejestrowany w formacie JSON, jak pokazano w poniższym przykładzie dla wersji 1:

|Wartość  |Opis  |
|---------|---------|
|instanceId     | Wystąpienia bramy aplikacji, który obsłużył żądanie.        |
|clientIP     | Źródłowy adres IP dla żądania.        |
|clientPort     | Port źródłowy dla żądania.       |
|HttpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebrane żądanie.        |
|RequestQuery     | **Serwer routingu**: Wystąpienie puli zaplecza, do którego wysłano żądanie.</br>**X-AzureApplicationGateway-LOG-ID**: Identyfikator korelacji użyta dla żądania. Może służyć do rozwiązywania problemów ruch na serwerach zaplecza. </br>**STAN SERWERA**: Kod odpowiedzi HTTP bramy Application Gateway otrzymany z zaplecza.       |
|UserAgent     | Agent użytkownika w nagłówku żądania HTTP.        |
|httpStatus     | Kod stanu HTTP zwracany do klienta z bramy aplikacji.       |
|httpVersion     | Wersja protokołu HTTP żądania.        |
|ReceivedBytes     | Rozmiar pakietu odebranych w bajtach.        |
|SentBytes| Rozmiar pakietów wysyłanych w bajtach.|
|Właściwość timeTaken| Długość czasu (w milisekundach) potrzebny do przetworzenia żądania i odpowiedzi przez punkt końcowy do wysłania. To jest obliczana jako interwału od czasu, gdy usługa Application Gateway odbiera pierwszy bajt żądania HTTP do chwili, gdy odpowiedź wysyłania zakończy operację. Należy zauważyć, że pole Time-Taken zawiera zwykle czas żądania i odpowiedzi pakiety są przesyłane przez sieć. |
|sslEnabled| Czy komunikacji z pul zaplecza używany protokół SSL. Prawidłowe wartości to włączać i wyłączać.|
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
        "sslEnabled": "off"
    }
}
```
Usługa Application Gateway i zapory aplikacji sieci Web w wersji 2 dzienniki przedstawiają nieco więcej informacji:

|Value  |Opis  |
|---------|---------|
|instanceId     | Wystąpienia bramy aplikacji, który obsłużył żądanie.        |
|clientIP     | Źródłowy adres IP dla żądania.        |
|clientPort     | Port źródłowy dla żądania.       |
|HttpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebrane żądanie.        |
|RequestQuery     | **Serwer routingu**: Wystąpienie puli zaplecza, do którego wysłano żądanie.</br>**X-AzureApplicationGateway-LOG-ID**: Identyfikator korelacji użyta dla żądania. Może służyć do rozwiązywania problemów ruch na serwerach zaplecza. </br>**STAN SERWERA**: Kod odpowiedzi HTTP bramy Application Gateway otrzymany z zaplecza.       |
|UserAgent     | Agent użytkownika w nagłówku żądania HTTP.        |
|httpStatus     | Kod stanu HTTP zwracany do klienta z bramy aplikacji.       |
|httpVersion     | Wersja protokołu HTTP żądania.        |
|ReceivedBytes     | Rozmiar pakietu odebranych w bajtach.        |
|SentBytes| Rozmiar pakietów wysyłanych w bajtach.|
|Właściwość timeTaken| Długość czasu (w milisekundach) potrzebny do przetworzenia żądania i odpowiedzi przez punkt końcowy do wysłania. To jest obliczana jako interwału od czasu, gdy usługa Application Gateway odbiera pierwszy bajt żądania HTTP do chwili, gdy odpowiedź wysyłania zakończy operację. Należy zauważyć, że pole Time-Taken zawiera zwykle czas żądania i odpowiedzi pakiety są przesyłane przez sieć. |
|sslEnabled| Czy komunikacji z pul zaplecza używany protokół SSL. Prawidłowe wartości to włączać i wyłączać.|
|sslCipher| Mechanizmy szyfrowania używanych na potrzeby komunikacji SSL (jeśli jest włączony protokół SSL).|
|sslProtocol| Protokół SSL używany (jeśli jest włączony protokół SSL).|
|serverRouted| Serwer wewnętrznej bazy danych tej bramy application gateway kieruje żądanie.|
|serverStatus| Kod stanu HTTP serwera wewnętrznej bazy danych.|
|serverResponseLatency| Opóźnienie odpowiedzi z serwera wewnętrznej bazy danych.|
|host| Adres na liście nagłówek hosta żądania.|
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
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "52.231.230.101"
    }
}
```

### <a name="performance-log"></a>Dziennik wydajności

Dziennik wydajności jest generowany tylko wtedy, gdy została włączona w każdym wystąpieniu bramy aplikacji, zgodnie z opisem w poprzednich krokach. Dane są przechowywane na koncie magazynu, który określiłeś, gdy włączono rejestrowanie. Dane dziennika wydajności są generowane w 1-minutowych interwałach. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     |  Dla wydajności, które dane są generowane wystąpienia bramy aplikacji. Dla bramy aplikacji z wieloma wystąpieniami ma jeden wiersz dla każdego wystąpienia.        |
|healthyHostCount     | Liczba hosty o dobrej kondycji w puli zaplecza.        |
|unHealthyHostCount     | Liczba hostów złej kondycji w puli zaplecza.        |
|requestCount     | Liczba żądań, które są obsługiwane.        |
|Czas oczekiwania | Średnie opóźnienie (w milisekundach) dla żądań z wystąpienia do zaplecza, która służy do żądania. |
|failedRequestCount| Liczba żądań zakończonych niepowodzeniem.|
|throughput| Średnia przepływność od czasu ostatniego dziennika, mierzone w bajtach na sekundę.|

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
> Czas oczekiwania jest obliczany od momentu, gdy odbiera pierwszy bajt żądania HTTP do chwili, gdy ostatni bajt odpowiedzi HTTP są wysyłane. Jest to suma czasu przetwarzania Application Gateway plus koszt sieci wewnętrznej, a także czas zaplecza przyjmuje przetwarzania żądania.

### <a name="firewall-log"></a>Dziennik zapory

Dziennik zapory jest generowany tylko wtedy, gdy włączono dla każdej bramy aplikacji, zgodnie z opisem w poprzednich krokach. Ten dziennik wymaga również, że Zapora aplikacji sieci web jest skonfigurowany w bramie aplikacji. Dane są przechowywane na koncie magazynu, który określiłeś, gdy włączono rejestrowanie. Rejestrowane są następujące dane:


|Value  |Opis  |
|---------|---------|
|instanceId     | Zapory, które dane są generowane wystąpienia bramy aplikacji. Dla bramy aplikacji z wieloma wystąpieniami ma jeden wiersz dla każdego wystąpienia.         |
|clientIp     |   Źródłowy adres IP dla żądania.      |
|clientPort     |  Port źródłowy dla żądania.       |
|requestUri     | Adres URL odebrane żądanie.       |
|ruleSetType     | Typ zestawu reguł. Dostępne wartości to OWASP.        |
|ruleSetVersion     | Wersja używanego zestawu reguł. Dostępne wartości to 2.2.9 i 3.0.     |
|ruleId     | Identyfikator reguły wyzwalającą zdarzenie.        |
|message     | Przyjazny dla użytkownika komunikat wyzwalająca zdarzenia. Szczegółowe informacje znajdują się w sekcji szczegółów.        |
|Akcja     |  Nie wykonano akcji na żądanie. Dostępne wartości są zablokowane i dozwolone.      |
|Witryny     | Witryna, dla której został wygenerowany dziennik. Obecnie tylko globalne jest wyświetlany, ponieważ reguły są globalne.|
|details informacje     | Szczegółowe informacje o zdarzeniu wyzwalającym.        |
|details.Message     | Opis reguły.        |
|details.data     | Odnaleziony w żądaniu, który jest zgodny z reguły określonych danych.         |
|details.file     | Plik konfiguracji, który zawierał reguły.        |
|details.Line     | Numer wiersza w pliku konfiguracji, który wywołał zdarzenie.       |

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

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Wyświetlanie i analizowanie dostępu, wydajność i dzienniki zapory

[Dzienniki platformy Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) może zbierać pliki liczników i dziennik zdarzeń z konta usługi Blob storage. Obejmuje ona wizualizacje oraz zaawansowane możliwości wyszukiwania na potrzeby analizowania dzienników.

Ponadto możesz połączyć się z kontem magazynu i pobrać wpisy dziennika JSON dotyczące dostępu i wydajności. Po pobraniu plików JSON możesz je przekonwertować do formatu CSV i wyświetlać w programie Excel, usłudze Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analizowanie dzienników dostępu za pośrednictwem GoAccess

Opublikowane dotychczas szablonu usługi Resource Manager, która jest instalowana i uruchamiana popularnej [GoAccess](https://goaccess.io/) dziennik analizatora, aby uzyskać dostęp do dzienników usługi Application Gateway. GoAccess udostępnia cenne statystyki ruch HTTP, takie jak unikatowych osób odwiedzających, żądane pliki, hosty, systemy operacyjne, przeglądarki, kody stanu HTTP i nie tylko. Aby uzyskać więcej informacji, zobacz [pliku Readme w folderu szablonu usługi Resource Manager w usłudze GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Metryki

Metryki są funkcją dla niektórych zasobów platformy Azure, w którym można wyświetlać liczniki wydajności w portalu. W usłudze Application Gateway dostępne są następujące metryki:

- **Bieżąca liczba połączeń**
- **Żądania zakończone niepowodzeniem**
- **Liczba hostów w dobrej kondycji**

   Można filtrować według poszczególnych puli wewnętrznej bazy danych do wyświetlenia hostów w dobrej kondycji/złej kondycji w puli zaplecza określone.


- **Stan odpowiedzi**

   Dystrybucja kodów stanu odpowiedzi może następnie podzielone do wyświetlania odpowiedzi w 2xx, 3xx, 4xx i 5xx kategorii.

- **Przepływność**
- **Łączna liczba żądań**
- **Liczba hostów w złej kondycji**

   Można filtrować według poszczególnych puli wewnętrznej bazy danych do wyświetlenia hostów w dobrej kondycji/złej kondycji w puli zaplecza określone.

Przejdź do bramy aplikacji w obszarze **monitorowanie** wybierz **metryki**. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

Na poniższej ilustracji możesz zobaczyć przykład, za pomocą trzech metryk dla ostatnich 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Wyświetl metryki")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Aby wyświetlić bieżącą listę metryk, zobacz [metryki obsługiwane z usługą Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Reguły alertów

Można uruchomić reguły alertów w oparciu o metryki dla zasobu. Na przykład alert może wywoływania elementu webhook lub wiadomości e-mail administratora, jeśli przepływność bramy application gateway jest powyżej, poniżej lub w wartości progowej przez określony okres.

Poniższy przykład przeprowadzi Cię przez tworzenie reguły alertu, który wysyła wiadomość e-mail do administratora po naruszeń przepływności próg:

1. Wybierz **Dodaj alert dotyczący metryki** otworzyć **Dodaj regułę** strony. Mogą również skorzystać z tej strony ze strony metryki.

   ![Przycisk "Dodaj alert dotyczący metryki"][6]

2. Na **Dodaj regułę** strony, wprowadź nazwę warunku sekcji Powiadom i wybierz **OK**.

   * W **warunek** selektor, wybierz jedną z czterech wartości: **Większa niż**, **większy lub równy**, **mniej niż**, lub **mniejsze niż lub równe**.

   * W **okres** selektor, wybierz okres, w ciągu pięciu minut do 6 godzin.

   * Jeśli wybierzesz **właściciele, współautorzy i czytelnicy poczty E-mail**, wiadomości e-mail może być dynamiczny na podstawie użytkowników, którzy mają dostęp do tego zasobu. W przeciwnym razie możesz podać listę rozdzielanych przecinkami użytkowników w **email(s) dodatkowego administratora** pole.

   ![Dodaj stronę reguły][7]

W przypadku włamania się progu, zostanie odebrana wiadomość e-mail, który jest podobny do przedstawionego na poniższym obrazie:

![Adres e-mail naruszenia progu][8]

Po utworzeniu alertu dotyczącego metryki, zostanie wyświetlona lista alertów. Zapewnia przegląd wszystkich reguł alertów.

![Lista reguł i alertów][9]

Aby dowiedzieć się więcej na temat powiadomień o alertach, zobacz [otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Aby dowiedzieć się więcej na temat elementów webhook i jak ich używać z alertami, odwiedź stronę [Konfigurowanie elementu webhook dla alertu dotyczącego metryki Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Następne kroki

* Wizualizuj dzienniki zdarzeń i liczników przy użyciu [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* [Wizualizuj dziennik aktywności platformy Azure z usługą Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) wpis w blogu.
* [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) wpis w blogu.

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
