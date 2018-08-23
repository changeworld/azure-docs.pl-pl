---
title: Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi Azure IoT Hub (platforma .NET) | Microsoft Docs
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu zestawów SDK IoT dla platformy .NET.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 8d5d4ab85d8441998fd384e01f85d1d427d68cc2
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058483"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Rozpoczynanie pracy z usługą IoT Hub tożsamości i moduł bliźniaczą reprezentację modułu przy użyciu zaplecza platformy .NET i .NET urządzenia

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **CreateIdentities**, która tworzy tożsamość urządzenia, tożsamość modułu oraz skojarzony klucz zabezpieczeń na potrzeby łączenia klientów modułu i urządzenia.
* **UpdateModuleTwinReportedProperties**, która wysyła zaktualizowane zgłoszone właściwości bliźniaczej reprezentacji modułu do Twojego centrum IoT Hub.

> [!NOTE]
> Artykuł [Azure IoT SDKs][lnk-hub-sdks] (Zestawy SDK usługi Azure IoT) zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Centrum IoT zostało już utworzone i masz nazwę hosta oraz parametry połączenia usługi IoT Hub potrzebne do ukończenia pozostałej części tego samouczka.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

W tej sekcji tworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

1. **Utwórz projekt programu Visual Studio** — w programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do istniejącego rozwiązania, używając szablonu projektu **Aplikacja konsolowa (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej. Nadaj projektowi nazwę **UpdateModuleTwinReportedProperties**.

    ![Tworzenie projektu programu Visual Studio][13]

2. **Zainstaluj najnowszy zestaw SDK urządzeń Azure IoT Hub dla środowiska .NET** -bliźniaczą reprezentację modułu i tożsamości modułu jest w publicznej wersji zapoznawczej. Są one dostępne tylko w wersjach wstępnych zestawów SDK urządzeń usługi IoT Hub. W programie Visual Studio wybierz pozycję Narzędzia > Menedżer pakietów NuGet > Zarządzaj pakietami NuGet dla rozwiązania. Wyszukaj ciąg Microsoft.Azure.Devices.Client. Upewnij się, że zaznaczono pole wyboru Uwzględnij wersję wstępną. Wybierz najnowszą wersję i instalowanie. Teraz masz dostęp do wszystkich funkcji modułu. 

    ![Instalowanie zestawu SDK usługi platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-005][14]

3. **Pobierz parametry połączenia modułu** — teraz zaloguj się do witryny [Azure Portal][lnk-portal]. Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź tożsamość myFirstDevice i otwórz ją. Zobaczysz, że tożsamość myFirstModule została pomyślnie utworzona. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

    ![Szczegóły modułu w witrynie Azure Portal][15]

4. **Utwórz aplikację konsolową UpdateModuleTwinReportedProperties** — dodaj następujące instrukcje `using` na początku pliku **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego na parametry połączenia modułu.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", status, reason);
    }
    ```

    Dodaj następującą metodę **OnDesiredPropertyChanged** do klasy **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Ten przykładowy kod przedstawia sposób pobierania bliźniaczej reprezentacji modułu i aktualizacji zgłoszonych właściwości za pomocą protokołu AMQP. W publicznej wersji zapoznawczej na potrzeby operacji bliźniaczych reprezentacji modułów obsługujemy tylko protokół AMQP.

5. Oprócz powyższych **Main** metody, można dodać poniżej blok kodu do wysyłania zdarzeń do Centrum IoT Hub z modułu:
    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji. W programie Visual Studio w Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **Ustaw projekty startowe**. Wybierz pozycję **Wiele projektów startowych**, a następnie wybierz pozycję **Uruchom** jako akcję dla aplikacji konsolowej. Następnie naciśnij klawisz F5, aby uruchomić aplikację. 

## <a name="next-steps"></a>Kolejne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami][lnk-device-management]
* [Getting started with IoT Edge][lnk-iot-edge] (Wprowadzenie do usługi IoT Edge)


<!-- Images. -->
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
