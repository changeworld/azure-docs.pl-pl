---
title: 'Obciążenia: przeznaczonego do przygotowania danych zestawu SDK języka Python'
titleSuffix: Azure Machine Learning service
description: Więcej informacji na temat ładowania danych przy użyciu zestawu SDK usługi Azure Machine Learning danych przedprodukcyjnym. Możesz załadować różnego rodzaju dane wejściowe, określić typy plików danych i parametry lub automatycznie Wykryj typ pliku przy użyciu funkcji odczytu inteligentne zestawu SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 2/22/2019
ms.custom: seodec18
ms.openlocfilehash: 34dd20826928d1ab2ba1fc7980c7d47b796ea663
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819213"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Ładowanie i odczytać dane za pomocą usługi Azure Machine Learning

W tym artykule dowiesz się różnych metod ładowania danych przy użyciu usługi Azure Machine Learning Prep zestawu SDK usługi Data. Aby wyświetlić dokumentację referencyjną dla zestawu SDK, zobacz [Przegląd](https://aka.ms/data-prep-sdk). Zestaw SDK obsługuje wiele funkcji pozyskiwania danych, takich jak:

* Ładowanie z wiele typów plików za pomocą analizy parametru wnioskowania (kodowanie, separator, nagłówki)
* Konwersja typu użycia wnioskowania podczas ładowania pliku
* Funkcja obsługi połączeń MS SQL Server i usługi Azure Data Lake Storage

W poniższej tabeli przedstawiono szereg funkcji, które służą do ładowania danych z popularnych typów plików.

| Typ pliku | Funkcja | Opis łącza |
|-------|-------|-------|
|Dowolne|`auto_read_file()`|[Odwołanie](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Text|`read_lines()`|[Odwołanie](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-lines-path--filepath--header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[Odwołanie](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[Odwołanie](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Stała szerokość|`read_fwf()`|[Odwołanie](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[Odwołanie](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Automatyczne ładowanie danych

Aby załadować dane automatycznie bez określenia typu pliku, należy użyć `auto_read_file()` funkcji. Typ pliku i argumenty wymagane do jego odczytu są automatycznie wywnioskować.

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Funkcja ta jest przydatna do automatycznego wykrywania typu pliku, kodowanie i inne argumenty analizy wszystko z jednego wpisu wygodny punkt. Funkcja automatycznie wykonuje następujące czynności, które są najczęściej wykonywane podczas ładowania danych podzielonej:

* Wnioskowanie i ustawiania ogranicznik
* Pomija puste rekordy w górnej części pliku
* Wnioskowanie i ustawiając wiersz nagłówka

Alternatywnie Jeśli znasz pliku, wpisz wcześniej i chcesz jawnie kontrolować sposób, który jest analizowany, należy użyć funkcji specyficznych dla pliku.

## <a name="load-text-line-data"></a>Ładowanie danych wiersza tekstu

Aby odczytać dane zwykłego tekstu do przepływu danych, należy użyć `read_lines()` bez określania parametrów opcjonalnych.

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||Kreska|
|----|-----|
|0|Data \| \| minimalna temperatura \| \| maksymalna temperatura|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|


Po dane są pozyskiwane, uruchom następujący kod, aby przekonwertować obiektu przepływu danych do elementów Pandas dataframe.

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Załaduj dane CSV

Podczas odczytywania plików, podstawowego środowiska uruchomieniowego można wywnioskować podczas analizowania parametrów (separator, kodowanie, czy ma być używany, nagłówków itp.). Uruchom następujący kod, aby podjąć próbę odczytania pliku CSV, określając tylko jego lokalizacji.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale hrabstwa|10171002158| |
|2|ALABAMA|1|101710|Hale hrabstwa|10171002162| |


Aby wykluczyć wiersze podczas ładowania, należy zdefiniować `skip_rows` parametru. Ten parametr zostanie pominięta podczas ładowania wierszy malejącej w pliku CSV (przy użyciu indeksu liczonego od jednego).

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale hrabstwa|10171002158|29|
|1|ALABAMA|1|101710|Hale hrabstwa|10171002162|40 |

Uruchom poniższy kod, aby wyświetlić typy danych kolumn.

```python
dflow.dtypes
```
Dane wyjściowe:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Domyślnie program Azure Machine Learning Prep zestawu SDK usługi Data nie zmienia typu danych. Źródła danych, które czytasz z jest plikiem tekstowym, aby zestaw SDK odczytuje wszystkie wartości jako ciągi. W tym przykładzie kolumny liczbowe ma być analizowana pod jako liczby. Ustaw `inference_arguments` parametr `InferenceArguments.current_culture()` automatycznie wywnioskować i przekonwertować typy kolumn podczas plik odczytany.

```
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
Dane wyjściowe:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Wykryto wiele kolumn poprawnie jako wartość numeric i ich typu jest ustawiona na `float64`.

## <a name="use-excel-data"></a>Korzystanie z danych programu Excel

Zestaw SDK zawiera `read_excel()` funkcję, aby załadować pliki programu Excel. Domyślnie funkcja zostanie załadowany pierwszy arkusz w skoroszycie. Aby zdefiniować określonego arkusza do ładowania, należy zdefiniować `sheet_name` parametru z wartością ciągu o nazwie arkusza.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Kolumna1|Kolumna2|Kolumna3|Kolumna4|Column5|Kolumna6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak| |
|1|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak| |
|2|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak| |
|3|Ranga|Stanowisko|Studio|Cały świat|Wywiad krajowy / %|Kolumna1|Zbiorowość / %|Kolumna2|Rok ^| |
|4|1|Awatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

Dane wyjściowe pokazują, że dane w arkuszu drugi miał trzy puste wiersze przed nagłówków. `read_excel()` Funkcja zawiera parametry opcjonalne dla pomijania wierszy i korzystanie z nagłówków. Uruchom poniższy kod, aby pominąć pierwsze trzy wiersze, a następnie użyć czwartego wiersza jako nagłówków.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||Ranga|Stanowisko|Studio|Cały świat|Wywiad krajowy / %|Kolumna1|Zbiorowość / %|Kolumna2|Rok ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Awatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|

## <a name="load-fixed-width-data-files"></a>Ładowanie plików danych o stałej szerokości

Aby załadować pliki o stałej szerokości, należy określić listę znaków offsetowych. Pierwsza kolumna jest zawsze zakłada się, że rozpoczynają się od przesunięcia, zerowego.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|SFAŁSZOWANY NORWEGIA|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|SFAŁSZOWANY NORWEGIA|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


Aby uniknąć wykrywania nagłówka i analizy danych, należy przekazać `PromoteHeadersMode.NONE` do `header` parametru.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Kolumna1|Kolumna2|Kolumna3|Kolumna4|Column5|Kolumna6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|SFAŁSZOWANY NORWEGIA|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|SFAŁSZOWANY NORWEGIA|NO|NO|ENSO||||


## <a name="load-sql-data"></a>Ładowanie danych SQL

Zestaw SDK może również ładowanie danych ze źródła SQL. Aktualnie obsługiwana jest tylko program Microsoft SQL Server. Do odczytywania danych z programu SQL server, należy utworzyć [ `MSSQLDataSource` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) obiekt, który zawiera parametry połączenia. Parametr hasła `MSSQLDataSource` akceptuje [ `Secret` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) obiektu. Można utworzyć wpisu tajnego obiektu na dwa sposoby:

* Zarejestruj wpisu tajnego i jego wartość przy użyciu aparatu wykonywania.
* Tworzenie wpisu tajnego z tylko `id` (Jeśli wartość wpisu tajnego jest już zarejestrowany w środowisku wykonawczym) przy użyciu `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Po utworzeniu obiektu źródła danych, możesz przejść do odczytywania danych z wyników zapytania.

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |Identyfikator produktu|Name (Nazwa)|ProductNumber|Kolor|StandardCost|ListPrice|Rozmiar|Waga|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|Thumbnailphoto usługa|ThumbnailPhotoFileName|ROWGUID|Data modyfikacji| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|Rama szosowa HL — czarna, 58|FR — R92B-58|Czarny|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|Rama szosowa HL — czerwona, 58|FR — R92R-58|Czerwony|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Kask Sport-100, czerwony|RAMA U509-R|Czerwony|13.0863|34.99|Brak|Brak|35|33|2005-07-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|


## <a name="use-azure-data-lake-storage"></a>Użyj usługi Azure Data Lake Storage

Istnieją dwa sposoby zestawu SDK można pobrać niezbędne tokenu protokołu OAuth do dostępu do usługi Azure Data Lake Storage:

* Pobieranie tokenu dostępu z najnowszych sesji logowania wiersza polecenia platformy Azure użytkownika
* Użyj nazwy głównej usługi (SP) i certyfikat jako wpis tajny

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Użyj tokenu dostępu z ostatnich sesji wiersza polecenia platformy Azure

Na komputerze lokalnym uruchom następujące polecenie.

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> Jeśli Twoje konto użytkownika jest członkiem więcej niż jednej dzierżawy platformy Azure, należy określić dzierżawy w formie nazwy hosta URL usługi AAD.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Tworzenie jednostki usługi przy użyciu wiersza polecenia platformy Azure

Użyj wiersza polecenia platformy Azure, aby utworzyć nazwę główną usługi i odpowiedni certyfikat. Tej jednostki usługi w szczególności skonfigurowano z `reader` roli z jej zakresem skrócony do tylko konto usługi Azure Data Lake Storage "dpreptestfiles".

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

To polecenie generuje `appId` i ścieżkę do pliku certyfikatu (zwykle w folderze głównym). Plik .crt zawiera zarówno certyfikatu publicznego, jak i klucz prywatny w formacie PEM.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Aby skonfigurować listy ACL do systemu plików usługi Azure Data Lake Storage, użyj identyfikator obiektu użytkownika. W tym przykładzie nazwa główna usługi jest używana.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Aby skonfigurować `Read` i `Execute` dostępu systemu plików usługi Azure Data Lake Storage, można skonfigurować listy ACL dla plików i folderów osobno. Jest to z faktu, że odpowiedni model listy ACL systemu plików HDFS nie obsługują dziedziczenia.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Uzyskiwanie tokenu dostępu OAuth

Użyj `adal` pakietu (`pip install adal`) do tworzenia kontekstu uwierzytelniania w dzierżawie MSFT i uzyskiwanie tokenu dostępu OAuth. Azure Data Lake Store, zasobu w żądania tokenu musi być dla "https:\//datalake.azure.net", która różni się od najbardziej innych zasobów platformy Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Witryna internetowa|Ulica|city|Powiat|
|----|------|-----|----|----|----|----|
|0|1012063|Rolników Kaledonia rynek skojarzenie — Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledonia|
|1|1011871|Rynek "rolników Stearns Homestead|http://Stearnshomestead.com |Drogowa ridge 6975|Parma|Cuyahoga|
|2|1011878|Rynek 100 mila|https://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. głównej ulicy rolników rynku|http://thetownofsixmile.wordpress.com/ |106 S. głównej ulicy|Sześć mila|||
|4|1010691|Ulica 10 społeczności rolników rynku|https://agrimissouri.com/... |Ulica 10 i topoli|Lamar|Barton|

## <a name="next-steps"></a>Kolejne kroki

* Zobacz zestaw SDK [Przegląd](https://aka.ms/data-prep-sdk) wzorców projektowych i przykłady użycia
* Zobacz zestaw SDK Azure Machine Learning danych Prep [samouczek](tutorial-data-prep.md) przykładem rozwiązania konkretnego scenariusza
