---
title: Moduł Azure IoT krawędzi C# | Dokumentacja firmy Microsoft
description: Utwórz moduł krawędzi IoT z kodem C# i wdróż je do urządzenia
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 09e20d9a80b881075d9bb6be7d4daafc739340a1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Tworzenie i wdrażanie modułu krawędzi IoT C# w symulowane urządzenie — w wersji preview

Moduły krawędzi IoT umożliwia wdrażanie kodu, który implementuje logiki biznesowej bezpośrednio do urządzenia IoT krawędzi. W tym samouczku przedstawiono sposób tworzenia i wdrażania moduł krawędzi IoT filtrujące danych czujnika. Użyjesz symulowane urządzenie brzegowe IoT utworzoną w programie IoT Edge Azure wdrożenia symulowanego urządzenia w [Windows] [ lnk-tutorial1-win] lub [Linux] [ lnk-tutorial1-lin]samouczki. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Użyj programu Visual Studio Code, aby utworzyć moduł krawędzi IoT .NET core 2.0 w oparciu
> * Użyj programu Visual Studio Code i Docker, aby utworzyć obraz docker i opublikować ją w rejestrze 
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Moduł IoT krawędzi, który możesz utworzyć w tym samouczku filtruje temperatury dane generowane przez urządzenie. Tylko wysyła komunikaty powyżej jeśli temperatura przekracza określoną wartość progową. Ten typ analizy na krawędzi jest przydatne w przypadku zmniejszenie ilości danych przekazane i przechowywane w chmurze. 

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie brzegowe IoT Azure utworzoną w pierwszym samouczku lub Szybki Start.
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.  
* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker](https://docs.docker.com/engine/installation/) na tym samym komputerze, na którym Visual Studio Code. Community Edition (CE) jest wystarczająca dla tego samouczka. 
* [Zestaw .NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.
2. Nazwij rejestr, wybierz subskrypcję i grupę zasobów oraz ustaw jednostkę SKU na wartość **Podstawowa**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Użyjesz tych wartości w dalszej części samouczka podczas publikowania obrazu Docker do rejestru, a podczas dodawania poświadczeń rejestru do środowiska wykonawczego krawędzi. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu krawędzi IoT
Pokaż następujące kroki należy jak utworzyć moduł krawędzi IoT na podstawie .NET core 2.0 przy użyciu programu Visual Studio Code i rozszerzenie Azure IoT krawędzi.
1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane terminali** otworzyć terminal zintegrowane kodzie VS.
2. W terminalu zintegrowanego, wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) **AzureIoTEdgeModule** szablonu w dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Utwórz projekt dla nowego modułu. Poniższe polecenie tworzy folder projektu **FilterModule**, z Twoim repozytorium kontenera. Drugi parametr powinien mieć postać `<your container registry name>.azurecr.io`, jeśli używasz rejestru kontenerów platformy Azure. Wprowadź następujące polecenie w bieżącym folderze roboczym:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Wybierz **pliku** > **Otwórz Folder**.
5. Przejdź do **FilterModule** folder i kliknij przycisk **wybierz Folder** otworzyć projektu w kodzie VS.
6. W kodzie VS explorer, kliknij przycisk **Program.cs** go otworzyć.

   ![Otwórz plik Program.cs][1]

7. W górnej części **FilterModule** przestrzeni nazw, Dodaj trzy `using` instrukcje dla typów używanych w późniejszym czasie na:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Dodaj `temperatureThreshold` zmienną **Program** klasy. Ta zmienna Określa wartość, której może przekraczać zmierzone temperatury dane do wysłania do Centrum IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Dodaj `MessageBody`, `Machine`, i `Ambient` klasy do **Program** klasy. Te klasy definiują oczekiwano elementu schema dla treści wiadomości przychodzących.

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

10. W **Init** metoda, kod tworzy i konfiguruje **DeviceClient** obiektu. Ten obiekt umożliwia modułu do nawiązania połączenia lokalnego środowiska uruchomieniowego Azure IoT krawędzi do wysyłania i odbierania wiadomości. Parametry połączenia używane w **Init** metody został dostarczony do modułu przez środowisko uruchomieniowe IoT krawędzi. Po utworzeniu **DeviceClient**, kod odczytuje TemperatureThreshold dwie modułu odpowiednie właściwości i rejestruje wywołanie zwrotne do odbierania wiadomości z Centrum IoT krawędzi za pośrednictwem **input1**punktu końcowego. Zastąp `SetInputMessageHandlerAsync` metody z nową i Dodaj `SetDesiredPropertyUpdateCallbackAsync` metodę dla żądanej właściwości aktualizacji. Aby to zrobić, Zamień ostatni wiersz **Init** metodę z następującym kodem:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Proerty TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Dodaj `onDesiredPropertiesUpdate` metodę **Program** klasy. Ta metoda odbiera aktualizacje na odpowiednie właściwości z dwie modułu i aktualizuje **temperatureThreshold** ze zmienną. Wszystkie moduły mają własne dwie moduł, który umożliwia skonfigurowanie z kodem uruchomionym wewnątrz modułu bezpośrednio z chmury.

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

12. Zastąp `PipeMessage` metody z `FilterMessages` metody. Ta metoda jest wywoływana, gdy moduł odbiera komunikat z Centrum IoT krawędzi. Odfiltrowuje wiadomości, które zgłosiły temperatury poniżej progu temperatura określonego przez dwie modułu. Dodano również **MessageType** właściwości komunikat z wartością ustawioną na **alertu**. 

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

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **module.json** i kliknij polecenie **Skompiluj i wypchnij obraz platformy Docker modułu usługi IoT Edge**. W podręcznym polu listy rozwijanej w górnej części okna programu VS Code wybierz platformę kontenera, **amd64** dla kontenera systemu Linux lub **windows-amd64** dla kontenera systemu Windows. Kod VS potem kompiluje kod, containerize `FilterModule.dll` i wypchnąć go do określonego rejestru kontenera.


3. Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w zintegrowanym terminalu programu VS Code. Aby uzyskać więcej informacji na temat definicji kompilacji i wypychania, zapoznaj się z plikiem `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Dodawanie poświadczeń rejestru do środowiska wykonawczego krawędzi
Dodaj poświadczenia dla rejestru do środowiska uruchomieniowego usługi Edge na komputerze, na którym jest uruchomione urządzenie usługi Edge. Te poświadczenia zapewniają dostęp środowiska uruchomieniowego do ściągnięcia kontenera. 

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
4. Sprawdź, czy **tempSensor** modułu jest wypełniane automatycznie. Jeśli nie, należy dodać ją za pomocą następujących czynności:
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `tempSensor`.
    3. W polu **Identyfikator URI obrazu** wprowadź wartość `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Pozostaw inne ustawienia bez zmian, a następnie kliknij pozycję **Zapisz**.
5. Dodaj **filterModule** moduł, który został utworzony w poprzednich sekcjach. 
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `filterModule`.
    3. W polu **Identyfikator URI obrazu** wprowadź adres obrazu, na przykład `<your container registry address>/filtermodule:0.0.1-amd64`. Pełen adres obrazu można znaleźć w poprzedniej sekcji.
    4. Sprawdź **włączyć** , dzięki czemu można edytować dwie modułu. 
    5. Zastąp dane JSON w polu tekstowym dla modułu dwie następujące JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Kliknij pozycję **Zapisz**.
6. Kliknij przycisk **Dalej**.
7. W kroku **Określanie tras** skopiuj poniższe dane JSON do pola tekstowego. Moduły opublikować wszystkie komunikaty do środowiska wykonawczego krawędzi. Deklaracyjne reguły w środowisku uruchomieniowym definiują, do których przepływu wiadomości. W tym samouczku należy na dwa sposoby. Pierwszy trasy transportu wiadomości z czujnika temperatury w module filtru za pośrednictwem punktu końcowego "input1", który jest punkt końcowy, który został skonfigurowany z **FilterMessages** programu obsługi. Druga trasa służy do transportu komunikatów z modułu filtru do centrum IoT Hub. W tej trasie element `upstream` jest specjalnym miejscem docelowym, które nakazuje centrum Edge Hub wysyłanie komunikatów do centrum IoT Hub. 

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
10. Wróć do strony szczegółów urządzenia usługi IoT Edge, a następnie wybierz pozycję **Odśwież**. Powinien zostać wyświetlony nowy **filtermodule** uruchomiona wraz z **tempSensor** modułu i **środowiska uruchomieniowego krawędzi IoT**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby monitorować urządzenie pod kątem komunikatów w chmurze wysyłanych z urządzenia usługi IoT Edge do centrum IoT Hub:
1. Skonfiguruj rozszerzenie Azure IoT Toolkit za pomocą parametrów połączenia na potrzeby centrum IoT Hub: 
    1. Otwórz Eksploratora kodzie VS wybierając **widoku** > **Explorer**. 
    2. W Eksploratorze kliknij **urządzenia IOT HUB** , a następnie kliknij przycisk **...** . Kliknij przycisk **ustawić parametry połączenia Centrum IoT** , a następnie wprowadź parametry połączenia dla Centrum IoT, które urządzenia IoT krawędzi łączy w wyskakującym oknie. 

        Aby znaleźć ciąg połączenia, kliknij Kafelek Centrum IoT w portalu Azure, a następnie kliknij przycisk **zasady dostępu współużytkowanego**. W **zasady dostępu współużytkowanego**, kliknij przycisk **iothubowner** zasad i skopiuj połączenia Centrum IoT ciąg w **iothubowner** okna.   

2. Do monitorowania danych otrzymywanych przez Centrum IoT, wybierz **widoku** > **palety polecenia** i wyszukaj **IoT: rozpocząć monitorowanie komunikat D2C** polecenia menu. 
3. Aby zatrzymać monitorowanie danych, należy użyć **IoT: zatrzymać monitorowanie komunikat D2C** polecenia menu. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku utworzony moduł IoT krawędzi, który zawiera kod, aby filtrować nieprzetworzone dane generowane przez urządzenia IoT krawędzi. Można kontynuować do jednej z następujących samouczków, aby uzyskać informacje o innych metod, które ułatwiają krawędź IoT Azure możesz przekształcić w danych biznesowych na krawędzi.

> [!div class="nextstepaction"]
> [Wdrażanie funkcji platformy Azure jako moduł](tutorial-deploy-function.md)
> [wdrożenia usługi Azure Stream Analytics jako moduł](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
