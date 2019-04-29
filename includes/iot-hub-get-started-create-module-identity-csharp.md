---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741845"
---
## <a name="create-a-module-identity"></a>Tworzenie tożsamości modułu

W tej sekcji utworzysz aplikację konsolową .NET, która tworzy tożsamość urządzenia i tożsamość modułu w rejestrze tożsamości centrum IoT Hub. Urządzenie lub moduł nie mogą łączyć się z centrum IoT Hub, jeśli nie mają odpowiedniego wpisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [rejestr tożsamości części przewodnika dla deweloperów usługi IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Urządzenie i moduł korzystają z tych wartości w celu identyfikowania się podczas wysyłania komunikatów urządzenie-chmura do usługi IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.


1. **Utwórz projekt programu Visual Studio** — w programie Visual Studio dodaj projekt Visual C# Windows Classic Desktop do nowego rozwiązania, używając szablonu projektu **Aplikacja konsolowa (.NET Framework)**. Upewnij się, że program .NET Framework jest w wersji 4.6.1 lub nowszej. Nadaj projektowi nazwę **CreateIdentities**, a rozwiązaniu **IoTHubGetStarted**.

    ![Tworzenie rozwiązania programu Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Zainstaluj zestaw SDK usługi platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-001** — tożsamość modułu i bliźniacza reprezentacja modułu są w publicznej wersji zapoznawczej. Jest on dostępny tylko w zestawy SDK z wersji wstępnej usługi IoT Hub. W programie Visual Studio wybierz pozycję Narzędzia > Menedżer pakietów NuGet > Zarządzaj pakietami NuGet dla rozwiązania. Wyszukaj ciąg Microsoft.Azure.Devices. Upewnij się, że zaznaczono pole wyboru Uwzględnij wersję wstępną. Wybierz wersję 1.16.0-preview-001 i zainstaluj ją. Teraz masz dostęp do wszystkich funkcji modułu. 

    ![Instalowanie zestawu SDK usługi platformy .NET usługi Azure IoT Hub w wersji 1.16.0-preview-001](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Dodaj następujące instrukcje `using` w górnej części pliku **Program.cs**:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Dodaj następujące pola do klasy **Program**: Zastąp wartość symbolu zastępczego parametrami połączenia usługi IoT Hub dla centrum IoT utworzonego w poprzedniej sekcji.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Dodaj następujący kod do **Main** klasy.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Dodaj następujące metody do klasy **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Metoda AddDeviceAsync() służy do tworzenia tożsamości urządzenia o identyfikatorze **myFirstDevice**. (Jeśli ten identyfikator urządzenia już istnieje w rejestrze tożsamości, kod po prostu pobiera informacje o istniejącym urządzeniu). Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Tego klucza używa się w symulowanej aplikacji urządzenia, aby nawiązać połączenie z centrum IoT Hub.

    Metoda AddModuleAsync() tworzy tożsamość modułu o identyfikatorze **myFirstModule** w ramach tożsamości urządzenia **myFirstDevice**. (Jeśli ten identyfikator modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera informacje o istniejącym module). Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Tego klucza używa się w symulowanej aplikacji modułu, aby nawiązać połączenie z centrum IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Uruchom tę aplikację, a następnie zanotuj klucz urządzenia i klucz modułu.

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia i modułu tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub — przewodnik dewelopera](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
