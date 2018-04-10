---
title: Wdrażanie funkcji platformy Azure za pomocą usługi Azure IoT Edge | Microsoft Docs
description: Wdrażanie funkcji platformy Azure jako moduł na urządzeniu usługi Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d7dd0986878c747f92afc712301453bc8772ef2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Wdrażanie funkcji platformy Azure jako moduł usługi IoT Edge — wersja zapoznawcza
Możesz użyć usługi Azure Functions, aby wdrożyć kod implementujący Twoją logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek zawiera instrukcje dotyczące tworzenia i wdrażania funkcji platformy Azure służącej do filtrowania danych czujników na symulowanym urządzeniu usługi IoT Edge utworzonym na podstawie informacji zawartych w samouczkach dotyczących wdrażania usługi Azure IoT Edge na symulowanym urządzeniu w systemie [Windows][lnk-tutorial1-win] lub [Linux][lnk-tutorial1-lin]. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:     

> [!div class="checklist"]
> * Używanie programu Visual Studio Code do tworzenia funkcji platformy Azure
> * Używanie programu VS Code i platformy Docker do tworzenia obrazu platformy Docker i publikowania go w rejestrze 
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Funkcja platforma Azure utworzona w tym samouczku służy do filtrowania danych dotyczących temperatury wygenerowanych przez urządzenia i wysyła komunikaty w ramach strumienia wychodzącego do usługi Azure IoT Hub tylko wtedy, gdy temperatura jest większa niż określona wartość progowa. 

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge utworzone w ramach przewodnika Szybki start lub poprzedniego samouczka
* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Platforma Docker](https://docs.docker.com/engine/installation/) Wersja Community Edition (CE) dla Twojej platformy jest wystarczająca na potrzeby tego samouczka 
* [Zestaw .NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.
2. Nazwij rejestr, wybierz subskrypcję i grupę zasobów oraz ustaw jednostkę SKU na wartość **Podstawowa**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości zostaną wykorzystane później w samouczku. 

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji
W następujących krokach przedstawiono sposób tworzenia funkcji usługi IoT Edge przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.
1. Otwórz program Visual Studio Code.
2. Aby otworzyć zintegrowany terminal programu VS Code, wybierz pozycję **Widok** > **Zintegrowany terminal**.
3. Aby zainstalować (lub zaktualizować) szablon **AzureIoTEdgeFunction** za pomocą polecenia dotnet, uruchom następujące polecenie w zintegrowanym terminalu:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Utwórz projekt dla nowego modułu. Poniższe polecenie utworzy folder projektu, **FilterFunction**, w Twoim repozytorium kontenerów. Drugi parametr powinien mieć postać `<your container registry name>.azurecr.io`, jeśli używasz rejestru kontenerów platformy Azure. Wprowadź następujące polecenie w bieżącym folderze roboczym:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Wybierz pozycję **Plik** > **Otwórz Folder**, a następnie przejdź do folderu **FilterFunction** i otwórz projekt w programie VS Code.
4. W eksploratorze programu VS Code rozwiń węzeł **EdgeHubTrigger-Csharp**, a następnie otwórz plik **run.csx**.
5. Zastąp zawartość pliku następującym kodem:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

11. Zapisz plik.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Tworzenie obrazu platformy Docker i publikowanie go w rejestrze

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Aby znaleźć nazwę użytkownika, hasło i serwer logowania do użycia w tym poleceniu, przejdź do witryny Azure Portal (https://portal.azure.com)). W obszarze **Wszystkie zasoby** kliknij kafelek usługi Azure Container Registry, aby otworzyć jej właściwości, a następnie kliknij pozycję **Klucze dostępu**. Skopiuj wartości w polach **Nazwa użytkownika**, **Hasło** i **Serwer logowania**. 

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **module.json** i kliknij polecenie **Skompiluj i wypchnij obraz platformy Docker modułu usługi IoT Edge**. W podręcznym polu listy rozwijanej w górnej części okna programu VS Code wybierz platformę kontenera, **amd64** dla kontenera systemu Linux lub **windows-amd64** dla kontenera systemu Windows. Program VS Code utworzy kontener z kodem funkcji i wypchnie go do określonego rejestru kontenerów.


3. Pełny adres obrazu kontenera możesz uzyskać za pomocą tagu w zintegrowanym terminalu programu VS Code. Aby uzyskać więcej informacji na temat definicji kompilacji i wypychania, zapoznaj się z plikiem `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Dodawanie poświadczeń rejestru na urządzeniu usługi Edge
Dodaj poświadczenia dla rejestru do środowiska uruchomieniowego usługi Edge na komputerze, na którym jest uruchomione urządzenie usługi Edge. Daje to dostęp do środowiska uruchomieniowego w celu ściągnięcia kontenera. 

- W systemie Windows uruchom następujące polecenie:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- W systemie Linux uruchom następujące polecenie:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Uruchamianie rozwiązania

1. W witrynie **Azure Portal** przejdź do centrum IoT Hub.
2. Przejdź do pozycji **IoT Edge (wersja zapoznawcza)** i wybierz urządzenie usługi IoT Edge.
1. Wybierz pozycję **Ustaw moduły**. 
2. Jeśli moduł **tempSensor** został już wdrożony na tym urządzeniu, może on być automatycznie wypełniony. Jeśli nie, wykonaj następujące kroki, aby go dodać:
    1. Wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `tempSensor`.
    3. W polu **Identyfikator URI obrazu** wprowadź wartość `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Pozostaw inne ustawienia bez zmian, a następnie kliknij pozycję **Zapisz**.
1. Dodaj moduł **filterFunction**.
    1. Ponownie wybierz pozycję **Dodaj moduł usługi IoT Edge**.
    2. W polu **Nazwa** wprowadź wartość `filterFunction`.
    3. W polu **Identyfikator URI obrazu** wprowadź adres obrazu, na przykład `<your container registry address>/filterfunction:0.0.1-amd64`. Pełen adres obrazu można znaleźć w poprzedniej sekcji.
    74. Kliknij pozycję **Zapisz**.
2. Kliknij przycisk **Dalej**.
3. W kroku **Określanie tras** skopiuj poniższe dane JSON do pola tekstowego. Pierwsza trasa służy do transportu komunikatów z czujnika temperatury do modułu filtru za pośrednictwem punktu końcowego „input1”. Druga trasa służy do transportu komunikatów z modułu filtru do centrum IoT Hub. W tej trasie element `$upstream` jest specjalnym miejscem docelowym, które nakazuje centrum Edge Hub wysyłanie komunikatów do centrum IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Kliknij przycisk **Dalej**.
5. W kroku **Przegląd szablonu** kliknij pozycję **Prześlij**. 
6. Wróć do strony szczegółów urządzenia usługi IoT Edge, a następnie wybierz pozycję **Odśwież**. Powinien zostać wyświetlony nowy moduł **filterfunction** uruchomiony wraz z modułem **tempSensor** i **środowiskiem uruchomieniowym usługi IoT Edge**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby monitorować urządzenie pod kątem komunikatów w chmurze wysyłanych z urządzenia usługi IoT Edge do centrum IoT Hub:
1. Skonfiguruj rozszerzenie Azure IoT Toolkit za pomocą parametrów połączenia na potrzeby centrum IoT Hub: 
    1. W witrynie Azure Portal przejdź do centrum IoT Hub, a następnie wybierz pozycję **Zasady dostępu współużytkowanego**. 
    2. Wybierz pozycję **iothubowner**, a następnie skopiuj wartość **Parametry połączenia — klucz podstawowy**.
    3. W eksploratorze programu VS Code kliknij pozycję **IOT HUB DEVICES**, a następnie kliknij pozycję **...**. 
    4. Wybierz pozycję **Ustaw parametry połączenia centrum IoT Hub**, a następnie wprowadź parametry połączenia centrum IoT Hub w oknie podręcznym. 

2. Aby monitorować dane otrzymywane przez centrum IoT Hub, wybierz pozycję **Widok** > **Paleta poleceń...** , a następnie wyszukaj ciąg **IoT: rozpocznij monitorowania komunikatu D2C**. 
3. Aby zatrzymać monitorowanie danych, użyj polecenia **IoT: zatrzymaj monitorowanie komunikatu D2C** w palecie poleceń. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzona została funkcja platformy Azure zawierająca kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Aby kontynuować eksplorowanie usługi Azure IoT Edge, dowiedz się, jak używać urządzenia usługi IoT Edge jako bramy. 

> [!div class="nextstepaction"]
> [Tworzenie urządzenia bramy usługi IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
