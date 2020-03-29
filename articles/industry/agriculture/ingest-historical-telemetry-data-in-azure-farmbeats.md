---
title: Pozyskiwanie historycznych danych telemetrycznych
description: W tym artykule opisano sposób pozyskiwania historycznych danych telemetrycznych.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b0b9d62e8761cfb67d0642d8e5a97e7d1f05af12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064449"
---
# <a name="ingest-historical-telemetry-data"></a>Pozyskiwanie historycznych danych telemetrycznych

W tym artykule opisano sposób pozyskiwania danych z czujników historycznych do usługi Azure FarmBeats.

Połknienie danych historycznych z zasobów Internetu rzeczy (IoT), takich jak urządzenia i czujniki, jest typowym scenariuszem w FarmBeats. Tworzenie metadanych dla urządzeń i czujników, a następnie pozyskiwania danych historycznych do FarmBeats w formacie kanonicznym.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zanim przejdziesz do tego artykułu, upewnij się, że zainstalowano FarmBeats i zebrałeś dane historyczne z urządzeń IoT. Należy również włączyć dostęp partnera, jak wspomniano w poniższych krokach.

## <a name="enable-partner-access"></a>Włączanie dostępu partnera

Należy włączyć integrację partnera do wystąpienia Usługi Azure FarmBeats. Ten krok tworzy klienta, który ma dostęp do wystąpienia Usługi Azure FarmBeats jako partnera urządzenia i zapewnia następujące wartości, które są wymagane w kolejnych krokach:

- Punkt końcowy interfejsu API: jest to adres URL usługi\<Datahub, na przykład https:// datahub>.azurewebsites.net
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta
- Parametry połączenia EventHub

Wykonaj następujące kroki:

> [!NOTE]
> Musisz być administratorem, aby wykonać następujące kroki.

1. Pobierz [plik zip](https://aka.ms/farmbeatspartnerscriptv2)i wyodrębnij go na dysk lokalny. Wewnątrz pliku zip znajdzie się jeden plik.

2. Zaloguj się https://portal.azure.com/ i przejdź do**rejestracji aplikacji**usługi Azure **Active Directory** > .

3. Wybierz **rejestrację aplikacji,** która została utworzona w ramach wdrożenia FarmBeats. Będzie miał taką samą nazwę jak FarmBeats Datahub.

4. Wybierz **opcję Uwidaczniaj interfejs API** > wybierz pozycję Dodaj aplikację **kliencką** i wprowadź **04b07795-8ddb-461a-bbee-02f9e1bf7b46** i zaznacz opcję **Autoryzuj zakres**. Spowoduje to udzielenie dostępu do interfejsu wiersza polecenia platformy Azure (Cloud Shell) w celu wykonania następujących kroków:

5. Otwórz usługę Cloud Shell. Ta opcja jest dostępna na pasku narzędzi w prawym górnym rogu witryny Azure portal.

    ![Pasek narzędzi portalu Azure](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Upewnij się, że środowisko jest ustawione na **program PowerShell**. Domyślnie jest ustawiona na Bash.

    ![Ustawienie paska narzędzi programu PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Przekaż plik z kroku 1 w wystąpieniu usługi Cloud Shell.

    ![Przycisk Przekaż pasek narzędzi](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Przejdź do katalogu, w którym plik został przekazany. Domyślnie pliki są przekazywane do katalogu macierzystego pod nazwą użytkownika.

9. Uruchom następujący skrypt. Skrypt prosi o identyfikator dzierżawy, który można uzyskać ze**strony Przegląd**usługi Azure **Active Directory** > .

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby przechwycić wartości **dla punktu końcowego interfejsu API,** **identyfikatora dzierżawy,** **identyfikatora klienta,** **klucza tajnego klienta**i ciągu połączenia **EventHub.**

## <a name="create-device-or-sensor-metadata"></a>Tworzenie metadanych urządzenia lub czujnika

 Teraz, gdy masz wymagane poświadczenia, można zdefiniować urządzenie i czujniki. Aby to zrobić, należy utworzyć metadane, wywołując Interfejsy API FarmBeats. Upewnij się, że wywołać interfejsy API jako aplikację kliencką, która została utworzona w powyższej sekcji.

 FarmBeats Datahub ma następujące interfejsy API, które umożliwiają tworzenie i zarządzanie metadanymi urządzenia lub czujnika.

 > [!NOTE]
 > Jako partner masz dostęp tylko do odczytu, tworzenia i aktualizowania metadanych; **opcja delete jest ograniczona do partnera.**

- /**DeviceModel**: DeviceModel odpowiada metadanym urządzenia, takie jak producent i typ urządzenia, które jest bramą lub węzłem.
- /**Urządzenie**: Urządzenie odpowiada urządzeniu fizycznemu obecnemu w gospodarstwie.
- /**SensorModel**: SensorModel odpowiada metadane czujnika, takie jak producent, typ czujnika, który jest analogowy lub cyfrowy, oraz pomiar czujnika, takich jak temperatura otoczenia i ciśnienie.
- /**Czujnik**: Czujnik odpowiada czujnikowi fizycznemu, który rejestruje wartości. Czujnik jest zazwyczaj podłączony do urządzenia z identyfikatorem urządzenia.  


|        DeviceModel   |  Sugestie   |
| ------- | -------             |
|     Typ (węzeł, brama)        |          Typ urządzenia — węzeł lub brama      |
|          Producent            |         Nazwa producenta    |
|  Kod_produktu                    |  Kod produktu urządzenia lub nazwa lub numer modelu. Na przykład EnviroMonitor #6800.  |
|            Porty          |     Nazwa i typ portu, który jest cyfrowy lub analogowy.
|     Nazwa                 |  Nazwa identyfikującego zasób. Na przykład nazwa modelu lub nazwa produktu.
      Opis     | Podaj opis modelu.
|    Właściwości          |    Dodatkowe właściwości producenta.   |
|    **Urządzenie**             |                      |
|   IdentyfikatorModelId urządzenia     |     Identyfikator skojarzonego modelu urządzenia.  |
|  Identyfikator sprzętowy          | Unikatowy identyfikator urządzenia, taki jak adres MAC.
|  RaportowanieInterwalne        |   Interwał raportowania w sekundach.
|  Lokalizacja            |  Szerokość urządzenia (-90 do +90), długość geograficzna (-180 do 180) i wysokość (w metrach).   
|Jednostka nadrzędna       |    Identyfikator urządzenia nadrzędnego, do którego jest podłączone to urządzenie. Na przykład węzeł, który jest połączony z bramą. Węzeł ma parentDeviceId jako bramę.  |
|    Nazwa            | Nazwa identyfikująca zasób. Partnerzy urządzeń muszą wysłać nazwę, która jest zgodna z nazwą urządzenia po stronie partnera. Jeśli nazwa urządzenia partnerskiego jest zdefiniowana przez użytkownika, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do Gruperów Rolnych.|
|     Opis       |      Podaj opis. |
|     Właściwości    |  Dodatkowe właściwości producenta.
|     **CzujnikModel**        |          |
|       Typ (analogowy, cyfrowy)          |      Typ czujnika, niezależnie od tego, czy jest analogowy, czy cyfrowy.       |
|          Producent            |       Producent czujnika.     |
|     Kod_produktu| Kod produktu lub nazwa lub numer modelu. Na przykład RS-CO2-N01. |
|       SensorZezuje nazwę >       | Nazwa miary czujnika. Obsługiwane są tylko małe litery. W przypadku pomiarów z różnych głębokości należy określić głębokość. Na przykład soil_moisture_15cm. Ta nazwa musi być zgodna z danymi telemetrycznymi.  |
|          SensorZezamia > DataType       |Typ danych telemetrycznych. Obecnie obsługiwane jest double.|
|    SensorZezje typ >    |Typ pomiaru danych telemetrycznych czujnika. Typy zdefiniowane przez system to AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, GlebaTemperytura, SolarRadiation, Stan, TimeDuration, UVRadiation, UVIndex, Objętość, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Aby dodać więcej, zapoznaj się z interfejsem API /ExtendedType.|
|        SensorZezje > jednostkę              | Jednostka danych telemetrycznych czujnika. Zdefiniowane przez system jednostki to NoUnit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSeconiMeter, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour Aby dodać więcej, zapoznaj się z /ExtendedType API.|
|    SensorMeasures > AggregationType    |  Wartości mogą być żadne, średnie, maksymalne, minimalne lub Standardoweodwianie.  |
|          Nazwa            | Nazwa identyfikującego zasób. Na przykład nazwa modelu lub nazwa produktu.  |
|    Opis        | Podaj opis modelu.|
|   Właściwości       |  Dodatkowe właściwości producenta.|
|    **Czujnik**      |          |
| Identyfikator sprzętowy          |   Unikatowy identyfikator czujnika ustawionego przez producenta.|
|  SensorModelId (CzujnikModelId)     |    Identyfikator powiązanego modelu czujnika.|
| Lokalizacja          |  Szerokość czujnika (-90 do +90), długość geograficzna (-180 do 180) i wysokość (w metrach).|
|   Nazwa > portu        |  Nazwa i typ portu podłączonego do czujnika w urządzeniu. Musi to być taka sama nazwa, jak zdefiniowano w modelu urządzenia.|
|    Deviceid  |    Identyfikator urządzenia, do które podłączony jest czujnik. |
| Nazwa            |   Nazwa do identyfikowania zasobu. Na przykład nazwa czujnika lub nazwa produktu i numer modelu lub kod produktu.|
|    Opis      | Podaj opis.|
|    Właściwości        |Dodatkowe właściwości producenta.|

Aby uzyskać więcej informacji o obiektach, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Żądanie interfejsu API do utworzenia metadanych

Aby utworzyć żądanie interfejsu API, należy połączyć metodę HTTP (POST), adres URL z usługą interfejsu API i identyfikator URI z zasobem, aby wykonać kwerendę, przesłać dane do żądania, utworzyć lub usunąć je. Następnie należy dodać jeden lub więcej nagłówków żądań HTTP. Adres URL usługi interfejsu API jest punktem końcowym interfejsu API, czyli\<adresem URL datahub (https:// yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Uwierzytelnianie

Usługa FarmBeats Datahub używa uwierzytelniania na okaziciela, które wymaga następujących poświadczeń, które zostały wygenerowane w poprzedniej sekcji:

- Identyfikator klienta
- Klucz tajny klienta
- Identyfikator dzierżawy

Przy użyciu tych poświadczeń, obiektu wywołującego można zażądać tokenu dostępu. Token musi być wysyłany w kolejnych żądaniach interfejsu API w sekcji nagłówka w następujący sposób:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Poniższy przykładowy kod języka Python daje token dostępu, który może służyć do kolejnych wywołań interfejsu API do FarmBeats: 

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

Oto najczęstsze nagłówki żądań, które muszą być określone podczas wywoływania interfejsu API do Pola Danych FarmBeats:

- **Typ zawartości:** aplikacja/json
- **Autoryzacja**:> tokenu dostępu na okaziciela <
- **Zaakceptuj**: wniosek/json

### <a name="input-payload-to-create-metadata"></a>Ładunek wejściowy do tworzenia metadanych

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Urządzenie

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

CzujnikModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Czujnik

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Następujące przykładowe żądanie tworzy urządzenie. To żądanie ma wejście JSON jako ładunek z treścią żądania.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Poniżej znajduje się przykładowy kod w języku Python. Token dostępu używany w tym przykładzie jest taki sam, który jest odbierany podczas uwierzytelniania.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Aby wysłać odpowiednie komunikaty telemetryczne, należy zachować identyfikatory.

### <a name="send-telemetry"></a>Wysyłanie danych telemetrycznych

Po utworzeniu urządzeń i czujników w FarmBeats można wysłać skojarzone komunikaty telemetryczne.

### <a name="create-a-telemetry-client"></a>Tworzenie klienta telemetrii

Należy wysłać dane telemetryczne do usługi Azure Event Hubs do przetwarzania. Usługa Azure Event Hubs to usługa umożliwiająca pozyskiwania danych w czasie rzeczywistym (telemetrii) z podłączonych urządzeń i aplikacji. Aby wysłać dane telemetryczne do FarmBeats, należy utworzyć klienta, który wysyła wiadomości do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat wysyłania danych telemetrycznych, zobacz [Usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Wysyłanie wiadomości telemetrii jako klienta

Po nawiązaniu połączenia jako klienta usługi Event Hubs można wysyłać wiadomości do centrum zdarzeń jako JSON.

Oto przykładowy kod języka Python, który wysyła dane telemetryczne jako klienta do określonego centrum zdarzeń:

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

Konwertuj format danych z czujników historycznych na format kanoniczny, który rozumie w usłudze Azure FarmBeats. Format wiadomości kanonicznej jest następujący:

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
        }
      ]
    }
 ]
}
```

Po dodaniu odpowiednich urządzeń i czujników, uzyskać identyfikator urządzenia i identyfikator czujnika w komunikacie telemetrii, jak opisano w poprzedniej sekcji.

Oto przykład komunikatu telemetryczne:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nie można wyświetlić danych telemetrycznych po spożyciu historycznych/strumieniowych danych z czujników

**Symptom:** Urządzenia lub czujniki są wdrażane i utworzono urządzenia/czujniki na FarmBeats i pochłonął dane telemetryczne do EventHub, ale nie można uzyskać lub wyświetlić dane telemetryczne na FarmBeats.

**Działania naprawcze:**

1. Upewnij się, że zrobiłeś odpowiednią rejestrację partnera - możesz to sprawdzić, przechodząc do datahub swagger, przejdź do /Partner API, Zrób get i sprawdź, czy partner jest zarejestrowany. Jeśli nie, wykonaj [kroki tutaj,](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) aby dodać partnera.

2. Upewnij się, że zostały utworzone metadane (DeviceModel, Device, SensorModel, Sensor) przy użyciu poświadczeń klienta partnera.

3. Upewnij się, że użyto poprawnego formatu komunikatu telemetrii (jak określono poniżej):

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
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat szczegółów integracji opartej na interfejsie API [REST,](rest-api-in-azure-farmbeats.md)zobacz INTERFEJS API REST .
