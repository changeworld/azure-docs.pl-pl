---
title: Rozpoczynanie pracy z usługą Azure IoT Hub bliźniaczych reprezentacji urządzeń (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT Hub bliźniaczych reprezentacji urządzeń Dodawanie tagów, a następnie użyć zapytania usługi IoT Hub. Urządzenia usługi Azure IoT SDK dla platformy .NET umożliwia wdrożenie aplikacji symulowanego urządzenia i usługi Azure IoT SDK dla platformy .NET do zaimplementowania app service, który dodaje znaczniki i uruchamia zapytanie usługi IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dobett
ms.openlocfilehash: c598fa5b8f44c6d04d3b4ca6b02b660a4cedaf04
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058481"
---
# <a name="get-started-with-device-twins-netnet"></a>Rozpoczynanie pracy z bliźniaczych reprezentacji urządzeń (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć te aplikacje konsolowe .NET:

* **CreateDeviceIdentity**, aplikację .NET, która tworzy tożsamość urządzenia i skojarzony klucz zabezpieczeń w celu podłączenia aplikacji urządzenia symulowanego.
* **AddTagsAndQuery**, aplikacji zaplecza platformy .NET, która dodaje znaczniki i zapytań bliźniaczych reprezentacji urządzeń.
* **ReportConnectivity**, aplikację urządzenia platformy .NET, która symuluje urządzenie, który nawiązuje połączenie z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDKs] [ lnk-hub-sdks] informacje dotyczące zestawów SDK usługi Azure IoT, w której można tworzyć aplikacje zarówno w przypadku urządzeń, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka potrzebne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi
W tej sekcji, tworzenie aplikacji konsolowej .NET (przy użyciu języka C#) dodającego metadanymi lokalizacji do bliźniaczej reprezentacji urządzenia skojarzone z **myDeviceId**. Następnie wykonuje zapytanie bliźniacze reprezentacje urządzeń, przechowywane w usłudze IoT hub, wybieranie urządzeń znajduje się w Stanach Zjednoczonych i tych, którzy zgłosili połączenie komórkowe.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **AddTagsAndQuery**.
   
    ![Nowy projekt Visual C# Windows Classic Desktop][img-createapp]
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **AddTagsAndQuery** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
1. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT][lnk-nuget-service-sdk] oraz jego zależności.
   
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
   
    **RegistryManager** klasa udostępnia wszystkie metody, które są wymagane do interakcji z bliźniaczych reprezentacji urządzeń z usługi. Poprzedni kod najpierw inicjuje **registryManager** obiektu, a następnie pobiera bliźniaczą reprezentację urządzenia dla **myDeviceId**i aktualizuje jej tagi z informacjami o wybraną lokalizację.
   
    Po zaktualizowaniu, wykonuje dwa zapytania: pierwszy wybiera tylko bliźniaków urządzeń urządzeń znajdujących się w **Redmond43** zakładu produkcyjnego, a drugi usprawniają zapytanie, aby wybrać tylko urządzenia, które są także połączone za pośrednictwem sieci komórkowej.
   
    Należy pamiętać, że poprzedniego kodu, podczas tworzenia **zapytania** obiektów, określa maksymalną liczbę zwróconych dokumentów. **Zapytania** obiekt zawiera **HasMoreResults** właściwość typu boolean, którego można użyć do wywołania **GetNextAsTwinAsync** metody kilka razy, aby pobrać wszystkie wyniki. Metoda wywoływana **GetNextAsJson** jest dostępna dla wyników, które są nie bliźniacze reprezentacje urządzeń, na przykład wyniki zapytań agregacji.
1. Na koniec dodaj następujące wiersze do metody **Główne**:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. W Eksploratorze rozwiązań Otwórz **Ustaw projekty startowe...**  i upewnij się, że **akcji** dla **AddTagsAndQuery** projekt jest **Start**. Skompiluj rozwiązanie.
1. Uruchom tę aplikację, klikając prawym przyciskiem myszy **AddTagsAndQuery** projektu i wybierając polecenie **debugowania**, a następnie **Uruchom nowe wystąpienie**. Powinien zostać wyświetlony jedno urządzenie w wynikach dotyczące zadawania zapytań dla wszystkich urządzeń znajdujących się w **Redmond43** a dla zapytania, które ogranicza wyniki do urządzenia korzystające z sieci komórkowej.
   
    ![Wyniki zapytania w oknie][img-addtagapp]

W następnej sekcji utworzysz aplikację urządzenie, raportuje informacje o łączności, która zmienia się wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia
W tej sekcji utworzysz aplikacji konsolowej .NET, który nawiązuje połączenie z Centrum jako **myDeviceId**, a następnie aktualizuje jego zgłoszonych właściwości zawierają informacje, że jest ona połączona korzystania z sieci komórkowej.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nadaj projektowi nazwę **ReportConnectivity**.
   
    ![Nowa aplikacja urządzenia Visual C# Windows Classic][img-createdeviceapp]
    
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ReportConnectivity** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
1. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** zainstalował **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, instaluje i dodanie odwołania do [zestaw SDK urządzeń Azure IoT] [ lnk-nuget-client-sdk] NuGet pakietu oraz jego zależności.
   
    ![Aplikacja kliencka okno Menedżera pakietów NuGet][img-clientnuget]
1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia zanotowanym w poprzedniej sekcji.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Dodaj następującą metodę do klasy **Program**:

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniaczych reprezentacji urządzeń z urządzenia. Inicjuje kodzie pokazanym powyżej, **klienta** obiektu, a następnie pobiera bliźniaczą reprezentację urządzenia dla **myDeviceId**.

1. Dodaj następującą metodę do klasy **Program**:
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   Kod powyżej aktualizacje **myDeviceId**przez zgłoszone właściwości o informacje o łączności.

1. Na koniec dodaj następujące wiersze do metody **Główne**:
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. W Eksploratorze rozwiązań Otwórz **Ustaw projekty startowe...**  i upewnij się, że **akcji** dla **ReportConnectivity** projekt jest **Start**. Skompiluj rozwiązanie.
1. Uruchom tę aplikację, klikając prawym przyciskiem myszy **ReportConnectivity** projektu i wybierając polecenie **debugowania**, a następnie **Uruchom nowe wystąpienie**. Powinna zostać wyświetlona na pobieranie informacji o bliźniaczych reprezentacji, a następnie wysyłając połączeń *zgłaszane właściwości*.
   
    ![Uruchom aplikację urządzenia do łączności z raportu][img-rundeviceapp]
    
    
1. Teraz, gdy urządzenie jest zgłaszane jego informacje o łączności, powinien pojawić się w obu zapytań. Uruchom z .NET **AddTagsAndQuery** aplikację, aby ponownie uruchomić zapytania. Tym razem **myDeviceId** powinno pojawić się w obu wyników zapytania.
   
    ![Łączność urządzeń zgłoszonych pomyślnie][img-tagappsuccess]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodane metadane urządzenia jako tagi z aplikacji zaplecza, a aplikacja powstała z jednego urządzenia symulowanego do raportu informacje o łączności urządzenia w bliźniaku urządzenia. Przedstawiono również sposób wykonywania zapytań te informacje przy użyciu języka zapytań usługi IoT Hub podobnego do SQL.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzeń przy użyciu [Rozpoczynanie pracy z usługą IoT Hub] [ lnk-iothub-getstarted] samouczka
* Konfigurowanie urządzeń przy użyciu żądane właściwości bliźniaczej reprezentacji urządzenia za pomocą [Użyj żądane właściwości, aby skonfigurować urządzenia] [ lnk-twin-how-to-configure] samouczka
* Formant urządzenia interakcyjne (takich jak włączenie wentylator z aplikacji kontrolowanej przez użytkownika) [używanie metod bezpośrednich] [ lnk-methods-tutorial] samouczka.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-how-to-configure]: tutorial-device-twins.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

