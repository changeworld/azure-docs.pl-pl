---
title: Azure zestaw SDK urządzeń IoT dla języka C - serializator | Dokumentacja firmy Microsoft
description: Jak używać biblioteki serializatora w urządzeń Azure IoT SDK dla języka C do tworzenia aplikacji urządzenia, które komunikują się z usługą IoT hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: 0a7e30be374ae5095e206ce0e519e51bb58f1f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399275"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Usługa Azure zestaw SDK urządzeń IoT dla języka C — więcej informacji na temat serializatora

Pierwszy artykuł w tej serii, wprowadzono [wprowadzenie do zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Kolejnym artykule podano bardziej szczegółowy opis [zestaw SDK urządzeń Azure IoT dla języka C — usługi IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Ten artykuł stanowi zakończenie pokrycia zestawu SDK, zapewniając bardziej szczegółowy opis pozostałych składników: **serializator** biblioteki.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Wprowadzające artykule opisano sposób używania **serializator** biblioteki do wysyłania zdarzeń do i odbierania komunikatów z usługi IoT Hub. W tym artykule rozszerzmy tej dyskusji, zapewniając bardziej szczegółowy opis sposobu modelowanie danych za pomocą **serializator** makra języka. Artykuł obejmuje również więcej szczegółów na temat sposobu biblioteki serializuje wiadomości (i w niektórych przypadkach, jak można kontrolować zachowanie serializacji). Również opiszemy niektóre parametry, które można modyfikować określające rozmiar modele, które tworzysz.

Na koniec tego artykułu powraca niektóre tematy omówione w poprzednich artykułów, takich jak wiadomości i Obsługa właściwości. Jak firma Microsoft będzie Znajdź prace, te funkcje w taki sam sposób przy użyciu **serializator** biblioteki tak samo, jak za pomocą **usługi IoTHubClient** biblioteki.

Wszystko, co opisano w tym artykule opiera się na **serializator** przykładowych zestawach SDK. Jeśli chcesz wykonać te instrukcje, zobacz **simplesample\_amqp** i **simplesample\_http** aplikacje uwzględnione w zestaw SDK urządzeń Azure IoT dla języka C.

Możesz znaleźć [ **zestaw SDK urządzeń Azure IoT dla języka C** ](https://github.com/Azure/azure-iot-sdk-c) GitHub repozytorium i wyświetlenia jej szczegółów interfejsu API w [dokumentacja interfejsu API języka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-modeling-language"></a>Język modelowania

[Zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md) artykuł w tej serii, wprowadzono **zestaw SDK urządzeń Azure IoT dla języka C** modelowania języka za pomocą przykładu w **simplesample\_ Protokół amqp** aplikacji:

```C
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

Jak widać, język modelowania opiera się na makr C. Zawsze zaczynają się swojej definicji za pomocą **BEGIN\_przestrzeni nazw** i zawsze kończy się znakiem **zakończenia\_przestrzeni nazw**. Jest wspólne dla nazwy przestrzeni nazw dla swojej firmy lub, jak w poniższym przykładzie, projekt, który pracujesz na komputerze.

Co jest umieszczane w obszarze nazw są definicje modelu. W tym przypadku jest jednym modelu dla anemometer. Jeszcze raz model może mieć nazwę niczego, ale zazwyczaj modelu nosi nazwę urządzenia lub typu danych, który chcesz programu exchange z usługą IoT Hub.  

Modele zawiera definicji zdarzeń można ruch przychodzący do usługi IoT Hub ( *danych*) oraz komunikaty, możesz otrzymywać z usługi IoT Hub ( *akcje*). Jak widać w przykładzie, zdarzenia mają typ i nazwę; akcji ma nazwę i parametry opcjonalne (każdy z typem).

Co nie jest pokazana w tym przykładzie są dodatkowe typy danych, które są obsługiwane przez zestaw SDK. Omówimy to w następnym kroku.

> [!NOTE]
> Usługa IoT Hub, który odwołuje się do danych urządzenie wysyła do niego jako *zdarzenia*, podczas gdy język modelowania, który odwołuje się do niego jako *danych* (zdefiniowane przy użyciu **WITH_DATA**). Podobnie, usługa IoT Hub odwołuje się do danych, możesz wysłać do urządzenia jako *wiadomości*, podczas gdy język modelowania, który odwołuje się do niego jako *akcje* (zdefiniowane przy użyciu **WITH_ACTION**). Należy pamiętać, że te warunki mogą być używane zamiennie w tym artykule.
> 
> 

## <a name="supported-data-types"></a>Obsługiwane typy danych

Następujące typy danych są obsługiwane w modelach utworzonych za pomocą **serializator** biblioteki:

| Type | Opis |
| --- | --- |
| double |Podwójna precyzja liczb zmiennoprzecinkowych |
| int |32-bitowa liczba całkowita |
| float |Liczba zmiennoprzecinkowa pojedynczej precyzji |
| long |Liczba całkowita typu Long |
| int8\_t |8-bitowa liczba całkowita |
| int16\_t |16-bitową liczbę całkowitą |
| int32\_t |32-bitowa liczba całkowita |
| int64\_t |64-bitowa liczba całkowita |
| bool |wartość logiczna |
| ascii\_char\_ptr |Ciąg ASCII |
| EDM\_DATE\_TIME\_OFFSET |Przesunięcie czasu daty |
| EDM\_GUID |Identyfikator GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |Typ złożony danych |

Zacznijmy od ostatniego typu danych. **DECLARE\_struktury** można zdefiniować złożone typy danych, stanowiące inne typy pierwotne. Te grupy umożliwiają zdefiniowanie modelu, który wygląda w następujący sposób:

```C
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

Nasz model zawiera zdarzenie danych jednego typu **elementu TestType**. **Element TestType** to typ złożony, który zawiera kilka składowych, które wykazują zbiorczo, typy pierwotne, obsługiwane przez **serializator** modelowania języka.

Przy użyciu modelu tak firma Microsoft można napisać kod, aby wysyłać dane do Centrum IoT Hub, który pojawia się w następujący sposób:

```C
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

Zasadniczo, firma Microsoft jest przypisywanie wartości do każdego członka **testu** struktury, a następnie wywołując **SendAsync** wysyłać **testu** zdarzenia danych do chmury. **SendAsync** jest funkcja pomocnicza, która wysyła zdarzenia pojedynczego danych do usługi IoT Hub:

```C
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

Ta funkcja wykonuje serializację danego zdarzenie i wysyła je do Centrum IoT przy użyciu **usługi IoTHubClient\_SendEventAsync**. Jest to ten sam kod omówione w poprzednich artykułach (**SendAsync** hermetyzuje logikę do wygodne funkcji).

Co inne funkcja pomocnika używana w poprzednim kodzie jest **GetDateTimeOffset**. Ta funkcja przekształca danej godziny w wartości typu **EDM\_data\_czasu\_przesunięcie**:

```C
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

Jeśli uruchomisz ten kod, następujący komunikat jest wysyłany do Centrum IoT:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Należy zauważyć, że w formacie JSON, czyli format serializacji generowane przez **serializator** biblioteki. Należy również zauważyć, że każdy element członkowski Zserializowany obiekt JSON odpowiada członkowie **elementu TestType** zdefiniowanego w naszym modelu. Wartości również dokładnie pasują do siebie te używane w kodzie. Jednak należy pamiętać, że dane binarne zakodowane w formacie base64: "AQID" jest base64 kodowanie {0x01, 0x02, 0x03}.

W tym przykładzie przedstawiono zaletą korzystania z **serializator** biblioteki — dzięki temu możemy wysłać JSON w chmurze, bez konieczności jawnego serializacji w naszej aplikacji. To wszystko, co mamy już martwić się o ustawienie wartości zdarzenia danych w naszym modelu, a następnie wywołując prostych interfejsów API do wysyłania zdarzeń do chmury.

Dzięki tym informacjom można definiujemy modeli, które obejmują zakres obsługiwanych typów danych, łącznie z typów złożonych (Firma Microsoft może nawet zawierać typy złożone w ramach innych typów złożonych). Jednak serializacji JSON generowanych przez powyższego przykładu powoduje wyświetlenie to ważny punkt. *Jak* możemy wysłać danych za pomocą **serializator** biblioteki określa dokładnie, jak został utworzony za pomocą pliku JSON. Określony punkt jest omówimy dalej.

## <a name="more-about-serialization"></a>Więcej informacji na temat serializacji

Poprzedniej sekcji opisano przykładowe dane wyjściowe generowane przez **serializator** biblioteki. W tej sekcji wyjaśnimy, jak biblioteka serializuje dane i jak można kontrolować to zachowanie za pomocą interfejsów API serializacji.

Aby można było z rozwojem dyskusji o serializacji możemy będziesz pracować nowy model oparty na zegarki. Najpierw Przyjrzyjmy zapewniają pewną wiedzę na scenariuszu, którą próbujemy adresu.

Chcemy modelu zegarki, służący do pomiaru temperatury i wilgotności. Każdy element danych będzie wysyłane do usługi IoT Hub inaczej. Domyślnie ingresses termostat to zdarzenie temperatury raz na 2 minuty; zdarzenie dotyczące wilgotności jest ingressed co 15 minut. Gdy albo zdarzeń jest ingressed, musi on zawierać sygnaturę czasową mającą pokazuje czas został zmierzony odpowiedniego temperatury i wilgotności.

Biorąc pod uwagę ten scenariusz, zaprezentujemy dwa różne sposoby do modelu danych i wyjaśnimy efekt, modelowanie miało na Zserializowany danych wyjściowych.

### <a name="model-1"></a>Model 1

W tym miejscu to pierwsza wersja modelu, który obsługuje poprzedni scenariusz:

```C
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

Należy zwrócić uwagę na to, że model zawiera dwa zdarzenia danych: **Temperatura** i **wilgotności**. W przeciwieństwie do poprzednich przykładach, typ każde zdarzenie jest definiowane przy użyciu struktury **DECLARE\_struktury**. **TemperatureEvent** obejmuje pomiarów temperatury i sygnaturę czasową; **HumidityEvent** zawiera miary wilgotności i sygnaturę czasową. Ten model zapewnia nam naturalny sposób modelowania danych dla scenariusza opisanego powyżej. Gdy firma Microsoft wysyła zdarzenie do chmury, albo wyślemy temperatury/sygnatura czasowa lub parę wilgotności/sygnatura czasowa.

Możemy wysłać zdarzenie temperatury, w chmurze przy użyciu kodu, takie jak następujące:

```C
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

Firma Microsoft będzie używać zakodowanych wartości temperatury i wilgotności w przykładowym kodzie, ale imagine, firma Microsoft przez próbkowanie odpowiedniego czujników na termostat faktycznie przypadku pobierania tych wartości.

Kod powyżej używa **GetDateTimeOffset** pomocnika, która została wprowadzona wcześniej. Przyczyn, które staną się wyczyść nowszego ten kod oddziela jawnie zadanie serializacji i wysyłania zdarzeń. Powyższy kod serializuje zdarzeń temperatury do bufora. Następnie **sendMessage** funkcji pomocnika, która jest (zawarte w **simplesample\_amqp**), wysyła zdarzenie do usługi IoT Hub:

```C
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

Ten kod jest podzbiorem **SendAsync** pomocnika opisane w poprzedniej sekcji, dzięki czemu firma Microsoft nie będzie omijają go ponownie w tym miejscu.

Uruchomienie poprzedniego kodu do wysłania zdarzenia temperatury, ta forma serializacji zdarzenia są wysyłane do usługi IoT Hub:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Wysyłamy temperatura, która jest typu **TemperatureEvent** i że struktura zawiera **temperatury** i **czasu** elementu członkowskiego. Znajduje to odzwierciedlenie bezpośrednio w danych serializacji.

Podobnie firma Microsoft może wysłać zdarzenie wilgotności przy użyciu tego kodu:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Serializowany formularz, które są wysyłane do usługi IoT Hub wygląda następująco:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Jest to ponownie, zgodnie z oczekiwaniami.

W tym modelu można sobie wyobrazić jak dodatkowe zdarzenia mogą być łatwo dodawane. Zdefiniuj więcej struktur za pomocą **DECLARE\_struktury**i uwzględniają odpowiednie zdarzenie w modelu, używając **WITH\_danych**.

Teraz aby obejmowała te same dane, ale z inną strukturę zmodyfikujmy modelu.

### <a name="model-2"></a>Model 2

Należy wziąć pod uwagę ten model alternatywne do przedstawionego powyżej:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

W tym przypadku wyeliminowaliśmy **DECLARE\_struktury** makr i po prostu definiowania elementów danych w naszym scenariuszu przy użyciu typów prostych, od języka modelowania.

Po prostu na tę chwilę należy zignorować **czasu** zdarzeń. Z tego aside, poniżej przedstawiono kod, aby ruch przychodzący **temperatury**:

```C
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

Ten kod wysyła następujące zdarzenie Zserializowany do usługi IoT Hub:

```C
{"Temperature":75}
```

I kod umożliwiający wysyłanie zdarzeń wilgotności wygląda następująco:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ten kod wysyła tym do usługi IoT Hub:

```C
{"Humidity":45}
```

Do tej pory nie ma jeszcze żadnych niespodzianek. Teraz zmienimy, jak za pomocą makra SERIALIZACJA.

**SERIALIZE** — makro może zająć wiele zdarzeń danych jako argumenty. Pozwala to nam serializacji **temperatury** i **wilgotności** zdarzeń razem i wyślij je do usługi IoT Hub w jednym wywołaniu:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Może się odgadnąć, czy wynik tego kodu jest czy dwa dane zdarzenia są wysyłane do usługi IoT Hub:

[{"Temperatura": 75}, {"Wilgotność": 45}]

Oznacza to, by oczekiwać, że ten kod jest taki sam, jak wysyłanie **temperatury** i **wilgotności** oddzielnie. Jest po prostu wygodne do przekazywania zarówno zdarzenia **SERIALIZE** w tym samym wywołaniu. Jednak to nie jest wymagane. Zamiast tego powyższy kod wysyła to zdarzenie danych jednego centrum IoT:

{"Temperatura": 75, "wilgotność": 45}

To może wydawać się dziwne ponieważ definiuje nasz model **temperatury** i **wilgotności** jako dwa *oddzielnych* zdarzenia:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Więcej do punktu, firma Microsoft nie tych zdarzeń modelu gdzie **temperatury** i **wilgotności** znajdują się w tej samej struktury:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Użycie tego modelu, będzie można łatwiej zrozumieć, jak **temperatury** i **wilgotności** będą wysyłane w ten sam komunikat serializacji. Jednak może nie być jasne Dlaczego działa w ten sposób podczas przekazywania zarówno zdarzenia danych, aby **SERIALIZE** przy użyciu modelu 2.

To zachowanie jest łatwiejsze do zrozumienia, jeśli znasz założeń, **serializator** osiągnął w bibliotece. Aby poznać to Wróćmy do naszego modelu:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Pomyśl o tym modelu w warunkach zorientowane obiektowo. W tym przypadku możemy modelujesz urządzenie fizyczne (termostat) i tego urządzenia zawiera atrybuty, takie jak **temperatury** i **wilgotności**.

Możemy wysyłać tym osobom stan cały nasz model przy użyciu kodu, takie jak następujące:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Zakładając, że wartości temperatury, wilgotności i godzina są ustawione, można sprawdzić, zdarzenie, takie jak to wysłane do Centrum IoT:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Czasami może być tylko chcesz wysłać *niektóre* właściwości modelu do chmury (jest to szczególnie istotne, jeśli model zawiera dużą liczbę zdarzeń danych). Warto wysłać tylko podzestaw danych dotyczących zdarzeń, takich jak w naszym wcześniejszym przykładzie:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Spowoduje to wygenerowanie dokładnie te same zdarzenia serializacji tak, jakby było zdefiniowaliśmy **TemperatureEvent** z **temperatury** i **czasu** elementu członkowskiego, podobnie jak za pomocą możemy modelu 1. W tym przypadku byliśmy w stanie wygenerować dokładnie te same zdarzenia serializacji przy użyciu innego modelu (model 2), ponieważ firma Microsoft o nazwie **SERIALIZE** w inny sposób.

Istotną kwestią jest to, że w przypadku przekazania wiele zdarzeń danych, aby **SERIALIZE,** , a następnie przyjęto założenie, że każde zdarzenie jest właściwością w pojedynczy obiekt JSON.

Najlepszym rozwiązaniem zależy od tego, i w jaki sposób rozważać modelu. Jeśli "miara events" jest wysyłana do chmury, a każde zdarzenie zawiera określony zbiór właściwości, pierwsze podejście zapewnia bardzo rozsądne. W takim przypadku należy użyć **DECLARE\_struktury** definiują strukturę każdego zdarzenia i uwzględnić je w modelu za pomocą **WITH\_danych** makra. Następnie możesz wysłać każde zdarzenie, ile My mieliśmy w pierwszym przykładzie powyżej. W tym podejściu przejdzie tylko danych jednego zdarzenia **SERIALIZATOR**.

Jeśli myślisz o modelu w sposób zorientowane obiektowo, drugie podejście może własnych użytkownik. W tym przypadku elementy zdefiniowane przy użyciu **WITH\_danych** są "właściwości" obiektu. Należy przekazać niezależnie od podzbiór zdarzeń **SERIALIZE** ciekawy, w zależności od ilości stan "obiektu", aby wysłać do chmury.

Nether podejście jest odpowiednie lub jest on nieprawidłowy. Wystarczy wiedzieć, jak **serializator** działa biblioteki i pobrania metoda modelowania, który najlepiej spełnia Twoje potrzeby.

## <a name="message-handling"></a>Obsługa komunikatów

Do tej pory w tym artykule zawiera tylko omówione wysyłania zdarzeń do Centrum IoT Hub, a nie zostało rozwiązane odbierania komunikatów. Przyczyna jest sytuacja, co należy wiedzieć o odbieranie wiadomości ma dużym stopniu zostać omówione w artykule [zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Jak pamiętamy z artykułu tego artykułu przetwarzania komunikatów, rejestrując funkcji wywołania zwrotnego komunikat:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Następnie napisać funkcję wywołania zwrotnego, która jest wywoływana, gdy wiadomość zostaje odebrana:

```C
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

Ta implementacja **IoTHubMessage** wywołuje określoną funkcję dla każdej akcji w modelu. Na przykład, jeśli model definiuje tę akcję:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Zdefiniuj funkcję z tą sygnaturą:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** jest następnie wywoływana, gdy ten komunikat jest wysyłany do Twojego urządzenia.

Co firma Microsoft nie zostały wyjaśnione jeszcze wygląda wersja po serializacji komunikatu. Innymi słowy Jeśli chcesz wysłać **SetAirResistance** komunikat na urządzeniu, jak wygląda ten?

Jeśli wiadomość jest wysyłana do urządzenia, czy Zrób to za pomocą zestawu SDK usługi Azure IoT. Nadal należy wiedzieć, jakiego ciągu do wysłania do wywołania określonej akcji. Ogólny format wysyłania komunikatu pojawia się w następujący sposób:

```C
{"Name" : "", "Parameters" : "" }
```

Wysyłasz Zserializowany obiekt JSON z dwie właściwości: **Nazwa** jest nazwa akcji (komunikat) i **parametry** zawiera parametry tę akcję.

Na przykład, aby wywołać **SetAirResistance** ta wiadomość zostanie wysłana na urządzeniu:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Nazwa akcji musi dokładnie odpowiadać akcji zdefiniowanych w modelu. Nazwy parametrów muszą być zgodne, jak również. Należy również zauważyć, rozróżnianie wielkości liter. **Nazwa** i **parametry** są zawsze wielkie litery. Upewnij się, że Uwzględnij wielkość liter nazwy akcji i parametrów w modelu. W tym przykładzie nazwa akcji jest "SetAirResistance", a nie "setairresistance".

Dwie inne akcje **TurnFanOn** i **TurnFanOff** może być wywoływany przez wysyłanie komunikatów do urządzenia:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

W tej sekcji opisano wszystko, co musisz wiedzieć, kiedy zdarzenia wysyłanie i odbieranie komunikatów za pomocą **serializator** biblioteki. Przed kontynuowaniem, teraz obejmuje niektóre parametry, które można skonfigurować, które kontrolują, rozmiar, jaki jest model.

## <a name="macro-configuration"></a>Konfiguracja — makro

Jeśli używasz **serializator** biblioteki ważnym zestawem SDK, aby mieć świadomość znajduje się w bibliotece azure-c narzędzia udostępnione.

Jeśli zostały sklonowane repozytorium Azure-iot-sdk-c, z usługi GitHub przy użyciu opcji--cyklicznego, znajdziesz tutaj tej biblioteki udostępnionej narzędzie:

```C
.\\c-utility
```

Jeśli biblioteka nie zostały sklonowane, możesz go znaleźć [tutaj](https://github.com/Azure/azure-c-shared-utility).

W bibliotece udostępnionej narzędzie znajdzie się następujący folder:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ten folder zawiera rozwiązania programu Visual Studio o nazwie **— makro\_utils\_h\_generator.sln**:

  ![Zrzut ekranu przedstawiający maco_utils_h_generator rozwiązania programu Visual Studio](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Generuje programie, w tym rozwiązaniu **— makro\_utils.h** pliku. Istnieje makro domyślne\_pliku utils.h zawarta w zestawie SDK. Dzięki temu rozwiązaniu można zmodyfikować niektóre parametry, a następnie ponownego utworzenia pliku nagłówka, w oparciu o te parametry.

Są dwa parametry klucza za zajmowanie się **nArithmetic** i **nMacroParameters** które są określone w te dwa wiersze, w makro\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Te wartości są domyślne parametry zawarta w zestawie SDK. Każdy parametr ma mają następujące znaczenie:

* nMacroParameters — Określa, jak wiele parametrów, które mogą mieć w jednej instrukcji DECLARE\_modelu definicji makra.
* nArithmetic — steruje łączna liczba elementów członkowskich dozwolone w modelu.

Powodów, dla którego te parametry są ważne jest, ponieważ kontrolują one jak duży może być modelu. Na przykład należy wziąć pod uwagę tę definicję modelu:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Jak wspomniano wcześniej, **DECLARE\_modelu** jest po prostu makro C. Nazwy modelu i **WITH\_danych** — instrukcja (jeszcze innego makra) czy parametry **DECLARE\_modelu**. **nMacroParameters** definiuje, ile parametry mogą być zawarte w **DECLARE\_modelu**. Definiuje, ile danych zdarzenia i akcje deklaracje mogą mieć. Jako takie przy użyciu domyślnego limitu 124 oznacza to, czy można zdefiniować model przy użyciu kombinacji około 60 działaniach i zdarzeniach danych. Jeśli zostanie podjęta próba przekracza ten limit, otrzymasz błędy kompilatora, które wyglądać mniej więcej tak:

  ![Zrzut ekranu przedstawiający błędy kompilatora parametrów — makro](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**NArithmetic** parametr jest więcej na temat wewnętrzne działanie języka — makro niż aplikacja.  Kontroluje, łączna liczba elementów członkowskich może mieć w modelu, w tym **DECLARE_STRUCT** makra. Jeśli wykonywanych błędy kompilatora, takie jak to, a następnie spróbuj użyć zwiększenie **nArithmetic**:

   ![Zrzut ekranu przedstawiający błędy kompilatora arytmetyczne](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Jeśli chcesz zmienić te parametry, zmodyfikuj wartości w makrze\_utils.tt plików, należy ponownie skompilować makro\_utils\_h\_generator.sln rozwiązania, a następnie uruchom program skompilowany. Po wykonaniu czynności nowe makro\_utils.h pliku jest generowana i umieszczane w.\\ Typowe\\inc katalogu.

Aby można było używać nowej wersji — makro\_utils.h, Usuń **serializator** obejmują pakiet NuGet z rozwiązania i w jego miejsce **serializator** projektu programu Visual Studio. Dzięki temu swój kod, aby skompilować na kod źródłowy biblioteki serializatora. W tym zaktualizowanego — makro\_utils.h. Jeśli chcesz to zrobić dla **simplesample\_amqp**, start, usuwając pakiet NuGet biblioteki serializator z rozwiązania:

   ![Zrzut ekranu przedstawiający usuwanie pakietu NuGet biblioteki serializatora](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Następnie dodaj ten projekt do rozwiązania programu Visual Studio:

> . \\c\\serializator\\kompilacji\\windows\\serializer.vcxproj
> 
> 

Gdy skończysz, rozwiązanie powinien wyglądać następująco:

   ![Zrzut ekranu przedstawiający simplesample_amqp rozwiązania Visual Studio](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Teraz podczas kompilacji rozwiązania, zaktualizowano — makro\_utils.h znajduje się plik binarny.

Należy pamiętać, że zwiększenie wartości te są wystarczająco wysoka, będą mogli przekraczać limity kompilatora. Do tej pory **nMacroParameters** głównego parametru za pomocą którego zainteresowanych. Specyfikacja C99 Określa, że co najmniej 127 parametrów są dozwolone w definicji makra. Kompilator Microsoft dokładnie następujące specyfikacje (i jest objęta limitem 127), dzięki czemu nie będzie można zwiększyć **nMacroParameters** ponad wartość domyślną. Inne kompilatory mogą zezwalać na w tym celu (na przykład GNU obsługiwanych przez kompilator wyższy limit).

Do tej pory gdy Omówiliśmy już prawie wszystko, co musisz wiedzieć na temat sposobu pisania kodu za pomocą **serializator** biblioteki. Przed zawarciem, Wróćmy niektóre tematy z poprzednich artykułów, które możesz się zastanawiać, temat.

## <a name="the-lower-level-apis"></a>Interfejsy API niższego poziomu
Przykładowa aplikacja, na którym ten artykuł koncentruje się jest **simplesample\_amqp**. W tym przykładzie użyto wyższego poziomu (non -**LL**) interfejsy API do wysyłania zdarzeń i odbierania komunikatów. Użycie tych interfejsów API, jest uruchamiany wątku w tle, który zajmuje się zarówno wysyłanie zdarzeń i odbieranie komunikatów. Jednak można użyć interfejsów API niższego poziomu (wszystkie) aby nie zawierała tego wątku w tle i wykonać jawną kontrolę nad tym podczas wysyłania zdarzeń lub odbieranie komunikatów z chmury.

Zgodnie z opisem w [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md), istnieje zestaw funkcji, która składa się z interfejsami API wyższego poziomu:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_Destroy

Te interfejsy API zostały przedstawione w **simplesample\_amqp**.

Istnieje również analogiczne zestaw interfejsów API niższego poziomu.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Należy zwrócić uwagę na to, czy interfejsy API niższego poziomu działają dokładnie tak samo, zgodnie z opisem w poprzedniej artykuły. Jeśli chcesz, aby wątku w tle do obsługi zdarzeń wysyłanie i odbieranie wiadomości, można użyć pierwszy zestaw interfejsów API. Drugi zestaw interfejsów API jest używane, jeśli chcesz, aby jawną kontrolę nad tym podczas wysyłania i odbierania danych z usługi IoT Hub. Któryś zbiór interfejsów API pracy równie dobrze z **serializator** biblioteki.

Przykład użycia interfejsów API niższego poziomu z **serializator** biblioteki, zobacz **simplesample\_http** aplikacji.

## <a name="additional-topics"></a>Tematy dodatkowe
Kilka innych tematów, warto zauważyć, są ponownie właściwość obsługi, przy użyciu poświadczeń alternatywnych urządzenia i opcji konfiguracji. Są to wszystkie tematy poruszane w [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md). Główną kwestią jest, że wszystkie te funkcje działają tak samo jak przy użyciu **serializator** biblioteki tak samo, jak za pomocą **usługi IoTHubClient** biblioteki. Na przykład, jeśli chcesz dołączyć właściwości zdarzenia z modelu, możesz użyć **IoTHubMessage\_właściwości** i **mapy**\_**AddorUpdate**, tak jak opisano wcześniej:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Czy od wygenerowania zdarzenia **serializator** biblioteki lub utworzyć ręcznie przy użyciu **usługi IoTHubClient** biblioteki nie ma znaczenia.

Dla poświadczenia alternatywnego urządzenia, za pomocą **usługi IoTHubClient\_LL\_Utwórz** działa równie dobrze jak **usługi IoTHubClient\_CreateFromConnectionString** dla przydzielanie **IOTHUB\_klienta\_obsługi**.

Na koniec Jeśli używasz **serializator** biblioteki, można ustawić opcje konfiguracji **usługi IoTHubClient\_LL\_SetOption** po prostu, tak jak w przypadku korzystania z **Usługi IoTHubClient** biblioteki.

Funkcja, która jest unikatowa dla **serializator** biblioteki są inicjowanie interfejsów API. Przed rozpoczęciem pracy z biblioteką, należy wywołać **serializator\_init**:

```C
serializer_init(NULL);
```

Jest to po prostu, przed wywołaniem **usługi IoTHubClient\_CreateFromConnectionString**.

Podobnie po zakończeniu pracy z biblioteką, ostatnie wywołanie wprowadzisz jest **serializator\_deinit**:

```C
serializer_deinit();
```

W przeciwnym razie wszystkie funkcje wymienione powyżej działać tak samo **serializator** biblioteki tak, jak w **usługi IoTHubClient** biblioteki. Aby uzyskać więcej informacji na temat tych tematów, zobacz [poprzednim artykule](iot-hub-device-sdk-c-iothubclient.md) w tej serii.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano szczegółowo unikatowe aspekty **serializator** zawarte w bibliotece **zestaw SDK urządzeń Azure IoT dla języka C**. Przy użyciu informacji pod warunkiem, że powinien dysponować dobrą znajomością przedstawiającym sposób użycia modeli do wysyłania zdarzeń i odbierać komunikaty z Centrum IoT Hub.

To jest koniec również częścią trzyczęściowej serii na temat programowania aplikacji za pomocą **zestaw SDK urządzeń Azure IoT dla języka C**. Powinna to być wystarczająco dużo informacji nie tylko ułatwiające rozpoczęcie pracy, ale umożliwiają dokładne zrozumienie, jak działają interfejsy API. Aby uzyskać dodatkowe informacje istnieje kilka przykładów w zestawie SDK, nieuwzględnione w tym miejscu. W przeciwnym razie [dokumentacji zestawu SDK Azure IoT](https://github.com/Azure/azure-iot-sdk-c) będzie użyteczny, aby uzyskać dodatkowe informacje.

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz [wdrażanie sztucznej Inteligencji na urządzenia brzegowe za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
