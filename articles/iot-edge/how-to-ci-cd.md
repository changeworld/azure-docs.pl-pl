---
title: Usługa Azure IoT krawędzi ciągłej integracji i ciągłe wdrażanie | Dokumentacja firmy Microsoft
description: Omówienie ciągłej integracji i ciągłe wdrażanie Edge IoT Azure
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 62d8d770f6b4c3a62a2395eb8c1505dbc3835c28
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047459"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągłej integracji i ciągłe wdrażanie krawędzią IoT Azure

W tym artykule przedstawiono sposób korzystania z funkcji ciągłego wdrażania programu Visual Studio Team Services (VSTS) i Microsoft Team Foundation Server (TFS) i ciągłej integracji do tworzenia, testowania i wdrażania aplikacji szybkie i skuteczne do platformy Azure Krawędź IoT. 

W tym artykule przedstawiono sposób:
* Tworzenie i sprawdź w przykładowym zawierającego jednostki rozwiązania IoT krawędzi testy.
* Zainstaluj rozszerzenie Azure IoT krawędzi dla Twojej usługi VSTS.
* Konfigurowanie ciągłej integracji (CI), aby skompilować rozwiązanie i uruchom testy jednostkowe.
* Konfigurowanie ciągłego wdrażania (CD) do wdrożenia rozwiązania i przeglądania odpowiedzi.

Potrwa 30 minut, aby wykonać kroki opisane w tym artykule.

![CI i dysku CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Tworzenie rozwiązań Azure IoT Edge próbki przy użyciu programu Visual Studio Code

W tej sekcji utworzysz próbkę krawędzi IoT rozwiązania zawierającego testy jednostkowe które mogą być wykonywane jako część procesu kompilacji. Przed wykonaniem wskazówki zawarte w tej sekcji, wykonaj kroki [opracowywania rozwiązań IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązanie graniczne IoT nowe**. Następnie wybierz folder roboczy, podaj nazwę rozwiązania (nazwa domyślna to **EdgeSolution**) i Utwórz moduł C# (**FilterModule**) jako pierwszego modułu użytkownika w tym rozwiązaniu. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Repozytorium obrazów domyślna jest oparta na lokalnym rejestrze Docker (`localhost:5000/filtermodule`). Należy je zmienić w rejestrze kontenera platformy Azure (`<your container registry address>/filtermodule`) lub Centrum Docker dla dalszego ciągłej integracji.

    ![Instalator ACR](./media/how-to-ci-cd/acr.png)

2. Okno kodzie VS załaduje rozwiązania IoT krawędzi obszaru roboczego. Opcjonalnie możesz wpisać i uruchomić **krawędzi: Dodaj krawędzi IoT modułu** można dodać więcej modułów. Brak `modules` folderu, `.vscode` folderu i pliku manifestu szablonu wdrażania w folderze głównym. Wszystkie kody modułu użytkownika będzie podfolderów w folderze `modules`. `deployment.template.json` Jest manifestu szablonu wdrożenia. Niektóre parametry w tym pliku zostaną wydzielone z `module.json`, który istnieje w folderze co moduł.

3. Teraz przykładu rozwiązań IoT Edge jest gotowa. Domyślny C# moduł działa jako moduł komunikat potoku. W `deployment.template.json`, pojawi się to rozwiązanie zawiera dwa moduły. Wiadomość zostanie wygenerowane z `tempSensor` modułu i będzie można bezpośrednio przetwarzana potokowo za pośrednictwem `FilterModule`, następnie wysyłane do Centrum IoT. Zastąp całą **Program.cs** pliku z poniżej zawartości. Aby uzyskać więcej informacji na temat następujący fragment kodu może odwoływać się do [utworzyć projekt modułu IoT krawędzi C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

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

4. Utwórz .net Core jednostkowy projekt testowy. W Eksploratorze plików kodu programu VS, Utwórz nowy folder **tests\FilterModuleTest** w obszarze roboczym. Następnie w kodzie VS zintegrowane terminali (**Ctrl + "**), uruchom następujące polecenia, aby utworzyć projekt testowy xunit i Dodaj odwołanie do **FilterModule** projektu.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Struktura folderów](./media/how-to-ci-cd/add-test-project.png)

5. W **FilterModuleTest** folderu, zaktualizuj nazwę pliku **UnitTest1.cs** do **FilterModuleTest.cs**. Wybierz i Otwórz **FilterModuleTest.cs**, Zamień poniższy fragment kodu, który zawiera testy jednostkowe dla projektów FilterModule cały kod z.

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

6. W terminalu zintegrowanego możesz wprowadzić następujące polecenia, aby uruchomić testy jednostkowe lokalnie. 
    ```cmd
    dotnet test
    ```

    ![Testu jednostkowego](./media/how-to-ci-cd/unit-test.png)

7. Zapisz te projekty, a następnie sprawdź go do repozytorium VSTS lub TFS.
    

> [!NOTE]
> Aby uzyskać więcej informacji o korzystaniu z programu VSTS kodu repozytoriów, zobacz [udostępnianie kodu programu Visual Studio i VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-continuous-integration"></a>Konfigurowanie ciągłej integracji
W tej sekcji utworzysz definicję kompilacji, która jest skonfigurowana do automatycznego uruchamiania podczas ewidencjonowania zmiany przykładowe rozwiązanie graniczne IoT, i automatycznie wykona testy jednostek, które zawiera.

1. Zaloguj się do konta usługi VSTS (**https://**_Twoje konto_**. visualstudio.com**) i otworzyć projekt, w którym zaznaczono w przykładowej aplikacji.

    ![Sprawdź w kodzie](./media/how-to-ci-cd/init-project.png)

1. Odwiedź stronę [Azure IoT krawędzi VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) w witrynie Marketplace programu VSTS. Kliknij przycisk **uzyskać bezpłatne** i użyj kreatora, aby zainstalować to rozszerzenie do konta usługi VSTS lub pobierania do programu TFS.

    ![Instalowanie rozszerzenia](./media/how-to-ci-cd/install-extension.png)

1. W programu VSTS Otwórz **kompilacji &amp; wersji** Centrum i w **kompilacje** , wybierz pozycję **+ nową definicję**. Lub, jeśli masz już definicje kompilacji, wybierz **+ nowy** przycisku. 

    ![Nowej kompilacji](./media/how-to-ci-cd/add-new-build.png)

1. Po wyświetleniu monitu wybierz **VSTS Git** typ źródła; następnie wybierz projekt, repozytorium i oddziału, w którym znajduje się kod. Wybierz **kontynuować**.

    ![Wybierz VSTS git](./media/how-to-ci-cd/select-vsts-git.png)

1. W **wybierz szablon** okna, wybierz **rozpoczynać pusty procesu**.

    ![Rozpocznij pusty](./media/how-to-ci-cd/start-with-empty.png)

1. Kliknij przycisk **+** prawej strony **fazy 1** można dodać zadania do fazy. Następnie wyszukaj i wybierz **.Net Core**i kliknij przycisk **Dodaj** można dodać zadania do fazy.

    ![DotNet test](./media/how-to-ci-cd/add-dot-net-core.png)

1. Aktualizacja **Nazwa wyświetlana** do **testu dotnet**i w **polecenia** listy rozwijanej wybierz **testu**. Dodaj poniżej ścieżkę do **ścieżkę do projektów**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Skonfiguruj dotnet test](./media/how-to-ci-cd/dotnet-test.png)

1. Kliknij przycisk **+** prawej strony **fazy 1** można dodać zadania do fazy. Następnie wyszukaj i wybierz **Azure IoT krawędzi**i kliknij przycisk **Dodaj** przycisk **dwukrotnie** do dodania do fazy te zadania.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. W pierwszym zadaniem Azure IoT krawędzi aktualizacji **Nazwa wyświetlana** do **moduł kompilacji i wypychania**i w **akcji** listy rozwijanej wybierz **kompilacji i Wypchnij**. W **pliku Module.json** pole tekstowe, Dodaj poniżej ścieżkę do niego. Następnie wybierz pozycję **Typ rejestru kontenera**, upewnij się, że należy skonfigurować i wybrać tej samej rejestru w kodzie. To zadanie zostanie kompilacji i push wszystkie moduły w rozwiązaniu i opublikuj wskazanej rejestru kontenera. Jeśli moduły zostanie przekazany do różnych rejestrów, może mieć wielu **moduł kompilacji i wypychania** zadania.

    ```
    **/module.json
    ```

    ![Moduł kompilacji i wypychania](./media/how-to-ci-cd/module-build-push.png)

1. W drugim zadaniem Azure IoT krawędzi aktualizacji **Nazwa wyświetlana** do **wdrażanie do urządzenia IoT**, a następnie w **akcji** listy rozwijanej wybierz pozycję **wdrażanie na krawędzi IoT urządzenie**. Wybierz subskrypcję platformy Azure i wprowadź nazwę Centrum IoT. Można określić identyfikator wdrożenia krawędzi IoT i priorytet wdrożenia. Można także wdrożyć do jednej lub wielu urządzeń. Jeśli są wdrażane na wielu urządzeniach, należy określić warunek docelowy urządzenia. Na przykład jeśli chcesz użyć znaczników urządzenia jako warunek, musisz zaktualizować urządzenia odpowiednie tagi przed przystąpieniem do wdrożenia. 

    ![Wdrażanie na krawędzi](./media/how-to-ci-cd/deploy-to-edge.png)

1. Kliknij przycisk **procesu** i upewnij się, że Twoje **kolejki agenta** jest **Podgląd Linux hostowanych**.

    ![Konfigurowanie](./media/how-to-ci-cd/configure-env.png)

1. Otwórz **wyzwalaczy** karcie i Włącz **ciągłej integracji** wyzwalacza. Upewnij się, że gałąź zawierającą kod jest dołączony.

    ![Wyzwalacz](./media/how-to-ci-cd/configure-trigger.png)

1. Zapisz nową definicję kompilacji i nowej kompilacji w kolejce. Kliknij przycisk **Zapisz & kolejka** przycisku.

1. Na pasku komunikatów, który pojawia się, wybierz link do kompilacji. Lub przejdź do definicji, aby wyświetlić zadania najnowszych kompilacji w kolejce kompilacji.

    ![Kompilacja](./media/how-to-ci-cd/build-def.png)

1. Po zakończeniu kompilacji, wyświetlane jest podsumowanie dla każdego zadania i wyniki w pliku dziennika na żywo. 
    
    ![Zakończ](./media/how-to-ci-cd/complete.png)

1. Można wracać do kodu VS i sprawdzić explorer urządzenia IoT Hub. Urządzenie brzegowe z modułem powinien rozpocząć uruchamianie (Upewnij się, że rejestru poświadczenia zostały dodane do środowiska wykonawczego krawędzi).

    ![Krawędź uruchomiona](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Ciągłe wdrażanie do urządzenia brzegowe IoT

Umożliwiające ciągłe wdrażanie zasadniczo należy skonfigurować zadania CI z prawidłowego urządzenia brzegowe IoT, włączanie **wyzwalaczy** dla gałęzie w projekcie. W klasycznym rozwiązaniem DevOps projekt zawiera dwa główne rozgałęzienia. Gałęzi głównej powinny być stabilna wersja kodu i opracowanie gałęzi zawiera najnowsze zmiany kodu. Każdy deweloper w zespole powinien rozwidlania gałęzi opracowanie ich lub własne gałęzi funkcji w przypadku uruchamiania aktualizowanie kodu, co oznacza, że wszystkie zatwierdza funkcję gałęzi poza gałęzi deweloperów. I każdym zatwierdzeniem wciśnięcia powinny być testowane za pośrednictwem elementu konfiguracji systemu. Po w pełni przetestowany kod lokalnie, w gałęzi funkcji powinny zostać scalone do gałęzi opracowanie przez żądanie ściągnięcia. Podczas testowania kodu w gałęzi developer za pośrednictwem elementu konfiguracji systemu, można by scalić do głównej gałęzi przy użyciu żądania ściągnięcia.

Tak podczas wdrażania urządzenia brzegowe IoT, istnieją trzy główne środowiska.
- Funkcja gałęzi można użyć symulowane urządzenie brzegowe IoT na komputerze deweloperskim lub wdrożyć na fizyczne urządzenie brzegowe IoT.
- Na opracowanie gałęzi, należy wdrożyć do fizycznego urządzenia IoT krawędzi.
- W głównej gałęzi te urządzenia IoT krawędzi powinien być urządzeń produkcji.

## <a name="next-steps"></a>Kolejne kroki

* Zrozumienie wdrożenia IoT krawędzi w [wdrożeń zrozumieć IoT Edge dla urządzeń z jednego lub na dużą skalę](module-deployment-monitoring.md)
* Szczegółowe kroki umożliwiające tworzenie, aktualizowanie lub usuwanie wdrożenia w [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor.md).
