---
title: Samouczek Tworzenie modułu C dla Windows - usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku pokazano, jak utworzyć moduł usługi IoT Edge przy użyciu kodu C i wdrożyć ją na urządzenie Windows z usługi IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 93910fd0baeace9da474073960dbdb83251a1a63
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576558"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Samouczek: Tworzenie modułu C IoT Edge dla urządzeń Windows

Tworzenie kodu C i wdrożyć ją na urządzeniu Windows z usługą Azure IoT Edge, należy użyć programu Visual Studio. 

Moduły usługi Azure IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. W tym samouczku przedstawiono sposób tworzenia i wdrażania modułu usługi IoT Edge, w którym są filtrowane dane czujnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:    

> [!div class="checklist"]
> * Użyj programu Visual Studio, aby utworzyć moduł usługi IoT Edge, która zależy od zestawu SDK programu .NET Core 2.1.
> * Aby utworzyć obraz platformy Docker i opublikuj go do rejestru, należy użyć programu Visual Studio i platformy Docker.
> * Wdrażanie modułu na urządzeniu usługi IoT Edge.
> * Wyświetlanie wygenerowanych danych.

Utworzony w tym samouczku moduł usługi IoT Edge filtruje dane temperatury generowane przez urządzenie. Komunikaty są wysyłane tylko wtedy, gdy temperatura przekroczy określony próg. Ten typ analizy brzegowej pomaga zmniejszyć ilość danych przekazywanych do chmury i w niej przechowywanych. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Zakres rozwiązania

Ten samouczek przedstawia sposób tworzenia modułu w **C** przy użyciu **programu Visual Studio 2017**oraz jak wdrożyć ją **urządzenia Windows**. Jeśli tworzysz modułów dla urządzeń z systemem Linux, przejdź do strony [Tworzenie modułu C IoT Edge dla urządzeń z systemem Linux](tutorial-c-module.md) zamiast tego. 

Skorzystaj z poniższej tabeli, aby poznać opcje tworzenia i wdrażania modułów C na urządzeniach Windows: 

| C | Visual Studio Code | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Tworzenie modułów języka C dla WinAMD64 w programie Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, powinien wykonano za pomocą poprzedniego samouczka, aby skonfigurować środowisko projektowe dla programowania kontenerów Windows: [Twórz moduły usługi IoT Edge dla urządzeń Windows](tutorial-develop-for-windows.md). Po ukończeniu tego samouczka, powinien mieć następujące wymagania wstępne spełnione: 

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* A [urządzenia Windows z usługą Azure IoT Edge](quickstart.md).
* Rejestr kontenera, takiej jak [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Program Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), wersji 15.7 lub nowszej, skonfigurowano [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) rozszerzenia.
* [Docker CE](https://docs.docker.com/install/) skonfigurowane do uruchamiania kontenerów Windows.
* Zestaw SDK usługi Azure IoT dla języka C. 

## <a name="create-a-module-project"></a>Utwórz projekt modułu

Poniższe kroki umożliwiają utworzenie projektu modułu usługi IoT Edge, która opiera się na zestaw .NET Core 2.0 SDK przy użyciu programu Visual Studio i rozszerzenia usługi Azure IoT Edge narzędzia. Po utworzeniu szablonu projektu utworzone, dodawanie nowego kodu, aby moduł odfiltrowuje komunikaty na podstawie ich zgłoszonych właściwości. 

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

Utwórz szablon rozwiązania języka C, który można dostosować przy użyciu własnego kodu.

1. Uruchom program Visual Studio jako administrator.

2. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**. 

3. W nowym oknie projektu, wybierz **usługi Azure IoT** typ projektu, a następnie wybierz **usługi Azure IoT Edge** projektu. Zmień nazwę projektu i rozwiązania do opisu coś podobnego **CTutorialApp**. Wybierz przycisk **OK**, aby utworzyć projekt. 

   ![Utwórz nowy projekt usługi Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

4. W oknie aplikacji IoT Edge i moduł należy skonfigurować projekt z następującymi wartościami: 

   | Pole | Wartość |
   | ----- | ----- |
   | Platforma aplikacji | Usuń zaznaczenie pola wyboru **Linux Amd64**i sprawdź **WindowsAmd64**. |
   | Wybierz szablon | Wybierz **modułu C**. | 
   | Nazwa projektu modułu | Nadaj modułowi nazwę **CModule**. | 
   | Repozytorium obrazów platformy docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wypełniana wstępnie z wartości Nazwa projektu modułu. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów wygląda następująco: \<nazwa rejestru\>.azurecr.io/cmodule. |

   ![Konfigurowanie projektu dla urządzeń docelowych, typ modułu i container registry](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Wybierz **OK** Aby zastosować zmiany. 

### <a name="add-your-registry-credentials"></a>Dodawanie poświadczeń rejestru

Manifest wdrażania udziałów poświadczenia dla rejestru kontenerów za pomocą środowiska uruchomieniowego usługi IoT Edge. Środowisko uruchomieniowe wymaga tych poświadczeń do ściągnięcia prywatnych obrazów na urządzenie usługi IoT Edge. Użyj poświadczeń z **klucze dostępu** sekcji usługi Azure container registry. 

1. W Eksploratorze rozwiązań programu Visual Studio Otwórz **deployment.template.json** pliku. 

2. Znajdź **registryCredentials** właściwość $edgeAgent żądane właściwości. 

3. Aktualizacja właściwości przy użyciu swoich poświadczeń, zgodnie z następującym formacie: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 


1. The data from the sensor in this scenario comes in JSON format. To filter messages in JSON format, import a JSON library for C. This tutorial uses Parson.

   1. Download the [Parson GitHub repository](https://github.com/kgabis/parson). Copy the **parson.c** and **parson.h** files into the **CModule** project.

   2. In Visual Studio, open the **CMakeLists.txt** file from the CModule project folder. At the top of the file, import the Parson files as a library called **my_parson**.

      ```
      add_library (my_parson parson.c parson.h)
      ```

   3. Add **my_parson** to the list of libraries in the **target_link_libraries** section of the CMakeLists.txt file.

   4. Save the **CMakeLists.txt** file.

   5. Open **CModule** > **main.c**. At the bottom of the list of include statements, add a new one to include `parson.h` for JSON support:

      ```c
      #include "parson.h"
      ```

2.  W **main.c** Dodaj zmienną globalną o nazwie `temperatureThreshold` obok zmiennej messagesReceivedByInput1Queue. Za pomocą tej zmiennej jest ustawiana wartość zmierzonej temperatury, której przekroczenie spowoduje wysłanie danych do usługi IoT Hub.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Znajdź `CreateMessageInstance` funkcji w main.c. Zastąp następujący kod, który dodaje kilka wierszy, funkcje wewnętrzne if-else — instrukcja: 

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

4. Znajdź `InputQueue1Callback` funkcji i Zastąp cały funkcji następującym kodem. Ta funkcja implementuje rzeczywisty filtr komunikatów. Gdy wiadomość zostaje odebrana, sprawdza, czy zgłoszonych temperatura przekroczy wartość progową. Jeśli tak, przekazuje komunikat za pośrednictwem swojej kolejki wyjściowej. Jeśli nie, następnie ignoruje komunikat. 

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

6. Znajdź `SetupCallbacksForModule` funkcji. Zamień na następujący kod, który dodaje funkcję **else if** instrukcję, aby sprawdzić, czy bliźniaczą reprezentację modułu zostały zaktualizowane. 

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

7. Zapisz plik main.c.

8. Otwórz **deployment.template.json** pliku. 

9. Dodaj bliźniaczą reprezentację modułu CModule do manifestu wdrożenia. Wstaw następującą zawartość do pliku JSON na końcu sekcji `moduleContent`, po bliźniaczej reprezentacji modułu `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dodawanie bliźniaczej reprezentacji modułu CModule do szablonu wdrożenia](./media/tutorial-c-module-windows/module-twin.png)

1. Zapisz plik **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Zbuduj i Wypchnij modułu

W poprzedniej sekcji, tworzenia rozwiązania usługi IoT Edge i dodano kod w celu **CModule** umożliwiające filtrowanie komunikatów gdzie temperatury zgłoszonych maszyny znajduje się poniżej dopuszczalny próg. Teraz należy skompilować to rozwiązanie jako obraz kontenera i wypchnąć go do rejestru kontenerów. 

1. Użyj następującego polecenia, aby zalogować się do platformy Docker na komputerze deweloperskim. Zaloguj się przy użyciu nazwy użytkownika, hasło i serwer logowania z usługi Azure container registry. Możesz pobrać te wartości z **klucze dostępu** sekcji rejestru w witrynie Azure portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

2. W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy nazwę projektu, który chcesz skompilować. Nazwa domyślna to **AzureIotEdgeApp1** a ponieważ tworzysz moduł Windows powinno być rozszerzenie **Windows.Amd64**. 

3. Wybierz **Zbuduj i Wypchnij moduły usługi IoT Edge**. 

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów. 

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Przy użyciu Eksploratora chmury programu Visual Studio i rozszerzenia usługi Azure IoT Edge narzędzia wdrożyć projekt modułu urządzenia usługi IoT Edge. Masz już manifest wdrożenia, jest przygotowane do scenariusza **deployment.json** pliku w folderze konfiguracji. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona. 

1. W programie Visual Studio cloud explorer rozwiń zasobami, aby zobaczyć Twoją listę urządzeń IoT. 

2. Kliknij prawym przyciskiem myszy nazwę urządzenia usługi IoT Edge, które chcesz otrzymywać wdrożenia. 

3. Wybierz **Utwórz wdrożenie**.

4. W Eksploratorze plików wybierz **deployment.windows amd64** pliku w folderze konfiguracji rozwiązania. 

5. Odśwież Eksplorator chmury, aby zobaczyć wdrożone moduły na liście w obszarze urządzenia. 


## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Gdy zastosujesz manifest wdrożenia na urządzeniu usługi IoT Edge, środowisko uruchomieniowe usługi IoT Edge na tym urządzeniu zbierze nowe informacje na temat wdrożenia i zacznie wykonywanie. Wszelkie moduły uruchomione na urządzeniu, których nie uwzględniono w manifeście wdrożenia, zostaną zatrzymane. Wszelkie moduły, których brakuje na urządzeniu, zostaną uruchomione. 

Rozszerzenie IoT Edge narzędzia służy do wyświetlania wiadomości przychodzące w Twoim Centrum IoT Hub. 

1. W programie cloud explorer programu Visual Studio wybierz nazwę urządzenia usługi IoT Edge. 

2. W **akcje** listy wybierz **Rozpocznij monitorowania komunikatu D2C**. 

3. Wyświetl wiadomości otrzymywanych z usługi IoT Hub. Może potrwać trochę wiadomości zostanie dostarczona, ponieważ urządzenia usługi IoT Edge do odbierania jego nowego wdrożenia i uruchamiania wszystkich modułów. Następnie zmiany wprowadzone do kodu CModule poczekaj, aż temperatury maszyny osiągnie 25 stopni przed wysłaniem wiadomości. Typ komunikatu dodaje także **alertu** na wszystkie komunikaty, w których progu temperatury. 

   ![Wyświetlanie wiadomości otrzymywanych z usługi IoT Hub](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Edytuj bliźniaczą reprezentację modułu

Użyliśmy CModule bliźniaczą reprezentację modułu, aby ustawić próg temperaturę w stopniach 25. Bliźniacza reprezentacja modułu można użyć w celu zmiany funkcji bez konieczności aktualizowania kodu modułu.

1. W programie Visual Studio, otwórz **deployment.windows amd64.json** pliku. (Nie plik deployment.template. Jeśli nie widzisz wdrażania, manifest w pliku konfiguracji w Eksploratorze rozwiązań, wybierz **Pokaż wszystkie pliki** ikonę na pasku narzędzi Eksploratora.)

2. Znajdź bliźniaczej reprezentacji CModule i zmień wartość właściwości **temperatureThreshold** parametru, aby nowe temperatury 5 stopni do 10 stopni wyższa niż najnowszy zgłoszonych temperatury. 

3. Zapisz **deployment.windows amd64.json** pliku.

4. Wykonaj kroki wdrażania ponownie, aby stosowanie manifest wdrażania aktualizacji na urządzeniu. 

5. Monitorowanie przychodzących komunikatów przesyłanych z chmury do urządzenia. Powinien zostać wyświetlony wiadomości, Zatrzymaj, aż do osiągnięcia progu temperatury nowe. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego. 

W przeciwnym razie można usunąć lokalnej konfiguracji i zasobów platformy Azure używane w tym artykule Aby uniknąć naliczania opłat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został utworzony moduł usługi IoT Edge zawierający kod służący do filtrowania nieprzetworzonych danych wygenerowanych przez urządzenie usługi IoT Edge. Gdy wszystko będzie gotowe do tworzenia własnych modułach, możesz dowiedzieć się więcej [tworzenia własnych modułów usługi IoT Edge](module-development.md) lub jak [opracowanie modułów za pomocą programu Visual Studio](how-to-visual-studio-develop-module.md). Można przejść do następnego samouczki, aby dowiedzieć się, jak usługa Azure IoT Edge mogą pomóc Ci wdrażanie usług w chmurze platformy Azure do przetwarzania i analizowania danych na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Funkcje](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [uczenia maszynowego](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
