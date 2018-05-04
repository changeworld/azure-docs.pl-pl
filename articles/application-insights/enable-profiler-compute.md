---
title: Włącz Application Insights profilera dla aplikacji, które znajdują się w zasobach usług Azure Cloud Services | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania profilera Insights aplikacji w aplikacji działających na usługi w chmurze Azure.
services: application-insights
documentationcenter: ''
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: ef6d8cb47971b1cf261edceb13485b940eff3d13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Włącz Application Insights profilera dla maszyn wirtualnych platformy Azure, usługi Service Fabric i usług w chmurze Azure

W tym artykule przedstawiono sposób włączania usługi Azure Application Insights profilera w aplikacji ASP.NET, która jest obsługiwana przez zasób usługi w chmurze Azure.

W tym artykule przykładami pomocy technicznej dla maszyny wirtualnej platformy Azure, zestawy skalowania maszyny wirtualnej, sieć szkieletowa usług Azure i usługi w chmurze Azure. Przykłady polegać na szablony, które obsługują [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelu wdrażania.  


## <a name="overview"></a>Przegląd

Na poniższej ilustracji przedstawiono, jak działa Application Insights profilera z aplikacjami, które znajdują się na zasoby usługi w chmurze Azure. Zasoby usługi w chmurze platformy Azure obejmują maszyn wirtualnych, zestawy skalowania usługi w chmurze i klastrów sieci szkieletowej usług. Jako przykład obrazu korzysta z maszyny wirtualnej platformy Azure.  

  ![Diagram pokazujący, jak działa Application Insights profilera z zasobami usługi w chmurze Azure](./media/enable-profiler-compute/overview.png)

Aby w pełni włączyć profilera, należy zmienić konfigurację w trzech miejscach:

* W okienku wystąpienia usługi Application Insights w portalu Azure.
* Kod źródłowy aplikacji (na przykład aplikacja sieci web ASP.NET).
* Środowisko wdrażania definicji kodu źródłowego (na przykład szablonu usługi Azure Resource Manager w pliku JSON).


## <a name="set-up-the-application-insights-instance"></a>Skonfiguruj wystąpienie usługi Application Insights

1. [Utwórz nowy zasób usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource), lub wybierz istniejący. 

2. Przejdź do zasobu usługi Application Insights, a następnie skopiuj klucz instrumentacji.

   ![Lokalizacja klucza Instrumentacji](./media/enable-profiler-compute/CopyAIKey.png)

3. Aby zakończyć konfigurowanie wystąpienie usługi Application Insights dla profilera, wykonaj procedurę opisaną w [Włącz profilera. Nie musisz połączyć aplikacji sieci web, ponieważ kroki są specyficzne dla zasobów usługi aplikacji. Upewnij się, że Profiler jest włączona w **skonfiguruj profilera** okienka.


## <a name="set-up-the-application-source-code"></a>Konfigurowanie kodu źródłowego aplikacji

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Aplikacje sieci web ASP.NET, role sieci web usługi w chmurze Azure lub frontonu sieci web platformy ASP.NET sieci szkieletowej usług
Konfigurowanie aplikacji do wysyłania danych telemetrycznych do wystąpienia usługi Application Insights w każdym `Request` operacji.  

Dodaj [zestaw SDK usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) projekt aplikacji. Upewnij się, że wersje pakietów NuGet są następujące:  
  - Dla aplikacji ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 lub nowszym.
  - Dla aplikacji platformy ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 lub nowszym.
  - Dla innych aplikacji .NET i .NET Core (na przykład usługi bezstanowej sieci szkieletowej usług lub roli procesu roboczego usługi w chmurze): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) lub [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 lub nowszej.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Role procesów roboczych usługi w chmurze platformy Azure lub usługi sieć szkieletowa usług bezstanowych wewnętrznej
Oprócz zakończeniu poprzedni krok, jeśli aplikacja jest *nie* aplikacji ASP.NET lub ASP.NET Core (na przykład, jeśli jest roli proces roboczy usług Azure Cloud Services lub bezstanowych interfejsów API usługi Service Fabric), wykonaj następujące czynności:  

  1. Na początku istnienia aplikacji Dodaj następujący kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Aby uzyskać więcej informacji na temat tej konfiguracji klucza Instrumentacji globalnych, zobacz [sieci szkieletowej usług użycia z usługą Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. Dla dowolnego elementu kodu, który ma zostać Instrumentacja, Dodaj `StartOperation<RequestTelemetry>` **USING** instrukcji wokół niego, jak pokazano w poniższym przykładzie:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Wywoływanie `StartOperation<RequestTelemetry>` w innym `StartOperation<RequestTelemetry>` zakres nie jest obsługiwany. Można użyć `StartOperation<DependencyTelemetry>` w zagnieżdżonych zamiast tego zakresu. Na przykład:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie środowiska definicji wdrożenia

Środowisko, w którym profilera i wykonywanie Twojej aplikacji mogą być maszynę wirtualną, zestaw skali maszyny wirtualnej, klaster sieci szkieletowej usług lub wystąpienie usług w chmurze.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Maszyny wirtualne, zestawy skalowania lub sieci szkieletowej usług

Pełna przykłady zobacz:  
  * [Maszyny wirtualne](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Zestaw skali maszyny wirtualnej](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Klaster sieci szkieletowej usług](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Aby skonfigurować środowisko, wykonaj następujące czynności:
1. Aby upewnić się, że używasz [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub później, wystarczy upewnić się, że jest wdrożony system operacyjny `Windows Server 2012 R2` lub nowszym.

2. Wyszukaj [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozszerzenia w szablonie wdrożenia pliku, a następnie dodaj następujące `SinksConfig` sekcji jako element podrzędny elementu `WadCfg`. Zastąp `ApplicationInsightsProfiler` wartości właściwości z własnego klucza Instrumentacji usługi Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Uzyskać informacji na temat dodawania rozszerzenia diagnostyki w szablonie wdrożenia, zobacz [Użyj monitorowania i diagnostyki z szablonów maszyny Wirtualnej systemu Windows i usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Dla maszyn wirtualnych ma Przejdź w portalu Azure, aby zamiast powyższe kroki json na podstawie **maszyn wirtualnych** > **ustawień diagnostycznych**  >   **Wychwytywanie** > Set Wyślij dane diagnostyczne do usługi Application Insights do **włączone** i wybierz konto usługi Application Insights lub określonych ikey.

### <a name="azure-cloud-services"></a>usług Azure Cloud Services

1. Aby upewnić się, że używasz [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub później, jest wystarczające, aby potwierdzić, że *element ServiceConfiguration.\*. cscfg* pliki mają `osFamily` wartość "5" lub nowszej.

2. Zlokalizuj [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* plików dla roli użytkownika aplikacji, jak pokazano poniżej:  

   ![Lokalizacja pliku konfiguracji diagnostyki](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Jeśli nie można odnaleźć pliku, aby dowiedzieć się, jak włączyć rozszerzenia diagnostyki w projekcie usługi w chmurze Azure, zobacz [skonfigurować diagnostykę dla usług Azure Cloud Services i maszyn wirtualnych](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Dodaj następujące `SinksConfig` sekcji jako element podrzędny elementu `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

> [!NOTE]  
> Jeśli *diagnostics.wadcfgx* plik zawiera także zbiornika innego typu `ApplicationInsights`, wszystkie trzy następujące klucze Instrumentacji musi odpowiadać:  
>  * Klucz, który jest używany przez aplikację.  
>  * Klucz, który jest używany przez `ApplicationInsights` ujścia.  
>  * Klucz, który jest używany przez `ApplicationInsightsProfiler` ujścia.  
>
> Można znaleźć używanym przez wartość klucza Instrumentacji rzeczywiste `ApplicationInsights` sink *element ServiceConfiguration.\*. cscfg* plików.  
> Po wydaniu programu Visual Studio 15,5 cala Azure SDK, klucze instrumentacji, które są używane przez aplikację i `ApplicationInsightsProfiler` potrzebę zbiornika pasują do siebie.


## <a name="configure-environment-deployment-and-runtime"></a>Skonfiguruj środowisko wdrażania i środowiska wykonawczego

1. Wdrożenia zmodyfikowane środowiska definicji wdrożenia.  

   Aby zastosować te zmiany zwykle obejmuje pełny szablon wdrożenia lub chmurę usługi na podstawie opublikowane za pomocą poleceń cmdlet programu PowerShell lub programu Visual Studio.  

   Poniżej znajduje się alternatywne metody dla istniejących maszyn wirtualnych, która go dotyka tylko rozszerzenia diagnostyki Azure:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Jeśli aplikację zamierzone działa za pośrednictwem [IIS](https://www.microsoft.com/web/platform/server.aspx), Włącz `IIS Http Tracing` funkcji systemu Windows, wykonując następujące czynności:  

   a. Ustanowić zdalnego dostępu do środowiska, a następnie użyj [Windows Dodaj funkcje]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna lub uruchom następujące polecenie w programie PowerShell (Administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ustanawianie dostęp zdalny to problem, można użyć [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) do uruchom następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Włącz profilera na serwerach lokalnych

Włączanie profilera na lokalnym serwerze jest nazywana uruchomionej aplikacji profilera wgląd w trybie autonomicznym. Nie jest związana z diagnostyki Azure rozszerzenia modyfikacje.

Nie mamy żadnych planu świadczył oficjalną pomoc techniczną profilera dla serwerów lokalnych. Jeśli interesuje Cię eksperymentowanie z tego scenariusza, możesz [Pobierz kod obsługi](https://github.com/ramach-msft/AIProfiler-Standalone). Możemy *nie* odpowiedzialna za utrzymanie kodu lub reagowania na problemy i żądania funkcji, które są powiązane z kodem.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (na przykład uruchomić [test dostępności](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Następnie zaczekaj 10 – 15 minut na śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady profilera](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) w portalu Azure.
- Uzyskaj pomoc dotyczącą rozwiązywania problemów profilera w [profilera Rozwiązywanie problemów z](app-insights-profiler.md#troubleshooting).
- Dowiedz się więcej o profilera w [Application Insights profilera](app-insights-profiler.md).
