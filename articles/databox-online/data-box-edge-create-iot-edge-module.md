---
title: C# moduł usługi IoT Edge dla usługi Azure Data Box Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie modułu C# usługi IoT Edge, który można wdrożyć na krawędzi sieci pola danych.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: c2803ba598895834bb197f4a06ff0635354fcaca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680892"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Twórz C# moduł usługi IoT Edge, aby przenieść pliki na krawędzi pola danych

Ten artykuł przeprowadzi Cię przez jak utworzyć moduł usługi IoT Edge dla wdrożenia przy użyciu urządzenia usługi Edge pola danych. Azure Data Box Edge to rozwiązanie magazynu umożliwiające przetwarzanie danych i wysyłanie ich za pośrednictwem sieci na platformę Azure.

Za pomocą usługi Azure IoT Edge modułów i krawędzi pola danych, sieci przekształcać dane ponieważ przeniesione do platformy Azure. Moduł używane w tym artykule implementuje logikę do kopiowania pliku z udziału lokalnego do udziału chmury na twoim urządzeniu krawędź pola danych.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie rejestru kontenerów do przechowywania i zarządzania nimi moduły (obrazy platformy Docker).
> * Utwórz moduł usługi IoT Edge można wdrożyć na urządzeniu usługi Edge pola danych.


## <a name="about-the-iot-edge-module"></a>Moduł usługi IoT Edge — informacje

Urządzenie brzegowe pole danych wdrażać i uruchamiać moduły usługi IoT Edge. Moduły usługi Edge to zasadniczo kontenerów platformy Docker, które wykonywania określonych zadań, takich jak odbieranie komunikatów z urządzenia, przekształcić wiadomość lub wysyłania komunikatu do usługi IoT Hub. W tym artykule utworzy moduł, który kopiuje pliki z udziału lokalnego do udziału chmury na twoim urządzeniu krawędź pola danych.

1. Pliki są zapisywane w udziale lokalnym na swoim urządzeniu krawędź pola danych.
2. Generator zdarzeń pliku Tworzy zdarzenie pliku dla każdego pliku zapisywane do udziału lokalnego. Zdarzenia pliku również są generowane, gdy plik zostanie zmodyfikowany. Zdarzenia w pliku są następnie wysyłane do usługi IoT Edge Hub (środowisko uruchomieniowe usługi IoT Edge).
3. Niestandardowy moduł usługi IoT Edge przetwarza zdarzenia pliku do utworzenia obiektu zdarzenia pliku, który również zawiera ścieżkę względną do pliku. Moduł generuje przy użyciu ścieżki względnej ścieżki bezwzględnej i kopiuje plik z udziału lokalnego do udziału w chmurze. Moduł następnie usuwa plik z lokalnego udziału.

![Jak działa moduł usługi Azure IoT Edge na krawędzi pola danych](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Gdy plik znajduje się w udziale chmury, automatycznie zostaje on przesłany do konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Urządzenie krawędź pola danych, które jest uruchomiona.

    - Urządzenie ma również skojarzony zasób usługi IoT Hub.
    - Urządzenie ma krawędzi obliczenia skonfigurowaną rolą.
    Aby uzyskać więcej informacji, przejdź do [Konfigurowanie obliczeń](data-box-edge-deploy-configure-compute.md#configure-compute) na krawędzi sieci pola danych.

- Programowanie w następujących zasobach:

    - [Program Visual Studio Code](https://code.visualstudio.com/)
    - [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
    - [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Program Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Może być konieczne Utwórz konto Aby pobrać i zainstalować oprogramowanie.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Usługa Azure Container Registry to rejestr prywatny platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów Docker i zarządzać nimi. Dwie popularne usługi rejestru Docker dostępne w chmurze są usługi Azure Container Registry i Docker Hub. W tym artykule korzysta z rejestru kontenerów.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Wybierz **Utwórz zasób > kontenery > Container Registry**. Kliknij pozycję **Utwórz**.
3. Zapewniają:

   1. Unikatowy **nazwa rejestru** w obrębie platformy Azure, która zawiera 5 do 50 znaków alfanumerycznych.
   2. Wybierz **subskrypcji**.
   3. Utwórz nową lub wybierz istniejącą **grupy zasobów**.
   4. Wybierz **lokalizację**. Zaleca się, że ta lokalizacja być taka sama, jak skojarzony z zasobem krawędź pola danych.
   5. Przełącz pozycję **Administrator** na wartość **Włącz**.
   6. Ustaw jednostkę SKU **podstawowe**.

      ![Tworzenie rejestru kontenerów](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Wybierz pozycję **Utwórz**.
5. Po utworzeniu rejestru kontenerów przejdź do niego i wybierz pozycję **Klucze dostępu**.

    ![Uzyskiwanie dostępu do kluczy](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Można użyć tych wartości później na opublikowanie obrazu platformy Docker do rejestru i dodawanie poświadczeń rejestru do środowiska uruchomieniowego usługi Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge

Poniższe kroki umożliwiają utworzenie usługi IoT Edge modułu projektu na podstawie zestawu SDK programu .NET Core 2.1. Projekt korzysta z programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu.

1. W programie Visual Studio Code wybierz **Widok > paletę poleceń** aby otworzyć paletę poleceń programu VS Code.
2. W palecie poleceń wprowadź i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)** , a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie:

    1. Wybierz folder, w którym chcesz utworzyć rozwiązanie.
    2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
    
        ![Utwórz nowe rozwiązanie 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Wybierz szablon modułu **C# Module** (Moduł języka C#).
    4. Zamień na nazwę, którą chcesz przypisać domyślną nazwę modułu, w tym przypadku jest to **FileCopyModule**.
    
        ![Utwórz nowe rozwiązanie 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Określ rejestru kontenerów, utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania.

        Końcowy ciąg wygląda jak `<Login server name>/<Module name>`. W tym przykładzie ciąg jest: `mycontreg2.azurecr.io/filecopymodule`.

        ![Utwórz nowe rozwiązanie 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Przejdź do **Plik > Otwórz Folder**.

    ![Utwórz nowe rozwiązanie 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Przeglądaj, a następnie wskaż **EdgeSolution** folder, który został utworzony wcześniej. Okna programu VS Code ładuje obszar roboczy rozwiązania usługi IoT Edge przy użyciu ich pięć elementów najwyższego poziomu. Nie będzie edytował **.vscode** folderu **.gitignore** pliku **ENV** pliku, a **deployment.template.json** w tym artykule.
    
    Jedyny składnik, który modyfikujesz jest folder modułów. Ten folder zawiera kod C# dla modułu i plików platformy Docker do tworzenia modułu jako obrazu kontenera.

    ![Utwórz nowe rozwiązanie 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. Otwórz w Eksploratorze programu VS Code **modułów > FileCopyModule > Program.cs**.
2. W górnej części **przestrzeni nazw FileCopyModule**, Dodaj następujące instrukcje using dla typów, które są używane w dalszej części. **Microsoft.Azure.Devices.Client.Transport.Mqtt** jest protokołem, aby wysyłać komunikaty do usługi IoT Edge Hub.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Dodaj **InputFolderPath** i **OutputFolderPath** zmiennej do klasy Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Dodaj **Element MessageBody** klasy do klasy Program. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. W metodzie **Init** kod tworzy i konfiguruje obiekt **ModuleClient**. Ten obiekt umożliwia modułu nawiązać połączenie lokalne środowisko uruchomieniowe usługi Azure IoT Edge, wysyłanie i odbieranie wiadomości przy użyciu protokołu MQTT. Parametry połączenia, który jest używany w metodzie Init jest dostarczany do modułu przez środowisko uruchomieniowe usługi IoT Edge. Ten kod rejestruje wywołanie zwrotne operacja FileCopy do odbierania komunikatów z Centrum IoT Edge za pośrednictwem **wejście1** punktu końcowego.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Wstaw kod **operacja FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Zapisz ten plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzyliśmy rozwiązanie IoT Edge i dodać kod do FileCopyModule do kopiowania plików z udziału lokalnego do udziału w chmurze. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. W programu VSCode, przejdź do terminala > nowe terminalu, aby otworzyć nowy zintegrowany terminal programu Visual Studio Code.
2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu.

    `docker login <ACR login server> -u <ACR username>`

    Użyj logowania serwera i nazwa użytkownika, który został skopiowany z rejestru kontenerów. 

    ![Kompilowanie i wypychanie rozwiązania usługi IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po wyświetleniu monitu o hasło, należy podać hasło. Możesz również pobrać wartości dla nazwy logowania serwera, nazwę użytkownika i hasło za pomocą **klucze dostępu** w rejestrze kontenera w witrynie Azure portal.
 
3. Gdy podano poświadczenia, możesz wypchnąć obraz moduł do usługi Azure container registry. W Eksploratorze programu VS Code kliknij prawym przyciskiem myszy **module.json** plik i wybierz **rozwiązanie kompilacji i wypychania usługi IoT Edge**.

    ![Kompilowanie i wypychanie rozwiązania usługi IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Gdy wiadomo, Visual Studio Code, aby utworzyć swoje rozwiązanie, jest ono wykonywane dwa polecenia w zintegrowanym terminalu: kompilacji platformy docker i wypychania platformy docker. Te dwa polecenia kompilują kod, konteneryzują plik CSharpModule.dll i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania.

    Zostanie wyświetlony monit wybierz platformę modułu. Wybierz *amd64* odpowiadający systemu Linux.

    ![Wybierz platformę](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Obsługiwane są tylko moduły systemu Linux.

    Zobaczysz poniższe ostrzeżenie, który można zignorować:

    *Program.cs(77,44): ostrzeżenie CS1998: Tej metodzie asynchronicznej brakuje operatorów "await" i zostanie ona uruchomiona synchronicznie. Należy wziąć pod uwagę, aby poczekać na nieblokujące wywołania interfejsów API za pomocą operatora "await" lub "await Task.Run(...)" do wykonania pracy Procesora CPU w wątku tła.*

4. Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu jest tworzona na podstawie informacji w pliku module.json przy użyciu formatu `<repository>:<version>-<platform>`. W tym artykule, powinien on wyglądać podobnie `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Kolejne kroki

Aby wdrożyć i uruchomić ten moduł na krawędzi pola danych, zobacz kroki opisane w [Dodaj moduł](data-box-edge-deploy-configure-compute.md#add-a-module).
