---
title: Wdrażanie funkcji Azure Functions za pomocą usługi Azure IoT Edge | Microsoft Docs
description: Wdrażanie funkcji platformy Azure jako modułu na urządzeniu brzegowym.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f3ba0ccb1cb8961344b605e7ec386b6d6692262
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006881"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Samouczek: wdrażanie funkcji Azure Functions jako modułów usługi IoT Edge (wersja zapoznawcza)

Możesz użyć usługi Azure Functions, aby wdrożyć kod implementujący Twoją logikę biznesową bezpośrednio na urządzeniach usługi Azure IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania funkcji platformy Azure, która filtruje dane czujnika na symulowanym urządzeniu IoT Edge. Używasz symulowanego urządzenia usługi IoT Edge utworzonego podczas pracy z przewodnikami Szybki start dotyczącymi wdrażania usługi Azure IoT Edge na urządzeniu symulowanym w systemie [Windows][lnk-tutorial1-win] lub [Linux][lnk-tutorial1-lin]. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:     

> [!div class="checklist"]
> * Używanie programu Visual Studio Code do tworzenia funkcji platformy Azure.
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu VS Code i platformy Docker.
> * Wdrażanie modułu z rejestru kontenerów na urządzeniu usługi IoT Edge.
> * Wyświetlanie filtrowanych danych.

>[!NOTE]
>Moduły funkcji platformy Azure w usłudze Azure IoT Edge są dostępne w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Utworzona w tym samouczku funkcja platformy Azure filtruje dane temperatury generowane przez urządzenie. Funkcja wysyła komunikaty w postaci strumienia wychodzącego do usługi Azure IoT Hub tylko wtedy, gdy temperatura przekroczy określony próg. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do przetestowania modułu funkcji skompilowanego w tym samouczku potrzebne jest urządzenie usługi IoT Edge. Możesz korzystać z urządzenia, które skonfigurowano w przewodniku Szybki start dla systemu [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Na maszynie deweloperskiej są niezbędne następujące wstępnie wymagane elementy: 
* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code. 
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Program Docker CE](https://docs.docker.com/install/) na maszynie deweloperskiej. 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz kolejno pozycje **Utwórz zasób** > **Kontenery** > **Container Registry**.

    ![Tworzenie rejestru kontenerów](./media/tutorial-deploy-function/create-container-registry.png)

2. Wprowadź nazwę rejestru i wybierz subskrypcję.
3. Dla grupy zasobów zaleca się użycie nazwy tej samej grupy zasobów, która zawiera centrum IoT Hub. Dzięki umieszczeniu wszystkich zasobów w tej samej grupie można nimi zarządzać razem. Na przykład usunięcie grupy zasobów używanej do testowania spowoduje usunięcie wszystkich zasobów testowych w grupie. 
4. Ustaw jednostkę SKU na wartość **Podstawowa** i ustaw przełącznik **Administrator** na pozycję **Włącz**. 
5. Wybierz pozycję **Utwórz**.
6. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
7. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Te wartości zostaną wykorzystane później w samouczku. 

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji
W następujących krokach przedstawiono sposób tworzenia funkcji usługi IoT Edge przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.

1. Otwórz program Visual Studio Code.
2. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)** > **Integrated Terminal (Zintegrowany terminal)**. 
2. Otwórz paletę poleceń programu VS Code, wybierając kolejno opcje **Widok** > **Paleta poleceń**.
3. W palecie poleceń wprowadź, a następnie uruchom polecenie **Azure: zaloguj się**. Postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
3. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie: 

   1. Wybierz folder, w którym chcesz utworzyć rozwiązanie. 
   2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
   3. Wybierz szablon **Azure Functions — C#** jako szablon modułu. 
   4. Nadaj modułowi nazwę **CSharpFunction**. 
   5. Określ rejestr kontenerów platformy Azure utworzony w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania. Ostateczny ciąg będzie wyglądał następująco: \<nazwa rejestru\>.azurecr.io/csharpfunction.

4. W oknie programu VS Code jest ładowany obszar roboczy rozwiązania usługi IoT Edge: folder \.vscode, plik szablonu manifestu wdrożenia i plik \.env. W eksploratorze programu VS Code otwórz plik **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Zastąp zawartość pliku następującym kodem:

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
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
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
   ```

6. Zapisz plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu **CSharpFunction**, aby filtrować komunikaty, w których zgłoszona temperatura maszyny jest niższa od akceptowalnego poziomu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code. Następnie możesz wypchnąć obraz modułu do rejestru kontenerów platformy Azure: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Użyj nazwy użytkownika i serwera logowania skopiowanych wcześniej z rejestru kontenerów platformy Azure. Zostanie wyświetlony monit o podanie hasła. Wklej hasło w monicie i naciśnij klawisz **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. W eksploratorze programu VS Code otwórz plik deployment.template.json w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik informuje środowisko uruchomieniowe usługi IoT Edge, które moduły mają zostać wdrożone na urządzeniu. Aby dowiedzieć się więcej na temat manifestów wdrożenia, zobacz [Jak używać modułów usługi IoT Edge, konfigurować je i używać ich ponownie](module-composition.md).

3. Znajdź sekcję **registryCredentials** w manifeście wdrożenia. Zaktualizuj elementy **username**, **password** i **address** (nazwa użytkownika, hasło i adres) przy użyciu poświadczeń z rejestru kontenerów. Ta sekcja przyznaje środowisku uruchomieniowemu usługi IoT Edge na urządzeniu uprawnienie do ściągania obrazów kontenerów, które są przechowywane w prywatnym rejestrze. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany przez usługę git.

5. Zapisz ten plik.

6. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Skompiluj rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik deployment.json w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują funkcje i wypychają je do rejestru kontenerów określonego podczas inicjowania rozwiązania. 

## <a name="view-your-container-image"></a>Wyświetlanie obrazu kontenera

Program Visual Studio Code generuje komunikat o powodzeniu po wypchnięciu obrazu kontenera do rejestru kontenerów. Jeśli chcesz osobiście potwierdzić pomyślne zakończenie operacji, możesz wyświetlić obraz w rejestrze. 

1. W witrynie Azure Portal przejdź do rejestru kontenerów platformy Azure. 
2. Wybierz pozycję **Repozytoria**.
3. Na liście powinno zostać wyświetlone repozytorium **csharpfunction**. Wybierz to repozytorium, aby wyświetlić więcej szczegółów.
4. W sekcji **Tagi** powinien zostać wyświetlony tag **0.0.1-amd64**. Ten tag odzwierciedla wersję i platformę skompilowanego obrazu. Te wartości są ustawiane w pliku module.json w folderze CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

Do wdrożenia modułu funkcji na urządzeniu usługi IoT Edge możesz użyć witryny Azure Portal, tak jak w przewodnikach Szybki start. Możesz również wdrażać i monitorować moduły z poziomu programu Visual Studio Code. W następujących sekcjach używane jest rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code, wymienione w wymaganiach wstępnych. Zainstaluj teraz rozszerzenie, jeśli jeszcze tego nie zrobiono. 

1. Otwórz paletę poleceń programu VS Code, wybierając kolejno opcje **Widok** > **Paleta poleceń**.

2. Wyszukaj i uruchom polecenie **Azure: zaloguj**. Postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. 

3. W palecie poleceń wyszukaj i uruchom polecenie **Azure IoT Hub: wybierz centrum IoT Hub**. 

4. Wybierz subskrypcję, która zawiera Twoje centrum IoT Hub, a następnie wybierz centrum IoT Hub, do którego chcesz uzyskać dostęp.

5. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**. 

6. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla urządzenia usługi IoT Edge**. 

7. Przejdź do folderu rozwiązania, który zawiera moduł **CSharpFunction**. Otwórz folder config, wybierz plik deployment.json, a następnie wybierz pozycję **Wybierz manifest wdrożenia usługi Edge**.

8. Odśwież sekcję **Urządzenia usługi Azure IoT Hub**. Powinien zostać wyświetlony nowy moduł **CSharpFunction** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. 

   ![Wyświetlanie wdrożonych modułów w programie VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Aby wyświetlać wszystkie komunikaty otrzymywane przez centrum IoT Hub, uruchom polecenie **Azure IoT Hub: rozpocznij monitorowanie komunikatu D2C** w palecie poleceń.

Możesz również filtrować widok, aby wyświetlać wszystkie komunikaty przychodzące do centrum IoT Hub z określonego urządzenia. Kliknij prawym przyciskiem myszy urządzenie w sekcji **Urządzenia usługi Azure IoT Hub** i wybierz polecenie **Rozpocznij monitorowanie komunikatów D2C**.

Aby zatrzymać monitorowanie komunikatów, uruchom polecenie **Azure IoT Hub: Stop monitoring D2C message (Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C)** w palecie poleceń. 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Usuń środowisko uruchomieniowe usługi IoT Edge oparte na platformie danego urządzenia IoT (Linux lub Windows).

#### <a name="windows"></a>Windows

Usuń środowisko uruchomieniowe usługi IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Usuń kontenery utworzone na urządzeniu. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Usuń środowisko uruchomieniowe usługi IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Usuń kontenery utworzone na urządzeniu. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Usuń środowisko uruchomieniowe kontenera.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Następne kroki

W tym samouczku została utworzona funkcja platformy Azure zawierająca kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [programować usługę Azure Functions za pomocą usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-csharp-function.md). 

Kontynuuj pracę z kolejnymi samouczkami, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na brzegu sieci.

> [!div class="nextstepaction"]
> [Znajdowanie średnich przy użyciu okna ruchomego w usłudze Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
