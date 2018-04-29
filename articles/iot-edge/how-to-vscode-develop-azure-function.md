---
title: Użyj programu Visual Studio Code do opracowywania i wdrażania usługi Azure Functions do usługi Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Tworzenie i wdrażanie C# usługi Azure Functions Azure IoT krawędzi w kodzie VS bez przełączania kontekstu
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 47d420b4b283b390f67719233c4bea59495a589a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Użyj programu Visual Studio Code do opracowywania i wdrażania usługi Azure Functions do krawędzi IoT Azure

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [Visual Studio Code](https://code.visualstudio.com/) jako narzędzie do projektowania głównego do opracowywania i wdrażania usługi Azure Functions na krawędzi IoT. 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenia IoT krawędzi może być inne fizyczne urządzenie lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

Upewnij się, że masz następujące samouczki ukończone przed rozpoczęciem tej wskazówki.
- Wdrożenie usługi Azure IoT krawędzi na symulowane urządzenie w [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) lub [systemu Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Wdrażanie usługi Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

Poniżej przedstawiono listę kontrolną, która zawiera elementy powinna mieć po zakończeniu poprzedniego samouczki.

- [Program Visual Studio Code](https://code.visualstudio.com/) 
- [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [Zestaw .NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT krawędzią formantu skryptu](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Szablon AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- Aktywnym Centrum IoT z co najmniej IoT urządzenia.

Proponowana jest również zainstalować [Docker obsługę kodzie VS](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) aby lepiej zarządzać modułu obrazów i kontenerów.

> [!NOTE]
> Obecnie usługi Azure Functions na krawędzi IoT obsługuje tylko C#.

## <a name="deploy-azure-iot-functions-in-vs-code"></a>Wdrażanie funkcji Azure IoT w kodzie VS
W samouczku [wdrożenia usługi Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function), zaktualizować, kompilacji i opublikować funkcji modułu obrazów w kodzie VS, a następnie odwiedź portal Azure w celu wdrożenia usługi Azure Functions. W tej sekcji przedstawiono sposób użycia kodzie VS do wdrażania i monitorowania funkcji platformy Azure.

### <a name="start-a-local-docker-registry"></a>Uruchom rejestru lokalnych docker
Można użyć dowolnego rejestru Docker zgodnego tego artykułu. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ta sekcja używa [lokalnego rejestru Docker](https://docs.docker.com/registry/deploying/), który jest łatwiejsze do celów testowania podczas wcześniejszego programowania.
W kodzie VS **zintegrowane terminal**(Ctrl + "), uruchom następujące polecenia, aby uruchomić w rejestrze lokalnym.  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Powyżej przykładzie pokazano konfiguracji rejestru, które są tylko odpowiednie do testowania. Rejestru gotowe do produkcji musi być chroniony przez protokół TLS i najlepiej należy używać mechanizmu kontroli dostępu. Zalecane jest użycie [rejestru kontenera Azure](https://docs.microsoft.com/azure/container-registry/) lub [Centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) wdrażania modułów krawędzi IoT gotowe do produkcji.

### <a name="create-a-function-project"></a>Tworzenie projektu funkcji
Pokaż następujące kroki należy jak utworzyć moduł krawędzi IoT na podstawie .NET core 2.0 przy użyciu programu Visual Studio Code i rozszerzenie Azure IoT krawędzi. Jeśli po wykonaniu tej sekcji samouczka poprzedniej, można bezpiecznie pominąć tę sekcję.

1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane terminali** otworzyć terminal zintegrowane kodzie VS.
2. Aby zainstalować (lub zaktualizować) szablon **AzureIoTEdgeFunction** za pomocą polecenia dotnet, uruchom następujące polecenie w zintegrowanym terminalu:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. Utwórz projekt dla nowego modułu. Poniższe polecenie służy do tworzenia folderu projektu **FilterFunction** w bieżącym folderze roboczym:

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. Wybierz **Plik > Otwórz Folder**, a następnie wyszukaj **funkcji FilterFunction** folderu i Otwórz projekt w kodzie VS.
5. Przejdź do **funkcji FilterFunction** folder i kliknij przycisk **wybierz Folder** otworzyć projektu w kodzie VS.
6. W eksploratorze programu VS Code rozwiń węzeł **EdgeHubTrigger-Csharp**, a następnie otwórz plik **run.csx**.
7. Zastąp zawartość pliku następującym kodem:

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

8. Zapisz plik.

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Tworzenie obrazu platformy Docker i publikowanie go w rejestrze

1. W eksploratorze programu VS Code rozwiń folder **Docker**. Następnie rozwiń folder dla platformy kontenera — **linux-x64** lub **windows-nano**.
2. Kliknij prawym przyciskiem myszy plik **Dockerfile**, a następnie kliknij pozycję **Zbuduj obraz platformy Docker modułu usługi IoT Edge**. 

    ![Utwórz obraz docker](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. Przejdź do folderu projektu **FilterFunction**, a następnie kliknij pozycję **Wybierz folder jako EXE_DIR**. 
4. W wyskakującym polu tekstowym w górnej części okna kodu programu VS Code wprowadź nazwę obrazu. Na przykład: `<your container registry address>/filterfunction:latest`. Jeśli są wdrażane w lokalnym rejestrze, powinien być `localhost:5000/filterfunction:latest`.
5. Wypchnij obraz do repozytorium platformy Docker. Użyj **krawędzi: Push krawędzi IoT modułu Docker obrazu** polecenia i wprowadź adres URL obrazu, w polu tekstowym wyskakujących w górnej części okna kodu programu VS. Użyj używany w ponad krok ten sam adres URL obrazu.
    ![Obraz docker wypychania](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>Wdrażanie funkcji na krawędzi IoT

1. Otwórz `deployment.json` pliku, Zastąp **modułów** sekcji z poniżej zawartości:
   ```json
   "tempSensor": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
         "createOptions": ""
      }
   },
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. Zastąp **tras** sekcji z poniżej zawartości:
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > Deklaracyjne reguły w środowisku uruchomieniowym definiują, do których przepływu wiadomości. W tym artykule należy na dwa sposoby. Pierwszy trasy transportu wiadomości z czujnika temperatury funkcji filtru za pośrednictwem punktu końcowego "input1", który jest punkt końcowy, który został skonfigurowany z obsługą FilterMessages. Drugi trasy transportu wiadomości z funkcji filtr do Centrum IoT. W tej trasy nadrzędnego jest specjalne lokalizacji docelowej, która informuje krawędzi koncentratora do wysyłania komunikatów do Centrum IoT.

3. Zapisz ten plik.
4. Polecenie palety, wybierz **krawędzi: tworzenie wdrożenia dla urządzenie brzegowe**. Następnie wybierz identyfikator urządzenia IoT krawędzi Aby utworzyć wdrożenie. Lub kliknij prawym przyciskiem myszy identyfikator urządzenia na liście urządzeń i wybierz polecenie **tworzenie wdrożenia dla urządzenie brzegowe**.

    ![Tworzenie wdrożenia](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. Wybierz `deployment.json` aktualne. W oknie danych wyjściowych widać odpowiednie dane wyjściowe dla danego wdrożenia.
6. Uruchomienie programu runtime krawędzi w palety polecenia. **Krawędź: Krawędzi początkowej**
7. Widać programu runtime krawędzi IoT uruchomione w Eksploratorze rozwiązania Docker funkcją symulowane czujników i filtru.

    ![Działającej](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Kliknij prawym przyciskiem myszy identyfikator urządzenia brzegowe i monitorować D2C wiadomości w kodzie VS.

    ![Monitor wiadomości](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>Kolejne kroki

[Debugowanie funkcji platformy Azure w kodzie VS](how-to-vscode-debug-azure-function.md)
