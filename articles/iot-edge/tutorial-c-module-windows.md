---
title: Samouczek opracowywać moduł C dla systemu Windows — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge z kodem C i wdrożyć go na urządzeniu z systemem Windows z uruchomiona aplikacją IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 09d039801107a44df4f3bf3745a1e074e6d708b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760968"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Samouczek: Opracowanie modułu C IoT Edge dla urządzeń z systemem Windows

Za pomocą programu Visual Studio opracować kod języka C i wdrożyć go na urządzeniu z systemem Windows z uruchomiona usługą Azure IoT Edge.

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Użyj programu Visual Studio, aby utworzyć moduł IoT Edge, który jest oparty na SDK C.
> * Użyj programu Visual Studio i platformy Docker, aby utworzyć obraz platformy Docker i opublikować go w rejestrze.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku pokazano, jak opracować moduł w **języku C** przy użyciu programu Visual **Studio 2019** i wdrożyć go na **urządzeniu z systemem Windows.** Jeśli tworzysz moduły dla urządzeń z systemem Linux, przejdź do [opracowania modułu C IoT Edge dla urządzeń z systemem Linux.](tutorial-c-module.md)

Poniższa tabela służy do zapoznania się z opcjami tworzenia i wdrażania modułów C na urządzeniach z systemem Windows:

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Tworzenie modułów C dla programu WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy przejść przez poprzedni samouczek, aby skonfigurować środowisko programistyczne dla tworzenia kontenerów systemu Windows: [Opracowanie modułów IoT Edge dla urządzeń z systemem Windows](tutorial-develop-for-windows.md). Po ukończeniu tego samouczka należy mieć następujące wymagania wstępne:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Windows z usługą Azure IoT Edge](quickstart.md).
* Rejestr kontenerów, taki jak [usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) skonfigurowany z [rozszerzeniem Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Pulpit platformy Docker](https://docs.docker.com/docker-for-windows/install/) skonfigurowany do uruchamiania kontenerów systemu Windows.
* Zainstaluj zestaw SDK języka C usługi Azure dla systemu Windows x64 za pośrednictwem wersji vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Jeśli używasz programu Visual Studio 2017 (wersja 15.7 lub nowsza), pobierz i zainstaluj [narzędzia Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu VS 2017 z witryny Visual Studio marketplace

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt modułu usługi IoT Edge, który jest oparty na SDK C przy użyciu programu Visual Studio i rozszerzenia Azure IoT Edge Tools. Po utworzeniu szablonu projektu dodaj nowy kod, aby moduł odfiltrowywany wiadomości na podstawie ich zgłoszonych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Uruchom program Visual Studio 2019 i wybierz pozycję **Utwórz nowy projekt**.

2. Wyszukaj **usługę IoT Edge** i wybierz projekt **usługi Azure IoT Edge (Windows amd64).** Kliknij przycisk **alej**.

   ![Tworzenie nowego projektu usługi Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. Zmień nazwę projektu i rozwiązania na coś opisowego jak **CTutorialApp**. Kliknij **przycisk Utwórz,** aby utworzyć projekt.

   ![Konfigurowanie nowego projektu usługi Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. Skonfiguruj projekt przy następujących wartościach:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybieranie szablonu | Wybierz **moduł C**. |
   | Nazwa projektu modułu | Nadaj modułowi nazwę **CModule**. |
   | Repozytorium obrazów platformy Docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniony z wartości nazwy projektu modułu. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/cmodule. |

   ![Konfigurowanie projektu dla urządzenia docelowego, typu modułu i rejestru kontenerów](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Wybierz **pozycję Dodaj,** aby utworzyć projekt.

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrażania udostępnia poświadczenia rejestru kontenerów ze środowiska wykonawczego usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z sekcji **Klucze programu Access** rejestru kontenerów platformy Azure.

1. W Eksploratorze rozwiązań programu Visual Studio otwórz plik **deployment.template.json.**

2. Znajdź **właściwość registryCredentials** w $edgeAgent żądanych właściwościach. Powinien mieć adres rejestru automatycznie wypełniony z informacji podanych podczas tworzenia projektu. Pola nazwy użytkownika i hasła powinny zawierać nazwy zmiennych. Przykład:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otwórz plik **.env** w rozwiązaniu modułu. (Jest domyślnie ukryty w Eksploratorze rozwiązań, więc może być konieczne wybranie przycisku **Pokaż wszystkie pliki,** aby go wyświetlić). Plik .env powinien zawierać te same zmienne nazwy użytkownika i hasła, które zostały 2000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000

4. Dodaj wartości **nazwy użytkownika** i **hasła** z rejestru kontenerów platformy Azure.

5. Zapisz zmiany w pliku env.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Domyślny kod modułu odbiera wiadomości w kolejce wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Dodajmy dodatkowy kod, aby moduł przetwarzał wiadomości na krawędzi przed przekazaniem ich do usługi IoT Hub. Zaktualizuj moduł tak, aby analizował dane temperatury w każdej wiadomości i wysyła komunikat do usługi IoT Hub tylko wtedy, gdy temperatura przekracza określony próg.

1. Dane z czujnika w tym scenariuszu są dostępne w formacie JSON. Aby filtrować komunikaty w formacie JSON, należy zaimportować bibliotekę JSON dla języka C. W tym samouczku używana jest biblioteka Parson.

   1. Pobierz [repozytorium Parson GitHub](https://github.com/kgabis/parson). Skopiuj pliki **parson.c** i **parson.h** do projektu **CModule.**

   2. W programie Visual Studio otwórz plik **CMakeLists.txt** z folderu projektu CModule. Na początku zaimportuj pliki biblioteki Parson jako bibliotekę o nazwie **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Dodaj `my_parson` do listy bibliotek w sekcji **target_link_libraries** pliku CMakeLists.txt.

   4. Zapisz plik **CMakeLists.txt**.

   5. Otwórz **CModule** > **main.c**. U dołu listy instrukcji include dodaj nową, `parson.h` aby uwzględnić obsługę JSON:

      ```c
      #include "parson.h"
      ```

2. W pliku **main.c** dodaj zmienną `temperatureThreshold` globalną o nazwie obok zmiennej messagesReceivedByInput1Queue. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Znajdź `CreateMessageInstance` funkcję w main.c. Zastąp wewnętrzną instrukcję if-else następującym kodem, który dodaje kilka wierszy funkcjonalności:

   ```c
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
   ```

   Nowe wiersze kodu w instrukcji else dodać nową właściwość do wiadomości, która etykiety wiadomości jako alert. Ten kod oznacza wszystkie komunikaty jako alerty, ponieważ dodamy funkcje, które wysyłają wiadomości do Usługi IoT Hub tylko wtedy, gdy zgłaszają wysokie temperatury.

4. Znajdź `InputQueue1Callback` funkcję i zastąp całą funkcję następującym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Po odebraniu wiadomości sprawdza, czy podnajemna temperatura przekracza próg. Jeśli tak, następnie przekazuje wiadomość za pośrednictwem kolejki wyjściowej. Jeśli nie, to ignoruje komunikat.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Dodaj funkcję `moduleTwinCallback`. Ta metoda odbiera aktualizacje odpowiednich właściwości z bliźniaczej reprezentacji modułu i aktualizuje zmienną **temperatureThreshold**, aby ją dopasować. Wszystkie moduły mają swoje bliźniacze reprezentacje, dzięki czemu można skonfigurować kod działający wewnątrz modułu bezpośrednio z poziomu chmury.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

6. Znajdź `SetupCallbacksForModule` tę funkcję. Zastąp funkcję następującym kodem, który dodaje **instrukcję else if,** aby sprawdzić, czy bliźniacza reprezentacja modułu została zaktualizowana.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Zapisz plik main.c.

8. Otwórz plik **deployment.template.json.**

9. Dodaj bliźniaczą reprezentację modułu CModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu CModule do szablonu wdrożenia](./media/tutorial-c-module-windows/module-twin.png)

10. Zapisz plik **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Zbuduj i wypchnij swój moduł

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do **modułu CModule,** aby odfiltrować komunikaty, w których podana temperatura maszyny jest poniżej dopuszczalnego progu. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Użyj następującego polecenia, aby zalogować się do platformy Docker na komputerze deweloperskim. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z sekcji **Klucze dostępu** rejestru w witrynie Azure portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu, który chcesz utworzyć. Domyślna nazwa to **AzureIotEdgeApp1,** a ponieważ budujesz moduł Windows, rozszerzenie powinno być **Windows.Amd64**.

3. Wybierz **opcję Zbuduj i wypychaj moduły brzegowe IoT**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Użyj Eksploratora chmury programu Visual Studio i rozszerzenia Azure IoT Edge Tools, aby wdrożyć projekt modułu na urządzeniu usługi IoT Edge. Masz już manifest wdrożenia przygotowany dla twojego scenariusza, plik **deployment.json** w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. W Eksploratorze chmury programu Visual Studio rozwiń zasoby, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, które ma zostać odebrane w celu wdrożenia.

3. Wybierz **pozycję Utwórz wdrożenie**.

4. W eksploratorze plików wybierz plik **deployment.windows-amd64** w folderze konfiguracji rozwiązania.

5. Odśwież Eksploratora chmury, aby wyświetlić wdrożone moduły wymienione w urządzeniu.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Za pomocą rozszerzenia Narzędzia brzegowe IoT można wyświetlać wiadomości po ich dotarcie do centrum IoT Hub.

1. W Eksploratorze chmury programu Visual Studio wybierz nazwę urządzenia usługi IoT Edge.

2. Na liście **Akcje** wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Wyświetlanie wiadomości docierających do Centrum IoT. Może upłynąć trochę czasu, aby komunikaty dotarły, ponieważ urządzenie usługi IoT Edge musi otrzymać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule czekać, aż temperatura maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Dodaje również typ komunikatu **Alert** do wszystkich komunikatów, które osiągają ten próg temperatury.

   ![Wyświetlanie wiadomości docierających do Usługi IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edycja bliźniaczej reprezentacji modułu

Użyliśmy modułu CModule twin, aby ustawić próg temperatury na 25 stopni. Bliźniaczej reprezentacji modułu można zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio otwórz plik **deployment.windows-amd64.json.** (Nie plik deployment.template. Jeśli manifest wdrożenia nie jest widoczny w pliku konfiguracyjnym w Eksploratorze rozwiązań, wybierz ikonę **Pokaż wszystkie pliki** na pasku narzędzi eksploratora).

2. Znajdź CModule twin i zmienić wartość **temperatureThreshold** parametr do nowej temperatury 5 stopni do 10 stopni wyższe niż ostatnio zgłaszane temperatury.

3. Zapisz plik **deployment.windows-amd64.json.**

4. Wykonaj ponownie kroki wdrażania, aby zastosować zaktualizowany manifest wdrażania na urządzeniu.

5. Monitorowanie przychodzących komunikatów z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie można usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy będziesz gotowy do tworzenia własnych modułów, możesz dowiedzieć się więcej o [tworzeniu własnych modułów IoT Edge](module-development.md) lub o tym, jak tworzyć [moduły za pomocą programu Visual Studio.](how-to-visual-studio-develop-module.md) Przykłady modułów IoT Edge, w tym symulowanego modułu temperatury, zobacz [przykłady modułów IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules) i [próbki SDK IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).

Możesz przejść do następnych samouczków, aby dowiedzieć się, jak usługa Azure IoT Edge może pomóc we wdrażaniu usług w chmurze platformy Azure w celu przetwarzania i analizowania danych na serwerze brzegowym.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Strumienia Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
