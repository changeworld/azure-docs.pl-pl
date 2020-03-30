---
title: Jak korzystać z diagnostyki platformy Azure (.NET) z usługami w chmurze | Dokumenty firmy Microsoft
description: Korzystanie z diagnostyki platformy Azure do zbierania danych z usług w chmurze platformy Azure do debugowania, pomiaru wydajności, monitorowania, analizy ruchu i innych.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: 1e49a0935a70a2470267e5458fa1f55e3059e965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469769"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Włączanie diagnostyki platformy Azure w usługach w chmurze platformy Azure
Zobacz [Omówienie diagnostyki platformy Azure](../azure-diagnostics.md) w tle diagnostyki platformy Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Jak włączyć diagnostykę w roli procesu roboczego
W tym przewodniku opisano sposób implementacji roli procesu roboczego platformy Azure, która emituje dane telemetryczne przy użyciu klasy .NET EventSource. Usługa Azure Diagnostics służy do zbierania danych telemetrycznych i przechowywania ich na koncie magazynu platformy Azure. Podczas tworzenia roli procesu roboczego program Visual Studio automatycznie włącza diagnostykę 1.0 jako część rozwiązania w zestawach SDK platformy Azure dla platformy .NET 2.4 i wcześniejszych. Poniższe instrukcje opisują proces tworzenia roli procesu roboczego, wyłączanie diagnostyki 1.0 z rozwiązania i wdrażanie diagnostyki 1.2 lub 1.3 do roli procesu roboczego.

### <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz subskrypcję platformy Azure i używasz programu Visual Studio z zestawem SDK platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz zarejestrować się w ramach [bezpłatnej wersji próbnej.][Free Trial] Upewnij się, że [należy zainstalować i skonfigurować program Azure PowerShell w wersji 0.8.7 lub nowszej][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Krok 1: Tworzenie roli pracownika
1. Uruchom program **Visual Studio**.
2. Utwórz projekt **usługi Azure Cloud Service** na podstawie szablonu **chmury** przeznaczonego dla platformy .NET Framework 4.5.  Nazwij projekt "WadExample" i kliknij przycisk Ok.
3. Wybierz **pozycję Rola procesu roboczego** i kliknij przycisk Ok. Projekt zostanie utworzony.
4. W **Eksploratorze rozwiązań**kliknij dwukrotnie plik właściwości **WorkerRole1.**
5. Na karcie **Konfiguracja** odeznaj **włącz diagnostykę,** aby wyłączyć diagnostykę 1.0 (zestaw Azure SDK 2.4 lub wcześniejszy).
6. Skompiluj rozwiązanie, aby sprawdzić, czy nie ma żadnych błędów.

### <a name="step-2-instrument-your-code"></a>Krok 2: Instrument kodu
Zastąp zawartość WorkerRole.cs następującym kodem. Klasa SampleEventSourceWriter, dziedziczona z [klasy EventSource,][EventSource Class]implementuje cztery metody rejestrowania: **SendEnums**, **MessageMethod**, **SetOther** i **HighFreq**. Pierwszy parametr **writeevent** metoda definiuje identyfikator dla danego zdarzenia. Run Metoda implementuje nieskończoną pętlę, która wywołuje każdą z metod rejestrowania zaimplementowanych w **SampleEventSourceWriter** klasy co 10 sekund.

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


### <a name="step-3-deploy-your-worker-role"></a>Krok 3: Wdrażanie roli pracownika

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Wdrażanie roli procesu roboczego na platformie Azure z poziomu programu Visual Studio, wybierając projekt **WadExample** w Eksploratorze rozwiązań, a następnie **publikuj** z menu **Kompilacja.**
2. Wybierz subskrypcję.
3. W oknie dialogowym **Ustawienia publikowania platformy Microsoft Azure** wybierz pozycję **Utwórz nowy...**.
4. W oknie dialogowym **Tworzenie usługi w chmurze i konta magazynu** wprowadź **nazwę** (na przykład "WadExample") i wybierz grupę regionu lub koligacji.
5. Ustaw **środowisko** na **Przemieszczanie**.
6. Odpowiednio zmodyfikuj inne **Ustawienia** i kliknij przycisk **Publikuj**.
7. Po zakończeniu wdrażania sprawdź w witrynie Azure portal, czy usługa w chmurze jest w stanie **uruchomionym.**

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Krok 4: Tworzenie pliku konfiguracyjnego diagnostyki i instalowanie rozszerzenia
1. Pobierz definicję schematu publicznego pliku konfiguracji, wykonując następujące polecenie programu PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Dodaj plik XML do projektu **WorkerRole1,** klikając prawym przyciskiem myszy projekt **WorkerRole1** i wybierając pozycję **Dodaj** -> **nowy element...** -> **Elementy języka Visual C#** -> **Data** -> **XML File**. Nazwij plik "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Skojarz wadconfig.xsd z plikiem konfiguracji. Upewnij się, że okno edytora WadExample.xml jest aktywnym oknem. Naciśnij **klawisz F4,** aby otworzyć okno **Właściwości.** Kliknij właściwość **Schematy** w oknie **Właściwości.** Kliknij **przycisk ...** w **Schemas** właściwości. Kliknij przycisk **Dodaj...** i przejdź do lokalizacji, w której zapisano plik XSD i wybierz plik WadConfig.xsd. Kliknij przycisk **OK**.

4. Zastąp zawartość pliku konfiguracyjnego WadExample.xml następującym plikiem XML i zapisz plik. Ten plik konfiguracyjny definiuje kilka liczników wydajności do zbierania: jeden do wykorzystania procesora CPU i jeden do wykorzystania pamięci. Następnie konfiguracja definiuje cztery zdarzenia odpowiadające metodom w SampleEventSourceWriter klasy.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Krok 5: Zainstaluj diagnostykę w roli pracownika
Polecenia cmdlet programu PowerShell do zarządzania diagnostyką w roli sieci web lub procesu roboczego to: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension i Remove-AzureServiceDiagnosticsExtension.

1. Otwórz program Azure PowerShell.
2. Wykonaj skrypt, aby zainstalować diagnostykę w roli procesu roboczego (zastąp *StorageAccountKey* kluczem konta magazynu dla konta magazynu wadexample i *config_path* ścieżką do pliku *WadExample.xml):*

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Krok 6: Spójrz na dane telemetryczne
W **Eksploratorze serwera**programu Visual Studio przejdź do konta magazynu wadexample. Po uruchomieniu usługi w chmurze około pięciu (5) minut, powinny być widoczne tabele **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** i **WADSetOtherTable**. Kliknij dwukrotnie jedną z tabel, aby wyświetlić dane telemetryczne, które zostały zebrane.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schemat pliku konfiguracji
Plik konfiguracji diagnostyki definiuje wartości, które są używane do inicjowania ustawień konfiguracji diagnostycznej po uruchomieniu agenta diagnostycznego. Zobacz [najnowsze odwołanie do schematu](/azure/azure-monitor/platform/diagnostics-extension-schema) dla prawidłowych wartości i przykładów.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy, zobacz [Rozwiązywanie problemów z diagnostyką platformy Azure, aby](../azure-diagnostics-troubleshooting.md) uzyskać pomoc dotyczącą typowych problemów.

## <a name="next-steps"></a>Następne kroki
[Zobacz listę powiązanych artykułów diagnostycznych platformy Azure maszyny wirtualnej,](../azure-monitor/platform/diagnostics-extension-overview.md) aby zmienić dane, które zbierasz, rozwiązywać problemy lub dowiedzieć się więcej o diagnostyce w ogóle.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



