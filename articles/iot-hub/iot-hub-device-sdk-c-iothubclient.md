---
title: Azure urządzenia IoT zestawu SDK dla języka C - IoTHubClient | Dokumentacja firmy Microsoft
description: Jak używać biblioteki IoTHubClient na urządzeniu Azure IoT SDK dla języka C do tworzenia aplikacji urządzenia, które komunikują się z Centrum IoT.
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 828cf2bf-999d-4b8a-8a28-c7c901629600
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: 0fa07b2a785bd7d1a26924918e4428fd420e1547
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure urządzenia IoT zestawu SDK dla języka C — więcej informacji na temat IoTHubClient
[Najpierw artykuł](iot-hub-device-sdk-c-intro.md) w tej serii wprowadzone **urządzenia Azure IoT SDK dla języka C**. Tym artykule wyjaśniono, że istnieją dwie warstwy architektury w zestawie SDK. Na podstawowym jest **IoTHubClient** biblioteki, który bezpośrednio prowadzi komunikację z Centrum IoT. Istnieje również **serializator** biblioteki, która tworzy znajdujący się na świadczenie usług serializacji. W tym artykule udostępnimy dodatkowych szczegółów na **IoTHubClient** biblioteki.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Poprzednim artykule opisano sposób użycia **IoTHubClient** biblioteki do wysyłania zdarzeń do Centrum IoT i odbierania wiadomości. W tym artykule rozszerza tej dyskusji, wyjaśniający, bardziej precyzyjne zarządzanie *podczas* wysyłania i odbierania danych, wprowadzenie do **interfejsów API niższego poziomu**. Prezentujemy również zasady do dołączania właściwości zdarzenia (i pobrać wiadomości) przy użyciu właściwości obsługi funkcji w **IoTHubClient** biblioteki. Ponadto firma Microsoft udostępni dodatkowe objaśnienia dotyczące obsługi komunikatów odebranych z Centrum IoT na różne sposoby.

Artykuł zawiera przez kilka dodatkowych tematy, w tym więcej o poświadczenia urządzenia oraz sposobu zmiany zachowanie obejmujących **IoTHubClient** za pomocą opcji konfiguracji.

Użyjemy **IoTHubClient** te tematy wyjaśniają próbek zestawu SDK. Jeśli chcesz z niego skorzystać, zobacz **Centrum iothub\_klienta\_próbki\_http** i **Centrum iothub\_klienta\_próbki\_amqp**aplikacji, które znajdują się w urządzeniu Azure IoT SDK dla C. wszystkie elementy opisane w poniższych sekcjach przedstawiono w te przykłady.

Można znaleźć [ **urządzenia Azure IoT SDK dla języka C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repozytorium i wyświetlenia jej szczegółów interfejsu API w [dokumentacja interfejsu API C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu
Poprzednim artykule opisano podstawowe operacje **IotHubClient** w kontekście **Centrum iothub\_klienta\_próbki\_amqp** aplikacji. Na przykład jego objaśniono, w jaki można zainicjować biblioteki przy użyciu tego kodu.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

On również opisany sposób wysyłania zdarzeń za pomocą tego wywołania funkcji.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Tego artykułu opisano również jak odbierać komunikaty, rejestrując funkcję wywołania zwrotnego.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Artykuł również pokazano, jak można zwolnić zasobów przy użyciu następującego kodu.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Istnieją jednak funkcji pomocnika do każdego z poniższych interfejsów API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_zniszczyć

Wszystkie funkcje te obejmują "Wszystko" w nazwie interfejsu API. Inną niż parametry każda z tych funkcji są takie same jak ich odpowiedniki nie wszystko. Jednak działanie tych funkcji różni się w jednym ze sposobów ważne.

Podczas wywoływania **IoTHubClient\_CreateFromConnectionString**, podstawowe biblioteki utworzyć nowego wątku, który działa w tle. Wysyła zdarzenia do tego wątku i odbiera komunikaty z Centrum IoT. Nie takiego wątku jest tworzony podczas pracy z interfejsów API "wszystko". Tworzenie wątku w tle jest wygodne do deweloperów. Nie trzeba martwić jawnie wysyłanie zdarzeń i odbieranie komunikatów z Centrum IoT — wykonywane automatycznie w tle. Z kolei "Wszystko" interfejsów API zapewniają jawne kontroluje komunikację z Centrum IoT, jeśli zajdzie taka potrzeba.

Aby lepiej zrozumieć to, Przyjrzyjmy się przykładem:

Podczas wywoływania **IoTHubClient\_SendEventAsync**, co faktycznie robimy obciążające zdarzenia w buforze. Wątek w tle tworzone podczas wywoływania **IoTHubClient\_CreateFromConnectionString** stale monitoruje buforu i wysyła on żadnych danych do Centrum IoT. Dzieje się w tle w tym samym czasie, że wątku głównego jest wykonywać inne zadania.

Podobnie po rejestracji funkcję wywołania zwrotnego dla wiadomości przy użyciu **IoTHubClient\_SetMessageCallback**, jest poinstruowanie zestaw SDK, aby mieć wywołania funkcji wywołania zwrotnego, gdy komunikat jest wątku w tle Odebrano, niezależnie od wątku głównego.

"Wszystkie" interfejsów API nie należy tworzyć wątku w tle. Zamiast tego nowy interfejs API musi zostać wywołany jawnie wysyłanie i odbieranie danych z Centrum IoT. Jest to pokazano w poniższym przykładzie.

**Centrum iothub\_klienta\_próbki\_http** aplikacji, który znajduje się w zestawie SDK pokazuje interfejsów API niższego poziomu. W tym przykładzie mamy wysyłania zdarzeń do Centrum IoT z kodem, takie jak następujące:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Pierwsze trzy wiersze utworzyć komunikat, a ostatni wiersz wysyła zdarzenia. Jednak jak już wspomniano, "Wysyłanie" zdarzenie oznacza, że dane są po prostu umieszczane w buforze. Nic nie jest przesyłane w sieci, gdy taki stan nazywa się **IoTHubClient\_LL\_SendEventAsync**. Aby rzeczywiście transferu danych do Centrum IoT, należy wywołać **IoTHubClient\_LL\_DoWork**w tym przykładzie:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ten kod (z **Centrum iothub\_klienta\_próbki\_http** aplikacji) wielokrotnie wywołuje **IoTHubClient\_LL\_DoWork**. Zawsze **IoTHubClient\_LL\_DoWork** jest wywoływana, wysyła niektórych zdarzeń z buforu do Centrum IoT i pobiera ona wiadomość w kolejce są wysyłane do urządzenia. Drugim przypadku oznacza to, że jeśli firma Microsoft zarejestrowana funkcja wywołania zwrotnego dla wiadomości, następnie wywołania zwrotnego została wywołana (przy założeniu, że komunikaty są umieszczone w kolejce). Firma Microsoft będzie zarejestrowanych funkcję wywołania zwrotnego z kodem, takie jak następujące:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Powód który **IoTHubClient\_LL\_DoWork** jest często nazywana pętli jest to, że zawsze jest nazywana, wysyła *niektórych* buforowane zdarzenia do Centrum IoT i pobiera *następne* wiadomości w kolejce dla danego urządzenia. Każde wywołanie nie jest gwarantowana do wysłania wszystkich buforowanych zdarzeń lub pobranie wszystkich wiadomości w kolejce. Jeśli chcesz wysłać wszystkie zdarzenia w buforze, a następnie kontynuuj na inne przetwarzania można zastąpić tę pętlę kodu, takie jak następujące:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ten kod wywołuje **IoTHubClient\_LL\_DoWork** dopóki wszystkie zdarzenia w buforze zostały wysłane do Centrum IoT. Należy pamiętać, że nie również oznacza otrzymanie wszystkie wiadomości w kolejce. Część przyczynę jest, że sprawdzanie komunikatów "all" nie jest deterministyczna jako akcję. Co się stanie, jeśli pobrać "wszystkie" wiadomości, ale następnie inny jest wysyłane do urządzenia bezpośrednio po? Lepszy sposób postępowania w przypadku z zaprogramowanych limitu czasu. Na przykład funkcja wywołania zwrotnego komunikat można zresetować czasomierz za każdym razem, gdy jest wywoływana. Następnie można napisać logiki, aby kontynuować przetwarzanie, jeśli na przykład żadnych komunikatów nie odebrano w ciągu ostatnich *X* sekund.

Gdy jest ingressing gotowego zdarzenia i odbieranie komunikatów, należy wywołać funkcję odpowiednie, aby wyczyścić zasoby.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Zasadniczo jest tylko jeden zestaw interfejsów API, aby wysyłać i odbierać dane z wątku w tle i inny zestaw interfejsów API, które jest równoznaczne bez wątku w tle. Wiele deweloperzy mogą preferować z systemem innym niż — wszystkie interfejsy API, ale interfejsów API niższego poziomu są przydatne, jeśli deweloper chce jawną kontrolę nad transmisji w sieci. Na przykład niektóre urządzenia zbieranie danych przez czasu i zdarzeń wejściowych tylko w określonych odstępach czasu (na przykład, jeden raz na godzinę lub raz dziennie). Interfejsy API niższego poziomu dać możliwość kontroli jawnie, wysyłania i odbierania danych z Centrum IoT. Inne po prostu preferowane prostotę, które zapewniają interfejsów API niższego poziomu. Wszystko, co się stanie w wątku głównego zamiast niektórych pracy w toku w tle.

Niezależnie od tego modelu, należy wybrać, pamiętaj zachować spójność interfejsów API, którego używasz. Po uruchomieniu przez wywołanie metody **IoTHubClient\_LL\_CreateFromConnectionString**, należy korzystać jedynie odpowiednich interfejsów API niższego poziomu kolejnych pracę:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_zniszczyć
* IoTHubClient\_LL\_DoWork

Wartość true, jest również odwrotny. Jeśli na początku zasubskrybujesz **IoTHubClient\_CreateFromConnectionString**, następnie użyć z systemem innym niż — wszystkie interfejsy API dla dowolnego dodatkowego przetwarzania.

Na urządzeniu Azure IoT SDK dla języka C, zobacz **Centrum iothub\_klienta\_próbki\_http** aplikacji pełny przykład interfejsów API niższego poziomu. **Centrum iothub\_klienta\_próbki\_amqp** aplikacji można odwoływać się na przykład Pełna z innych niż — wszystkie interfejsy API.

## <a name="property-handling"></a>Obsługa właściwości
Do tej pory gdy mamy już opisane wysyłanie danych, firma Microsoft już został odwołujących się do treści wiadomości. Rozważmy na przykład ten kod:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

W tym przykładzie wysyła komunikat do Centrum IoT na tekst "Hello World". Centrum IoT umożliwia także właściwości, które mają być dołączone do każdej wiadomości. Właściwości są pary nazwa/wartość, które można dołączyć do wiadomości. Możemy na przykład zmodyfikować poprzedni kod można dołączyć właściwości komunikat:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Rozpoczniemy wywołując **IoTHubMessage\_właściwości** i przekazanie jej przez dojście wiadomości. Co mamy wrócić jest **mapy\_obsługi** odwołania, który pozwala rozpocząć dodawanie właściwości. Drugie odbywa się przez wywołanie metody **mapy\_AddOrUpdate**, który przyjmuje odwołanie do mapy\_UCHWYTU, nazwa właściwości i wartości właściwości. W tym interfejsie API można dodać dowolną liczbę właściwości, jak firma Microsoft.

Gdy zdarzenie zostanie odczytany z **usługi Event Hubs**, odbiorca może wyliczenia właściwości i pobieranie ich wartości. Na przykład w środowisku .NET to może osiągnąć, uzyskując dostęp do [kolekcji właściwości w obiekcie EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

W poprzednim przykładzie możemy Cię dołączanie właściwości do zdarzenia, możemy wysyłać do Centrum IoT. Ponadto można dołączyć właściwości do komunikatów odebranych z Centrum IoT. Jeśli chcemy, aby pobrać właściwości z komunikatu, możemy użyć następującego kodu w naszej funkcji wywołania zwrotnego komunikat:

```
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

Wywołanie **IoTHubMessage\_właściwości** zwraca **mapy\_obsługi** odwołania. Następnie jest przekazywana odwołujące się do **mapy\_GetInternals** uzyskać odwołanie do tablicy pary nazwa/wartość (a także liczbę właściwości). W tym momencie jest polegać wyliczania właściwości, aby uzyskać dostęp do wartości, którą chcemy udostępnić.

Nie trzeba używać właściwości w aplikacji. Jednak, aby ustawić je na zdarzenia lub pobrać je z wiadomości, **IoTHubClient** biblioteki ułatwia.

## <a name="message-handling"></a>Obsługa komunikatów
Jak wspomniano wcześniej, gdy nadchodzą komunikaty z Centrum IoT **IoTHubClient** Biblioteka odpowiada za pomocą zarejestrowana funkcja wywołania zwrotnego. Brak parametru zwrotnego tej funkcji, która wymaga dodatkowych wyjaśnień. Oto fragment funkcja wywołania zwrotnego w **Centrum iothub\_klienta\_próbki\_http** aplikacji przykładowej:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Należy pamiętać, że typ zwracany jest **IOTHUBMESSAGE\_DYSPOZYCJI\_wynik** w tym przypadku zostanie zwrócona **IOTHUBMESSAGE\_ZAAKCEPTOWANE**. Istnieją inne wartości firma Microsoft może zwracać z tej funkcji, które zmieniają sposób **IoTHubClient** biblioteki reaguje na wywołanie zwrotne komunikatu. Poniżej przedstawiono opcje.

* **IOTHUBMESSAGE\_ZAAKCEPTOWANE** — komunikat został pomyślnie przetworzony. **IoTHubClient** biblioteki nie wywoła funkcję wywołania zwrotnego ponownie, podając tę samą wiadomość.
* **IOTHUBMESSAGE\_ODRZUCONE** — wiadomość nie została przetworzona, i chcesz to zrobić w przyszłości nie istnieje. **IoTHubClient** biblioteki nie powinna wywołać funkcję wywołania zwrotnego ponownie, podając tę samą wiadomość.
* **IOTHUBMESSAGE\_ABANDONED** — wiadomość nie została przetworzona pomyślnie, ale **IoTHubClient** biblioteki należy wywołać funkcję wywołania zwrotnego ponownie, podając tę samą wiadomość.

Dla dwóch pierwszych zwraca kody, **IoTHubClient** biblioteki wysyła komunikat do Centrum IoT informujący, że usuwane z kolejki urządzenia i nie można dostarczyć ponownie wiadomości. Net efekt jest taki sam (komunikat jest usuwany z kolejki urządzenia), ale nadal jest rejestrowany czy zaakceptowane lub odrzucone wiadomości.  Rejestrowanie tego rozróżnienie przydaje się do nadawców wiadomości, którzy mogą nasłuchiwać opinii i sprawdzić, czy urządzenie ma zaakceptowane lub odrzucone danego komunikatu.

W przypadku ostatniego komunikatu jest również przesyłany do Centrum IoT, ale wskazuje, czy komunikat powinien być dostarczony ponownie. Zwykle będzie abandon komunikat, jeśli wystąpi błąd, ale chcesz spróbować ponownie przetworzyć komunikatu. Z kolei odrzuca wiadomości jest odpowiednia, w przypadku wystąpienia nieodwracalnego błędu (lub po prostu zdecydować się, że nie chcesz przetworzyć komunikatu).

W każdym przypadku należy pamiętać różnych kodów powrotnych, dzięki czemu można wywołać z zachowaniem **IoTHubClient** biblioteki.

## <a name="alternate-device-credentials"></a>Poświadczenia alternatywne urządzenia
Jak opisano wcześniej, najpierw musisz podczas pracy z **IoTHubClient** biblioteki jest uzyskanie **Centrum IOTHUB\_klienta\_obsługi** przy użyciu wywołania, takie jak następujące:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenty **IoTHubClient\_CreateFromConnectionString** są parametry połączenia urządzenia i parametrem, który wskazuje protokołu używamy w celu komunikowania się z Centrum IoT. Ciąg połączenia urządzenia ma format, który wygląda następująco:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Istnieją cztery elementy informacji w tym ciągu: Nazwa centrum IoT, sufiks Centrum IoT identyfikator urządzenia i udostępniony klucz dostępu. W pełni kwalifikowaną nazwę (FQDN) można uzyskać z Centrum IoT podczas tworzenia wystąpienia Centrum IoT w portalu Azure — dzięki temu można nazwę Centrum IoT (pierwsza część nazwy FQDN) i sufiksu Centrum IoT (reszty nazwy FQDN). Pobierz identyfikator urządzenia i klucza dostępu współdzielonego podczas rejestrowania urządzenia z Centrum IoT (zgodnie z opisem w [poprzednim artykule](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** udostępnia jeden sposób zainicjować biblioteki. Jeśli wolisz, możesz utworzyć nową **Centrum IOTHUB\_klienta\_obsługi** przy użyciu tych parametrów, a nie parametry połączenia urządzenia. Jest to osiągane następującym kodem:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Wykonuje to samo jako **IoTHubClient\_CreateFromConnectionString**.

Może wydawać się oczywiste, czy chcesz użyć **IoTHubClient\_CreateFromConnectionString** zamiast tego pełniejsze metody inicjowania. Należy pamiętać, że podczas rejestrowania urządzenia w Centrum IoT można pobrać jest identyfikator urządzenia i klucz urządzenia (nie w ciągu połączenia). *Explorer urządzenia* narzędzia zestawu SDK wprowadzone w systemie [poprzednim artykule](iot-hub-device-sdk-c-intro.md) korzysta z bibliotek w **zestawu SDK usług Azure IoT** Aby utworzyć parametry połączenia urządzenia z identyfikator urządzenia , klucz urządzenia i nazwy hosta Centrum IoT. Dlatego wywołanie **IoTHubClient\_LL\_Utwórz** może być preferowana, ponieważ zapisuje kroku Generowanie ciągu połączenia. Należy użyć metody jest wygodne.

## <a name="configuration-options"></a>Opcje konfiguracji
Do tej pory wszystkie elementy opisane o sposobie **IoTHubClient** works biblioteki odzwierciedla jego zachowanie domyślne. Istnieje jednak kilka opcji, które można ustawić, aby zmienić sposób działania biblioteki. Jest to osiągane przez wykorzystanie **IoTHubClient\_LL\_SetOption** interfejsu API. Należy wziąć pod uwagę w tym przykładzie:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Istnieje kilka opcji, które są powszechnie stosowane:

* **SetBatching** (wartość logiczna) — Jeśli **true**, dane wysyłane do Centrum IoT jest wysyłane w partiach. Jeśli **false**, komunikaty są wysyłane pojedynczo. Wartość domyślna to **false**. Należy pamiętać, że **SetBatching** dotyczy tylko protokołu HTTPS, a nie MQTT lub AMQP protokołów.
* **Limit czasu** (unsigned int) — ta wartość jest reprezentowana w milisekundach. Jeśli wysyła żądanie HTTPS lub odebrania odpowiedzi trwa dłużej niż ten czas, a następnie upłynie limit czasu połączenia.

Opcja łączenia we wsady jest ważna. Domyślnie zdarzenia ingresses biblioteki indywidualnie (pojedyncze zdarzenie jest niezależnie od przekazania do **IoTHubClient\_LL\_SendEventAsync**). Jeśli opcja łączenia we wsady jest **true**, biblioteki gromadzi zdarzenia tyle jak go z buforu (maksymalnie maksymalny rozmiar wiadomości akceptujące Centrum IoT).  Wsadowe zdarzenia są wysyłane do Centrum IoT w pojedynczym wywołaniu HTTPS (zdarzenia są połączone w tablicy JSON). Włączanie przetwarzanie wsadowe zwykle powoduje duża wydajność, ponieważ jest zmniejszenie przechodzenia do sieci. Ponadto znacząco zmniejsza przepustowości od wysyłania jeden zestaw nagłówków protokołu HTTPS z partii zdarzeń, a nie zestaw nagłówki dla każdego wybranego zdarzenia. Chyba że masz powód, aby nie zwykle należy włączyć przetwarzanie wsadowe.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano szczegółowo zachowanie **IoTHubClient** Biblioteka odnaleziona w **urządzenia Azure IoT SDK dla języka C**. Dzięki tym informacjom powinien dysponować dobrą znajomością możliwości **IoTHubClient** biblioteki. [Kolejnym artykule](iot-hub-device-sdk-c-serializer.md) zawiera szczegółowe informacje podobne na **serializator** biblioteki.

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz [Azure IoT SDK][lnk-sdks].

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
