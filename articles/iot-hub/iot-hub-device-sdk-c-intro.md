---
title: Zestaw SDK urządzeń Azure IoT dla języka C | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy z usługą SDK urządzeń Azure IoT dla języka C i Dowiedz się, jak tworzyć aplikacje do urządzeń, które komunikują się z usługą IoT hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: yizhon
ms.openlocfilehash: d758d761e560642de76e149c83fc6898aa78bafb
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65910332"
---
# <a name="azure-iot-device-sdk-for-c"></a>Usługa Azure zestaw SDK urządzeń IoT dla języka C

**Zestaw SDK urządzeń Azure IoT** to zestaw bibliotek zaprojektowane w celu uproszczenia procesu wysyłania wiadomości do i odbierania komunikatów z **usługi Azure IoT Hub** usługi. Istnieją różne odmiany zestawu SDK, każdy przeznaczonych dla określonej platformy, ale w tym artykule opisano **zestaw SDK urządzeń Azure IoT dla języka C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zestaw SDK urządzeń Azure IoT dla języka C jest zapisywany w ANSI C (C99), aby zmaksymalizować przenośność. Ta funkcja sprawia, że biblioteki są dobrze nadaje się do działania na wielu platformach i urządzeniach, szczególnie w przypadku, gdy minimalizując dysku i ma najwyższy priorytet, zużycie pamięci.

Istnieją szerokiego zakresu platform, na których został przetestowany zestaw SDK (zobacz [certyfikatu platformy Azure dla IoT — wykaz urządzeń](https://catalog.azureiotsolutions.com/) Aby uzyskać szczegółowe informacje). Chociaż ten artykuł zawiera wskazówki dotyczące przykładowego kodu na platformie Windows, kodu opisanego w tym artykule jest identyczna w zakresie obsługiwanych platform.

Poniższy klip wideo zawiera omówienie zestawu SDK usługi Azure IoT dla języka C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Ten artykuł stanowi wprowadzenie do architektury zestawu SDK urządzeń Azure IoT dla języka C. Pokazuje jak zainicjować biblioteki urządzeń, wysyłać dane do Centrum IoT Hub i odbieranie komunikatów z niego. Informacje przedstawione w tym artykule powinien być wystarczająco dużo, aby rozpocząć korzystanie z zestawu SDK, ale również zapewnia łącza do dodatkowych informacji na temat biblioteki.

## <a name="sdk-architecture"></a>Architektura zestawu SDK

Możesz znaleźć [ **zestaw SDK urządzeń Azure IoT dla języka C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repozytorium i wyświetlenia jej szczegółów interfejsu API w [dokumentacja interfejsu API języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

Najnowszą wersję bibliotek można znaleźć w **wzorca** gałęzi repozytorium:

  ![Zrzut ekranu przedstawiający głównej gałęzi w repozytorium](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Implementacja core SDK znajduje się w **iothub\_klienta** folder, który zawiera implementację najniższy poziom interfejsu API zestawu SDK: **usługi IoTHubClient** biblioteki. **Usługi IoTHubClient** biblioteka zawiera interfejsy API Implementowanie nieprzetworzone komunikaty do wysyłania komunikatów do Centrum IoT Hub i odbieranie komunikatów z usługi IoT Hub. Korzystając z tej biblioteki, odpowiedzialność za wdrażanie serializacji komunikatu, ale inne informacje szczegółowe dotyczące komunikacji z usługą IoT Hub są obsługiwane dla Ciebie.

* **Serializator** folder zawiera funkcje pomocnicze i przykłady, które pokazują, jak do serializowania danych przed wysłaniem do usługi Azure IoT Hub przy użyciu biblioteki klienta. Korzystanie z serializatora nie jest obowiązkowe i jest dostarczana jako udogodnienie. Aby użyć **serializator** biblioteki, zdefiniuj model, który określa dane do wysłania do Centrum IoT i komunikaty oczekuje się od niego. Gdy model jest zdefiniowana, zestaw SDK udostępnia powierzchni interfejsu API, która umożliwia łatwą pracę z komunikatami z urządzenia do chmury i z chmury do urządzeń bez konieczności martwienia się o szczegóły serializacji. Biblioteka jest zależny od innych bibliotek typu open source, które implementują transportu przy użyciu protokołów, takich jak MQTT i protokół AMQP.

* **Usługi IoTHubClient** biblioteki jest zależny od innych bibliotek typu open source:

  * [Narzędzie współużytkowane Azure C](https://github.com/Azure/azure-c-shared-utility) biblioteki, która zapewnia funkcje wspólne podstawowych zadań (na przykład ciągów, listy manipulacji i we/wy) wymaganą przy zestawów SDK C dotyczących platformy Azure.

  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) biblioteki, która jest implementacją protokołu AMQP zoptymalizowany pod kątem urządzeń ograniczonych zasobów po stronie klienta.

  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) biblioteki, która jest biblioteką ogólnego przeznaczenia, implementacja protokołu MQTT i zoptymalizowane na potrzeby ograniczonego zasobu urządzenia.

Korzystanie z tych bibliotek jest łatwiejsze do zrozumienia, analizując przykładowy kod. Poniższe sekcje opisują kilka przykładowych aplikacji, które znajdują się w zestawie SDK. W tym przewodniku powinien zapewnić dobry sposób działania dla różnych funkcji warstwy architektury zestawu SDK i wprowadzenie do działania za pośrednictwem interfejsów API.

## <a name="before-you-run-the-samples"></a>Przed uruchomieniem próbki

Zanim będzie można uruchomić przykłady z zestawu SDK urządzenia usługi Azure IoT dla języka C, należy najpierw [Utwórz wystąpienie usługi IoT Hub](iot-hub-create-through-portal.md) w subskrypcji platformy Azure. Następnie wykonaj następujące czynności:

* Przygotowywanie środowiska projektowego
* Uzyskaj poświadczenia urządzenia.

### <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Pakiety są dostarczane dla popularnych platform (np. NuGet dla Windows lub apt_get Debian i Ubuntu) i przykłady korzystają z tych pakietów, jeśli jest dostępna. W niektórych przypadkach należy skompilować zestaw SDK dla lub na urządzeniu. Jeśli potrzebujesz skompilować zestaw SDK, zobacz [przygotowywanie środowiska projektowego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w repozytorium GitHub.

Aby uzyskać przykładowy kod aplikacji, należy pobrać kopię zestawu SDK z usługi GitHub. Uzyskaj swoją kopię źródła z **wzorca** gałęzi [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Uzyskaj poświadczenia urządzenia

Teraz, gdy masz przykładowy kod źródłowy, w następnej kolejności wykonaj jest zestawu poświadczeń urządzenia. Dla urządzenia mieć możliwość dostępu do usługi IoT hub musisz najpierw dodać urządzenia w rejestrze tożsamości Centrum IoT Hub. Po dodaniu urządzenia, otrzymasz zestaw poświadczeń urządzenia, które należy dla tego urządzenia można było połączyć się z Centrum IoT hub. Przykładowe aplikacje omówiona w następnej sekcji oczekiwać te poświadczenia w postaci **parametry połączenia urządzenia**.

Istnieje kilka narzędzi typu open source, które ułatwiają zarządzanie Centrum IoT hub.

* Aplikacji Windows, o nazwie [Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

* Rozszerzenia programu Visual Studio Code dla wielu platform o nazwie [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Interfejs wiersza polecenia dla wielu platform Python o nazwie [rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

Ten samouczek używa graficznym *Eksplorator urządzeń* narzędzia. Możesz użyć *narzędzia usługi Azure IoT dla programu VS Code* w przypadku tworzenia w programie VS Code. Można również użyć *rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure w wersji 2.0* narzędzia, jeśli wolisz używać narzędzi interfejsu wiersza polecenia.

Narzędzie device explorer używa bibliotek usługi Azure IoT do wykonywania różnych funkcji w Centrum IoT Hub, w tym dodawanie urządzeń. Jeśli używasz narzędzie device explorer, aby dodać urządzenie, zostanie wyświetlony ciąg połączenia dla Twojego urządzenia. Należy te parametry połączenia do uruchamiania aplikacji przykładowej.

Jeśli nie znasz narzędzie device explorer, Poniższa procedura opisuje jak z niej korzystać, aby dodać urządzenie i uzyskać parametry połączenia urządzenia.

1. Aby zainstalować narzędzie device explorer, zobacz [sposób używania Device Explorer dla urządzeń usługi IoT Hub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Kiedy uruchamiasz program, zostanie wyświetlony ten interfejs:

   ![Zrzut ekranu z bliźniaczej reprezentacji Device Explorer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Wprowadź swoje **parametry połączenia Centrum IoT Hub** w pierwszym polu i kliknij **aktualizacji**. Ten krok obejmuje skonfigurowanie narzędzie tak, aby umożliwić komunikację z usługą IoT Hub. 

**Parametry połączenia** znajduje się w obszarze **usługi IoT Hub** > **ustawienia** > **zasady dostępu współużytkowanego**  >  **iothubowner**.

1. Po skonfigurowaniu parametry połączenia Centrum IoT kliknij **zarządzania** karty:

   ![Bliźniacza reprezentacja Device Explorer / zarządzania zrzut ekranu](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Ta karta jest, którym zarządzasz urządzeń zarejestrowanych w usłudze IoT hub.

1. Tworzenie urządzenia, klikając **Utwórz** przycisku. Wyświetla okno dialogowe z zestawem wstępnie wypełnionych klucze (podstawowe i pomocnicze). Wprowadź **identyfikator urządzenia** a następnie kliknij przycisk **Utwórz**.

   ![Utwórz zrzut ekranu urządzenia](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Po utworzeniu urządzenia urządzenia Wyświetl listę wszystkich zarejestrowanych urządzeń, który został utworzony w tym aktualizacje. Kliknięcie prawym przyciskiem myszy nowe urządzenie, zostanie wyświetlony w tym menu:

   ![Wyników kliknij prawym przyciskiem myszy bliźniaczej reprezentacji Device Explorer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Jeśli wybierzesz **skopiuj parametry połączenia dla wybranego urządzenia**, parametry połączenia urządzenia są kopiowane do Schowka. Zachowaj kopię parametry połączenia urządzenia. Będzie on potrzebny podczas uruchamiania aplikacji przykładowej, opisane w poniższych sekcjach.

Po ukończeniu powyższych kroków, wszystko będzie gotowe rozpocząć uruchamianie kodu. Większość przykładów ma stałą w górnej części pliku źródłowego głównego, który umożliwia wprowadzanie parametrów połączenia. Na przykład, odpowiedni wiersz z **iothub_client\_przykłady\_iothub_convenience_sample** aplikacja pojawi się w następujący sposób.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Korzystanie z biblioteki usługi IoTHubClient

W ramach **iothub\_klienta** folderu w [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) repozytorium istnieje **przykłady** folderze, który zawiera aplikację o nazwie **iothub\_klienta\_przykładowe\_mqtt**.

Wersja Windows **iothub_client\_przykłady\_iothub_convenience_sample** aplikacja zawiera następujące rozwiązania Visual Studio:

  ![Visual Studio Solution Explorer](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Jeśli program Visual Studio pyta rzekieruj projekt do najnowszej wersji, należy zaakceptować monit.

To rozwiązanie zawiera jeden projekt. Istnieją cztery pakiety NuGet zainstalowane w tym rozwiązaniu:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Należy zawsze **Microsoft.Azure.C.SharedUtility** pakietu podczas pracy z zestawem SDK. W tym przykładzie korzysta z protokołu MQTT, dlatego należy uwzględnić **Microsoft.Azure.umqtt** i **Microsoft.Azure.IoTHub.MqttTransport** pakietów (są równoważne pakietów dla protokołów AMQP oraz protokołu HTTPS). Ponieważ w przykładzie użyto **usługi IoTHubClient** biblioteki, należy również uwzględnić **Microsoft.Azure.IoTHub.IoTHubClient** pakietu w rozwiązaniu.

Można znaleźć implementacji dla przykładowej aplikacji w **iothub_client\_przykłady\_iothub_convenience_sample** pliku źródłowego.

Następujące kroki przeprowadzą Cię przez co to są wymagane do użycia za pomocą tej aplikacji przykładowej **usługi IoTHubClient** biblioteki.

### <a name="initialize-the-library"></a>Inicjowanie biblioteki

> [!NOTE]
> Przed rozpoczęciem pracy z bibliotekami, konieczne może przeprowadzić inicjowanie specyficzne dla platformy. Na przykład jeśli planowane jest użycie połączeń AMQP dla systemu Linux musisz zainicjować bibliotekę OpenSSL. Przykłady w [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c) wywołania funkcji narzędzia **platformy\_init** podczas uruchamiania klienta i wywoływać **platformy\_deinit**funkcji przed zamknięciem. Te funkcje są deklarowane w pliku nagłówkowym platform.h. Sprawdź definicje funkcji dla platformy docelowej w [repozytorium](https://github.com/Azure/azure-iot-sdk-c) do określenia, czy muszą zawierać dowolny kod inicjowania specyficzne dla platformy w swoim kliencie.

Aby rozpocząć pracę z biblioteki, najpierw należy przydzielić dojścia klienta usługi IoT Hub:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Należy przekazać kopię parametry połączenia urządzenia, uzyskany w narzędzie device explorer dotyczących wyłącznie tej funkcji. Można również wyznaczyć protokołu komunikacji do użycia. W tym przykładzie użyto protokołu MQTT, ale AMQP i HTTPS są również opcje.

Gdy masz prawidłową **IOTHUB\_klienta\_obsługi**, możesz rozpocząć wywoływanie interfejsów API do wysyłania i odbierania wiadomości z usługi IoT Hub.

### <a name="send-messages"></a>Wysyłanie wiadomości

Przykładowa aplikacja konfiguruje pętlę do wysyłania komunikatów do Centrum IoT hub. Poniższy fragment kodu:

- Tworzy komunikat.
- Dodaje właściwość do wiadomości.
- Wysyła komunikat.

Najpierw utwórz wiadomość:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Za każdym razem, gdy wysyłasz wiadomości, można określić odwołanie do funkcji wywołania zwrotnego, które jest wywoływane, gdy dane są przesyłane. W tym przykładzie jest wywoływana funkcja wywołania zwrotnego **SendConfirmationCallback**. Poniższy fragment kodu przedstawia tę funkcję wywołania zwrotnego:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Należy pamiętać, wywołanie **IoTHubMessage\_Destroy** działać po zakończeniu korzystania z komunikatem. Ta funkcja powoduje zwolnienie zasobów przydzielonych podczas tworzenia komunikatu.

### <a name="receive-messages"></a>Odbieranie wiadomości

Odebranie komunikatu jest operacją asynchroniczną. Najpierw należy zarejestrować się wywołanie zwrotne do wywołania, gdy urządzenie otrzyma komunikat:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Ostatni parametr jest pusty wskaźnik dowolnie. W tym przykładzie jest wskaźnik do liczby całkowitej, ale może to być wskaźnik do bardziej złożone struktury danych. Ten parametr umożliwia funkcji wywołania zwrotnego do wykonywania operacji udostępnionego stanu z funkcją wywołującą tej funkcji.

Gdy urządzenie otrzymuje komunikat, wywoływana jest zarejestrowana funkcja wywołania zwrotnego. Ta funkcja wywołania zwrotnego pobiera:

* Identyfikator komunikatu i identyfikator korelacji z komunikatu.
* Treść komunikatu.
* Wszystkie niestandardowe właściwości z komunikatu.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Użyj **IoTHubMessage\_GetByteArray** funkcję, aby pobrać wiadomości, czyli w tym przykładzie ciąg tekstowy.

### <a name="uninitialize-the-library"></a>Odinicjalizuj biblioteki

Po zakończeniu zdarzenia wysyłanie i odbieranie wiadomości możesz uninitialize biblioteki IoT. Aby to zrobić, wykonaj poniższe wywołanie funkcji:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

To wywołanie zwolnienie zasobów uprzednio przydzielonej przez **usługi IoTHubClient\_CreateFromConnectionString** funkcji.

Jak widać, to proste wysyłanie i odbieranie komunikatów za pomocą **usługi IoTHubClient** biblioteki. Biblioteka obsługuje szczegółowe informacje o komunikacji z usługą IoT Hub, który protokół do użycia w tym (z perspektywy dewelopera, jest to opcja prostej konfiguracji).

**Usługi IoTHubClient** biblioteki również zapewnia precyzyjną kontrolę nad jak do serializacji danych urządzenie wysyła do usługi IoT Hub. W niektórych przypadkach ten poziom kontroli przynosi korzyści, ale w innych to szczegół implementacji, które nie chcesz wziąć pod uwagę. Jeśli tak jest rzeczywiście, można rozważyć przy użyciu **serializator** biblioteki, który jest opisany w następnej sekcji.

## <a name="use-the-serializer-library"></a>Korzystanie z biblioteki serializatora

Koncepcyjnie **serializator** biblioteki znajduje się w górnej części **usługi IoTHubClient** biblioteki w zestawie SDK. Używa ona **usługi IoTHubClient** biblioteki podstawowej komunikacji z Centrum IoT Hub, ale dodaje funkcje modelowania, które usunąć obciążeń związanych z serializacji wiadomości od dewelopera. Jak działa ta biblioteka jest najlepsze przedstawione według przykładu.

Wewnątrz **serializator** folderu w [repozytorium azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), jest **przykłady** folderze, który zawiera aplikację o nazwie **simplesample\_mqtt**. W tym przykładzie w wersji Windows zawiera następujące rozwiązania Visual Studio:

  ![Rozwiązania Visual Studio dla przykładu protokołu mqtt](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Jeśli program Visual Studio pyta rzekieruj projekt do najnowszej wersji, należy zaakceptować monit.

Podobnie jak w poprzednim przykładzie ta obejmuje kilka pakietów NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

W tym samouczku większość tych pakietów w poprzednim przykładzie, ale **Microsoft.Azure.IoTHub.Serializer** nowego. Ten pakiet jest wymagany, gdy używasz **serializator** biblioteki.

Można znaleźć implementacji aplikacji przykładowej w **iothub_client\_przykłady\_iothub_convenience_sample** pliku.

Poniższe sekcje przeprowadzą Cię przez proces kluczowych części w tym przykładzie.

### <a name="initialize-the-library"></a>Inicjowanie biblioteki

Aby rozpocząć pracę z **serializator** biblioteki, wywołaj inicjowania interfejsów API:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

Wywołanie **serializator\_init** funkcji jest jednorazowe wywołanie i inicjuje podstawowej biblioteki. Następnie wywołaj **usługi IoTHubClient\_LL\_CreateFromConnectionString** funkcji, która jest tego samego interfejsu API, jak **usługi IoTHubClient** próbki. To wywołanie ustawia parametry połączenia urządzenia (to wywołanie jest również, gdzie wybierz protokół chcesz użyć). W tym przykładzie używa protokołu MQTT transportu, ale można użyć protokołu AMQP lub HTTPS.

Na koniec Wywołaj **Utwórz\_modelu\_wystąpienia** funkcji. **WeatherStation** jest przestrzenią nazw, modelu i **ContosoAnemometer** jest nazwa modelu. Po utworzeniu wystąpienia modelu służy do uruchomienia, wysyłanie i odbieranie komunikatów. Jednak jest ważne zrozumieć, jakie model.

### <a name="define-the-model"></a>Zdefiniuj model

Modelu w **serializator** Biblioteka definiuje wiadomości, które urządzenia mogą wysyłać do Centrum IoT Hub i komunikaty o nazwie *akcje* w języku modelowania, który może odbierać. Należy zdefiniować model przy użyciu zestawu makr C, podobnie jak w **iothub_client\_przykłady\_iothub_convenience_sample** przykładowej aplikacji:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**Rozpocząć\_przestrzeni nazw** i **zakończenia\_przestrzeni nazw** makra zarówno nie przyjmują przestrzeni nazw modelu jako argument. Oczekuje się, że wszystko między te makra jest definicja modelu lub modele oraz struktur danych, które używają modeli.

W tym przykładzie jest jednym modelu o nazwie **ContosoAnemometer**. Ten model definiuje dwie części danych, które urządzenie może wysyłać do Centrum IoT: **DeviceId** i **prędkość wiatru**. Definiuje trzy czynności (wiadomości), które urządzenia mogą odbierać: **TurnFanOn**, **TurnFanOff**, i **SetAirResistance**. Każdy element danych ma typ, a każde działanie ma nazwę (i opcjonalnie zestaw parametrów).

Zdefiniuj powierzchni interfejsu API, który służy do wysyłania komunikatów do Centrum IoT i odpowiadać na komunikaty wysyłane do urządzenia, danych i akcji zdefiniowanych w modelu. Użyj tego modelu najlepiej rozumie przykładu.

### <a name="send-messages"></a>Wysyłanie wiadomości

Model definiuje dane, które można wysyłać do Centrum IoT Hub. W tym przykładzie, oznacza to jeden z elementów danych dwóch zdefiniowane przy użyciu **WITH_DATA** makra. Istnieje kilka kroków wymaganych do wysyłania **DeviceId** i **prędkość wiatru** wartości do usługi IoT hub. Pierwsza to można ustawić dane, które mają zostać wysłane:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model wcześniej zdefiniowaną umożliwia ustawienie wartości przez ustawienie członkowie **struktury**. Następnie serializacji komunikatu, który chcesz wysłać:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Ten kod serializuje urządzenie chmura do buforu (wskazywanym przez **docelowy**). Następnie wywołuje kod **sendMessage** funkcję, aby wysłać wiadomość do usługi IoT Hub:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

Drugi do ostatniego parametru **usługi IoTHubClient\_LL\_SendEventAsync** jest odwołanie do funkcji wywołania zwrotnego, która jest wywoływana, gdy dane są przesyłane w pomyślnie. Oto funkcji wywołania zwrotnego w przykładzie:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Drugi parametr jest wskaźnikiem do kontekstu użytkownika; Ten sam wskaźnik przekazywany do **usługi IoTHubClient\_LL\_SendEventAsync**. W tym przypadku kontekst jest prostego licznika, ale coś, czego chcesz, aby można go.

To wszystko na temat wysyłania komunikatów z urządzenia do chmury. Pozostało jedynie na pokrycie jest jak odbierać komunikaty.

### <a name="receive-messages"></a>Odbieranie wiadomości

Odbieranie komunikatów działa podobnie jak sposób wiadomości działają w **usługi IoTHubClient** biblioteki. Najpierw należy zarejestrować się funkcji wywołania zwrotnego komunikat:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Następnie napiszesz funkcji wywołania zwrotnego, które jest wywoływane, gdy wiadomość zostaje odebrana:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Ten kod jest standardowy — jest to ta sama dla dowolnego rozwiązania. Funkcja ta odbiera komunikat i dba o rozsyłania jej do odpowiedniej funkcji za pośrednictwem wywołania do **EXECUTE\_polecenia**. Funkcja wywoływana na tym etapie zależy od definicji akcje w modelu.

Podczas definiowania akcji w modelu, dlatego musisz zaimplementować funkcję, która jest wywoływana, gdy urządzenie otrzymuje odpowiedni komunikat. Na przykład, jeśli model definiuje tę akcję:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Zdefiniuj funkcję z tą sygnaturą:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Należy zauważyć, jak nazwa funkcji jest zgodna Nazwa akcji w modelu i parametry funkcji zgodne parametrów określonych dla akcji. Pierwszy parametr jest zawsze wymagany i zawiera wskaźnik do wystąpienia modelu.

Gdy urządzenie otrzymuje komunikat, który jest zgodny z tą sygnaturą, nosi nazwę odpowiedniej funkcji. W związku z tym, oprócz konieczności dołączania standardowy kod z **IoTHubMessage**, odbierania wiadomości jest to kwestia Definiowanie prostej funkcji dla każdej akcji zdefiniowanych w modelu.

### <a name="uninitialize-the-library"></a>Odinicjalizuj biblioteki

Po zakończeniu wysyłania danych i odbierania wiadomości możesz uninitialize biblioteki IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Każda z tych trzech funkcji jest wyrównywany z trzech funkcji inicjowania opisanych powyżej. Wywoływanie tych interfejsów API zapewnia zwalnia wcześniej przydzielone zasoby.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano podstawowe informacje dotyczące korzystania z bibliotek w **zestaw SDK urządzeń Azure IoT dla języka C**. Go podano wystarczających informacji, aby zrozumieć, co znajduje się w zestawie SDK, jego architekturę i jak rozpocząć pracę z usługą przykłady Windows. Opis zestawu SDK będzie nadal następnego artykułu, ponieważ wyjaśnia [więcej informacji na temat usługi IoTHubClient biblioteki](iot-hub-device-sdk-c-iothubclient.md).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
