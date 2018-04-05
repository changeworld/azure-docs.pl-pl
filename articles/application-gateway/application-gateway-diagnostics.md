---
title: Monitoruj dzienniki dostęp, Dzienniki wydajności kondycji zaplecza i metryki bramy aplikacji
description: Dowiedz się, jak włączyć i zarządzać Dzienniki wydajności i dzienników dostępu bramy aplikacji
services: application-gateway
author: amitsriva
manager: rossort
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/2/2018
ms.author: amitsriva
ms.openlocfilehash: 982ae712320cb390b1822de6a7a3980ebfb6251e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Kondycji zaplecza, dzienniki diagnostyczne i metryki bramy aplikacji

Korzystając z bramy aplikacji Azure, możesz monitorować zasobów w następujący sposób:

* [Kondycja zaplecza](#back-end-health): bramy aplikacji umożliwia monitorowanie kondycji serwerów w puli zaplecza za pośrednictwem portalu Azure i przy użyciu programu PowerShell. Można również znaleźć kondycję pul zaplecza za pośrednictwem dzienników diagnostycznych wydajności.

* [Dzienniki](#diagnostic-logging): dzienniki umożliwiają wydajność, dostępu i innych danych, które mają być zapisywane lub używane z zasobu do celów monitorowania.

* [Metryki](#metrics): bramy aplikacji ma obecnie siedem metryki, aby wyświetlić liczniki wydajności.

## <a name="back-end-health"></a>Kondycja zaplecza

Brama aplikacji umożliwia monitorowanie kondycji poszczególnych członków pul zaplecza za pośrednictwem portalu, programu PowerShell i interfejsu wiersza polecenia (CLI). Możesz również znaleźć zagregowane kondycji podsumowania pul zaplecza za pośrednictwem dzienników diagnostycznych wydajności. 

Raport o kondycji zaplecza odzwierciedla dane wyjściowe do wystąpień zaplecza sondy kondycji bramy aplikacji. Podczas badania zakończy się pomyślnie i wewnętrznej mogą odbierać dane, jest on uznawany za dobrej kondycji. W przeciwnym razie jego jest określana jako zła.

> [!IMPORTANT]
> Jeśli istnieje grupa zabezpieczeń sieci (NSG) w podsieci bramy aplikacji, otwórz zakresy portów 65503 65534 podsieci bramy aplikacji dla ruchu przychodzącego. Te porty są wymagane dla kondycji zaplecza interfejsu API do pracy.


### <a name="view-back-end-health-through-the-portal"></a>Wyświetl kondycję zaplecza za pośrednictwem portalu

W portalu wewnętrzną kondycji znajduje się automatycznie. Wybierz istniejącą bramę aplikacji **monitorowanie** > **kondycji zaplecza**. 

Każdy element członkowski w puli zaplecza znajduje się na tej stronie (czy jest ona karty Sieciowej, adresu IP lub FQDN). Nazwa puli zaplecza, portu, nazwy ustawienia HTTP zaplecza i stan kondycji są wyświetlane. Prawidłowe wartości stanu kondycji to **dobra kondycja**, **niezdrowego**, i **nieznany**.

> [!NOTE]
> Jeśli zostanie wyświetlony stan kondycji zaplecza **nieznany**, upewnij się, że dostęp do wewnętrznych nie jest blokowane przez reguły NSG, trasy zdefiniowane przez użytkownika (przez) lub niestandardowe DNS w sieci wirtualnej.

![Kondycja zaplecza][10]

### <a name="view-back-end-health-through-powershell"></a>Wyświetl kondycję zaplecza za pomocą programu PowerShell

Poniższy kod programu PowerShell pokazano, jak wyświetlić kondycję zaplecza przy użyciu `Get-AzureRmApplicationGatewayBackendHealth` polecenia cmdlet:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Wyświetl kondycję zaplecza za pośrednictwem 2.0 interfejsu wiersza polecenia platformy Azure

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

Różne typy dzienników Azure umożliwia zarządzanie i rozwiązywanie problemów z bramy aplikacji. Niektóre z tych dzienników dostępne za pośrednictwem portalu. Wszystkie dzienniki można wyodrębnić z magazynu obiektów Blob platformy Azure i wyświetlane w różnych narzędzi, takich jak [analizy dzienników](../log-analytics/log-analytics-azure-networking-analytics.md), Excel i Power BI. Użytkownik może więcej informacji na temat różnych typów dzienników z poniższej listy:

* **Dziennik aktywności**: można użyć [Dzienniki aktywności Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (wcześniej znane jako dzienniki inspekcji i operacyjne dzienniki) aby wyświetlić wszystkie operacje, które są przesyłane do Twojej subskrypcji platformy Azure i ich stan. Wpisy dziennika aktywności są zbierane domyślnie i można je wyświetlić w portalu Azure.
* **Dziennik dostępu**: ten dziennik służy do wyświetlania wzorce dostępu bramy aplikacji i analizowania ważne informacje, w tym adresu IP, żądanego adresu URL wywołującego, czas oczekiwania na odpowiedź, kod powrotny i bajtów i wylogowanie. Dziennik dostępu są gromadzone co 300 sekund. Ten dziennik zawiera jeden rekord dla każdego wystąpienia bramy aplikacji. Wystąpienie bramy aplikacji mogą zostać zidentyfikowane na podstawie właściwość instanceId.
* **Dziennik wydajności**: ten dziennik służy do wyświetlania, jak działają wystąpieniach bramy aplikacji. Ten dziennik zawiera informacje o wydajności dla każdego wystąpienia, w tym całkowita liczba żądań obsłużonych, przepływność w bajtach, całkowita liczba żądań obsłużonych, liczba nieudanych żądań, a liczba wystąpień zaplecza dobrej kondycji i złej kondycji. Dziennik wydajności są zbierane co 60 sekund.
* **Dziennik zapory**: ten dziennik służy do wyświetlania żądań, które są rejestrowane za pomocą wykrywania i zapobiegania tryb bramę aplikacji, który jest skonfigurowany z zapory aplikacji sieci web.

> [!NOTE]
> Dzienniki są dostępne tylko dla zasobów wdrożone w modelu wdrażania usługi Azure Resource Manager. Nie można używać dzienników zasobów w klasycznym modelu wdrażania. Aby lepiej zrozumieć dwa modele, zobacz [wdrożenia Understanding Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md) artykułu.

Są trzy opcje do przechowywania dzienników:

* **Konto magazynu**: konta magazynu są najlepiej nadaje się do dzienników podczas dzienniki są przechowywane przez dłuższy czas i sprawdzić, w razie potrzeby.
* **Centra zdarzeń**: Event hubs to doskonałe rozwiązanie dla integracji z innych informacji o zabezpieczeniach i narzędzi do zarządzania zdarzenia (SEIM) można pobrać alertów dotyczących zasobów.
* **Zaloguj się Analytics**: analizy dzienników najlepiej nadaje się do ogólnego monitorowania w czasie rzeczywistym aplikacji lub analizowania trendów.

### <a name="enable-logging-through-powershell"></a>Włącz rejestrowanie za pomocą programu PowerShell

Rejestrowanie aktywności jest automatycznie włączona dla każdego zasobu usługi Resource Manager. Należy włączyć dostęp i rejestrowania w celu rozpoczęcia zbierania danych dostępne za pośrednictwem tych dzienników wydajności. Aby włączyć rejestrowanie, wykonaj następujące kroki:

1. Należy zwrócić uwagę identyfikatorów zasobów konta magazynu, w którym są przechowywane dane dziennika. Ta wartość ma postać: /subscriptions/\<subscriptionId\>/resourceGroups/\<Nazwa grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<nazwy konta magazynu\>. Można użyć dowolnego konta magazynu w ramach subskrypcji. Azure portal umożliwia znalezienie tych informacji.

    ![Portalu: identyfikator zasobu dla konta magazynu](./media/application-gateway-diagnostics/diagnostics1.png)

2. Należy pamiętać, dla którego włączono rejestrowanie identyfikator zasobu bramy aplikacji. Ta wartość ma postać: /subscriptions/\<subscriptionId\>/resourceGroups/\<Nazwa grupy zasobów\>/providers/Microsoft.Network/applicationGateways/\<nazwa bramy aplikacji \>. Portal umożliwia znalezienie tych informacji.

    ![Portalu: identyfikator zasobu bramy aplikacji](./media/application-gateway-diagnostics/diagnostics2.png)

3. Za pomocą następującego polecenia cmdlet programu PowerShell, należy włączyć rejestrowanie diagnostyczne:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Dzienniki aktywności nie wymagają oddzielnego konta magazynu. Użycie magazynu dla dostępu do rejestrowania i wydajności wiąże opłaty za usługę.

### <a name="enable-logging-through-the-azure-portal"></a>Włącz rejestrowanie za pośrednictwem portalu Azure

1. W portalu Azure Znajdź zasobu, a następnie kliknij przycisk **dzienniki diagnostyczne**.

   Brama aplikacji dostępne są trzy dzienniki:

   * Dziennik dostępu
   * Dziennika wydajności
   * Dziennik zapory

2. Aby rozpocząć, zbieranie danych, kliknij przycisk **Włącz diagnostykę**.

   ![Włączanie diagnostyki][1]

3. **Ustawień diagnostycznych** blok zawiera ustawienia dla dzienników diagnostycznych. W tym przykładzie analizy dzienników są przechowywane dzienniki. Kliknij przycisk **Konfiguruj** w obszarze **analizy dzienników** do skonfigurowania swojego obszaru roboczego. Aby zapisać dzienników diagnostycznych, można użyć centra zdarzeń i konto magazynu.

   ![Uruchamia proces konfiguracji][2]

4. Wybierz istniejący obszar roboczy analizy dzienników lub Utwórz nową. W tym przykładzie użyto jednego z istniejących.

   ![Opcje dla obszarów roboczych analizy dzienników][3]

5. Potwierdź ustawienia, a następnie kliknij przycisk **zapisać**.

   ![Blok ustawień diagnostycznych z zaznaczenia][4]

### <a name="activity-log"></a>Dziennik aktywności

Domyślnie Azure generuje dziennik aktywności. Dzienniki są zachowywane przez 90 dni w magazynie Azure dzienniki zdarzeń. Dowiedz się więcej o tych dzienników, odczytując [wyświetlanie zdarzeń i dziennika aktywności](../monitoring-and-diagnostics/insights-debugging-with-events.md) artykułu.

### <a name="access-log"></a>Dziennik dostępu

Dziennik dostępu jest generowany tylko wtedy, gdy włączono na każde wystąpienie bramy aplikacji, zgodnie z opisem w poprzedniej procedurze. Dane są przechowywane w określonej po włączeniu rejestrowania konta magazynu. Każdy dostęp brama aplikacji jest rejestrowany w formacie JSON, jak pokazano w poniższym przykładzie:


|Wartość  |Opis  |
|---------|---------|
|instanceId     | Wystąpienie bramy aplikacji, który obsłużył żądanie.        |
|clientIP     | Źródłowy adres IP dla żądania.        |
|clientPort     | Port źródłowy dla żądania.       |
|HttpMethod     | Metoda HTTP używana przez żądanie.       |
|requestUri     | Identyfikator URI odebrane żądanie.        |
|RequestQuery     | **Serwer routingu**: wystąpienie puli zaplecza, którego wysłano żądanie.</br>**X-AzureApplicationGateway-dziennika-ID**: Identyfikator korelacji użytej w żądaniu. Może służyć do rozwiązywania problemów ruchu na serwerach wewnętrznych. </br>**Stan serwera**: kod odpowiedzi HTTP o bramy aplikacji otrzymanych od wewnętrznej.       |
|UserAgent     | Agent użytkownika z nagłówka żądania HTTP.        |
|httpStatus     | Kod stanu HTTP zwrócona do klienta z bramy aplikacji.       |
|Wersja_http     | Wersja protokołu HTTP żądania.        |
|ReceivedBytes     | Rozmiar pakietów otrzymanych w bajtach.        |
|SentBytes| Rozmiar pakietu wysłane w bajtach.|
|Właściwość timeTaken| Długość czas (w milisekundach) przetwarzania żądania i odpowiedzi mają być wysyłane. To jest obliczany jako wartość interwału od czasu, gdy brama aplikacji w odbiera pierwszy bajt żądania HTTP do czasu podczas wysyłania zakończenie operacji w odpowiedzi. Należy pamiętać, że pole Time-Taken zwykle zawiera godzinę, o której żądanie i odpowiedź pakiety są przesyłane przez sieć. |
|SSL| Czy komunikacji z pul zaplecza używać protokołu SSL. Prawidłowe wartości to on i off.|
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

### <a name="performance-log"></a>Dziennika wydajności

Dziennik wydajności jest generowany tylko wtedy, gdy włączono na każde wystąpienie bramy aplikacji, zgodnie z opisem w poprzedniej procedurze. Dane są przechowywane w określonej po włączeniu rejestrowania konta magazynu. Dane dzienników wydajności jest generowana w 1-minutowych interwałach. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     |  Dla wydajności, które dane są generowane wystąpienia bramy aplikacji. Brama aplikacji w wielu wystąpień jest jeden wiersz dla każdego wystąpienia.        |
|healthyHostCount     | Liczba hostów dobrej kondycji w puli zaplecza.        |
|unHealthyHostCount     | Liczba hostów złej kondycji w puli zaplecza.        |
|RequestCount     | Liczba żądań obsłużonych.        |
|opóźnienie | Czas oczekiwania (w milisekundach) żądań z wystąpienia zaplecza, która służy do żądania. |
|failedRequestCount| Liczba żądań zakończonych niepowodzeniem.|
|Przepływność| Średnia przepustowość od czasu ostatniego dziennika, mierzony w bajtach na sekundę.|

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
> Czas oczekiwania jest obliczana na podstawie czasu, po odebraniu pierwszy bajt żądania HTTP do momentu wysłania ostatniego bajtu odpowiedzi HTTP. Jest to suma czasu przetwarzania bramy aplikacji oraz koszty sieci wewnętrznej, a także czas, jaki wewnętrznej potrzebny do przetwarzania żądania.

### <a name="firewall-log"></a>Dziennik zapory

Dziennik zapory jest generowany tylko wtedy, gdy włączono dla każdej bramy aplikacji, zgodnie z opisem w poprzedniej procedurze. Ten dziennik wymaga również, czy Zapora aplikacji sieci web jest skonfigurowany dla bramy aplikacji. Dane są przechowywane w określonej po włączeniu rejestrowania konta magazynu. Rejestrowane są następujące dane:


|Wartość  |Opis  |
|---------|---------|
|instanceId     | Zapory, które dane są generowane wystąpienia bramy aplikacji. Brama aplikacji w wielu wystąpień jest jeden wiersz dla każdego wystąpienia.         |
|clientIp     |   Źródłowy adres IP dla żądania.      |
|clientPort     |  Port źródłowy dla żądania.       |
|requestUri     | Adres URL odebrane żądanie.       |
|ruleSetType     | Typ zestawu reguł. Dostępne wartości to OWASP.        |
|ruleSetVersion     | Wersja używanego zestawu reguł. Dostępne wartości to 2.2.9 i 3.0.     |
|ruleId     | Identyfikator reguły wyzwalająca zdarzenia.        |
|message     | Przyjazny komunikat wyzwalająca zdarzenia. Bardziej szczegółowe informacje znajdują się w sekcji szczegółów.        |
|action     |  Działania podjęte w żądaniu. Dostępne wartości to zablokowany, a dozwolone.      |
|witryna     | Witryna, dla którego wygenerowano dziennika. Obecnie tylko Global jest na liście, ponieważ reguły są globalne.|
|szczegóły     | Szczegóły wyzwalająca zdarzenia.        |
|details.Message     | Opis reguły.        |
|details.data     | Znaleziono żądania, które pasowało reguły określone dane.         |
|details.file     | Plik konfiguracji zawiera reguły.        |
|details.line     | Numer wiersza w pliku konfiguracji, który wywołał zdarzenie.       |

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

Można wyświetlać i analizować dane dzienników działania przy użyciu dowolnej z następujących metod:

* **Narzędzia Azure**: pobieranie informacji z dziennika aktywności za pomocą programu Azure PowerShell, interfejsu wiersza polecenia Azure, interfejsu API REST Azure lub portalu Azure. Instrukcje krok po kroku dla każdej metody wyszczególnione w [operacji działania za pomocą Menedżera zasobów](../azure-resource-manager/resource-group-audit.md) artykułu.
* **Power BI**: Jeśli nie masz jeszcze [usługi Power BI](https://powerbi.microsoft.com/pricing) konta, możesz spróbować ją bezpłatnie. Za pomocą [Dzienniki aktywności Azure zawartości pakietu dla usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), można analizować danych za pomocą wstępnie skonfigurowanych pulpity nawigacyjne, które można użyć jako jest lub dostosować je.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Wyświetlanie i analizowanie dostępu, wydajności i dzienniki zapory

Azure [analizy dzienników](../log-analytics/log-analytics-azure-networking-analytics.md) może zbierać pliki dziennika zdarzeń i liczników z konta magazynu obiektów Blob. Obejmuje on wizualizacji oraz możliwości wyszukiwania zaawansowanego do analizowania dzienników.

Można również nawiązać połączenia z kontem magazynu i pobrać JSON wpisów dziennika dla dzienników dostępu i wydajności. Po pobraniu pliki w formacie JSON można przekonwertować je do pliku CSV i wyświetlić je w programie Excel, usłudze Power BI lub innych narzędzi wizualizacji danych.

> [!TIP]
> Jeśli znasz podstawowe koncepcje zmiany wartości stałych i zmiennych w języku C# i Visual Studio, możesz użyć [dziennika narzędzia konwertera](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępne w serwisie GitHub.
> 
> 

## <a name="metrics"></a>Metryki

Metryki są funkcją dla niektórych zasobów platformy Azure, w którym liczniki wydajności można przeglądać w portalu. Brama aplikacji dostępne są następujące metryki:

- **Bieżąca liczba połączeń**
- **Żądań zakończonych niepowodzeniem**
- **Liczba hosta dobrej kondycji**

   Można filtrować według poszczególnych puli wewnętrznej bazy danych do wyświetlenia w puli zaplecza określonych dobrej kondycji/zła hostów.


- **Stan odpowiedzi**

   Dystrybucji kod stanu odpowiedzi można następnie podzielone do wyświetlenia odpowiedzi na 2xx, 3xx 4xx i 5xx kategorii.

- **Przepływność**
- **Całkowita liczba żądań**
- **Zła liczba hosta**

   Można filtrować według poszczególnych puli wewnętrznej bazy danych do wyświetlenia w puli zaplecza określonych dobrej kondycji/zła hostów.

Przejdź do bramy aplikacji w obszarze **monitorowanie** kliknij **metryki**. Zaznacz, aby wyświetlić dostępne wartości **METRYKA** listy rozwijanej.

Na poniższej ilustracji Zobacz przykład z trzech metryki wyświetlane w ciągu ostatnich 30 minut:

[![](media/application-gateway-diagnostics/figure5.png "Metryki widoku")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Aby wyświetlić bieżącą listę metryki, zobacz [obsługiwane metryki z monitorem Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Reguły alertów

Można uruchomić reguły alertów w oparciu metryki dla zasobu. Na przykład alert można wywołać elementu webhook lub wiadomości e-mail administratora, jeśli przepływność bramy aplikacji jest powyżej, poniżej lub w wartości progowej przez określony czas.

Poniższy przykład przeprowadzi Cię przez proces tworzenia reguły alertu, który wysyła wiadomość e-mail do administratora po naruszeń przepływność a próg:

1. Kliknij przycisk **Dodaj alert metryki** otworzyć **Dodaj regułę** bloku. Zapewnia także łączność tego bloku z bloku metryki.

   ![Przycisk "Dodaj metryki alert"][6]

2. Na **Dodaj regułę** bloku, wypełnij nazwę warunku, powiadom sekcje i kliknij przycisk **OK**.

   * W **warunku** selektora, wybierz jedną z czterech wartości: **większe**, **większy lub równy**, **mniej niż**, lub **Mniejsze niż lub równe**.

   * W **okres** selektora, wybierz okres od 5 do sześciu godzin.

   * W przypadku wybrania **E-mail właściciele, współautorzy i czytelnicy**, wiadomości e-mail może być dynamiczny oparta na użytkownikach, którzy mają dostęp do tego zasobu. W przeciwnym razie możesz podać rozdzielana przecinkami lista użytkowników w **email(s) dodatkowe administratora** pole.

   ![Dodaj regułę bloku][7]

W przypadku naruszenia progu dociera do wiadomości e-mail, który jest podobny do przedstawionego na poniższej ilustracji:

![Wiadomości e-mail w przypadku naruszenia progu][8]

Po utworzeniu metryki alert zostanie wyświetlona lista alertów. Zapewnia przegląd wszystkich reguł alertów.

![Lista alertów i reguł][9]

Aby dowiedzieć się więcej na temat powiadomień o alertach, zobacz [otrzymywać powiadomienia o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Aby dowiedzieć się więcej o elementów webhook i sposobie ich użycia z alertami, odwiedź stronę [skonfigurować elementu webhook na alert metryki Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="next-steps"></a>Kolejne kroki

* Wizualizuj w dziennikach zdarzeń i liczników przy użyciu [analizy dzienników](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Wizualizuj dziennik aktywności platformy Azure z usługi Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) wpis w blogu.
* [Przeglądać i analizować Dzienniki aktywności platformy Azure w usłudze Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) wpis w blogu.

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
