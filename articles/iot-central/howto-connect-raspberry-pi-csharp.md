---
title: Łączenie Raspberry Pi z aplikacją platformy Azure IoT Central (C#) | Microsoft Docs
description: Jako deweloper urządzenia dołączymy Raspberry Pi do aplikacji Azure IoT Central przy użyciu C#programu.
author: viv-liu
ms.author: viviali
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7a66925dceee4bf90bc6a5cd155f99347bbd124e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886002"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Łączenie Raspberry Pi z aplikacją platformy Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano sposób, w jaki deweloper urządzenia nawiązuje połączenie Raspberry Pi z aplikacją IoT Central Microsoft Azure przy użyciu języka C# programowania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące składniki:

* Aplikacja IoT Central platformy Azure utworzona na podstawie **przykładowego** szablonu aplikacji Devkits. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Urządzenie Raspberry Pi działające w systemie operacyjnym raspbian. Raspberry Pi musi mieć możliwość nawiązania połączenia z Internetem. Aby uzyskać więcej informacji, zobacz [Konfigurowanie Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Przykładowa aplikacja Devkits**

Aplikacja utworzona na podstawie **przykładowego** szablonu aplikacji Devkits zawiera szablon urządzenia **Raspberry Pi** o następujących cechach:

- Dane telemetryczne, które obejmują następujące pomiary zebrane przez urządzenie:
  - Wilgotność
  - Temperatura
  - Ciśnienie
  - Magnetometer (X, Y, Z)
  - Przyspieszeniomierz (X, Y, Z)
  - Żyroskop (X, Y, Z)
- Ustawienia
  - Zakres
  - Bieżący
  - Szybkość wentylatorów
  - Przełącznik IR.
- Właściwości
  - Właściwość urządzenia numeru wartości
  - Właściwość chmury lokalizacji

Aby uzyskać szczegółowe informacje na temat konfiguracji szablonu urządzenia, zobacz [szczegóły szablonu urządzenia Raspberry Pi](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie z szablonu urządzenia **Raspberry Pi** . Zanotuj szczegóły połączenia urządzenia (**Identyfikator zakresu**, **Identyfikator urządzenia**i **klucz podstawowy**). Aby uzyskać więcej informacji, zobacz [Dodawanie rzeczywistego urządzenia do aplikacji IoT Central platformy Azure](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Tworzenie aplikacji .NET

Tworzenie i testowanie aplikacji urządzenia na komputerze stacjonarnym.

Aby wykonać poniższe kroki, można użyć Visual Studio Code. Aby uzyskać więcej informacji, zobacz [Praca C#z ](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Jeśli wolisz, możesz wykonać następujące kroki przy użyciu innego edytora kodu.

1. Aby zainicjować projekt .NET i dodać wymagane pakiety NuGet, uruchom następujące polecenia:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. `pisample` Otwórz folder w Visual Studio Code. Następnie otwórz plik projektu **pisample. csproj** . `<RuntimeIdentifiers>` Dodaj tag przedstawiony w poniższym fragmencie kodu:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Numer wersji pakietu **Microsoft. Azure. Devices. Client** może być wyższy niż podany.

1. Zapisz **pisample. csproj**. Jeśli Visual Studio Code zostanie wyświetlony komunikat z prośbą o wykonanie polecenia Restore, wybierz pozycję **Przywróć**.

1. Otwórz **program.cs** i Zastąp zawartość następującym kodem:

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
    > W następnym kroku zaktualizujesz symbol zastępczy `{your device connection string}` .

## <a name="run-your-net-application"></a>Uruchamianie aplikacji .NET

Dodaj parametry połączenia specyficzne dla urządzenia do kodu na potrzeby uwierzytelniania urządzenia za pomocą usługi Azure IoT Central. Postępuj zgodnie z tymi instrukcjami, aby [wygenerować parametry połączenia urządzenia](howto-generate-connection-string.md) przy użyciu **identyfikatora zakresu**, **identyfikatora urządzenia**i **klucza podstawowego** , dla których wykonano wcześniej uwagi.

1. Zastąp `{your device connection string}` element w pliku **program.cs** wygenerowanymi parametrami połączenia.

1. Uruchom następujące polecenie w środowisku wiersza polecenia:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` Skopiuj folder na urządzenie Raspberry Pi. Możesz użyć polecenia **SCP** , aby skopiować pliki, na przykład:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Aby uzyskać więcej informacji, zobacz [Raspberry Pi dostępu zdalnego](https://www.raspberrypi.org/documentation/remote-access/).

1. Zaloguj się do urządzenia Raspberry Pi i uruchom następujące polecenia w powłoce:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Na Raspberry Pi Uruchom następujące polecenia:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Rozpoczyna się program](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. W aplikacji IoT Central platformy Azure można zobaczyć, jak kod działający w Raspberry Pi współdziała z aplikacją:

   * Na stronie **miary** rzeczywistego urządzenia można zobaczyć dane telemetryczne.
   * Na stronie **Właściwości** można zobaczyć wartość właściwości raportowany **numer** platformy.
   * Na stronie **Ustawienia** można zmienić różne ustawienia w Raspberry Pi, takie jak napięcie i szybkość wentylatorów.

     Poniższy zrzut ekranu przedstawia Raspberry Pi otrzymujący zmianę ustawienia:

     ![Raspberry Pi otrzymuje zmianę ustawienia](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Szczegóły szablonu urządzenia Raspberry Pi

Aplikacja utworzona na podstawie **przykładowego** szablonu aplikacji Devkits zawiera szablon urządzenia **Raspberry Pi** o następujących cechach:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

| Nazwa pola     | Jednostki  | Minimalne | Maksimum | Miejsca dziesiętne |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| przyspieszeniomierz | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Ustawienia

Ustawienia liczbowe

| Nazwa wyświetlana | Nazwa pola | Jednostki | Miejsca dziesiętne | Minimalne | Maksimum | Początkowa |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Zakres      | setnapięci | Wolty | 0              | 0       | 240     | 0       |
| Bieżący      | SetCurrent | Amper  | 0              | 0       | 100     | 0       |
| Szybkość wentylatorów    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Przełącz ustawienia

| Nazwa wyświetlana | Nazwa pola | Na tekst | Off text | Początkowa |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | WŁĄCZONE      | WYŁĄCZONE      | Wyłączone     |

### <a name="properties"></a>Właściwości

| Type            | Nazwa wyświetlana | Nazwa pola | Typ danych                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Właściwość urządzenia | Numer struktury   | dieNumber  | number                                 |
| Location        | Location     | location   | {Szerokość: float, Long: float, Alt?: float} |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć Raspberry Pi z aplikacją Azure IoT Central, sugerowanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie niestandardowego szablonu urządzenia](howto-set-up-template.md) dla własnego urządzenia IoT.
