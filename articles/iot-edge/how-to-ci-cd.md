---
title: Usługa Azure IoT Edge, ciągła integracja i ciągłe wdrażanie | Dokumentacja firmy Microsoft
description: Omówienie ciągłą integrację i ciągłe wdrażanie dla usługi Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5099ca70503ba2ed4ae8f4969a9199816c4986fb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302575"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge

W tym artykule przedstawiono sposób korzystania z funkcji ciągłego wdrażania usługom DevOps platformy Azure i programu Microsoft Team Foundation Server (TFS) i ciągłej integracji do tworzenia, testowania i wdrażania aplikacji, szybko i skutecznie do usługi Azure IoT Edge. 

W tym artykule dowiesz się jak:
* Tworzenie i sprawdź w przykładzie jednostki zawierającej rozwiązania usługi IoT Edge testów.
* Zainstaluj rozszerzenie Azure IoT Edge dla usługi DevOps platformy Azure.
* Skonfiguruj ciągłą integrację (CI), aby skompilować rozwiązanie i uruchomić testy jednostkowe.
* Skonfiguruj ciągłe wdrażanie (CD) na wdrożeniu rozwiązania i przeglądania odpowiedzi.

Potrwa 30 minut na wykonanie czynności opisanych w tym artykule.

![Ciągła Integracja i ciągłe dostarczanie](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Utwórz przykładowe rozwiązanie Azure IoT Edge, przy użyciu programu Visual Studio Code

W tej sekcji utworzysz przykładowe usługi IoT Edge rozwiązania zawierającego testy jednostkowe, które można wykonać w ramach procesu kompilacji. Przed zgodnie ze wskazówkami w tej sekcji, wykonaj kroki opisane w [tworzenia rozwiązań usługi IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. W palecie poleceń programu VS Code, typ, a następnie uruchom polecenie **Edge: rozwiązanie nowej usługi IoT Edge**. Następnie wybierz folder obszaru roboczego, podaj nazwę rozwiązania (nazwa domyślna to **EdgeSolution**) i Tworzenie modułu C# (**FilterModule**) jako pierwszego modułu, w tym rozwiązaniu. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Repozytorium obrazów domyślne opiera się na lokalnych rejestru platformy Docker (`localhost:5000/filtermodule`). Należy je zmienić w usłudze Azure Container Registry (`<your container registry address>/filtermodule`) lub usługi Docker Hub dalsze ciągłej integracji.

    ![Ustawienia rejestru Azure container Registry](./media/how-to-ci-cd/acr.png)

2. Okna programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Możesz opcjonalnie wpisać i uruchomić **Edge: Dodaj moduł IoT Edge** można dodać więcej modułów. Brak `modules` folderze `.vscode` folder i plik manifestu szablonu wdrożenia w folderze głównym. Wszystkie kody modułu użytkownika będzie podfolderów w folderze `modules`. `deployment.template.json` Jest szablon manifestu wdrożenia. Niektóre parametry w tym pliku, zostanie przetworzona z `module.json`, który istnieje w folderze każdego modułu.

3. Przykład rozwiązania usługi IoT Edge jest teraz gotowy. Domyślnego języka C# modułu działa jako moduł potok komunikatów. W `deployment.template.json`, zostanie wyświetlony, to rozwiązanie zawiera dwa moduły. Komunikat zostanie wygenerowane z `tempSensor` moduł i zostanie bezpośrednio przekazać w potoku za pomocą `FilterModule`, następnie wysyłane do usługi IoT hub. Zastąp całą **Program.cs** plików za pomocą poniżej zawartości. Aby uzyskać więcej informacji na temat ten fragment kodu mogą odwoływać się do [utworzyć projekt moduł IoT Edge C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
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
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. .Net Core utworzyć projekt testów jednostkowych. W Eksploratorze plików programu VS Code, Utwórz nowy folder **tests\FilterModuleTest** w obszarze roboczym. Następnie w zintegrowanym terminalu programu VS Code (**Ctrl + '**), uruchom następujące polecenia, aby utworzyć projekt testu xunit i Dodaj odwołanie do **FilterModule** projektu.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Struktura folderów](./media/how-to-ci-cd/add-test-project.png)

5. W **FilterModuleTest** folderu, zaktualizuj nazwę pliku **UnitTest1.cs** do **FilterModuleTest.cs**. Wybierz i Otwórz **FilterModuleTest.cs**, Zastąp poniższy fragment kodu, który zawiera testy jednostkowe z projektem FilterModule cały kod z.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. W zintegrowanym terminalu możesz wprowadzić następujące polecenia, aby uruchomić testy jednostkowe lokalnie. 
    ```cmd
    dotnet test
    ```

    ![Test jednostkowy](./media/how-to-ci-cd/unit-test.png)

7. Zapisz te projekty, a następnie zaewidencjonować je w repozytorium DevOps platformy Azure lub na serwerze TFS.
    

> [!NOTE]
> Aby uzyskać więcej informacji o korzystaniu z repozytoriów platformy Azure, zobacz [udostępnić swój kod za pomocą programu Visual Studio i repozytoria, Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Skonfiguruj ciągłą integrację
W tej sekcji opisano tworzenie potoku kompilacji, który jest skonfigurowany do automatycznego uruchamiania podczas ewidencjonowania zmiany przykładowe rozwiązanie IoT Edge, a jego automatycznie powoduje uruchomienie testów jednostkowych, które zawiera.

1. Zaloguj się do Twojej organizacji DevOps platformy Azure (**https://**_— konta_**. visualstudio.com**), a następnie otwórz projekt, gdzie sprawdzane w przykładowej aplikacji.

    ![Kod ewidencjonowania](./media/how-to-ci-cd/init-project.png)

1. Odwiedź stronę [usługi Azure IoT Edge dla usługi Azure DevOps](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) w witrynie Azure Marketplace metodyki DevOps. Kliknij przycisk **Pobierz bezpłatnie** i wykonaj instrukcje kreatora, aby zainstalować to rozszerzenie Twojej organizacji DevOps platformy Azure lub pobrać do TFS.

    ![Instalowanie rozszerzenia](./media/how-to-ci-cd/install-extension.png)

1. W swojej DevOps platformy Azure, otwórz **kompilacji &amp; wersji** Centrum i w **kompilacje** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz opcję **+ nowy** przycisku. 

    ![Nowej kompilacji](./media/how-to-ci-cd/add-new-build.png)

1. Po wyświetleniu monitu wybierz **DevOps Git platformy Azure** typ źródła; następnie wybierz projekt, repozytorium i gałąź, na którym znajduje się kod. Wybierz **nadal**.

    ![Wybierz git DevOps platformy Azure](./media/how-to-ci-cd/select-vsts-git.png)

1. W **wybierz szablon** oknie Wybierz **Rozpocznij z pustym procesem**.

    ![Rozpocznij pusty](./media/how-to-ci-cd/start-with-empty.png)

1. Kliknij przycisk **+** na prawej krawędzi **fazy 1** Aby dodać zadanie do fazy. Następnie wyszukaj i wybierz **.Net Core**i kliknij przycisk **Dodaj** zostać dodane to zadanie do fazy.

    ![polecenia DotNet test](./media/how-to-ci-cd/add-dot-net-core.png)

1. Aktualizacji **nazwę wyświetlaną** do **polecenia dotnet test**, a następnie w **polecenia** listy rozwijanej wybierz **testu**. Dodać pod ścieżką do **ścieżka do projektów**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Konfigurowanie polecenia dotnet test](./media/how-to-ci-cd/dotnet-test.png)

1. Kliknij przycisk **+** na prawej krawędzi **fazy 1** Aby dodać zadanie do fazy. Następnie wyszukaj i wybierz **usługi Azure IoT Edge**i kliknij przycisk **Dodaj** przycisk **dwukrotnie** można dodać te zadania do fazy.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Pierwsze zadanie usługi Azure IoT Edge, zaktualizować **nazwę wyświetlaną** do **moduł kompilacji i wypychania**, a następnie w **akcji** listy rozwijanej wybierz **Zbuduj i Wypchnij**. W **pliku Module.json** pola tekstowego, dodać pod ścieżką do niego. Następnie wybierz **Typ rejestru kontenerów**, upewnij się, możesz skonfigurować i wybrać ten sam rejestru w kodzie. Tego zadania utworzysz i Wypchnij wszystkie moduły w rozwiązaniu i publikowania w rejestrze kontenerów, wskazana. Jeśli moduły zostaną wypchnięte do różnych rejestrów, może mieć wiele **moduł kompilacji i wypychania** zadania.

    ```
    **/module.json
    ```

    ![Moduł kompilacji i wypychania](./media/how-to-ci-cd/module-build-push.png)

1. Drugie zadanie usługi Azure IoT Edge, zaktualizować **nazwę wyświetlaną** do **wdrażanie na urządzeniu usługi IoT Edge**, a następnie w **akcji** listy rozwijanej wybierz **wdrażanie do usługi IoT Edge urządzenie**. Wybierz swoją subskrypcję platformy Azure, a następnie wprowadź nazwę Centrum IoT Hub. Możesz określić identyfikator wdrożenia usługi IoT Edge i priorytetem wdrożenia. Możesz również wdrożyć na jednym lub wielu urządzeń. Jeśli są wdrażane na wielu urządzeniach, należy określić warunek docelowy urządzenia. Na przykład jeśli chcesz użyć znaczników urządzenia jako warunek, należy zaktualizować urządzenie odpowiednie tagi przed przystąpieniem do wdrożenia. 

    ![Wdrażanie na urządzenia brzegowe](./media/how-to-ci-cd/deploy-to-edge.png)

1. Kliknij przycisk **procesu** i upewnij się, że Twoje **kolejki agentów** jest **(wersja zapoznawcza) systemu Linux hostowanych**.

    ![Konfigurowanie](./media/how-to-ci-cd/configure-env.png)

1. Otwórz **wyzwalaczy** kartę, a następnie Włącz **ciągłej integracji** wyzwalacza. Upewnij się, że gałąź z kodem jest dołączony.

    ![Wyzwalacz](./media/how-to-ci-cd/configure-trigger.png)

1. Zapisz nowy potok kompilacji i nową kompilację w kolejce. Kliknij przycisk **Zapisz k & olejką** przycisku.

1. Wybierz łącze do kompilacji, na pasku komunikatów, który pojawia się. Lub przejdź do tworzenia potoku, aby wyświetlić zadania najnowszych kompilacji w kolejce.

    ![Kompilacja](./media/how-to-ci-cd/build-def.png)

1. Po zakończeniu kompilacji, zobaczysz podsumowanie dla każdego zadania oraz wyniki w pliku dziennika na żywo. 
    
    ![Zakończ](./media/how-to-ci-cd/complete.png)

1. Możesz wrócić do programu VS Code i sprawdzić Eksplorator urządzeń w usłudze IoT Hub. Urządzenie brzegowe za pomocą modułu powinien rozpocząć uruchamianie (Upewnij się, że poświadczenia rejestru zostały dodane do środowiska uruchomieniowego Edge).

    ![Uruchamianie usługi Edge](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Ciągłe wdrażanie na urządzeniach usługi IoT Edge

Aby włączyć ciągłe wdrażanie, po prostu należy skonfigurować zadania ciągłej integracji przy użyciu odpowiedniego urządzenia usługi IoT Edge, włączanie **wyzwalaczy** dla gałęzi w projekcie. W klasycznej praktyki metodyki DevOps projekt zawiera dwie gałęzie główne. Gałąź główna powinna być stabilną wersję kodu, a gałąź artykułów zawiera najnowszych zmian w kodzie. Każdy deweloper w zespole należy przejrzeć Programowanie gałęzi w celu jego lub własnych gałęzi funkcji sytuacji Rozpoczynanie aktualizowania kodu, co oznacza, że wszystkie zatwierdzenia na temat funkcji gałęzie Programowanie gałęzi. A co wypchnięte zatwierdzenie, powinny być badane za pośrednictwem systemu ciągłej integracji. Po w pełni przetestowane kod lokalnie, gałęzi funkcji powinny zostać scalone z gałęzią rozwoju za pośrednictwem żądania ściągnięcia. Kod w gałęzi dla deweloperów jest testowana za pośrednictwem elementu konfiguracji systemu, można scalone gałęzi głównej za pośrednictwem żądania ściągnięcia.

Dlatego podczas wdrażania aplikacji na urządzeniach usługi IoT Edge, istnieją trzy główne środowiska.
- W gałęzi funkcji możesz użyć symulowane urządzenie usługi IoT Edge na komputerze deweloperskim lub wdrożyć na urządzeniu fizycznym usługi IoT Edge.
- Na Programowanie gałęzi, należy wdrożyć na urządzeniu fizycznym usługi IoT Edge.
- W gałęzi głównej urządzenia usługi IoT Edge docelowego powinien być urządzeń produkcyjnych.

## <a name="next-steps"></a>Kolejne kroki

* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).
