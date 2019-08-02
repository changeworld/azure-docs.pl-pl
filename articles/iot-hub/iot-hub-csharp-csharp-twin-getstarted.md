---
title: Wprowadzenie do usługi Azure IoT Hub Device bliźniaczych reprezentacji (.NET/.NET) | Microsoft Docs
description: Jak dodać tagi przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji, a następnie użyć kwerendy IoT Hubowej. Zestaw SDK urządzeń Azure IoT dla platformy .NET służy do implementowania aplikacji symulowanego urządzenia i zestawu SDK usługi Azure IoT dla platformy .NET w celu zaimplementowania aplikacji usługi, która dodaje Tagi i uruchamia kwerendę IoT Hubową.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: 9bf34fd48c3a4a9a9672ac162f63dcce118b2c0a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668166"
---
# <a name="get-started-with-device-twins-net"></a>Wprowadzenie do bliźniaczych reprezentacji urządzeń (.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będą dostępne następujące aplikacje konsolowe platformy .NET:

* **CreateDeviceIdentity**, aplikacja platformy .NET, która tworzy tożsamość urządzenia i skojarzony klucz zabezpieczeń w celu podłączenia aplikacji symulowanego urządzenia.

* **AddTagsAndQuery**— aplikacja zaplecza platformy .NET, która dodaje Tagi i kwerendy bliźniaczych reprezentacji urządzeń.

* **ReportConnectivity**, aplikacja urządzenia platformy .NET, która symuluje urządzenie, które nawiązuje połączenie z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia i zgłasza warunek łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK](iot-hub-devguide-sdks.md) zawiera informacje na temat zestawów SDK usługi Azure IoT, których można użyć do tworzenia aplikacji zarówno dla urządzeń, jak i zaplecza.
> 

Do ukończenia tego samouczka potrzebne są następujące elementy:

* Program Visual Studio.
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsolową .NET (przy użyciu C#), która dodaje metadane lokalizacji do sznurka urządzenia skojarzonego z **myDeviceId**. Następnie wysyła zapytanie do bliźniaczych reprezentacji urządzenia przechowywanego w usłudze IoT Hub, wybierając urządzenia znajdujące się w Stanach Zjednoczonych, a następnie te, które zgłosiły połączenie komórkowe.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **AddTagsAndQuery**.
   
    ![Nowy projekt Visual C# Windows Classic Desktop](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **AddTagsAndQuery** , a następnie kliknij pozycję **Zarządzaj pakietami NuGet.** ...

3. W oknie **Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj** i wyszukaj ciąg **Microsoft. Azure. Devices**. Wybierz pozycję **Zainstaluj** , aby zainstalować pakiet **Microsoft. Azure. Devices** , i zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK usługi Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) oraz jego zależności.
   
    ![Okno Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Dodaj następującą metodę do klasy **Program**:

    ```csharp  
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
   
        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```
   
    Klasa **registrymanager** udostępnia wszystkie metody wymagane do współpracy z bliźniaczych reprezentacji urządzeń z usługi. Poprzedni kod najpierw inicjuje obiekt **registrymanager** , a następnie pobiera sznurki urządzenia dla **myDeviceId**, a wreszcie aktualizuje Tagi o informacje o żądanych lokalizacjach.
   
    Po aktualizacji wykonuje dwa zapytania: pierwszy wybiera tylko urządzenie bliźniaczych reprezentacji urządzeń znajdujące się w zakładzie **Redmond43** , a drugi zawęża zapytanie w celu wybrania tylko tych urządzeń, które są również połączone przez sieć komórkową.
   
    Należy zauważyć, że poprzedni kod, gdy tworzy obiekt **zapytania** , określa maksymalną liczbę zwracanych dokumentów. Obiekt **zapytania** zawiera właściwość logiczną **HasMoreResults** , która umożliwia wywoływanie metod **GetNextAsTwinAsync** wiele razy, aby pobrać wszystkie wyniki. Metoda o nazwie **GetNextAsJson** jest dostępna dla wyników, które nie są bliźniaczych reprezentacji urządzenia, na przykład wyników zapytań agregacji.

7. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. W Eksplorator rozwiązań otwórz projekt **Ustaw projekty startowe...** i upewnij się, że **Akcja** dla projektu **AddTagsAndQuery** jest **uruchamiana**. Skompiluj rozwiązanie.

9. Uruchom tę aplikację, klikając prawym przyciskiem myszy projekt **AddTagsAndQuery** i wybierając pozycję **Debuguj**, a następnie polecenie **Uruchom nowe wystąpienie**. Powinno zostać wyświetlone jedno urządzenie w wynikach zapytania z pytaniem o wszystkie urządzenia znajdujące się w **Redmond43** i brak dla zapytania, które ogranicza wyniki do urządzeń korzystających z sieci komórkowej.
   
    ![Wyniki zapytania w oknie](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

W następnej sekcji utworzysz aplikację urządzenia, która zgłosi informacje o łączności i zmieni wynik zapytania w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową platformy .NET, która łączy się z centrum jako **myDeviceId**, a następnie aktualizuje raportowane właściwości w taki sposób, aby zawierała informacje, które są połączone z siecią komórkową.

1. W programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do bieżącego rozwiązania przy użyciu szablonu projektu **Aplikacja konsolowa**. Nazwij projekt **ReportConnectivity**.
   
    ![Nowa aplikacja C# klasycznego urządzenia z systemem Visual Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **ReportConnectivity** , a następnie kliknij pozycję **Zarządzaj pakietami NuGet.** ...

3. W oknie **Menedżer pakietów NuGet** wybierz pozycję **Przeglądaj** i wyszukaj ciąg **Microsoft. Azure. Devices. Client**. Wybierz pozycję **Zainstaluj** , aby zainstalować pakiet **Microsoft. Azure. Devices. Client** , a następnie zaakceptuj warunki użytkowania. Ta procedura spowoduje pobranie, zainstalowanie i dodanie odwołania do pakietu NuGet [zestawu SDK urządzenia Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) oraz jego zależności.
   
    ![Aplikacja kliencka Menedżera pakietów NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia urządzenia, które zostały zanotowane w poprzedniej sekcji.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Dodaj następującą metodę do klasy **Program**:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Obiekt **Client** uwidacznia wszystkie metody wymagane do współpracy z urządzeniem bliźniaczych reprezentacji z urządzenia. Pokazany powyżej kod inicjuje obiekt **klienta** , a następnie pobiera sznurki urządzenia dla **myDeviceId**.

7. Dodaj następującą metodę do klasy **Program**:

    ```csharp  
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
    ```

   Kod powyżej aktualizuje właściwość **myDeviceId**z informacjami o łączności.

8. Na koniec dodaj następujące wiersze do metody **Główne**:

    ```csharp
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
    ```

9. W Eksplorator rozwiązań otwórz projekt **Ustaw projekty startowe...** i upewnij się, że **Akcja** dla projektu **ReportConnectivity** jest **uruchamiana**. Skompiluj rozwiązanie.

10. Uruchom tę aplikację, klikając prawym przyciskiem myszy projekt **ReportConnectivity** i wybierając pozycję **Debuguj**, a następnie polecenie **Uruchom nowe wystąpienie**. Powinieneś wyświetlić informacje o bliźniaczych, a następnie wysłać łączność jako raportowaną *Właściwość*.
   
    ![Uruchom aplikację urządzenia, aby zgłosić łączność](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Teraz, gdy urządzenie zgłosiło swoje informacje o łączności, powinno być wyświetlane w obu zapytaniach. Uruchom aplikację .NET **AddTagsAndQuery** , aby ponownie uruchomić zapytania. Ten czas **myDeviceId** powinien pojawić się w obu wynikach zapytania.
   
    ![Pomyślnie zgłoszono łączność urządzenia](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Metadane urządzenia zostały dodane jako Tagi z aplikacji zaplecza i zapisały symulowaną aplikację urządzenia do raportowania informacji o łączności urządzenia w ramach sznurka urządzenia. Dowiesz się również, jak wykonywać zapytania dotyczące tych informacji przy użyciu języka zapytań IoT Hub w programie SQL Server.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wysyłaj dane telemetryczne z urządzeń za pomocą usługi wysyłanie danych telemetrycznych [z urządzenia do usługi IoT Hub](quickstart-send-telemetry-dotnet.md) — samouczek

* Konfigurowanie urządzeń za pomocą odpowiednich właściwości sznurka urządzenia z użyciem [wymaganych właściwości do konfigurowania urządzeń](tutorial-device-twins.md) ,

* Interakcyjne sterowanie urządzeniami (na przykład Włączanie wentylatorów z poziomu aplikacji sterowanej przez użytkownika) przy użyciu samouczka [Korzystanie z metod bezpośrednich](quickstart-control-device-dotnet.md) .
