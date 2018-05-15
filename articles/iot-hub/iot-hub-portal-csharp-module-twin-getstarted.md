---
title: Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi Azure IoT Hub (portal i platforma .NET) | Microsoft Docs
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu portalu i platformy.NET.
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99b0acbf6461750c2606cc3d4338c10b03a4b430
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi IoT Hub przy użyciu portalu i urządzenia platformy .NET

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności: 
1. Tworzenie tożsamości modułu w portalu. 
2. Aktualizowanie bliźniaczej reprezentacji modułu za pomocą zestawu SDK urządzenia platformy .NET z poziomu Twojego urządzenia.

> [!NOTE]
> Artykuł [Zestawy SDK usługi Azure IoT][lnk-hub-sdks] zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Tworzenie tożsamości urządzenia w portalu

Teraz masz własne centrum IoT Hub. Otwórz [portal](https://portal.azure.com) i przejdź do swojego centrum IoT Hub. Kliknij pozycję Urządzenia IoT, a następnie kliknij przycisk Dodaj w celu utworzenia tożsamości urządzenia. Nadaj jej nazwę **MyFirstDevice**. 

![Tworzenie tożsamości urządzenia][8]

Po zapisaniu na liście tożsamości urządzeń można zobaczyć, że tożsamość MyFirstDevice została pomyślnie utworzona.

![Utworzona tożsamość urządzenia][11]

Teraz kliknij wiersz. Zostaną wyświetlone szczegóły urządzenia.

![Szczegóły urządzenia][10]

## <a name="create-a-module-identity-in-the-portal"></a>Tworzenie tożsamości modułu w portalu

W ramach jednej tożsamości urządzenia możesz utworzyć maksymalnie 20 tożsamości modułu. Kliknij znajdujący się u góry przycisk **Dodaj tożsamość modułu**, aby utworzyć pierwszą tożsamość modułu o nazwie **myFirstModule**. 

![Szczegóły urządzenia][9]

Zapisz i kliknij właśnie utworzoną tożsamość modułu. Zostaną wyświetlone szczegóły tożsamości modułu. Zapisz wartość pola Parametry połączenia — klucz podstawowy. Zostanie ona użyta w następnej sekcji podczas konfigurowania modułu na urządzeniu.

![Szczegóły urządzenia][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

Pomyślnie utworzono tożsamość modułu w centrum IoT Hub. Spróbujmy nawiązać połączenie z chmurą z poziomu urządzenia symulowanego. Po utworzeniu tożsamości modułu w centrum IoT Hub jest niejawnie tworzona bliźniacza reprezentacja modułu. W tej sekcji utworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

1. **Utwórz projekt programu Visual Studio** — w programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do istniejącego rozwiązania, używając szablonu projektu **Aplikacja konsolowa (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej. Nadaj projektowi nazwę **UpdateModuleTwinReportedProperties**.

    ![Tworzenie projektu programu Visual Studio][13]

2. **Zainstaluj zestaw SDK urządzenia platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-005** — tożsamość modułu i bliźniacza reprezentacja modułu są w publicznej wersji zapoznawczej. Są one dostępne tylko w wersjach wstępnych zestawów SDK urządzeń usługi IoT Hub. W programie Visual Studio wybierz pozycję Narzędzia > Menedżer pakietów NuGet > Zarządzaj pakietami NuGet dla rozwiązania. Wyszukaj ciąg Microsoft.Azure.Devices.Client. Upewnij się, że zaznaczono pole wyboru Uwzględnij wersję wstępną. Wybierz wersję 1.16.0-preview-005 i zainstaluj ją. Teraz masz dostęp do wszystkich funkcji modułu. 

    ![Instalowanie zestawu SDK usługi platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-005][14]

3. **Pobierz parametry połączenia modułu** — teraz zaloguj się do witryny [Azure Portal][lnk-portal]. Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź tożsamość myFirstDevice i otwórz ją. Zobaczysz, że tożsamość myFirstModule została pomyślnie utworzona. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

    ![Szczegóły modułu w witrynie Azure Portal][15]

4. **Utwórz aplikację konsolową UpdateModuleTwinReportedProperties** — dodaj następujące instrukcje `using` na początku pliku **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego na parametry połączenia modułu.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static DeviceClient Client = null;
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
            Client = DeviceClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

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
        Client.CloseAsync().Wait();
    }
    ```

    Ten przykładowy kod przedstawia sposób pobierania bliźniaczej reprezentacji modułu i aktualizacji zgłoszonych właściwości za pomocą protokołu AMQP. W publicznej wersji zapoznawczej na potrzeby operacji bliźniaczych reprezentacji modułów obsługujemy tylko protokół AMQP.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
