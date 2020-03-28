---
title: 'Samouczek: Dołączanie danych z czujników do danych prognozy pogody przy użyciu notesów platformy Azure (Python) | Mapy platformy Microsoft Azure'
description: W tym samouczku pokazano, jak dołączyć dane czujnika do danych prognozy pogody z usługi pogody Microsoft Azure Maps przy użyciu notesów platformy Azure (Python).
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e5292f5166e739264e9cf969480b70f415fcc75a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333494"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Samouczek: Dołączanie danych z czujników do danych prognozy pogody przy użyciu notesów platformy Azure (Python)

Energia wiatrowa jest jednym z alternatywnych źródeł energii dla paliw kopalnych w walce ze zmianami klimatycznymi. Ponieważ wiatr nie jest z natury spójny, operatorzy energii wiatrowej muszą tworzyć modele uczenia maszynowego (ML), aby przewidzieć moc wiatru. Przewidywanie to jest niezbędne do zaspokojenia zapotrzebowania na energię elektryczną i zapewnienia stabilności sieci. W tym samouczku przedstawiamy sposób łączenia danych prognozy pogody usługi Azure Maps z danymi demonstracyjnymi odczytów pogody. Dane prognozy pogody są wymagane przez wywołanie usługi Azure Maps Weather.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Praca z plikami danych w [notesach platformy Azure](https://docs.microsoft.com/azure/notebooks) w chmurze.
> * Załaduj dane demonstracyjne z pliku.
> * Wywołanie interfejsów API REST usługi Azure Maps w języku Python.
> * Renderowanie danych o lokalizacji na mapie.
> * Wzbogać dane demonstracyjne za pomocą danych pogodowych usługi Azure Maps [Daily Forecast.](https://aka.ms/AzureMapsWeatherDailyForecast)
> * Wykreślić dane prognozy na wykresach.


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz najpierw:

1. Utwórz subskrypcję konta usługi Azure Maps w warstwie cenowej S0, postępując zgodnie z instrukcjami w [sekcji Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Pobierz podstawowy klucz subskrypcji dla swojego konta, postępuj zgodnie z instrukcjami w [pobierz klucz podstawowy](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

Aby zapoznać się z notesami platformy Azure i dowiedzieć się, jak rozpocząć pracę, postępuj zgodnie z instrukcjami [Tworzenie notesu platformy Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Plik notebooka Jupyter dla tego projektu można pobrać z [repozytorium notebooka Weather Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Załaduj wymagane moduły i struktury

Aby załadować wszystkie wymagane moduły i struktury, uruchom następujący skrypt:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importowanie danych pogodowych

Ze względu na ten samouczek użyjemy odczytów danych pogodowych z czujników zainstalowanych w czterech różnych turbinach wiatrowych. Przykładowe dane składają się z 30 dni odczytów pogody. Odczyty te są zbierane z centrów danych pogodowych w pobliżu każdej lokalizacji turbiny. Dane demonstracyjne zawierają odczyty danych dotyczących temperatury, prędkości wiatru i kierunku. Możesz pobrać dane demo [stąd](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Poniższy skrypt importuje dane demonstracyjne do notesu platformy Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Żądanie dziennych danych prognozy

W naszym scenariuszu chcielibyśmy poprosić o dzienną prognozę dla każdej lokalizacji czujnika. Poniższy skrypt wywołuje [interfejs API prognozy dziennej](https://aka.ms/AzureMapsWeatherDailyForecast) usługi pogodowej usługi Azure Maps. Ten interfejs API zwraca prognozę pogody dla każdej turbiny wiatrowej na następne 15 dni od bieżącej daty.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Poniższy skrypt renderuje lokalizacje turbin na mapie, wywołując usługę Azure Maps [Pobierz obraz mapy.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Lokalizacje turbin](./media/weather-service-tutorial/location-map.png)


Dane prognozy pogrupujemy z danymi demonstracyjnymi na podstawie identyfikatora stacji. Identyfikator stacji jest przeznaczony dla centrum danych pogodowych. To grupowanie zwiększa dane demonstracyjne o dane prognozy.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

W poniższej tabeli przedstawiono połączone dane historyczne i prognozy dla jednej z lokalizacji turbiny.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Dane zgrupowane](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Wykreślić dane prognozy

Wykreślimy prognozowane wartości na dzień, dla którego są prognozowane. Ta działka pozwala nam zobaczyć zmiany prędkości i kierunku wiatru przez następne 15 dni.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Poniższe wykresy wizualizują dane prognozy. Zmiana prędkości wiatru można znaleźć na lewym wykresie. Aby uzyskać zmianę kierunku wiatru, patrz prawy wykres. Te dane są przewidywanie dla następnych 15 dni od dnia, w którym dane są wymagane.

<center>

![Wykres](./media/weather-service-tutorial/speed-date-plot.png) ![prędkości wiatru Działka kierunek wiatru](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Następne kroki

W tym samouczku, który został nauczona, jak wywołać interfejsy API REST usługi Azure Maps, aby uzyskać dane prognozy pogody. Dowiesz się również, jak wizualizować dane na wykresach.

Aby dowiedzieć się więcej o wywoływaniu interfejsów API REST usługi Azure Maps w notesach platformy Azure, zobacz [Routing przyuwającając przy użyciu notesów platformy Azure.](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing)

Aby zapoznać się z interfejsami API usługi Azure Maps, które są używane w tym samouczku, zobacz:

* [Prognoza dzienna](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render - Pobierz obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Aby uzyskać pełną listę interfejsów API REST usługi Azure Maps, zobacz [Interfejsy API REST usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Aby dowiedzieć się więcej o notesach platformy Azure, zobacz [Notesy platformy Azure](https://docs.microsoft.com/azure/notebooks).
