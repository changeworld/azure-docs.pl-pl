---
title: Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi Azure IoT Hub (portal i platforma .NET) | Microsoft Docs
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczą reprezentację modułu przy użyciu portalu i platformy.NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: ce71c64aff66ea94282a82c1f1b1ee564e74f192
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403891"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Rozpoczynanie pracy z tożsamością modułu i bliźniaczą reprezentacją modułu usługi IoT Hub przy użyciu portalu i urządzenia platformy .NET

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.
>

Z tego samouczka dowiesz się:

1. Jak utworzyć tożsamość modułu w portalu.

2. Jak używać zestawu SDK urządzenia .NET do aktualizowania sznurka modułu z urządzenia.

> [!NOTE]
> Aby uzyskać informacje na temat zestawów SDK usługi Azure IoT, których można użyć do kompilowania aplikacji do uruchamiania na urządzeniach oraz zaplecze rozwiązania, zobacz [zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md).
>

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio.
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Tworzenie tożsamości modułu w portalu

W ramach jednej tożsamości urządzenia możesz utworzyć maksymalnie 20 tożsamości modułu. Kliknij znajdujący się u góry przycisk **Dodaj tożsamość modułu**, aby utworzyć pierwszą tożsamość modułu o nazwie **myFirstModule**.

  ![Szczegóły urządzenia](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Zapisz i kliknij właśnie utworzoną tożsamość modułu. Zostaną wyświetlone szczegóły tożsamości modułu. Zapisz wartość pola Parametry połączenia — klucz podstawowy. Zostanie ona użyta w następnej sekcji podczas konfigurowania modułu na urządzeniu.

  ![Szczegóły urządzenia](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu zestawu SDK urządzenia platformy .NET

Pomyślnie utworzono tożsamość modułu w centrum IoT Hub. Spróbujmy nawiązać połączenie z chmurą z poziomu urządzenia symulowanego. Po utworzeniu tożsamości modułu w centrum IoT Hub jest niejawnie tworzona bliźniacza reprezentacja modułu. W tej sekcji utworzysz na urządzeniu symulowanym aplikację konsolową .NET, która aktualizuje zgłoszone właściwości bliźniaczej reprezentacji modułu.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

W programie Visual Studio Dodaj projekt Visual C# Windows Classic Desktop do istniejącego rozwiązania, używając szablonu projektu **aplikacja konsoli (.NET Framework)** . Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej. Nadaj projektowi nazwę **UpdateModuleTwinReportedProperties**.

  ![Tworzenie projektu programu Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Zainstaluj najnowszą wersję zestawu SDK urządzeń usługi Azure IoT Hub .NET

Tożsamość modułu i sznurki modułu są w publicznej wersji zapoznawczej. Jest on dostępny tylko w zestawach SDK urządzeń IoT Hub w wersji wstępnej. W programie Visual Studio wybierz pozycję Narzędzia > Menedżer pakietów NuGet > Zarządzaj pakietami NuGet dla rozwiązania. Wyszukaj ciąg Microsoft.Azure.Devices.Client. Upewnij się, że zaznaczono pole wyboru Uwzględnij wersję wstępną. Wybierz najnowszą wersję i zainstaluj. Teraz masz dostęp do wszystkich funkcji modułu.

  ![Instalowanie zestawu SDK usługi platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Pobierz parametry połączenia modułu

Zaloguj się w [portalu Azure](https://portal.azure.com/). Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź myFirstDevice, otwórz go i zobaczysz, że myFirstModule został pomyślnie utworzony. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

  ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Tworzenie aplikacji konsolowej UpdateModuleTwinReportedProperties

Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego na parametry połączenia modułu.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Ten przykładowy kod przedstawia sposób pobierania bliźniaczej reprezentacji modułu i aktualizacji zgłoszonych właściwości za pomocą protokołu AMQP. W publicznej wersji zapoznawczej na potrzeby operacji bliźniaczych reprezentacji modułów obsługujemy tylko protokół AMQP.

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji. W programie Visual Studio w Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij przycisk **Ustaw projekty startowe**. Wybierz pozycję **Wiele projektów startowych**, a następnie wybierz pozycję **Uruchom** jako akcję dla aplikacji konsolowej. Naciśnij klawisz F5, aby uruchomić obie uruchomione aplikacje.

## <a name="next-steps"></a>Kolejne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Rozpoczynanie pracy z modułem IoT Hub tożsamość i moduły modułu przy użyciu programu .NET Backup i urządzenia .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Wprowadzenie do IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
