---
title: Wprowadzenie do zarządzania urządzeniami Azure IoT Hub (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak zainicjować ponownego uruchomienia urządzenia zdalnego za pomocą zarządzania urządzeniami Azure IoT Hub. Urządzenia usługi Azure IoT SDK dla platformy .NET umożliwia implementowanie aplikacji symulowanego urządzenia, która obejmuje metody bezpośredniej i usługi Azure IoT SDK dla platformy .NET w celu zaimplementowania app service, która wywołuje metody bezpośredniej.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: c1cee32e3ee60ce229308055cca7f0e9832ddc49
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578904"
---
# <a name="get-started-with-device-management-netnet"></a>Wprowadzenie do zarządzania urządzeniami (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure portal do utworzenia Centrum IoT, a następnie tworzenie tożsamości urządzenia w usłudze IoT hub.
* Tworzenie aplikacji symulowanego urządzenia, która zawiera bezpośrednie metodę, która wywołuje ponowne uruchomienie tego urządzenia. Metody bezpośrednie są wywoływane z poziomu chmury.
* Tworzenie aplikacji konsolowej .NET, która wywołuje metody bezpośredniej ponowny rozruch w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **SimulateManagedDevice**, łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje metody bezpośredniej o ponowne uruchomienie komputera, symuluje ponowne uruchomienie komputera fizycznego, a następnie raportuje czas ostatniego ponownego uruchomienia.
* **TriggerReboot**, która wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia, wyświetla odpowiedzi i wyświetla zaktualizowany zgłoszonych właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Zdalne ponowne uruchamianie systemu na urządzeniu, korzystając z metody bezpośredniej wyzwalacza
W tej sekcji służy do tworzenia aplikacji konsolowej .NET (przy użyciu języka C#) inicjujący zdalnego ponowny rozruch na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczych reprezentacji urządzeń, aby odnaleźć ostatniego ponownego uruchomienia dla tego urządzenia.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do nowego rozwiązania, używając szablonu projektu **Aplikacja konsoli (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.5.1 lub nowszej. Nadaj projektowi nazwę **TriggerReboot**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createserviceapp]

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **TriggerReboot** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.
3. W oknie **Menedżer pakietów NuGet** wybierz opcję **Przeglądaj**, wyszukaj pozycję **microsoft.azure.devices**, wybierz opcję **Zainstaluj**, aby zainstalować pakiet **Microsoft.Azure.Devices**, i zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.

    ![Okno Menedżera pakietów NuGet][img-servicenuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia Centrum IoT Hub dla Centrum utworzonego w sekcji "Tworzenie Centrum IoT". 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Dodaj następującą metodę do klasy **Program**.  Ten kod pobiera bliźniaczą reprezentację urządzenia dla trwa jego ponowne uruchamianie urządzenia i wysyła zgłaszane właściwości.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Dodaj następującą metodę do klasy **Program**.  Ten kod powoduje zainicjowanie ponowny rozruch na urządzeniu, korzystając z metody bezpośredniej.

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
> W tym samouczku wykonuje jedno zapytanie zgłaszanych właściwości na urządzeniu. W kodzie produkcyjnym zalecamy sondowania, aby wykryć zmiany w zgłaszanych właściwości.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji zostanie

* Tworzenie aplikacji konsolowej .NET, która reaguje na metodę bezpośrednią wywołaną przez chmurę
* Wyzwolić ponowne uruchomienie symulowanego urządzenia
* Włączanie zapytań bliźniaczych reprezentacji urządzeń do identyfikowania urządzeń za pomocą zgłoszonych właściwości, a podczas ostatniego ponownego uruchomienia

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **SimulateManagedDevice**.
   
    ![Nowa aplikacja urządzenia Visual C# Windows Classic][img-createdeviceapp]
    
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulateManagedDevice** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
3. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestaw SDK urządzeń Azure IoT] [ lnk-nuget-client-sdk] NuGet pakietu oraz jego zależności.
   
    ![Aplikacja kliencka okno Menedżera pakietów NuGet][img-clientnuget]
4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia zanotowanym w poprzedniej sekcji.
   
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

7. Na koniec Dodaj następujący kod do **Main** metodę, aby otworzyć połączenie do usługi IoT hub i zainicjować odbiornika metody:
   
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
        
8. W Eksploratorze rozwiązań programu Visual Studio kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij polecenie **Ustaw projekty startowe...**. Wybierz **pojedynczy projekt startowy**, a następnie wybierz pozycję **SimulateManagedDevice** project w menu rozwijanym. Skompiluj rozwiązanie.       

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.
1. Uruchom aplikację urządzenia platformy .NET **SimulateManagedDevice**.  Kliknij prawym przyciskiem myszy **SimulateManagedDevice** projektu, wybierz opcję **debugowania**, a następnie wybierz pozycję **Uruchom nowe wystąpienie**. Należy uruchomić, nasłuchiwanie wywołania metody z usługi IoT Hub: 

2. Teraz, gdy urządzenie jest podłączone i Oczekiwanie na wywołania metody uruchamiania .NET **TriggerReboot** aplikacji, które można wywołać metody ponownego uruchomienia w symulowanej aplikacji urządzenia. Kliknij prawym przyciskiem myszy **TriggerReboot** projektu, wybierz opcję **debugowania**, a następnie wybierz pozycję **Uruchom nowe wystąpienie**. Powinien zostać wyświetlony "Ponowne uruchomienie!" napisana **SimulatedManagedDevice** konsoli i zgłaszanych właściwości urządzenia, które obejmują ostatnich ponowny rozruch czasu, napisany w **TriggerReboot** konsoli.
   
    ![Uruchamiania aplikacji usługi i urządzenia][img-combinedrun]

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
