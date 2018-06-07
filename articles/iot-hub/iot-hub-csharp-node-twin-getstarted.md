---
title: Rozpoczynanie pracy z Centrum IoT Azure urządzenia twins (.NET/węzła) | Dokumentacja firmy Microsoft
description: Jak używać twins urządzenia Azure IoT Hub Dodawanie tagów, a następnie użyć kwerendy Centrum IoT. Przy użyciu urządzenia Azure IoT SDK dla środowiska Node.js usługi Azure IoT SDK dla platformy .NET do implementacji usługi aplikacji, która dodaje znaczniki i uruchamia kwerendy Centrum IoT i aplikacji symulowane urządzenie.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: e2d78fe3f43c493eb126af86580ee473a6aa18c2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634424"
---
# <a name="get-started-with-device-twins-netnode"></a>Rozpoczynanie pracy z urządzenia twins (.NET/węzeł)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka konieczne będzie .NET i Node.js aplikację konsoli:

* **AddTagsAndQuery.sln**, aplikacji zaplecza .NET, która dodaje znaczniki i zapytanie twins urządzenia.
* **TwinSimulatedDevice.js**, aplikacji Node.js, która symuluje urządzenie, które łączy do Centrum IoT z tożsamości urządzenia utworzony wcześniej, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK] [ lnk-hub-sdks] informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka należy spełnić następujące warunki:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi
W tej sekcji Tworzenie aplikacji konsoli .NET (przy użyciu języka C#) umożliwiający dodawanie metadanych lokalizacji do dwie urządzeń skojarzonych z **myDeviceId**. Tworzy następnie kwerendę twins urządzenia przechowywane w Centrum IoT Wybieranie urządzeń znajdujących się w Stanach Zjednoczonych i te, które połączenie transmisji.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **AddTagsAndQuery**.
   
    ![Nowy projekt Visual C# Windows Classic Desktop][img-createapp]
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **AddTagsAndQuery** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
1. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices**. Wybierz **zainstalować** do zainstalowania **Microsoft.Azure.Devices** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.
   
    ![Okno Menedżera pakietów NuGet][img-servicenuget]
1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices;
1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia usługi IoT Hub dla centrum IoT utworzonego w poprzedniej sekcji.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Dodaj następującą metodę do klasy **Program**:
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** klasy udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z usługi. Poprzedni kod najpierw inicjuje **registryManager** obiekt, a następnie pobiera dwie urządzenia dla **myDeviceId**i na koniec aktualizuje jego tagi informacji żądaną lokalizację.
   
    Po zaktualizowaniu, wykonuje on dwa zapytania: pierwszy wybiera tylko twins urządzenia urządzeń znajdujących się w **Redmond43** zakładu, a drugi udoskonalanie zapytanie, aby wybrać tylko te urządzenia, które także są połączone za pośrednictwem sieci komórkowej.
   
    Należy pamiętać, że poprzedni kod, w którym tworzy **zapytania** obiektów, określa maksymalną liczbę zwracanych dokumentów. **Zapytania** zawiera obiekt **HasMoreResults** właściwości typu boolean, którego można użyć do wywołania **GetNextAsTwinAsync** metody kilka razy, aby pobrać wszystkie wyniki. Wywołana metoda **GetNextAsJson** jest dostępna dla wyników, które są nie twins urządzenia, na przykład wyniki zapytań agregacji.
1. Na koniec dodaj następujące wiersze do metody **Główne**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. W Eksploratorze rozwiązań Otwórz **projektów startowych ustawić...**  i upewnij się, że **akcji** dla **AddTagsAndQuery** projekt jest **Start**. Skompiluj rozwiązanie.
1. Uruchomić tę aplikację, klikając prawym przyciskiem myszy **AddTagsAndQuery** projekt i wybierając **debugowania**, a następnie **Start nowe wystąpienie**. Jedno urządzenie w wynikach zadać kwerendy powinna być widoczna dla wszystkich urządzeń znajdujących się w **Redmond43** i Brak dla zapytania, który ogranicza wyniki do urządzenia, które korzystają z sieci komórkowej.
   
    ![Wyniki zapytania w oknie][img-addtagapp]

W następnej sekcji utworzysz aplikację urządzenia, która raportuje informacje dotyczące łączności i zmienia się wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzeń
W tej sekcji zostanie utworzona aplikacja konsoli Node.js łączący się do Centrum jako **myDeviceId**, a następnie aktualizuje jego zgłoszone właściwości zawierają informacje, że jest połączony za pomocą sieci komórkowej.

1. Utwórz nowy, pusty folder o nazwie **reportconnectivity**. W **reportconnectivity** folderu, Utwórz nowy plik package.json za pomocą następującego polecenia z wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne.
   
    ```
    npm init
    ```
1. Z wiersza polecenia w **reportconnectivity** folderu, uruchom następujące polecenie, aby zainstalować **azure iot urządzenia**, i **azure-iot urządzenie mqtt** pakietu:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. Za pomocą edytora tekstu, Utwórz nową **ReportConnectivity.js** w pliku **reportconnectivity** folderu.
1. Dodaj następujący kod do **ReportConnectivity.js** plików i Zastąp symbol zastępczy parametrów połączenia urządzenia z skopiowane podczas tworzenia **myDeviceId** tożsamości urządzenia:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z urządzenia. Poprzedni kod po jego inicjuje **klienta** obiektów, pobiera dwie urządzenia dla **myDeviceId** i aktualizuje jego właściwość zgłoszone informacje o łączności.
1. Uruchamianie aplikacji urządzeń
   
        node ReportConnectivity.js
   
    Powinien zostać wyświetlony komunikat `twin state reported`.
1. Teraz, gdy urządzenie zgłosiło jego informacje dotyczące łączności, powinna pojawić się w obu zapytania. Uruchamianie programu .NET **AddTagsAndQuery** aplikacji, aby ponownie uruchomić zapytania. Teraz **myDeviceId** powinny być wyświetlane w obu wyników zapytania.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodaje metadane urządzenia jako tagi z aplikacji zaplecza i zapisano aplikacji symulowane urządzenie informacji w raporcie urządzenia łączności w dwie urządzenia. Przedstawiono również sposób kwerendy te informacje przy użyciu języka przypominającego SQL Centrum IoT zapytania.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT] [ lnk-iothub-getstarted] samouczka
* Konfigurowanie urządzeń przy użyciu właściwości żądaną dwie urządzenia z [Użyj żądanego właściwości, aby skonfigurować urządzenia] [ lnk-twin-how-to-configure] samouczka
* kontrolowanie urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) z [metody bezpośredniego] [ lnk-methods-tutorial] samouczka.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

