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
ms.openlocfilehash: 1fba2c4e5191d4c827035362a8eb6876fcbb67cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611878"
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

Urządzenie usługi Azure IoT Edge:

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Narzędzia usługi Azure IoT dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). 
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Program Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

W tym samouczku narzędzia usługi Azure IoT dla programu Visual Studio Code zostaną użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Do przechowywania obrazów kontenerów możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

    ![Tworzenie rejestru kontenerów w witrynie Azure Portal](./media/tutorial-deploy-function/create-container-registry.png)

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

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

Narzędzia usługi Azure IoT dla programu Visual Studio Code, które zostało zainstalowane w ramach wymagań wstępnych, udostępnia możliwości zarządzania oraz pewne szablony kodu. W tej sekcji użyjesz programu Visual Studio Code do utworzenia rozwiązania usługi IoT Edge, które zawiera funkcję platformy Azure. 

1. Otwórz program Visual Studio Code na maszynie deweloperskiej.

2. Otwórz paletę poleceń programu VS Code, wybierając kolejno opcje **Widok** > **Paleta poleceń**.

3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania, na przykład **FunctionSolution**, lub zaakceptuj nazwę domyślną. |
   | Wybierz szablon modułu | Wybierz pozycję **Azure Functions — C#**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CSharpFunction**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. Ostateczny ciąg będzie wyglądał następująco: \<nazwa rejestru\>.azurecr.io/CSharpFunction. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-deploy-function/repository.png)

4. W oknie programu VS Code jest ładowany obszar roboczy rozwiązania usługi IoT Edge: folder \.vscode, plik szablonu manifestu wdrożenia i plik \.env. W eksploratorze programu VS Code otwórz plik **modules** > **CSharpFunction** > **CSharpFunction.cs**.

5. Zastąp zawartość pliku **CSharpFunction.cs** następującym kodem. Ten kod odbiera dane telemetrii dotyczące temperatury otoczenia i maszyny oraz przesyła dalej komunikat do usługi IoT Hub tylko wtedy, gdy temperatura maszyny przekracza zdefiniowaną wartość progową.

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

6. Zapisz plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpFunction**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

W tej sekcji dwa razy podasz poświadczenia dla rejestru kontenerów. Za pierwszym razem ma to na celu zalogowanie się lokalnie z maszyny deweloperskiej, aby program Visual Studio Code mógł wypchnąć obrazy do rejestru. Za drugim razem ma to miejsce w pliku **.env** rozwiązania usługi IoT Edge, co zapewnia urządzeniu usługi IoT Edge uprawnienia do ściągania obrazów z rejestru. 

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)** > **Terminal**. 

2. Zaloguj się do rejestru kontenerów, wprowadzając następujące polecenie w zintegrowanym terminalu. Użyj nazwy użytkownika i serwera logowania skopiowanych wcześniej z rejestru kontenerów platformy Azure.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Po wyświetleniu monitu o hasło wklej hasło dla rejestru kontenerów, a następnie naciśnij klawisz **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik informuje środowisko uruchomieniowe usługi IoT Edge, które moduły mają zostać wdrożone na urządzeniu. Zwróć uwagę, że Twój moduł funkcji **CSharpFunction** znajduje się na liście wraz z modułem **tempSensor** udostępniającym dane testowe. Aby dowiedzieć się więcej na temat manifestów wdrożenia, zobacz [Jak używać modułów usługi IoT Edge, konfigurować je i używać ich ponownie](module-composition.md).

   ![Wyświetlanie modułu w manifeście wdrożenia](./media/tutorial-deploy-function/deployment-template.png)

3. Otwórz plik **.env** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik ignorowany przez repozytorium git przechowuje poświadczenia rejestru, dzięki czemu nie ma potrzeby umieszczenia ich w szablonie manifestu wdrożenia. Podaj **nazwę użytkownika** i **hasło** dla rejestru kontenerów. 

5. Zapisz ten plik.

6. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują funkcje i wypychają je do rejestru kontenerów określonego podczas inicjowania rozwiązania. 

## <a name="view-your-container-image"></a>Wyświetlanie obrazu kontenera

Program Visual Studio Code generuje komunikat o powodzeniu po wypchnięciu obrazu kontenera do rejestru kontenerów. Jeśli chcesz osobiście potwierdzić pomyślne zakończenie operacji, możesz wyświetlić obraz w rejestrze. 

1. W witrynie Azure Portal przejdź do rejestru kontenerów platformy Azure. 
2. Wybierz pozycję **Repozytoria**.
3. Na liście powinno zostać wyświetlone repozytorium **csharpfunction**. Wybierz to repozytorium, aby wyświetlić więcej szczegółów.
4. W sekcji **Tagi** powinien zostać wyświetlony tag **0.0.1-amd64**. Ten tag odzwierciedla wersję i platformę skompilowanego obrazu. Te wartości są ustawiane w pliku module.json w folderze CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Do wdrożenia modułu funkcji na urządzeniu usługi IoT Edge możesz użyć witryny Azure Portal, tak jak w przewodnikach Szybki start. Możesz również wdrażać i monitorować moduły z poziomu programu Visual Studio Code. W następujących sekcjach używane są narzędzia usługi Azure IoT dla programu Visual Studio Code, wymienione w wymaganiach wstępnych. Zainstaluj teraz rozszerzenie, jeśli jeszcze tego nie zrobiono. 

1. Otwórz paletę poleceń programu VS Code, wybierając kolejno opcje **Widok** > **Paleta poleceń**.

2. Wyszukaj i uruchom polecenie **Azure: Sign in** (Azure: zaloguj). Postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. 

3. W palecie poleceń wyszukaj i uruchom polecenie **Azure IoT Hub: Select IoT Hub**  (Azure IoT Hub: wybierz centrum IoT Hub). 

4. Wybierz subskrypcję, która zawiera Twoje centrum IoT Hub, a następnie wybierz centrum IoT Hub, do którego chcesz uzyskać dostęp.

5. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

6. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Create Deployment for single device** (Utwórz wdrożenie dla pojedynczego urządzenia). 

7. Przejdź do folderu rozwiązania, który zawiera moduł **CSharpFunction**. Otwórz folder config, wybierz plik **deployment.json**, a następnie wybierz pozycję **Wybierz manifest wdrożenia usługi Edge**.

8. Odśwież sekcję **Urządzenia usługi Azure IoT Hub**. Powinien zostać wyświetlony nowy moduł **CSharpFunction** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. Wyświetlenie nowych modułów może zająć kilka chwil. Urządzenie usługi IoT Edge musi pobrać nowe informacje o wdrożeniu z usługi IoT Hub, uruchomić nowe kontenery i zgłosić stan usłudze IoT Hub. 

   ![Wyświetlanie wdrożonych modułów w programie VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby wyświetlać wszystkie komunikaty otrzymywane przez centrum IoT Hub, uruchom polecenie **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: rozpocznij monitorowanie komunikatu D2C) w palecie poleceń.

Możesz również filtrować widok, aby wyświetlać wszystkie komunikaty przychodzące do centrum IoT Hub z określonego urządzenia. Kliknij prawym przyciskiem myszy urządzenie w sekcji **Urządzenia usługi Azure IoT Hub** i wybierz polecenie **Rozpocznij monitorowanie komunikatów D2C**.

Aby zatrzymać monitorowanie komunikatów, uruchom polecenie **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C) w palecie poleceń. 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku została utworzona funkcja platformy Azure zawierająca kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [programować usługę Azure Functions za pomocą usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-csharp-function.md). 

Kontynuuj pracę z kolejnymi samouczkami, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na brzegu sieci.

> [!div class="nextstepaction"]
> [Znajdowanie średnich przy użyciu okna ruchomego w usłudze Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

