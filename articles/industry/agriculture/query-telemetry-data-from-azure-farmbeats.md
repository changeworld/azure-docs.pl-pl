---
title: Kwerenda przygodzone dane telemetryczne
description: W tym artykule opisano sposób wykonywania zapytań o dane telemetryczne.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349807"
---
# <a name="query-ingested-telemetry-data"></a>Kwerenda przygodzone dane telemetryczne

W tym artykule opisano sposób wykonywania zapytań o dane z czujników pozyskiwania z poziomu usługi Azure FarmBeats.

Pojmowanie danych z zasobów Internetu rzeczy (IoT), takich jak urządzenia i czujniki, jest typowym scenariuszem w FarmBeats. Tworzenie metadanych dla urządzeń i czujników, a następnie pozyskiwania danych historycznych do FarmBeats w formacie kanonicznym. Gdy dane czujnika są dostępne w usłudze FarmBeats Datahub, możemy zbadać to samo, aby wygenerować użyteczne informacje lub utworzyć modele.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed kontynuowaniem tego artykułu upewnij się, że zainstalowano FarmBeats i pochłonięte dane telemetryczne czujnika z urządzeń IoT do FarmBeats.

To ingest sensor telemetry data, visit [ingest historical telemetry data](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Przed kontynuowaniem należy również upewnić się, że są zaznajomieni z FarmBeats REST interfejsów API, jak będzie kwerenda pozyskiwania danych telemetrycznych przy użyciu interfejsów API. Aby uzyskać więcej informacji na temat interfejsów API FarmBeats, zobacz [FarmBeats REST API .](rest-api-in-azure-farmbeats.md) **Upewnij się, że możesz składać żądania interfejsu API do punktu końcowego Pola Danych FarmBeats.**

## <a name="query-ingested-sensor-telemetry-data"></a>Kwerenda przygodzone dane telemetryczne czujnika

Istnieją dwa sposoby uzyskiwania dostępu do danych telemetrycznych i zapytań z FarmBeats:

- API i
- Statystyki szeregów czasowych (TSI).

### <a name="query-using-rest-api"></a>Kwerenda przy użyciu interfejsu API REST

Wykonaj kroki, aby zbadać dane telemetryczne pochłoniętych czujników przy użyciu interfejsów API FarmBeats REST:

1. Zidentyfikuj czujnik, który Cię interesuje. Można to zrobić, składając żądanie GET na /Sensor API.

> [!NOTE]
> **Identyfikator** i **czujnikModelId** zainteresowanych obiektów czujnika.

2. Zrób GET/{id} na /SensorModel API dla **sensorModelId,** jak wspomniano w kroku 1. "Model czujnika" zawiera wszystkie metadane i szczegóły dotyczące połkniętych danych telemetrycznych z czujnika. Na przykład **miara czujnika** w obiekcie **Model czujnika** zawiera szczegółowe informacje o tym, jakie środki są wysyłane przez czujnik oraz jakie typy i jednostki. Na przykład:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Zanotuj odpowiedź z wywołania GET/{id} dla modelu czujnika.

3. Wykonaj wywołanie POST w interfejsie API /Telemetry z następującym ładunkiem wejściowym

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Odpowiedź z /Telemetry API będzie wyglądać mniej więcej tak:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
W powyższej odpowiedzi przykładowej dane telemetryczne czujnika kwerendy podaje dane dla dwóch sygnatur czasowych wraz z nazwą miary ("moist_soil_last") i wartością zgłoszonych danych telemetrycznych w dwóch sygnaturach czasowych. Aby zinterpretować typ i jednostkę zgłoszonych wartości, należy odwołać się do skojarzonego modelu czujnika (zgodnie z opisem w kroku 2).

### <a name="query-using-azure-time-series-insights-tsi"></a>Kwerenda przy użyciu usługi Azure Time Series Insights (TSI)

FarmBeats wykorzystuje [usługę Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) do pozyskiwania, przechowywania, wykonywania zapytań i wizualizowania danych w skali IoT — danych, które są wysoce kontekstowe i zoptymalizowane pod kątem szeregów czasowych.

Dane telemetryczne są odbierane w uspotkaniu EventHub, a następnie przetwarzane i wypychane do środowiska TSI w grupie zasobów FarmBeats. Dane mogą być następnie bezpośrednio wyszukiwane z TSI. Aby uzyskać więcej informacji, zobacz [dokumentację TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Wykonaj czynności, aby wizualizować dane w TSI:

1. Przejdź do grupy zasobów **Usługi Azure Portal** > **FarmBeats DataHub** > wybierz opcję **Środowisko usługi Time Series Insights** (tsi-xxxx) > zasady dostępu **do danych**. Dodaj użytkownika za pomocą programu Reader lub dostęp współautora.
2. Przejdź do strony **Przegląd** środowiska **usługi Time Series Insights** (tsi-xxxx) i wybierz adres URL **Eksploratora wglądu**w szczegółowe informacje o szeregach czasowych . Teraz będzie można wizualizować pogoń za dane telemetryczne.

Oprócz przechowywania, wykonywania zapytań i wizualizacji danych telemetrycznych, TSI umożliwia również integrację z pulpitem nawigacyjnym usługi Power BI. Aby uzyskać więcej informacji, zobacz [tutaj]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Następne kroki

Teraz masz poszukiwane dane czujnika z wystąpienia Usługi Azure FarmBeats. Teraz dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla swoich farm.
