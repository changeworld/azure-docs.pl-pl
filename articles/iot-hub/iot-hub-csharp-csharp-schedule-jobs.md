---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub do wywołania metody bezpośredniej na wielu urządzeniach. Urządzenia usługi Azure IoT SDK dla platformy .NET umożliwia wdrożenie aplikacji urządzenia symulowanego i app service, aby uruchomić zadanie.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: robinsh
ms.openlocfilehash: db34734e9fbb8635f1a62def8a877d83d02e2206
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044444"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Planowanie i emitowanie zadań (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Za pomocą usługi Azure IoT Hub planować i śledzić zadania, które aktualizują milionów urządzeń. Używanie zadań do:

* Aktualizowanie żądanych właściwości
* Aktualizacji tagów
* Wywoływanie metod bezpośrednich

Zadanie opakowuje jedną z następujących czynności i śledzenie wykonywanie kodu na zbiór urządzeń, który jest definiowany przez zapytanie bliźniaczej reprezentacji urządzenia. Na przykład do wywołania metody bezpośredniej na 10 000 urządzeń, który wykonuje ponowny rozruch urządzenia zadań można użyć aplikacji zaplecza. Określ zbiór urządzeń przy użyciu zapytań bliźniaczych reprezentacji urządzeń i zaplanować zadania do uruchomienia w przyszłości. Postęp śledzi zadania, ponieważ do każdego urządzenia otrzymują i wykonaj ponowny rozruch metody bezpośredniej.

Aby dowiedzieć się więcej na temat każdego z tych funkcji, zobacz:

* Bliźniacza reprezentacja urządzenia i właściwości: [Wprowadzenie do bliźniaków urządzeń](iot-hub-csharp-csharp-twin-getstarted.md) i [samouczka: Jak korzystać z właściwości bliźniaczych reprezentacji urządzeń](tutorial-device-twins.md)

* Metody bezpośrednie: [Usługi IoT Hub developer guide - metod bezpośrednich](iot-hub-devguide-direct-methods.md) i [samouczka: Używanie metod bezpośrednich](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji urządzenia, która implementuje metodę bezpośrednią wywołaną **LockDoor** , można wywoływać za pomocą aplikacji zaplecza.

* Tworzenie aplikacji zaplecza, która tworzy zadanie, aby wywołać **LockDoor** metoda bezpośrednia na wielu urządzeniach. Inne zadanie wysyła aktualizacje żądanych właściwości na wielu urządzeniach.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET (C#):

**SimulateDeviceMethods** łączący się z Centrum IoT, a implementuje **LockDoor** bezpośrednie metody.

**ScheduleJob** używającej zadań do wywołania **LockDoor** metoda bezpośrednia i zaktualizuj odpowiednich właściwości bliźniaka urządzenia na wielu urządzeniach.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2017.
* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji służy do tworzenia aplikacji konsolowej .NET, która odpowiada na metodę bezpośrednią wywołaną przez rozwiązanie ponownie zakończenia.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **SimulateDeviceMethods**.
   
    ![Nowa aplikacja urządzenia Visual C# Windows Classic](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateDeviceMethods** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **Microsoft.Azure.Devices.Client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestaw SDK urządzeń Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet pakietu oraz jego zależności.
   
    ![Aplikacja kliencka okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia zanotowanym w poprzedniej sekcji:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Dodaj następujący kod do implementacji metody bezpośredniej na urządzeniu:

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

7. Dodaj następujące polecenie, aby zaimplementować odbiornik bliźniaczych reprezentacji urządzenia na urządzeniu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Na koniec Dodaj następujący kod do **Main** metodę, aby otworzyć połączenie do usługi IoT hub i zainicjować odbiornika metody:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

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
        
9. Zapisz swoją pracę i Skompiluj rozwiązanie.         

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. Ponów próbę połączenia), zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planowanie zadań podczas wywoływania metody bezpośredniej i wysyłania aktualizacji bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikacji konsoli .NET (przy użyciu języka C#), który używa zadań w celu wywołania **LockDoor** metoda bezpośrednia i Wyślij aktualizacje żądanych właściwości na wielu urządzeniach.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **ScheduleJob**.

    ![Nowy projekt Visual C# Windows Classic Desktop](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ScheduleJob** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .

3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj**, wyszukaj **Microsoft.Azure.Devices**, wybierz opcję **zainstalować** do zainstalowania **Microsoft.Azure.Devices** pakietu, a następnie zaakceptuj warunki użytkowania. Ten krok spowoduje pobranie, instaluje i dodanie odwołania do [zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet pakietu oraz jego zależności.

    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Dodaj następujący kod `using` instrukcji, jeśli jeszcze nie istnieje w instrukcjach domyślne.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze przy użyciu parametrów połączenia usługi IoT Hub dla Centrum utworzonego w poprzedniej sekcji, a nazwa urządzenia.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Dodaj inną metodę do **Program** klasy:

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
    > Aby uzyskać więcej informacji o składni zapytań, zobacz [język zapytań usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Na koniec dodaj następujące wiersze do metody **Główne**:

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

11. Zapisz swoją pracę i Skompiluj rozwiązanie. 

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W Eksploratorze rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **kompilacji**. **Wiele projektów startowych**. Upewnij się, że `SimulateDeviceMethods` u góry listy, a następnie `ScheduleJob`. Ustaw obie swoje działania **Start** i kliknij przycisk **OK**.

2. Uruchom projektów, klikając pozycję **Start** lub przejdź do **debugowania** menu i kliknij przycisk **Rozpocznij debugowanie**.

3. Zostaną wyświetlone dane wyjściowe zarówno z urządzeń i aplikacji zaplecza.

    ![Uruchamianie aplikacji do planowania zadań](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto zadania można zaplanować metody bezpośredniej do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować wprowadzenie do usługi IoT Hub i wzorców zarządzania urządzeniami, takich jak zdalne za pośrednictwem aktualizacji oprogramowania układowego air, przeczytaj [samouczka: Jak zaktualizować oprogramowanie układowe](tutorial-firmware-update.md).

Aby dowiedzieć się o wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge, zobacz [wprowadzenie do usługi IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).