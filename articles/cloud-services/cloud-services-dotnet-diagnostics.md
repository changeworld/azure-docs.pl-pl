---
title: Jak używać z usługami w chmurze Azure diagnostics (.NET) | Dokumentacja firmy Microsoft
description: Za pomocą diagnostyki Azure zbieranie danych z usług w chmurze Azure dla debugowania, pomiaru wydajności, monitorowania, analizy ruchu sieciowego i inne.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: adegeo
ms.openlocfilehash: a8d6b16fa363062e06d48bfc5af2ca37697d5cd8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2018
ms.locfileid: "29460903"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Włączanie diagnostyki Azure usług w chmurze Azure
Zobacz [Omówienie diagnostyki Azure](../azure-diagnostics.md) w tle na diagnostyki Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Jak włączyć diagnostyki w roli procesu roboczego
W tym przewodniku opisano Implementowanie roli procesu roboczego platformy Azure, który emituje dane telemetryczne za pomocą klasy .NET EventSource. Diagnostyka Azure służy do zbierania danych telemetrycznych i zapisz go w koncie magazynu platformy Azure. Podczas tworzenia roli procesu roboczego, Visual Studio automatycznie włącza 1.0 diagnostyki w ramach rozwiązania w zestawy Azure SDK dla platformy .NET 2.4 i starszych wersji. Poniższe instrukcje opisują proces tworzenia roli procesu roboczego, wyłączenie diagnostyki 1.0 z rozwiązania oraz wdrażanie diagnostyki 1.2 lub 1.3 do swojej roli procesu roboczego.

### <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, posiadania subskrypcji platformy Azure i przy użyciu programu Visual Studio z zestawem Azure SDK. Jeśli nie masz subskrypcji platformy Azure, możesz zarejestrować się w celu [bezpłatnej wersji próbnej][Free Trial]. Upewnij się, że [Instalowanie i konfigurowanie programu Azure PowerShell w wersji 0.8.7 lub nowszym][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Krok 1: Tworzenie roli procesu roboczego
1. Uruchom program **Visual Studio**.
2. Utwórz **usługi w chmurze Azure** projekt z **chmury** szablonu, którego celem jest środowisko .NET Framework 4.5.  Nazwa projektu "WadExample", a następnie kliknij przycisk Ok.
3. Wybierz **roli procesu roboczego** i kliknij przycisk Ok. Projekt zostanie utworzony.
4. W **Eksploratora rozwiązań**, kliknij dwukrotnie **WorkerRole1** właściwości pliku.
5. W **konfiguracji** karcie, usuń zaznaczenie **włączyć diagnostyki** wyłączenie 1.0 diagnostyki Azure SDK (2.4 lub starszej).
6. Skompiluj rozwiązanie, aby zweryfikować, że użytkownik nie ma błędów.

### <a name="step-2-instrument-your-code"></a>Krok 2: Instrumentacja kodu
Zastąp zawartość WorkerRole.cs z następującym kodem. Odziedziczone po klasie SampleEventSourceWriter, [EventSource — klasa][EventSource Class], implementuje cztery metody rejestrowania: **SendEnums**, **MessageMethod** , **SetOther** i **HighFreq**. Pierwszy parametr **metody WriteEvent** Metoda określa identyfikator dla odpowiednich zdarzenia. Metoda Run implementuje nieskończoną pętlę, która wywołuje każdą z metod rejestrowania w **SampleEventSourceWriter** klasy co 10 sekund.

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
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Krok 3: Wdrażanie swojej roli procesu roboczego

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Wdrażanie swojej roli procesu roboczego na platformie Azure z poziomu programu Visual Studio, wybierając **WadExample** projekt w Eksploratorze rozwiązań następnie **publikowania** z **kompilacji** menu.
2. Wybierz subskrypcję.
3. W **ustawień publikowania platformy Azure Microsoft** okno dialogowe, wybierz opcję **Utwórz nowy...** .
4. W **Tworzenie usługi w chmurze i konto magazynu** okna dialogowego, wprowadź **nazwa** (na przykład "WadExample") i wybierz region lub grupę koligacji.
5. Ustaw **środowiska** do **przemieszczania**.
6. Zmodyfikuj inne **ustawienia** jako odpowiednie i kliknij przycisk **publikowania**.
7. Sprawdź, czy po zakończeniu wdrożenia w portalu Azure, która usługi w chmurze jest w **systemem** stanu.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Krok 4: Tworzenie pliku konfiguracji diagnostyki i zainstaluj rozszerzenie
1. Pobierz definicję schematu pliku konfiguracji publicznego, wykonując następujące polecenie programu PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Dodawanie pliku XML do Twojej **WorkerRole1** projektu, klikając prawym przyciskiem myszy **WorkerRole1** projekt i wybierz **Dodaj** -> **nowy element...** -> **Visual C# elementów** -> **danych** -> **pliku XML**. Nadaj nazwę plikowi "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Skojarz WadConfig.xsd z pliku konfiguracji. Upewnij się, że okno edytora WadExample.xml jest aktywnym oknem. Naciśnij klawisz **F4** otworzyć **właściwości** okna. Kliknij przycisk **schematy** właściwości w **właściwości** okna. Kliknij przycisk **...** w **schematy** właściwości. Kliknij przycisk **Dodaj...** przycisk i przejdź do lokalizacji, w której zapisano plik XSD, a następnie wybierz plik WadConfig.xsd. Kliknij przycisk **OK**.

4. Zastąp zawartość pliku konfiguracji WadExample.xml następujące XML i Zapisz plik. Ten plik konfiguracyjny definiuje kilka liczniki wydajności do: jeden dla jednego wykorzystania pamięci i czasu procesora. Konfiguracja definiuje zdarzenia cztery odpowiadających metod w klasie SampleEventSourceWriter.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Krok 5: Instalowanie diagnostyki w swojej roli procesu roboczego
Polecenia cmdlet programu PowerShell do zarządzania diagnostyki dla roli sieci web lub procesu roboczego są: Set-AzureServiceDiagnosticsExtension, Get AzureServiceDiagnosticsExtension i Usuń AzureServiceDiagnosticsExtension.

1. Otwórz program Azure PowerShell.
2. Uruchom skrypt do zainstalowania diagnostyki na swojej roli procesu roboczego (Zastąp *StorageAccountKey* kluczem konta magazynu dla konta magazynu wadexample i *config_path* ze ścieżką do  *WadExample.xml* pliku):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Krok 6: Sprawdź dane telemetryczne
W programie Visual Studio **Eksploratora serwera**, przejdź do konta magazynu wadexample. Po około pięciu (5) minut działaniu usługi w chmurze, należy wyświetlić tabele **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** i **WADSetOtherTable**. Dwukrotnie kliknij jedną z tabel, aby wyświetlić dane telemetryczne, które zostały zebrane.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schemat pliku konfiguracji
Plik konfiguracji diagnostyki definiuje wartości, które są używane do inicjowania ustawień diagnostycznych konfiguracji podczas uruchamiania agenta diagnostyki. Zobacz [najnowsze odwołanie do schematu](https://msdn.microsoft.com/library/azure/mt634524.aspx) poprawne wartości i przykłady.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy, zobacz [Rozwiązywanie problemów z diagnostyki Azure](../azure-diagnostics-troubleshooting.md) Aby uzyskać pomoc dotyczącą typowych problemów.

## <a name="next-steps"></a>Następne kroki
[Zobacz listę pokrewne artykuły diagnostycznych maszyn wirtualnych Azure](../monitoring-and-diagnostics/azure-diagnostics.md#cloud-services-using-azure-diagnostics) Aby zmienić dane są zbierane, rozwiązywanie problemów, lub Dowiedz się więcej o diagnostyce ogólnie.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
