---
title: 'Samouczek: wdrażanie funkcji platformy Azure na urządzeniu — Azure IoT Edge | Microsoft Docs'
description: W tym samouczku zaprojektujesz funkcję platformy Azure w postaci modułu usługi IoT Edge, a następnie wdrożysz ją na urządzeniu brzegowym.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 5b7d903c8be74e4c0561bb4a857619c9c62f95a9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239656"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Samouczek: Wdrażanie funkcji platformy Azure jako modułów usługi IoT Edge

Możesz użyć usługi Azure Functions, aby wdrożyć kod implementujący Twoją logikę biznesową bezpośrednio na urządzeniach usługi Azure IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania funkcji platformy Azure, która filtruje dane czujnika na symulowanym urządzeniu IoT Edge. Używasz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows](quickstart.md) lub [Linux](quickstart-linux.md). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:     

> [!div class="checklist"]
> * Używanie programu Visual Studio Code do tworzenia funkcji platformy Azure.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu VS Code i platformy Docker.
> * Wdrażanie modułu z rejestru kontenerów na urządzeniu usługi IoT Edge.
> * Wyświetlanie filtrowanych danych.

<center>

![Diagram — samouczek architektury, testowanie i wdrażanie modułu — funkcja](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Moduły funkcji platformy Azure w usłudze Azure IoT Edge są dostępne w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Utworzona w tym samouczku funkcja platformy Azure filtruje dane temperatury generowane przez urządzenie. Funkcja wysyła komunikaty w postaci strumienia wychodzącego do usługi Azure IoT Hub tylko wtedy, gdy temperatura przekroczy określony próg. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano za pomocą poprzedniego samouczka, aby skonfigurować środowisko programowania do tworzenia aplikacji kontenera systemu Linux: [Twórz moduły usługi IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Przez wykonanie kroków tego samouczka, musisz mieć następujące wymagania wstępne spełnione: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [Linux urządzenia z usługą Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowano [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów systemu Linux.

Aby opracować moduł usługi IoT Edge przy użyciu usługi Azure Functions, należy zainstalować następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

Narzędzia usługi Azure IoT dla programu Visual Studio Code, które zostało zainstalowane w ramach wymagań wstępnych, udostępnia możliwości zarządzania oraz pewne szablony kodu. W tej sekcji użyjesz programu Visual Studio Code do utworzenia rozwiązania usługi IoT Edge, które zawiera funkcję platformy Azure. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Tworzenie C# szablon rozwiązania funkcji, który można dostosować przy użyciu własnego kodu.

1. Otwórz program Visual Studio Code na maszynie deweloperskiej.

2. Otwórz paletę poleceń programu VS Code, wybierając kolejno opcje **Widok** > **Paleta poleceń**.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania, na przykład **FunctionSolution**, lub zaakceptuj nazwę domyślną. |
   | Wybierz szablon modułu | Wybierz pozycję **Azure Functions — C#** . |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CSharpFunction**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. Ostateczny ciąg będzie wyglądał następująco: \<nazwa rejestru\>.azurecr.io/CSharpFunction. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybierz swoje Architektura docelowa

Obecnie usługa Visual Studio Code można tworzyć moduły języka C dla urządzeń z AMD64 systemu Linux i ARM32v7 systemu Linux. Musisz wybrać architektury objęci za pomocą każdego rozwiązania, ponieważ skompilowane i uruchom w różny sposób dla każdego typu architektury kontenera. Wartość domyślna to AMD64 systemu Linux. 

1. Otwórz paletę poleceń i wyszukaj **usługi Azure IoT Edge: Ustaw domyślne platformę docelową dla nowoczesne rozwiązanie**, lub wybierz ikonę skrótu prowadzącą w pasku bocznym, w dolnej części okna. 

2. W palecie poleceń Wybierz architektury docelowej z listy opcji. W tym samouczku używamy maszynę wirtualną Ubuntu jako urządzenie usługi IoT Edge, dzięki czemu będzie Zachowaj ustawienie domyślne **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Dodajmy trochę kodu dodatkowe, tak aby moduł przetwarza wiadomości na urządzeniach brzegowych przed przekazaniem ich do usługi IoT Hub.

1. W programie Visual Studio Code Otwórz **modułów** > **CSharpFunction** > **CSharpFunction.cs**.

1. Zastąp zawartość pliku **CSharpFunction.cs** następującym kodem. Ten kod odbiera dane telemetrii dotyczące temperatury otoczenia i maszyny oraz przesyła dalej komunikat do usługi IoT Hub tylko wtedy, gdy temperatura maszyny przekracza zdefiniowaną wartość progową.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Zapisz plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpFunction**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

W tej sekcji dwa razy podasz poświadczenia dla rejestru kontenerów. Za pierwszym razem ma to na celu zalogowanie się lokalnie z maszyny deweloperskiej, aby program Visual Studio Code mógł wypchnąć obrazy do rejestru. Za drugim razem ma to miejsce w pliku **.env** rozwiązania usługi IoT Edge, co zapewnia urządzeniu usługi IoT Edge uprawnienia do ściągania obrazów z rejestru. 

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)**  > **Terminal**. 

2. Zaloguj się do rejestru kontenerów, wprowadzając następujące polecenie w zintegrowanym terminalu. Użyj nazwy użytkownika i serwera logowania skopiowanych wcześniej z rejestru kontenerów platformy Azure.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Po wyświetleniu monitu o hasło wklej hasło dla rejestru kontenerów, a następnie naciśnij klawisz **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują funkcje i wypychają je do rejestru kontenerów określonego podczas inicjowania rozwiązania. 

## <a name="view-your-container-image"></a>Wyświetlanie obrazu kontenera

Program Visual Studio Code generuje komunikat o powodzeniu po wypchnięciu obrazu kontenera do rejestru kontenerów. Jeśli chcesz osobiście potwierdzić pomyślne zakończenie operacji, możesz wyświetlić obraz w rejestrze. 

1. W witrynie Azure Portal przejdź do rejestru kontenerów platformy Azure. 
2. Wybierz pozycję **Repozytoria**.
3. Na liście powinno zostać wyświetlone repozytorium **csharpfunction**. Wybierz to repozytorium, aby wyświetlić więcej szczegółów.
4. W sekcji **Tagi** powinien zostać wyświetlony tag **0.0.1-amd64**. Ten tag odzwierciedla wersję i platformę skompilowanego obrazu. Te wartości są ustawiane w pliku module.json w folderze CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Do wdrożenia modułu funkcji na urządzeniu usługi IoT Edge możesz użyć witryny Azure Portal, tak jak w przewodnikach Szybki start. Możesz również wdrażać i monitorować moduły z poziomu programu Visual Studio Code. W następujących sekcjach używane są narzędzia usługi Azure IoT dla programu Visual Studio Code, wymienione w wymaganiach wstępnych. Zainstaluj teraz rozszerzenie, jeśli jeszcze tego nie zrobiono. 

1. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Create Deployment for single device** (Utwórz wdrożenie dla pojedynczego urządzenia). 

3. Przejdź do folderu rozwiązania, który zawiera moduł **CSharpFunction**. Otwórz folder config, wybierz plik **deployment.json**, a następnie wybierz pozycję **Wybierz manifest wdrożenia usługi Edge**.

4. Odśwież sekcję **Urządzenia usługi Azure IoT Hub**. Powinien zostać wyświetlony nowy moduł **CSharpFunction** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. Wyświetlenie nowych modułów może zająć kilka chwil. Urządzenie usługi IoT Edge musi pobrać nowe informacje o wdrożeniu z usługi IoT Hub, uruchomić nowe kontenery i zgłosić stan usłudze IoT Hub. 

   ![Wyświetlanie wdrożonych modułów w programie VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby wyświetlać wszystkie komunikaty otrzymywane przez centrum IoT Hub, uruchom polecenie **Azure IoT Hub: Rozpocznij monitorowanie wbudowany punkt końcowy w zdarzeń** w palecie poleceń.

Możesz również filtrować widok, aby wyświetlać wszystkie komunikaty przychodzące do centrum IoT Hub z określonego urządzenia. Kliknij prawym przyciskiem myszy urządzenie w **Azure IoT Hub Devices** i wybierz pozycję **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**.

Aby zatrzymać monitorowanie komunikatów, uruchom polecenie **Azure IoT Hub: Zatrzymaj monitorowanie wbudowany punkt końcowy w zdarzeń** w palecie poleceń. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku została utworzona funkcja platformy Azure zawierająca kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułach, możesz dowiedzieć się więcej o sposobie [opracowywanie zawartości przy użyciu usługi Azure IoT Edge dla programu Visual Studio Code](how-to-vs-code-develop-module.md). 

Kontynuuj pracę z kolejnymi samouczkami, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na brzegu sieci.

> [!div class="nextstepaction"]
> [Znajdowanie średnich przy użyciu okna ruchomego w usłudze Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
