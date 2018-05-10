---
title: Rozpoczynanie pracy z Centrum IoT Azure urządzenia twins (.NET/.NET) | Dokumentacja firmy Microsoft
description: Jak używać twins urządzenia Azure IoT Hub Dodawanie tagów, a następnie użyć kwerendy Centrum IoT. Przy użyciu urządzenia Azure IoT SDK dla platformy .NET usługi Azure IoT SDK dla platformy .NET do implementacji usługi aplikacji, która dodaje znaczniki i uruchamia kwerendy Centrum IoT i aplikacji symulowane urządzenie.
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: ''
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: b337e463ac4aefd7e4644af4c2cdcad4f3c5300a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-device-twins-netnet"></a>Rozpoczynanie pracy z urządzenia twins (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka konieczne będzie tych aplikacji konsoli .NET:

* **CreateDeviceIdentity**, który tworzy tożsamości urządzenia i klucz zabezpieczeń skojarzony także łączenie aplikacji symulowane urządzenie aplikacji .NET.
* **AddTagsAndQuery**, aplikacji zaplecza .NET, która dodaje znaczniki i zapytanie twins urządzenia.
* **ReportConnectivity**, aplikacji urządzenia .NET, która symuluje urządzenie, które łączy do Centrum IoT z tożsamości urządzenia utworzony wcześniej, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK] [ lnk-hub-sdks] informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka należy spełnić następujące warunki:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

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
W tej sekcji Tworzenie aplikacji konsoli .NET, który łączy do Centrum jako **myDeviceId**, a następnie aktualizuje jego zgłoszone właściwości zawierają informacje, że jest połączony za pomocą sieci komórkowej.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **ReportConnectivity**.
   
    ![Nowa aplikacja Visual C# Windows Classic urządzenia][img-createdeviceapp]
    
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **ReportConnectivity** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet...** .
1. W **Menedżera pakietów NuGet** wybierz **Przeglądaj** i wyszukaj **microsoft.azure.devices.client**. Wybierz **zainstalować** do zainstalowania **Microsoft.Azure.Devices.Client** pakietu, a następnie zaakceptuj warunki użytkowania. Ta procedura pliki do pobrania, instaluje i dodaje odwołanie do [urządzenia Azure IoT SDK] [ lnk-nuget-client-sdk] NuGet pakiet i jego zależności.
   
    ![Aplikacja kliencka okna Menedżera pakietów NuGet][img-clientnuget]
1. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Dodaj następujące pola do klasy **Program**: Zamień wartość symbolu zastępczego ciąg połączenia urządzenia zanotowanym w poprzedniej sekcji.
   
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

    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z urządzenia. Inicjuje kodzie pokazanym powyżej, **klienta** obiekt, a następnie pobiera dwie urządzenia dla **myDeviceId**.

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

   Kod nad aktualizacje **myDeviceId**obiektu podać właściwość o informacje dotyczące łączności.

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

1. W Eksploratorze rozwiązań Otwórz **projektów startowych ustawić...**  i upewnij się, że **akcji** dla **ReportConnectivity** projekt jest **Start**. Skompiluj rozwiązanie.
1. Uruchomić tę aplikację, klikając prawym przyciskiem myszy **ReportConnectivity** projekt i wybierając **debugowania**, a następnie **Start nowe wystąpienie**. Powinny pojawić się on pobierania informacji o dwie, a następnie wysyła łączność *podać właściwość*.
   
    ![Uruchamianie aplikacji urządzeń z łącznością raportu][img-rundeviceapp]
    
    
1. Teraz, gdy urządzenie zgłosiło jego informacje dotyczące łączności, powinna pojawić się w obu zapytania. Uruchamianie programu .NET **AddTagsAndQuery** aplikacji, aby ponownie uruchomić zapytania. Teraz **myDeviceId** powinny być wyświetlane w obu wyników zapytania.
   
    ![Łączność urządzeń zgłoszonych pomyślnie][img-tagappsuccess]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodaje metadane urządzenia jako tagi z aplikacji zaplecza i zapisano aplikacji symulowane urządzenie informacji w raporcie urządzenia łączności w dwie urządzenia. Przedstawiono również sposób kwerendy te informacje przy użyciu języka przypominającego SQL Centrum IoT zapytania.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT] [ lnk-iothub-getstarted] samouczka
* Konfigurowanie urządzeń przy użyciu właściwości żądaną dwie urządzenia z [Użyj żądanego właściwości, aby skonfigurować urządzenia] [ lnk-twin-how-to-configure] samouczka
* kontrolowanie urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) z [metody bezpośredniego] [ lnk-methods-tutorial] samouczka.

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

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

