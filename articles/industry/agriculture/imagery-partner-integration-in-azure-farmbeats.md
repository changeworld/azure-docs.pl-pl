---
title: Integracja z partnerami obsługującymi obrazy
description: W tym artykule opisano integrację partnerów obrazów.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131865"
---
# <a name="imagery-partner-integration"></a>Integracja z partnerami obsługującymi obrazy

W tym artykule opisano sposób używania składnika Azure FarmBeats Translator do wysyłania danych obrazów do FarmBeats. Dane dotyczące zdjęć rolniczych mogą być generowane z różnych źródeł, takich jak kamery wielospektralne, satelity i drony. Partnerzy w dziedzinie zdjęć rolniczych mogą integrować się z FarmBeats, aby zapewnić klientom niestandardowe mapy dla swoich gospodarstw.

Dane, po ich udostępnieniu, mogą być wizualizowane za pośrednictwem akceleratora FarmBeats i potencjalnie wykorzystywane do tworzenia modelu fuzji danych i uczenia maszynowego/sztucznej inteligencji (ML/AI) przez firmy rolnicze lub integratorów systemów klientów.

FarmBeats zapewnia możliwość:

- Definiowanie niestandardowych typów obrazów, źródła i formatu pliku przy użyciu interfejsów API /ExtendedType.
- Pozyskiwanie danych z różnych źródeł za pośrednictwem interfejsów API /Scene i /SceneFile.

Poniższe informacje skupiają się na uzyskaniu dowolnej formy zdjęć do systemu FarmBeats.

Po wybraniu sekcji **Obrazy dronów** otwiera się okno podręczne, aby wyświetlić obraz ortomozaii w wysokiej rozdzielczości. Możesz uzyskać dostęp do oprogramowania partnerskiego, które pomaga zaplanować loty dronami i uzyskać surowe dane. Będziesz nadal korzystać z oprogramowania partnera do planowania ścieżek i szycia obrazów ortozajskich.

Partnerzy dronów muszą umożliwić klientom łączenie konta klienta z ich wystąpieniem FarmBeats na platformie Azure.

Aby połączyć farmbeats, należy użyć następujących poświadczeń w oprogramowaniu partnera drona:

- Punkt końcowy interfejsu API
- Identyfikator dzierżawy
- Identyfikator klienta
- Klucz tajny klienta

## <a name="api-development"></a>Tworzenie API

Interfejsy API zawierają dokumentację techniczną Swagger. Aby uzyskać informacje na temat interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Uwierzytelnianie

FarmBeats używa usługi Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD). Usługa Azure App Service zapewnia wbudowaną obsługę uwierzytelniania i autoryzacji. 

Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [Usługa Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

Usługa FarmBeats Datahub używa uwierzytelniania na okaziciela, które wymaga następujących poświadczeń:

- Identyfikator klienta
- Klucz tajny klienta
- Identyfikator dzierżawy

Przy użyciu poprzednich poświadczeń, wywołujący można zażądać tokenu dostępu, który musi być wysyłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka, w następujący sposób:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Poniższy przykład kodu języka Python pobiera token dostępu. Następnie można użyć tokenu dla kolejnych wywołań interfejsu API do FarmBeats.

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

Oto najczęstsze nagłówki żądań, które należy określić podczas wywoływania interfejsu API do Pola Danych FarmBeats.

**Nagłówka** | **Opis i przykład**
--- | ---
Content-Type  | Format żądania (Typ zawartości:<format>aplikacja/ ). W przypadku interfejsów API usługi FarmBeats Datahub format jest JSON. Typ zawartości: aplikacja/json
Autoryzacja | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja:> tokenu dostępu na okaziciela <
Zaakceptuj  | Format odpowiedzi. W przypadku interfejsów API usługi FarmBeats Datahub format jest JSON. Zaakceptuj: wniosek/json


## <a name="api-requests"></a>Żądania interfejsu API

Aby wykonać żądanie interfejsu API REST, należy połączyć:

- Metoda HTTP (GET, POST i PUT).
- Adres URL usługi interfejsu API.
- Identyfikator URI zasobu (do wykonywania zapytań, przesyłania danych, aktualizowania lub usuwania).
- Co najmniej jeden nagłówek żądania HTTP.

Opcjonalnie można uwzględnić parametry kwerendy w wywołaniach GET, aby filtrować, ograniczać rozmiar i sortować dane w odpowiedziach.

Następujące przykładowe żądanie jest, aby uzyskać listę urządzeń:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniższe przykładowe żądanie polega na utworzeniu urządzenia. Ten przykład ma wejście JSON z treścią żądania.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Format danych

JSON jest wspólnym formatem danych niezależnym od języka, który zapewnia prostą reprezentację tekstową dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Połknąć zdjęcia do FarmBeats

Po partner ma poświadczenia, aby połączyć się z FarmBeats Datahub, partner wykonuje następujące kroki w translator składnika.

1.  Utwórz nowy typ rozszerzony dla następujących pól, zgodnie z typem obrazów, które mają zostać przesłane:

    - **Źródło sceny**: Na przykład drone_partner_name
    - **Typ sceny**: Na przykład dron
    - **Typ pliku sceny**: Na przykład indeks chlorofilu
    - **Typ zawartości pliku sceny:** Na przykład obraz/tiff

2.  Wywołanie interfejsu API /Farms, aby uzyskać listę farm z poziomu systemu Azure FarmBeats.
3.  Zapewnij klientowi możliwość wyboru jednej farmy z listy gospodarstw.

    System partnerski musi pokazać farmę w oprogramowaniu partnerskim, aby wykonać planowanie ścieżki i zbieranie danych o locie drona i obrazach.

4.  Wywołanie /Scene API i podać wymagane szczegóły, aby utworzyć nową scenę z unikatowym identyfikatorem sceny.
5.  Odbierz adres URL sygnatury dostępu Współdzielonego obiektu blob, aby przekazać wymagane obrazy do pola Danych FarmBeats w kontekście wybranej farmy w systemie FarmBeats.

Oto szczegółowy przepływ na wywołania interfejsu API.

### <a name="step-1-extendedtype"></a>Krok 1: ExtendedType

Zaewidencjonuj interfejs API /ExtendedType, aby sprawdzić, czy typ i źródło pliku są dostępne w polu FarmBeats. Aby to zrobić, wywołaj GET w interfejsie API /ExtendedType.

Oto wartości zdefiniowane przez system:

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

Ten krok jest konfiguracją jednorazową. Zakres tego nowego typu sceny jest ograniczony do subskrypcji, w której jest zainstalowany azure farmbeats.

Na przykład, aby dodać SceneSource: "SlantRange", należy wykonać put na identyfikator /ExtendedType API z kluczem "SceneSource" ładunku wejściowego.

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

Zielone pole jest nowym dodatkiem do zdefiniowanych przez system wartości źródłowych sceny.

### <a name="step-2-get-farm-details"></a>Krok 2: Uzyskaj szczegóły farmy

Sceny (.tiff lub .csv plików) są w kontekście farmy. Musisz uzyskać szczegóły farmy, wykonując GET w interfejsie API /Farm. Interfejs API zwraca listę gospodarstw, które są dostępne w FarmBeats. Można wybrać farmę, dla której chcesz pozyskiwania danych.

GET /Odpowiedź farmy:

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

### <a name="step-3-create-a-scene-id-post-call"></a>Krok 3: Tworzenie identyfikatora sceny (wywołanie POST)

Utwórz nową scenę (plik csv.tiff lub csv) z podanymi informacjami, która zawiera datę, sekwencję i identyfikator farmy, z którym jest skojarzona scena. Metadane skojarzone ze sceną można zdefiniować w obszarze właściwości, które obejmują czas trwania i typ miary.

Utworzenie nowej sceny tworzy nowy identyfikator sceny, który jest skojarzony z farmą. Po utworzeniu identyfikatora sceny użytkownik może użyć tego samego do utworzenia nowego pliku (.tiff lub csv) i przechowywania zawartości pliku.

Przykładowy ładunek wejściowy dla wywołania POST w interfejsie API /Scene:

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

**Tworzenie pliku sceny**

Identyfikator sceny zwrócony w kroku 3 jest dane wejściowe dla pliku sceny. Plik sceny zwraca token adresu URL sygnatury dostępu Współdzielonego, który jest ważny przez 24 godziny.

Jeśli użytkownik wymaga programowego sposobu przekazywania strumienia obrazów, sdk magazynu obiektów blob może służyć do definiowania metody przy użyciu identyfikatora pliku sceny, lokalizacji i adresu URL.

Przykładowy ładunek wejściowy dla wywołania POST w interfejsie API /SceneFile:

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

Wywołanie POST do interfejsu API /SceneFile zwraca adres URL przekazywania sygnatury dostępu Współdzielonego, który może służyć do przekazywania pliku csv lub .tiff przy użyciu klienta lub biblioteki magazynu obiektów Blob platformy Azure.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat szczegółów integracji opartej na interfejsie API REST, zobacz [INTERFEJS API REST](rest-api-in-azure-farmbeats.md).
