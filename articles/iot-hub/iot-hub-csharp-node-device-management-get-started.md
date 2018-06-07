---
title: Wprowadzenie do zarządzania urządzeniami Centrum IoT Azure (.NET/węzła) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami Centrum IoT Azure do zainicjowania ponownego uruchomienia urządzenia zdalnego. Przy użyciu urządzenia Azure IoT SDK dla środowiska Node.js aplikacji symulowane urządzenie, która zawiera metoda bezpośrednia i usługę Azure IoT SDK dla platformy .NET zaimplementować aplikację usługi, która wywołuje metodę bezpośredniego.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/05/2017
ms.author: juanpere
ms.openlocfilehash: df41d8b88cf630183afc314dafdc1e898f4e02d9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632241"
---
# <a name="get-started-with-device-management-netnode"></a>Wprowadzenie do zarządzania urządzeniami (.NET/węzeł)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj portalu Azure do tworzenia Centrum IoT i tworzenia tożsamości urządzenia w Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie zawierającej bezpośredniego metodę, która wykonuje ponowny rozruch tego urządzenia. Bezpośrednie metody są wywoływane z chmury.
* Tworzenie aplikacji konsoli .NET, który wywołuje metodę bezpośredniego ponowny rozruch w aplikacji symulowane urządzenie za pomocą Centrum IoT.

Na końcu tego samouczka masz urządzenie aplikacji konsoli Node.js i zaplecza aplikacji konsoli .NET (C#):

**dmpatterns_getstarted_device.js**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje metoda bezpośrednia ponowny rozruch, symuluje ponowny rozruch komputera fizycznego i raporty czasu ostatniego ponownego uruchomienia.

**TriggerReboot**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i wyświetla zaktualizowane zgłoszone właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Środowisko node.js w wersji 4.0.x lub nowszym <br/>  [Przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisuje sposób instalowania programu Node.js w tym samouczku w systemie Windows lub Linux.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalacz zdalnego ponownego uruchomienia na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji służy do tworzenia aplikacji konsoli .NET (przy użyciu języka C#) inicjującym zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniego. Aplikacja używa urządzenia dwie zapytania do odnajdywania ostatniego ponownego uruchomienia dla tego urządzenia.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do nowego rozwiązania, używając szablonu projektu **Aplikacja konsoli (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.5.1 lub nowszej. Nazwij projekt **TriggerReboot**.

    ![Nowy projekt Visual C# Windows Classic Desktop][img-createapp]

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
        static JobClient jobClient;
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

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji zostanie

* Tworzenie aplikacji konsolowej Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę
* Wyzwalacz symulowane urządzenie ponowny rozruch
* Włącz zapytania dwie urządzenia w celu identyfikowania urządzeń przy użyciu właściwości zgłoszone, a podczas ostatniego ponownego uruchomienia

1. Utwórz nowy, pusty folder o nazwie **manageddevice**.  W folderze **manageddevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **manageddevice** folderu, uruchom następujące polecenie, aby zainstalować **azure iot urządzenia** pakiet SDK urządzenia i **azure-iot urządzenie mqtt** pakietu:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Za pomocą edytora tekstu, Utwórz nową **dmpatterns_getstarted_device.js** w pliku **manageddevice** folderu.
4. Dodaj następujące "wymagane" instrukcje na początku **dmpatterns_getstarted_device.js** pliku:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  Parametry połączenia należy zastąpić ciąg połączenia urządzenia.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Dodanie następujących funkcji do zaimplementowania metoda bezpośrednia na urządzeniu
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Dodaj następujący kod, aby otworzyć połączenia z Centrum IoT i uruchomić odbiornik metoda bezpośrednia:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Zapisz i Zamknij **dmpatterns_getstarted_device.js** pliku.
   
> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **manageddevice** folderu, uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Uruchom aplikację konsoli języka C# **TriggerReboot**. Kliknij prawym przyciskiem myszy **TriggerReboot** projektu, zaznacz **debugowania**, a następnie wybierz **Start nowe wystąpienie**.

3. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
