---
title: Użyj Visual Studio Code do opracowywania C# modułu Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Opracowywania i wdrażania modułu C# z krawędzią IoT Azure w programie Visual Studio Code, bez przełączania kontekstu.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 48c6cacebdeb7505c8dc2bcaed099c33862589ac
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Korzystanie z programu Visual Studio Code w celu projektowania modułu C# z krawędzią IoT Azure
Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [Visual Studio Code](https://code.visualstudio.com/) jako narzędzie programowanie głównego do opracowywania i wdrażania własnych modułów Azure IoT krawędzi. 

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenie brzegowe IoT może być inne urządzenie fizyczne lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

Przed rozpoczęciem tych wskazówek, wykonaj następujące samouczki:
- Wdrożenie usługi Azure IoT krawędzi na symulowane urządzenie w [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) lub [systemu Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Opracowywania i wdrażania modułu krawędzi IoT C# do symulowane urządzenie](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Poniżej przedstawiono listę kontrolną, która zawiera elementy powinna mieć po zakończeniu poprzedniego samouczki:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Rozszerzenie krawędzi IoT Azure dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# dla rozszerzenia Visual Studio Code (obsługiwane przez OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT krawędzią formantu skryptu](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Szablon AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Aktywnym Centrum IoT z co najmniej IoT urządzenia

Warto również zainstalować [Docker obsługę kodzie VS](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) aby lepiej zarządzać modułu obrazów i kontenerów.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Wdrażanie modułu Azure IoT krawędzi w kodzie VS

### <a name="list-your-iot-hub-devices"></a>Lista urządzeń Centrum IoT
Istnieją dwa sposoby, aby wyświetlić listę urządzeń Centrum IoT w kodzie VS. Można wybrać w obu przypadkach, aby kontynuować.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Zaloguj się do konta platformy Azure w kodzie VS, a następnie wybierz pozycję Centrum IoT
1. W palecie polecenia (F1 lub Ctrl + Shift + P), wpisz i wybierz **Azure: Zaloguj**. Następnie wybierz **skopiuj & Otwórz**. Wklej (Ctrl + V) kod w przeglądarce, a następnie wybierz **Kontynuuj**. Następnie zaloguj się przy użyciu konta platformy Azure. Informacje o swoim koncie w kodzie VS pasek stanu jest widoczny.
2. W palecie polecenia, wpisz i wybierz **IoT: Wybierz Centrum IoT**. Najpierw wybierz subskrypcję, której utworzono Centrum IoT w poprzednich instrukcji. Następnie wybierz pozycję Centrum IoT, które zawiera urządzenia IoT krawędzi.

    ![Zrzut ekranu przedstawiający listę urządzeń](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Ustawianie parametrów połączenia Centrum IoT
W palecie polecenia, wpisz i wybierz **IoT: Ustaw parametry połączenia Centrum IoT**. Upewnij się, że możesz wkleić łączącego ciągu w obszarze zasady **iothubowner**. (Można znaleźć go w zasadach dostępu współdzielonego z Centrum IoT w portalu Azure.)
 
Widać listy urządzeń w Eksploratorze urządzenia IoT Hub paska bocznego po lewej stronie.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Rozpoczynanie pracy programu runtime krawędzi IoT i wdrażanie modułu
Zainstaluj i uruchom środowiska uruchomieniowego Azure IoT Edge na urządzeniu. Wdrożenie symulowane czujnik moduł, który wysyła dane telemetryczne z Centrum IoT Azure.
1. Polecenie palety, wybierz **krawędzi: Instalator krawędzi** i wybierz z krawędzi IoT identyfikator urządzenia. Alternatywnie kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi w **listę urządzeń**i wybierz **krawędzi Instalatora**.

    ![Zrzut ekranu Instalatora krawędzi środowiska wykonawczego](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. W palecie polecenia Wybierz **krawędzi: Start krawędzi** można uruchomić programu runtime IoT krawędzi. Widać odpowiednie dane wyjściowe w zintegrowanym terminala.

    ![Zrzut ekranu Start krawędzi środowiska wykonawczego](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Sprawdź stan środowiska uruchomieniowego IoT krawędzi w Eksploratorze rozwiązania Docker. Zielony oznacza, że jest uruchomiona, a Twoje środowisko uruchomieniowe krawędzi IoT został uruchomiony pomyślnie. Komputer jest teraz symuluje urządzenia IoT.

    ![Zrzut ekranu krawędzi stanu w czasie wykonywania](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Aby symulować czujnika, który wysyła wiadomości do Twojego urządzenia IoT krawędzi. W palecie polecenia, wpisz i wybierz **krawędzi: plik konfiguracji Generowanie krawędzi**. Wybierz folder do utworzenia tego pliku. W pliku deployment.json, który jest generowany, zastąpi zawartość `<registry>/<image>:<tag>` z `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`i Zapisz plik.

    ![Zrzut ekranu przedstawiający czujnik modułu](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Wybierz **krawędzi: tworzenie wdrożenia dla urządzenie brzegowe**i wybierz polecenie krawędzi IoT identyfikator urządzenia można utworzyć nowego wdrożenia. Alternatywnie można kliknąć prawym przyciskiem myszy identyfikator urządzenia IoT Edge na liście urządzeń i wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. 

6. Powinna zostać wyświetlona z krawędzi IoT uruchomione w Eksploratorze rozwiązania Docker z czujnika symulowane. Kliknij prawym przyciskiem myszy kontener, w Eksploratorze rozwiązania Docker. Możesz obserwować Docker dzienniki dla każdego modułu. Ponadto można wyświetlić listy modułów na liście urządzeń.

    ![Zrzut ekranu przedstawiający listę modułu](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a można monitorować D2C wiadomości w kodzie VS.
8. Aby zatrzymać programu runtime krawędzi IoT i moduł czujnika, wpisz i wybierz **krawędzi: Zatrzymaj krawędzi** w palecie polecenia.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Tworzenie i wdrażanie modułu C# w kodzie VS
W samouczku [opracowanie modułu C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), aktualizowanie, tworzenie i publikowanie obrazu modułu w kodzie VS. Następnie przejdź do portalu Azure, aby wdrożyć modułu C#. W tej sekcji przedstawiono sposób użycia kodzie VS do wdrażania i monitorowania modułu C#.

### <a name="start-a-local-docker-registry"></a>Uruchom lokalnego rejestru Docker
Można użyć dowolnego rejestru Docker zgodnego tego artykułu. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ta sekcja używa [lokalnego rejestru Docker](https://docs.docker.com/registry/deploying/), który jest łatwiejsze testowanie podczas wcześniejszego programowania.
W kodzie VS **zintegrowane terminal** (Ctrl + "), uruchom następujące polecenie, aby uruchomić lokalnego rejestru:  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Ten przykład przedstawia konfiguracji rejestru, które są tylko odpowiednie do testowania. Rejestru gotowe do produkcji musi być chroniony przez protokół TLS, a w idealnym przypadku należy używać mechanizmu kontroli dostępu. Zalecane jest użycie [rejestru kontenera Azure](https://docs.microsoft.com/azure/container-registry/) lub [Centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) wdrażania modułów krawędzi IoT gotowe do produkcji.

### <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu krawędzi IoT
Poniższe kroki pokazują, jak utworzyć moduł krawędzi IoT oparte na .NET Core 2.0, za pomocą programu Visual Studio Code i rozszerzenie Azure IoT krawędzi. Jeśli po wykonaniu tej sekcji samouczka poprzedniej, można bezpiecznie pominąć tę sekcję.
1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane terminali** otworzyć terminal zintegrowane kodzie VS.
3. W terminalu zintegrowanego, wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) **AzureIoTEdgeModule** szablonu w dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Utwórz projekt dla nowego modułu. Poniższe polecenie tworzy folder projektu **FilterModule**, w bieżącym folderze roboczych:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Wybierz **pliku** > **Otwórz Folder**.
4. Przejdź do **FilterModule** folder, a następnie kliknij przycisk **wybierz Folder** otworzyć projektu w kodzie VS.
5. W Eksploratorze kodu VS wybierz **Program.cs** go otworzyć. W górnej części **program.cs**, obejmują następujących przestrzeni nazw:
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Dodaj `temperatureThreshold` zmienną **Program** klasy. Ta zmienna Określa wartość, której może przekraczać zmierzone temperatury dane do wysłania do Centrum IoT. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Dodaj `MessageBody`, `Machine`, i `Ambient` klasy do **Program** klasy. Te klasy definiują oczekiwano elementu schema dla treści wiadomości przychodzących.

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

8. W **Init** metoda, kod tworzy i konfiguruje **DeviceClient** obiektu. Ten obiekt umożliwia modułu do nawiązania połączenia lokalnego środowiska uruchomieniowego krawędzi IoT do wysyłania i odbierania wiadomości. Środowisko uruchomieniowe krawędzi IoT dostarcza do modułu, ciąg połączenia używany w **Init** metody. Po utworzeniu **DeviceClient** obiekt kod rejestruje wywołanie zwrotne do odbierania wiadomości z Centrum IoT krawędzi za pośrednictwem **input1** punktu końcowego. Zastąp `SetInputMessageHandlerAsync` metody z nową i Dodaj `SetDesiredPropertyUpdateCallbackAsync` metodę dla żądanej właściwości aktualizacji. Aby to zrobić, Zamień ostatni wiersz **Init** metodę z następującym kodem:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Dodaj `onDesiredPropertiesUpdate` metodę **Program** klasy. Ta metoda odbiera aktualizacje na odpowiednie właściwości z dwie modułu i aktualizuje **temperatureThreshold** ze zmienną. Wszystkie moduły mają własne dwie moduł, który umożliwia skonfigurowanie z kodem uruchomionym wewnątrz modułu bezpośrednio z chmury.

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

10. Zastąp `PipeMessage` metody z `FilterMessages` metody. Ta metoda jest wywoływana, gdy moduł odbiera wiadomości z Centrum IoT krawędzi. Odfiltrowuje wiadomości, które zgłosiły temperatury poniżej progu temperatura określonego przez dwie modułu. Dodano również **MessageType** właściwości komunikat z wartością ustawioną na **alertu**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
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
            DeviceClient deviceClient = (DeviceClient)userContext;
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

11. Aby skompilować projekt, kliknij prawym przyciskiem myszy **FilterModule.csproj** plików w Eksploratorze i wybierz **krawędzi IoT Tworzenie modułu**. Ten proces kompiluje modułu i eksportuje dane binarne i jego zależności w folderze, który jest używany do tworzenia obrazu Docker. 

    ![Zrzut ekranu przedstawiający Eksplorator kodu VS](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Tworzenie obrazu Docker i publikowanie go do rejestru

1. W Eksploratorze kodu VS rozwiń **Docker** folderu. Następnie rozwiń folder dla platformy kontenera — **linux-x64** lub **windows-nano**.
2. Kliknij prawym przyciskiem myszy **plik Dockerfile** pliku, a następnie wybierz **krawędzi IoT Tworzenie modułu Docker obrazu**. 

    ![Zrzut ekranu przedstawiający Eksplorator kodu VS](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. W **wybierz Folder** okna, przejdź do albo wprowadź `./bin/Debug/netcoreapp2.0/publish`. Wybierz **wybierz Folder jako EXE_DIR**.
4. W wyskakującym polu tekstowym w górnej części okna kodu programu VS Code wprowadź nazwę obrazu. Na przykład: `<your container registry address>/filtermodule:latest`. Jeśli są wdrażane w lokalnym rejestrze, powinien być `localhost:5000/filtermodule:latest`.
5. Wypchnij obraz do repozytorium platformy Docker. Użyj **krawędzi: Push krawędzi IoT modułu Docker obrazu** polecenia i wprowadź adres URL obrazu, w polu tekstowym wyskakujących w górnej części okna kodu programu VS. Użyj tego samego adresu URL obraz używany w poprzednim kroku. Upewnij się, że obraz został pomyślnie przypisany za pomocą dzienniku konsoli.

    ![Zrzut ekranu przedstawiający wypychanie obrazu Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![zrzut ekranu konsoli dziennika](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Wdrażanie własnych modułów krawędzi IoT

1. Otwórz `deployment.json` plików i Zastąp **modułów** sekcji z następujących czynności:
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
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Zastąp **tras** sekcji z następujących czynności:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Deklaracyjne reguły w środowisku uruchomieniowym definiują, do których przepływu wiadomości. W tym artykule należy na dwa sposoby. Pierwsza trasa służy do transportu komunikatów z czujnika temperatury do modułu filtru za pośrednictwem punktu końcowego „input1”. To jest punkt końcowy, który został skonfigurowany z obsługą FilterMessages. Druga trasa służy do transportu komunikatów z modułu filtru do centrum IoT Hub. W tej trasy nadrzędnego jest specjalne lokalizacji docelowej, która informuje Centrum IoT krawędzi do wysyłania komunikatów do Centrum IoT.

3. Zapisz ten plik.
4. W palecie polecenia Wybierz **krawędzi: tworzenie wdrożenia dla urządzenie brzegowe**. Następnie wybierz identyfikator urządzenia IoT krawędzi Aby utworzyć wdrożenie. Lub kliknij prawym przyciskiem myszy identyfikator urządzenia na liście urządzeń i wybierz **tworzenie wdrożenia dla urządzenie brzegowe**.

    ![Zrzut ekranu utworzyć opcji wdrażania](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Wybierz `deployment.json` aktualne. W oknie danych wyjściowych widać odpowiednie dane wyjściowe dla danego wdrożenia.

    ![Zrzut ekranu przedstawiający okno danych wyjściowych](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Uruchom programu runtime IoT krawędzi w palecie polecenia (wybierz **krawędzi: Start krawędzi**).
7. Twoje krawędzi IoT środowiska uruchomieniowego uruchomione w Eksploratorze rozwiązania Docker symulowane czujnik i moduł filtru jest widoczny.

    ![Zrzut ekranu przedstawiający Eksploratora Docker](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a można monitorować D2C wiadomości w kodzie VS.


## <a name="next-steps"></a>Kolejne kroki

[Debugowanie modułu C# w kodzie VS](how-to-vscode-debug-csharp-module.md)
