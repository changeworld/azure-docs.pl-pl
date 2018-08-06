---
title: Samouczek usługi Azure IoT Edge dla języka C | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge za pomocą kodu języka C i wdrożyć go na urządzeniu brzegowym
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c9d1931f1b78bb19f5e321a19baca45265ea7ab4
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413165"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Samouczek: opracowywanie modułu usługi IoT Edge w języku C i wdrażanie go na urządzeniu symulowanym

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

* Jako urządzenia brzegowego możesz użyć maszyny deweloperskiej albo maszyny wirtualnej, postępując zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń z systemem [Linux](quickstart-linux.md) lub [Windows](quickstart.md).

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Standardowa na platformie Azure. 

Zasoby do programowania:

* [Program Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie języka C/C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) dla programu Visual Studio Code.
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) dla programu Visual Studio Code.
* [Program Docker CE](https://docs.docker.com/install/). 


## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów
W tym samouczku rozszerzenie usługi Azure IoT Edge dla programu VS Code zostanie użyte do zbudowania modułu i utworzenia **obrazu kontenera** na podstawie plików. Następnie ten obraz zostanie wypchnięty do **rejestru**, w którym obrazy są przechowywane i zarządzane. Na koniec obraz zostanie wdrożony z rejestru w celu uruchomienia na urządzeniu usługi IoT Edge.  

Na potrzeby tego samouczka możesz użyć dowolnego rejestru zgodnego z platformą Docker. Dwie popularne usługi rejestru Docker dostępne w chmurze to [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) i [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). W tym samouczku używana jest usługa Azure Container Registry. 

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Kontenery** > **Azure Container Registry**.
2. Nazwij rejestr, wybierz subskrypcję i grupę zasobów oraz ustaw jednostkę SKU na wartość **Podstawowa**. 
3. Wybierz pozycję **Utwórz**.
4. Po utworzeniu kontenera rejestru przejdź do niego i wybierz pozycję **Klucze dostępu**. 
5. Przełącz pozycję **Administrator** na wartość **Włącz**.
6. Skopiuj wartości w polach **Serwer logowania**, **Nazwa użytkownika** i **Hasło**. Użyjesz tych wartości w dalszej części samouczka podczas publikowania obrazu platformy Docker do rejestru oraz podczas dodawania poświadczeń rejestru do środowiska uruchomieniowego usługi Edge. 

## <a name="create-an-iot-edge-module-project"></a>Tworzenie projektu modułu usługi IoT Edge
W następujących krokach przedstawiono sposób tworzenia projektu modułu usługi IoT Edge bazującego na platformie .NET Core 2.0 przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge.
1. W programie Visual Studio Code wybierz kolejno pozycje **Widok** > **Zintegrowany terminal**, aby otworzyć zintegrowany terminal programu VS Code.
2. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code. 
3. W palecie poleceń wpisz i uruchom polecenie **Azure: zaloguj**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.
4. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. W palecie poleceń podaj następujące informacje, aby utworzyć rozwiązanie: 
   1. Wybierz folder, w którym chcesz utworzyć rozwiązanie. 
   2. Podaj nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**.
   3. Wybierz szablon modułu **C Module** (Moduł języka C). 
   4. Nadaj modułowi nazwę **CModule**. 
   5. Określ usługę Azure Container Registry utworzoną w poprzedniej sekcji jako repozytorium obrazów dla pierwszego modułu. Zastąp ciąg **localhost:5000** skopiowaną wartością serwera logowania. Ostatecznie ciąg będzie wyglądał następująco: **\<nazwa rejestru\>.azurecr.io/cmodule**.
 
4. W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Zawiera foldery **modules** i **.vscode**, plik szablonu manifestu wdrożenia oraz plik **env**. Domyślny kod modułu jest implementowany jako moduł potoku. 

5. Do filtrowania komunikatów w formacie JSON trzeba zaimportować bibliotekę JSON dla języka C. Możesz wybrać dowolną bibliotekę JSON lub napisać własną do analizowania danych JSON w module języka C. W poniższych krokach jako przykład używana jest biblioteka [Parson](https://github.com/kgabis/parson).
   1. Pobierz pliki **parson.c** i **parson.h** z [repozytorium Parson w usłudze Github](https://github.com/kgabis/parson). Skopiuj i wklej te dwa pliki do folderu **CModule**.
   2. Otwórz plik **modules** > **CModule** > **CMakeLists.txt**. Dodaj poniższe wiersze, aby zaimportować bibliotekę parson jako my_parson.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. W sekcji `target_link_libraries` w pliku **CMakeLists.txt** dodaj pozycję `my_parson`.
   4. Otwórz plik **modules** > **CModule** > **main.c**. Na dole sekcji include dodaj poniższy kod, aby dołączyć plik `parson.h` na potrzeby obsługi kodu JSON:

      ```c
      #include "parson.h"
      ```

6. Dodaj zmienną globalną `temperatureThreshold` po sekcji include. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub. 

    ```c
    static double temperatureThreshold = 25;
    ```

7. Zastąp całą funkcję `CreateMessageInstance` poniższym kodem. Ta funkcja przydziela kontekst dla wywołania zwrotnego. 

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

8. Zastąp całą funkcję `InputQueue1Callback` poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. 

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

9. Dodaj funkcję `moduleTwinCallback`. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

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

10. Dodaj nowe wywołanie zwrotne dla elementu `moduleTwinCallback` w funkcji `SetupCallbacksForModule`. Zastąp całą funkcję `SetupCallbacksForModule` poniższym kodem.

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

11. Zapisz ten plik.

## <a name="build-your-iot-edge-solution"></a>Kompilowanie rozwiązania usługi IoT Edge

W poprzedniej sekcji utworzono rozwiązanie usługi IoT Edge i dodano kod do modułu CModule, filtrującego komunikaty, w których zgłoszona temperatura maszyny jest poniżej akceptowalnego progu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Zaloguj się w aplikacji Docker, wprowadzając następujące polecenie w zintegrowanym terminalu programu Visual Studio Code, aby móc wypchnąć obraz modułu do usługi ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Użyj nazwy użytkownika, hasła i serwera logowania skopiowanych z usługi Azure Container Registry w sekcji pierwszej. Możesz też pobrać je ponownie z sekcji **Klucze dostępu** rejestru w witrynie Azure Portal.

2. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge. Ten plik wydaje agentowi `$edgeAgent` polecenie wdrożenia dwóch modułów: **tempSensor** i **CModule**. Wartość `CModule.image` jest ustawiona na wersję obrazu Linux amd64. Aby dowiedzieć się więcej na temat manifestów wdrożenia, zobacz [Jak używać modułów usługi IoT Edge, konfigurować je i używać ich ponownie](module-composition.md).

3. Plik **deployment.template.json** zawiera sekcję **registryCredentials**, w której są przechowywane poświadczenia rejestru platformy Docker. Rzeczywiste pary nazw użytkownika i haseł są przechowywane w pliku env, który jest ignorowany w usłudze Git.

4. Dodaj bliźniaczą reprezentację modułu CModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`: 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Zapisz ten plik.
5. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj rozwiązanie usługi IoT Edge**. 

Po wybraniu polecenia kompilowania rozwiązania w programie Visual Studio Code program najpierw pobiera informacje z szablonu wdrożenia i generuje plik `deployment.json` w nowym folderze **config**. Następnie uruchamia dwa polecenia w zintegrowanym terminalu: `docker build` i `docker push`. Te dwa polecenia kompilują kod, konteneryzują moduł `CModule.dll` i wypychają go do rejestru kontenerów określonego podczas inicjowania rozwiązania. 

Pełny adres obrazu kontenera możesz wyświetlić za pomocą tagu w zintegrowanym terminalu programu VS Code. Adres obrazu składa się z informacji z pliku `module.json` w formacie **\<repozytorium\>:\<wersja\>-\<platforma\>**. W tym samouczku powinien wyglądać następująco: **registryname.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Wdrażanie i uruchamianie rozwiązania

1. Skonfiguruj rozszerzenie Azure IoT Toolkit za pomocą parametrów połączenia na potrzeby centrum IoT Hub: 
    1. Otwórz eksploratora programu VS Code, wybierając pozycję **Widok** > **Eksplorator**. 
    2. W eksploratorze kliknij pozycję **URZĄDZENIA USŁUGI AZURE IOT HUB**, a następnie kliknij pozycję **...**. Kliknij pozycję **Wybierz centrum IoT Hub**. Postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure, a następnie wybierz centrum IoT Hub. 
       Pamiętaj, że możesz je również skonfigurować, klikając pozycję **Ustaw parametry połączenia centrum IoT Hub**. Wprowadź parametry połączenia dla centrum IoT Hub, z którym nawiązuje połączenie urządzenie usługi IoT Edge, w oknie podręcznym.

2. W eksploratorze urządzeń usługi Azure IoT Hub kliknij prawym przyciskiem myszy urządzenie usługi IoT Edge, a następnie kliknij opcję **Utwórz wdrożenie dla urządzenia usługi IoT Edge**. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**.

3. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **CModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**. 

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować dane przychodzące do centrum IoT Hub, kliknij przycisk **...** i wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
2. Aby monitorować komunikat D2C dla określonego urządzenia, kliknij prawym przyciskiem myszy to urządzenie na liście, a następnie wybierz opcję **Rozpocznij monitorowanie komunikatów D2C**.
3. Aby zatrzymać monitorowanie danych, uruchom polecenie **Azure IoT Hub: zatrzymaj monitorowanie komunikatu D2C** w palecie poleceń. 
4. Aby wyświetlić lub zaktualizować bliźniaczą reprezentację modułu, kliknij prawym przyciskiem myszy moduł na liście, a następnie wybierz opcję **Edytuj bliźniaczą reprezentację modułu**. Aby zaktualizować bliźniaczą reprezentację modułu, zapisz plik JSON, kliknij prawym przyciskiem myszy obszar edycji i wybierz opcję **Zaktualizuj bliźniaczą reprezentację modułu**.
5. Aby wyświetlić dzienniki platformy Docker, możesz zainstalować rozszerzenie [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla narzędzia VS Code i znaleźć uruchomione moduły lokalnie w eksploratorze platformy Docker. W menu kontekstowym kliknij opcję **Pokaż dzienniki**, aby wyświetlić je w zintegrowanym terminalu.
 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować już utworzone zasoby oraz konfiguracje i użyć ich ponownie.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie zasobów i grupy zasobów platformy Azure jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć tylko centrum IoT Hub, uruchom następujące polecenie, używając nazwy centrum i nazwy grupy zasobów:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Aby usunąć całą grupę zasobów na podstawie nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów zawierającej usługę IoT Hub. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.



## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułów, możesz dowiedzieć się więcej na temat tego, jak [utworzyć moduł w języku C za pomocą rozszerzenia usługi Azure IoT Edge dla programu Visual Studio Code](how-to-develop-c-module.md). Możesz teraz kontynuować pracę, korzystając z kolejnych samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Store data at the edge with SQL Server databases (Przechowywanie danych na brzegu sieci przy użyciu baz danych programu SQL Server)](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
