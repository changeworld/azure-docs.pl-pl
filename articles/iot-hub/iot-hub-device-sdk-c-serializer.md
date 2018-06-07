---
title: Azure urządzenia IoT zestawu SDK dla języka C - serializator | Dokumentacja firmy Microsoft
description: Jak używać biblioteki serializator na urządzeniu Azure IoT SDK dla języka C do tworzenia aplikacji urządzenia, które komunikują się z Centrum IoT.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: a724fa5acc930475bdbe4ffcc74141470a92326c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634148"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT urządzenia zestawu SDK dla języka C — więcej informacji na temat serializator
[Najpierw artykuł](iot-hub-device-sdk-c-intro.md) w tej serii wprowadzone **urządzenia Azure IoT SDK dla języka C**. Kolejnym artykule podano bardziej szczegółowy opis [ **IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). W tym artykule zakończeniu pokrycia zestawu SDK, zapewniając bardziej szczegółowy opis pozostałych składników: **serializator** biblioteki.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Wprowadzające artykule opisano sposób użycia **serializator** biblioteki do zdarzeń w celu wysyłania i odbierania wiadomości z Centrum IoT. W tym artykule rozbudowujemy zapewniając bardziej szczegółowy opis modelu danych za pomocą tej dyskusji **serializator** makra języka. Artykuł obejmuje również więcej szczegółów na temat sposobu biblioteki serializuje wiadomości (i w niektórych przypadkach, jak można kontrolować zachowanie serializacji). Opiszemy także niektóre parametry, które można modyfikować określające rozmiar tworzenia modeli.

Na koniec tego artykułu revisits niektóre tematy omówione w poprzednich artykułach, takie jak wiadomości i Obsługa właściwości. Jako firma Microsoft będzie Znajdź prace, te funkcje w taki sam sposób przy użyciu **serializator** biblioteki co z **IoTHubClient** biblioteki.

Wszystkie elementy opisane w tym artykule jest oparta na **serializator** przykłady zestawu SDK. Jeśli chcesz z niego skorzystać, zobacz **simplesample\_amqp** i **simplesample\_http** aplikacje zawarte w urządzenia Azure IoT SDK dla C.

Można znaleźć [ **urządzenia Azure IoT SDK dla języka C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repozytorium i wyświetlenia jej szczegółów interfejsu API w [dokumentacja interfejsu API C](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>Język modelowania
[Wprowadzające artykułu](iot-hub-device-sdk-c-intro.md) w tej serii wprowadzone **urządzenia Azure IoT SDK dla języka C** modeling language za pośrednictwem przykładzie przedstawionym w **simplesample\_amqp** aplikacji:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Jak widać, język modelowania jest oparta na makr C. Zawsze należy zacząć definicja z **BEGIN\_przestrzeni nazw** i zawsze kończyć **zakończenia\_przestrzeni nazw**. Są często nazwę przestrzeni nazw w firmie lub, jak w poniższym przykładzie w projekcie, który pracuje.

Co jest umieszczane w obszarze nazw są definicje modelu. W takim przypadku jest pojedynczego modelu dla anemometer. Ponownie model może mieć nazwę niczego, ale zazwyczaj jest to urządzenie lub typu danych, który chcesz wymiany z Centrum IoT.  

Modele zawiera definicji zdarzeń umożliwia transfer danych przychodzących z Centrum IoT ( *danych*) oraz komunikaty mogą odbierać z Centrum IoT ( *akcje*). Jak widać w przykładzie zdarzenia mają typem i nazwą; Akcje mieć nazwę i następujące parametry opcjonalne (każde z nich typu).

Co nie jest prezentowana w tym przykładzie są typy dodatkowe dane, które są obsługiwane przez zestaw SDK. Firma Microsoft będzie obejmować tego dalej.

> [!NOTE]
> Centrum IoT odwołuje się do danych urządzenie wysyła do niego jako *zdarzenia*, podczas gdy język modelowania odwołuje się do niego jako *danych* (zdefiniowany przy użyciu **WITH_DATA**). Podobnie, Centrum IoT odwołuje się do danych, możesz wysłać do urządzenia jako *wiadomości*, podczas gdy język modelowania odwołuje się do niego jako *akcje* (zdefiniowany przy użyciu **WITH_ACTION**). Należy pamiętać, że te terminy mogą używane zamiennie w tym artykule.
> 
> 

### <a name="supported-data-types"></a>Obsługiwane typy danych
Następujące typy danych są obsługiwane w modelach utworzone za pomocą **serializator** biblioteki:

| Typ | Opis |
| --- | --- |
| double |Podwójna precyzja liczba zmiennoprzecinkowa |
| int |32-bitowa liczba całkowita |
| liczba zmiennoprzecinkowa |Liczba zmiennoprzecinkowa pojedynczej precyzji |
| dł. |długich liczb całkowitych |
| int8\_t |8-bitową liczbą całkowitą |
| Int16\_t |16-bitową liczbę całkowitą |
| int32\_t |32-bitowa liczba całkowita |
| Int64\_t |64-bitowa liczba całkowita |
| wartość logiczna |wartość logiczna |
| ASCII\_char\_ptr |Ciąg ASCII |
| EDM\_DATA\_CZASU\_PRZESUNIĘCIA |Przesunięcie czasu daty |
| EDM\_IDENTYFIKATOR GUID |Identyfikator GUID |
| EDM\_BINARY |Binarne |
| DEKLAROWANIE\_— STRUKTURA |Typ danych złożonych |

Zacznijmy od ostatniego typu danych. **DECLARE\_struktury** można zdefiniować złożone typy danych, które są grupami innych typów pierwotnych. Te grupy umożliwiają definiowanie modelu, który wygląda następująco:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Nasz model zawiera zdarzenie danych jednego typu **elementu TestType**. **Element TestType** jest typem złożonym, który zawiera kilka elementów członkowskich, które wykazują zbiorczo, typy pierwotne, obsługiwane przez **serializator** język modelowania.

Z modelem takie firma Microsoft można napisać kod do wysyłania danych do Centrum IoT, która wygląda następująco:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Zasadniczo, firma Microsoft jest przypisywanie wartości do wszystkich członków **testu** struktury i wywołując **SendAsync** do wysyłania **testu** zdarzenie danych do chmury. **SendAsync** jest funkcji pomocnika, która wysyła zdarzenia pojedynczego danych do Centrum IoT:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Ta funkcja wykonuje serializację danego zdarzenie i wysyła go do Centrum IoT przy użyciu **IoTHubClient\_SendEventAsync**. Jest to ten sam kod omówione w poprzednich artykułach (**SendAsync** hermetyzuje logiki do funkcji wygodny).

Jest jednym innych funkcji pomocnika używany w poprzednim kodzie **GetDateTimeOffset**. Ta funkcja przekształca danej chwili w wartości typu **EDM\_data\_czasu\_PRZESUNIĘCIA**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Po uruchomieniu tego kodu następujący komunikat jest wysyłany do Centrum IoT:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Należy pamiętać, że serializacji JSON, czyli format generowane przez **serializator** biblioteki. Należy również zauważyć, że każdy element członkowski Zserializowany obiekt JSON odpowiada członków **elementu TestType** zdefiniowanego w modelu. Wartości również dokładnie odpowiadać używanych w kodzie. Jednak należy pamiętać, że dane binarne algorytmem Base64: "AQID" jest base64 kodowanie {0x01, 0x02, 0x03}.

W tym przykładzie pokazano, zaletą korzystania z **serializator** biblioteki — umożliwia firmie Microsoft w celu wysyłania JSON w chmurze, bez konieczności jawnego postępowania w przypadku serializacji w naszej aplikacji. Wszystko, co mamy martwić się o jest ustawienie wartości danych zdarzeń w modelu, a następnie wywołując prostych interfejsów API do wysyłania zdarzeń do chmury.

Dzięki tym informacjom możemy zdefiniować modele, które obejmują zakres obsługiwane typy danych, łącznie z typów złożonych (Firma Microsoft może nawet obejmują typy złożone w ramach innych typów złożonych). Jednak on serializacji JSON generowanych przez w powyższym przykładzie wywołuje punkt ważne. *Jak* wyślemy danych za pomocą **serializator** biblioteki określa dokładnie, jak kod JSON jest sformatowany. Czy konkretnej jest co omówione zostaną następujące czynności dalej.

## <a name="more-about-serialization"></a>Więcej informacji na temat serializacji
Poprzedniej sekcji przedstawiono przykładowe dane wyjściowe, generowane przez **serializator** biblioteki. W tej sekcji wyjaśniamy sposób biblioteki serializuje dane i metody kontrolowania tego zachowania, za pomocą serializacji interfejsów API.

Aby poprawić dyskusji w serializacji, firma Microsoft będzie współpracować z nowy model oparty na termostacie. Po pierwsze umożliwia zawierają niektóre tła na scenariuszu próbujemy adres.

Chcemy termostat, która temperatury i wilgotności modelu. Każda część danych będzie wysyłane do Centrum IoT inaczej. Domyślnie ingresses termostat zdarzeniem temperatury raz na 2 minuty; Zdarzenie wilgotności jest ingressed co 15 minut. W przypadku ingressed obu zdarzeń, musi on zawierać sygnatury czasowej, która przedstawia czas odpowiedniego temperatury i wilgotności zmierzono.

Zostanie przedstawiony dwa różne sposoby modelu danych podane w tym scenariuszu, i wyjaśniamy efekt czy modelowania ma na serializowane dane wyjściowe.

### <a name="model-1"></a>Model 1
Oto pierwszej wersji modelu, który obsługuje poprzedniego scenariusza:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Należy pamiętać, że model zawiera dwa zdarzenia danych: **temperatury** i **wilgotności**. W przeciwieństwie do poprzednich przykładach, typ każdego zdarzenia jest strukturą zdefiniowane przy użyciu **DECLARE\_struktury**. **TemperatureEvent** zawiera miary temperatury i sygnaturę czasową; **HumidityEvent** zawiera miary wilgoć i sygnaturę czasową. Ten model umożliwia nam fizycznych modelu danych scenariusz opisany powyżej. Gdy firma Microsoft wysyła zdarzenie do chmury, albo wyślemy temperatury/sygnatura czasowa lub pary wilgotności/sygnatura czasowa.

Możemy wysłać zdarzenie temperatury w chmurze przy użyciu kodu, takie jak następujące:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Firma Microsoft będzie używać zakodowanych wartości temperatury i wilgotności w przykładowym kodzie, ale Wyobraź sobie możemy poprzez próbkowanie odpowiednich czujników na termostat faktycznie jest pobierania tych wartości.

Kod nad używa **GetDateTimeOffset** pomocnika, który został wprowadzony wcześniej. Przyczyn, które będzie wyczyść nowsze ten kod jawnie oddziela zadania serializacji i wysyła zdarzenia. Poprzedni kod serializuje zdarzeń temperatury do buforu. Następnie **sendMessage** jest funkcją pomocnika (objęte **simplesample\_amqp**) która wysyła zdarzenia do Centrum IoT:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Ten kod jest podzbiorem **SendAsync** pomocnika opisanych w poprzedniej sekcji, dlatego firma Microsoft nie będą przekazywane go ponownie w tym miejscu.

Uruchomienie poprzedni kod do wysyłania zdarzeń temperatury, ta forma serializacji zdarzenia są wysyłane do Centrum IoT:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Została wysłana temperatury, który jest typu **TemperatureEvent** i zawiera tej struktury **temperatury** i **czasu** elementu członkowskiego. Znajduje to odzwierciedlenie bezpośrednio w danych serializacji.

Podobnie możemy wysłać zdarzenie wilgotności o tym kodzie:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Zserializowany formularz, który jest wysyłany do Centrum IoT wygląda następująco:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Jest to ponownie, zgodnie z oczekiwaniami.

Z tym modelem, w pewnym sensie jak dodatkowe zdarzenia można łatwo dodać. Zdefiniuj więcej struktur za pomocą **DECLARE\_struktury**i dołączyć odpowiednie zdarzenie w modelu, używając **WITH\_danych**.

Teraz zmodyfikujmy modelu co zawierają one te same dane, ale z inną strukturę.

### <a name="model-2"></a>Modelu 2
Należy wziąć pod uwagę alternatywnych modelu poprzednim:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

W takim przypadku możemy Wyeliminowano **DECLARE\_struktury** makra i po prostu są definiowane w naszym scenariuszu przy użyciu proste typy z język modelowania elementów danych.

Tylko dla obecnie umożliwia ignorowanie **czasu** zdarzeń. Z tym Zarezerwuj Oto kod, aby ruch przychodzący **temperatury**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ten kod wysyła następujące zdarzenie Zserializowany do Centrum IoT:

```
{"Temperature":75}
```

I kod umożliwiający wysyłanie zdarzeń wilgotności wygląda następująco:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ten kod wysyła to Centrum IoT:

```
{"Humidity":45}
```

Do tej pory nie ma jeszcze nie niespodzianki. Teraz Zmieńmy wykorzystanie makro SERIALIZACJA.

**SERIALIZACJA** makro może zająć wiele zdarzeń danych jako argumenty. Pozwala na serializować **temperatury** i **wilgotności** razem zdarzeń i wyślij je do Centrum IoT w jednym wywołaniu:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Może się odgadnąć, czy wynik ten kod jest czy dwóch danych zdarzenia są wysyłane do Centrum IoT:

[

{"Temperatury": 75},

{"Humidity":45}

]

Innymi słowy, może spodziewać, ten kod jest taka sama, jak wysyłanie **temperatury** i **wilgotności** oddzielnie. Jest po prostu wygody do przekazywania zarówno zdarzeń do **SERIALIZACJA** w tym samym wywołaniu. Jednak to nie jest wielkość liter. Zamiast tego kodu powyżej wysyła to zdarzenie danych jednego z Centrum IoT:

{"Temperatury": 75, "wilgotności": 45}

Może to wyglądać dziwne, ponieważ definiuje nasz model **temperatury** i **wilgotności** jako dwa *oddzielnych* zdarzenia:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Więcej do punktu, firma Microsoft nie tych zdarzeń modelu gdzie **temperatury** i **wilgotności** znajdują się w tej samej struktury:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Użycie tego modelu, będzie można łatwiej zrozumieć, jak **temperatury** i **wilgotności** wysłania w tym samym szeregowanego komunikatu. Jednak nie może być wyczyść Dlaczego podczas przekazywania obu zdarzeń danych działa w ten sposób **SERIALIZACJA** przy użyciu modelu 2.

To zachowanie jest łatwiejsze do zrozumienia, jeśli znasz założeń który **serializator** biblioteki jest wprowadzenie. Aby zorientować się, to wróć do naszego modelu:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

W kategoriach zorientowane obiektowo należy traktować tego modelu. W takim przypadku jest modelowanie urządzenie fizyczne (termostacie) i urządzenia zawiera atrybutów, takich jak **temperatury** i **wilgotności**.

Możemy wysłać cały stan modelu z kodem, takie jak następujące:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Zakładając, że wartości temperatury, wilgotności i godzina są ustawione, firma Microsoft będzie Zobacz zdarzenia, takiego jak to wysyłane do Centrum IoT:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Czasami może tylko chcesz wysłać *niektórych* właściwości modelu do chmury (jest to szczególnie istotne, jeśli model zawiera wiele zdarzeń danych). Warto wysłać tylko podzestaw danych dotyczących zdarzeń, takich jak w naszym przykładzie wcześniej:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Spowoduje to wygenerowanie dokładnie tego samego zdarzenia serializacji tak, jakby była zdefiniowanego **TemperatureEvent** z **temperatury** i **czas** — członek, tak samo jak z możemy modelu 1. W takim przypadku udało się wygenerować dokładnie tego samego zdarzenia serializacji przy użyciu inny model (model 2), ponieważ dzwoniliśmy **SERIALIZACJA** w inny sposób.

Istotne jest to, że w przypadku przekazania wiele zdarzeń danych do **SERIALIZACJA,** , a następnie przyjęto założenie, że każde wydarzenie jest właściwością w pojedynczy obiekt JSON.

Najlepszym rozwiązaniem jest zależna od i jak myślisz na temat modelu. Jeśli wysyłasz "zdarzenia" w chmurze, a każde zdarzenie zawiera określony zbiór właściwości, pierwszego podejścia udostępnia wiele znaczeniu. W takim przypadku należy użyć **DECLARE\_struktury** definiować strukturę każdego zdarzenia i dołącz je do modelu z **WITH\_danych** makra. Następnie możesz wysłać każde zdarzenie, jak robiliśmy w pierwszym przykładzie powyżej. W tym podejście przejdzie tylko danych jednego zdarzenia **SERIALIZATOR**.

Jeśli myślisz o modelu w sposób zorientowane obiektowo, drugiej metody może własnych użytkownik. W takim przypadku elementy zdefiniowane przy użyciu **WITH\_danych** są "właściwości" obiektu. Przekaż niezależnie od podzbioru zdarzeń do **SERIALIZACJA** które lubisz, w zależności od ilości stanu użytkownika "obiektu" chcesz wysyłać do chmury.

Nether podejście jest nieprawidłowy lub w prawo. Po prostu znać sposób **serializator** works biblioteki i pobrania metoda modelowania, która najlepiej odpowiada Twoim potrzebom.

## <a name="message-handling"></a>Obsługa komunikatów
Do tej pory w tym artykule tylko został omówiony wysyłania zdarzeń do Centrum IoT i nie adresowane odbierania wiadomości. Powód dla tego jest to, że co należy wiedzieć o odbieranie wiadomości ma przede wszystkim zostały omówione w [wcześniej artykuł](iot-hub-device-sdk-c-intro.md). Z tego artykułu przypominają przetwarzania komunikatów przez zarejestrowanie funkcję wywołania zwrotnego komunikat:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Następnie napiszesz funkcja wywołania zwrotnego, które jest wywoływane, gdy wiadomość zostanie odebrana:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

Ta implementacja **IoTHubMessage** wywołuje funkcję określonych dla każdej akcji w modelu. Na przykład, jeśli model definiuje tej akcji:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Zdefiniuj funkcję z tego podpisu:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** następnie jest wywoływane, gdy ten komunikat jest wysyłane do urządzenia.

Jeszcze co firma Microsoft nie zostały wyjaśnione jest wygląda wersja serializowanego komunikatu. Innymi słowy Jeśli chcesz wysłać **SetAirResistance** komunikat na urządzeniu, jak wygląda ten?

Jeśli wiadomość jest wysyłana do urządzenia, czy Zrób to za pomocą zestawu SDK usług Azure IoT. Nadal trzeba wiedzieć, jak ciąg znaków, aby wysłać do wywołania określonej akcji. Ogólny format wysyłania komunikatu wygląda następująco:

```
{"Name" : "", "Parameters" : "" }
```

Wysyłasz Zserializowany obiekt JSON z dwóch właściwości: **nazwa** jest nazwa akcji (komunikat) i **parametry** zawiera parametry tej akcji.

Na przykład, aby wywołać **SetAirResistance** możesz wysłać tę wiadomość na urządzeniu:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Nazwa akcji musi dokładnie odpowiadać akcji zdefiniowanych w modelu. Nazwy parametrów muszą być zgodne, jak również. Należy również zauważyć uwzględniana wielkość liter. **Nazwa** i **parametrów** są zawsze wielkimi literami. Upewnij się, że wielkość liter nazwy akcji i parametrów w modelu. W tym przykładzie nazwa akcji jest "SetAirResistance", a nie "setairresistance".

Dwie inne akcje **TurnFanOn** i **TurnFanOff** może być wywoływany przez wysyłanie tych wiadomości na urządzeniu:

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

W tej sekcji opisano wszystko, co należy wiedzieć, kiedy zdarzenia wysyłanie i odbieranie komunikatów z **serializator** biblioteki. Przed kontynuowaniem, teraz obejmuje niektóre parametry, które można skonfigurować określające, jak duże jest modelu.

## <a name="macro-configuration"></a>Konfiguracja — makro
Jeśli używasz **serializator** biblioteki ważnym elementem zestaw SDK, aby mieć świadomość znajduje się w bibliotece azure-c udostępnione — narzędzie.
Jeśli zostały sklonowane repozytorium Azure-iot-sdk-c z serwisu GitHub, za pomocą opcji--cykliczne, będą dostępne w tej biblioteki jako narzędzia udostępnione:

```
.\\c-utility
```

Jeśli nie zostały sklonowane biblioteki, możesz go znaleźć [tutaj](https://github.com/Azure/azure-c-shared-utility).

W bibliotece jako narzędzia udostępnione można znaleźć następujący folder:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ten folder zawiera rozwiązania Visual Studio o nazwie **makro\_witryny\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

W tym rozwiązaniu program generuje **makro\_utils.h** pliku. Brak domyślne makro\_plik utils.h dołączony do zestawu SDK. To rozwiązanie umożliwia modyfikowanie niektórych parametrów, a następnie utwórz ponownie plik nagłówka, na podstawie tych parametrów.

Są dwa parametry klucza zainteresowanych z **nArithmetic** i **nMacroParameters** które zostały określone w tych dwóch wierszach znaleziono w makrze\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Te wartości są domyślne parametry dołączone do zestawu SDK. Każdy parametr ma następujące znaczenie:

* nMacroParameters — Określa, ile parametrów mogą mieć w jednej instrukcji DECLARE\_definicji makra w modelu.
* Określa, nArithmetic — łączna liczba elementów członkowskich dozwolone w modelu.

Przyczyny, dla której te parametry są ważne jest, ponieważ decydować, jak duży może być modelu. Rozważmy na przykład tej definicji modelu:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak wspomniano wcześniej, **DECLARE\_modelu** jest po prostu makr C. Nazwy modelu i **WITH\_danych** instrukcji (jeszcze innego makra) czy parametry **DECLARE\_modelu**. **nMacroParameters** definiuje, ile parametry mogą być zawarte w **DECLARE\_modelu**. W rezultacie definiuje liczbę zdarzeń i akcji deklaracjach danych może mieć. W związku z domyślnym limitem 124 oznacza to, zdefiniować modelu z kombinacją akcji około 60 i zdarzenia danych. Jeśli spróbujesz przekracza ten limit, otrzymasz błędów, które wyglądać podobnie do poniższego:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

**NArithmetic** parametr jest więcej informacji na temat wewnętrzne działanie języka makro niż aplikacja.  Kontroluje łączna liczba elementów członkowskich w modelu, mogą znajdować się w tym **DECLARE_STRUCT** makra. Jeśli możesz zacząć się wyświetlać błędy kompilatora, takich jak ta, a następnie spróbuj użyć zwiększenie **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Jeśli chcesz zmienić te parametry, zmodyfikuj wartości w makrze\_utils.tt plików, skompiluj ponownie makra\_witryny\_h\_generator.sln rozwiązania, a następnie uruchom program skompilowany. Się nowe makro\_utils.h plik jest generowany i umieszczane w.\\ Typowe\\inc katalogu.

Aby można było używać nowej wersji makro\_utils.h, Usuń **serializator** obejmują pakietu NuGet z rozwiązania i w jego miejsce **serializator** projekt programu Visual Studio. Dzięki temu kodu do kompilacji z kodem źródłowym biblioteki serializatora. Obejmuje to makro zaktualizowane\_utils.h. Jeśli chcesz to zrobić **simplesample\_amqp**, uruchom przez usunięcie pakietu NuGet dla biblioteki serializator z rozwiązania:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Następnie dodaj ten projekt do rozwiązania Visual Studio:

> . \\c\\serializator\\kompilacji\\windows\\serializer.vcxproj
> 
> 

Gdy wszystko będzie gotowe rozwiązanie powinien wyglądać następująco:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Teraz podczas kompilowania rozwiązania zaktualizowane makro\_utils.h znajduje się w sieci danych binarnych.

Należy pamiętać, że zwiększenie wartości wystarczająco wysoka, może być dłuższa niż limity kompilatora. W tym punkcie **nMacroParameters** parametru głównego, z którą zainteresowanym. Specyfikacja C99 Określa, czy co najmniej 127 parametrów są dozwolone w definicji makra. Kompilator Microsoft następuje specyfikację dokładnie (i ma limit 127), więc nie można zwiększyć **nMacroParameters** ponad wartość domyślną. Inne kompilatory może zezwalać na zrobić (na przykład kompilatora GNU obsługuje wyższy limit).

Do tej pory możemy zostały omówione prawie wszystko, co należy wiedzieć o tym, jak napisać kod z **serializator** biblioteki. Przed zawierania, możemy ponownie niektóre tematy z poprzednich artykułów, które możesz się zastanawiać, informacje.

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu
Przykładowa aplikacja, na którym ten artykuł skupia się **simplesample\_amqp**. W przykładzie użyto wyższego poziomu (z systemem innym niż — "wszystko") interfejsy API służące do wysyłania zdarzeń i odbierania wiadomości. Korzystając z poniższych interfejsów API, uruchamia wątku w tle, który zajmuje się zarówno zdarzenia wysyłania i odbierania wiadomości. Jednak można użyć interfejsów API niższego poziomu (wszystkie), aby wyeliminować ten wątek w tle, a następnie jawną kontrolę nad wysłanie zdarzenia lub odbieranie komunikatów z chmury.

Zgodnie z opisem w [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md), istnieje zestaw funkcji, która składa się z wyższego poziomu interfejsów API:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_zniszczyć

Te interfejsy API przedstawiono w części **simplesample\_amqp**.

Istnieje również analogiczne zestaw interfejsów API niższego poziomu.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_zniszczyć

Należy pamiętać, interfejsy API niższego poziomu pracować dokładnie tak samo, zgodnie z opisem w poprzedniej artykułów. Jeśli chcesz, aby wątku w tle do obsługi zdarzeń wysyłanie i odbieranie wiadomości, można użyć pierwszy zestaw interfejsów API. Drugi zestaw interfejsów API można użyć, jeśli możesz jawną kontrolę nad podczas wysyłania i odbierania danych z Centrum IoT. Albo zestaw interfejsów API pracy jednakowo oraz z **serializator** biblioteki.

Przykład użycia interfejsów API niższego poziomu z **serializator** biblioteki, zobacz **simplesample\_http** aplikacji.

## <a name="additional-topics"></a>Tematy dodatkowe
Kilka innych tematów, warto zauważyć, są ponownie właściwości obsługi, przy użyciu poświadczeń alternatywnych urządzenia i opcje konfiguracji. Są to wszystkie tematy objęte [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md). Jest głównym punktem, czy wszystkie te funkcje działają tak samo jak z **serializator** biblioteki co z **IoTHubClient** biblioteki. Na przykład, jeśli chcesz dołączyć właściwości na zdarzenie z modelu, należy użyć **IoTHubMessage\_właściwości** i **mapy**\_**AddorUpdate**, tak samo, jak opisano wcześniej:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Czy to zdarzenie zostało wygenerowane z **serializator** biblioteki lub utworzone ręcznie za pomocą **IoTHubClient** biblioteki nie ma znaczenia.

W przypadku poświadczeń alternatywnych urządzenia przy użyciu **IoTHubClient\_LL\_Utwórz** działa równie dobrze jako **IoTHubClient\_CreateFromConnectionString** dla przydzielanie **Centrum IOTHUB\_klienta\_obsługi**.

Ponadto jeśli używasz **serializator** biblioteki, można ustawić opcji konfiguracji z **IoTHubClient\_LL\_SetOption** podobnie jak w przypadku korzystania z **IoTHubClient** biblioteki.

Funkcja, która jest unikatowa dla **serializator** biblioteki są inicjowania interfejsów API. Przed rozpoczęciem pracy z biblioteki, należy wywołać **serializator\_init**:

```
serializer_init(NULL);
```

Jest to realizowane tylko przed wywołaniem **IoTHubClient\_CreateFromConnectionString**.

Podobnie, po zakończeniu pracy z biblioteki, jest ostatnim wywołaniu, które należy podjąć do **serializator\_deinit**:

```
serializer_deinit();
```

W przeciwnym razie wszystkie inne funkcje wymienione powyżej działać w identyczny **serializator** biblioteki tak jak w **IoTHubClient** biblioteki. Aby uzyskać więcej informacji o tych tematów, zobacz [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md) w tej serii.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule opisano szczegółowo unikatowe aspekty **serializator** zawartych w bibliotece **urządzenia Azure IoT SDK dla języka C**. Z informacjami pod warunkiem, że powinien dysponować dobrą znajomością używania modeli do wysyłania zdarzeń i odbieranie komunikatów z Centrum IoT.

Teraz również serii trzech części o tworzeniu aplikacji za pomocą **urządzenia Azure IoT SDK dla języka C**. Powinno to być wystarczających informacji do nie tylko ułatwiające rozpoczęcie pracy, ale umożliwiają dokładne zrozumienie działania interfejsów API. Aby uzyskać dodatkowe informacje istnieje kilka przykładów w zestawie SDK nie pasuje do tutaj. W przeciwnym razie [dokumentacji zestawu SDK](https://github.com/Azure/azure-iot-sdk-c) jest dobrym zasobów, aby uzyskać dodatkowe informacje.

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz [Azure IoT SDK][lnk-sdks].

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
