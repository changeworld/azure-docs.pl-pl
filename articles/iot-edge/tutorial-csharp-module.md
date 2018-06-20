---
title: Moduł usługi Azure IoT Edge w języku C# | Microsoft Docs
description: Tworzenie modułu usługi IoT Edge za pomocą kodu C# i wdrażanie go na urządzeniu brzegowym
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1da3a246a2ad33a4563f491058f5d4d115f3954d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631075"
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Opracowywanie i wdrażanie modułu usługi IoT Edge w języku C# na urządzeniu symulowanym — wersja zapoznawcza

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z samouczkami dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows][lnk-tutorial1-win] lub [Linux][lnk-tutorial1-lin]. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Korzystanie z edytora Visual Studio Code w celu utworzenia modułu usługi IoT Edge opartego na platformie .NET Core 2.0
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker 
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge utworzone w ramach przewodnika Szybki start lub pierwszego samouczka.
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.  
* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* Platforma [Docker](https://docs.docker.com/engine/installation/) na tym samym komputerze, na którym działa program Visual Studio Code. Wersja Community Edition (CE) jest wystarczająca na potrzeby tego samouczka. 
* [Zestaw .NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.
2. Nazwij rejestr, wybierz subskrypcję i grupę zasobów oraz ustaw jednostkę SKU na wartość **Podstawowa**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Użyjesz tych wartości w dalszej części samouczka podczas publikowania obrazu platformy Docker do rejestru oraz podczas dodawania poświadczeń rejestru do środowiska uruchomieniowego usługi Edge. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W następujących krokach przedstawiono sposób tworzenia modułu usługi IoT Edge bazującego na platformie .NET core 2.0 przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.
1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu VS Code.
2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) szablon **AzureIoTEdgeModule** za pomocą polecenia dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Utwórz projekt dla nowego modułu. Poniższe polecenie utworzy folder projektu, **FilterModule**, w Twoim repozytorium kontenerów. Drugi parametr powinien mieć postać `<your container registry name>.azurecr.io`, jeśli używasz rejestru kontenerów platformy Azure. Wprowadź następujące polecenie w bieżącym folderze roboczym:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Wybierz pozycję **Plik** > **Otwórz folder**.
5. Przejdź do folderu **FilterModule** i kliknij pozycję **Wybierz folder**, aby otworzyć projekt w programie VS Code.
6. W eksploratorze programu VS Code kliknij plik **Program.cs**, aby go otworzyć.

   ![Otwieranie pliku Program.cs][1]

7. W górnej części przestrzeni nazw **FilterModule** dodaj trzy instrukcje `using` dla typów używanych w późniejszym czasie:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Dodaj zmienną `temperatureThreshold` do klasy **Program**. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Dodaj klasy `MessageBody`, `Machine` i `Ambient` do klasy **Program**. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

10. W metodzie **Init** kod tworzy i konfiguruje obiekt **DeviceClient**. Dzięki temu obiektowi moduł może nawiązać połączenie z lokalnym środowiskiem uruchomieniowym usługi Azure IoT Edge na potrzeby wysyłania i odbierania komunikatów. Parametry połączenia używane w metodzie **Init** są dostarczane do modułu za pośrednictwem środowiska uruchomieniowego usługi IoT Edge. Po utworzeniu obiektu **DeviceClient** kod odczytuje wartość TemperatureThreshold z odpowiednich właściwości bliźniaczej reprezentacji modułu i rejestruje wywołanie zwrotne do odbierania komunikatów z centrum usługi IoT Edge za pośrednictwem punktu końcowego **input1**. Zastąp metodę `SetInputMessageHandlerAsync` nową metodą i dodaj metodę `SetDesiredPropertyUpdateCallbackAsync` do aktualizowania odpowiednich właściwości. Aby dokonać tej zmiany, zastąp ostatni wiersz metody **Init** następującym kodem:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Property TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Dodaj metodę `onDesiredPropertiesUpdate` do klasy **Program**. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

12. Zastąp metodę `PipeMessage` metodą `FilterMessages`. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

13. Zapisz ten plik.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Tworzenie obrazu platformy Docker i publikowanie go w rejestrze

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Aby znaleźć nazwę użytkownika, hasło i serwer logowania do użycia w tym poleceniu, przejdź do witryny Azure Portal (https://portal.azure.com)). W obszarze **Wszystkie zasoby** kliknij kafelek usługi Azure Container Registry, aby otworzyć jej właściwości, a następnie kliknij pozycję **Klucze dostępu**. Skopiuj wartości w polach **Nazwa użytkownika**, **Hasło** i **Serwer logowania**. 

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **module.json** i kliknij polecenie **Skompiluj i wypchnij obraz platformy Docker modułu usługi IoT Edge**. W podręcznym polu listy rozwijanej w górnej części okna programu VS Code wybierz platformę kontenera, **amd64** dla kontenera systemu Linux lub **windows-amd64** dla kontenera systemu Windows. Program VS Code tworzy kod, umieszcza plik `FilterModule.dll` w kontenerze i wypycha go do określonego rejestru kontenerów.


3. Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w zintegrowanym terminalu programu VS Code. Aby uzyskać więcej informacji na temat definicji kompilacji i wypychania, zapoznaj się z plikiem `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Dodawanie poświadczeń rejestru do środowiska uruchomieniowego usługi Edge
Dodaj poświadczenia dla rejestru do środowiska uruchomieniowego usługi Edge na komputerze, na którym jest uruchomione urządzenie usługi Edge. Dzięki tym poświadczeniom środowisko uruchomieniowe może pobrać kontener. 

- W systemie Windows uruchom następujące polecenie:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- W systemie Linux uruchom następujące polecenie:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Uruchamianie rozwiązania

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do centrum IoT Hub.
2. Przejdź do pozycji **IoT Edge (wersja zapoznawcza)** i wybierz urządzenie usługi IoT Edge.
3. Wybierz pozycję **Ustaw moduły**. 
4. Sprawdź, czy moduł **tempSensor** jest automatycznie wypełniany. Jeśli nie, wykonaj następujące czynności, aby go dodać:
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `tempSensor`.
    3. W polu **Identyfikator URI obrazu** wprowadź wartość `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Pozostaw inne ustawienia bez zmian, a następnie kliknij pozycję **Zapisz**.
5. Dodaj moduł **filterModule**, który został utworzony w poprzednich sekcjach. 
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `filterModule`.
    3. W polu **Identyfikator URI obrazu** wprowadź adres obrazu, na przykład `<your container registry address>/filtermodule:0.0.1-amd64`. Pełen adres obrazu można znaleźć w poprzedniej sekcji.
    4. Zaznacz pole wyboru **Włącz**, aby móc edytować bliźniaczą reprezentację modułu. 
    5. Zastąp dane JSON w polu tekstowym dla bliźniaczej reprezentacji modułu następującym kodem JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kliknij pozycję **Zapisz**.
6. Kliknij przycisk **Dalej**.
7. W kroku **Określanie tras** skopiuj poniższe dane JSON do pola tekstowego. Moduły publikują wszystkie komunikaty do środowiska uruchomieniowego usługi Edge. Reguły deklaratywne w środowisku uruchomieniowym definiują trasy przepływu komunikatów. W tym samouczku będą potrzebne dwie trasy. Pierwsza trasa jest przeznaczona do transportowania komunikatów z czujnika temperatury do modułu filtru za pośrednictwem punktu końcowego „input1”, który jest punktem końcowym skonfigurowanym za pomocą procedury obsługi **FilterMessages**. Druga trasa służy do transportu komunikatów z modułu filtru do centrum IoT Hub. W tej trasie element `upstream` jest specjalnym miejscem docelowym, które nakazuje centrum Edge Hub wysyłanie komunikatów do centrum IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Kliknij przycisk **Dalej**.
9. W kroku **Przegląd szablonu** kliknij pozycję **Prześlij**. 
10. Wróć do strony szczegółów urządzenia usługi IoT Edge, a następnie wybierz pozycję **Odśwież**. Powinien zostać wyświetlony nowy moduł **filtermodule** uruchomiony wraz z modułem **tempSensor** i **środowiskiem uruchomieniowym usługi IoT Edge**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby monitorować urządzenie pod kątem komunikatów w chmurze wysyłanych z urządzenia usługi IoT Edge do centrum IoT Hub:
1. Skonfiguruj rozszerzenie Azure IoT Toolkit za pomocą parametrów połączenia na potrzeby centrum IoT Hub: 
    1. Otwórz eksploratora programu VS Code, wybierając pozycję **Widok** > **Eksplorator**. 
    2. W eksploratorze kliknij pozycję **IOT HUB DEVICES**, a następnie kliknij pozycję **...**. Kliknij pozycję **Ustaw parametry połączenia centrum IoT Hub** i w oknie podręcznym wprowadź parametry połączenia dla centrum IoT Hub, z którym nawiązuje połączenie urządzenie usługi IoT Edge. 

        Aby znaleźć parametry połączenia, kliknij kafelek centrum IoT Hub w witrynie Azure Portal, a następnie kliknij pozycję **Zasady dostępu współużytkowanego**. W obszarze **Zasady dostępu współużytkowanego** kliknij zasady **iothubowner** i skopiuj parametry połączenia usługi IoT Hub w oknie **iothubowner**.   

2. Aby monitorować dane otrzymywane przez centrum IoT Hub, wybierz pozycję **Widok** > **Paleta poleceń**, a następnie wyszukaj polecenie menu **IoT: rozpocznij monitorowania komunikatu D2C**. 
3. Aby zatrzymać monitorowanie danych, użyj polecenia menu **IoT: zatrzymaj monitorowanie komunikatu D2C**. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Możesz teraz kontynuować pracę, korzystając z dowolnego z następujących samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako modułu](tutorial-deploy-function.md)
> [Wdrażanie usługi Azure Stream Analytics jako modułu](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
