---
title: Moduł Usługi IoT Edge języka C# dla usługi Azure Data Box Edge | Dokumenty firmy Microsoft
description: Dowiedz się, jak opracować moduł usługi IoT Edge języka C#, który można wdrożyć w witrynie Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942553"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Tworzenie modułu C# IoT Edge w celu przenoszenia plików na krawędzi pola danych

W tym artykule opisano, jak utworzyć moduł usługi IoT Edge do wdrożenia za pomocą urządzenia data box edge. Azure Data Box Edge to rozwiązanie magazynu umożliwiające przetwarzanie danych i wysyłanie ich za pośrednictwem sieci na platformę Azure.

Za pomocą modułów usługi Azure IoT Edge z usługą Data Box Edge można przekształcać dane podczas przenoszenia danych na platformę Azure. Moduł używany w tym artykule implementuje logikę kopiowania pliku z udziału lokalnego do udziału w chmurze na urządzeniu usługi Data Box Edge.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz rejestr kontenerów do przechowywania modułów i zarządzania nimi (obrazy platformy Docker).
> * Utwórz moduł usługi IoT Edge do wdrożenia na urządzeniu data box edge.


## <a name="about-the-iot-edge-module"></a>Informacje o module IoT Edge

Urządzenie Data Box Edge może wdrażać i uruchamiać moduły usługi IoT Edge. Moduły usługi Edge są zasadniczo kontenery platformy Docker, które wykonują określone zadanie, takie jak pozyskiwania wiadomości z urządzenia, przekształcenia wiadomości lub wysłać wiadomość do Centrum IoT Hub. W tym artykule utworzysz moduł, który kopiuje pliki z udziału lokalnego do udziału w chmurze na urządzeniu data box edge.

1. Pliki są zapisywane w udziale lokalnym na urządzeniu Data Box Edge.
2. Generator zdarzeń pliku tworzy zdarzenie pliku dla każdego pliku zapisanego w udziale lokalnym. Zdarzenia pliku są również generowane, gdy plik jest modyfikowany. Zdarzenia pliku są następnie wysyłane do usługi IoT Edge Hub (w czasie wykonywania usługi IoT Edge).
3. Moduł niestandardowy usługi IoT Edge przetwarza zdarzenie pliku w celu utworzenia obiektu zdarzenia pliku, który zawiera również względną ścieżkę dla pliku. Moduł generuje ścieżkę bezwzględną przy użyciu ścieżki pliku względnego i kopiuje plik z udziału lokalnego do udziału w chmurze. Następnie moduł usuwa plik z udziału lokalnego.

![Jak działa moduł Usługi Azure IoT Edge w usłudze Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Gdy plik znajduje się w udziale w chmurze, automatycznie zostanie przekazany do konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Urządzenie Data Box Edge, które jest uruchomione.

    - Urządzenie ma również skojarzony zasób Usługi IoT Hub.
    - Urządzenie ma skonfigurowane rolę obliczeniową edge.
    Aby uzyskać więcej informacji, przejdź do [konfigurowania obliczeń](data-box-edge-deploy-configure-compute.md#configure-compute) dla krawędzi pola danych.

- Następujące zasoby programistyczne:

    - [Program Visual Studio Code](https://code.visualstudio.com/)
    - [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
    - [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Program Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Może być trzeba utworzyć konto, aby pobrać i zainstalować oprogramowanie.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Usługa Azure Container Registry to rejestr prywatny platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów Docker i zarządzać nimi. Dwie popularne usługi rejestru platformy Docker dostępne w chmurze to Azure Container Registry i Docker Hub. W tym artykule użyto rejestru kontenerów.

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. Wybierz **pozycję Utwórz > kontenerów > rejestru kontenerów**. Kliknij przycisk **Utwórz**.
3. Zapewnić:

   1. Unikatowa **nazwa rejestru** na platformie Azure zawierająca od 5 do 50 znaków alfanumerycznej.
   2. Wybierz **subskrypcję**.
   3. Utwórz nowy lub wybierz istniejącą **grupę zasobów**.
   4. Wybierz **lokalizację**. Zaleca się, aby ta lokalizacja była taka sama, jak ta jest skojarzona z zasobem Data Box Edge.
   5. Przełącz pozycję **Administrator** na wartość **Włącz**.
   6. Ustaw jednostkę SKU na **Podstawową**.

      ![Tworzenie rejestru kontenerów](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Wybierz **pozycję Utwórz**.
5. Po utworzeniu rejestru kontenerów przejdź do niego i wybierz pozycję **Klucze dostępu**.

    ![Uzyskiwanie kluczy dostępu](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości można użyć później, aby opublikować obraz platformy Docker w rejestrze i dodać poświadczenia rejestru do środowiska wykonawczego usługi Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge

Poniższe kroki tworzą projekt modułu usługi IoT Edge na podstawie sdk .NET Core 2.1. Projekt używa programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu.

1. W programie Visual Studio Code wybierz pozycję **Wyświetl > paletę poleceń,** aby otworzyć paletę poleceń programu VS Code.
2. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie:

    1. Wybierz folder, w którym chcesz utworzyć rozwiązanie.
    2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
    
        ![Tworzenie nowego rozwiązania 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Wybierz **moduł języka C#** jako szablon modułu.
    4. Zastąp domyślną nazwę modułu nazwą, którą chcesz przypisać, w tym przypadku jest to **FileCopyModule**.
    
        ![Tworzenie nowego rozwiązania 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Określ rejestr kontenerów utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania.

        Ostatni ciąg wygląda `<Login server name>/<Module name>`jak . W tym przykładzie ciąg `mycontreg2.azurecr.io/filecopymodule`to: .

        ![Tworzenie nowego rozwiązania 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Przejdź do **folderu > Otwórz folder**.

    ![Tworzenie nowego rozwiązania 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Przeglądanie i wskazywowynie folderu **EdgeSolution** utworzonego wcześniej. Okno Kod programu VS ładuje obszar roboczy rozwiązania usługi IoT Edge za pomocą pięciu składników najwyższego poziomu. Folder **vscode,** plik **gitignore,** plik **.env** i plik **deployment.template.json** nie będą edytowane w tym artykule.
    
    Jedynym składnikiem, który można zmodyfikować jest folder modułów. Ten folder ma kod C# dla modułu i pliki platformy Docker do tworzenia modułu jako obrazu kontenera.

    ![Tworzenie nowego rozwiązania 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze kodu VS otwórz **moduły > > Program.cs.**
2. W górnej części **obszaru nazw FileCopyModule**dodaj następujące instrukcje przy użyciu instrukcji dla typów, które są używane później. **Microsoft.Azure.Devices.Client.Transport.Mqtt** to protokół do wysyłania wiadomości do centrum usługi IoT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Dodaj **zmienną InputFolderPath** i **OutputFolderPath** do klasy Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Natychmiast po poprzednim kroku dodaj **FileEvent** klasy, aby zdefiniować treść wiadomości.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. W **Init metody**, kod tworzy i konfiguruje **ModuleClient** obiektu. Ten obiekt umożliwia modułowi łączenie się z lokalnym czasem wykonywania usługi Azure IoT Edge przy użyciu protokołu MQTT do wysyłania i odbierania wiadomości. Parametry połączenia używane w metodzie Init są dostarczane do modułu za pośrednictwem środowiska uruchomieniowego usługi IoT Edge. Kod rejestruje wywołania zwrotnego FileCopy do odbierania wiadomości z usługi IoT Edge hub za pośrednictwem punktu końcowego **input1.** Zastąp **Init metody** z następującym kodem.

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

6. Usuń kod **pipemessage metody** i w jego miejsce, wstawić kod **FileCopy**.

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
8. Można również [pobrać istniejący przykład kodu](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) dla tego projektu. Następnie można sprawdzić poprawność pliku zapisanego w pliku **program.cs** w tym przykładzie.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do FileCopyModule do kopiowania plików z udziału lokalnego do udziału w chmurze. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. W programie VSCode przejdź do terminalu > nowego terminalu, aby otworzyć nowy zintegrowany terminal programu Visual Studio Code.
2. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu.

    `docker login <ACR login server> -u <ACR username>`

    Użyj serwera logowania i nazwy użytkownika skopiowanej z rejestru kontenerów.

    ![Tworzenie i wypychanie rozwiązania IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po wyświetleniu monitu o podanie hasła podaj hasło. Można również pobrać wartości serwera logowania, nazwy użytkownika i hasła z **kluczy dostępu** w rejestrze kontenerów w witrynie Azure portal.
 
3. Po podaniu poświadczeń można wypchnąć obraz modułu do rejestru kontenerów platformy Azure. W Eksploratorze kodu VS kliknij prawym przyciskiem myszy plik **module.json** i wybierz polecenie **Skompiluj i wypychaj rozwiązanie IoT Edge**.

    ![Tworzenie i wypychanie rozwiązania IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Po poinformowaniu programu Visual Studio Code o utworzeniu rozwiązania uruchamia dwa polecenia w zintegrowanym terminalu: kompilacja platformy docker i wypychanie docker. Te dwa polecenia kompilują kod, konteneryzują plik CSharpModule.dll i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania.

    Zostaniesz poproszony o wybranie platformy modułu. Wybierz *amd64* odpowiadające Linuksowi.

    ![Wybierz platformę](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Obsługiwane są tylko moduły Linuksa.

    Może zostać wyświetlone następujące ostrzeżenie, które można zignorować:

    *Program.cs(77,44): ostrzeżenie CS1998: Ta metoda asynchroniza nie ma operatorów "await" i będzie działać synchronicznie. Należy rozważyć użycie operatora "await", aby oczekiwać na nieblokujące wywołania interfejsu API lub "await Task.Run(...)", aby wykonać pracę związaną z procesorem CPU w wątku w tle.*

4. Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu jest zbudowany na podstawie informacji zawartych w pliku `<repository>:<version>-<platform>`module.json w formacie . W tym artykule powinien `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`wyglądać .

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć i uruchomić ten moduł na stronie Data Box Edge, zobacz kroki opisane w oknie [Dodawanie modułu](data-box-edge-deploy-configure-compute.md#add-a-module).
