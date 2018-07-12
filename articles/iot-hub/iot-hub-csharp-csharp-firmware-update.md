---
title: Aktualizacja oprogramowania układowego urządzenia za pomocą usługi Azure IoT Hub (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak zainicjować aktualizację oprogramowania układowego urządzenia, za pomocą zarządzania urządzeniami w usłudze Azure IoT Hub. Urządzenia usługi Azure IoT SDK dla platformy .NET umożliwia wdrożenie aplikacji symulowanego urządzenia i usługi Azure IoT SDK dla platformy .NET do zaimplementowania app service, która powoduje aktualizację oprogramowania układowego.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: 1bf7a647ab2fdc175231133b0cfd8abdd51b6d43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723931"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Wprowadzenie
W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczków pokazano, jak używać [bliźniaczej reprezentacji urządzenia] [ lnk-devtwin] i [metody bezpośrednie ] [ lnk-c2dmethod] podstawowych, aby zdalnie ponownie uruchomić urządzenie. Ten samouczek używa tych samych podstawowych usługi IoT Hub oraz dowiesz się, jak przeprowadzić aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany we wdrażaniu aktualizacji oprogramowania układowego dla [przykładu implementacji urządzenia Raspberry Pi][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsolowej .NET, która wywołuje **firmwareUpdate** bezpośrednie metody w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.
* Tworzenie aplikacji symulowanego urządzenia, który implementuje **firmwareUpdate** bezpośrednie metody. Ta metoda inicjuje proces wieloetapowych czeka do pobierania obrazu oprogramowania układowego, który pobierze obraz oprogramowania układowego i na koniec ma zastosowanie obrazu oprogramowania układowego. Na etapie każdej aktualizacji urządzenie używa zgłaszanych właściwości do raportowania postępu.

Na końcu tego samouczka masz urządzenie aplikacji konsoli .NET (C#) i serwer zaplecza w aplikacji konsolowej .NET (C#):

* **SimulatedDeviceFwUpdate**, która łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje **firmwareUpdate** bezpośrednie metody, uruchamia proces wielostanowy Symulowanie aktualizacji oprogramowania układowego w tym: Oczekiwanie na pobranie obrazu, pobierania nowego obrazu i na koniec stosowanie obrazu.

* **TriggerFWUpdate**, który używa zestawu SDK usługi do zdalnego wywołania **firmwareUpdate** bezpośrednie metody na symulowanym urządzeniu Wyświetla odpowiedzi i okresowo (co 500 ms) Wyświetla zaktualizowany zgłoszone właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Postępuj zgodnie z [wprowadzenie do zarządzania urządzeniami](iot-hub-csharp-csharp-device-management-get-started.md) artykuł, aby utworzyć Centrum IoT i pobieranie parametrów połączenia usługi IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, korzystając z metody bezpośredniej
W tej sekcji służy do tworzenia aplikacji konsolowej .NET (przy użyciu języka C#) który inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja używa metody bezpośredniej do inicjowania aktualizacji i używa zapytań bliźniaczych reprezentacji urządzeń, aby okresowo pobieranie stanu aktualizacji oprogramowania układowego active.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **TriggerFWUpdate**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createserviceapp]

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **TriggerFWUpdate** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
3. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wiele wartości symboli zastępczych parametrami połączenia Centrum IoT Hub dla Centrum utworzonego w poprzedniej sekcji i Identyfikatora urządzenia.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Dodaj następującą metodę do klasy **Program**. Ta metoda sonduje bliźniaczą reprezentację urządzenia dla figurować ze statusem co 500 milisekund. Zapisuje ona konsoli tylko wtedy, gdy stan rzeczywiście został zmieniony. Dla tego przykładu, aby uniemożliwić korzystanie z dodatkowych komunikatów usługi IoT Hub w ramach subskrypcji, sondowanie zatrzymuje, gdy urządzenie zgłosi stan **applyComplete** lub komunikat o błędzie.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Dodaj następującą metodę do klasy **Program**:

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Na koniec należy dodać następujące wiersze do **Main** metody. Tworzy rejestr Menedżera odczytać bliźniaczej reprezentacji urządzenia za pomocą rozpoczyna się zadanie sondowania w wątku roboczego, a następnie wyzwala aktualizację oprogramowania układowego.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Skompiluj rozwiązanie.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsolowej .NET, która reaguje na metodę bezpośrednią wywołaną przez chmurę
* Symulowanie wyzwolone przez usługę zaplecza za pośrednictwem metody bezpośredniej aktualizacji oprogramowania układowego
* Włączanie zapytań bliźniaczych reprezentacji urządzeń przy użyciu zgłoszonych właściwości w celu zidentyfikowania urządzeń i ustalenia, kiedy ostatnio przeprowadzono na nich aktualizację oprogramowania układowego

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **SimulateDeviceFWUpdate**.
   
    ![Nowa aplikacja urządzenia Visual C# Windows Classic][img-createdeviceapp]
    
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateDeviceFWUpdate** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestaw SDK urządzeń Azure IoT] [ lnk-nuget-client-sdk] NuGet pakietu oraz jego zależności.
   
    ![Aplikacja kliencka okno Menedżera pakietów NuGet][img-clientnuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia zanotowaną w **tworzenie tożsamości urządzenia** sekcji.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Dodaj następującą metodę do raportów o stanie do chmury za pomocą bliźniaczej reprezentacji urządzenia: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Dodaj następującą metodę do symulacji pobierania obrazu oprogramowania układowego:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Dodaj następującą metodę do symulowania stosowania obrazu oprogramowania układowego urządzenia:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Dodaj następującą metodę do symulacji oczekujących na pobranie obrazu oprogramowania układowego. Zmiana stanu na **oczekiwania** i usuń zaznaczenie innych właściwości aktualizacji oprogramowania układowego w bliźniaczej reprezentacji. Te właściwości zostaną wyczyszczone, aby usunąć wszelkie istniejące wartości z aktualizacji oprogramowania układowego poprzedniego. Jest to konieczne, ponieważ zgłaszane właściwości są wysyłane jako operacji PATCH (delta), a nie operacji PUT (kompletny zestaw właściwości zastępuje wszystkie poprzednie wartości). Zazwyczaj urządzenia otrzymują informacje o dostępnej aktualizacji, a zasady określone przez administratora powodują, że urządzenie zaczyna pobierać i stosować aktualizację. To w tej funkcji powinna zostać uruchomiona logika włączająca te zasady. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Dodaj następującą metodę w celu wykonania pobierania. Aktualizuje stan do **pobieranie** wywołuje metodę pobierania Symulacja, za pomocą bliźniaczej reprezentacji urządzenia, a następnie raportuje stan **downloadComplete** lub **downloadFailed** za pomocą bliźniaczej reprezentacji w zależności od wyników operacji pobierania. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Dodaj następującą metodę w celu zastosowania obrazu. Aktualizuje stan do **stosowanie** za pomocą bliźniaczej reprezentacji urządzenia wywołania Symulacja Zastosuj obraz metoda i aktualizuje stan do **applyComplete** lub **applyFailed** za pośrednictwem bliźniacza reprezentacja w zależności od wyników operacji zastosowania. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Dodaj następującą metodę do sekwencji operacji aktualizacji oprogramowania układowego z oczekiwanie na pobranie obrazu poprzez zastosowanie obrazu na urządzeniu:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Dodaj następującą metodę do obsługi **updateFirmware** bezpośrednie metody z chmury. Wybiera adres URL do aktualizacji oprogramowania układowego z ładunku komunikatu i przekazuje go do **doUpdate** zadania, które uruchamia się na inny wątek puli wątków. Następnie natychmiast zwraca odpowiedź metody do chmury.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Ta metoda wyzwala symulowane aktualizacji, aby był uruchamiany jako **zadań** i następnie natychmiast reaguje na wywołanie metody informowania usługę uruchomienia aktualizacji oprogramowania układowego. Stan aktualizacji oraz moment zakończenia będą wysyłane do usługi za pomocą zgłoszonych właściwości bliźniaka urządzenia. Pod uwagę brane są wywołania metody podczas uruchamiania aktualizacji, a nie po jej zakończeniu, ponieważ:
> * Proces aktualizacji rzeczywistych jest najprawdopodobniej będzie trwać dłużej niż limit czasu wywołania metody.
> * Proces aktualizacji rzeczywistych jest bardzo prawdopodobne, konieczne jest ponowne uruchomienie będzie ponownie uruchomić to tworzenie aplikacji **MethodRequest** obiekt niedostępny. (Aktualizowania zgłaszanych właściwości, jednak jest możliwe nawet po ponownym uruchomieniu). 

14. Na koniec Dodaj następujący kod do **Main** metodę, aby otworzyć połączenie do usługi IoT hub i zainicjować odbiornika metody:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Skompiluj rozwiązanie.       

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.
1. Uruchom aplikację urządzenia platformy .NET **SimulatedDeviceFWUpdate**.  Kliknij prawym przyciskiem myszy **SimulatedDeviceFWUpdate** projektu, wybierz opcję **debugowania**, a następnie wybierz pozycję **Uruchom nowe wystąpienie**. Należy uruchomić, nasłuchiwanie wywołania metody z usługi IoT Hub: 

2. Gdy urządzenie jest podłączone i Oczekiwanie na wywołania metody uruchamiania .NET **TriggerFWUpdate** aplikację, aby wywołać metodę aktualizacji oprogramowania układowego w symulowanej aplikacji urządzenia. Kliknij prawym przyciskiem myszy **TriggerFWUpdate** projektu, wybierz opcję **debugowania**, a następnie wybierz pozycję **Uruchom nowe wystąpienie**. Powinien zostać wyświetlony zaktualizować sekwencję napisane w **SimulatedDeviceFWUpdate** konsoli, a także sekwencji zgłoszone przez zgłoszone właściwości urządzenia w **TriggerFWUpdate** konsoli. Należy zauważyć, że proces trwa kilka sekund. 
   
    ![Uruchamiania aplikacji usługi i urządzenia][img-combinedrun]


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używane metody bezpośredniej do wyzwalania aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postęp aktualizacji oprogramowania układowego w zgłaszanych właściwości.

Aby dowiedzieć się, jak rozszerzyć rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach IoT, zobacz [harmonogramu i zadań emisji] [ lnk-tutorial-jobs] samouczka.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-csharp-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
