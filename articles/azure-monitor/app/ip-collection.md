---
title: Zbieranie adresów IP usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Opis sposobu obsługi adresów IP i geolokalizacji za pomocą usługi Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656521"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolokalizacja i obsługa adresów IP

W tym artykule wyjaśniono, jak w usłudze Application Insights występuje wyszukiwanie geolokalizacyjne i obsługa adresów IP, a także sposób modyfikowania zachowania domyślnego.

## <a name="default-behavior"></a>Zachowanie domyślne

Domyślnie adresy IP są tymczasowo zbierane, ale nie są przechowywane w usłudze Application Insights. Podstawowy proces przebiega w następujący sposób:

Adresy IP są wysyłane do usługi Application Insights jako część danych telemetrycznych. Po osiągnięciu punktu końcowego pozyskiwania na platformie Azure adres IP jest używany do wykonywania wyszukiwania geolokalizacji przy użyciu [GeoLite2 z MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Wyniki tego wyszukiwania są używane do wypełniania `client_City`następujących `client_StateOrProvince` `client_CountryOrRegion`pól , . W tym momencie adres IP `0.0.0.0` jest odrzucany `client_IP` i zapisywany w polu.

* Telemetria przeglądarki: tymczasowo zbieramy adres IP nadawcy. Adres IP jest obliczany przez punkt końcowy pozyskiwania.
* Telemetria serwera: Moduł usługi Application Insights tymczasowo zbiera adres IP klienta. Nie jest zbierany, jeśli `X-Forwarded-For` jest ustawiony.

To zachowanie jest zgodnie z projektem, aby uniknąć niepotrzebnego zbierania danych osobowych. W miarę możliwości zalecamy unikanie gromadzenia danych osobowych. 

## <a name="overriding-default-behavior"></a>Zastępowanie zachowania domyślnego

Chociaż domyślnym zachowaniem jest zminimalizowanie zbierania danych osobowych, nadal oferujemy elastyczność w zbieraniu i przechowywaniu danych adresów IP. Przed podjęciem decyzji o przechowywaniu jakichkolwiek danych osobowych, takich jak adresy IP, zdecydowanie zalecamy sprawdzenie, czy nie łamie to żadnych wymagań dotyczących zgodności ani lokalnych przepisów, na które możesz podlegać. Aby dowiedzieć się więcej o przetwarzaniu danych osobowych w usłudze Application Insights, zapoznaj się ze [wskazówkami dotyczącymi danych osobowych.](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)

## <a name="storing-ip-address-data"></a>Przechowywanie danych adresów IP

Aby włączyć zbieranie i przechowywanie `DisableIpMasking` adresów IP, właściwość składnika Usługi Application Insights musi być ustawiona na `true`. Tę właściwość można ustawić za pomocą szablonów usługi Azure Resource Manager lub wywołując interfejs API REST. 

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Jeśli trzeba tylko zmodyfikować zachowanie dla pojedynczego zasobu usługi Application Insights najprostszym sposobem, aby to osiągnąć jest za pośrednictwem witryny Azure portal.  

1. Przejdź do szablonu**eksportu** zasobów usługi Application Insights > **Ustawienia** >  

    ![Szablon eksportu](media/ip-collection/export-template.png)

2. Wybierz **pozycję Wdrażanie**

    ![Przycisk Wdrażanie wyróżniony na czerwono](media/ip-collection/deploy.png)

3. Wybierz **pozycję Edytuj szablon**. (Jeśli szablon ma dodatkowe właściwości lub zasoby, które nie są wyświetlane w tym przykładowym szablonie, należy zachować ostrożność, aby upewnić się, że wszystkie zasoby zaakceptują wdrożenie szablonu jako przyrostową zmianę/aktualizację).

    ![Edytowanie szablonu](media/ip-collection/edit-template.png)

4. Wykonuj następujące zmiany w zasobie json, a następnie kliknij przycisk **Zapisz:**

    ![Zrzut ekranu dodaje przecinek po "IbizaAIExtension" i dodać nowy wiersz poniżej z "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Jeśli wystąpi błąd ** _informujący: Grupa zasobów znajduje się w lokalizacji, która nie jest obsługiwana przez jeden lub więcej zasobów w szablonie. Wybierz inną grupę zasobów._** Tymczasowo wybierz inną grupę zasobów z listy rozwijanej, a następnie ponownie wybierz oryginalną grupę zasobów, aby rozwiązać ten błąd.

5. Wybierz **pozycję Zgadzam się** > **na zakup**. 

    ![Edytowanie szablonu](media/ip-collection/purchase.png)

    W tym przypadku nic nowego nie jest kupowany, jesteśmy po prostu aktualizowanie konfiguracji istniejącego zasobu usługi Application Insights.

6. Po zakończeniu wdrażania zostaną zarejestrowane nowe dane telemetryczne.

    Jeśli chcesz ponownie wybrać i edytować szablon, zobaczysz tylko domyślny szablon i nie zobaczysz nowo dodanej właściwości i skojarzonej z nią wartości. Jeśli nie widzisz danych adresu IP i `"DisableIpMasking": true` chcesz potwierdzić, że jest ustawiona. Uruchom następujący program PowerShell: (Zamień `Fabrikam-dev` odpowiednią nazwę zasób i grupę zasobów).
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    W rezultacie zostanie zwrócona lista właściwości. Jedna z właściwości powinna `DisableIpMasking: true`być odczytana . Jeśli uruchomisz program PowerShell przed wdrożeniem nowej właściwości za pomocą usługi Azure Resource Manager, właściwość nie będzie istnieć.

### <a name="rest-api"></a>Interfejs API odpoczynku

Ładunek [interfejsu API reszta,](https://docs.microsoft.com/rest/api/azure/) aby te same modyfikacje jest w następujący sposób:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicjator telemetrii

Jeśli potrzebujesz bardziej elastycznej `DisableIpMasking` alternatywy niż rejestrowanie wszystkich lub części adresów IP, można użyć [inicjatora telemetrii,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) aby skopiować całość lub część adresu IP do pola niestandardowego. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / Rdzeń ASP.NET

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Jeśli nie możesz `ISupportProperties`uzyskać dostępu, sprawdź i upewnij się, że jest uruchomiona najnowsza stabilna wersja SDK usługi Application Insights. `ISupportProperties`są przeznaczone dla wartości wysokiej `GlobalProperties` kardynalności, podczas gdy są bardziej odpowiednie dla wartości niskiej kardynalności, takich jak nazwa regionu, nazwa środowiska itp. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Włącz inicjatora telemetrii dla ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Włączanie inicjatora telemetrii dla ASP.NET Core

Inicjatora telemetrii można utworzyć w taki sam sposób dla ASP.NET Core jak ASP.NET ale aby włączyć inicjatora, należy użyć następującego przykładu dla odwołania:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>Język JavaScript po stronie klienta

W przeciwieństwie do sdk po stronie serwera, javascript javascript po stronie klienta nie oblicza adres IP. Domyślnie obliczanie adresu IP dla telemetrii po stronie klienta jest wykonywane w punkcie końcowym pozyskiwania na platformie Azure po przybyciu telemetrii. Oznacza to, że w przypadku wysyłania danych po stronie klienta do serwera proxy, a następnie przekazywania do punktu końcowego pozyskiwania, obliczanie adresu IP może wyświetlać adres IP serwera proxy, a nie klienta. Jeśli nie jest używany serwer proxy, nie powinno to być problemem.

Jeśli chcesz obliczyć adres IP bezpośrednio po stronie klienta, musisz dodać własną logikę niestandardową, `ai.location.ip` aby wykonać to obliczenie i użyć wyniku, aby ustawić tag. Po `ai.location.ip` ustawieniu obliczanie adresu IP nie jest wykonywane przez punkt końcowy pozyskiwania, a podany adres IP jest honorowany i używany do wykonywania wyszukiwania geograficznego. W tym scenariuszu adres IP będzie nadal wyzerowany domyślnie. 

Aby zachować cały adres IP obliczony na podstawie logiki niestandardowej, można użyć inicjatora telemetrii, który kopiuje dane adresu IP podane w `ai.location.ip` osobnym polu niestandardowym. Ale znowu w przeciwieństwie do sdk po stronie serwera, bez polegania na bibliotekach innych firm lub własnej niestandardowej logiki zbierania adresów IP po stronie klienta, sdk po stronie klienta nie obliczy adresu IP dla Ciebie.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Wyświetlanie wyników inicjatora telemetrii

Jeśli następnie wyzwolić nowy ruch w witrynie i odczekać około 2-5 minut, aby upewnić się, że miał czas do spożycia, można uruchomić zapytanie Kusto, aby sprawdzić, czy zbieranie adresów IP działa:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nowo zebrane adresy IP `customDimensions_client-ip` powinny pojawić się w kolumnie. Domyślna `client-ip` kolumna nadal będzie miała wszystkie 4 oktety wyzerowane lub wyświetla tylko pierwsze trzy oktety w zależności od sposobu skonfigurowania kolekcji adresów IP na poziomie składnika. Jeśli testujesz lokalnie po zaimplementowanie inicjatora telemetrii i wartość, którą widzisz, `customDimensions_client-ip` jest `::1` oczekiwanym zachowaniem. `::1`reprezentuje adres sprzężenia zwrotnego w IPv6. Jest odpowiednikiem `127.0.01` w IPv4 i jest wynikiem, który zobaczysz podczas testowania z localhost.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zbieraniu danych osobowych](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) w usłudze Application Insights.

* Dowiedz się więcej o tym, jak działa [zbieranie adresów IP](https://apmtips.com/blog/2016/07/05/client-ip-address/) w usłudze Application Insights. (Jest to starszy zewnętrzny wpis na blogu napisany przez jednego z naszych inżynierów. To poprzedza bieżące zachowanie domyślne, gdzie `0.0.0.0`adres IP jest rejestrowany jako , ale `ClientIpHeaderTelemetryInitializer`idzie w większą głębię na mechanikę wbudowanego .)
