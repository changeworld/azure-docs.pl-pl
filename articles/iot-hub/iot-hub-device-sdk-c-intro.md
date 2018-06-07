---
title: Urządzenia Azure IoT SDK dla języka C | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy z urządzeń Azure IoT SDK dla języka C i Dowiedz się, jak tworzyć aplikacje urządzenia, które komunikują się z Centrum IoT.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: yizhon
ms.openlocfilehash: 78cd8b2d6afe98e34c33ed3c841c8023d9dab764
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635274"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure urządzenia IoT zestawu SDK dla języka C

**Urządzenia Azure IoT SDK** to zestaw bibliotek zaprojektowane w celu uproszczenia procesu wysyłania wiadomości i odbieranie komunikatów z **Centrum IoT Azure** usługi. Istnieją różne odmiany zestawu SDK, każdy przeznaczonych dla określonej platformy, ale w tym artykule opisano **urządzenia Azure IoT SDK dla języka C**.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Urządzenia Azure IoT SDK dla języka C są zapisywane w ANSI C (C99), aby zmaksymalizować przenoszenia. Ta funkcja udostępnia biblioteki dobrze nadaje się do działania na wielu platformach i urządzeniach, szczególnie w przypadku, gdy minimalizując dysku i zużycie pamięci jest priorytetem.

Istnieje szeroka gama platformy, na których zostały przetestowane zestawu SDK (zobacz [Azure certyfikowane dla katalogu urządzenia IoT](https://catalog.azureiotsuite.com/) szczegółowe informacje). Chociaż ten artykuł zawiera wskazówki przykładowy kod działających na platformie systemu Windows, kod opisane w tym artykule jest identyczna w zakresie obsługiwanych platform.

Poniższe wideo zawiera omówienie zestawu SDK platformy Azure IoT dla C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Ten artykuł stanowi wprowadzenie do architektury urządzenia Azure IoT SDK dla C. Go pokazano, jak zainicjować biblioteki urządzenia, wysyłania danych do Centrum IoT i otrzymywać wiadomości. Informacje przedstawione w tym artykule powinny być wystarczające, aby rozpocząć korzystanie z zestawu SDK, ale również zawiera łącza do dodatkowych informacji o bibliotekach.

## <a name="sdk-architecture"></a>Architektura zestawu SDK

Można znaleźć [ **urządzenia Azure IoT SDK dla języka C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repozytorium i wyświetlenia jej szczegółów interfejsu API w [dokumentacja interfejsu API C](https://azure.github.io/azure-iot-sdk-c/index.html).

Najnowszą wersję biblioteki można znaleźć w **wzorca** gałęzi repozytorium:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

* Implementacja core zestawu SDK znajduje się w **Centrum iothub\_klienta** folderu, który zawiera implementację najniższy poziom interfejsu API zestawu SDK: **IoTHubClient** biblioteki. **IoTHubClient** biblioteka zawiera interfejsy API Implementowanie raw wiadomości wysyłanie komunikatów do Centrum IoT i odbierania wiadomości z Centrum IoT. Korzystając z tej biblioteki, jest odpowiedzialny za wdrażanie serializacji komunikatu, ale inne szczegóły komunikowania się z Centrum IoT są obsługiwane dla Ciebie.
* **Serializator** folder zawiera funkcje pomocnicze i przykłady, których pokazano, jak do serializowania danych przed wysłaniem do Centrum IoT Azure za pomocą biblioteki klienta. Użycie Serializator nie jest obowiązkowe i jest dostępne dla wygody. Aby użyć **serializator** biblioteki, należy zdefiniować modelu, który określa dane do wysłania do Centrum IoT i komunikaty, oczekuje się od niego. Po zdefiniowaniu modelu zestaw SDK udostępnia powierzchni interfejsu API, który umożliwia łatwe pracę z urządzenia do chmury i chmury do urządzenia wiadomości bez obaw o szczegóły serializacji. Biblioteka jest zależny od innych bibliotek typu open source, które implementują transportu przy użyciu protokołów, takich jak MQTT i protokołu AMQP.
* **IoTHubClient** biblioteki jest zależny od innych bibliotek typu open source:
  * [Azure C udostępnione narzędzie](https://github.com/Azure/azure-c-shared-utility) biblioteki, która zawiera często używane funkcje podstawowe zadania (na przykład ciągi, manipulowanie listy i we/wy) wymagane przez zestawów SDK C związanych z platformy Azure.
  * [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) biblioteki, która jest implementacją protokołu AMQP zoptymalizowane pod kątem urządzeń zasobów ograniczone po stronie klienta.
  * [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) biblioteki, która jest biblioteką ogólnego przeznaczenia implementacji protokołu MQTT i zoptymalizowany pod kątem urządzeń zasobów ograniczone.

Korzystanie z biblioteki jest łatwiejsze do zrozumienia analizując przykładowy kod. Poniższe sekcje umożliwia przeprowadzenie kilka przykładowych aplikacji, które znajdują się w zestawie SDK. W tym przewodniku powinien zapewnić dobrą działanie dla różnych funkcjach warstwy architektury zestawu SDK i wprowadzenie do działania za pośrednictwem interfejsów API.

## <a name="before-you-run-the-samples"></a>Przed uruchomieniem próbki

Przed uruchomieniem próbek w urządzenia Azure IoT SDK dla języka C, należy najpierw [utworzenia wystąpienia usługi IoT Hub](iot-hub-create-through-portal.md) w Twojej subskrypcji platformy Azure. Następnie wykonaj następujące czynności:

* Przygotowywanie środowiska projektowego
* Uzyskaj poświadczenia urządzenia.

### <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Pakiety są dostępne dla wspólnego platformy (na przykład NuGet dla systemu Windows lub apt_get Debian i Ubuntu) i przykłady za pomocą tych pakietów, jeśli jest dostępna. W niektórych przypadkach należy skompilować w zestawie SDK dla lub na urządzeniu. Jeśli potrzebujesz do kompilowania zestawu SDK, zobacz [przygotowywanie środowiska projektowego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w repozytorium GitHub.

Aby uzyskać przykładowy kod aplikacji, należy pobrać kopię zestawu SDK z usługi GitHub. Pobierz kopię źródło **wzorca** gałęzi [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c).


### <a name="obtain-the-device-credentials"></a>Uzyskaj poświadczenia urządzenia

Teraz, gdy masz przykładowy kod źródłowy następnym etapem jest można pobrać zestawu poświadczeń urządzenia. Urządzenia mogły uzyskiwać dostęp do Centrum IoT najpierw należy dodać urządzenie w rejestrze tożsamości Centrum IoT. Po dodaniu urządzenia możesz pobrać zestaw poświadczeń urządzenia, które należy urządzenia móc połączyć się z Centrum IoT. Przykładowe aplikacje omówiona w następnej sekcji mogą pojawić się te poświadczenia w postaci **ciąg połączenia urządzenia**.

Istnieje kilka narzędzi typu open source służących do zarządzania Centrum IoT.

* Aplikacji systemu Windows o nazwie [explorer urządzenia](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
* Wywołuje narzędzie interfejsu wiersza polecenia języka Python i platform [rozszerzenie IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

W tym samouczku używana graficznym *explorer urządzenia* narzędzia. Można również użyć *rozszerzenie IoT Azure CLI 2.0* narzędzia, jeśli wolisz za pomocą narzędzia interfejsu wiersza polecenia.

Narzędzie explorer urządzenie korzysta z bibliotek usługi Azure IoT do wykonywania różnych funkcji w Centrum IoT, łącznie z dodawaniem urządzeń. Jeśli za pomocą narzędzia Eksplorator urządzenia można dodać urządzenie, możesz uzyskać ciąg połączenia dla danego urządzenia. Należy te parametry połączenia do uruchamiania aplikacji przykładowej.

Jeśli nie znasz za pomocą narzędzia Eksplorator urządzenia, Poniższa procedura opisuje jak z niego korzystać, aby dodać urządzenie i uzyskać ciąg połączenia urządzenia.

Aby zainstalować narzędzie explorer urządzenia, zobacz [jak używać Eksploratora urządzenia dla urządzeń z Centrum IoT](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

Po uruchomieniu programu pojawia się tego interfejsu:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Wprowadź użytkownika **parametry połączenia Centrum IoT** w pierwszym polu i kliknij **aktualizacji**. Ten krok konfiguruje narzędzie, dzięki czemu może komunikować się z Centrum IoT.

Po skonfigurowaniu parametry połączenia Centrum IoT kliknij **zarządzania** karty:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Ta karta jest, którym zarządzasz urządzeń zarejestrowanych w Centrum IoT.

Urządzenie można tworzyć przez kliknięcie **Utwórz** przycisku. Wyświetla okno dialogowe z zestawu kluczy wstępnie wypełnione (podstawowych i pomocniczych). Wprowadź **identyfikator urządzenia** , a następnie kliknij przycisk **Utwórz**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Po utworzeniu urządzenia urządzenia Wyświetl listę wszystkich zarejestrowanych urządzeń, w tym utworzonej przed chwilą aktualizacji. Kliknięcie prawym przyciskiem myszy urządzenie nowej pojawić tego menu:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Jeśli wybierzesz **skopiuj parametry połączenia dla wybranego urządzenia**, ciąg połączenia urządzenia jest kopiowany do Schowka. Przechowywać kopię ciąg połączenia urządzenia. Należy go podczas uruchamiania aplikacji przykładowych opisane w poniższych sekcjach.

Po wykonaniu powyższych czynności możesz rozpocząć uruchamianie kodu. Oba przykłady ma stałą w górnej części plik źródłowy głównego, który umożliwia wprowadzanie parametrów połączenia. Na przykład odpowiedni wiersz z **Centrum iothub\_klienta\_próbki\_mqtt** aplikacja pojawi się w następujący sposób.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Korzystanie z biblioteki IoTHubClient

W ramach **Centrum iothub\_klienta** folderu w [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) repozytorium, Brak **przykłady** folder, który zawiera aplikację o nazwie **Centrum iothub\_klienta\_próbki\_mqtt**.

Wersja systemu Windows **Centrum iothub\_klienta\_próbki\_mqtt** aplikacji obejmują następujące rozwiązania Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-mqtt.PNG)

> [!NOTE]
> Jeśli w programie Visual Studio 2017 r, możesz otworzyć ten projekt, należy zaakceptować z monitami, aby przekierować projektu do najnowszej wersji.

To rozwiązanie zawiera jeden projekt. Istnieją cztery pakiety NuGet zainstalowany w tym rozwiązaniu:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Należy zawsze **Microsoft.Azure.C.SharedUtility** pakietu podczas pracy z zestawem SDK. W tym przykładzie korzysta z protokołu MQTT, dlatego należy uwzględnić **Microsoft.Azure.umqtt** i **Microsoft.Azure.IoTHub.MqttTransport** pakietów (są równoważne pakietów dla protokołu AMQP i HTTPS). Ponieważ w przykładzie użyto **IoTHubClient** biblioteki, należy również uwzględnić **Microsoft.Azure.IoTHub.IoTHubClient** pakietu w rozwiązaniu.

Można znaleźć implementacji dla przykładowej aplikacji w **Centrum iothub\_klienta\_próbki\_mqtt.c** pliku źródłowego.

Następujące kroki umożliwiają przeprowadzenie co to są wymagane, aby użyć tej przykładowej aplikacji **IoTHubClient** biblioteki.

### <a name="initialize-the-library"></a>Zainicjować biblioteki

> [!NOTE]
> Przed rozpoczęciem pracy z bibliotekami, może być konieczne zainicjować niektóre specyficzne dla platformy. Na przykład jeśli planujesz używać protokołu AMQP w systemie Linux należy zainicjować biblioteki OpenSSL. Przykłady w [repozytorium GitHub](https://github.com/Azure/azure-iot-sdk-c) wywołanie funkcji narzędzia **platformy\_init** kiedy klient uruchamia i Wywołaj **platformy\_deinit**funkcja przed zakończeniem. Te funkcje są zadeklarowane w pliku nagłówka platform.h. Sprawdź definicje tych funkcji dla danej platformy docelowej w [repozytorium](https://github.com/Azure/azure-iot-sdk-c) do określenia, czy muszą zawierać dowolny kod inicjujący specyficzne dla platformy, na kliencie.

Aby rozpocząć pracę z biblioteki, najpierw przydzielić dojścia klienta Centrum IoT:

```c
if ((iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Należy przekazać kopię urządzenia parametry połączenia uzyskane z narzędzia Eksplorator urządzenia do tej funkcji. Możesz również określić protokół komunikacji ma być używany. W tym przykładzie użyto MQTT, ale AMQP i HTTPS są także opcje.

Jeśli użytkownik ma prawidłowy **Centrum IOTHUB\_klienta\_obsługi**, możesz uruchomić wywoływania interfejsów API, aby wysyłać i odbierać komunikaty z Centrum IoT.

### <a name="send-messages"></a>Wysyłanie wiadomości

Przykładowa aplikacja konfiguruje pętlę do wysyłania komunikatów do Centrum IoT. Poniższy fragment kodu:

- Tworzy komunikat.
- Dodaje właściwość do wiadomości.
- Wysyła komunikat.

Najpierw utwórz komunikat:

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

Każdorazowym wysłaniu wiadomości, można określić odwołania do funkcji wywołania zwrotnego, które jest wywoływane, gdy dane są przesyłane. W tym przykładzie jest wywoływana funkcja wywołania zwrotnego **SendConfirmationCallback**. Poniższy fragment kodu przedstawia tej funkcji wywołania zwrotnego:

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

Należy pamiętać, wywołanie **IoTHubMessage\_Destroy** działać po zakończeniu z komunikatem. Ta funkcja zwalnia zasoby przydzielone podczas tworzenia komunikatu.

### <a name="receive-messages"></a>Odbieranie wiadomości

Odbieranie wiadomości jest operacja asynchroniczna. Najpierw należy zarejestrować wywołania zwrotnego do wywołania, gdy urządzenie otrzyma wiadomość:

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

Ostatni parametr jest typu void wskaźnik do dowolne. W przykładzie jest wskaźnik do wartości całkowitej, ale może być wskaźnikiem do bardziej złożonych struktury danych. Ten parametr umożliwia działanie funkcji wywołania zwrotnego do działania na udostępniania stanu wywołującego tej funkcji.

Zarejestrowana funkcja wywołania zwrotnego jest wywoływane, gdy urządzenie otrzyma wiadomość. Ta funkcja wywołania zwrotnego pobiera:

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

Użyj **IoTHubMessage\_GetByteArray** funkcji pobierania wiadomości, który w tym przykładzie jest ciągiem.

### <a name="uninitialize-the-library"></a>Odinicjalizuj biblioteki

Gdy wszystko będzie gotowe, zdarzenia wysyłanie i odbieranie wiadomości, można uninitialize biblioteki IoT. Aby to zrobić, należy wydać następujące wywołanie funkcji:

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

To wywołanie zwolni zasoby przydzielone wcześniej przez **IoTHubClient\_CreateFromConnectionString** funkcji.

Jak widać, jest łatwy do wysyłania i odbierania wiadomości z **IoTHubClient** biblioteki. Biblioteka obsługi komunikacji z Centrum IoT, protokół, który ma być używany w tym szczegóły (z perspektywy dewelopera jest opcja prostą konfigurację).

**IoTHubClient** Biblioteka również zapewnia precyzyjną kontrolę nad jak do serializowania danych urządzenie wysyła do Centrum IoT. W niektórych przypadkach ten poziom kontroli jest korzyści, ale w innych jest szczegółów implementacji, które nie mają być związane z. Jeśli tak jest, można rozważyć przy użyciu **serializator** biblioteki, który jest opisany w następnej sekcji.

## <a name="use-the-serializer-library"></a>Korzystanie z biblioteki serializator

Koncepcyjnie **serializator** biblioteki znajduje się nad **IoTHubClient** biblioteki w zestawie SDK. Używa **IoTHubClient** biblioteki podstawowej komunikacji z Centrum IoT, ale zwiększa możliwości modelowania, które okresowo usuwają obciążenie zajmowanie serializacji komunikatu od dewelopera. Jak działa ta biblioteka najlepsze dowodzą przykładem.

Wewnątrz **serializator** folderu w [repozytorium azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c), jest **przykłady** folder, który zawiera aplikację o nazwie **simplesample\_mqtt**. Wersja systemu Windows w tym przykładzie obejmują następujące rozwiązania Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_mqtt.PNG)

> [!NOTE]
> Jeśli w programie Visual Studio 2017 r, możesz otworzyć ten projekt, należy zaakceptować z monitami, aby przekierować projektu do najnowszej wersji.

Podobnie jak w poprzednim przykładzie ta obejmuje kilka pakietów NuGet:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.IoTHub.Serializer
* Microsoft.Azure.umqtt

W tym samouczku większość tych pakietów w poprzednim przykładzie, ale **Microsoft.Azure.IoTHub.Serializer** nowego. Ten pakiet jest wymagany, gdy używasz **serializator** biblioteki.

Można znaleźć implementacji przykładowej aplikacji w **simplesample\_mqtt.c** pliku.

W poniższych sekcjach opisano części klucza w tym przykładzie.

### <a name="initialize-the-library"></a>Zainicjować biblioteki

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

Wywołanie **serializator\_init** funkcji jednorazowym wywołanie i inicjuje podstawowej biblioteki. Następnie wywołaj metodę **IoTHubClient\_LL\_CreateFromConnectionString** funkcji, która jest tego samego interfejsu API, jak w **IoTHubClient** próbki. To wywołanie ustawia parametry połączenia urządzenia (to wywołanie jest również, gdzie możesz wybrać protokół ma być używany). W tym przykładzie używa MQTT jako transportu, ale można użyć protokołu AMQP lub HTTPS.

Na koniec wywołania **Utwórz\_modelu\_wystąpienia** funkcji. **WeatherStation** jest przestrzeń nazw modelu i **ContosoAnemometer** jest nazwa modelu. Utworzone wystąpienie modelu można go uruchomić wysyłania i odbierania wiadomości. Jednak ważne jest, aby zrozumieć, jakie modelu.

### <a name="define-the-model"></a>Zdefiniuj modelu

Model w **serializator** Biblioteka definiuje wiadomości, które urządzenia mogą przesyłać dane do Centrum IoT i komunikaty o nazwie *akcje* w języku modelowania, który może odbierać. Zdefiniuj modelu przy użyciu zestawu makr C, podobnie jak w **simplesample\_mqtt** aplikacji przykładowej:

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

**Rozpocząć\_przestrzeni nazw** i **zakończenia\_przestrzeni nazw** makra zarówno przestrzeń nazw modelu jako argument. Oczekuje się, że wszystko między tymi makra jest definicji modelu lub modeli i struktury danych, korzystających z modeli.

W tym przykładzie jest pojedynczego modelu o nazwie **ContosoAnemometer**. Ten model definiuje dwie części danych, które urządzenie może wysyłać do Centrum IoT: **DeviceId** i **prędkość wiatru**. Definiuje również trzy czynności (wiadomości), które urządzenia mogą odbierać: **TurnFanOn**, **TurnFanOff**, i **SetAirResistance**. Każdy element danych ma typ, a każda akcja ma nazwę (i opcjonalnie zestaw parametrów).

Dane i akcje zdefiniowane w modelu zdefiniuj powierzchni interfejsu API, który służy do wysyłania komunikatów do Centrum IoT i odpowiadać na komunikaty wysyłane do urządzenia. Użycie tego modelu najlepiej jest rozpoznawany przez przykładowy.

### <a name="send-messages"></a>Wysyłanie wiadomości

Model Określa dane, które można wysyłać do Centrum IoT. W tym przykładzie to oznacza, jeden z elementów danych dwa zdefiniowane przy użyciu **WITH_DATA** makra. Istnieje kilka czynności wymaganych do wysyłania **DeviceId** i **prędkość wiatru** wartości do Centrum IoT. Pierwsza to ustawić danych, który chcesz wysłać:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Model został zdefiniowany wcześniej służy do ustawiania wartości przez ustawienie członkami **struktury**. Następnie serializować komunikat, który ma być wysyłane:

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

Ten kod serializuje urządzenia do chmury w buforze (odwołuje **docelowego**). Następnie wywołuje kod **sendMessage** funkcja do wysyłania komunikatu do Centrum IoT:

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


Drugi do ostatniego parametru metody **IoTHubClient\_LL\_SendEventAsync** jest odwołaniem do funkcji wywołania zwrotnego, które jest wywoływane, gdy dane są pomyślnie wysłane. W tym miejscu jest funkcja wywołania zwrotnego w przykładzie:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Drugi parametr jest wskaźnik do kontekstu użytkownika; tym samym wskaźnik przekazany do **IoTHubClient\_LL\_SendEventAsync**. W takim przypadku kontekst jest prostego licznika, ale może być dowolny.

To wszystko jest do wysyłania wiadomości urządzenia do chmury. Jedyną operacją, lewo, aby pokrywał jest jak odbierać komunikaty.

### <a name="receive-messages"></a>Odbieranie wiadomości

Odbieranie działa komunikat podobny sposób wiadomości działają w **IoTHubClient** biblioteki. Najpierw należy zarejestrować funkcję wywołania zwrotnego komunikat:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Następnie napiszesz funkcja wywołania zwrotnego, które jest wywoływane, gdy wiadomość zostanie odebrana:

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

Ten kod jest standardowy — jest taka sama dla dowolnego rozwiązania. Ta funkcja odbiera wiadomości i zajmuje się rozsyłania jej do odpowiedniej funkcji za pośrednictwem wywołania **EXECUTE\_polecenia**. Funkcja wywoływana na tym etapie zależy od definicji działań w modelu.

Po zdefiniowaniu akcji w modelu, musisz zaimplementować funkcję, która jest wywoływana, gdy urządzenie otrzyma odpowiedni komunikat. Na przykład, jeśli model definiuje tej akcji:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Zdefiniuj funkcję z tego podpisu:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Należy zwrócić uwagę, jak nazwa funkcji zgodna z nazwą akcji w modelu, a parametry funkcji takie same parametry określone dla akcji. Pierwszy parametr jest zawsze wymagane i zawiera wskaźnik do wystąpienia modelu.

Gdy urządzenie otrzyma wiadomość pasującego do tego podpisu, odpowiednich funkcja jest wywoływana. W związku z tym jako uzupełnienie konieczności dołączania schematyczny kod z **IoTHubMessage**, odbierania wiadomości jest wystarczy zdefiniować funkcję proste dla każdej akcji zdefiniowanych w modelu.

### <a name="uninitialize-the-library"></a>Odinicjalizuj biblioteki

Gdy wszystko będzie gotowe, dane wysyłanie i odbieranie wiadomości, można uninitialize biblioteki IoT:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Każda z tych trzech funkcji wyrównana z trzech opisanych wcześniej funkcji inicjowania. Te interfejsy API do wywoływania zapewnia zwolnienie wcześniej przydzielone zasoby.

## <a name="next-steps"></a>Następne kroki

W tym artykule omówione podstawy używania bibliotek w **urządzenia Azure IoT SDK dla języka C**. Go podano za mało informacji, aby zrozumieć, co jest zawarte w zestawie SDK jego architektury i jak rozpocząć pracę z usługą Windows przykłady. Kolejnym artykule nadal opis zestawu SDK przez wyjaśniający [więcej informacji na temat biblioteki IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz [Azure IoT SDK][lnk-sdks].

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
