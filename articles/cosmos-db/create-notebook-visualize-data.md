---
title: 'Samouczek: Tworzenie notesu w usłudze Azure Cosmos DB w celu analizowania i wizualizowania danych'
description: 'Samouczek: Dowiedz się, jak używać wbudowanych notesów Jupyter do importowania danych do usługi Azure Cosmos DB, analizowania danych i wizualizacji danych wyjściowych.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73720928"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Samouczek: Tworzenie notesu w usłudze Azure Cosmos DB w celu analizowania i wizualizowania danych

W tym artykule opisano sposób używania wbudowanych notesów Jupyter do importowania przykładowych danych detalicznych do usługi Azure Cosmos DB. Zobaczysz, jak używać magicznych poleceń SQL i Usługi Azure Cosmos DB do uruchamiania zapytań, analizowania danych i wizualizacji wyników.

## <a name="prerequisites"></a>Wymagania wstępne

* [Włączanie obsługi notesów podczas tworzenia konta usługi Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Tworzenie zasobów i importowanie danych
 
W tej sekcji utworzysz bazę danych usługi Azure Cosmos, kontener i zaimportujesz dane sieci sprzedaży do kontenera.

1. Przejdź do swojego konta usługi Azure Cosmos i otwórz **Eksploratora danych.**

1. Przejdź do karty **Notesy,** wybierz obok `…` pozycji Moje **notesy** i utwórz nowy **notes**. Wybierz **Python 3** jako domyślne jądro.

   ![Tworzenie nowego notesu](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Po utworzeniu nowego notesu można zmienić jego nazwę na coś takiego jak **VisualizeRetailData.ipynb.**

1. Następnie utworzysz bazę danych o nazwie "RetailDemo" i kontener o nazwie "WebsiteData" do przechowywania danych detalicznych. Jako klucz partycji można użyć /CardID. Skopiuj i wklej następujący kod do nowej komórki notesu i uruchom go:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Aby uruchomić komórkę, zaznacz lub `Shift + Enter` zaznacz komórkę i wybierz opcję Uruchom **aktywną komórkę** na pasku nawigacyjnym eksploratora danych.

   ![Uruchamianie aktywnej komórki](./media/create-notebook-visualize-data/run-active-cell.png)

   Baza danych i kontener są tworzone na bieżącym koncie usługi Azure Cosmos. Kontener jest aprowizowany z 400 RU/s. Po utworzeniu bazy danych i kontenera zostaną wyświetleniu następujące dane wyjściowe. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Można również odświeżyć kartę **Dane** i wyświetlić nowo utworzone zasoby:

   ![Odśwież kartę danych, aby wyświetlić nowy kontener](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Następnie zaimportujesz przykładowe dane sieci sprzedaży do kontenera usługi Azure Cosmos. Oto format elementu z danych detalicznych:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   W celu samouczka przykładowe dane sieci sprzedaży są przechowywane w magazynie obiektów blob platformy Azure. Można zaimportować go do kontenera usługi Azure Cosmos, wklejając następujący kod do nowej komórki. Można potwierdzić, że dane zostały pomyślnie zaimportowane, uruchamiając kwerendę, aby wybrać liczbę elementów.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Po uruchomieniu poprzedniej kwerendy zwraca następujące dane wyjściowe:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Pobierz swoje dane do dataframe

Przed uruchomieniem kwerend do analizy danych, można odczytać dane z kontenera do [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) do analizy. Użyj następującego polecenia sql magic, aby odczytać dane w ramce DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Aby dowiedzieć się więcej, zobacz [wbudowane polecenia notesu i funkcje w artykule usługi Azure Cosmos DB.](use-notebook-features-and-commands.md) Uruchomisz zapytanie- `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. Wyniki zostaną zapisane w Pandas DataFrame o nazwie df_cosmos. Wklej następujące polecenie w nowej komórce notesu i uruchom ją:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

W nowej komórce notesu uruchom następujący kod, aby odczytać pierwsze 10 elementów z danych wyjściowych:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Uruchom kwerendę, aby uzyskać 10 najważniejszych elementów](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Uruchamianie zapytań i analizowanie danych

W tej sekcji zostanie uruchomione niektóre kwerendy dotyczące pobranych danych.

* **Zapytanie1:** Uruchom grupę według kwerendy na DataFrame, aby uzyskać sumę całkowitych przychodów ze sprzedaży dla każdego kraju i wyświetlić 5 elementów z wyników. W nowej komórce notesu uruchom następujący kod:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Łączna produkcja przychodów ze sprzedaży](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Zapytanie2:** Aby uzyskać listę pięciu pierwszych zakupionych elementów, otwórz nową komórkę notesu i uruchom następujący kod:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Pięć najlepszych zakupionych przedmiotów](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Wizualizacja danych  

1. Teraz, gdy mamy nasze dane dotyczące przychodów z kontenera usługi Azure Cosmos, możesz wizualizować dane za pomocą wybranej biblioteki wizualizacji. W tym samouczku użyjemy biblioteki Bokeh. Otwórz nową komórkę notesu i uruchom następujący kod, aby zainstalować bibliotekę Bokeh. Po spełnieniu wszystkich wymagań biblioteka zostanie zainstalowana.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Następnie przygotuj się do wykreślenia danych na mapie. Dołącz do danych w usłudze Azure Cosmos DB z informacjami o kraju znajdującymi się w magazynie obiektów Blob platformy Azure i przekonwertuj wynik na format GeoJSON. Skopiuj następujący kod do nowej komórki notesu i uruchom go.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Wizualizuj przychody ze sprzedaży różnych krajów na mapie świata, uruchamiając następujący kod w nowej komórce notesu:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   Dane wyjściowe wyświetla mapę świata w różnych kolorach. Kolory ciemniejsze do jaśniejszych reprezentują kraje o najwyższych dochodach do najniższych dochodów.

   ![Wizualizacja mapy przychodów krajów](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Zobaczmy inny przypadek wizualizacji danych. Kontener WebsiteData zawiera rekord użytkowników, którzy wyświetlili element, dodali go do koszyka i zakupili. Wykreślijmy współczynnik konwersji zakupionych przedmiotów. Uruchom następujący kod w nowej komórce, aby wizualizować współczynnik konwersji dla każdego elementu:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![Wizualizuj współczynnik konwersji zakupu](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o poleceniach notesu, [zobacz, jak używać wbudowanych poleceń i funkcji notesu w artykule usługi Azure Cosmos DB.](use-notebook-features-and-commands.md)
