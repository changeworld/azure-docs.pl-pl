---
title: Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi Azure IoT Hub (platforma .NET) | Microsoft Docs
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu zestawów SDK IoT dla platformy .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: ea42a459365848386082470deb5120e09cb92421
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668154"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Rozpoczynanie pracy z modułem IoT Hub Identity and module (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe .NET:

* **CreateIdentities**, która tworzy tożsamość urządzenia, tożsamość modułu oraz skojarzony klucz zabezpieczeń na potrzeby łączenia klientów modułu i urządzenia.

* **UpdateModuleTwinReportedProperties**, która wysyła zaktualizowane zgłoszone właściwości bliźniaczej reprezentacji modułu do Twojego centrum IoT Hub.

> [!NOTE]
> Aby uzyskać informacje na temat zestawów SDK usługi Azure IoT, których można użyć do kompilowania aplikacji do uruchamiania na urządzeniach oraz zaplecze rozwiązania, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

W tej sekcji tworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

1. **Utwórz projekt programu Visual Studio:** W programie Visual Studio Dodaj projekt Visual C# Windows Classic Desktop do istniejącego rozwiązania, używając szablonu projektu **aplikacja konsoli (.NET Framework)** . Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej. Nadaj projektowi nazwę **UpdateModuleTwinReportedProperties**.

    ![Tworzenie projektu programu Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Zainstaluj najnowszą wersję zestawu SDK urządzeń usługi Azure IoT Hub .NET:** Tożsamość modułu i sznurki modułu są w publicznej wersji zapoznawczej. Jest on dostępny tylko w IoT Hub zestaw SDK urządzeń wersji wstępnej. W programie Visual Studio wybierz pozycję Narzędzia > Menedżer pakietów NuGet > Zarządzaj pakietami NuGet dla rozwiązania. Wyszukaj ciąg Microsoft.Azure.Devices.Client. Upewnij się, że zaznaczono pole wyboru Uwzględnij wersję wstępną. Wybierz najnowszą wersję i zainstaluj. Teraz masz dostęp do wszystkich funkcji modułu. 

    ![Instalowanie zestawu SDK usługi platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Pobierz parametry połączenia modułu** — teraz, jeśli zalogujesz się do [Azure Portal](https://portal.azure.com/). Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź myFirstDevice, otwórz go i zobaczysz, że myFirstModule został pomyślnie utworzony. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

    ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Tworzenie aplikacji konsolowej UpdateModuleTwinReportedProperties**

    Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego na parametry połączenia modułu.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Dodaj następującą metodę **OnDesiredPropertyChanged** do klasy **Program**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
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

5. Oprócz powyższej metody **Main** można dodać poniższy blok kodu w celu wysłania zdarzenia do IoT Hub z modułu:

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

* [Wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md)
* [Wprowadzenie do IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
