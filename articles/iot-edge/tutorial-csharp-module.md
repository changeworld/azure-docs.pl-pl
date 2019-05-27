---
title: Samouczek dotyczący tworzenia C# modułu dla systemu Linux — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć moduł usługi IoT Edge przy użyciu C# kodu, a następnie wdrożyć go na urządzeniu usługi IoT Edge w systemie Linux.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b9ced0b08ed26057a45959f759fb90cbd7efe2a5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239789"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Samouczek: Twórz C# moduł usługi IoT Edge dla urządzeń z systemem Linux

Korzystanie z programu Visual Studio Code w celu projektowania C# kodu, a następnie wdrożyć go urządzeniem z systemem Linux z usługą Azure IoT Edge. 

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows](quickstart.md) lub [Linux](quickstart-linux.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge opartego na zestawie .NET Core SDK 2.1 przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

Ten samouczek przedstawia sposób tworzenia modułu w **C#** przy użyciu **programu Visual Studio Code**oraz jak wdrożyć ją **urządzenia z systemem Linux**. Jeśli tworzysz modułów dla urządzeń Windows przejdź do strony [programowanie C# moduł usługi IoT Edge dla urządzeń Windows](tutorial-csharp-module-windows.md) zamiast tego.

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów języka C z systemem Linux: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **AMD64 systemu Linux** | ![C#Moduły LinuxAMD64 w programie VS Code](./media/tutorial-c-module/green-check.png) | ![C#Moduły LinuxAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| **ARM32 systemu Linux** | ![C#Moduły LinuxARM32 w programie VS Code](./media/tutorial-c-module/green-check.png) | ![C#Moduły LinuxARM64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano już instrukcje poprzednim samouczku do skonfigurowania środowiska deweloperskiego [opracowywanie moduł usługi IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Po ukończeniu tego samouczka musisz już mieć następujące wymagania wstępne: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [Linux urządzenia z usługą Azure IoT Edge](quickstart-linux.md).
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowano [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów systemu Linux.

Aby wykonać te samouczki, należy przygotować następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Utwórz projekt modułu

Poniższe kroki umożliwiają utworzenie projektu modułu usługi IoT Edge dla C# przy użyciu programu Visual Studio Code i rozszerzenia Azure IoT Tools. Po utworzeniu szablonu projektu utworzone, dodawanie nowego kodu, aby moduł odfiltrowuje komunikaty na podstawie ich zgłoszonych właściwości. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu. 

1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 

2. W palecie poleceń wprowadź i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)** , a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Nazwij moduł **CSharpModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/csharpmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z **klucze dostępu** sekcji usługi Azure container registry. 

1. W eksploratorze programu VS Code otwórz plik **env**. 
2. Zaktualizuj pola w taki sposób, przy użyciu **username** i **hasło** wartości z usługi Azure container registry. 
3. Zapisz ten plik. 

### <a name="select-your-target-architecture"></a>Wybierz swoje Architektura docelowa

Obecnie usługa Visual Studio Code można tworzyć moduły języka C dla urządzeń z AMD64 systemu Linux i ARM32v7 systemu Linux. Musisz wybrać architektury objęci za pomocą każdego rozwiązania, ponieważ skompilowane i uruchom w różny sposób dla każdego typu architektury kontenera. Wartość domyślna to AMD64 systemu Linux. 

1. Otwórz paletę poleceń i wyszukaj **usługi Azure IoT Edge: Ustaw domyślne platformę docelową dla nowoczesne rozwiązanie**, lub wybierz ikonę skrótu prowadzącą w pasku bocznym, w dolnej części okna. 

2. W palecie poleceń Wybierz architektury docelowej z listy opcji. W tym samouczku używamy maszynę wirtualną Ubuntu jako urządzenie usługi IoT Edge, dzięki czemu będzie Zachowaj ustawienie domyślne **amd64**. 

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

5. Znajdź **Init** funkcji. Ta funkcja tworzy i konfiguruje **ModuleClient** obiektu, który umożliwia modułu nawiązać połączenie lokalne środowisko uruchomieniowe usługi Azure IoT Edge, wysyłanie i odbieranie wiadomości. Po utworzeniu elementu **ModuleClient** kod odczytuje wartość **temperatureThreshold** z żądanych właściwości bliźniaczej reprezentacji modułu. Ten kod rejestruje wywołanie zwrotne w celu odbierania komunikatów z centrum usługi IoT Edge za pośrednictwem punktu końcowego **input1**. Zastąp metodę **SetInputMessageHandlerAsync** nową metodą i dodaj metodę **SetDesiredPropertyUpdateCallbackAsync** dla aktualizacji do żądanych właściwości. Aby dokonać tej zmiany, zastąp ostatni wiersz metody **Init** następującym kodem:

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

9. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. 

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


## <a name="build-and-push-your-module"></a>Zbuduj i Wypchnij modułu

W poprzedniej sekcji utworzyliśmy rozwiązanie IoT Edge i dodać kod do CSharpModule, która będzie filtrować wiadomości, gdzie temperatury zgłoszonych maszyny wykracza poza dopuszczalne limity. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)**  > **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasło i serwer logowania z usługi Azure container registry. Możesz pobrać te wartości z **klucze dostępu** sekcji rejestru w witrynie Azure portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Do wdrożenia projektu modułu do urządzenia usługi IoT Edge przy użyciu Eksploratora Visual Studio Code i rozszerzenia narzędzia IoT platformy Azure. Masz już manifest wdrożenia, jest przygotowane do scenariusza **deployment.json** pliku w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona.

1. W Eksploratorze programu Visual Studio Code rozwiń **Azure IoT Hub Devices** sekcję, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**. 

5. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json. 

6. Kliknij przycisk Odśwież. Nowy moduł **CSharpModule** powinien być teraz uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.  

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze programu Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**.

2. Wyświetl wiadomości otrzymywanych z usługi IoT Hub. Może potrwać trochę wiadomości zostanie dostarczona, ponieważ urządzenia usługi IoT Edge do odbierania jego nowego wdrożenia i uruchamiania wszystkich modułów. Następnie zmiany wprowadzone do kodu CModule poczekaj, aż temperatury maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Typ komunikatu dodaje także **alertu** na wszystkie komunikaty, w których progu temperatury. 

   ![Wyświetlanie wiadomości otrzymywanych z usługi IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Edytuj bliźniaczą reprezentację modułu

Użyliśmy CSharpModule bliźniaczą reprezentację modułu w pliku manifestu wdrożenia, aby ustawić próg temperaturę w stopniach 25. Bliźniacza reprezentacja modułu można użyć w celu zmiany funkcji bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia usługi IoT Edge, aby wyświetlić uruchomione moduły. 

2. Kliknij prawym przyciskiem myszy **CSharpModule** i wybierz **bliźniaczą reprezentację modułu edycji**. 

3. Znajdź **TemperatureThreshold** w odpowiednich właściwości. Zmień jej wartość temperatury nowe 5 stopni do 10 stopni wyższa niż najnowszy zgłoszonych temperatury. 

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w bliźniaczej reprezentacji modułu do edycji okienko, a następnie wybierz **bliźniaczą reprezentację modułu aktualizacji**. 

5. Monitorowanie przychodzących komunikatów przesyłanych z chmury do urządzenia. Powinien zostać wyświetlony wiadomości, Zatrzymaj, aż do osiągnięcia progu temperatury nowe. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie można usunąć lokalnej konfiguracji i zasobów platformy Azure używane w tym artykule Aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułach, możesz dowiedzieć się więcej [tworzenia własnych modułów usługi IoT Edge](module-development.md) lub jak [opracowanie modułów przy użyciu programu Visual Studio Code](how-to-vs-code-develop-module.md). Można przejść do następnego samouczki, aby dowiedzieć się, jak usługa Azure IoT Edge mogą pomóc Ci wdrażanie usług w chmurze platformy Azure do przetwarzania i analizowania danych na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [uczenia maszynowego](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
