---
title: Samouczek dotyczący tworzenia niestandardowego modułu C# — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka C# i wdrożyć go na urządzeniu brzegowym.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f4afd83e31cf724e734b4e86045f8404e65753c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612390"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Samouczek: Opracowywanie modułu usługi IoT Edge w języku C# i wdrażanie go na urządzeniu symulowanym

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows](quickstart.md) lub [Linux](quickstart-linux.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge opartego na zestawie .NET Core SDK 2.1 przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

>[!NOTE]
>Można również użyć [programu Visual Studio 2017 do tworzenia, debugowania i wdrażania modułów usługi IoT Edge](how-to-visual-studio-develop-csharp-module.md).

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla programu Visual Studio Code. 
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Program Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym samouczku narzędzia usługi Azure IoT dla programu Visual Studio Code zostaną użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Do przechowywania obrazów kontenerów możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

Jeśli nie masz jeszcze rejestru kontenerów, wykonaj następujące kroki, aby utworzyć nowy rejestr na platformie Azure:

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

2. Podaj następujące wartości, aby utworzyć rejestr kontenerów:

   | Pole | Wartość | 
   | ----- | ----- |
   | Nazwa rejestru | Podaj unikatową nazwę. |
   | Subskrypcja | Wybierz subskrypcję z listy rozwijanej. |
   | Grupa zasobów | Zalecamy używanie tej samej grupy zasobów dla wszystkich zasobów testowych tworzonych podczas pracy z przewodnikami Szybki Start i samouczkami usługi IoT Edge. Na przykład **IoTEdgeResources**. |
   | Lokalizacja | Wybierz bliską lokalizację. |
   | Administrator | Ustaw na wartość **Włącz**. |
   | SKU | Wybierz pozycję **Podstawowa**. | 

5. Wybierz pozycję **Utwórz**.

6. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 

7. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Użyjesz tych wartości w dalszej części samouczka, aby zapewnić dostęp do rejestru kontenerów. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W poniższych krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge opartego na zestawie .NET Core 2.0 SDK przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

2. W palecie poleceń wprowadź i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Nazwij moduł **CSharpModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/csharpmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-csharp-module/repository.png)

W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Obszar roboczy rozwiązania zawiera pięć składników najwyższego poziomu. Folder **modules** zawiera kod w języku C# dla modułu, a także pliki Dockerfile na potrzeby tworzenia modułu jako obrazu kontenera. W pliku **\.env** są przechowywane poświadczenia rejestru kontenerów. Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu. Natomiast plik **deployment.debug.template.json** zawiera wersję modułów służącą do debugowania. Folder **\.vscode** i plik **\.gitignore** nie będą edytowane w tym samouczku.

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env. 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. 

1. W eksploratorze programu VS Code otwórz plik env. 
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry. 
3. Zapisz ten plik. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze programu VS Code otwórz plik **modules** > **CSharpModule** > **Program.cs**.

2. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Dodaj zmienną **temperatureThreshold** do klasy **Program**. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do centrum IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Dodaj klasy **MessageBody**, **Machine**, i **Ambient** do klasy **Program**. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

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

5. W metodzie **Init** kod tworzy i konfiguruje obiekt **ModuleClient**. Dzięki temu obiektowi moduł może nawiązać połączenie z lokalnym środowiskiem uruchomieniowym usługi Azure IoT Edge na potrzeby wysyłania i odbierania komunikatów. Parametry połączenia używane w metodzie **Init** są dostarczane do modułu za pośrednictwem środowiska uruchomieniowego usługi IoT Edge. Po utworzeniu elementu **ModuleClient** kod odczytuje wartość **temperatureThreshold** z żądanych właściwości bliźniaczej reprezentacji modułu. Ten kod rejestruje wywołanie zwrotne w celu odbierania komunikatów z centrum usługi IoT Edge za pośrednictwem punktu końcowego **input1**. Zastąp metodę **SetInputMessageHandlerAsync** nową metodą i dodaj metodę **SetDesiredPropertyUpdateCallbackAsync** dla aktualizacji do żądanych właściwości. Aby dokonać tej zmiany, zastąp ostatni wiersz metody **Init** następującym kodem:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Dodaj metodę **onDesiredPropertiesUpdate** do klasy **Program**. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

7. Zastąp metodę **PipeMessage** metodą **FilterMessages**. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Zapisz plik Program.cs.

9. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik informuje agenta usługi IoT Edge, które moduły mają zostać wdrożone (w tym przypadku **tempSensor** i **CSharpModule**), i informuje centrum usługi IoT Edge o tym, jak kierować wiadomości między nimi. Rozszerzenie programu Visual Studio Code automatycznie wypełni większość informacji potrzebnych w szablonie wdrożenia, ale sprawdź, czy wszystko jest dokładne dla Twojego rozwiązania: 

   1. Domyślną platformą ustawioną na pasku stanu programu VS Code dla usługi IoT Edge jest platforma **amd64**, co oznacza, że dla modułu **CSharpModule** ustawiono wersję obrazu Linux amd64. Zmień domyślną platformę na pasku stanu z **amd64** na **arm32v7** lub **windows-amd64**, jeśli taka jest architektura urządzenia usługi IoT Edge. 

      ![Aktualizowanie platformy obrazu modułu](./media/tutorial-csharp-module/image-platform.png)

   2. Sprawdź, czy szablon ma prawidłową nazwę modułu, a nie domyślną nazwę **SampleModule** zmienioną podczas tworzenia rozwiązania usługi IoT Edge.

   3. W sekcji **registryCredentials** przechowywane są poświadczenia rejestru platformy Docker, aby agent usługi IoT Edge mógł ściągnąć obraz modułu. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany przez usługę git. Jeśli jeszcze nie zostało to zrobione, dodaj swoje poświadczenia do pliku env.  

   4. Jeśli chcesz dowiedzieć się więcej na temat manifestów wdrożenia, zobacz artykuł [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md) (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge).

10. Dodaj bliźniaczą reprezentację modułu **CSharpModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **modulesContent** po bliźniaczej reprezentacji modułu **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-csharp-module/module-twin.png)

11. Zapisz plik deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code. Następnie możesz wypchnąć obraz modułu do rejestru kontenerów platformy Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z rejestru kontenerów platformy Azure w pierwszej sekcji. Możesz także pobrać te wartości z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują plik CSharpModule.dll i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania. 

Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku module.json w formacie \<repozytorium\>:\<wersja\>-\<platforma\>. W tym samouczku powinien wyglądać następująco: nazwa_rejestru.azurecr.io/csharpmodule:0.0.1-amd64.

>[!TIP]
>Jeśli podczas próby skompilowania i wypchnięcia modułu pojawia się błąd, sprawdź następujące rzeczy:
>* Czy do platformy Docker w programie Visual Studio Code zalogowano się przy użyciu poświadczeń z rejestru kontenerów? Te poświadczenia są inne niż te, których używasz do logowania się w witrynie Azure Portal.
>* Czy używasz właściwego repozytorium kontenerów? Otwórz plik **modules** > **cmodule** > **module.json** i znajdź pole **repository**. Repozytorium obrazów powinno wyglądać tak: **\<nazwa_rejestru\>.azurecr.io/csharpmodule**. 
>* Czy kompilujesz kontenery tego samego typu, co działające na maszynie deweloperskiej? Domyślnie w programie Visual Studio Code są używane kontenery amd64 systemu Linux. Jeśli na maszynie deweloperskiej działają kontenery systemu Windows lub kontenery arm32v7 systemu Linux, zaktualizuj platformę na niebieskim pasku stanu w dolnej części okna programu VS Code, aby była zgodna z platformą kontenerów.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Moduły można także wdrażać przy użyciu rozszerzenia Azure IoT Hub Toolkit (dawniej rozszerzenia Azure IoT Toolkit) dla programu Visual Studio Code. Masz już manifest wdrożenia przygotowany dla danego scenariusza — plik **deployment.json**. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

1. W palecie poleceń programu VS Code uruchom polecenie **Azure IoT Hub: Select IoT Hub**  (Azure IoT Hub: wybierz centrum IoT Hub). 

2. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować. 

3. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

4. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**. 

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-csharp-module/create-deployment.png)

5. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json. 

6. Kliknij przycisk Odśwież. Nowy moduł **CSharpModule** powinien być teraz uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.  

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione. 

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów. 

Możesz również wyświetlić stan modułów wdrożenia na samym urządzeniu usługi IoT Edge, używając polecenia `iotedge list`. Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, moduł tempSensor i moduł niestandardowy utworzony w ramach tego samouczka. Uruchomienie wszystkich modułów może potrwać kilka minut, dlatego jeśli na początku wszystkie nie będą widoczne, uruchom polecenie ponownie. 

Aby wyświetlić komunikaty generowane przez któryś moduł, użyj polecenia `iotedge logs <module name>`. 

Komunikaty odbierane w centrum IoT można wyświetlać za pomocą programu Visual Studio Code. 

1. Aby monitorować dane, które docierają do centrum IoT Hub, wybierz przycisk wielokropka (**...**), a następnie wybierz pozycję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C) w palecie poleceń. 
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz jej plik JSON, kliknij prawym przyciskiem myszy obszar edytora i wybierz pozycję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, zainstaluj rozszerzenie [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla narzędzia VS Code. Uruchomione moduły można znaleźć lokalnie w eksploratorze platformy Docker. W menu kontekstowym wybierz pozycję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu.
 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [utworzyć moduł w języku C# za pomocą rozszerzenia usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-csharp-module.md). Możesz teraz kontynuować pracę, korzystając z kolejnych samouczków, aby poznać inne metody, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Store data at the edge with SQL Server databases (Przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server)](tutorial-store-data-sql-server.md)
