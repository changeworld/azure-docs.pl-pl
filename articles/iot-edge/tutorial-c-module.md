---
title: Samouczek opracowywać moduł C dla systemu Linux — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł IoT Edge z kodem C i wdrożyć go na urządzeniu z systemem Linux z uruchomiona aplikacją IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/07/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4236b7ad3b15d1bb58a146f5905d226e0c3833d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760951"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Samouczek: Opracowanie modułu C IoT Edge dla urządzeń z systemem Linux

Użyj programu Visual Studio Code, aby opracować kod C i wdrożyć go na urządzeniu z systemem Linux z uruchomiona usługą Azure IoT Edge.

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie modułu usługi IoT Edge w języku C przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu Visual Studio Code i platformy Docker
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

W tym samouczku pokazano, jak opracować moduł w **języku C** przy użyciu programu Visual **Studio Code**i jak wdrożyć go na urządzeniu z **systemem Linux.** Jeśli tworzysz moduły dla urządzeń z systemem Windows, przejdź do [opracowania modułu C IoT Edge dla urządzeń z systemem Windows.](tutorial-c-module-windows.md)

Użyj poniższej tabeli, aby zrozumieć opcje tworzenia i wdrażania modułów C w systemie Linux:

| C | Visual Studio Code | Visual Studio |
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Użyj modułów VS Code for C w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Użyj modułów VS dla C w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Użyj modułów VS Code for C w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Użyj modułów VS dla C w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy przejść przez poprzedni samouczek, aby skonfigurować środowisko programistyczne dla tworzenia kontenerów Systemu Linux: [Opracowanie modułów IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Wypełniając ten samouczek, należy mieć następujące wymagania wstępne w miejscu:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* [Urządzenie z systemem Linux z usługą Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenerów, taki jak [usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowany za pomocą [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowany do uruchamiania kontenerów systemu Linux.

Aby opracować moduł usługi IoT Edge w języku C, zainstaluj następujące dodatkowe wymagania wstępne na komputerze deweloperskim:

* [Rozszerzenie języka C/C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) dla programu Visual Studio Code.

## <a name="create-a-module-project"></a>Tworzenie projektu modułu

Poniższe kroki tworzą projekt modułu usługi IoT Edge dla języka C przy użyciu programu Visual Studio Code i rozszerzenia Narzędzia Azure IoT. Po utworzeniu szablonu projektu dodaj nowy kod, aby moduł odfiltrowywany wiadomości na podstawie ich zgłoszonych właściwości.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Wybierz **pozycję Wyświetl** > **paletę poleceń,** aby otworzyć paletę poleceń VS Code.

2. W palecie poleceń wpisz i uruchom polecenie **Azure: zaloguj**, a następnie postępuj zgodnie z instrukcjami, aby zalogować się na koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: nowe rozwiązanie usługi IoT Edge**. Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz **moduł C**. |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/cmodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge.

1. W eksploratorze programu VS Code otwórz plik env.
2. Zaktualizuj pola, używając **nazwy użytkownika** i **hasła**, które zostały skopiowane z usługi Azure Container Registry.
3. Zapisz ten plik.

### <a name="select-your-target-architecture"></a>Wybierz architekturę docelową

Obecnie Visual Studio Code może tworzyć moduły C dla urządzeń z systemem Linux AMD64 i Linux ARM32v7. Należy wybrać architekturę, która jest przeznaczona dla każdego rozwiązania, ponieważ kontener jest zbudowany i uruchamiany inaczej dla każdego typu architektury. Domyślnie jest Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj **usługę Azure IoT Edge: Set Default Target Platform for Edge Solution**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, więc zachowamy domyślny **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizowanie modułu przy użyciu kodu niestandardowego

Domyślny kod modułu odbiera wiadomości w kolejce wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Dodajmy dodatkowy kod, aby moduł przetwarzał wiadomości na krawędzi przed przekazaniem ich do usługi IoT Hub. Zaktualizuj moduł tak, aby analizował dane temperatury w każdej wiadomości i wysyła komunikat do usługi IoT Hub tylko wtedy, gdy temperatura przekracza określony próg.

1. Dane z czujnika w tym scenariuszu są dostępne w formacie JSON. Aby filtrować komunikaty w formacie JSON, należy zaimportować bibliotekę JSON dla języka C. W tym samouczku używana jest biblioteka Parson.

   1. Pobierz [repozytorium Parson GitHub](https://github.com/kgabis/parson). Skopiuj pliki **parson.c** i **parson.h** do folderu **CModule**.

   2. Otwórz **moduły** > **CModule** > **CMakeLists.txt**. Na początku zaimportuj pliki biblioteki Parson jako bibliotekę o nazwie **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Dodaj `my_parson` do listy bibliotek w **funkcji target_link_libraries** CMakeLists.txt.

   4. Zapisz plik **CMakeLists.txt**.

   5. Otwórz **moduły** > **CModule** > **main.c**. U dołu listy instrukcji include dodaj nową, `parson.h` aby uwzględnić obsługę JSON:

      ```c
      #include "parson.h"
      ```

1. W pliku **main.c** dodaj zmienną globalną o nazwie `temperatureThreshold` po sekcji include. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Znajdź `CreateMessageInstance` funkcję w main.c. Zastąp wewnętrzną instrukcję if-else następującym kodem, który dodaje kilka wierszy funkcjonalności:

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

1. Zastąp całą funkcję `InputQueue1Callback` poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Po odebraniu wiadomości sprawdza, czy podnajemna temperatura przekracza próg. Jeśli tak, następnie przekazuje wiadomość za pośrednictwem kolejki wyjściowej. Jeśli nie, to ignoruje komunikat.

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

1. Znajdź `SetupCallbacksForModule` tę funkcję. Zastąp funkcję następującym kodem, który dodaje **instrukcję else if,** aby sprawdzić, czy bliźniacza reprezentacja modułu została zaktualizowana.

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

1. Zapisz plik main.c.

1. W eksploratorze programu VS Code otwórz plik **deployment.template.json** w obszarze roboczym rozwiązania usługi IoT Edge.

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

## <a name="build-and-push-your-module"></a>Zbuduj i wypchnij swój moduł

W poprzedniej sekcji utworzono rozwiązanie IoT Edge i dodano kod do modułu CModule, który odfiltruje komunikaty, w których podana temperatura maszyny mieści się w dopuszczalnych granicach. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz terminal VS Code, wybierając **pozycję View** > **Terminal**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasła i serwera logowania z rejestru kontenerów platformy Azure. Te wartości można pobrać z sekcji **Klucze dostępu** rejestru w witrynie Azure portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Użyj Eksploratora kodu programu Visual Studio i rozszerzenia Narzędzia Azure IoT, aby wdrożyć projekt modułu na urządzeniu usługi IoT Edge. Masz już manifest wdrożenia przygotowany dla twojego scenariusza, plik **deployment.json** w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. W Eksploratorze kodu programu Visual Studio rozwiń sekcję **Urządzenia usługi Azure IoT Hub,** aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy **moduł CModule** uruchomiony wraz z **modułem SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze kodu programu Visual Studio kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

2. Wyświetlanie wiadomości docierających do Centrum IoT. Może upłynąć trochę czasu, aby komunikaty dotarły, ponieważ urządzenie usługi IoT Edge musi otrzymać nowe wdrożenie i uruchomić wszystkie moduły. Następnie zmiany wprowadzone w kodzie CModule czekać, aż temperatura maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Dodaje również typ komunikatu **Alert** do wszystkich komunikatów, które osiągają ten próg temperatury.

   ![Wyświetlanie wiadomości docierających do Usługi IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edycja bliźniaczej reprezentacji modułu

Użyliśmy bliźniaczej reprezentacji modułu CModule w manifeście wdrażania, aby ustawić próg temperatury na poziomie 25 stopni. Bliźniaczej reprezentacji modułu można zmienić funkcjonalność bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia IoT Edge, aby wyświetlić uruchomione moduły.

2. Kliknij prawym przyciskiem myszy **polecenie CModule** i wybierz polecenie **Edytuj bliźniaczą modułu**.

3. Znajdź **TemperatureThreshold** w żądanych właściwościach. Zmień jego wartość na nową temperaturę o 5 stopni do 10 stopni wyższą niż ostatnio zgłoszona temperatura.

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy dowolne miejsce w okienku edycji bliźniaczej reprezentacji modułu i wybierz opcję **Aktualizuj bliźniaczej reprezentacji modułu**.

6. Monitorowanie przychodzących komunikatów z urządzenia do chmury. Komunikaty powinny zostać zatrzymane do momentu osiągnięcia nowego progu temperatury.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie można usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały użyte w tym artykule, aby uniknąć opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy będziesz gotowy do tworzenia własnych modułów, możesz dowiedzieć się więcej o [tworzeniu własnych modułów IoT Edge](module-development.md) lub o tym, jak tworzyć [moduły za pomocą programu Visual Studio Code.](how-to-vs-code-develop-module.md) Przykłady modułów IoT Edge, w tym symulowanego modułu temperatury, zobacz [przykłady modułów IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules) i [próbki SDK IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).

Możesz przejść do następnych samouczków, aby dowiedzieć się, jak usługa Azure IoT Edge może pomóc we wdrażaniu usług w chmurze platformy Azure w celu przetwarzania i analizowania danych na serwerze brzegowym.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Strumienia Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
