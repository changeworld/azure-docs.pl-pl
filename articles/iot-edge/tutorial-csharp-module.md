---
title: Samouczek — opracowanie modułu C# dla systemu Linux przy użyciu usługi Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge z kodem C# i wdrożyć go na urządzeniu z aplikacją Linux IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 53e1863f6f3421a6d8df9112f463f16443cff93e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943046"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Samouczek: Opracowanie modułu C# IoT Edge dla urządzeń z systemem Linux

Użyj programu Visual Studio Code, aby opracować kod języka C# i wdrożyć go na urządzeniu z systemem Linux z uruchomiona usługą Azure IoT Edge.

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Użyjesz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows](quickstart.md) lub [Linux](quickstart-linux.md). Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie modułu usługi IoT Edge opartego na zestawie .NET Core SDK 2.1 przy użyciu programu Visual Studio Code.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze przy użyciu programu Visual Studio Code i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku pokazano, jak opracować moduł w **języku C#** przy użyciu **programu Visual Studio Code** i wdrożyć go na urządzeniu z systemem **Linux.** Jeśli tworzysz moduły dla urządzeń z systemem Windows, przejdź do [opracowania modułu Usługi IoT Edge języka C# dla urządzeń z systemem Windows.](tutorial-csharp-module-windows.md)

Użyj poniższej tabeli, aby zrozumieć opcje tworzenia i wdrażania modułów języka C# w systemie Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Moduły C# dla LinuxAMD64 w kodzie VS](./media/tutorial-c-module/green-check.png) | ![Moduły języka C# dla systemu LinuxAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Moduły C# dla LinuxARM32 w kodzie VS](./media/tutorial-c-module/green-check.png) | ![Moduły C# dla systemu LinuxARM64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Obsługa urządzeń Arm64 z systemem Linux jest dostępna w [publicznej wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aby uzyskać więcej informacji, zobacz [Tworzenie i debugowanie modułów usługi ARM64 IoT Edge w programie Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy przejść przez poprzedni samouczek, aby skonfigurować środowisko programistyczne, [Opracowanie modułu IoT Edge dla urządzenia z systemem Linux](tutorial-develop-for-linux.md). Po zakończeniu tego samouczka, już powinien mieć następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux z usługą Azure IoT Edge](quickstart-linux.md).
* Rejestr kontenerów, taki jak [usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany za pomocą [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowany do uruchamiania kontenerów systemu Linux.

Aby wykonać te samouczki, przygotuj następujące dodatkowe wymagania wstępne na komputerze deweloperskim:

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt modułu usługi IoT Edge dla języka C# przy użyciu programu Visual Studio Code i rozszerzenia Narzędzia Azure IoT. Po utworzeniu szablonu projektu dodaj nowy kod, aby moduł odfiltrowywany wiadomości na podstawie ich zgłoszonych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania w języku C#, który można dostosować przy użyciu własnego kodu.

1. W programie Visual Studio Code wybierz pozycję **Wyświetl** > **paletę poleceń,** aby otworzyć paletę poleceń programu VS Code.

2. W palecie poleceń wprowadź i uruchom polecenie **Azure: zaloguj się**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Nazwij moduł **CSharpModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br>Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/csharpmodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **Klucze programu Access** rejestru kontenerów platformy Azure.

1. W eksploratorze programu VS Code otwórz plik **env**.
2. Zaktualizuj pola za pomocą wartości **nazwy użytkownika** i **hasła** z rejestru kontenerów platformy Azure.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybierz architekturę docelową

Obecnie Visual Studio Code może tworzyć moduły C# dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać architekturę, która jest przeznaczona dla każdego rozwiązania, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Domyślnie jest Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj **usługę Azure IoT Edge: Set Default Target Platform for Edge Solution**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, więc zachowamy domyślny **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

1. W eksploratorze kodu VS otwórz **moduły** > **CSharpModule** > **Program.cs**.

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

5. Znajdź funkcję **Init.** Ta funkcja tworzy i konfiguruje **ModuleClient** obiektu, który umożliwia modułowi połączyć się z lokalnym środowisko uruchomieniowe usługi Azure IoT Edge do wysyłania i odbierania wiadomości. Po utworzeniu elementu **ModuleClient** kod odczytuje wartość **temperatureThreshold** z żądanych właściwości bliźniaczej reprezentacji modułu. Ten kod rejestruje wywołanie zwrotne w celu odbierania komunikatów z centrum usługi IoT Edge za pośrednictwem punktu końcowego **input1**. Zastąp metodę **SetInputMessageHandlerAsync** nową metodą i dodaj metodę **SetDesiredPropertyUpdateCallbackAsync** dla aktualizacji do żądanych właściwości. Aby dokonać tej zmiany, zastąp ostatni wiersz metody **Init** następującym kodem:

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
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
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

## <a name="build-and-push-your-module"></a>Zbuduj i wypchnij swój moduł

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do CSharpModule. Nowy kod odfiltruje komunikaty, w których podana temperatura komputera mieści się w dopuszczalnych granicach. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz terminal zintegrowany vs code, wybierając **view** > **terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z sekcji **Klucze dostępu** rejestru w witrynie Azure portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Użyj Eksploratora kodu programu Visual Studio i rozszerzenia Narzędzia Azure IoT, aby wdrożyć projekt modułu na urządzeniu usługi IoT Edge. Masz już manifest wdrożenia przygotowany dla twojego scenariusza, plik **deployment.json** w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. W Eksploratorze kodu programu Visual Studio rozwiń sekcję **Urządzenia usługi Azure IoT Hub,** aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy **moduł CSharpModule** uruchomiony wraz z **modułem SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.  

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze kodu programu Visual Studio kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetlanie wiadomości docierających do Centrum IoT. Może upłynąć trochę czasu, aby komunikaty dotarły, ponieważ urządzenie usługi IoT Edge musi otrzymać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule czekać, aż temperatura maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Dodaje również typ komunikatu **Alert** do wszystkich komunikatów, które osiągają ten próg temperatury.

   ![Wyświetlanie wiadomości docierających do Usługi IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edycja bliźniaczej reprezentacji modułu

Użyliśmy cSharpModule moduł bliźniaczej reprezentacji w manifeście wdrażania, aby ustawić próg temperatury na 25 stopni. Bliźniaczej reprezentacji modułu można zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia IoT Edge, aby wyświetlić uruchomione moduły.

2. Kliknij prawym przyciskiem myszy **polecenie CSharpModule** i wybierz polecenie **Edytuj bliźniaczą modułu**.

3. Znajdź **TemperatureThreshold** w żądanych właściwościach. Zmień jego wartość na nową temperaturę o 5 stopni do 10 stopni wyższą niż ostatnio zgłoszona temperatura.

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy dowolne miejsce w okienku edycji bliźniaczej reprezentacji modułu i wybierz opcję **Aktualizuj bliźniaczej reprezentacji modułu**.

6. Monitorowanie przychodzących komunikatów z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie można usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy będziesz gotowy do tworzenia własnych modułów, możesz dowiedzieć się więcej o [tworzeniu własnych modułów IoT Edge](module-development.md) lub o tym, jak tworzyć [moduły za pomocą programu Visual Studio Code.](how-to-vs-code-develop-module.md) Przykłady modułów IoT Edge, w tym symulowanego modułu temperatury, można znaleźć w [przykładach modułów IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Możesz przejść do następnych samouczków, aby dowiedzieć się, jak usługa Azure IoT Edge może pomóc we wdrażaniu usług w chmurze platformy Azure w celu przetwarzania i analizowania danych na serwerze brzegowym.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Strumienia Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
