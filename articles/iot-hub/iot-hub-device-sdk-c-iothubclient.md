---
title: Usługa Azure zestaw SDK urządzeń IoT dla języka C - usługi IoTHubClient | Dokumentacja firmy Microsoft
description: Jak używać biblioteki usługi IoTHubClient w urządzeń Azure IoT SDK dla języka C do tworzenia aplikacji urządzenia, które komunikują się z usługą IoT hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: ff766375dd9ad7cb3bbdf1ef686abb77d1206099
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797866"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Usługa Azure zestaw SDK urządzeń IoT dla języka C — więcej informacji na temat usługi IoTHubClient

[Usługa Azure zestaw SDK urządzeń IoT dla języka C](iot-hub-device-sdk-c-intro.md) jest pierwszym artykuł w tej serii wprowadzenie **zestaw SDK urządzeń Azure IoT dla języka C**. Tym artykule wyjaśniono, że istnieją dwie warstwy architektury w zestawie SDK. Z podstawową jest **usługi IoTHubClient** bibliotekę, która bezpośrednio zarządza komunikacją z usługą IoT Hub. Istnieje również **serializator** bibliotekę, która tworzy nazywaną do świadczenia usług serializacji. W tym artykule udostępnimy dodatkowe szczegóły na **usługi IoTHubClient** biblioteki.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Poprzednim artykule opisano sposób używania **usługi IoTHubClient** bibliotekę wysyłanie zdarzeń do Centrum IoT Hub i odbieranie komunikatów. W tym artykule rozszerza tej dyskusji, wyjaśniające, jak zarządzać bardziej precyzyjne *podczas* wysyłania i odbierania danych, umożliwia wprowadzenie **niskiego poziomu interfejsy API**. Wyjaśnimy również sposób dołączania właściwości zdarzenia (i pobierać je z wiadomości) przy użyciu właściwości obsługi funkcji **usługi IoTHubClient** biblioteki. Ponadto firma Microsoft udostępni dodatkowego objaśnienia różne sposoby obsługi komunikatów odebranych z usługi IoT Hub.

Artykuł zawiera przez obejmującego kilka różne tematy, w tym bardziej o poświadczenia urządzenia oraz sposobu zmiany zachowania **usługi IoTHubClient** za pomocą opcji konfiguracji.

Użyjemy **usługi IoTHubClient** przykładowych zestawach SDK, aby wyjaśnić te tematy. Jeśli chcesz wykonać te instrukcje, zobacz **iothub\_klienta\_przykładowe\_http** i **iothub\_klienta\_przykładowe\_amqp**aplikacje, które znajdują się w zestawie SDK urządzeń Azure IoT dla C. wszystko, co opisano w poniższych sekcjach przedstawiono w tych przykładach.

Możesz znaleźć [ **zestaw SDK urządzeń Azure IoT dla języka C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repozytorium i wyświetlenia jej szczegółów interfejsu API w [dokumentacja interfejsu API języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu

Poprzednim artykule opisano podstawowe operacje **usługi IotHubClient** w kontekście **iothub\_klienta\_przykładowe\_amqp** aplikacji. Na przykład wyjaśniono sposób inicjowania biblioteki przy użyciu tego kodu.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

On również opisany sposób wysyłania zdarzeń za pomocą tego wywołania funkcji.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Tego artykułu opisano również jak odbierać komunikaty, rejestrując funkcji wywołania zwrotnego.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Artykuł również pokazano, jak zwolnić zasoby przy użyciu następującego kodu.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Dostępne są funkcje pomocnika dla każdego z tych interfejsów API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Te funkcje obejmują **LL** nazwę interfejsu API. Inne **LL** część nazwy, są identyczne z ich odpowiedniki nie wszystkie parametry każda z tych funkcji. Jednak działanie tych funkcji różni się w jednym ze sposobów ważne.

Gdy wywołujesz **usługi IoTHubClient\_CreateFromConnectionString**, podstawowej biblioteki Utwórz nowy wątek, który działa w tle. Ten wątek wysyła zdarzenia do i odbiera komunikaty z usługi IoT Hub. Nie takiego wątku jest tworzony podczas pracy z **LL** interfejsów API. Tworzenie wątku w tle jest wygoda dla dewelopera. Nie trzeba martwić się o jawnie wysyłanie zdarzeń i odbieranie komunikatów z usługi IoT Hub — wykonywane automatycznie w tle. Z kolei **LL** interfejsów API dają jawną kontrolę nad komunikacji z usługą IoT Hub, jeśli jest potrzebna.

Aby lepiej zrozumieć tę koncepcję, Spójrzmy na przykład:

Gdy wywołujesz **usługi IoTHubClient\_SendEventAsync**, co faktycznie robisz to umieszczenie zdarzenia w buforze. Wątek w tle tworzone po wywołaniu **usługi IoTHubClient\_CreateFromConnectionString** stale monitoruje tego buforu i wysyła dane, które zawiera do usługi IoT Hub. Dzieje się w tle w tym samym czasie, że główny wątek jest wykonywania innych zadań.

Podobnie, po dokonaniu rejestracji funkcję wywołania zwrotnego dla komunikatów za pomocą **usługi IoTHubClient\_SetMessageCallback**, one poinstruowanie zestawem SDK, aby mieć wywołania funkcji wywołania zwrotnego, gdy komunikat jest wątku w tle Odebrano, niezależnie od wątku głównego.

**LL** interfejsy API nie należy tworzyć wątku w tle. Zamiast tego nowy interfejs API musi być wywoływana jawnie wysyłać i odbierać dane z usługi IoT Hub. Jest to zaprezentowane w poniższym przykładzie.

**Iothub\_klienta\_przykładowe\_http** aplikacji, który znajduje się w zestawie SDK pokazuje interfejsy API niższego poziomu. W tym przykładzie firma Microsoft wysyła zdarzenia do Centrum IoT przy użyciu kodu, takie jak następujące:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Pierwsze trzy wiersze tworzą wiadomość, a ostatni wiersz wysyła zdarzenie. Jednakże jak wspomniano wcześniej, wysyła zdarzenie oznacza, że dane po prostu znajduje się w buforze. Nic nie jest przesyłany w sieci podczas nazywamy **usługi IoTHubClient\_LL\_SendEventAsync**. Z kolei faktycznie przychodzących danych do usługi IoT Hub, musisz wywołać **usługi IoTHubClient\_LL\_DoWork**, jak w tym przykładzie:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ten kod (z **iothub\_klienta\_przykładowe\_http** aplikacji) wielokrotnie wywołuje **usługi IoTHubClient\_LL\_DoWork**. Każdorazowo **usługi IoTHubClient\_LL\_DoWork** jest wywoływana, wysyła niektóre zdarzenia z buforu do usługi IoT Hub i pobiera wiadomość w kolejce są wysyłane do urządzenia. Ostatnim przypadku oznacza to, że jeśli firma Microsoft zarejestrowana funkcja wywołania zwrotnego dla wiadomości, wywołanie zwrotne jest wywoływane (przy założeniu, że wszystkie komunikaty są kolejkowane w górę). Firma Microsoft będzie funkcję wywołania zwrotnego zostały zarejestrowane przy użyciu kodu, takie jak następujące:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Przyczyna, **usługi IoTHubClient\_LL\_DoWork** jest często określany mianem pętli jest to, że zawsze jest wywoływana, wysyła *niektóre* buforowanych zdarzeń do Centrum IoT i pobiera *następnego* wiadomości w kolejce dla urządzenia. Każde wywołanie nie jest gwarantowane do wysyłania zdarzeń wszystkie buforowane lub aby pobrać wszystkie wiadomości w kolejce. Jeśli chcesz wysłać wszystkie zdarzenia w buforze, a następnie kontynuuj na inne procesy przetwarzania można zastąpić tę pętlę kodu, takie jak następujące:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ten kod wywołuje **usługi IoTHubClient\_LL\_DoWork** dopóki wszystkie zdarzenia w buforze zostały wysłane do Centrum IoT Hub. Należy zauważyć, że nie również oznacza Odebrano wszystkie wiadomości w kolejce. Przyczyną jest czy sprawdzanie komunikatów "all" nie jest jako deterministyczny akcji. Co się stanie po pobraniu "all", wiadomości, ale następnie inny będą wysyłane do urządzenia bezpośrednio po? Lepszy sposób radzenia sobie z tym, który jest zaplanowany limitu czasu. Na przykład funkcja wywołania zwrotnego komunikat można zresetować czasomierz, za każdym razem, gdy jest wywoływana. Następnie można napisać logikę kontynuować przetwarzanie, jeśli na przykład, odebrano żadnych komunikatów w ciągu ostatnich *X* sekund.

Gdy jesteś ingressing gotowego zdarzenia i odbieranie komunikatów, upewnij się wywołać z odpowiednimi funkcjami, aby wyczyścić zasoby.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Zasadniczo jest tylko jeden zestaw interfejsów API do wysyłania i odbierania danych z wątku w tle i inny zestaw interfejsów API, które działa tak samo bez wątku w tle. Wielu deweloperów lepiej niż — wszystkie interfejsy API, ale niskiego poziomu interfejsy API są przydatne w przypadku, gdy deweloper chce jawną kontrolę nad tym transmisji w sieci. Na przykład niektóre urządzenia zbieranie danych czasu i zdarzeń tylko ruch przychodzący w określonych odstępach czasu (na przykład raz na godzinę lub raz dziennie). Interfejsy API niższego poziomu zapewniają możliwość do jawnie kontroli, wysyłania i odbierania danych z usługi IoT Hub. Inne osoby będą po prostu preferujesz prostotę, zapewniające interfejsów API niższego poziomu. Wszystko, co się dzieje w wątku głównym, a nie kilka sytuacji pracy w tle.

Niezależnie od tego modelu, możesz wybrać, musi znajdować się spójne interfejsy API, której używasz. Po uruchomieniu przez wywołanie metody **usługi IoTHubClient\_LL\_CreateFromConnectionString**, pamiętaj, że używasz tylko odpowiednie interfejsy API niskiego poziomu dla żadnych dalszych działań:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

Odwrotny jest również wartość true. Jeśli na początku zasubskrybujesz **usługi IoTHubClient\_CreateFromConnectionString**, następnie użyć innego niż — wszystkie interfejsy API dla dowolnego dodatkowego przetwarzania.

Na urządzeniu usługi Azure IoT SDK dla języka C, zobacz **iothub\_klienta\_przykładowe\_http** aplikacji pełny przykład interfejsów API niższego poziomu. **Iothub\_klienta\_przykładowe\_amqp** aplikacji można odwoływać się pełny przykład z innych — wszystkie interfejsy API.

## <a name="property-handling"></a>Obsługa właściwości

Do tej pory Opisaliśmy wysłanie danych Firma Microsoft już został odnosi do treści wiadomości. Na przykład rozważmy ten kod:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

W tym przykładzie wysyła komunikat do Centrum IoT Hub z tekstu "Hello World". Usługa IoT Hub umożliwia także właściwości, aby być dołączony do każdej wiadomości. Właściwości to pary nazwa/wartość, które mogą być dołączone do wiadomości. Na przykład możemy zmodyfikować poprzedni kod do dołączenia właściwości do wiadomości:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Rozpoczniemy pracę przez wywołanie metody **IoTHubMessage\_właściwości** i przekazanie do niej dojście naszej wiadomości. Otrzymujemy ponownie **mapy\_obsługi** odwołania, która umożliwia nam rozpocząć dodawanie właściwości. Drugim odbywa się przez wywołanie metody **mapy\_AddOrUpdate**, który przyjmuje odwołanie do mapy\_UCHWYT, nazwa właściwości i wartości właściwości. Dzięki temu interfejsowi API możemy dodać dowolną liczbę właściwości, jak firma Microsoft.

Gdy zdarzenia są odczytywane z **usługi Event Hubs**, odbiorca może wyliczyć właściwości i pobrać odpowiadające im wartości. Na przykład na platformie .NET to będzie można osiągnąć, uzyskując dostęp do [kolekcji właściwości dla obiektu EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

W poprzednim przykładzie możemy teraz dołączanie właściwości do zdarzenia, które możemy wysłać do usługi IoT Hub. Właściwości można dołączyć w taki sposób, aby komunikatów odebranych z usługi IoT Hub. Jeśli chcesz pobrać właściwości z wiadomości, możemy użyć następującego kodu w naszej funkcji wywołania zwrotnego komunikat:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Wywołanie **IoTHubMessage\_właściwości** zwraca **mapy\_obsługi** odwołania. Następnie przekazanie tego odwołania do **mapy\_GetInternals** uzyskać odwołanie do tablicy pary nazwa/wartość (a także liczbę właściwości). W tym momencie jest wyliczanie właściwości, aby uzyskać dostęp do wartości, które chcemy polegać.

Nie trzeba korzystać z właściwości w aplikacji. Jednakże, jeśli trzeba je ustawić dla zdarzenia lub pobrać je z wiadomości, **usługi IoTHubClient** Biblioteka ułatwia.

## <a name="message-handling"></a>Obsługa komunikatów

Jak wspomniano wcześniej, po nadejściu wiadomości z usługi IoT Hub **usługi IoTHubClient** Biblioteka odpowiada za pomocą funkcji zarejestrowanego wywołania zwrotnego. Brak parametru zwrotnego tej funkcji, która wymaga dodatkowego objaśnienia. Poniżej przedstawiono fragment funkcji wywołania zwrotnego w **iothub\_klienta\_przykładowe\_http** przykładowej aplikacji:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Należy zauważyć, że typ zwracany jest **IOTHUBMESSAGE\_DYSPOZYCJI\_wynik** , w tym przypadku zostanie zwrócona **IOTHUBMESSAGE\_ZAAKCEPTOWANO**. Istnieją inne wartości przekierujemy z tej funkcji, które zmieniają sposób, w jaki **usługi IoTHubClient** biblioteki reaguje na wywołanie zwrotne komunikatu. Poniżej przedstawiono opcje.

* **IOTHUBMESSAGE\_ZAAKCEPTOWANO** — komunikat został pomyślnie przetworzony. **Usługi IoTHubClient** biblioteki nie będzie wywołania funkcji wywołania zwrotnego ponownie, używając tego samego komunikatu.

* **IOTHUBMESSAGE\_ODRZUCONY** — wiadomość nie została przetworzona, i jest używany przede wszystkim wtedy w tym celu w przyszłości. **Usługi IoTHubClient** biblioteki nie należy wywołać funkcję wywołania zwrotnego ponownie, używając tego samego komunikatu.

* **IOTHUBMESSAGE\_ABANDONED** — wiadomość nie została przetworzona pomyślnie, ale **usługi IoTHubClient** biblioteki należy wywołać funkcję wywołania zwrotnego ponownie, używając tego samego komunikatu.

Dla dwóch pierwszych zwrócić kody, **usługi IoTHubClient** biblioteki wysyła komunikat do Centrum IoT Hub informujący, że usuwane z kolejki urządzenia i nie dostarczono ponownie wiadomość. Efektem sieciowym jest taka sama (komunikat jest usuwany z kolejki urządzenia), ale nadal jest rejestrowana tego, czy komunikat został zaakceptowany lub odrzucony.  Rejestrowanie wykonywania tego rozróżnienia przydaje się do nadawców wiadomości, w którym można nasłuchiwać opinii i Dowiedz się, jeśli urządzenie ma zaakceptowany lub odrzucony określonej wiadomości.

W ostatnim przypadku również wysłaniu komunikatu do usługi IoT Hub, ale oznacza to, że komunikat powinien być dostarczony ponownie. Zwykle będzie porzucić wiadomości, jeśli wystąpi błąd, ale chcesz spróbować ponownie przetworzyć komunikatu. Odrzuca wiadomości jest odpowiednie w przypadku wystąpienia nieodwracalnego błędu (lub po prostu określić, że użytkownik nie chce przetworzyć komunikatu).

W każdym przypadku należy pamiętać o różnych kodów powrotnych, dzięki czemu może wywołać zachowanie z **usługi IoTHubClient** biblioteki.

## <a name="alternate-device-credentials"></a>Poświadczenia alternatywne urządzenia

Jak wyjaśniono wcześniej, najpierw musisz podczas pracy z **usługi IoTHubClient** biblioteki jest uzyskanie **IOTHUB\_klienta\_obsługi** za pomocą wywołania podobny do następującego:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty **usługi IoTHubClient\_CreateFromConnectionString** są parametry połączenia urządzenia i parametr, który określa protokół, używamy w celu komunikowania się z usługą IoT Hub. Parametry połączenia urządzenia ma format, który pojawia się w następujący sposób:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Istnieją cztery rodzajów informacji w tym ciągu: Nazwa centrum IoT, sufiks usługi IoT Hub, identyfikator urządzenia i klucza dostępu współdzielonego. Możesz uzyskać w pełni kwalifikowana nazwa domeny (FQDN) usługi IoT hub podczas tworzenia wystąpienia usługi IoT hub w witrynie Azure portal — daje nazwę Centrum IoT (pierwsza część nazwy FQDN) i sufiks Centrum IoT (rest FQDN). Pobierz identyfikator urządzenia i klucz dostępu współdzielonego podczas rejestrowania urządzenia z usługą IoT Hub (zgodnie z opisem w [poprzednim artykule](iot-hub-device-sdk-c-intro.md)).

**Usługi IoTHubClient\_CreateFromConnectionString** zapewnia jeden sposób na inicjowanie biblioteki. Jeśli wolisz, możesz utworzyć nową **IOTHUB\_klienta\_obsługi** przy użyciu tych parametrów, a nie parametry połączenia urządzenia. Jest to osiągane przy użyciu następującego kodu:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

To tak samo jak w ramach **usługi IoTHubClient\_CreateFromConnectionString**.

Może wydawać się oczywiste, że chcesz użyć **usługi IoTHubClient\_CreateFromConnectionString** zamiast tej pełniejszy metody inicjowania. Należy pamiętać, jednak podczas rejestrowania urządzenia w usłudze IoT Hub możesz pobrać to identyfikator urządzenia i klucz urządzenia (nie ciąg połączenia). *Eksplorator urządzeń* narzędzia SDK wprowadzona w [poprzednim artykule](iot-hub-device-sdk-c-intro.md) korzysta z bibliotek w **zestawu SDK usługi Azure IoT** Aby utworzyć parametry połączenia urządzenia na podstawie Identyfikatora urządzenia , klucz urządzenia i nazwy hosta usługi IoT Hub. Dlatego wywołanie **usługi IoTHubClient\_LL\_Utwórz** może być preferowana, ponieważ krok generowania parametrów połączenia. Użyj, niezależnie od metody jest wygodne.

## <a name="configuration-options"></a>Opcje konfiguracji

Do tej pory wszystkie elementy opisane o sposobie **usługi IoTHubClient** działa biblioteki odzwierciedla jej zachowanie domyślne. Istnieje jednak kilka opcji, które można ustawić, aby zmienić sposób działania biblioteki. Jest to realizowane przy użyciu **usługi IoTHubClient\_LL\_SetOption** interfejsu API. Rozważmy następujący przykład:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Istnieje kilka opcji, które są często używane:

* **SetBatching** (wartość logiczna) — Jeśli **true**, wówczas dane wysyłane do usługi IoT Hub jest wysyłany w partii. Jeśli **false**, a następnie komunikaty są wysyłane indywidualnie. Wartość domyślna to **false**. Przetwarzanie wsadowe za pośrednictwem protokołu AMQP / AMQP WS, a także dodawanie właściwości systemu na komunikaty D2C jest obsługiwana.

* **Limit czasu** (unsigned int) — ta wartość jest reprezentowana w milisekundach. Jeśli wysyła żądanie HTTPS lub odbierania odpowiedzi trwa dłużej niż ten czas, a następnie połączenie zostanie rozłączone.

Opcja łączenia we wsady jest ważna. Domyślnie zdarzenia ingresses biblioteki indywidualnie (jest to pojedyncze zdarzenie, niezależnie od przekazania do **usługi IoTHubClient\_LL\_SendEventAsync**). Jeśli opcja przetwarzania wsadowego jest **true**, biblioteka zbiera dowolną liczbę zdarzeń, jak to możliwe z buforu (maksymalnie maksymalny rozmiar wiadomości z wykorzystaniem usługi IoT Hub).  Batch zdarzenia są wysyłane do usługi IoT Hub w jednym wywołaniu HTTPS (poszczególne zdarzenia są połączone w tablicy JSON). Włączanie przetwarzanie wsadowe zwykle powoduje wzrost wydajności dużych, ponieważ jesteś zmniejszenia rund sieciowych. Ponadto znacząco zmniejsza przepustowości, ponieważ w przypadku wysyłania jeden zestaw nagłówków protokołu HTTPS z usługą batch zdarzeń, a nie zestaw nagłówki dla poszczególnych zdarzeń. Chyba, że masz powód, aby robić, zazwyczaj należy włączyć, przetwarzanie wsadowe.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano szczegółowo zachowanie **usługi IoTHubClient** Biblioteka odnaleziona w **zestaw SDK urządzeń Azure IoT dla języka C**. Dzięki tym informacjom powinien dysponować dobrą znajomością możliwości **usługi IoTHubClient** biblioteki. Drugi artykuł w tej serii jest [zestaw SDK urządzeń Azure IoT dla języka C - serializator](iot-hub-device-sdk-c-serializer.md), zapewniającą podobne szczegółów na **serializator** biblioteki.

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz [wdrażanie sztucznej Inteligencji na urządzenia brzegowe za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
