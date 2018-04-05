---
title: Aktualizacja oprogramowania układowego urządzenia z Centrum IoT Azure (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami w usłudze Azure IoT Hub zainicjować aktualizację oprogramowania układowego urządzenia. Urządzenia Azure IoT SDK dla platformy .NET użyta do wdrożenia aplikacji symulowane urządzenie i usługa Azure IoT SDK dla platformy .NET do implementacji usługi aplikacji, które wyzwala aktualizacji oprogramowania układowego.
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: 984fd7516b5c05973c45891f4eda31c41a5a2be5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Wprowadzenie
W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczka przedstawiono sposób użycia [dwie urządzenia] [ lnk-devtwin] i [bezpośrednie metody ] [ lnk-c2dmethod] podstawowych zdalnie ponownego uruchomienia urządzenia. Ten samouczek używa tego samego podstawowych Centrum IoT i pokazuje, jak wykonać aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany w implementacji aktualizacji oprogramowania układowego dla [Pi malina urządzenia implementacji próbki][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsoli .NET, który wywołuje **firmwareUpdate** metoda bezpośrednia w aplikacji symulowane urządzenie za pomocą Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie, który implementuje **firmwareUpdate** metoda bezpośrednia. Ta metoda inicjuje procesem wieloetapowym czeka na pobranie obrazu oprogramowania układowego, pobierze obraz oprogramowania układowego i na koniec dotyczy oprogramowania układowego obrazu. Podczas każdego etapu aktualizacji urządzenie korzysta z właściwości zgłoszony do raport dotyczący postępu.

Na końcu tego samouczka masz urządzenie aplikacji konsoli .NET (C#) i zaplecza aplikacji konsoli .NET (C#):

* **SimulatedDeviceFwUpdate**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje **firmwareUpdate** metoda, uruchamia proces wielu stanu do symulowania aktualizacji oprogramowania układowego bezpośrednia w tym: oczekuje do pobrania obrazu, pobierania nowego obrazu, a na koniec stosowania obrazu.

* **TriggerFWUpdate**, który korzysta z usługi SDK można zdalnie wywołać **firmwareUpdate** bezpośredniego metoda symulowane urządzenie Wyświetla odpowiedzi i okresowo (co 500 MS.) Wyświetla zaktualizowanego zgłoszone właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Postępuj zgodnie z [wprowadzenie do zarządzania urządzeniami](iot-hub-csharp-csharp-device-management-get-started.md) artykuł, aby utworzyć Centrum IoT i pobrać parametry połączenia Centrum IoT.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji służy do tworzenia aplikacji konsoli .NET (przy użyciu języka C#) inicjującym aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja korzysta metoda bezpośrednia zainicjować aktualizację, a zapytania dwie urządzenia okresowo pobrać stanu aktualizacji oprogramowania układowego active.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **TriggerFWUpdate**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createserviceapp]

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **TriggerFWUpdate** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
3. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp symbole zastępcze wielu Centrum IoT ciąg połączenia dla koncentratora, który został utworzony w poprzedniej sekcji oraz identyfikator urządzenia.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Dodaj następującą metodę do klasy **Program**. Ta metoda sprawdza dwie urządzenia zaktualizowany stan co 500 milisekund. Zapisuje do konsoli tylko wtedy, gdy stan faktycznie został zmieniony. Dla tego przykładu, aby uniemożliwić korzystanie z dodatkowych komunikatów Centrum IoT w ramach subskrypcji, sondowanie zatrzymuje, gdy urządzenie zgłasza stan **applyComplete** lub wystąpił błąd.  
   
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

8. Na koniec należy dodać następujące wiersze do **Main** metody. Tworzy rejestru Menedżera odczytać dwie urządzenia z uruchamia zadanie sondowania w wątku roboczego, a następnie wyzwala aktualizacji oprogramowania układowego.
   
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

* Tworzenie aplikacji konsoli .NET, które odpowiada metoda bezpośrednia wywoływane przez chmury
* Symulowanie aktualizacji oprogramowania układowego wyzwalane przez usługi wewnętrznej bazy danych za pomocą bezpośrednich — metoda
* Włączanie zapytań bliźniaczych reprezentacji urządzeń przy użyciu zgłoszonych właściwości w celu zidentyfikowania urządzeń i ustalenia, kiedy ostatnio przeprowadzono na nich aktualizację oprogramowania układowego

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **SimulateDeviceFWUpdate**.
   
    ![Nowa aplikacja Visual C# Windows Classic urządzenia][img-createdeviceapp]
    
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateDeviceFWUpdate** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** do zainstalowania **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura pliki do pobrania, instaluje i dodaje odwołanie do [urządzenia Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet pakiet i jego zależności.
   
    ![Aplikacja kliencka okna Menedżera pakietów NuGet][img-clientnuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego zanotowaną w ciągu połączenia urządzenia **tworzenie tożsamości urządzenia** sekcji.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Dodaj następującą metodę w celu zgłoszenia stanu do chmury przez dwie urządzenia: 

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

7. Dodaj następującą metodę do symulowania pobierania oprogramowania układowego obrazu:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Dodaj następującą metodę do symulowania stosowania obrazu oprogramowania układowego na urządzeniu:
        
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
 
9.  Dodaj następującą metodę do symulowania oczekujących na pobranie obrazu oprogramowania układowego. Zmiana stanu na **oczekiwania** i usuń zaznaczenie innych właściwości aktualizacji oprogramowania układowego na dwie. Te właściwości zostaną wyczyszczone, aby usunąć wszystkie istniejące wartości z aktualizacji oprogramowania układowego wcześniejszych. Jest to konieczne, ponieważ właściwości zgłoszone są wysyłane jako operacją poprawki (delta), a nie operacji PUT (kompletny zestaw właściwości zastępuje wszystkie poprzednie wartości). Zazwyczaj urządzenia otrzymują informacje o dostępnej aktualizacji, a zasady określone przez administratora powodują, że urządzenie zaczyna pobierać i stosować aktualizację. To w tej funkcji powinna zostać uruchomiona logika włączająca te zasady. 
        
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

10. Dodaj następującą metodę w celu wykonania pobierania. Aktualizuje stan **pobieranie** przez dwie urządzenia wywołuje metodę pobierania Symulacja i raporty o stanie **downloadComplete** lub **downloadFailed** za pomocą dwie w zależności od wyników operacji pobierania. 
        
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

11. Dodaj następującą metodę w celu zastosowania obrazu. Aktualizuje stan **stosowania** przez dwie urządzenia wywołania Symulacja zastosowanie obrazu metoda i stan aktualizacji do **applyComplete** lub **applyFailed** za pośrednictwem dwie w zależności od wyników operacji zastosowania. 
        
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

12. Dodaj następującą metodę do sekwencji operacji aktualizacji oprogramowania układowego oczekujących na pobranie obrazu do stosowania obrazu na urządzeniu:
        
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

13. Dodaj następującą metodę do obsługi **updateFirmware** bezpośrednie metody z chmury. Wyodrębnia adres URL do aktualizacji oprogramowania układowego z ładunek komunikatu i przekazuje go do **doUpdate** zadania, która rozpoczyna się on w innym wątku puli wątków. Następnie natychmiast zwraca odpowiedź metody do chmury.
        
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
> Ta metoda jest wyzwalane symulowane aktualizacji do uruchamiania jako **zadań** i natychmiast odpowiada do wywołania metody informowania usługę uruchomienia aktualizacji oprogramowania układowego. Stan aktualizacji i zakończeniu zostanie wysłane do usługi za pośrednictwem właściwości zgłoszone dwie urządzenia. Odpowiemy do wywołania metody podczas uruchamiania aktualizacji, a nie po ich zakończeniu, ponieważ:
> * Proces aktualizacji rzeczywistych jest bardzo może trwać dłużej niż limit czasu wywołania metody.
> * Proces aktualizacji rzeczywistych jest bardzo prawdopodobne wymagać ponownego uruchomienia komputera, który będzie ponownie uruchom to tworzenie aplikacji **MetodRequest** obiektu jest niedostępny. (Aktualizowanie właściwości zgłoszone, jednak jest możliwe nawet po ponownym uruchomieniu). 

14. Na koniec należy dodać następujący kod, aby **Main** metodę, aby otworzyć połączenia z Centrum IoT i zainicjować odbiornika metody:
   
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
1. Uruchamianie aplikacji .NET urządzenia **SimulatedDeviceFWUpdate**.  Kliknij prawym przyciskiem myszy **SimulatedDeviceFWUpdate** projektu, zaznacz **debugowania**, a następnie wybierz **Start nowe wystąpienie**. Należy uruchomić, nasłuchiwania dla wywołań metod z Centrum IoT: 

2. Gdy urządzenie jest podłączone i Oczekiwanie na wywołania metody uruchamiania programu .NET **TriggerFWUpdate** aplikacji można wywołać metody aktualizacji oprogramowania układowego w aplikacji symulowane urządzenie. Kliknij prawym przyciskiem myszy **TriggerFWUpdate** projektu, zaznacz **debugowania**, a następnie wybierz **Start nowe wystąpienie**. Powinny pojawić się zaktualizować sekwencję napisana **SimulatedDeviceFWUpdate** konsoli, a także sekwencja zgłoszone za pośrednictwem właściwości zgłoszonego urządzenia w **TriggerFWUpdate** konsoli. Należy pamiętać, że ten proces trwa kilka sekund. 
   
    ![Aplikacja usługi i urządzenia, uruchom][img-combinedrun]


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używana metoda bezpośrednia do wyzwolenia aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postępy aktualizacji oprogramowania układowego zgłoszone właściwości.

Aby dowiedzieć się, jak rozszerzyć IoT, Twoje rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach, zobacz [emisji zadania i harmonogramu] [ lnk-tutorial-jobs] samouczka.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/