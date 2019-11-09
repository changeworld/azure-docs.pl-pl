---
title: Integracja z partnerem czujnika
description: Opisuje integrację z partnerem czujnika
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e90284ce2f8ea37eb9249822e38cef04e1356f59
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889576"
---
# <a name="sensor-partner-integration"></a>Integracja z partnerem czujnika

Ten artykuł zawiera informacje na temat składnika usługi Azure FarmBeats **translator** , który umożliwia integrację z partnerem czujnika.

Korzystając z tego składnika, partnerzy mogą opracowywać czujniki, które integrują się z FarmBeats, wykorzystując nasz interfejs API i wysyłając dane urządzenia klienta oraz telemetrię do centrum danych FarmBeats. Dane są wizualizowane przy użyciu akceleratora FarmBeats. Dane mogą służyć do łączenia danych oraz do tworzenia modeli maszyn/sztucznej analizy.

## <a name="link-farmbeats-account"></a>Połącz konto FarmBeats

Gdy klienci zakupili i wdrożono urządzenia/czujniki, mogą uzyskać dostęp do danych urządzenia i telemetrii w portalu SaaS (oprogramowanie jako usługa). Partnerzy urządzeń muszą umożliwić klientom łączenie ich kont z wystąpieniem FarmBeats na platformie Azure. Poniższe poświadczenia są wymagane do wypełnienia przez Integrator klienta/systemu:

   - Nazwa wyświetlana (opcjonalne pole dla użytkownika do zdefiniowania nazwy dla tej integracji)
   - Punkt końcowy interfejsu API
   - Identyfikator dzierżawy
   - Identyfikator klienta
   - Klucz tajny klienta
   - Parametry połączenia EventHub
   - Data rozpoczęcia

   > [!NOTE]
   > Data rozpoczęcia umożliwia historyczne strumieniowe źródło danych, czyli dane od daty określonej przez użytkownika.

## <a name="unlink-farmbeats"></a>Odłącz FarmBeats

Klienci mają możliwość odłączenia istniejącej integracji FarmBeats. Odłączanie FarmBeats nie powinno usuwać żadnych metadanych urządzenia/czujnika, które zostały utworzone w centrum danych klienta. Odłączanie wykonuje następujące czynności:

   - Powoduje zatrzymanie przepływu telemetrii.
   - Usuwa i wymazuje poświadczenia integracji w partnerze urządzeń.

## <a name="edit-farmbeats-integration"></a>Edytuj integrację FarmBeats

Klient może edytować ustawienia integracji FarmBeats, jeśli klucz tajny klienta lub parametry połączenia są zmieniane. W takim przypadku klient może edytować tylko następujące pola:

   - Nazwa wyświetlana (jeśli dotyczy)
   - Wpis tajny klienta (powinien być wyświetlany w formacie "2X8 * * * * * * * * *
   - Parametry połączenia (powinny być wyświetlane w postaci "2X8 * * * * * * * * * * *," format lub Pokaż/Ukryj funkcję, a nie zwykły tekst)

## <a name="view-last-telemetry-sent"></a>Wyświetl ostatnie wysłane dane telemetryczne

Można wyświetlić sygnaturę czasową ostatniej **wysyłanej telemetrii**. Jest to czas, w którym Najnowsza Telemetria została pomyślnie wysłana do FarmBeats.

## <a name="translator-development"></a>Programowanie w usłudze translator

**Integracja oparta na interfejsie API REST**

Możliwości integracji danych z czujnika FarmBeats są udostępniane za pośrednictwem interfejsu API REST. Możliwości obejmują definicje metadanych, Inicjowanie obsługi administracyjnej urządzenia/czujnika, urządzenia i zarządzanie czujnikami.

**Pozyskiwanie danych telemetrycznych**

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hub do przetwarzania. Azure EventHub to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji.

**Programowanie interfejsu API**

Interfejsy API zawierają dokumentację techniczną struktury Swagger. Aby uzyskać więcej informacji na temat interfejsów API i odpowiadających im żądań/odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**Uwierzytelnianie**

FarmBeats wykorzystuje Active Directory uwierzytelniania Microsoft Azure. Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji.

Aby uzyskać więcej informacji, zobacz [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats centrum danych używa uwierzytelniania okaziciela, który wymaga następujących poświadczeń:
   - Identyfikator klienta
   - Klucz tajny klienta
   - Identyfikator dzierżawy

Korzystając z powyższych poświadczeń, obiekt wywołujący może zażądać tokenu dostępu, który musi zostać wysłany w kolejnych żądaniach interfejsu API w sekcji nagłówka w następujący sposób:

```json
headers = {"Authorization": "Bearer " + access_token, …} 
```

Poniżej znajduje się przykładowy kod w języku Python, który zapewnia token dostępu, który może być używany do kolejnych wywołań interfejsu API FarmBeats: 

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

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do centrum danych FarmBeats:


**Nagłówek** | **Opis i przykład**
--- | ---
Content-Type | Format żądania (Content-Type: Application/<format>) dla FarmBeats interfejsu API usługi Data Hub to JSON. Content-Type: Application/JSON
Autoryzacja | Określa token dostępu wymagany do nadawania autoryzacji wywołania interfejsu API: okaziciela < token dostępu >
odebrać | Format odpowiedzi. W przypadku interfejsów API usługi FarmBeats Data Hub format jest akceptowany przez kod JSON: Application/JSON

**Żądania interfejsu API**

Aby utworzyć żądanie interfejsu API REST (Representational State Transfer), należy połączyć metodę HTTP (GET, POST lub PUT), adres URL usługi API Service, identyfikator URI (Uniform Resource Identifier) do zasobu, który będzie wysyłać zapytania, przesłać dane do, zaktualizować lub usunąć oraz co najmniej jedno żądanie HTTP nagłówka. Adres URL usługi interfejsu API jest punktem końcowym interfejsu API dostarczonym przez klienta. Oto przykład: https://\<yourdatahub-Website-Name >. azurewebsites. NET

Opcjonalnie można uwzględnić parametry zapytania dla wywołań GET do filtrowania, ograniczyć rozmiar i sortować dane w odpowiedziach.

Poniżej przykładowego żądania jest uzyskanie listy urządzeń:

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniżej przykładowego żądania jest utworzenie urządzenia (ten przykład zawiera wejściowy kod JSON z treścią żądania).

```azurepowershell-interactive
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON (JavaScript Object Notation) to typowy, niezależny od języka format danych, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specyfikacje metadanych

FarmBeats Data Hub udostępnia następujące interfejsy API, które umożliwiają partnerom urządzeń tworzenie metadanych urządzenia/czujnika i zarządzanie nimi.  

- /**DeviceModel** — model urządzenia odpowiada metadanych urządzenia, takich jak producent, typ urządzenia albo brama lub węzeł.  
- urządzenie **/urządzenia** odpowiada urządzeniu fizycznemu znajdującemu się w farmie.
- /**model** czujnika odnosi się do meta danych czujnika, takiego jak producent, typ czujnika albo analogowe lub cyfrowe, środki czujnika, takie jak temperatura otoczenia, ciśnienie itp.,
- /czujnik **czujnika** odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.

  Model urządzenia| DeviceModel odpowiada na metadane urządzenia, takie jak producent, typ urządzenia albo brama lub węzeł.
  --- | ---
  Typ (węzeł, brama)  | 1 gwiazdka |
  Producent  | 2 gwiazdki |
  ProductCode  | Kod produktu urządzenia lub nazwa/numer modelu. Na przykład EnviroMonitor # 6800 |
  Porty  | Nazwa i typ portu (cyfrowe/analogowe)  |
  Nazwa  | Nazwa identyfikująca zasób. Na przykład nazwa modelu/Product Name |
  Opis  | Podaj znaczący opis modelu |
  Właściwości  | Dodatkowe właściwości producenta |
  **Pliku** | **Urządzenie odpowiada urządzeniu fizycznemu znajdującemu się w farmie. Każde urządzenie ma unikatowy identyfikator urządzenia** |
DeviceModelId  |Identyfikator skojarzonego modelu urządzenia. |
HardwareId   |Unikatowy identyfikator urządzenia, na przykład adres MAC itp.,  |
ReportingInterval |Interwał raportowania w sekundach |
Lokalizacja    |Urządzenia Latitude (-90 do + 90)/Longitude (-180 do 180)/Elevation (w metrach) |
ParentDeviceId | Identyfikator urządzenia nadrzędnego, z którym jest połączone to urządzenie. Na przykład. Węzeł połączony z bramą; węzeł będzie parentDeviceID jako brama |
  Nazwa  | Nazwa identyfikująca zasób.  Partnerzy urządzeń będą musieli wysłać nazwę zgodną z nazwą urządzenia po stronie partnera urządzeń. Jeśli nazwa urządzenia jest zdefiniowana przez użytkownika po stronie partnera urządzeń, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do FarmBeats  |
  Opis  | Podaj znaczący opis  |
  Właściwości  |Dodatkowe właściwości producenta  |
  **Model czujnika** | SensorModel odnosi się do metadanych czujnika, takich jak producent, typ czujnika albo analogowe lub cyfrowe, pomiar czujnika, taki jak temperatura otoczenia, ciśnienie itp. |
  Typ (analogowy, cyfrowy)  |Wzmianka o czujniku analogowym lub cyfrowym|
  instrukcj  | Nazwa producenta |
  ProductCode  | Kod produktu lub nazwa modelu/numer. Na przykład RS-CO2-N01  |
  Nazwa > SensorMeasures  | Nazwa miary czujnika. Obsługiwane jest tylko małe litery. W przypadku pomiaru z różnych głębokości należy określić głębokość. Na przykład, soil_moisture_15cm ta nazwa musi być spójna z danymi telemetrycznymi. |
  SensorMeasures > DataType  | Typ danych telemetrii. Obecnie jest obsługiwana Podwójna precyzja  |
  Typ > SensorMeasures  | Typ pomiaru danych telemetrii czujnika. Poniżej przedstawiono typy zdefiniowane przez system: AmbientTemperature, CO2, Głębokość, ElectricalConductivity, LeafWetness, długość, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, zasolenie, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Aby dowiedzieć się więcej, zapoznaj się z interfejsem API/ExtendedType
  Jednostka > SensorMeasures | Jednostka danych telemetrii czujnika. Poniżej przedstawiono jednostki zdefiniowane przez system: nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetr, centymetr, metr, cal, stopy, mila, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stopień, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, wartość procentowa, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour, aby dodać więcej, zapoznaj się z/ Rozszerzony interfejs API
  SensorMeasures > agregacji  | Brak, średnia, maksimum, minimum, StandardDeviation
  Głębokość > SensorMeasures  | Głębokość czujnika w centymetrach (na przykład pomiar wilgoci 10 cm pod ziemią)
  sensorMeasures > Opis  | Podaj znaczący opis miary
  name  | Nazwa identyfikująca zasób. Na przykład nazwa modelu/Product Name
  description  | Podaj znaczący opis modelu
  properties  | Dodatkowe właściwości producenta
  **Czujnik**  |
  HardwareId  | Unikatowy identyfikator dla czujnika ustawiony przez producenta
  SensorModelId  | Identyfikator skojarzonego modelu czujnika.
  location  | Czujnik Latitude (-90 do + 90)/Longitude (-180 do 180)/Elevation (w metrach)
  Nazwa > portu  |Nazwa i typ portu, z którym jest połączony czujnik na urządzeniu. Ta nazwa musi być taka sama, jak zdefiniowana w modelu urządzenia
  deviceId  | Identyfikator urządzenia, z którym jest połączony czujnik
  name  | Nazwa identyfikująca zasób. Na przykład nazwa czujnika/Nazwa produktu i numer modelu/Kod produktu.
  description  | Podaj znaczący opis
  properties  | Dodatkowe właściwości producenta

 Aby uzyskać informacje na temat poszczególnych obiektów i ich właściwości, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Ten identyfikator musi być zachowywany przez translator w celu zarządzania urządzeniami i synchronizowania metadanych.


**Synchronizacja metadanych**

Translator powinien wysyłać aktualizacje metadanych. Na przykład scenariusze aktualizacji to zmiana nazwy urządzenia/czujnika, zmiana lokalizacji urządzenia/czujnika.

Translator powinien mieć możliwość dodawania nowych urządzeń i/lub czujników, które zostały zainstalowane przez użytkownika po utworzeniu linku FarmBeats. Podobnie, jeśli urządzenie/Czujnik został zaktualizowany przez użytkownika, ta sama powinna zostać zaktualizowana w FarmBeats dla odpowiedniego urządzenia/czujnika. Typowymi scenariuszami dotyczącymi aktualizacji urządzenia/czujnika mogą być: zmiana lokalizacji urządzenia, dodanie czujników w węźle itd.


> [!NOTE]
> Usuwanie nie jest obsługiwane w przypadku metadanych urządzenia/czujnika.
>
> Aby zaktualizować metadane, należy wywołać/Get/{ID} na urządzeniu/czujniku, zaktualizować zmienione właściwości, a następnie wykonać/Put/{ID}, tak aby wszystkie właściwości ustawione przez użytkownika nie zostały utracone

### <a name="adding-new-typesunit"></a>Dodawanie nowych typów/jednostek

FarmBeats obsługuje dodawanie nowych typów i jednostek miary czujnika. Aby uzyskać więcej informacji o interfejsie API/ExtendedType, [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specyfikacje telemetrii

Dane telemetryczne są mapowane na komunikat kanoniczny, który jest publikowany w usłudze Azure Event Hub do przetwarzania. Azure EventHub to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji.

## <a name="send-telemetry-data-to-farmbeats"></a>Wyślij dane telemetryczne do FarmBeats

Aby wysłać dane telemetryczne do FarmBeats, konieczne będzie utworzenie klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat danych telemetrycznych, zobacz [wysyłanie telemetrii do centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Oto przykładowy kod w języku Python, który wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń:

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

Format komunikatu kanonicznego jest następujący:

```json
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

Wszystkie nazwy kluczy w formacie JSON danych telemetrycznych powinny być małymi literami, na przykład DeviceID, sensordata itp.

Na przykład komunikat telemetrii:


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

## <a name="troubleshooterror-management"></a>Rozwiązywanie problemów/zarządzanie błędami

**Rozwiązywanie problemów — opcja/pomoc techniczna**

W przypadku, gdy klient nie może odebrać danych urządzenia i/lub telemetrii w określonym wystąpieniu FarmBeats, partner urządzenia powinien zapewnić pomoc techniczną i mechanizm rozwiązywania problemów.

**Przechowywanie danych telemetrii**

Dane telemetryczne powinny być również przechowywane dla wstępnie zdefiniowanego okresu, dzięki czemu takie samo może być przydatne w debugowaniu lub ponownej próbie wysłania telemetrii w przypadku błędu lub utraty danych.

**Zarządzanie błędami/powiadamianie o błędach**

W przypadku błędu, który ma wpływ na metadane urządzenia/czujnika/integracji danych lub dane telemetryczne w systemie partnera urządzeń, ten sam Klient powinien otrzymywać powiadomienia. Mechanizm rozwiązywania błędów powinien również zostać zaprojektowany i wdrożony.

**Lista kontrolna połączenia**

Producenci urządzeń/partnerzy mogą mieć następujące Sanity testu/listy kontrolnej, aby upewnić się, że poświadczenia podane przez klienta są dokładne.

   - Sprawdź, czy token dostępu został odebrany z podanymi poświadczeniami
   - Sprawdź, czy wywołanie interfejsu API powiedzie się, gdy otrzymano token dostępu
   - Sprawdź, czy nawiązano połączenie z klientem EventHub

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu API REST, zobacz [interfejs API REST](references-for-farmbeats.md#rest-api).
