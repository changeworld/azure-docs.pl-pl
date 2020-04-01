---
title: Integracja z partnerami obsługującymi czujniki
description: W tym artykule opisano integrację z partnerami czujników.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437464"
---
# <a name="sensor-partner-integration"></a>Integracja z partnerami obsługującymi czujniki

Ten artykuł zawiera informacje na temat składnika Usługi Azure FarmBeats **Translator,** który umożliwia integrację z partnerami czujników.

Za pomocą tego składnika partnerzy mogą integrować się z FarmBeats przy użyciu interfejsów API Pola Danych FarmBeats i wysyłać dane urządzenia klienta i dane telemetryczne do pola Danych FarmBeats. Gdy dane są dostępne w FarmBeats, jest wizualizowany za pomocą FarmBeats Accelerator i może służyć do fuzji danych i do tworzenia modeli uczenia maszynowego / sztucznej inteligencji.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby opracować składnik Translator, należy następujące poświadczenia, które umożliwią dostęp do Interfejsów API FarmBeats.

- Punkt końcowy interfejsu API
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta
- Ciąg połączenia EventHub

Zobacz tę sekcję, aby uzyskać powyższe poświadczenia: [Włącz integrację urządzeń](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Rozwój tłumaczy

**Integracja oparta na interfejsie API REST**

Możliwości integracji danych czujników FarmBeats są udostępniane za pośrednictwem interfejsu API REST. Funkcje obejmują definicję metadanych, inicjowanie obsługi administracyjnej urządzeń i czujników oraz zarządzanie urządzeniami i czujnikami.

**Połknienia danych telemetrycznych**

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hubs do przetwarzania. Usługa Azure Event Hubs to usługa umożliwiająca pozyskiwania danych w czasie rzeczywistym (telemetrii) z podłączonych urządzeń i aplikacji.

**Tworzenie API**

Interfejsy API zawierają dokumentację techniczną Swagger. Aby uzyskać więcej informacji na temat interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsSwagger).

**Authentication**

FarmBeats używa uwierzytelniania usługi Microsoft Azure Active Directory.Usługa Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji.

Aby uzyskać więcej informacji, zobacz [Usługa Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

Usługa FarmBeats Datahub używa uwierzytelniania na okaziciela, które wymaga następujących poświadczeń:
   - Identyfikator klienta
   - Klucz tajny klienta
   - Identyfikator dzierżawy

Przy użyciu tych poświadczeń, obiektu wywołującego można zażądać tokenu dostępu. Token musi być wysyłany w kolejnych żądaniach interfejsu API w sekcji nagłówka w następujący sposób:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Poniższy przykładowy kod języka Python daje token dostępu, który może służyć do kolejnych wywołań interfejsu API do FarmBeats.

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**Nagłówki żądań HTTP**

Oto najczęstsze nagłówki żądań, które należy określić podczas wywoływania interfejsu API do Pola Danych FarmBeats.


**Nagłówka** | **Opis i przykład**
--- | ---
Content-Type | Format żądania (Typ zawartości:<format>aplikacja/ ). W przypadku interfejsów API usługi FarmBeats Datahub format jest JSON. Typ zawartości: aplikacja/json
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja:> tokenu dostępu na okaziciela <
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API usługi FarmBeats Datahub format jest JSON. Zaakceptuj: wniosek/json

**Żądania interfejsu API**

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET, POST lub PUT), adres URL usługi INTERFEJSU API, jednolity identyfikator zasobu (URI) z zasobem, aby wykonać kwerendę, przesłać dane, zaktualizować lub usunąć oraz jeden lub więcej nagłówków żądań HTTP. Adres URL usługi interfejsu API jest punktem końcowym interfejsu API, który podajesz. Oto przykład: https://\<yourdatahub-website-name>.azurewebsites.net

Opcjonalnie można uwzględnić parametry kwerendy w wywołaniach GET, aby filtrować, ograniczać rozmiar i sortować dane w odpowiedziach.

Poniższe przykładowe żądanie jest, aby uzyskać listę urządzeń.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniższe przykładowe żądanie polega na utworzeniu urządzenia. (Ten przykład ma wejście JSON z treścią żądania.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON jest wspólnym formatem danych niezależnym od języka, który zapewnia prostą reprezentację tekstową dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [json.org](http://json.org).

## <a name="metadata-specifications"></a>Specyfikacje metadanych

FarmBeats Datahub ma następujące interfejsy API, które umożliwiają partnerom urządzeń tworzenie metadanych urządzeń lub czujników oraz zarządzanie nimi.

- /**DeviceModel**: DeviceModel odpowiada metadanym urządzenia, takie jak producent i typ urządzenia, które jest bramą lub węzłem.
- /**Urządzenie**: Urządzenie odpowiada urządzeniu fizycznemu obecnemu w gospodarstwie.
- /**SensorModel**: SensorModel odpowiada metadane czujnika, takie jak producent, typ czujnika, który jest analogowy lub cyfrowy, oraz pomiar czujnika, taki jak temperatura otoczenia i ciśnienie.
- /**Czujnik**: Czujnik odpowiada czujnikowi fizycznemu, który rejestruje wartości. Czujnik jest zazwyczaj podłączony do urządzenia z identyfikatorem urządzenia.

  **Model urządzenia** |  |
  --- | ---
  Typ (węzeł, brama)  | Typ urządzenia — węzeł lub brama |
  Producent  | Nazwa producenta |
  Kod_produktu  | Kod produktu urządzenia lub nazwa lub numer modelu. Na przykład EnviroMonitor #6800. |
  Porty  | Nazwa i typ portu, który jest cyfrowy lub analogowy.  |
  Nazwa  | Nazwa do identyfikowania zasobu. Na przykład nazwa modelu lub nazwa produktu. |
  Opis  | Podaj opis modelu. |
  Właściwości  | Dodatkowe właściwości producenta. |
  **Urządzenie** |  |
  IdentyfikatorModelId urządzenia  |Identyfikator skojarzonego modelu urządzenia. |
  Identyfikator sprzętowy   |Unikatowy identyfikator urządzenia, taki jak adres MAC.  |
  RaportowanieInterwalne |Interwał raportowania w sekundach. |
  Lokalizacja    |Szerokość urządzenia (-90 do +90), długość geograficzna (-180 do 180) i wysokość (w metrach). |
  Jednostka nadrzędna | Identyfikator urządzenia nadrzędnego, do którego jest podłączone to urządzenie. Na przykład jeśli węzeł jest połączony z bramą, węzeł ma parentDeviceID jako bramę. |
  Nazwa  | Nazwa identyfikującego zasób. Partnerzy urządzeń muszą wysłać nazwę, która jest zgodna z nazwą urządzenia po stronie partnera urządzenia. Jeśli nazwa urządzenia jest zdefiniowana przez użytkownika po stronie partnera urządzenia, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do Gruperów Rolnych.  |
  Opis  | Podaj opis.  |
  Właściwości  |Dodatkowe właściwości producenta.  |
  **CzujnikModel** |  |
  Typ (analogowy, cyfrowy)  |Wspomnieć o czujniku analogowym lub cyfrowym.|
  Producent  | Nazwa producenta. |
  Kod_produktu  | Kod produktu lub nazwa lub numer modelu. Na przykład RS-CO2-N01.  |
  SensorZezuje nazwę >  | Nazwa miary czujnika. Obsługiwane są tylko małe litery. W przypadku pomiarów z różnych głębokości należy określić głębokość. Na przykład soil_moisture_15cm. Ta nazwa musi być zgodna z danymi telemetrycznymi. |
  SensorZezamia > DataType  | Typ danych telemetrycznych. Obecnie obsługiwane jest double. |
  SensorZezje typ >  | Typ pomiaru danych telemetrycznych czujnika. Poniżej znajdują się typy zdefiniowane przez system: AmbientTemperature, CO2, Głębokość, Elektryczność, LeafWetness, Długość, LiquidLevel, Azotan, O2, PH, Fosforan, PointInTime, Potas, Ciśnienie, RainGauge, WzględnaHumidity, Zasolenie, GlebaPrzestrzenianie, GlebaTemperatyzacja, SolarRadiation, Stan, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Aby dodać więcej, zapoznaj się z interfejsem API /ExtendedType.
  SensorZezje > jednostkę | Jednostka danych telemetrycznych czujnika. Poniżej znajdują się jednostki zdefiniowane przez system: NoUnit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, Merkury, PSI, Milimetr, CentiMeter, Meter, Inch, Stopy, Mile, Kilometr, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Sekundy, UnixTimestamp, MicroMolPerMeterSquaredPerSecond i InchesPerHour. Aby dodać więcej, zapoznaj się z interfejsem API /ExtendedType.
  SensorMeasures > AggregationType  | Brak, średnia, maksymalna, minimalna lub StandardDeviation.
  SensorZezuje > głębokość  | Głębokość czujnika w centymetrach. Na przykład pomiar wilgoci 10 cm pod ziemią.
  SensorZezamia > Opis  | Podaj opis pomiaru.
  Nazwa  | Nazwa do identyfikowania zasobu. Na przykład nazwa modelu lub nazwa produktu.
  Opis  | Podaj opis modelu.
  Właściwości  | Dodatkowe właściwości producenta.
  **Czujnik**  |  |
  Identyfikator sprzętowy  | Unikatowy identyfikator czujnika ustawionego przez producenta.
  SensorModelId (CzujnikModelId)  | Identyfikator powiązanego modelu czujnika.
  Lokalizacja  | Szerokość czujnika (-90 do +90), długość geograficzna (-180 do 180) i wysokość (w metrach).
  Nazwa > portu  |Nazwa i typ portu podłączonego do czujnika w urządzeniu. Musi to być taka sama nazwa, jak zdefiniowano w modelu urządzenia.
  DeviceId  | Identyfikator urządzenia, do które podłączony jest czujnik.
  Nazwa  | Nazwa identyfikującego zasób. Na przykład nazwa czujnika lub nazwa produktu i numer modelu lub kod produktu.
  Opis  | Podaj opis.
  Właściwości  | Dodatkowe właściwości producenta.

 Aby uzyskać informacje na temat każdego z obiektów i ich właściwości, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Ten identyfikator musi zostać zachowany przez translatora do zarządzania urządzeniami i synchronizacji metadanych.


**Synchronizacja metadanych**

Translator powinien wysyłać aktualizacje metadanych. Na przykład scenariusze aktualizacji to zmiana nazwy urządzenia lub czujnika oraz zmiana lokalizacji urządzenia lub czujnika.

Translator powinien mieć możliwość dodawania nowych urządzeń lub czujników, które zostały zainstalowane przez użytkownika post łączenia FarmBeats. Podobnie, jeśli urządzenie lub czujnik został zaktualizowany przez użytkownika, to samo należy zaktualizować w FarmBeats dla odpowiedniego urządzenia lub czujnika. Typowe scenariusze, które wymagają aktualizacji urządzenia lub czujnika są zmiany w lokalizacji urządzenia lub dodanie czujników w węźle.


> [!NOTE]
> Usuwanie nie jest obsługiwane dla metadanych urządzenia lub czujnika.
>
> Aby zaktualizować metadane, należy wywołać /Get/{id} na urządzeniu lub czujniku, zaktualizować zmienione właściwości, a następnie wykonać /Put/{id} tak, aby wszystkie właściwości ustawione przez użytkownika nie zostały utracone.

### <a name="add-new-types-and-units"></a>Dodawanie nowych typów i jednostek

FarmBeats obsługuje dodawanie nowych typów pomiarów czujników i jednostek. Aby uzyskać więcej informacji na temat interfejsu API /ExtendedType, zobacz [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="telemetry-specifications"></a>Specyfikacje telemetryczne

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hubs do przetwarzania. Usługa Azure Event Hubs to usługa umożliwiająca pozyskiwania danych w czasie rzeczywistym (telemetrii) z podłączonych urządzeń i aplikacji.

## <a name="send-telemetry-data-to-farmbeats"></a>Wysyłanie danych telemetrycznych do FarmBeats

Aby wysłać dane telemetryczne do FarmBeats, należy utworzyć klienta, który wysyła wiadomości do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat danych telemetrycznych, zobacz [Wysyłanie danych telemetrycznych do centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Oto przykładowy kod języka Python, który wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Format wiadomości kanonicznej jest następujący:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
Wszystkie nazwy kluczy w telemetrii JSON powinny być małe litery. Przykładami są deviceid i sensordata.

Na przykład oto komunikat telemetrii:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> Poniższe sekcje są związane z innymi zmianami (np. Interfejs użytkownika, zarządzanie błędami itp.) partner czujnika może się odwołać przy opracowywaniu komponentu Translator.


## <a name="link-a-farmbeats-account"></a>Łączenie konta FarmBeats

Po zakupie i wdrożeniu przez klientów urządzeń lub czujników mogą uzyskać dostęp do danych urządzenia i danych telemetrycznych w portalu partnerów urządzeń jako usługi (SaaS). Partnerzy urządzeń mogą umożliwić klientom łączenie ich konta z ich wystąpieniem FarmBeats na platformie Azure, zapewniając sposób wprowadzania następujących poświadczeń:

   - Nazwa wyświetlana (opcjonalne pole dla użytkowników do zdefiniowania nazwy dla tej integracji)
   - Punkt końcowy interfejsu API
   - Identyfikator dzierżawy
   - Identyfikator klienta
   - Klucz tajny klienta
   - Parametry połączenia EventHub
   - Data rozpoczęcia

   > [!NOTE]
   > Data rozpoczęcia umożliwia historyczne zestawienie danych, czyli dane z daty określonej przez użytkownika.

## <a name="unlink-farmbeats"></a>Odłączyć FarmBeats

Partnerzy urządzeń mogą umożliwić klientom odłączenie istniejącej integracji FarmBeats. Odłączanie FarmBeats nie należy usuwać żadnych urządzeń lub czujnika metadanych, który został utworzony w FarmBeats Datahub. Odłączanie wykonuje następujące czynności:

   - Zatrzymuje przepływ telemetrii.
   - Usuwa i usuwa poświadczenia integracji na partnera urządzenia.

## <a name="edit-farmbeats-integration"></a>Edytuj integrację FarmBeats

Partnerzy urządzeń mogą umożliwić klientom edytowanie ustawień integracji FarmBeats, jeśli zmieni się klucz tajny klienta lub parametry połączenia. W takim przypadku można edytować tylko następujące pola:

   - Nazwa wyświetlana (jeśli dotyczy)
   - Klucz tajny klienta (powinien być wyświetlany w formacie "2x8************" lub pokaż/ukryj zamiast zwykłego tekstu)
   - Parametry połączenia (powinny być wyświetlane w formacie "2x8*************" lub Pokaż/Ukryj zamiast zwykłego tekstu)

## <a name="view-the-last-telemetry-sent"></a>Wyświetlanie ostatniej wysłanej telemetrii

Partnerzy urządzeń mogą umożliwić klientom wyświetlanie sygnatury czasowej ostatniej wysłanej telemetrii, która znajduje się w obszarze **Wysłane dane telemetryczne**. Jest to czas, w którym najnowsze dane telemetryczne został pomyślnie wysłany do FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Rozwiązywanie problemów i zarządzanie błędami

**Rozwiązywanie problemów z opcją lub pomocą techniczną**

Jeśli klient nie może odbierać danych urządzenia lub danych telemetrycznych w wystąpieniu FarmBeats określony, partner urządzenia należy zapewnić obsługę i mechanizm rozwiązywania problemów.

**Przechowywanie danych telemetrycznych**

Dane telemetryczne powinny być również przechowywane przez wstępnie zdefiniowany okres czasu, dzięki czemu może być przydatne w debugowaniu lub ponownym wysłaniu danych telemetrycznych, jeśli wystąpi błąd lub utrata danych.

**Zarządzanie błędami lub powiadomienie o błędzie**

Jeśli błąd wpływa na metadane urządzenia lub czujnika lub integracji danych lub przepływ danych telemetrycznych w systemie partnera urządzenia, klient powinien otrzymać powiadomienie. Należy również opracować i wdrożyć mechanizm rozwiązywania wszelkich błędów.

**Lista kontrolna połączeń**

Producenci lub partnerzy urządzeń mogą korzystać z następującej listy kontrolnej, aby upewnić się, że poświadczenia podane przez klienta są dokładne:

   - Sprawdź, czy token dostępu jest odbierany przy użyciu poświadczeń, które zostały dostarczone.
   - Sprawdź, czy wywołanie interfejsu API zakończy się pomyślnie z tokenem dostępu, który został odebrany.
   - Sprawdź, czy nawiązanie połączenia klienta eventhub.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu API REST, zobacz [INTERFEJS API REST](rest-api-in-azure-farmbeats.md).
