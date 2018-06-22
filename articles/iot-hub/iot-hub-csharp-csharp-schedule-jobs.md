---
title: Planowanie zadań z Centrum IoT Azure (.NET/.NET) | Dokumentacja firmy Microsoft
description: Sposób tworzenia harmonogramu zadań Centrum IoT Azure do wywołania metody bezpośrednio na wielu urządzeniach. Urządzenia Azure IoT SDK dla platformy .NET umożliwia wdrożenie symulowane urządzenie aplikacje i usługi aplikacji w celu uruchomienia zadania.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: beb1e1e166325cb41a5d4e4fa07565b1f3d4b3bb
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308588"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Zadania harmonogramu i emisji (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Użyj Centrum IoT Azure do planowania i śledzenia zadań, które aktualizują milionów urządzeń. Należy użyć zadania, aby:

* Aktualizowanie żądanych właściwości
* Tagi aktualizacji
* Wywołanie metody bezpośredniego

Zadanie opakowuje jedną z następujących czynności i śledzi wykonanie względem zbiór urządzeń jest definiowana za pomocą zapytania dwie urządzenia. Na przykład aplikacji zaplecza zadanie służy do wywołania metody bezpośrednie na 10 000 urządzeń, które ponownym uruchomieniu urządzenia. Określ zbiór urządzeń za pomocą kwerendy dwie urządzenia i zaplanować zadania do uruchomienia w przyszłości. Śledzi postęp zadania jako każdego urządzenia odbierają i wykonać metoda bezpośrednia ponowny rozruch.

Aby dowiedzieć się więcej na temat każdego z tych funkcji, zobacz:

* Dwie urządzeń i właściwości: [Rozpoczynanie pracy z urządzenia twins] [ lnk-get-started-twin] i [samouczek: sposób użycia właściwości dwie urządzenia][lnk-twin-props]
* Bezpośrednie metody: [przewodnik dewelopera Centrum IoT — metody bezpośredniego] [ lnk-dev-methods] i [samouczek: bezpośrednie metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji urządzenia implementujący bezpośredniego metodę o nazwie **LockDoor** który można wywołać za pomocą aplikacji zaplecza.
* Tworzenie aplikacji zaplecza, która tworzy zadanie, aby wywołać **LockDoor** metoda bezpośrednia na wielu urządzeniach. Inne zadanie po zaakceptowaniu żądanej właściwości na wielu urządzeniach.

Na końcu tego samouczka masz dwie aplikacji konsoli .NET (C#):

**SimulateDeviceMethods** łączące się z Centrum IoT i implementuje **LockDoor** metoda bezpośrednia.

**ScheduleJob** używającą zadania do wywołania **LockDoor** metoda bezpośrednia i zaktualizuj właściwości dwie żądanego urządzenia na wielu urządzeniach.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji utworzysz odpowiadający do bezpośredniego metody wywoływane przez rozwiązania końcowy aplikacji konsoli .NET.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **SimulateDeviceMethods**.
   
    ![Nowa aplikacja Visual C# Windows Classic urządzenia][img-createdeviceapp]
    
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateDeviceMethods** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

1. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** do zainstalowania **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura pliki do pobrania, instaluje i dodaje odwołanie do [urządzenia Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet pakiet i jego zależności.
   
    ![Aplikacja kliencka okna Menedżera pakietów NuGet][img-clientnuget]

1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp symbol zastępczy wartości ciągu połączenia urządzenia zanotowanym w poprzedniej sekcji:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Dodaj następujący kod do implementacji metody bezpośredniej na urządzeniu:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Dodaj następujący kod do implementacji odbiornika twins urządzenia na urządzeniu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Na koniec należy dodać następujący kod, aby **Main** metodę, aby otworzyć połączenia z Centrum IoT i zainicjować odbiornika metody:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Zapisz swoją pracę i Skompiluj rozwiązanie.         

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (np. Ponów próbę połączenia), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planowanie zadań dla wywołania metody bezpośredniego i wysyłania aktualizacji dwie urządzenia

W tej sekcji Tworzenie aplikacji konsoli .NET (przy użyciu języka C#) korzystającą z zadań w celu wywołania **LockDoor** metoda bezpośrednia i wysyłać aktualizacje żądanej właściwości na wielu urządzeniach.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **ScheduleJob**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createapp]

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ScheduleJob** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

1. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ten krok pliki do pobrania, instaluje i dodaje odwołanie do [zestawu SDK usług Azure IoT] [ lnk-nuget-service-sdk] NuGet pakiet i jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]

1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Dodaj następujące `using` instrukcję, jeśli jeszcze nie istnieje w instrukcjach domyślne.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze Centrum IoT ciąg połączenia dla koncentratora, który został utworzony w poprzedniej sekcji oraz nazwę urządzenia.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Dodaj inną metodą **Program** klasy:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat składni zapytania, zobacz [język zapytań Centrum IoT][lnk-query].
    > 

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Zapisz swoją pracę i Skompiluj rozwiązanie. 


## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W Eksploratorze rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **kompilacji**. **Wiele projektów startowych**. Upewnij się, że `SimulateDeviceMethods` znajduje się na początku listy, a następnie `ScheduleJob`. Ustawiono zarówno ich działania **Start** i kliknij przycisk **OK**.

1. Uruchom projektów, klikając **Start** lub przejdź do **debugowania** menu i kliknij przycisk **Rozpocznij debugowanie**.

1. Możesz wyświetlić dane wyjściowe z urządzeń i aplikacji zaplecza.

    ![Uruchamianie aplikacji do planowania zadań][img-schedulejobs]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto zadanie można zaplanować metoda bezpośrednia urządzenia i aktualizacji właściwości dwie urządzenia.

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT i urządzenia zarządzania wzorców, takich jak zdalnego za pośrednictwem aktualizacji oprogramowania układowego lotniczego, przeczytaj [samouczek: sposób wykonywania aktualizacji oprogramowania układowego][lnk-fwupdate].

Aby dowiedzieć się więcej o wdrażaniu AI na krawędzi urządzenia Azure IoT krawędzi, zobacz [wprowadzenie krawędzi IoT][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
