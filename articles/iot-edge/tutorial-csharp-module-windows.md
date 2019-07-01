---
title: Samouczek dotyczący tworzenia C# modułu dla Windows - usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak utworzyć moduł usługi IoT Edge przy użyciu C# kodu, a następnie wdrożyć go na urządzeniu Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d7ccce1f21b1caa2268317b7239617a80ddce10b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485927"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Samouczek: Twórz C# moduł usługi IoT Edge dla urządzeń Windows

Programowanie przy użyciu programu Visual Studio C# kodu, a następnie wdrożyć go na urządzeniu Windows z usługą Azure IoT Edge. 

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Utwórz moduł usługi IoT Edge, która jest oparta na przy użyciu programu Visual Studio C# zestawu SDK.
> * Aby utworzyć obraz platformy Docker i opublikuj go do rejestru, należy użyć programu Visual Studio i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

Ten samouczek przedstawia sposób tworzenia modułu w **C#** przy użyciu **Visual Studio 2019**oraz jak wdrożyć ją **urządzenia Windows**. Jeśli tworzysz modułów dla urządzeń z systemem Linux, przejdź do strony [programowanie C# moduł usługi IoT Edge dla urządzeń z systemem Linux](tutorial-csharp-module.md) zamiast tego. 

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów C na urządzeniach Windows: 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Twórz Windows AMD64** | ![Twórz C# modułów na potrzeby WinAMD64 w programie VS Code](./media/tutorial-c-module/green-check.png) | ![Twórz C# modułów na potrzeby WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 debugowania** |   | ![Debugowanie C# modułów na potrzeby WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano już instrukcje poprzednim samouczku do skonfigurowania środowiska deweloperskiego [opracowywanie moduł usługi IoT Edge dla urządzeń Windows](tutorial-develop-for-windows.md). Po ukończeniu tego samouczka musisz już mieć następujące wymagania wstępne: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [urządzenia Windows z usługą Azure IoT Edge](quickstart.md).
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) skonfigurowano [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) rozszerzenia.
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów Windows.

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (w wersji 15.7 lub nowszej) plrease Pobierz i zainstaluj [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu VS 2017 z witryny marketplace programu Visual Studio

## <a name="create-a-module-project"></a>Utwórz projekt modułu

Poniższe kroki umożliwiają utworzenie projektu modułu usługi IoT Edge przy użyciu programu Visual Studio i rozszerzenia usługi Azure IoT Edge narzędzia. Po utworzeniu szablonu projektu utworzone, dodawanie nowego kodu, aby moduł odfiltrowuje komunikaty na podstawie ich zgłoszonych właściwości. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Narzędzia usługi Azure IoT Edge zapewnia szablony projektu dla wszystkich obsługiwanych IoT Edge, moduł języków w programie Visual Studio. Te szablony mają wszystkie pliki i kod, który należy zainstalować moduł pracy do testowania usługi IoT Edge lub zapewniają punkt wyjścia, aby dostosować szablon z własną logiką biznesową. 

1. Uruchom program Visual Studio 2019 r, a następnie wybierz pozycję **Utwórz nowy projekt**.

2. W nowym oknie projektu, wyszukiwanie **usługi IoT Edge** projektu, a następnie wybierz **usługi Azure IoT Edge (Windows amd64)** projektu. Kliknij przycisk **Dalej**. 

   ![Utwórz nowy projekt usługi Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. W konfiguracji okno nowego projektu, Zmień nazwę projektu i rozwiązania do opisu coś podobnego **CSharpTutorialApp**. Kliknij przycisk **Utwórz** do tworzenia projektu. 

   ![Skonfiguruj nowy projekt usługi Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. W oknie aplikacji IoT Edge i moduł należy skonfigurować projekt z następującymi wartościami: 

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz szablon | Wybierz  **C# modułu**. | 
   | Nazwa projektu modułu | Nazwij moduł **CSharpModule**. | 
   | Repozytorium obrazów platformy docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wypełniana wstępnie z wartości Nazwa projektu modułu. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/csharpmodule. |

   ![Konfigurowanie projektu dla urządzeń docelowych, typ modułu i container registry](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Wybierz **OK** Aby zastosować zmiany. 

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrażania udziałów poświadczenia dla rejestru kontenerów za pomocą środowiska uruchomieniowego usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z **klucze dostępu** sekcji usługi Azure container registry. 

1. W Eksploratorze rozwiązań programu Visual Studio Otwórz **deployment.template.json** pliku. 

2. Znajdź **registryCredentials** właściwość $edgeAgent żądane właściwości. 

3. Aktualizacja właściwości przy użyciu swoich poświadczeń, zgodnie z następującym formacie: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Zapisz plik deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Domyślny kod modułu odbiera komunikaty w kolejce wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Dodajmy trochę kodu dodatkowe, tak aby moduł przetwarza wiadomości na urządzeniach brzegowych przed przekazaniem ich do usługi IoT Hub. Zaktualizuj moduł analizuje dane dotyczące temperatury w każdej wiadomości, a tylko wysyła komunikat do Centrum IoT Hub, gdy temperatura przekroczy określony próg. 

1. W programie Visual Studio, otwórz **CSharpModule** > **Program.cs**.

2. Na początku przestrzeni nazw **CSharpModule** dodaj trzy instrukcje **using** dla typów, które będą używane później:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Dodaj **temperatureThreshold** zmienną **Program** klasy po zmiennej licznika. Zmienna temperatureThreshold ustawia wartość, która mierzonego temperatura przekraczać danych do wysłania do Centrum IoT hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Dodaj **Element MessageBody**, **maszyny**, i **przestrzenny** klasy do **Program** klasy po deklaracji zmiennych. Te klasy definiują oczekiwany schemat treści komunikatów przychodzących.

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

5. Znajdź **Init** metody. Ta metoda tworzy i konfiguruje **ModuleClient** obiektu, który umożliwia modułu nawiązać połączenie lokalne środowisko uruchomieniowe usługi Azure IoT Edge, wysyłanie i odbieranie wiadomości. Ten kod rejestruje także wywołanie zwrotne w celu odbierania komunikatów z Centrum IoT Edge za pośrednictwem **wejście1** punktu końcowego.

   Zastąp cały metody Init następującym kodem:
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   Ten zaktualizowany metody Init nadal konfiguruje połączenia do środowiska uruchomieniowego usługi IoT Edge przy użyciu ModuleClient, ale również dodaje nowe funkcje. Odczytuje żądane właściwości bliźniaczego można pobrać moduł **temperatureThreshold** wartości. Następnie tworzy wywołanie zwrotne, które wykrywa wszystkie przyszłe aktualizacje żądane właściwości bliźniaczego modułu. Z tym wywołaniu zwrotnym próg temperatury w bliźniaczej reprezentacji modułu można aktualizować zdalnie, a zmiany zostaną uwzględnione w module. 

   Zaktualizowana Metoda Init zmienia się również istniejące **SetInputMessageHandlerAsync** metody. W przykładowym kodzie komunikaty przychodzące *wejście1* są przetwarzane *PipeMessage* funkcji, ale chcesz zmienić, aby użyć *FilterMessages* , funkcja utworzymy w poniższych krokach. 

6. Dodaj nową **onDesiredPropertiesUpdate** metody **Program** klasy. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

7. Usuwanie przykładu **PipeMessage** metodę i zastąp go nowego **FilterMessages** metody. Ta metoda jest wywoływana za każdym razem, gdy moduł odbiera komunikat z centrum usługi IoT Edge. Odfiltrowuje ona komunikaty zgłaszające temperatury nie przekraczające wartości progowej temperatury ustawionej za pomocą bliźniaczej reprezentacji modułu. Dodaje ona również właściwość **MessageType** do komunikatu z wartością ustawioną na **Alert**. 

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

9. Otwórz **deployment.template.json** plików w rozwiązaniu IoT Edge. Ten plik informuje agenta usługi IoT Edge, które moduły mają zostać wdrożone (w tym przypadku **tempSensor** i **CSharpModule**), i informuje centrum usługi IoT Edge o tym, jak kierować wiadomości między nimi.

10. Dodaj bliźniaczą reprezentację modułu **CSharpModule** do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji **modulesContent** po bliźniaczej reprezentacji modułu **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dodawanie bliźniaczej reprezentacji modułu do szablonu wdrożenia](./media/tutorial-csharp-module-windows/module-twin.png)

11. Zapisz plik deployment.template.json.


## <a name="build-and-push-your-module"></a>Zbuduj i Wypchnij modułu

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpModule**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Użyj następującego polecenia, aby zalogować się do platformy Docker na komputerze deweloperskim. Użyj nazwy użytkownika, hasło i serwer logowania z usługi Azure container registry. Możesz pobrać te wartości z **klucze dostępu** sekcji rejestru w witrynie Azure portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

2. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu, który chcesz skompilować. Nazwa domyślna to **AzureIotEdgeApp1** a ponieważ tworzysz moduł Windows powinno być rozszerzenie **Windows.Amd64**. 

3. Wybierz **Zbuduj i Wypchnij moduły usługi IoT Edge**. 

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów. 

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Przy użyciu Eksploratora chmury programu Visual Studio i rozszerzenia usługi Azure IoT Edge narzędzia wdrożyć projekt modułu urządzenia usługi IoT Edge. Masz już manifest wdrożenia, jest przygotowane do scenariusza **deployment.json** pliku w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona. 

1. W programie Visual Studio cloud explorer rozwiń zasobami, aby zobaczyć Twoją listę urządzeń IoT. 

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, które chcesz otrzymywać wdrożenia. 

3. Wybierz **Utwórz wdrożenie**.

4. W Eksploratorze plików wybierz **deployment.windows amd64** pliku w folderze konfiguracji rozwiązania. 

5. Odśwież Eksplorator chmury, aby zobaczyć wdrożone moduły na liście w obszarze urządzenia. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione. 

Rozszerzenie IoT Edge narzędzia służy do wyświetlania wiadomości przychodzące w Twoim Centrum IoT Hub. 

1. W programie cloud explorer programu Visual Studio wybierz nazwę urządzenia usługi IoT Edge. 

2. W **akcje** listy wybierz **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**. 

3. Wyświetl wiadomości otrzymywanych z usługi IoT Hub. Może upłynąć trochę wiadomości zostanie dostarczona, ponieważ zmiany wprowadzone do kodu CSharpModule czekać do momentu temperatury maszyny osiągnięcia 25 stopni przed wysłaniem wiadomości. Typ komunikatu dodaje także **alertu** na wszystkie komunikaty, w których progu temperatury. 

   ![Wyświetlanie wiadomości otrzymywanych z usługi IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj bliźniaczą reprezentację modułu

Użyliśmy CSharpModule bliźniaczą reprezentację modułu, aby ustawić próg temperaturę w stopniach 25. Bliźniacza reprezentacja modułu można użyć w celu zmiany funkcji bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio, otwórz **deployment.windows amd64.json** pliku. (Nie plik deployment.template. Jeśli nie widzisz wdrażania, manifest w pliku konfiguracji w Eksploratorze rozwiązań, wybierz **Pokaż wszystkie pliki** ikonę na pasku narzędzi Eksploratora.)

2. Znajdź bliźniaczej reprezentacji CSharpModule i zmień wartość właściwości **temperatureThreshold** parametru, aby nowe temperatury 5 stopni do 10 stopni wyższa niż najnowszy zgłoszonych temperatury. 

3. Zapisz **deployment.windows amd64.json** pliku.

4. Wykonaj kroki wdrażania ponownie, aby stosowanie manifest wdrażania aktualizacji na urządzeniu. 

5. Monitorowanie przychodzących komunikatów przesyłanych z chmury do urządzenia. Powinien zostać wyświetlony wiadomości, Zatrzymaj, aż do osiągnięcia progu temperatury nowe. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie można usunąć lokalnej konfiguracji i zasobów platformy Azure używane w tym artykule Aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułach, możesz dowiedzieć się więcej [tworzenia własnych modułów usługi IoT Edge](module-development.md) lub jak [opracowanie modułów za pomocą programu Visual Studio](how-to-visual-studio-develop-module.md). Można przejść do następnego samouczki, aby dowiedzieć się, jak usługa Azure IoT Edge mogą pomóc Ci wdrażanie usług w chmurze platformy Azure do przetwarzania i analizowania danych na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [uczenia maszynowego](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
