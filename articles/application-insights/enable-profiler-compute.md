---
title: Włącz Application Insights Profiler dla aplikacji, które są hostowane na zasoby usług Azure Cloud Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować Application Insights Profiler na aplikację działającą w usługach Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2017
ms.reviewer: ramach
ms.author: mbullwin
ms.openlocfilehash: eb2ec0c0b77e71a54d1e7f852a22d82203abf7b6
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091984"
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Włącz Application Insights Profiler dla maszyn wirtualnych platformy Azure, Usługa Service Fabric i usługi Azure Cloud Services

W tym artykule przedstawiono sposób włączania usługi Azure Application Insights Profiler dla aplikacji ASP.NET, która jest obsługiwana przez zasób usługi Azure Cloud Services.

Przykłady w niniejszym artykule obejmują obsługę maszyn wirtualnych platformy Azure, zestawy skalowania maszyn wirtualnych, usługi Azure Service Fabric i usług Azure Cloud Services. Przykłady opierają się na szablonach, które obsługują [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelu wdrażania.  


## <a name="overview"></a>Przegląd

Na poniższej ilustracji przedstawiono, jak Application Insights Profiler działa z aplikacjami, które są hostowane na zasoby usług Azure Cloud Services. Zasoby usług w chmurze platformy Azure obejmują maszyny wirtualne, zestawy skalowania, usługi w chmurze i klastry usługi Service Fabric. Obraz, który używa maszynie wirtualnej platformy Azure jako przykładu.  

  ![Diagram przedstawiający, jak Application Insights Profiler działa z zasobami usług Azure Cloud Services](./media/enable-profiler-compute/overview.png)

Aby w pełni włączyć Profiler, należy zmienić konfigurację w trzech miejscach:

* W okienku wystąpienie usługi Application Insights w witrynie Azure portal.
* Kod źródłowy aplikacji (na przykład, aplikacja sieci web ASP.NET).
* Środowisko wdrażania definicji kodu źródłowego (na przykład szablonu usługi Azure Resource Manager w pliku JSON).


## <a name="set-up-the-application-insights-instance"></a>Skonfiguruj wystąpienie usługi Application Insights

1. [Utwórz nowy zasób usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource), lub wybierz istniejącą grupę. 

1. Przejdź do zasobu usługi Application Insights, a następnie skopiuj klucz instrumentacji.

   ![Lokalizacja klucza Instrumentacji](./media/enable-profiler-compute/CopyAIKey.png)

1. Aby zakończyć konfigurowanie wystąpienia usługi Application Insights Profiler, wykonaj procedurę opisaną w [Włącz Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler). Nie trzeba połączyć aplikacji sieci web, ponieważ kroki są specyficzne dla zasobów usługi aplikacji. Upewnij się, że Profiler jest włączone w **skonfigurować Profiler** okienka.


## <a name="set-up-the-application-source-code"></a>Konfigurowanie kodu aplikacji

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Aplikacje sieci web platformy ASP.NET, usług Azure Cloud Services role sieć web lub frontonu sieci web usługi Service Fabric ASP.NET
Konfigurowanie aplikacji do wysyłania danych telemetrycznych do wystąpienia usługi Application Insights na każdym `Request` operacji.  

Dodaj [zestawu SDK usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) projekt aplikacji. Upewnij się, że wersji pakietów NuGet w następujący sposób:  
  - Dla aplikacji ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 lub nowszej.
  - W przypadku aplikacji platformy ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 lub nowszej.
  - Dla innych aplikacji .NET i .NET Core (na przykład usługi bezstanowej usługi Service Fabric lub roli procesu roboczego usług Cloud Services): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) lub [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 lub nowszej.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Role proces roboczy w usłudze Azure Cloud Services lub usługi Service Fabric bezstanowe wewnętrznej bazy danych
Oprócz ukończenie poprzedniego kroku, jeśli aplikacja jest *nie* aplikacji ASP.NET lub ASP.NET Core (na przykład, jeśli rola procesu roboczego w usługach Azure Cloud Services lub interfejsów API usługi bezstanowej usługi Service Fabric), wykonaj następujące czynności:  

  1. Na wczesnym etapie istnienia aplikacji Dodaj następujący kod:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Aby uzyskać więcej informacji na temat tej konfiguracji klucza Instrumentacji globalne Zobacz [użycia usługi Service Fabric za pomocą usługi Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  1. Dla dowolnego fragmentu kodu, który ma być instrumentacji, należy dodać `StartOperation<RequestTelemetry>` **USING** instrukcji wokół niego, jak pokazano w poniższym przykładzie:

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

        Wywoływanie `StartOperation<RequestTelemetry>` w innym `StartOperation<RequestTelemetry>` zakres nie jest obsługiwany. Możesz użyć `StartOperation<DependencyTelemetry>` w zagnieżdżonej zamiast tego zakresu. Na przykład:  
        
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

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie definicji wdrożenia środowiska

Środowisko, w którym Profiler i wykonywania Twojej aplikacji mogą być maszyny wirtualnej, zestawu skalowania maszyn wirtualnych, klaster usługi Service Fabric lub wystąpienie usług w chmurze.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Maszyny wirtualne, zestawy skalowania lub usługi Service Fabric

Pełne przykłady zobacz:  
  * [Maszyna wirtualna](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Zestaw skalowania maszyn wirtualnych](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Klaster usługi Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Aby skonfigurować środowisko, wykonaj następujące czynności:
1. Aby upewnić się, że używasz [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszym będą wystarczające, aby potwierdzić, że wdrożonego systemu operacyjnego jest `Windows Server 2012 R2` lub nowszej.

1. Wyszukaj [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozszerzenia w szablonie wdrażania pliku, a następnie dodaj następujący kod `SinksConfig` sekcji jako element podrzędny `WadCfg`. Zastąp `ApplicationInsightsProfiler` wartość właściwości przy użyciu własnego klucza Instrumentacji usługi Application Insights:  

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

      Aby dowiedzieć się, jak dodawanie rozszerzenia diagnostyki do wdrożenia szablonu, zobacz [Użyj monitorowania i diagnostyki za pomocą szablonów maszyn wirtualnych Windows i usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Dla maszyn wirtualnych zamiast powyższych kroków opartych na notacji json jest nawigowanie w portalu Azure w celu **maszyn wirtualnych** > **ustawień diagnostycznych**  >   **Wychwytywanie** > Ustaw Wyślij dane diagnostyczne do usługi Application Insights do **włączone** i wybierz konto usługi Application Insights lub określonych klucz instrumentacji.

### <a name="azure-cloud-services"></a>usług Azure Cloud Services

1. Aby upewnić się, że używasz [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszym będą wystarczające, aby potwierdzić, że *ServiceConfiguration.\*.cscfg* pliki mają `osFamily` wartość "5" lub nowszej.

1. Znajdź [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* plików dla roli użytkownika aplikacji, jak pokazano poniżej:  

   ![Lokalizacja pliku konfiguracji diagnostyki](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Jeśli nie można znaleźć pliku, aby dowiedzieć się, jak włączyć rozszerzenie diagnostyki w projekcie usługi Azure Cloud Services, zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i virtual machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

1. Dodaj następujący kod `SinksConfig` sekcji jako element podrzędny `WadCfg`:  

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
> Jeśli *diagnostics.wadcfgx* plik zawiera także ujścia innego typu `ApplicationInsights`, wszystkie trzy następujące klucze instrumentacji muszą być zgodne:  
>  * Klucz, który jest używany przez aplikację.  
>  * Klucz, który jest używany przez `ApplicationInsights` ujścia.  
>  * Klucz, który jest używany przez `ApplicationInsightsProfiler` ujścia.  
>
> Można znaleźć wartości klucza Instrumentacji rzeczywiste, używanego przez `ApplicationInsights` ujścia *ServiceConfiguration.\*.cscfg* plików.  
> Po wydaniu programu Visual Studio 15.5 Azure SDK, kluczy instrumentacji, które są używane przez aplikację i `ApplicationInsightsProfiler` konieczność ujścia ze sobą zgodne.


## <a name="configure-environment-deployment-and-runtime"></a>Konfigurowanie wdrażania środowiska i środowiska uruchomieniowego

1. Wdrożenia zmodyfikowane środowisko definicji wdrożenia.  

   Aby zastosować zmiany, zazwyczaj polega na wdrożeniu pełnym szablonem znajdującym się lub chmury usługi na podstawie opublikowane za pomocą poleceń cmdlet programu PowerShell lub Visual Studio.  

   Poniżej przedstawiono alternatywnym podejściu, odnoszące się przez rozszerzenie Diagnostyka Azure dla istniejących maszyn wirtualnych:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. W przypadku zamierzonej aplikacji działających za pośrednictwem [IIS](https://www.microsoft.com/web/downloads/platform.aspx), Włącz `IIS Http Tracing` funkcji Windows, wykonując następujące czynności:  

   a. Ustanowić dostęp zdalny do środowiska, a następnie użyj [Windows Dodaj funkcje]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna lub uruchom następujące polecenie w programie PowerShell (jako administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ustanawianie dostęp zdalny jest problem, można użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uruchomić następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Włącz Profiler na serwerach lokalnych

Włączanie Profiler na lokalny serwer jest również nazywany uruchomionej Application Insights Profiler w trybie autonomicznym. Nie jest związany z modyfikacje rozszerzenia diagnostyki platformy Azure.

Mamy zamiar świadczył oficjalną pomoc techniczną Profiler dla serwerów lokalnych. Jeśli interesuje Cię eksperymentowanie z tego scenariusza, możesz to zrobić [Pobierz kod pomocy technicznej](https://github.com/ramach-msft/AIProfiler-Standalone). Jesteśmy *nie* odpowiedzialna za utrzymanie kodu, lub reagowania na problemy i żądania funkcji, które są związane z kodem.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) w witrynie Azure portal.
- Uzyskaj pomoc dotyczącą rozwiązywania problemów programu profilującego w [Profiler Rozwiązywanie problemów z](app-insights-profiler.md#troubleshooting).
- Dowiedz się więcej o Profiler w [Application Insights Profiler](app-insights-profiler.md).
