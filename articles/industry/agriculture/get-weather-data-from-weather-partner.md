---
title: Uzyskaj dane pogodowe od partnerów pogodowych
description: W tym artykule opisano sposób utrzymywać dane pogodowe od partnerów.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266164"
---
# <a name="get-weather-data-from-weather-partners"></a>Uzyskaj dane pogodowe od partnerów pogodowych

Usługa Azure FarmBeats ułatwia przesyłanie danych pogodowych z dostawców danych pogodowych przy użyciu platformy łącznika opartej na platformie docker. Korzystając z tej struktury, dostawców danych pogodowych implementować docker, które mogą być zintegrowane z FarmBeats. Obecnie obsługiwane są następujące dostawcy danych pogodowych:

[Dane NOAA z otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/)

Dane pogodowe mogą służyć do generowania szczegółowych informacji i tworzenia modeli AI/ML na FarmBeats.

## <a name="before-you-start"></a>Przed rozpoczęciem

Aby uzyskać dane pogodowe, upewnij się, że zainstalowano FarmBeats. **Integracja pogody jest obsługiwana w wersji 1.2.11 lub nowszej.** Aby zainstalować platformę Azure FarmBeats, zobacz [Instalowanie farmbeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Włącz integrację pogody z FarmBeats

Aby rozpocząć uzyskiwanie danych pogodowych w centrum danych FarmBeats, wykonaj poniższe czynności:

1. Przejdź do centrum danych FarmBeats (https://yourdatahub.azurewebsites.net/swagger)

2. Przejdź do interfejsu API /Partner i zgłać żądanie POST z następującym ładunkiem wejściowym:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Na przykład, aby uzyskać dane pogodowe z NOAA przez Azure Open Datasets, użyj ładunku poniżej. Możesz zmodyfikować nazwę i opis zgodnie z preferencjami.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Aby uzyskać więcej informacji na temat obiektu Partner, zobacz [Dodatek](get-weather-data-from-weather-partner.md#appendix)

   W poprzednim kroku będzie aprowizować zasoby, aby włączyć docker do uruchamiania w środowisku FarmBeats klienta.  

   Trwa około 10-15 minut, aby udostępnić powyższe zasoby.

3. Sprawdź stan /Partner obiektu utworzonego w kroku 2. Aby to zrobić, należy wykonać żądanie GET w /Partner API i sprawdzić **stan** obiektu partnera. Gdy FarmBeats skutecznie przepisuje partnerowi, stan jest ustawiony na **Aktywny**.

4. Przejdź do /JobType INTERFEJSU API i złożyć żądanie GET na tym samym. Sprawdź, czy zadania pogodowe są tworzone w ramach procesu dodawania partnera w kroku 2. Pole **pipelineName** w zadaniach pogodowych będzie mieć następujący format: "partner-name_partner-type_job-name".

5. Teraz Twoje wystąpienie FarmBeats ma aktywnego partnera danych pogodowych i można uruchamiać zadania, aby zażądać danych pogodowych dla określonej lokalizacji (szerokość/długość geograficzna) i zakresu dat. JobType(y) będzie miał szczegółowe informacje na temat parametrów wymaganych do uruchamiania zadań pogodowych.

   Na przykład dla danych NOAA z zestawów danych Azure Open zostaną utworzone następujące JobType(y):

   - get_weather_data (Pobierz ISD/ historyczne dane pogodowe)
   - get_weather_forecast_data (Pobierz GFS / prognoza danych pogodowych)

6. Zanotuj **identyfikator** i parametry jobtype(y).

7. Przejdź do /Jobs API i zgłać żądanie POST na /Zadania z następującym ładunkiem wejściowym:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Na przykład, aby uruchomić **get_weather_data,** należy użyć następującego ładunku:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. W poprzednim kroku uruchomi zadania pogody zdefiniowane w docker partnera i pozyskiwania danych pogodowych do FarmBeats. Można sprawdzić stan zadania, składając żądanie GET na /Jobs i poszukaj **currentState** w odpowiedzi. Po zakończeniu, currentState jest **ustawiona**na Pomyślnie .

## <a name="query-ingested-weather-data"></a>Zapytanie o pogoń za danymi pogodowymi

Po zakończeniu zadań pogodowych można zbadać pochłonięte dane pogodowe w celu utworzenia modeli lub szczegółowych informacji o możliwości wykonania akcji. Istnieją dwa sposoby uzyskiwania dostępu do danych pogodowych i zapytań z FarmBeats:

- API i
- Statystyki szeregów czasowych (TSI).

### <a name="query-using-rest-api"></a>Kwerenda przy użyciu interfejsu API REST

Aby zbadać dane pogodowe przy użyciu interfejsu API Rest FarmBeats, wykonaj poniższe czynności:

1. W witrynie Centrum danych FarmBeats ( przejdźhttps://yourdatahub.azurewebsites.net/swagger)do interfejsu API /WeatherDataLocation i złóż żądanie GET. Odpowiedź będzie miała /WeatherDataLocation obiektów utworzonych dla lokalizacji (szerokość/długość geograficzna), który został określony jako część zadania. Zanotuj **identyfikator** i **identyfikator weatherDataModelId** obiektu(-ów).

2. Make get/{id} w /WeatherDataModel API dla **weatherDataModelId** jak wspomniano w kroku 1. "Model danych pogodowych" zawiera wszystkie metadane i szczegóły dotyczące pogoń za danymi pogodowymi. Na przykład **miara pogody** w obiekcie **Model danych pogodowych** zawiera szczegółowe informacje o tym, jakie informacje o pogodzie są obsługiwane oraz w jakich typach i jednostkach. Na przykład:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Zanotuj odpowiedź z wywołania GET/{id} dla modelu danych pogody.

3. Przejdź do interfejsu API **telemetrii** i zgłać żądanie POST z następującym ładunkiem wejściowym:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Odpowiedź zawierająca dane pogodowe, która jest dostępna dla określonego zakresu czasu będzie wyglądać następująco:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

W poprzednim przykładzie odpowiedź ma dane dla dwóch sygnatur czasowych wraz z nazwą miary ("Temperatura") i wartościami zgłoszonych danych pogodowych w dwóch sygnaturach czasowych. Należy odwołać się do skojarzonego modelu danych pogodowych (zgodnie z opisem w kroku 2 powyżej), aby zinterpretować typ i jednostkę zgłoszonych wartości.

### <a name="query-using-azure-time-series-insights-tsi"></a>Kwerenda przy użyciu usługi Azure Time Series Insights (TSI)

FarmBeats używa [usługi Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) do pozyskiwania, przechowywania, wykonywania zapytań i wizualizowania danych w skali IoT — danych, które są wysoce kontekstowe i zoptymalizowane pod kątem szeregów czasowych.

Dane pogodowe są odbierane w ujrze eventhub, a następnie wypychane do środowiska TSI w grupie zasobów FarmBeats. Dane mogą być następnie bezpośrednio wyszukiwane z TSI. Aby uzyskać więcej informacji, zobacz [dokumentację TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Wykonaj czynności, aby wizualizować dane w TSI:

1. Przejdź do grupy zasobów **Usługi Azure portal** > **FarmBeats DataHub** > wybierz opcję **Środowisko usługi Time Series Insights** (tsi-xxxx) > zasady dostępu **do danych**. Dodaj użytkownika za pomocą programu Reader lub dostęp współautora.

2. Przejdź do strony **Przegląd** środowiska **usługi Time Series Insights** (tsi-xxxx) i wybierz adres URL **Eksploratora wglądu**w szczegółowe informacje o szeregach czasowych . Teraz można wizualizować połkniętych danych pogodowych.

Oprócz przechowywania, wykonywania zapytań i wizualizacji danych pogodowych, TSI umożliwia również integrację z pulpitem nawigacyjnym usługi Power BI. Aby uzyskać więcej informacji, zobacz [Wizualizacja danych z usługi Time Series Insights w usłudze Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Dodatek

|        Partner   |  Szczegóły   |
| ------- | -------             |
|     DockerDetails - imageName         |          Nazwa obrazu platformy Docker. Na przykład docker.io/azurefarmbeats/farmbeats-noaa (obraz w hub.docker.com) LUB myazureacr.azurecr.io/mydockerimage (obraz w usłudze Azure Container Registry) i tak dalej. Jeśli nie podano rejestru, domyślnie jest hub.docker.com      |
|          DockerDetails - imageTag             |         Oznacz nazwę obrazu docker. Wartość domyślna to "najnowsze"     |
|  DockerDetails — poświadczenia      |  Poświadczenia dostępu do prywatnego dokowane. Zostanie to dostarczone przez partnera klientowi   |
|  DockerDetails — azureBatchVMDetails - batchVMSKU     |    Jednostka SKU maszyny wirtualnej usługi Azure batch. Zobacz [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dla wszystkich maszyn wirtualnych Linuksa dostępnych. Prawidłowe wartości to: "Small", "ExtraLarge", "Large", "A8", "A9", "Medium", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4". , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3 BASIC_A2", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC24", "STANDARD_NC24r". , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Wartość domyślna to "standard_d2_v2"**  |
|    DockerDetails — azureBatchVMDetails — dedicatedComputerNodes   |  Nie. dedykowanych węzłów komputerowych dla puli wsadowej. Wartość domyślna to 1. |
|    DockerDetails — azureBatchVMDetails - nodeAgentSKUID          |    Identyfikator jednostki SKU agenta węzła wsadowego platformy Azure. Obecnie obsługiwany jest tylko agent węzła wsadowego "batch.node.ubuntu 18.04".    |
| DockerDetails — partnerCredentials | poświadczenia do wywoływania interfejsu API partnera w docker. Partner musi przekazać te informacje swoim klientom w oparciu o obsługiwany mechanizm eru. Nazwa użytkownika/hasło lub klucze INTERFEJSU API. |
| typ partnera | "Pogoda" (Inne typy partnerów w FarmBeats to "Czujnik" i "Zdjęcia")  |
|  name   |   Żądana nazwa partnera w systemie FarmBeats   |
|  description |  Opis   |

## <a name="next-steps"></a>Następne kroki

Teraz masz poszukiwane dane czujnika z wystąpienia Usługi Azure FarmBeats. Teraz dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla swoich farm.
