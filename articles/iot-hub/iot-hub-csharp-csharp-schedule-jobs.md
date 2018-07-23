---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub do wywołania metody bezpośredniej na wielu urządzeniach. Urządzenia usługi Azure IoT SDK dla platformy .NET umożliwia wdrożenie aplikacji urządzenia symulowanego i app service, aby uruchomić zadanie.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: 0ac74a5b1a65dc171c6addd30152010965888808
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185530"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Planowanie i emitowanie zadań (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Za pomocą usługi Azure IoT Hub planować i śledzić zadania, które aktualizują milionów urządzeń. Używanie zadań do:

* Aktualizowanie żądanych właściwości
* Aktualizacji tagów
* Wywoływanie metod bezpośrednich

Zadanie opakowuje jedną z następujących czynności i śledzenie wykonywanie kodu na zbiór urządzeń, który jest definiowany przez zapytanie bliźniaczej reprezentacji urządzenia. Na przykład do wywołania metody bezpośredniej na 10 000 urządzeń, który wykonuje ponowny rozruch urządzenia zadań można użyć aplikacji zaplecza. Określ zbiór urządzeń przy użyciu zapytań bliźniaczych reprezentacji urządzeń i zaplanować zadania do uruchomienia w przyszłości. Postęp śledzi zadania, ponieważ do każdego urządzenia otrzymują i wykonaj ponowny rozruch metody bezpośredniej.

Aby dowiedzieć się więcej na temat każdego z tych funkcji, zobacz:

* Bliźniacza reprezentacja urządzenia i właściwości: [wprowadzenie do bliźniaków urządzeń] [ lnk-get-started-twin] i [samouczek: jak korzystać z właściwości bliźniaczych reprezentacji urządzeń][lnk-twin-props]
* Metody bezpośrednie: [usługi IoT Hub developer guide - metod bezpośrednich] [ lnk-dev-methods] i [samouczek: używanie metod bezpośrednich][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji urządzenia, która implementuje metodę bezpośrednią wywołaną **LockDoor** , można wywoływać za pomocą aplikacji zaplecza.
* Tworzenie aplikacji zaplecza, która tworzy zadanie, aby wywołać **LockDoor** metoda bezpośrednia na wielu urządzeniach. Inne zadanie wysyła aktualizacje żądanych właściwości na wielu urządzeniach.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET (C#):

**SimulateDeviceMethods** łączący się z Centrum IoT, a implementuje **LockDoor** bezpośrednie metody.

**ScheduleJob** używającej zadań do wywołania **LockDoor** metoda bezpośrednia i zaktualizuj odpowiednich właściwości bliźniaka urządzenia na wielu urządzeniach.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji służy do tworzenia aplikacji konsolowej .NET, która odpowiada na metodę bezpośrednią wywołaną przez rozwiązanie ponownie zakończenia.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **SimulateDeviceMethods**.
   
    ![Nowa aplikacja urządzenia Visual C# Windows Classic][img-createdeviceapp]
    
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateDeviceMethods** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

1. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestaw SDK urządzeń Azure IoT] [ lnk-nuget-client-sdk] NuGet pakietu oraz jego zależności.
   
    ![Aplikacja kliencka okno Menedżera pakietów NuGet][img-clientnuget]

1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia zanotowanym w poprzedniej sekcji:

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

1. Dodaj następujące polecenie, aby zaimplementować odbiornik bliźniaczych reprezentacji urządzenia na urządzeniu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Na koniec Dodaj następujący kod do **Main** metodę, aby otworzyć połączenie do usługi IoT hub i zainicjować odbiornika metody:
   
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
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. Ponów próbę połączenia), zgodnie z sugestią w artykule z witryny MSDN [obsługi błędów przejściowych][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planowanie zadań podczas wywoływania metody bezpośredniej i wysyłania aktualizacji bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikacji konsoli .NET (przy użyciu języka C#), który używa zadań w celu wywołania **LockDoor** metoda bezpośrednia i Wyślij aktualizacje żądanych właściwości na wielu urządzeniach.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **ScheduleJob**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createapp]

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ScheduleJob** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

1. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ten krok spowoduje pobranie, instaluje i dodanie odwołania do [zestawu SDK usługi Azure IoT] [ lnk-nuget-service-sdk] NuGet pakietu oraz jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]

1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Dodaj następujący kod `using` instrukcji, jeśli jeszcze nie istnieje w instrukcjach domyślne.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze przy użyciu parametrów połączenia usługi IoT Hub dla Centrum utworzonego w poprzedniej sekcji, a nazwa urządzenia.

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

1. Dodaj inną metodę do **Program** klasy:

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
    > Aby uzyskać więcej informacji o składni zapytań, zobacz [język zapytań usługi IoT Hub][lnk-query].
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

1. W Eksploratorze rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **kompilacji**. **Wiele projektów startowych**. Upewnij się, że `SimulateDeviceMethods` u góry listy, a następnie `ScheduleJob`. Ustaw obie swoje działania **Start** i kliknij przycisk **OK**.

1. Uruchom projektów, klikając pozycję **Start** lub przejdź do **debugowania** menu i kliknij przycisk **Rozpocznij debugowanie**.

1. Zostaną wyświetlone dane wyjściowe zarówno z urządzeń i aplikacji zaplecza.

    ![Uruchamianie aplikacji do planowania zadań][img-schedulejobs]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto zadania można zaplanować metody bezpośredniej do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować wprowadzenie do usługi IoT Hub i wzorców zarządzania urządzeniami, takich jak zdalne za pośrednictwem aktualizacji oprogramowania układowego air, przeczytaj [samouczek: jak zaktualizować oprogramowanie układowe][lnk-fwupdate].

Aby dowiedzieć się o wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge, zobacz [wprowadzenie do usługi IoT Edge][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: tutorial-device-twins.md
[lnk-c2d-methods]: quickstart-control-device-dotnet.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
