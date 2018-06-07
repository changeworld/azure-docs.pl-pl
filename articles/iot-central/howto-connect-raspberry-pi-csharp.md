---
title: Konstruktor Pi malina do aplikacji Azure IoT centralnej (C#) | Dokumentacja firmy Microsoft
description: Deweloper urządzenia jak nawiązać Pi malina Azure IoT centralnej aplikację przy użyciu języka C#.
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 58f363c522f3e5abe6bf49a2aebafe4e953e00df
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628593"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Pi malina nawiązać połączenia z aplikacji Azure IoT centralnej (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

W tym artykule opisano sposób Deweloper urządzenia nawiązać Pi malina aplikację Microsoft Azure IoT centralnej przy użyciu języka programowania w języku C#.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

* [.NET core 2](https://www.microsoft.com/net) zainstalowane na komputerze deweloperskim. Należy również zaznaczyć Edytor odpowiedni kod takich jak [Visual Studio Code](https://code.visualstudio.com/).
* Aplikacja Azure IoT centralnej utworzone na podstawie **Devkits próbki** szablon aplikacji. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji centralnej Azure IoT](howto-create-application.md).
* Pi malina urządzenie z systemem operacyjnym Raspbian.

Aplikacji utworzone na podstawie **Devkits próbki** szablon aplikacji obejmuje **Pi malina** szablonu urządzenia o następującej charakterystyce:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| wilgotność       | %      | 0       | 100     | 0              |
| Temp           | C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
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

Ustawienia numeryczne

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Napięcia      | setVoltage | V | 0              | 0       | 240     | 0       |
| Bieżący      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Wentylator szybkości    | fanSpeed   | OBR. / MIN   | 0              | 0       | 1000    | 0       |

Ustawienia przełącznika

| Nazwa wyświetlana | Nazwa pola | W tekście | Wyłączanie tekstu | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR.           | activateIR | ON      | WYŁ.      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Typ            | Nazwa wyświetlana | Nazwa pola | Typ danych |
| --------------- | ------------ | ---------- | --------- |
| Właściwości urządzenia | Die numer   | dieNumber  | numer    |
| Tekst            | Lokalizacja     | location   | ND       |

### <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT centralnej, Dodaj rzeczywistego urządzenia z **Pi malina** szablon urządzenia i zanotować ciąg połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodawania rzeczywistego urządzenia do aplikacji Azure IoT centralnej](tutorial-add-device.md).

## <a name="create-your-net-application"></a>Tworzenie aplikacji .NET

Tworzenie i testowanie aplikacji urządzenia na komputerze pulpitu.

Wykonaj poniższe kroki, umożliwia Visual Studio Code. Aby uzyskać więcej informacji, zobacz [pracy w języku C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Jeśli wolisz, można wykonać następujące czynności, za pomocą edytora inny kod.

1. Aby zainicjować .NET projektu i dodać wymagane pakiety NuGet, uruchom następujące polecenia:

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Otwórz `pisample` folderu w programie Visual Studio Code. Następnie otwórz **pisample.csproj** pliku projektu. Dodaj `<RuntimeIdentifiers>` tag pokazano następujący fragment kodu:

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

1. Zapisz **pisample.csproj**. Jeśli program Visual Studio Code wyświetli monit o wykonanie polecenia restore, wybierz **przywrócić**.

1. Otwórz **Program.cs** i Zastąp zawartość następującym kodem:

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
    > Zaktualizuj symbol zastępczy `{your device connection string}` w następnym kroku.

## <a name="run-your-net-application"></a>Uruchamianie aplikacji .NET

Dodaj parametry połączenia specyficzne dla urządzenia z kodu dla tego urządzenia do uwierzytelniania w usłudze Azure IoT centralnej. Zanotuj te parametry połączenia są wprowadzone po dodaniu prawdziwe urządzenia do aplikacji Azure IoT centralnej.

1. Zastąp `{your device connection string}` w **Program.cs** plik z parametrami połączenia zanotowany wcześniej.

1. Uruchom następujące polecenie w wierszu polecenia środowiska:

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. Kopiuj `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` folderu na urządzeniu malina Pi. Można użyć **scp** polecenie, aby skopiować pliki, na przykład:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Aby uzyskać więcej informacji, zobacz [dostępu zdalnego Pi malina](https://www.raspberrypi.org/documentation/remote-access/).

1. Zaloguj się do Twojego urządzenia Pi malina i uruchom następujące polecenia w powłoce:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. W Twojej Pi malina uruchom następujące polecenia:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Rozpoczyna się program](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. W aplikacji Azure IoT centralnej można wyświetlić, jak uruchomiony Pi malina kod współdziała z aplikacją:

    * Na **pomiary** strony dla rzeczywistego urządzenia, można wyświetlić dane telemetryczne.
    * Na **właściwości** strony, można zobaczyć wartość opisane **Die numer** właściwości.
    * Na **ustawienia** strony, można zmienić różnych ustawień na Pi malina, takich jak napięcia i wentylator szybkości.

    Poniższy zrzut ekranu przedstawia Pi malina odbieranie zmiana ustawień:

    ![Zmiana ustawień odbiera malinowe Pi](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób nawiązywania Pi malina Azure IoT centralnej aplikacji, w tym miejscu są Sugerowane następne kroki:

* [Połącz ogólnego aplikacji klienckiej Node.js do centralnej IoT Azure](howto-connect-nodejs.md)