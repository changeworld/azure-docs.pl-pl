---
title: Jak używać usługi Diagnostyka Azure (.NET) z usługami w chmurze | Dokumentacja firmy Microsoft
description: Przy użyciu diagnostyki Azure do zbierania danych z usług Azure cloud Services, debugowanie, mierzenia wydajności, monitorowania, analizy ruchu sieciowego i inne.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: jeconnoc
ms.openlocfilehash: ba69a5aaffb39c26731ffd209587a8c8223b032a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337395"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Włączając diagnostykę platformy Azure w usługach Azure Cloud Services
Zobacz [Omówienie diagnostyki Azure](../azure-diagnostics.md) w tle w usłudze Diagnostyka Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Jak włączyć diagnostykę w roli procesu roboczego
W tym przewodniku opisano sposób wdrażania roli procesu roboczego platformy Azure, który emituje dane telemetryczne za pomocą klasy Element EventSource platformy .NET. Diagnostyka Azure służy do zbierania danych telemetrycznych i zapisz go na koncie usługi Azure storage. Po utworzeniu roli procesu roboczego programu Visual Studio automatycznie włącza Diagnostyka 1.0 w ramach rozwiązania w zestawy Azure SDK dla platformy .NET 2.4 i starszych wersji. Poniższe instrukcje opisują proces tworzenia roli procesu roboczego, z wyłączeniem Diagnostyka 1.0 z rozwiązania i wdrażanie diagnostyki w wersji 1.2 lub 1.3 do swojej roli procesu roboczego.

### <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, mieć subskrypcję platformy Azure i przy użyciu programu Visual Studio przy użyciu zestawu SDK platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz zarejestrować się w celu [bezpłatnej wersji próbnej][Free Trial]. Upewnij się, że [Instalowanie i konfigurowanie programu Azure PowerShell w wersji 0.8.7 lub nowszej][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Krok 1: Tworzenie roli procesu roboczego
1. Uruchom program **Visual Studio**.
2. Tworzenie **usługa w chmurze** projekt z **chmury** szablon, który jest przeznaczony dla .NET Framework 4.5.  Nazwij projekt "WadExample", a następnie kliknij przycisk Ok.
3. Wybierz **roli procesu roboczego** i kliknij przycisk Ok. Projekt zostanie utworzony.
4. W **Eksploratora rozwiązań**, kliknij dwukrotnie **WorkerRole1** pliku właściwości.
5. W **konfiguracji** karcie, usuń zaznaczenie **Włącz diagnostykę** wyłączyć Diagnostyka 1.0 (zestaw Azure SDK 2.4 lub starszej).
6. Skompiluj rozwiązanie, aby sprawdzić, czy żadne błędy.

### <a name="step-2-instrument-your-code"></a>Krok 2: Instrumentować swój kod
Zastąp zawartość WorkerRole.cs następującym kodem. Odziedziczone po klasie SampleEventSourceWriter, [EventSource — klasa][EventSource Class], implementuje cztery metody rejestrowania: **SendEnums**, **MessageMethod**, **SetOther** i **HighFreq**. Pierwszy parametr **WriteEvent** Metoda określa identyfikator odpowiednie zdarzenie. Metoda Run implementuje nieskończoną pętlę, która wywołuje każdą z metod rejestrowania zaimplementowanych w **SampleEventSourceWriter** klasy co 10 sekund.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Krok 3: Wdrażanie swojej roli procesu roboczego

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Wdrażanie swojej roli procesu roboczego na platformie Azure z poziomu programu Visual Studio, wybierając **WadExample** projekt w Eksploratorze rozwiązań następnie **Publikuj** z **kompilacji** menu.
2. Wybierz subskrypcję.
3. W **ustawień publikowania platformy Microsoft** okno dialogowe, wybierz opcję **Utwórz nowy...** .
4. W **tworzenia usługi w chmurze i konto magazynu** okno dialogowe, wprowadź **nazwa** (na przykład "WadExample") i wybierz region lub grupa koligacji.
5. Ustaw **środowiska** do **przemieszczania**.
6. Zmodyfikuj inne **ustawienia** jako odpowiednie i kliknij przycisk **Publikuj**.
7. Po zakończeniu wdrożenia należy sprawdzić w witrynie Azure portal, należącym do usługi w chmurze **systemem** stanu.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Krok 4: Tworzenie pliku konfiguracji diagnostyki i zainstalować rozszerzenie
1. Pobierz definicję schematu pliku konfiguracji publicznej, wykonując następujące polecenie programu PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Dodaj plik XML do Twojej **WorkerRole1** projektu, klikając prawym przyciskiem myszy **WorkerRole1** projektu, a następnie wybierz **Dodaj** -> **nowy element...** -> **Elementy Visual C#**  -> **danych** -> **pliku XML**. Nadaj plikowi nazwę "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Skojarz WadConfig.xsd z pliku konfiguracji. Upewnij się, że okno edytora WadExample.xml jest aktywnym oknem. Naciśnij klawisz **F4** otworzyć **właściwości** okna. Kliknij przycisk **schematów** właściwość **właściwości** okna. Kliknij przycisk **...** w **schematów** właściwości. Kliknij przycisk **Dodaj...** przycisk i przejdź do lokalizacji, w której zapisano plik XSD, a następnie wybierz plik WadConfig.xsd. Kliknij przycisk **OK**.

4. Zastąp zawartość pliku konfiguracji WadExample.xml następujący kod XML, a następnie zapisz plik. Ten plik konfiguracyjny definiuje kilka liczników wydajności do zbierania: jeden dla użycia Procesora CPU i jeden dla użycia pamięci. Konfiguracja definiuje cztery zdarzenia odpowiadającej metody w klasie SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Krok 5. Zainstaluj diagnostyki na swojej roli procesu roboczego
Polecenia cmdlet programu PowerShell do zarządzania diagnostyki w roli sieci web lub proces roboczy są: Zestaw AzureServiceDiagnosticsExtension Get AzureServiceDiagnosticsExtension i Remove AzureServiceDiagnosticsExtension.

1. Otwórz program Azure PowerShell.
2. Uruchom skrypt do zainstalowania diagnostyki na swojej roli procesu roboczego (Zastąp *StorageAccountKey* przy użyciu klucza konta magazynu dla konta usługi storage wadexample i *config_path* ze ścieżką do  *WadExample.xml* plików):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Krok 6: Spójrz na dane telemetryczne
W programie Visual Studio **Eksploratora serwera**, przejdź do konta magazynu wadexample. Po około pięciu (5) minut działaniu usługi w chmurze, powinien zostać wyświetlony tabele **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** i **WADSetOtherTable**. Kliknij dwukrotnie jednej z tabel, aby wyświetlić dane telemetryczne, które zostały zebrane.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schemat pliku konfiguracji
Plik konfiguracji diagnostyki definiuje wartości, które są stosowane do inicjalizacji ustawień diagnostycznych konfiguracji podczas uruchamiania agenta diagnostyki. Zobacz [najnowsze informacje ogólne o schematach](/azure/azure-monitor/platform/diagnostics-extension-schema) uzyskać prawidłowe wartości i przykładów.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy, zobacz [Rozwiązywanie problemów z usługi Azure Diagnostics](../azure-diagnostics-troubleshooting.md) Pomoc dotycząca typowych problemów.

## <a name="next-steps"></a>Następne kroki
[Wyświetlenie listy pokrewne artykuły diagnostyki maszyny wirtualnej platformy Azure](../azure-monitor/platform/diagnostics-extension-overview.md#cloud-services-using-azure-diagnostics) Aby zmienić dane są zbierane, rozwiązywanie problemów, lub Dowiedz się więcej o diagnostyce ogólnie rzecz biorąc.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/
