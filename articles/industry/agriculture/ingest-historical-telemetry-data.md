---
title: Pozyskiwanie historycznych danych telemetrycznych
description: Opisuje sposób pobierania danych historyczne dane telemetryczne pozyskiwania
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e6bd9b5c09e1af5ec587e1f0e52ab25d21d2293b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889609"
---
# <a name="ingest-historical-telemetry-data"></a>Pozyskiwanie historycznych danych telemetrycznych

W tym artykule opisano sposób pozyskiwania historycznych danych czujników do usługi Azure FarmBeats.

Pozyskiwanie danych historycznych z Internet rzeczy (IoT) dla zasobów, takich jak urządzenia i czujniki, jest typowym scenariuszem w FarmBeats. Tworzysz metadane dla urządzeń i czujników, a następnie pozyskasz dane historyczne do FarmBeats w postaci kanonicznej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed kontynuowaniem pracy z tym artykułem upewnij się, że zainstalowano FarmBeats i zebrano dane historyczne z usługi IoT.

## <a name="enable-partner-access"></a>Włącz dostęp partnera

Musisz włączyć integrację partnera z wystąpieniem usługi Azure FarmBeats. W tym kroku jest tworzony klient, który będzie miał dostęp do usługi Azure FarmBeats jako partner urządzenia i zawiera następujące wartości, które są wymagane w kolejnych krokach.

- Punkt końcowy interfejsu API — jest to adres URL centrum danych, na przykład https://<datahub>. azurewebsites.net
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta
- Parametry połączenia EventHub

Wykonaj poniższe kroki, aby je wygenerować:

>[!NOTE]
> Musisz być administratorem, aby wykonać następujące czynności.

1. Pobierz ten [skrypt](https://aka.ms/farmbeatspartnerscript) i wyodrębnij go na dysku lokalnym. W pliku ZIP znajdują się dwa pliki.
2. Zaloguj się do [Azure Portal](https://portal.azure.com/) i Otwórz Cloud Shell (Ta opcja jest dostępna w prawym górnym pasku portalu)  

    ![Farmy projektów](./media/for-tutorials/navigation-bar-1.png)

3. Upewnij się, że środowisko jest ustawione na **PowerShell**.

    ![Farmy projektów](./media/for-tutorials/power-shell-new-1.png)

4. Przekaż dwa pobrane pliki (z kroku 1 powyżej) do Cloud Shell.  

    ![Farmy projektów](./media/for-tutorials/power-shell-two-1.png)

5. Przejdź do katalogu, do którego pliki zostały przekazane

   >[!NOTE]
   > Domyślnie plik jest przekazywany do katalogu macierzystego/home/username/.
6. Uruchom skrypt za pomocą polecenia:  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby ukończyć procedurę.

## <a name="create-devicesensor-metadata"></a>Tworzenie metadanych urządzenia/czujnika

 Teraz, gdy masz wymagane poświadczenia, możesz zdefiniować urządzenie i czujniki, tworząc metadane przy użyciu interfejsów API FarmBeats.

 FarmBeats Data Hub udostępnia następujące interfejsy API, które umożliwiają tworzenie metadanych urządzenia/czujnika i zarządzanie nimi.   

- /**DeviceModel** — model urządzenia odpowiada metadanych urządzenia, takich jak producent, typ urządzenia albo brama lub węzeł.  
- urządzenie **/urządzenia** odpowiada urządzeniu fizycznemu znajdującemu się w farmie.  
- /**model** czujnika odpowiada metadanych czujnika, takiego jak producent, typ czujnika albo analogowy lub cyfrowy, pomiar czujnika, taki jak temperatura otoczenia lub ciśnienie.
- /czujnik **czujnika** odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.  


|        Tryb urządzenia   |  Sugestie   |
| ------- | -------             |
|     Typ (węzeł, brama)        |          1 gwiazdka      |
|          Producent            |         2 gwiazdki     |
|  ProductCode                    |  Kod produktu urządzenia lub nazwa/numer modelu. Na przykład EnviroMonitor # 6800.  |
|            Porty          |     Nazwa i typ portu (cyfrowe/analogowe)
|     Nazwa                 |  Nazwa identyfikująca zasób. Na przykład nazwa modelu/Product Name.
      Opis     | Podaj znaczący opis modelu
|    Właściwości          |    Dodatkowe właściwości producenta   |
|    **Pliku**             |                      |
|   DeviceModelId     |     Identyfikator skojarzonego modelu urządzenia  |
|  HardwareId          | Unikatowy identyfikator urządzenia, na przykład adres MAC itp.,
|  ReportingInterval        |   Interwał raportowania w sekundach
|  Lokalizacja            |  Urządzenia Latitude (-90 do + 90)/Longitude (-180 do 180)/Elevation (w metrach)   
|ParentDeviceId       |    Identyfikator urządzenia nadrzędnego, z którym jest połączone to urządzenie. Na przykład węzeł połączony z bramą. Węzeł będzie miał parentDeviceId jako bramę.  |
|    Nazwa            | Nazwa identyfikująca zasób. Partnerzy urządzeń muszą wysłać nazwę zgodną z nazwą urządzenia po stronie partnera. Jeśli nazwa urządzenia partnerskiego jest zdefiniowana przez użytkownika, ta sama nazwa zdefiniowana przez użytkownika powinna być propagowana do FarmBeats.|
|     Opis       |      Podaj znaczący opis  |
|     Właściwości    |  Dodatkowe właściwości producenta
|     **Model czujnika**        |          |
|       Typ (analogowy, cyfrowy)          |      Typ czujnika, czy analogowy lub cyfrowy       |
|          Producent            |       Producent czujnika     |
|     ProductCode| Kod produktu lub nazwa modelu/numer. Na przykład RS-CO2-N01. |
|       Nazwa > SensorMeasures       | Nazwa miary czujnika. Obsługiwane jest tylko małe litery. W przypadku pomiaru z różnych głębokości należy określić głębokość. Na przykład soil_moisture_15cm. Ta nazwa musi być spójna z danymi telemetrycznymi  |
|          SensorMeasures > DataType       |Typ danych telemetrii. Obecnie jest obsługiwana Podwójna precyzja|
|    Typ > SensorMeasures    |Typ pomiaru danych telemetrii czujnika. Poniżej przedstawiono typy zdefiniowane przez system: AmbientTemperature, CO2, Głębokość, ElectricalConductivity, LeafWetness, długość, LiquidLevel, azotan, O2, PH, fosforan, PointInTime, potas, ciśnienie, RainGauge, RelativeHumidity, zasolenie, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Aby dowiedzieć się więcej, zapoznaj się z interfejsem API/ExtendedType.|
|        Jednostka > SensorMeasures              | Jednostka danych telemetrii czujnika. Poniżej przedstawiono jednostki zdefiniowane przez system: nounit, Celsjusza, Fahrenheita, Kelvin, Rankine, Pascal, rtęć, PSI, milimetr, centymetr, metr, cal, stopy, mila, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, stopień, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, wartość procentowa, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litr, MilliLiter, seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour, aby dodać więcej, zapoznaj się z/ Rozszerzony interfejs API.|
|    SensorMeasures > agregacji    |  Wartości mogą być brak, średnia, maksimum, minimum lub StandardDeviation  |
|          Nazwa            | Nazwa identyfikująca zasób. Na przykład nazwa modelu/Product Name.  |
|    Opis        | Podaj znaczący opis modelu  |
|   Właściwości       |  Dodatkowe właściwości producenta  |
|    **Czujnik**      |          |
| HardwareId          |   Unikatowy identyfikator dla czujnika ustawiony przez producenta |
|  SensorModelId     |    Identyfikator skojarzonego modelu czujnika   |
| location          |  Czujnik Latitude (-90 do + 90)/Longitude (-180 do 180)/Elevation (w metrach)|
|   Nazwa > portu        |  Nazwa i typ portu, z którym jest połączony czujnik na urządzeniu. Ta nazwa musi być taka sama, jak zdefiniowana w modelu urządzenia. |
|    Identyfikator  |    Identyfikator urządzenia, z którym jest połączony czujnik     |
| Nazwa            |   Nazwa identyfikująca zasób. Na przykład nazwa czujnika/Nazwa produktu i numer modelu/Kod produktu.|
|    Opis      | Podaj znaczący opis |
|    Właściwości        |Dodatkowe właściwości producenta |

Aby uzyskać więcej informacji na temat obiektów, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Żądanie interfejsu API do tworzenia metadanych**

Aby wykonać żądanie interfejsu API, należy połączyć metodę HTTP (POST), adres URL usługi interfejsu API, identyfikator URI zasobu do wysłania zapytania, przesłać dane w celu utworzenia lub usunięcia żądania i dodania co najmniej jednego nagłówka żądania HTTP. Adres URL usługi interfejsu API to punkt końcowy interfejsu API, czyli adres URL centrum danych (https://<yourdatahub>. azurewebsites.net)  

**Uwierzytelnianie**:

FarmBeats centrum danych używa uwierzytelniania okaziciela, który wymaga następujących poświadczeń, które zostały wygenerowane w powyższej sekcji.

- Identyfikator klienta
- Klucz tajny klienta
- Identyfikator dzierżawy  

Korzystając z powyższych poświadczeń, obiekt wywołujący może zażądać tokenu dostępu, który musi zostać wysłany w kolejnych żądaniach interfejsu API w sekcji nagłówka w następujący sposób:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**Nagłówki żądań HTTP**:

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do centrum danych FarmBeats:

- Content-Type: Application/JSON
- Autoryzacja: < tokenu dostępu >
- Akceptuj: Application/JSON

**Ładunek wejściowy do tworzenia metadanych**:

**DeviceModel**


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

SensorModel

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
Poniżej przykładowego żądania jest utworzenie urządzenia (ma on wejściowy kod JSON jako ładunek z treścią żądania).  

```azurepowershell-interactive
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "
{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\",}"*
```

> [!NOTE]
> Interfejsy API zwracają unikatowe identyfikatory dla każdego utworzonego wystąpienia. Należy zachować identyfikatory wysyłania odpowiednich komunikatów telemetrycznych.

**Wyślij dane telemetryczne**

Teraz, po utworzeniu urządzeń i czujników w FarmBeats, można wysłać skojarzone komunikaty telemetryczne.  

**Utwórz klienta telemetrii**

Musisz wysłać dane telemetryczne do centrum zdarzeń platformy Azure w celu przetworzenia. Azure EventHub to usługa, która umożliwia pozyskiwanie danych w czasie rzeczywistym z połączonych urządzeń i aplikacji. Aby wysłać dane telemetryczne do FarmBeats, należy utworzyć klienta wysyłającego komunikaty do centrum zdarzeń w FarmBeats. Aby uzyskać więcej informacji na temat wysyłania telemetrii, zobacz [Centra zdarzeń platformy Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Wyślij komunikat telemetrii jako klienta**

Po ustanowieniu połączenia jako klient usługi EventHub można wysyłać komunikaty do centrum zdarzeń jako dane JSON.  
Przekonwertuj historyczny format danych z czujnika na format kanoniczny, który jest rozpoznawany przez platformę Azure FarmBeats. Format komunikatu kanonicznego jest następujący:  



 ```
  {   
      “deviceid”: “<id of the Device created>”,   
      "timestamp": "<timestamp in ISO 8601 format>",     
      "version" : "1",   
      "sensors":
      [     
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


Po dodaniu odpowiednich urządzeń i czujników Uzyskaj identyfikator DeviceID i sensorid w komunikacie telemetrii, zgodnie z opisem w poprzedniej sekcji.

Przykładowy komunikat telemetrii:


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


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o szczegółach integracji opartych na interfejsie API REST, zobacz [interfejs API REST](references-for-farmbeats.md#rest-api).
