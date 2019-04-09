---
title: Samouczek dotyczący tworzenia niestandardowego modułu C — Azure IoT Edge | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka C i wdrożyć go na urządzeniu brzegowym
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: eeaff4769dba5b6e6951665d09cd12d13f22af07
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273722"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Samouczek: Opracowywanie modułu usługi IoT Edge w języku C i wdrażanie go na urządzeniu symulowanym

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w języku C przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu Visual Studio Code i platformy Docker
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych


Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Maszynę wirtualną platformy Azure można użyć jako urządzenia usługi IoT Edge, wykonując kroki opisane w przewodniku Szybki Start dla [Linux](quickstart-linux.md) lub [urządzenia Windows](quickstart.md). 

   >[!TIP]
   >Ten samouczek używa Visual Studio Code do tworzenia modułu C za pomocą kontenerów systemu Linux. Jeśli chcesz programować w C dla Windows kontenery, należy użyć programu Visual Studio 2017. Aby uzyskać więcej informacji, zobacz [Użyj programu Visual Studio 2017 do tworzenia i debugowania modułów dla usługi Azure IoT Edge](how-to-visual-studio-develop-module.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Rozszerzenie języka C/C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) dla programu Visual Studio Code.
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla programu Visual Studio Code.
* [Program Docker CE](https://docs.docker.com/install/).

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
W następujących krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge bazującego na platformie .NET Core 2.0 przy użyciu programu Visual Studio Code i narzędzi usługi Azure IoT.

### <a name="create-a-new-solution"></a>Tworzenie nowego rozwiązania

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code.

2. W palecie poleceń wpisz i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz pozycję **C Module** (Moduł języka C). |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/cmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-c-module/repository.png)

Okna programu VS Code ładuje obszar roboczy rozwiązania usługi IoT Edge z pięciu części najwyższego poziomu. **Modułów** folder zawiera kod języka C dla modułu i pliki Dockerfile do tworzenia modułu jako obrazu kontenera. W pliku **\.env** są przechowywane poświadczenia rejestru kontenerów. Plik **deployment.template.json** zawiera informacje, których środowisko uruchomieniowe usługi IoT Edge używa do wdrażania modułów na urządzeniu. Natomiast plik **deployment.debug.template.json** zawiera wersję modułów służącą do debugowania. Folder **\.vscode** i plik **\.gitignore** nie będą edytowane w tym samouczku.

Jeśli podczas tworzenia własnego rozwiązania nie określisz rejestru kontenerów, ale zaakceptujesz wartość domyślną localhost:5000, nie będziesz mieć pliku \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Dodaj kod do modułu C umożliwiająca go, aby sprawdzić, czy temperatura zgłoszonych maszyny został przekroczony próg bezpieczne. Jeśli temperatura jest zbyt duża, moduł dodaje parametrów alertu do komunikatu przed wysłaniem danych do usługi IoT Hub. 

1. Dane z czujnika w tym scenariuszu są dostępne w formacie JSON. Aby filtrować komunikaty w formacie JSON, należy zaimportować bibliotekę JSON dla języka C. W tym samouczku używana jest biblioteka Parson.

   1. Pobierz [repozytorium Parson w usłudze GitHub](https://github.com/kgabis/parson). Skopiuj pliki **parson.c** i **parson.h** do folderu **CModule**.

   2. Otwórz plik **modules** > **CModule** > **CMakeLists.txt**. Na początku zaimportuj pliki biblioteki Parson jako bibliotekę o nazwie **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Dodaj bibliotekę **my_parson** do listy bibliotek w funkcji **target_link_libraries** pliku CMakeLists.txt.

   4. Zapisz plik **CMakeLists.txt**.

   5. Otwórz plik **modules** > **CModule** > **main.c**. W dolnej części listy instrukcji #include, Dodaj nowy, aby uwzględnić `parson.h` obsługi formatu JSON:

      ```c
      #include "parson.h"
      ```

1. W pliku **main.c** dodaj zmienną globalną o nazwie `temperatureThreshold` po sekcji include. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Zastąp całą funkcję `CreateMessageInstance` poniższym kodem. Ta funkcja przydziela kontekst dla wywołania zwrotnego.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

1. Zastąp całą funkcję `InputQueue1Callback` poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów.

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Dodaj funkcję `moduleTwinCallback`. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Zastąp funkcję `SetupCallbacksForModule` poniższym kodem.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Zapisz plik main.c.

1. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik informuje agenta usługi IoT Edge, które moduły mają zostać wdrożone (w tym przypadku **tempSensor** i **CModule**), i informuje centrum usługi IoT Edge o tym, jak kierować wiadomości między nimi. Rozszerzenie programu Visual Studio Code automatycznie wypełni większość informacji potrzebnych w szablonie wdrożenia, ale sprawdź, czy wszystko jest dokładne dla Twojego rozwiązania: 

   1. Domyślną platformą ustawioną na pasku stanu programu VS Code dla usługi IoT Edge jest platforma **amd64**, co oznacza, że dla modułu **CModule** ustawiono wersję obrazu Linux amd64. Zmień domyślną platformę na pasku stanu z **amd64** na **arm32v7**, jeśli taka jest architektura urządzenia usługi IoT Edge. 

      ![Aktualizowanie platformy obrazu modułu](./media/tutorial-c-module/image-platform.png)

   2. Sprawdź, czy szablon ma prawidłową nazwę modułu, a nie domyślną nazwę **SampleModule** zmienioną podczas tworzenia rozwiązania usługi IoT Edge.

   3. W sekcji **registryCredentials** przechowywane są poświadczenia rejestru platformy Docker, aby agent usługi IoT Edge mógł ściągnąć obraz modułu. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany przez usługę git. Jeśli jeszcze nie zostało to zrobione, dodaj swoje poświadczenia do pliku env.  

   4. Jeśli chcesz dowiedzieć się więcej na temat manifestów wdrożenia, zobacz artykuł [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md) (Dowiedz się, jak wdrażać moduły i ustanawiać trasy w usłudze IoT Edge).

1. Dodaj bliźniaczą reprezentację modułu CModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
       "CModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu CModule do szablonu wdrożenia](./media/tutorial-c-module/module-twin.png)

1. Zapisz plik **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

W poprzedniej sekcji utworzyliśmy rozwiązanie IoT Edge i dodać kod do CModule, która będzie filtrować wiadomości, gdzie temperatury zgłoszonych maszyny wykracza poza dopuszczalne limity. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz zintegrowany terminal programu VS Code, wybierając pozycję **View (Widok)** > **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code. Musisz zalogować się przy użyciu poświadczeń usługi Azure Container Registry, aby móc wypchnąć obraz modułu do rejestru.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z usługi Azure Container Registry w sekcji pierwszej. Możesz też pobrać je ponownie z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw generuje plik `deployment.json` w nowym folderze **config**. Informacje dla pliku deployment.json są zbierane ze zaktualizowanego pliku szablonu, pliku env używanego do przechowywania poświadczeń rejestru kontenerów oraz pliku module.json znajdującego się w folderze CModule.

Następnie program Visual Studio Code uruchamia w zintegrowanym terminalu dwa polecenia: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują moduł `CModule.dll` i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania.

Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku `module.json` w formacie **\<repozytorium\>:\<wersja\>-\<platforma\>**. W tym samouczku powinien wyglądać następująco: **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Jeśli podczas próby skompilowania i wypchnięcia modułu pojawia się błąd, sprawdź następujące rzeczy:
>* Czy do platformy Docker w programie Visual Studio Code zalogowano się przy użyciu poświadczeń z rejestru kontenerów? Te poświadczenia są inne niż te, których używasz do logowania się w witrynie Azure Portal.
>* Czy używasz właściwego repozytorium kontenerów? Otwórz plik **modules** > **cmodule** > **module.json** i znajdź pole **repository**. Repozytorium obrazów powinno wyglądać tak: **\<nazwa_rejestru\>.azurecr.io/cmodule**. 
>* Czy kompilujesz kontenery tego samego typu, co działające na maszynie deweloperskiej? Domyślnie w programie Visual Studio Code są używane kontenery amd64 systemu Linux. Jeśli na maszynie deweloperskiej działają kontenery arm32v7 systemu Linux, zaktualizuj platformę na niebieskim pasku stanu w dolnej części okna programu VS Code, aby była zgodna z platformą kontenerów. Modułów języka C nie można kompilować jako kontenerów systemu Windows. 

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Moduły można także wdrażać przy użyciu rozszerzenia Azure IoT Hub Toolkit (dawniej rozszerzenia Azure IoT Toolkit) dla programu Visual Studio Code. Masz już manifest wdrożenia przygotowany dla danego scenariusza — plik **deployment.json**. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

1. W palecie poleceń programu VS Code uruchom polecenie **Azure IoT Hub: Select IoT Hub**  (Azure IoT Hub: wybierz centrum IoT Hub).

2. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować.

3. W eksploratorze programu VS rozwiń sekcję **Urządzenia usługi Azure IoT Hub**.

4. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-c-module/create-deployment.png)

5. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

6. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **CModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

Na urządzeniu usługi IoT Edge można zobaczyć stan moduły wdrożenia za pomocą polecenia `iotedge list`. Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, moduł tempSensor i moduł niestandardowy utworzony w ramach tego samouczka. Uruchomienie wszystkich modułów może potrwać kilka minut, dlatego jeśli na początku wszystkie nie będą widoczne, uruchom polecenie ponownie.

Aby wyświetlić komunikaty generowane przez któryś moduł, użyj polecenia `iotedge logs <module name>`.

Komunikaty odbierane w centrum IoT można wyświetlać za pomocą programu Visual Studio Code.

1. Aby monitorować dane przychodzące do centrum IoT Hub, kliknij przycisk **...** i wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C) w palecie poleceń.
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz plik JSON, kliknij prawym przyciskiem myszy obszar edycji i wybierz opcję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, możesz zainstalować rozszerzenie [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla narzędzia VS Code i znaleźć uruchomione moduły lokalnie w eksploratorze platformy Docker. W menu kontekstowym kliknij opcję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [utworzyć moduł w języku C za pomocą rozszerzenia usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-c-module.md). Możesz teraz kontynuować pracę, korzystając z kolejnych samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server](tutorial-store-data-sql-server.md)

