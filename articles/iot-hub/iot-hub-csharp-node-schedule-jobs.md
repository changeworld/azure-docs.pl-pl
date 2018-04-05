---
title: Planowanie zadań z Centrum IoT Azure (.NET/węzła) | Dokumentacja firmy Microsoft
description: Sposób tworzenia harmonogramu zadań Centrum IoT Azure do wywołania metody bezpośrednio na wielu urządzeniach. Urządzenia Azure IoT SDK dla środowiska Node.js umożliwia wdrożenie symulowane urządzenie aplikacje i usługi Azure IoT SDK dla platformy .NET do implementacji usługi aplikacji, aby uruchomić to zadanie.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: 360daf918051ce901a81f96d1873dc90af238e19
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Zadania harmonogramu i emisji (.NET/Node.js)

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

* Tworzenie aplikacji urządzenia implementujący bezpośredniego metodę o nazwie **lockDoor** który można wywołać za pomocą aplikacji zaplecza. Aplikacji urządzenia Ponadto otrzymuje żądanej właściwości zmiany z poziomu aplikacji zaplecza.
* Tworzenie aplikacji zaplecza, która tworzy zadanie, aby wywołać **lockDoor** metoda bezpośrednia na wielu urządzeniach. Inne zadanie po zaakceptowaniu żądanej właściwości na wielu urządzeniach.

Na końcu tego samouczka masz urządzenie aplikacji konsoli Node.js i zaplecza aplikacji konsoli .NET (C#):

**simDevice.js** które nawiązuje połączenie z Centrum IoT, implementuje **lockDoor** bezpośrednie — metoda i uchwytów potrzebne zmiany właściwości.

**ScheduleJob** używającą zadania do wywołania **lockDoor** metoda bezpośrednia i zaktualizuj właściwości dwie żądanego urządzenia na wielu urządzeniach.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Środowisko Node.js w wersji 4.0.x lub nowszej. Artykuł [przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisuje sposób instalowania programu Node.js w tym samouczku w systemie Windows lub Linux.
* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planowanie zadań dla wywołania metody bezpośredniego i wysyłania aktualizacji dwie urządzenia

W tej sekcji Tworzenie aplikacji konsoli .NET (przy użyciu języka C#) korzystającą z zadań w celu wywołania **lockDoor** metoda bezpośrednia i wysyłać aktualizacje żądanej właściwości na wielu urządzeniach.

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

1. Dodaj następujące pola do klasy **Program**: Zastąp symbol zastępczy parametrów połączenia Centrum IoT dla koncentratora, który został utworzony w poprzedniej sekcji.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
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
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
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

1. W Eksploratorze rozwiązań Otwórz **projektów startowych ustawić...**  i upewnij się, że **akcji** dla **ScheduleJob** projekt jest **Start**. Skompiluj rozwiązanie.

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Node.js, która odpowiada na bezpośrednie metody wywoływane przez chmury, które wyzwala ponowne uruchomienie symulowane urządzenie i używa właściwości zgłoszone, aby umożliwić zapytania dwie urządzenia w celu identyfikowania urządzeń, a podczas ich ostatniego ponownego uruchomienia.

1. Utwórz nowy, pusty folder o nazwie **simDevice**.  W **simDevice** folderu, Utwórz plik package.json za pomocą następującego polecenia z wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

1. Z wiersza polecenia w **simDevice** folderu, uruchom następujące polecenie, aby zainstalować **azure iot urządzenia** i **azure-iot urządzenie mqtt** pakiety:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Za pomocą edytora tekstu, Utwórz nową **simDevice.js** w pliku **simDevice** folderu.

1. Dodaj następujące "wymagane" instrukcje na początku **simDevice.js** pliku:

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**. Upewnij się zastąpić symbole zastępcze wartości ustawień.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Dodaj następującą funkcję obsługi **lockDoor** metody.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Dodaj następujący kod, aby zarejestrować obsługi dla **lockDoor** metody.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Zapisz i Zamknij **simDevice.js** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **simDevice** folderu, uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.

    ```cmd/sh
    node simDevice.js
    ```

1. Uruchom aplikację konsoli języka C# **ScheduleJob** przez kliknięcie prawym przyciskiem myszy **ScheduleJob** projektu, wybierając **debugowania** i **Start nowe wystąpienie**.

1. Możesz wyświetlić dane wyjściowe z urządzeń i aplikacji zaplecza.

    ![Uruchamianie aplikacji do planowania zadań][img-schedulejobs]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto zadanie można zaplanować metoda bezpośrednia urządzenia i aktualizacji właściwości dwie urządzenia.

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT i urządzenia zarządzania wzorców, takich jak zdalnego za pośrednictwem aktualizacji oprogramowania układowego lotniczego, przeczytaj [samouczek: sposób wykonywania aktualizacji oprogramowania układowego][lnk-fwupdate].

Aby dowiedzieć się więcej o wdrażaniu AI na krawędzi urządzenia Azure IoT krawędzi, zobacz [wprowadzenie krawędzi IoT][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
