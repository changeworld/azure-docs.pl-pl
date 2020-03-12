---
title: Kwerenda pozyskiwania danych telemetrycznych
description: W tym artykule opisano, jak wykonywać zapytania dotyczące pozyskiwanych danych telemetrycznych.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 2786519bfc54b39c986ba57c78d9d6409a596351
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129781"
---
# <a name="query-ingested-telemetry-data"></a>Kwerenda pozyskiwania danych telemetrycznych

W tym artykule opisano sposób wykonywania zapytań dotyczących pozyskiwanych danych z czujnika z usługi Azure FarmBeats.

Pozyskiwanie danych z zasobów Internet rzeczy (IoT), takich jak urządzenia i czujniki, jest typowym scenariuszem w FarmBeats. Tworzysz metadane dla urządzeń i czujników, a następnie pozyskasz dane historyczne do FarmBeats w postaci kanonicznej. Gdy dane czujnika są dostępne w FarmBeats Data Hub, możemy wysyłać zapytania do tego samego, aby generować szczegółowe dane lub modele kompilacji z możliwością działania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed przejściem do tego artykułu upewnij się, że zainstalowano FarmBeats i dane telemetryczne czujnika z urządzeń IoT w FarmBeats.
Aby pozyskać dane telemetryczne czujnika, odwiedź stronę pozyskiwania [danych telemetrycznych](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Przed kontynuowaniem upewnij się, że znasz interfejsy API REST FarmBeats, ponieważ będziesz wykonywać zapytania dotyczące pozyskiwanych danych telemetrycznych za pomocą interfejsów API. Aby uzyskać więcej informacji na temat interfejsów API FarmBeats, zobacz [FarmBeats API REST](rest-api-in-azure-farmbeats.md). **Upewnij się, że możesz wykonywać żądania interfejsu API w punkcie końcowym centrum danych FarmBeats**

## <a name="query-ingested-sensor-telemetry-data"></a>Kwerenda pozyskiwania danych telemetrycznych czujnika

Istnieją dwa sposoby uzyskiwania dostępu do danych telemetrycznych z FarmBeats: API i Time Series Insights (TSI) oraz wykonywania na nich zapytań. 

### <a name="query-using-rest-api"></a>Zapytanie przy użyciu interfejsu API REST

Postępuj zgodnie z poniższymi instrukcjami, aby wykonać zapytanie dotyczące danych telemetrycznych czujnika pozyskiwania przy użyciu interfejsów API REST FarmBeats:

1. Zidentyfikuj żądany czujnik. Można to zrobić, wykonując żądanie GET w interfejsie API/sensor. Zanotuj **Identyfikator** i **sensorModelId** interesującego obiektu czujnika.

2. Utwórz funkcję GET/{ID} w interfejsie API/SensorModel dla **sensorModelId** zgodnie z opisem w kroku 1. "Model czujnika" zawiera wszystkie metadane i szczegóły pozyskiwanej danych telemetrycznych z czujnika. Na przykład "pomiar czujników" w obiekcie "model czujnika" zawiera szczegółowe informacje o tym, jakie miary są wysyłane przez czujnik i w jakich typach i jednostkach. Na przykład:

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
Zanotuj odpowiedź z wywołania GET/{ID} dla modelu czujnika.

3. Wykonaj wywołanie POST interfejsu API/Telemetry z następującym ładunkiem wejściowym

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
4. Odpowiedź z interfejsu API/Telemetry będzie wyglądać następująco:

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
W powyższym przykładzie odpowiedzi dane telemetryczne czujnika są dostępne dla dwóch sygnatur czasowych wraz z nazwą miary ("moist_soil_last") i wartościami raportowanych danych telemetrycznych w dwóch sygnaturach czasowych. Należy odwołać się do skojarzonego modelu czujnika (zgodnie z opisem w kroku 2), aby interpretować typ i jednostkę zgłoszonych wartości.

### <a name="query-using-azure-time-series-insights-tsi"></a>Zapytanie przy użyciu Azure Time Series Insights (TSI)

FarmBeats wykorzystuje [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) do pozyskiwania, przechowywania, wykonywania zapytań i wizualizacji danych w skali Internet rzeczy (IoT) — dane, które są wysoce zastrzeżonych i zoptymalizowane pod kątem szeregów czasowych.

Dane telemetryczne są odbierane w centrum EventHub, a następnie przetwarzane i wypychane do środowiska TSI w ramach grupy zasobów FarmBeats. Dane mogą następnie być wysyłane bezpośrednio z TSI. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą TSI](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) .

Postępuj zgodnie z poniższymi instrukcjami, aby wizualizować dane w TSI

1. Przejdź do witryny Azure Portal — > FarmBeats DataHub grupę zasobów — > kliknij pozycję środowisko Time Series Insights środowisku (TSI-xxxx) — > zasad dostępu do danych. Dodaj użytkownika z dostępem czytelnika lub współautorem.
2. Przejdź do strony Przegląd środowiska Time Series Insights (TSI-xxxx), a następnie kliknij pozycję "adres URL Eksploratora Time Series Insights". Teraz będzie można wizualizować pozyskiwaną telemetrię.

Oprócz przechowywania, wykonywania zapytań i wizualizacji danych telemetrycznych, TSI również umożliwia integrację z pulpitem nawigacyjnym Power BI. Więcej szczegółowych [informacji]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Następne kroki

Masz teraz zapytanie o dane czujnika z wystąpienia usługi Azure FarmBeats. Teraz Dowiedz się, jak [generować mapy](generate-maps-in-azure-farmbeats.md#generate-maps) dla Farm.
