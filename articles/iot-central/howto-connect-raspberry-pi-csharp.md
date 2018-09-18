---
title: Konstruktor Raspberry Pi z aplikacją usługi Azure IoT Central (C#) | Dokumentacja firmy Microsoft
description: Jako deweloper w urządzeniu jak łączyć z urządzeniem Raspberry Pi do usługi Azure IoT Central aplikacji przy użyciu języka C#.
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: a9390ac9046ad1e0ec5a1689052ee99bf76ec6f4
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734239"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

W tym artykule opisano jak Deweloper urządzenia do łączenia z urządzeniem Raspberry Pi z aplikacją Microsoft Azure IoT Central, przy użyciu języka programowania C#.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

* [Platforma .NET core 2](https://www.microsoft.com/net) zainstalowane na komputerze deweloperskim. Należy przygotować także Edytor odpowiedni kod taki jak [programu Visual Studio Code](https://code.visualstudio.com/).
* Aplikacja usługi Azure IoT Central, utworzone na podstawie **Devkits przykładowe** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [utworzyć aplikację usługi Azure IoT centralnej](howto-create-application.md).
* Urządzenie Raspberry Pi, systemem operacyjnym Raspbian.


## <a name="sample-devkits-application"></a>**Przykładowy Devkits** aplikacji

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce: 

- Dane telemetryczne, zawierającą pomiarów urządzenia **wilgotności**, **temperatury**, **wykorzystanie**, **Magnometer** (mierzoną wzdłuż X Y, osi Z), **Accelorometer** (mierzoną wzdłuż X, Y osi Z) i **Żyroskop** (mierzoną wzdłuż X, Y osi Z).
- Ustawień przedstawiający **napięcia**, **bieżącego**,**szybkość wentylator** i **IR** przełącznika.
- Właściwości zawierający właściwości urządzenia **zdechną numer** i **lokalizacji** właściwość w chmurze.


Można znaleźć szczegółowe informacje o konfiguracji szablonu urządzenia [Szczegóły szablonu Raspberry PI urządzenia](howto-connect-raspberry-pi-csharp.md#raspberry-pi-device-template-details)


## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central, Dodaj prawdziwe urządzenie z **Raspberry Pi** szablon urządzenia i zanotować parametry połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodać rzeczywistego urządzenia do aplikacji usługi Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Tworzenie aplikacji .NET

Tworzenie i testowanie aplikacji urządzenia na komputerze stacjonarnym.

Aby wykonać następujące czynności, można użyć programu Visual Studio Code. Aby uzyskać więcej informacji, zobacz [pracy w języku C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Jeśli wolisz, możesz wykonać następujące czynności za pomocą edytora inny kod.

1. Aby zainicjować projektu .NET i Dodaj wymagane pakiety NuGet, uruchom następujące polecenia:

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Otwórz `pisample` folderu w programie Visual Studio Code. Następnie otwórz **pisample.csproj** pliku projektu. Dodaj `<RuntimeIdentifiers>` tag pokazano w poniższym fragmencie kodu:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Twoje **Microsoft.Azure.Devices.Client** numer wersji pakietu może być większa niż wskazano.

1. Zapisz **pisample.csproj**. Visual Studio Code jest wyświetlany monit o wykonanie polecenia restore, wybierz opcję **przywrócić**.

1. Otwórz **Program.cs** i zastąp jego zawartość następującym kodem:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Aktualizuj symbol zastępczy `{your device connection string}` w następnym kroku.

## <a name="run-your-net-application"></a>Uruchamianie aplikacji .NET

Dodaj parametry połączenia specyficzne dla urządzenia do kodu dla tego urządzenia do uwierzytelniania za pomocą usługi Azure IoT Central. Zanotuj te parametry połączenia zostały wprowadzone po dodaniu rzeczywistego urządzenia do aplikacji usługi Azure IoT Central.

  > [!NOTE]
   > Usługa Azure IoT Central przeszła do przy użyciu usługi Azure IoT Hub Device Provisioning (DPS) dla wszystkich połączeń urządzeń, wykonaj te instrustions do [pobieranie parametrów połączenia urządzenia](concepts-connectivity.md#getting-device-connection-string) i kontynuować pracę z pozostałej części tego samouczka.

1. Zastąp `{your device connection string}` w **Program.cs** pliku zanotowanymi wcześniej parametrami połączenia.

1. W środowisku wiersza polecenia, uruchom następujące polecenie:

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. Kopiuj `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` folderu na urządzeniu Raspberry Pi. Możesz użyć **scp** polecenie, aby skopiować pliki, na przykład:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Aby uzyskać więcej informacji, zobacz [dostępu zdalnego z urządzeniem Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/).

1. Zaloguj się do Twojego urządzenia Raspberry Pi i uruchom następujące polecenia w powłoce:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Na urządzenia Raspberry Pi uruchom następujące polecenia:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Rozpoczyna się program](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. W aplikacji usługi Azure IoT Central można zobaczyć, jak kod uruchomiony na urządzenia Raspberry Pi współdziała z aplikacją:

    * Na **pomiarów** strony dla Twojego rzeczywistego urządzenia można wyświetlić dane telemetryczne.
    * Na **właściwości** stronie widać wartości zgłaszanych **zdechną numer** właściwości.
    * Na **ustawienia** strony, można zmienić różnych ustawień na urządzenia Raspberry Pi, takie jak szybkość napięcia i wychodzącą.

    Poniższy zrzut ekranu przedstawia Raspberry Pi odbieranie zmiana ustawienia:

    ![Urządzenie raspberry Pi odbiera zmianę ustawień](./media/howto-connect-raspberry-pi-csharp/device_switch.png)


## <a name="raspberry-pi-device-template-details"></a>Szczegóły szablonu w usłudze raspberry PI urządzenia

Utworzone na podstawie aplikacji **Devkits przykładowe** szablon aplikacji zawiera **Raspberry Pi** szablon urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| Temp           | C     | -40     | 120     | 0              |
| pressure       | hPa pakietu    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | grupą zarządzania     | -2000   | 2000    | 0              |
| accelerometerY | grupą zarządzania     | -2000   | 2000    | 0              |
| accelerometerZ | grupą zarządzania     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napięcie      | setVoltage | V | 0              | 0       | 240     | 0       |
| Bieżący      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| Nazwa wyświetlana | Nazwa pola | W tekście | Wyłącz tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| ŚRODOWISKO IR           | activateIR | ON      | WYŁ.      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Zdechną liczb   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak połączyć z urządzeniem Raspberry Pi z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

* [Łączenie z ogólnego klienta aplikacji Node.js usługi Azure IoT Central](howto-connect-nodejs.md)
