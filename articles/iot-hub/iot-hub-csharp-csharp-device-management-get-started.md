---
title: Wprowadzenie do zarządzania urządzeniami w usłudze Azure IoT Hub (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami Centrum IoT Azure do zainicjowania ponownego uruchomienia urządzenia zdalnego. Urządzenia Azure IoT SDK dla platformy .NET użyta do wdrożenia aplikacji symulowane urządzenie, która zawiera metoda bezpośrednia i usługę Azure IoT SDK dla platformy .NET zaimplementować aplikację usługi, która wywołuje metodę bezpośredniego.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: v-jamebr;dobett
ms.openlocfilehash: 44160eeb90f0f65c974b7188dd7c70cce382bf21
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-device-management-netnet"></a>Wprowadzenie do zarządzania urządzeniami (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj portalu Azure do tworzenia Centrum IoT i tworzenia tożsamości urządzenia w Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie zawierającej bezpośredniego metodę, która wykonuje ponowny rozruch tego urządzenia. Bezpośrednie metody są wywoływane z chmury.
* Tworzenie aplikacji konsoli .NET, który wywołuje metodę bezpośredniego ponowny rozruch w aplikacji symulowane urządzenie za pomocą Centrum IoT.

Na końcu tego samouczka masz dwie aplikacji konsoli .NET:

* **SimulateManagedDevice**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje metoda bezpośrednia ponowny rozruch, symuluje ponowny rozruch komputera fizycznego i raporty czasu ostatniego ponownego uruchomienia.
* **TriggerReboot**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i wyświetla zaktualizowane zgłoszone właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalacz zdalnego ponownego uruchomienia na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji służy do tworzenia aplikacji konsoli .NET (przy użyciu języka C#) inicjującym zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniego. Aplikacja używa urządzenia dwie zapytania do odnajdywania ostatniego ponownego uruchomienia dla tego urządzenia.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do nowego rozwiązania, używając szablonu projektu **Aplikacja konsoli (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.5.1 lub nowszej. Nazwij projekt **TriggerReboot**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createserviceapp]

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **TriggerReboot** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
3. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego Centrum IoT ciąg połączenia dla koncentratora, który został utworzony w sekcji "Utwórz Centrum IoT." 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Dodaj następującą metodę do klasy **Program**.  Ten kod pobiera dwie urządzenia rebooting urządzenia i danych wyjściowych zgłoszone właściwości.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Dodaj następującą metodę do klasy **Program**.  Ten kod inicjuje ponownego uruchomienia na urządzeniu, za pomocą metody bezpośredniego.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Na koniec dodaj następujące wiersze do metody **Główne**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Skompiluj rozwiązanie.

> [!NOTE]
> W tym samouczku wykonuje tylko pojedynczego zapytania dla właściwości zgłoszonego urządzenia. W kodzie produkcyjnym zaleca się sondowania, aby wykryć zmiany we właściwościach zgłoszony.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji zostanie

* Tworzenie aplikacji konsoli .NET, które odpowiada metoda bezpośrednia wywoływane przez chmury
* Wyzwalacz symulowane urządzenie ponowny rozruch
* Włącz zapytania dwie urządzenia w celu identyfikowania urządzeń przy użyciu właściwości zgłoszone, a podczas ostatniego ponownego uruchomienia

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **SimulateManagedDevice**.
   
    ![Nowa aplikacja Visual C# Windows Classic urządzenia][img-createdeviceapp]
    
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateManagedDevice** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** do zainstalowania **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura pliki do pobrania, instaluje i dodaje odwołanie do [urządzenia Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet pakiet i jego zależności.
   
    ![Aplikacja kliencka okna Menedżera pakietów NuGet][img-clientnuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego ciąg połączenia urządzenia zanotowanym w poprzedniej sekcji.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Dodaj następujący kod do implementacji metody bezpośredniej na urządzeniu:

        static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
        {
            // In a production device, you would trigger a reboot scheduled to start after this method returns
            // For this sample, we simulate the reboot by writing to the console and updating the reported properties 
            try
            {
                Console.WriteLine("Rebooting!");

                // Update device twin with reboot time. 
                TwinCollection reportedProperties, reboot, lastReboot;
                lastReboot = new TwinCollection();
                reboot = new TwinCollection();
                reportedProperties = new TwinCollection();
                lastReboot["lastReboot"] = DateTime.Now;
                reboot["reboot"] = lastReboot;
                reportedProperties["iothubDM"] = reboot;
                Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }

            string result = "'Reboot started.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

7. Na koniec należy dodać następujący kod, aby **Main** metodę, aby otworzyć połączenia z Centrum IoT i zainicjować odbiornika metody:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "reboot" method
            Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
            Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "reboot" handler
            Client.SetMethodHandlerAsync("reboot", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
8. W Eksploratorze rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **Ustaw projekty startowe...** . Wybierz **jednego projektu startowego**, a następnie wybierz **SimulateManagedDevice** projektu w menu rozwijanym. Skompiluj rozwiązanie.       

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.
1. Uruchamianie aplikacji .NET urządzenia **SimulateManagedDevice**.  Kliknij prawym przyciskiem myszy **SimulateManagedDevice** projektu, zaznacz **debugowania**, a następnie wybierz **Start nowe wystąpienie**. Należy uruchomić, nasłuchiwania dla wywołań metod z Centrum IoT: 

2. Teraz, gdy urządzenie jest podłączone i Oczekiwanie na wywołania metody uruchamiania programu .NET **TriggerReboot** aplikacji można wywołać metody ponownego uruchomienia w aplikacji symulowane urządzenie. Kliknij prawym przyciskiem myszy **TriggerReboot** projektu, zaznacz **debugowania**, a następnie wybierz **Start nowe wystąpienie**. Powinny pojawić się "Ponowne uruchomienie!" napisana **SimulatedManagedDevice** konsoli i zgłoszone właściwości urządzenia, które obejmują ostatniego ponownego rozruchu czasu napisany w **TriggerReboot** konsoli.
   
    ![Aplikacja usługi i urządzenia, uruchom][img-combinedrun]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
