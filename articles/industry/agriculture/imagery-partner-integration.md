---
title: Integracja z partnerami obsługującymi obrazy
description: Zawiera opis integracji partnera obrazów
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 232ade55350ad1ac85612cf8c69117c19cb20d0c
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927793"
---
# <a name="imagery-partner-integration"></a>Integracja z partnerami obsługującymi obrazy

W tym artykule opisano, jak za pomocą składnika usługi Azure FarmBeats translator wysyłać dane obrazów do FarmBeats. Dane dotyczące obrazów w rolnictwie mogą pochodzić z różnych źródeł, w tym kamer wielowidmowych, satelitów i dronom. Partnerzy w społecznościach społecznościowych mogą zintegrować się z usługą FarmBeats, aby zapewnić klientom niestandardowe mapy dla swoich Farm.

Dane, które są dostępne, mogą być wizualizowane za pomocą akceleratora FarmBeats i mogą być używane na potrzeby fuzji danych i (Machine Learning/sztuczna inteligencja), które są tworzone przez firmy rolnicze lub Integratory systemu klienta.

FarmBeats zapewnia możliwość:

- Definiowanie niestandardowych typów obrazów, źródła i formatu plików przy użyciu rozszerzonych interfejsów API typu
- Pozyskiwanie danych obrazów z różnych źródeł za pośrednictwem sceny & SceneFile interfejsy API.

Poniższe informacje koncentrują się na pobieraniu obrazów do systemu FarmBeats.

Po wybraniu sekcji drona obrazy zostanie otwarte okno podręczne pokazujące obraz o wysokiej rozdzielczości drona orthomosaic. Możesz uzyskać dostęp do oprogramowania partnerskiego, które ułatwia planowanie lotów drona oraz uzyskiwanie danych pierwotnych. Będziesz nadal używać oprogramowania partnera do planowania ścieżki i orthomosaic obrazu.

Partnerzy drona muszą umożliwić klientom łączenie ich kont klientów z ich wystąpieniem FarmBeats na platformie Azure.

Aby połączyć FarmBeats, należy użyć następujących poświadczeń w oprogramowaniu partnerskim drona:

- Punkt końcowy interfejsu API
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta

## <a name="api-development"></a>Programowanie interfejsu API

Interfejsy API zawierają dokumentację techniczną struktury Swagger. Przejrzyj strukturę [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) , aby uzyskać informacje o interfejsach API i odpowiadających im żądania/odpowiedzi.

## <a name="authentication"></a>Authentication

FarmBeats wykorzystuje [Active Directory]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization))Microsoft Azure. Azure App Service uwierzytelniania zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji. 

Aby uzyskać więcej informacji na temat, zobacz [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats centrum danych używa uwierzytelniania okaziciela, który wymaga następujących poświadczeń:

- Identyfikator klienta
- Klucz tajny klienta
- Identyfikator dzierżawy

Korzystając z powyższych poświadczeń, obiekt wywołujący może zażądać tokenu dostępu, który musi zostać wysłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka w następujący sposób:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Poniżej znajduje się przykładowy kod języka Python, który pobiera token dostępu. Następnie można użyć tokenu dla kolejnych wywołań interfejsu API do FarmBeat:   
Importuj platformę Azure 

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>Nagłówki żądań HTTP

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do centrum danych FarmBeats:

**Nagłówek** | **Opis i przykład**
--- | ---
Content-Type  | Format żądania (Content-Type: Application/<format>) dla FarmBeats interfejsu API usługi Data Hub to JSON. Content-Type: Application/JSON
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja: < tokenu dostępu >
odebrać  | Format odpowiedzi. W przypadku interfejsów API usługi FarmBeats Data Hub formatem jest akceptowanie JSON: Application/JSON


## <a name="api-requests"></a>Żądania interfejsu API

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET/POST/PUT), adres URL usługi interfejsu API, identyfikator URI zasobu (do zapytania, przesłać dane, zaktualizować lub usunąć) oraz co najmniej jeden nagłówek żądania HTTP.

Opcjonalnie można uwzględnić parametry zapytania dla wywołań GET do filtrowania, ograniczyć rozmiar i sortować dane w odpowiedziach.

Poniżej przykładowego żądania jest uzyskanie listy urządzeń:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniżej przykładowego żądania jest utworzenie urządzenia (zawiera on wejściowy kod JSON z treścią żądania).


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON (JavaScript Object Notation) to typowy, niezależny od języka format danych, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz temat [org JSON](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Pozyskiwanie obrazów w FarmBeats

Gdy partner ma poświadczenia do łączenia się z centrum danych FarmBeats, partner wykonuje następujące czynności w składniku Translator:

1.  Utwórz nowy typ rozszerzony dla następujących pól, zgodnie z typem obrazów, które zostaną przekazane:

    - Źródło sceny: na przykład < drone_partner_name >
    - Typ sceny: na przykład <drone>
    - Typ pliku sceny: na przykład <chlorophyll index>
    - Typ zawartości pliku sceny: na przykład < obraz/TIFF >

2.  Wywołaj interfejs API Farm, aby uzyskać listę Farm z systemu Azure FarmBeats.
3.  Zapewnij klientowi możliwość wyboru pojedynczej farmy z listy Farm.

    W systemie partnerskim musi zostać wyświetlona Farma w ramach oprogramowania partnerskiego, która umożliwia planowanie ścieżki i drona oraz zbieranie obrazów.

4.  Wywołaj interfejs API sceny i podaj wymagane szczegóły, aby utworzyć nową scenę z unikatowym SceneID.
5.  Odbierz adres URL sygnatury dostępu współdzielonego obiektu BLOB w celu przekazania wymaganych obrazów do centrum danych FarmBeats w kontekście wybranej farmy w systemie FarmBeats.

Oto szczegółowy przepływ wywołań interfejsu API:

### <a name="step-1-extendedtype"></a>Krok 1: Rozszerzonytype

Zaewidencjonuj interfejs API rozszerzonych, jeśli typ i źródło plików są dostępne w FarmBeats. Można to zrobić przez wywołanie metody GET w interfejsie API/ExtendedType.

Poniżej znajdują się wartości zdefiniowane przez system:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Jest to jednorazowa konfiguracja, a zakres tego nowego typu sceny jest ograniczony do subskrypcji, w której wdrożono projekt FarmBeats.

Przykład: aby dodać SceneSource: "SlantRange", należy umieścić identyfikator/ExtendedType z kluczem: "SceneSource" ładunku wejściowego:

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Zielonym polem jest nowe dodanie do wartości źródłowych sceny zdefiniowanej przez system.

### <a name="step-2-get-farmdetails"></a>Krok 2. Pobieranie FarmDetails

Sceny (pliki TIFF lub CSV) będą znajdować się w kontekście farmy. Aby uzyskać szczegółowe informacje dotyczące farmy, należy wykonać instrukcje Get on/Farm API. Interfejs API zwróci listę Farm dostępnych w FarmBeats i można wybrać farmę, dla której mają zostać pozyskiwane dane.

Pobierz odpowiedź/Farm:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-ascene-id-post-call"></a>Krok 3. Tworzenie identyfikatora/sceny (wywołanie post)

Utwórz nową scenę (plik TIFF lub CSV) przy użyciu podanych informacji, podając datę, sekwencję i identyfikator farmy, z którą zostanie skojarzona scena. Metadane skojarzone z sceną można zdefiniować w obszarze właściwości, w tym czas trwania i typ miary.

Spowoduje to utworzenie nowego SceneID, który zostanie skojarzony z farmą. Po utworzeniu SceneID użytkownik może użyć tego samego do utworzenia nowego pliku (TIFF lub. csv) & zapisania zawartości pliku.

Przykładowy ładunek wejściowy dla interfejsu API wywołania/sceny

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Odpowiedź interfejsu API:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Utwórz/SceneFile**

Identyfikator sceny zwracany w kroku 3 to dane wejściowe dla SceneFile. SceneFile zwraca token adresu URL sygnatury dostępu współdzielonego, który jest ważny przez 24 godziny.

Jeśli użytkownik wymaga programistycznego sposobu przekazywania strumienia obrazów, można użyć zestawu SDK usługi BLOB Storage do zdefiniowania metody przy użyciu identyfikatora Scenefile, lokalizacji & adresu URL.

Przykładowy ładunek wejściowy dla wywołania post w interfejsie API/Scenefile:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Odpowiedź interfejsu API:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

Wywołanie interfejsu API/SceneFile zwraca adres URL przekazywania sygnatury dostępu współdzielonego, który może służyć do przekazywania pliku CSV lub TIFF przy użyciu usługi Azure Blob Storage Client/Library.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o szczegółach integracji opartych na interfejsie API REST, zobacz [interfejs API REST](references-for-farmbeats.md#rest-api).
