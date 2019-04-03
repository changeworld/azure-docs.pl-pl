---
title: Monitorowanie wydajności usługi aplikacji Azure | Dokumentacja firmy Microsoft
description: Monitorowanie wydajności aplikacji dla usług Azure app services. Wykresu, obciążenia i czas odpowiedzi, informacje o zależnościach oraz ustawianie alertów dotyczących wydajności.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 9d121146924eb153227e35d608a3c6c33aae31a1
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862611"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorowanie wydajności w usłudze Azure App Service

Włączanie monitorowania w .NET i .NET Core oparte na sieci web działających na platformie Azure App Services jest teraz łatwiejsze niż kiedykolwiek wcześniej. Potrzebne wcześniej ręcznie zainstalować rozszerzenie witryny najnowsze rozszerzenia/agent jest obecnie wbudowany w obraz usługi aplikacji domyślnie. Ten artykuł będzie przeprowadzą Cię przez włączanie monitorowania usługi Application Insights także zawierają wstępne wskazówki dotyczące automatyzacji procesu w przypadku wdrożeń na dużą skalę.

> [!NOTE]
> Ręczne dodanie rozszerzenia witryny usługi Application Insights za pośrednictwem **narzędzia programistyczne** > **rozszerzenia** jest przestarzała. Najnowsza wersja stabilna rozszerzenia jest teraz [wstępnie](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako część obrazu usługi App Service. Pliki znajdują się w `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` i są automatycznie aktualizowane wraz z każdą wersją stabilne. Jeśli postępuj zgodnie z instrukcjami agenta na podstawie, aby włączyć monitorowanie poniżej go automatycznie usunie przestarzałe rozszerzenia dla Ciebie.

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Istnieją dwa sposoby, aby włączyć monitorowanie aplikacji dla aplikacji hostowanych usług aplikacji platformy Azure:

* **Monitorowanie aplikacji opartej o agentów** (ApplicationInsightsAgent).  
    * Ta metoda jest najłatwiejszym włączyć, a nie jest wymagana żadna konfiguracja zaawansowanych. Go często nazywa się "środowiska uruchomieniowego" monitorowania. Dla usług Azure App Service zalecamy co najmniej włączania monitorowania na tym poziomie i następnie oparte na Twoje konkretnego scenariusza, który będziesz w stanie ocenić, czy wymagane jest bardziej zaawansowane monitorowanie za pomocą ręcznej instrumentacji.

* **Ręcznej instrumentacji aplikacji za pomocą kodu** przez zainstalowanie zestawu SDK usługi Application Insights.

    * To podejście jest bardziej można dostosowywać, ale wymaga [Dodawanie zależności w pakietach NuGet zestawu SDK usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Ta metoda również oznacza, że trzeba zarządzać samodzielnie aktualizacji do najnowszej wersji pakietów.

    * Jeśli potrzebujesz wykonywania niestandardowych wywołań interfejsu API do zdarzenia śledzenia/zależności nie są przechwytywane domyślnie opartej o agentów monitorowania, będziesz potrzebować do używania tej metody. Zapoznaj się z [interfejsu API dla niestandardowych zdarzeń i metryk artykułu](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) Aby dowiedzieć się więcej.

> [!NOTE]
> Jeśli zarówno na podstawie monitorowania agentów, jak i ręczne zestawu SDK na podstawie Instrumentacji wykrycia tylko ustawienia ręcznej Instrumentacji będą uznawane. Pozwala to uniknąć zduplikowanych danych z wysyłane. Aby dowiedzieć się więcej o tym wyewidencjonowanie [Rozwiązywanie problemów z sekcji](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) poniżej.

## <a name="enable-agent-based-monitoring-net"></a>Włącz opartej o agentów monitorowania .NET

1. **Wybierz usługę Application Insights** w Panelu sterowania platformy Azure dla usługi app service.

    ![W obszarze Ustawienia wybierz pozycję Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * O ile nie jest już skonfigurowany zasób usługi Application Insights dla tej aplikacji, należy wybrać opcję utworzenia nowego zasobu. 

     > [!NOTE]
     > Po kliknięciu **OK** do utworzenia nowego zasobu, zostanie wyświetlony monit do **Zastosuj ustawienia monitorowania**. Wybieranie **Kontynuuj** połączy nowy zasób usługi Application Insights do usługi app service, wykonując będzie również **wyzwolić ponowne uruchomienie usługi app service**. 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource-01.png)

2. Po określenie zasobu do użycia, możesz wybrać sposób usługi application insights do zbierania danych dla danej platformy dla aplikacji. Monitorowanie aplikacji ASP.NET jest na domyślnie z dwóch różnych poziomach w kolekcji.

    ![Wybierz opcje dla danej platformy](./media/azure-web-apps/choose-options-new.png)

   * .NET **podstawowe kolekcji** poziom oferuje podstawowe funkcje APM jednego wystąpienia.

   * .NET **zalecane kolekcji** poziom:
       * Dodaje trendy użycia procesora CPU, pamięci i operacji We/Wy.
       * Koreluje mikrousługi w granicach żądania/zależności.
       * Zbiera dane trendów użycia i umożliwia korelacji z wyniki dostępności transakcji.
       * Gromadzi informacje o wyjątki nieobsługiwane przez proces hosta.
       * Zwiększa dokładność metryki APM pod obciążeniem, pobieranie próbek jest używany.

3. Aby skonfigurować ustawienia, takie jak pobieranie próbek, które możesz wcześniej można kontrolować za pomocą pliku applicationinsights.config mogą teraz współdziałać z tych tych samych ustawień za pomocą ustawień aplikacji za pomocą odpowiedniego prefiksu. 

    * Na przykład, aby zmienić wartości procentowej próbkowania początkowej, można utworzyć ustawienie aplikacji o: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` oraz wartość `100`.

    * Listę obsługiwanych próbkowanie adaptacyjne telemetrią procesora, należy zapoznać się [kodu](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) i [powiązana dokumentacja](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Włącz opartej o agentów monitorowania .NET Core

Obsługiwane są następujące wersje programu .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Przeznaczanie dla pełnej platformy .NET Core, niezależna wdrożenia i ASP.NET Core 3.0 są obecnie **nieobsługiwane** z monitorowaniem agenta i rozszerzeń na podstawie. ([Ręcznej Instrumentacji](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) kod będzie działać we wszystkich poprzednich scenariuszach.)

1. **Wybierz usługę Application Insights** w Panelu sterowania platformy Azure dla usługi app service.

    ![W obszarze Ustawienia wybierz pozycję Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * O ile nie jest już skonfigurowany zasób usługi Application Insights dla tej aplikacji, należy wybrać opcję utworzenia nowego zasobu. 

     > [!NOTE]
     > Po kliknięciu **OK** do utworzenia nowego zasobu, zostanie wyświetlony monit do **Zastosuj ustawienia monitorowania**. Wybieranie **Kontynuuj** połączy nowy zasób usługi Application Insights do usługi app service, wykonując będzie również **wyzwolić ponowne uruchomienie usługi app service**. 

     ![Instrumentacja aplikacji internetowej](./media/azure-web-apps/create-resource-01.png)

2. Po określenie zasobu do użycia, możesz wybrać sposób usługi Application Insights do zbierania danych dla danej platformy dla aplikacji. .NET core oferuje **zalecane kolekcji** lub **wyłączone** dla platformy .NET Core 2.0, 2.1 i 2.2.

    ![Wybierz opcje dla danej platformy](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Włącz platformę .NET monitorowania po stronie klienta

Monitorowanie po stronie klienta jest zoptymalizowany pod kątem programu ASP.NET. Aby włączyć monitorowanie po stronie klienta:

* Wybierz **ustawienia** > ** ** Ustawienia aplikacji ***
   * W obszarze Ustawienia aplikacji Dodaj nową **Nazwa ustawienia aplikacji** i **wartość**:

     Nazwa: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość: `true`

   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

![Zrzut ekranu przedstawiający aplikację ustawienia interfejsu użytkownika](./media/azure-web-apps/appinsights-javascript-enabled.png)

Aby wyłączyć monitorowania po stronie klienta albo usuń skojarzone parę klucz-wartość w ustawieniach aplikacji, lub ustaw wartość false.

## <a name="enable-client-side-monitoring-net-core"></a>Włączanie monitorowania po stronie klienta platformy .NET Core

Monitorowanie po stronie klienta jest **domyślnie** dla aplikacji platformy .NET Core za pomocą **zalecane kolekcji**, niezależnie od tego, czy ma ustawienie "APPINSIGHTS_JAVASCRIPT_ENABLED" aplikacji.

Jeśli z jakiegoś powodu chcesz wyłączyć monitorowanie po stronie klienta:

* Wybierz **ustawienia** > **ustawień aplikacji**
   * W obszarze Ustawienia aplikacji Dodaj nową **Nazwa ustawienia aplikacji** i **wartość**:

     Nazwa: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Wartość: `false`

   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.

![Zrzut ekranu przedstawiający aplikację ustawienia interfejsu użytkownika](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatyzowanie monitorowania

Aby włączyć zbieranie danych telemetrycznych z usługi Application Insights, należy skonfigurować tylko ustawienia aplikacji:

   ![Ustawienia aplikacji usługi App z dostępnymi ustawieniami usługi Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definicje ustawień aplikacji

|Nazwa ustawienia aplikacji |  Definicja | Wartość |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Rozszerzenie głównego, który kontroluje, monitorowanie w czasie wykonywania. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  W domyślnym trybie tylko podstawowych funkcji są włączone w celu uzyskania optymalnej wydajności upewnij się. | `default` lub `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Kontroluje, czy aparat ponownego zapisywania danych binarnych `InstrumentationEngine` zostanie włączona. To ustawienie ma wpływ na wydajność i ma wpływ na czas zimny start/uruchamiania. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Określa, czy SQL i Azure table tekstu będzie można przechwycić wraz z wywołania zależności. Ostrzeżenie dotyczące wydajności: to ustawienie wymaga `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Ustawienia aplikacji usługi aplikacji za pomocą usługi Azure Resource Manager

Zarządzane i skonfigurowano ustawienia aplikacji dla usług App Services [szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Ta metoda może służyć w przypadku wdrażania nowych zasobów usługi App Service z usługą automation usługi Azure Resource Manager lub modyfikując ustawienia istniejących zasobów.

Podstawowa struktura ustawienia aplikacji JSON dla usługi app service znajduje się poniżej:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Na przykład szablonu usługi Azure Resource Manager za pomocą ustawień aplikacji skonfigurowany dla usługi Application Insights to [szablonu](https://github.com/Andrew-MSFT/BasicImageGallery) mogą być przydatne, w szczególności sekcji od [wiersz 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Aby zautomatyzować tworzenie zasobu usługi Application Insights i łącza do nowo utworzonej usługi aplikacji.

Aby utworzyć szablon usługi Azure Resource Manager z wszystkie usługi Application Insights ustawień domyślnych, rozpoczęcie procesu tak, jakby miało Tworzenie nowej aplikacji sieci Web za pomocą usługi Application Insights włączone.

Wybierz **opcje automatyzacji**

   ![Menu tworzenia aplikacji sieci web usługi App Service](./media/azure-web-apps/create-web-app.png)

Ta opcja powoduje wygenerowanie najnowszy szablon usługi Azure Resource Manager przy użyciu wszystkich wymaganych ustawień skonfigurowane.

  ![Szablon aplikacji sieci web usługi App Service](./media/azure-web-apps/arm-template.png)

Poniżej przedstawiono przykład, zastąpić wszystkie wystąpienia `AppMonitoredSite` z nazwą Twojej witryny:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Szablon spowoduje wygenerowanie ustawienia aplikacji w trybie "default". Ten tryb jest zoptymalizowane pod kątem, wydajności, chociaż można zmodyfikować szablon, aby aktywować niezależnie od funkcji użytkownik sobie tego życzy.

### <a name="enabling-through-powershell"></a>Włączanie za pomocą programu PowerShell

Aby włączyć monitorowania za pomocą programu PowerShell aplikacji, tylko podstawowe ustawienia aplikacji muszą zostać zmienione. Poniżej znajduje się przykład, która umożliwia monitorowanie aplikacji dla witryny sieci Web o nazwie "AppMonitoredSite" w grupie zasobów "AppMonitoredRG", i konfiguruje dane do wysłania do klucza Instrumentacji "012345678-abcd-ef01-2345-6789abcd".

```powershell
$app = Get-AzureRmWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzureRmWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Uaktualnij rozszerzenie/agenta monitorowania

### <a name="upgrading-from-versions-289-and-up"></a>Uaktualnienie z wersji 2.8.9 oraz jego nowszymi wersjami

Uaktualnienie z wersji 2.8.9 odbywa się automatycznie, bez żadnych dodatkowych akcji. Nowe monitorowania bity są dostarczane w tle do usługi aplikacji docelowej, a na ponowne uruchamianie aplikacji będzie zostać pobrana.

Aby sprawdzić wersję rozszerzenia, które są uruchomione wizyty `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Zrzut ekranu przedstawiający Ścieżka adresu url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uaktualnienie z wersji 1.0.0 - 2.6.5

Począwszy od wersji 2.8.9 jest używane rozszerzenie wstępnie zainstalowanej lokacji. Jeśli masz wcześniejszą wersję, można zaktualizować przy użyciu jednej z dwóch sposobów:

* [Uaktualnienia, należy włączyć za pośrednictwem portalu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Nawet jeśli masz rozszerzenia usługi Application Insights dla usługi Azure App Service zainstalowany interfejs użytkownika zawiera tylko **Włącz** przycisku. Za kulisami stare rozszerzenia witryny prywatnej zostaną usunięte.)

* [Uaktualnianie za pomocą programu PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Skonfiguruj ustawienia aplikacji, aby włączyć rozszerzenie ApplicationInsightsAgent wstępnie zainstalowanej lokacji. Zobacz [Włączanie za pomocą programu powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ręcznie usuń rozszerzenie prywatnej witryny o nazwie rozszerzenia usługi Application Insights dla usługi Azure App Service.

Jeśli uaktualnienie odbywa się w wersji wcześniejszej niż 2.5.1, sprawdź, czy biblioteki DLL ApplicationInsigths zostały usunięte z folderu bin aplikacji [zobacz kroki rozwiązywania problemów](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej znajduje się nasze krok po kroku przewodnik rozwiązywania problemów na podstawie rozszerzenia/agent monitorowania dla platformy .NET i .NET Core oparte aplikacji działających w usłudze Azure App Services.

> [!NOTE]
> Aplikacji języka Java i Node.js są obsługiwane tylko w usłudze Azure App Services za pomocą ręcznej Instrumentacji na podstawie zestawu SDK i dlatego poniższe kroki nie dotyczą tych scenariuszy.

1. Upewnij się, że aplikacja jest monitorowany za pośrednictwem `ApplicationInsightsAgent`.
    * Sprawdź, czy `ApplicationInsightsAgent_EXTENSION_VERSION` ustawienia aplikacji jest ustawiona na wartość "~ 2".
2. Upewnij się, że aplikacja spełnia wymagania, które mają być monitorowane.
    * Przejdź do `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Zrzut ekranu przedstawiający https://yoursitename.scm.azurewebsites/applicationinsights strony wyników](./media/azure-web-apps/app-insights-sdk-status.png)

    * Upewnij się, że `Application Insights Extension Status` jest `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Jeśli nie jest uruchomiona, wykonaj [Włącz instrukcje dotyczące monitorowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Upewnij się, że stan istnieje i wygląda następująco: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Jeśli podobną wartość nie jest obecny, oznacza to, aplikacja nie jest obecnie uruchomiony lub nie jest obsługiwany. Aby upewnić się, że aplikacja jest uruchomiona, spróbuj ręcznie odwiedzający aplikacji adresu url/aplikacji punktów końcowych, co umożliwi informacje środowiska uruchomieniowego, które staną się dostępne.

    * Upewnij się, że `IKeyExists` jest `true`
        * Jeśli jest to wartość false, należy dodać "APPINSIGHTS_INSTRUMENTATIONKEY za pomocą usługi guid klucz Instrumentacji w ustawieniach Twojej aplikacji.

    * Upewnij się, że nie ma żadnych wpisów dla `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, i `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Jeśli istnieje dowolne z tych wpisów, należy usunąć następujące pakiety z aplikacji: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, i `Microsoft.AspNet.TelemetryCorrelation`.

W poniższej tabeli przedstawiono bardziej szczegółowe wyjaśnienie, co oznaczają te wartości, powoduje, że ich bazowe i zalecane poprawki:

|Wartość problem|Wyjaśnienie|Napraw
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ta wartość wskazuje, że rozszerzenie wykryła, że niektóre aspekty zestawu SDK znajduje się już w aplikacji będą wycofywania. Można go ze względu na odwołanie do `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, lub `Microsoft.ApplicationInsights`  | Usuń odwołania. Niektóre z tych odwołań są domyślnie dodawane od pewnych szablonów programu Visual Studio i starszych wersji programu Visual Studio może dodać odwołania do `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Ta wartość również może być spowodowane obecności powyższych bibliotek DLL w folderze aplikacji z poprzedniego wdrożenia. | Wyczyść folderu aplikacji, aby upewnić się, że te biblioteki DLL są usuwane.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ta wartość wskazuje, że rozszerzenie wykryto odwołania do `Microsoft.AspNet.TelemetryCorrelation` w aplikacji i będzie wycofywania. | Usuń odwołanie.
|`AppContainsDiagnosticSourceAssembly**:true`|Ta wartość wskazuje, że rozszerzenie wykryto odwołania do `System.Diagnostics.DiagnosticSource` w aplikacji i będzie wycofywania.| Usuń odwołanie.
|`IKeyExists:false`|Ta wartość wskazuje, że klucz Instrumentacji nie jest obecny w AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`. Możliwe przyczyny: Wartości mogą przypadkowo usunięte, to, że nie można ustawić wartości w skrypt automatyzacji itp. | Upewnij się, że to ustawienie znajduje się w ustawieniach aplikacji w usłudze App Service.

Aby uzyskać najnowsze informacje dotyczące agenta/rozszerzenie usługi Application Insights, zapoznaj się [informacje o wersji](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Kolejne kroki
* [Uruchom profilera aplikacji na żywo](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) — monitorowanie usługi Azure Functions za pomocą usługi Application Insights
* [Włącz diagnostykę platformy Azure](../platform/diagnostics-extension-to-application-insights.md), która ma być wysyłana do usługi Application Insights.
* [Monitoruj metryki kondycji usługi](../platform/data-platform.md), aby upewnić się, że usługa jest dostępna i szybko reaguje.
* [Odbieraj powiadomienia o alertach](../platform/alerts-overview.md) zawsze, gdy wystąpią zdarzenia operacyjne lub metryki przekroczą próg.
* Użyj pozycji [Usługa Application Insights dla aplikacji JavaScript i stron sieci Web](javascript.md), aby pobrać telemetrię klienta z przeglądarek, w których odwiedzono stronę sieci Web.
* [Konfiguruj testy sieci Web dostępności](monitor-web-app-availability.md), aby otrzymywać alerty, gdy witryna nie działa.
