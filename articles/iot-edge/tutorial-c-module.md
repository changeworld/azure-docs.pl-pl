---
title: Samouczek Tworzenie modułu C w systemie Linux — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge przy użyciu kodu C i wdrożyć ją urządzeniem z systemem Linux z IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: dcf15130b1b720277fcb6f551f1e19229a6c5e7c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239722"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Samouczek: Tworzenie modułu C IoT Edge dla urządzeń z systemem Linux

Tworzenie kodu C i wdrożyć ją urządzeniem z systemem Linux z usługą Azure IoT Edge, należy użyć programu Visual Studio Code. 

Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie modułu usługi IoT Edge w języku C przy użyciu programu Visual Studio Code
> * Tworzenie obrazu platformy Docker i publikowanie go w rejestrze kontenerów przy użyciu programu Visual Studio Code i platformy Docker
> * Wdrażanie modułu na urządzeniu usługi IoT Edge
> * Wyświetlanie wygenerowanych danych

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

Ten samouczek przedstawia sposób tworzenia modułu w **C** przy użyciu **programu Visual Studio Code**oraz jak wdrożyć ją **urządzenia z systemem Linux**. Jeśli tworzysz modułów dla urządzeń Windows przejdź do strony [Tworzenie modułu C IoT Edge dla urządzeń Windows](tutorial-c-module-windows.md) zamiast tego.

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów języka C z systemem Linux: 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **AMD64 systemu Linux** | ![Używanie programu VS Code dla modułów języka C w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Użyj programu VS dla modułów języka C w systemie Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **ARM32 systemu Linux** | ![Używanie programu VS Code dla modułów języka C w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Użyj programu VS dla modułów języka C w systemie Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano za pomocą poprzedniego samouczka, aby skonfigurować środowisko programowania do tworzenia aplikacji kontenera systemu Linux: [Twórz moduły usługi IoT Edge dla urządzeń z systemem Linux](tutorial-develop-for-linux.md). Przez wykonanie kroków tego samouczka, musisz mieć następujące wymagania wstępne spełnione: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [Linux urządzenia z usługą Azure IoT Edge](quickstart-linux.md)
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) skonfigurowano [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów systemu Linux.

Aby opracować moduł usługi IoT Edge w języku C, należy zainstalować następujące dodatkowe wymagania wstępne na komputerze deweloperskim: 

* [Rozszerzenie języka C/C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) dla programu Visual Studio Code.

## <a name="create-a-module-project"></a>Utwórz projekt modułu

Poniższe kroki umożliwiają utworzenie projektu modułu usługi IoT Edge dla języka C za pomocą programu Visual Studio Code i rozszerzenia Azure IoT Tools. Po utworzeniu szablonu projektu utworzone, dodawanie nowego kodu, aby moduł odfiltrowuje komunikaty na podstawie ich zgłoszonych właściwości. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Wybierz kolejno opcje **Widok** > **Paleta poleceń**, aby otworzyć paletę poleceń programu VS Code.

2. W palecie poleceń wpisz i uruchom polecenie **Azure: Sign in (Azure: zaloguj się)** , a następnie postępuj zgodnie z instrukcjami, aby zalogować się na swoim koncie platformy Azure. Jeśli już się zalogowano, można pominąć ten krok.

3. W palecie poleceń wpisz i uruchom polecenie **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Postępuj zgodnie z monitami wyświetlanymi na palecie poleceń, aby utworzyć rozwiązanie.

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz pozycję **C Module** (Moduł języka C). |
   | Podaj nazwę modułu | Nadaj modułowi nazwę **CModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/cmodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-c-module/repository.png)

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

Domyślny kod modułu odbiera komunikaty w kolejce wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Dodajmy trochę kodu dodatkowe, tak aby moduł przetwarza wiadomości na urządzeniach brzegowych przed przekazaniem ich do usługi IoT Hub. Zaktualizuj moduł analizuje dane dotyczące temperatury w każdej wiadomości, a tylko wysyła komunikat do Centrum IoT Hub, gdy temperatura przekroczy określony próg. 

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

1. Znajdź `CreateMessageInstance` funkcji w main.c. Zastąp następujący kod, który dodaje kilka wierszy, funkcje wewnętrzne if-else — instrukcja: 

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

   Nowe wiersze kodu w instrukcji else Dodaj nową właściwość do komunikatu, która oznacza komunikat jako alert. Ten kod etykiet wszystkie wiadomości jako alerty, ponieważ dodamy funkcje, które tylko wysyła komunikaty do usługi IoT Hub, jeśli raporty są wysokie temperatury. 

1. Zastąp całą funkcję `InputQueue1Callback` poniższym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Gdy wiadomość zostaje odebrana, sprawdza, czy zgłoszonych temperatura przekroczy wartość progową. Jeśli tak, przekazuje komunikat za pośrednictwem swojej kolejki wyjściowej. Jeśli nie, następnie ignoruje komunikat. 

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

1. Znajdź `SetupCallbacksForModule` funkcji. Zamień na następujący kod, który dodaje funkcję **else if** instrukcję, aby sprawdzić, czy bliźniaczą reprezentację modułu zostały zaktualizowane.

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

## <a name="build-and-push-your-module"></a>Zbuduj i Wypchnij modułu

W poprzedniej sekcji utworzyliśmy rozwiązanie IoT Edge i dodać kod do CModule, która będzie filtrować wiadomości, gdzie temperatury zgłoszonych maszyny wykracza poza dopuszczalne limity. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów.

1. Otwórz terminal programu VS Code, wybierając **widoku** > **terminalu**.

1. Zaloguj się do platformy Docker, wprowadzając następujące polecenie w terminalu. Zaloguj się przy użyciu nazwy użytkownika, hasło i serwer logowania z usługi Azure container registry. Możesz pobrać te wartości z **klucze dostępu** sekcji rejestru w witrynie Azure portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

2. W eksploratorze programu VS Code kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypchnij rozwiązanie usługi IoT Edge**.

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Do wdrożenia projektu modułu do urządzenia usługi IoT Edge przy użyciu Eksploratora Visual Studio Code i rozszerzenia narzędzia IoT platformy Azure. Masz już manifest wdrożenia, jest przygotowane do scenariusza **deployment.json** pliku w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona. 

1. W Eksploratorze programu Visual Studio Code rozwiń **Azure IoT Hub Devices** sekcję, aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**.

3. Wybierz plik **deployment.json** w folderze **config**, a następnie kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. Nie używaj pliku deployment.template.json.

4. Kliknij przycisk Odśwież. Powinien zostać wyświetlony nowy moduł **CModule** uruchomiony wraz z modułami **TempSensor**, **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione.

Możesz wyświetlić stan urządzenia usługi IoT Edge w sekcji **Azure IoT Hub Devices** (Urządzenia usługi Azure IoT Hub) w eksploratorze programu Visual Studio Code. Rozwiń szczegóły urządzenia, aby wyświetlić listę wdrożonych i uruchomionych modułów.

1. W Eksploratorze programu Visual Studio Code kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, a następnie wybierz **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**.

2. Wyświetl wiadomości otrzymywanych z usługi IoT Hub. Może potrwać trochę wiadomości zostanie dostarczona, ponieważ urządzenia usługi IoT Edge do odbierania jego nowego wdrożenia i uruchamiania wszystkich modułów. Następnie zmiany wprowadzone do kodu CModule poczekaj, aż temperatury maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Typ komunikatu dodaje także **alertu** na wszystkie komunikaty, w których progu temperatury. 

   ![Wyświetlanie wiadomości otrzymywanych z usługi IoT Hub](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj bliźniaczą reprezentację modułu

Użyliśmy CModule bliźniaczą reprezentację modułu w pliku manifestu wdrożenia, aby ustawić próg temperaturę w stopniach 25. Bliźniacza reprezentacja modułu można użyć w celu zmiany funkcji bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio Code rozwiń szczegóły w obszarze urządzenia usługi IoT Edge, aby wyświetlić uruchomione moduły. 

2. Kliknij prawym przyciskiem myszy **CModule** i wybierz **bliźniaczą reprezentację modułu edycji**. 

3. Znajdź **TemperatureThreshold** w odpowiednich właściwości. Zmień jej wartość temperatury nowe 5 stopni do 10 stopni wyższa niż najnowszy zgłoszonych temperatury. 

4. Zapisz plik bliźniaczej reprezentacji modułu.

5. Kliknij prawym przyciskiem myszy w dowolnym miejscu w bliźniaczej reprezentacji modułu do edycji okienko, a następnie wybierz **bliźniaczą reprezentację modułu aktualizacji**. 

5. Monitorowanie przychodzących komunikatów przesyłanych z chmury do urządzenia. Powinien zostać wyświetlony wiadomości, Zatrzymaj, aż do osiągnięcia progu temperatury nowe. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie można usunąć lokalnej konfiguracji i zasobów platformy Azure używane w tym artykule Aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułach, możesz dowiedzieć się więcej [tworzenia własnych modułów usługi IoT Edge](module-development.md) lub jak [opracowanie modułów przy użyciu programu Visual Studio Code](how-to-vs-code-develop-module.md). Można przejść do następnego samouczki, aby dowiedzieć się, jak usługa Azure IoT Edge mogą pomóc Ci wdrażanie usług w chmurze platformy Azure do przetwarzania i analizowania danych na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [uczenia maszynowego](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)

