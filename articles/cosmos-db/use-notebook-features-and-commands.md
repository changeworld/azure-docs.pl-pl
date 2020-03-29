---
title: Używanie wbudowanych poleceń i funkcji notesu w usłudze Azure Cosmos DB (wersja zapoznawcza)
description: Dowiedz się, jak używać wbudowanych poleceń i funkcji do wykonywania typowych operacji przy użyciu wbudowanych notesów usługi Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513403"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Używanie wbudowanych poleceń i funkcji notesu w usłudze Azure Cosmos DB (wersja zapoznawcza)

Wbudowane notesy Jupyter w usłudze Azure Cosmos DB umożliwiają analizowanie i wizualizowanie danych z witryny Azure portal. W tym artykule opisano sposób wykonywania typowych operacji przy użyciu wbudowanych funkcji i poleceń notesu.

## <a name="install-a-new-package"></a>Instalowanie nowego pakietu
Po włączeniu obsługi notesu dla kont usługi Azure Cosmos można otworzyć nowy notes i zainstalować pakiet.

W nowej komórce kodu wstaw i ``PackageToBeInstalled`` uruchom następujący kod, zastępując żądanym pakietem Języka Python.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Ten pakiet będzie dostępny do użycia z dowolnego notesu w obszarze roboczym konta usługi Azure Cosmos. 

> [!TIP]
> Jeśli notes wymaga pakietu niestandardowego, zaleca się dodanie komórki w notesie w celu zainstalowania pakietu, ponieważ pakiety są usuwane po [zresetowaniu obszaru roboczego](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Uruchamianie kwerendy SQL

Za pomocą ``%%sql`` polecenia magic można uruchomić [kwerendę SQL](sql-query-getting-started.md) względem dowolnego kontenera na koncie. Użyj składni:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Zastąp ``{database_id}`` i ``{container_id}`` nazwę bazy danych i kontenera na koncie usługi Cosmos. Jeśli ``--database`` argumenty ``--container`` i argumenty nie są podane, kwerenda zostanie wykonana w [domyślnej bazie danych i kontenerze](#set-default-database-for-queries).
- Można uruchomić dowolną kwerendę SQL, która jest prawidłowa w usłudze Azure Cosmos DB. Tekst kwerendy musi znajdować się w nowym wierszu.

Przykład: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Uruchom ```%%sql?``` w komórce, aby zobaczyć dokumentację pomocy dla polecenia sql magic w notesie.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Uruchamianie kwerendy SQL i dane wyjściowe do pandas DataFrame

Wyniki ``%%sql`` kwerendy można wyprowadzić do [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Użyj składni: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Zastąp ``{database_id}`` i ``{container_id}`` nazwę bazy danych i kontenera na koncie usługi Cosmos. Jeśli ``--database`` argumenty ``--container`` i argumenty nie są podane, kwerenda zostanie wykonana w [domyślnej bazie danych i kontenerze](#set-default-database-for-queries).
- Zamień ``{outputDataFrameVar}`` na nazwę zmiennej DataFrame, która będzie zawierać wyniki.
- Można uruchomić dowolną kwerendę SQL, która jest prawidłowa w usłudze Azure Cosmos DB. Tekst kwerendy musi znajdować się w nowym wierszu. 

Przykład:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>Przekazywanie elementów JSON do kontenera
Za pomocą ``%%upload`` polecenia magic można przekazać dane z pliku JSON do określonego kontenera usługi Azure Cosmos. Użyj następującego polecenia, aby przekazać elementy:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Zamień ``{database_id}`` i ``{container_id}`` nazwę bazy danych i kontenera na koncie usługi Azure Cosmos. Jeśli ``--database`` argumenty ``--container`` i argumenty nie są podane, kwerenda zostanie wykonana w [domyślnej bazie danych i kontenerze](#set-default-database-for-queries).
- Zamień ``{url_location_of_file}`` lokalizację pliku JSON. Plik musi być tablicą prawidłowych obiektów JSON i powinien być dostępny za pośrednictwem publicznego Internetu.

Przykład:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Za pomocą statystyk wyjściowych można obliczyć efektywne ru/s używane do przesyłania elementów. Na przykład jeśli 25 000 ru zostały zużyte w ciągu 38 sekund, skuteczne RU/s jest 25,000 RU / 38 sekund = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Ustawianie domyślnej bazy danych dla kwerend
Można ustawić domyślne ```%%sql``` polecenia bazy danych będą używane dla notesu. Zamień ```{database_id}``` na nazwę bazy danych.

```bash
%database {database_id}
```
Uruchom ```%database?``` w komórce, aby wyświetlić dokumentację w notesie.

## <a name="set-default-container-for-queries"></a>Ustawianie domyślnego kontenera dla kwerend
Można ustawić domyślne ```%%sql``` polecenia kontenera będą używane dla notesu. Zamień ```{container_id}``` na nazwę kontenera.

```bash
%container {container_id}
```
Uruchom ```%container?``` w komórce, aby wyświetlić dokumentację w notesie.

## <a name="use-built-in-nteract-data-explorer"></a>Korzystanie z wbudowanego eksploratora danych nteract
Za pomocą wbudowanego [eksploratora danych nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) można filtrować i wizualizować ramkę dataframe. Aby włączyć tę funkcję, ``pd.options.display.html.table_schema`` ``True`` ustaw ``pd.options.display.max_rows`` opcję na żądaną ``pd.options.display.max_rows`` wartość ``None`` i żądaną (można ustawić, aby wyświetlić wszystkie wyniki).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract eksplorator danych](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Korzystanie z wbudowanego modułu SDK języka Python
Wersja 4 [interfejsu SDK języka Python usługi Azure Cosmos DB dla interfejsu API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) jest zainstalowana i uwzględniona w środowisku notesu dla konta usługi Azure Cosmos.

Użyj wbudowanego ``cosmos_client`` wystąpienia, aby uruchomić dowolną operację SDK. 

Przykład:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Zobacz [przykłady zestawów SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> Wbudowany pakiet SDK języka Python jest obsługiwany tylko dla kont interfejsu API SQL (Core). W przypadku innych interfejsów API należy [zainstalować odpowiedni sterownik języka Python,](#install-a-new-package) który odpowiada interfejsowi API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Tworzenie niestandardowego wystąpienia``cosmos_client``
Aby uzyskać większą elastyczność, można ``cosmos_client`` utworzyć niestandardowe wystąpienie w celu:

- Dostosowywanie [zasad połączeń](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- Uruchamiaj operacje względem innego konta usługi Azure Cosmos niż konto, w którego się znajdujesz

Dostęp do ciągu połączenia i klucza podstawowego bieżącego konta można uzyskać za pośrednictwem [zmiennych środowiskowych](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Uzyskiwanie dostępu do zmiennych env punktu końcowego konta i klucza podstawowego
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` Zmienne ``COSMOS_KEY`` środowiskowe i środowiskowe mają zastosowanie tylko do interfejsu API SQL. W przypadku innych interfejsów API znajdź punkt końcowy i klucz w **bloku Parametry połączenia** lub **klucze** na koncie usługi Azure Cosmos.  

## <a name="reset-notebooks-workspace"></a>Resetowanie obszaru roboczego notesów
Aby zresetować obszar roboczy notesów do ustawień domyślnych, wybierz **pozycję Resetuj obszar roboczy** na pasku poleceń. Spowoduje to usunięcie wszystkich niestandardowych zainstalowanych pakietów i ponowne uruchomienie serwera Jupyter. Nie będzie to miało wpływu na notesy, pliki i zasoby usługi Azure Cosmos.  

![Resetowanie obszaru roboczego notesów](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o zaletach [notebooków usługi Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Dowiedz się więcej o interfejsie [SDK języka Python usługi Azure Cosmos DB dla interfejsu API SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
