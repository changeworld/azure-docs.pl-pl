---
title: Ładowanie danych w zestawie Azure Machine Learning danych Prep SDK — Python
description: Więcej informacji na temat ładowania danych przy użyciu zestawu SDK usługi Azure Machine Learning danych przedprodukcyjnym. Możesz załadować różnego rodzaju dane wejściowe, określić typy plików danych i parametry lub automatycznie Wykryj typ pliku przy użyciu funkcji odczytu inteligentne zestawu SDK.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 436ff9d318dc311efe27352a8b2ac91cfb5be618
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221333"
---
#<a name="load-and-read-data-with-azure-machine-learning"></a>Ładowanie i odczytać dane za pomocą usługi Azure Machine Learning

Użyj [zestawu SDK usługi Azure Machine Learning danych Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) do ładowania różnych typów danych wejściowych. 

Aby załadować dane, masz dwie metody:
+ Określ typ pliku danych i jego parametrów
+ Automatycznie wykrywaj typ pliku przy użyciu funkcji inteligentnego czytania zestawu SDK

## <a name="use-text-line-data"></a>Korzystanie z danych wiersza tekstu 
To najprostszy sposób ładowania danych do jego odczytu jako wiersze tekstu.

Poniżej przedstawiono przykładowy kod:
```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Kreska|
|----|-----|
|0|Data \| \| minimalna temperatura \| \| maksymalna temperatura|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|
|3|2015-07-3 \| \| -7.0 \| \| 10.5|
|4|2015-07-4 \| \| -5.5 \| \| 9.3|

Po dane są pozyskiwane, możesz pobrać pandas DataFrame dla pełnego zestawu danych.

Poniżej przedstawiono przykładowy kod:
```python
df = dataflow.to_pandas_dataframe()
df
```
Przykładowe dane wyjściowe:
||Kreska|
|----|-----|
|0|Data\| \| minimalna temperatura\| \| maksymalna temperatura|
|1|2015-07-1\| \| 4.1\| \| 10.0|
|2|2015-07-2\| \| 0,8\| \| 10.8|
|3|2015-07-3\| \| 7.0\| \| 10.5|
|4|2015-07-4\| \| 5.5\| \| 9.3|

## <a name="use-csv-data"></a>Korzystanie z danych CSV
Podczas odczytywania plików, można pozwolić podstawowego środowiska uruchomieniowego wywnioskować podczas analizowania parametrów (na przykład separator kodowania, czy ma być używany, nagłówków itp.), zamiast je udostępniać. W tym przykładzie, próba odczytania pliku, określając tylko jego lokalizacji. 

Poniżej przedstawiono przykładowy kod:
```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

Przykładowe dane wyjściowe:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale hrabstwa|10171002158| |
|2|ALABAMA|1|101710|Hale hrabstwa|10171002162| |
|3|ALABAMA|1|101710|Hale hrabstwa|10171002156| |
|4|ALABAMA|1|101710|Hale hrabstwa|10171000588|2|

Jest jeden z parametrów, które możesz określić liczbę wierszy do pominięcia w plikach, które podczas odczytu. Użyj poniższego kodu, aby odfiltrować zduplikowany wiersz.
```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

Przykładowe dane wyjściowe:
| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale hrabstwa|10171002158|29|
|1|ALABAMA|1|101710|Hale hrabstwa|10171002162|40 |
|2|ALABAMA|1|101710|Hale hrabstwa|10171002156| 43|
|3|ALABAMA|1|101710|Hale hrabstwa|10171000588|2|
|4|ALABAMA|1|101710|Hale hrabstwa|10171000589|23 |

Następnie możesz obejrzeć typy danych kolumn.
Poniżej przedstawiono przykładowy kod:
```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

Niestety wszystkie nasze kolumny wrócił jako ciągi. Jest to spowodowane Domyślnie program Azure Machine Learning Prep zestawu SDK usługi Data nie zmienia typu danych. Źródła danych, które firma Microsoft podczas odczytu z jest plikiem tekstowym, aby zestaw SDK odczytuje wszystkie wartości jako ciągi. W tym przykładzie chcemy jednak przeanalizować kolumny liczbowe jako liczby. Aby to zrobić, można ustawić parametru inference_arguments do current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Wykryto wiele kolumn poprawnie jako liczby i ustawiono ich typ float64. Za pomocą pozyskiwania gotowe możesz pobrać pandas DataFrame dla pełnego zestawu danych.
Poniżej przedstawiono przykładowy kod:
```python
df = dataflow.to_pandas_dataframe()
df
```

Przykładowe dane wyjściowe:
| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale hrabstwa|1.017100e + 10|49.0|
|1|ALABAMA|Hale hrabstwa|1.017100e + 10|40.0|
|2|ALABAMA|Hale hrabstwa|1.017100e + 10|43.0|
|3|ALABAMA|Hale hrabstwa|1.017100e + 10|2.0|
|4|ALABAMA|Hale hrabstwa|1.017100e + 10|23.0|

## <a name="use-excel-data"></a>Korzystanie z danych programu Excel
Zestaw SDK Azure Machine Learning danych Prep zawiera `read_excel` funkcję, aby załadować pliki programu Excel. Poniżej przedstawiono przykładowy kod:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```

Przykładowe dane wyjściowe:
||Kolumna1|Kolumna2|Kolumna3|Kolumna4|Column5|Kolumna6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Żelaza, IVB|60000000.0|Znaleziono|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19.58333|17.91667|
|1|Prowincja Przylądkowa Jorku|Żelaza, IIIAB|58200000.0|Znaleziono|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76.13333|-64.93333|
|2|Campo del Cielo|Żelaza, IAB MG|50000000.0|Znaleziono|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27.46667|-60.58333|
|3|Canyon Diablo|Żelaza, IAB MG|30000000.0|Znaleziono|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35.05000|-111.03333|
|4|Armanty|Żelaza, IIIE|28000000.0|Znaleziono|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47.00000|88.00000|

Załadowano pierwszy arkusz z pliku programu Excel. Ten sam efekt można osiągnąć, jawnie określając nazwę arkusz, który chcesz załadować. Jeśli chcesz załadować drugi arkusz zamiast tego możesz podać jego nazwę jako argument. Na przykład:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

Przykładowe dane wyjściowe:
||Kolumna1|Kolumna2|Kolumna3|Kolumna4|Column5|Kolumna6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|1|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|2|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|Brak|
|3|Ranga|Stanowisko|Studio|Na całym świecie|Wywiad krajowy / %|Kolumna1|Zbiorowość / %|Kolumna2|Rok ^|
|4|1|Awatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

Jak widać, tabelę w arkuszu drugi ma nagłówki i trzech pustych wierszy. Należy odpowiednio zmodyfikować argumentów funkcji. Na przykład:
```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

Przykładowe dane wyjściowe:
||Ranga|Stanowisko|Studio|Na całym świecie|Wywiad krajowy / %|Kolumna1|Zbiorowość / %|Kolumna2|Rok ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Awatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|
|2|3|Firmy Marvel Avengers|BV|1518.6|623.4|0.41|895.2|0,59|2012|
|3|4|Harry Potter i Deathly Hallows część 2|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|Zamrożone|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="use-fixed-width-data-files"></a>Używanie plików danych stała szerokość
W przypadku plików o stałej szerokości można określić listę przesunięć. Pierwsza kolumna jest zawsze zakłada się, że na przykład rozpoczynają się od przesunięcia 0:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

Przykładowe dane wyjściowe:
||010000|99999|SFAŁSZOWANY NORWEGIA|NIE|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|SFAŁSZOWANY NORWEGIA|NIE|NIE|ENSO||||
|1|010010|99999|JAN MAYEN|NIE|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NIE|NIE|||||
|3|010014|99999|SOERSTOKKEN|NIE|NIE|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NIE|NIE|ENBL|+61383|+005867|+03270|


Brak nagłówków w plikach, należy traktować pierwszy wiersz jako dane. Przekazując `PromoteHeadersMode.NONE` argumentowi — słowo kluczowe nagłówka, możesz uniknąć wykrycia nagłówka i uzyskać prawidłowe dane. Na przykład:
```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```

Przykładowe dane wyjściowe:

||Kolumna1|Kolumna2|Kolumna3|Kolumna4|Column5|Kolumna6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|SFAŁSZOWANY NORWEGIA|NIE|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|SFAŁSZOWANY NORWEGIA|NIE|NIE|ENSO||||
|2|010010|99999|JAN MAYEN|NIE|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NIE|NIE|||||
|4|010014|99999|SOERSTOKKEN|NIE|NIE|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NIE|NIE|ENBL|+61383|+005867|+03270|

## <a name="use-sql-data"></a>Korzystanie z danych SQL
Azure Machine Learning Prep zestawu SDK usługi Data również mogą ładować dane z serwerami programu SQL Server. Aktualnie obsługiwana jest tylko program Microsoft SQL Server.
Do odczytywania danych z programu SQL server, należy utworzyć obiekt źródła danych, który zawiera informacje o połączeniu. Na przykład:
```python
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Jak widać, parametr hasła `MSSQLDataSource` akceptuje obiekt za pomocą klucza tajnego. Obiekt wpisu tajnego można uzyskać na dwa sposoby:
-   Zarejestruj wpisu tajnego i jego wartość przy użyciu aparatu wykonywania. 
-   Utwórz klucz tajny tylko identyfikator (przydatne, jeśli wartość wpisu tajnego jest już zarejestrowany w środowisku wykonawczym).

Po utworzeniu obiektu źródła danych, możesz przejść do odczytywania danych. Na przykład:
```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

Przykładowe dane wyjściowe:
||Identyfikator produktu|Name (Nazwa)|ProductNumber|Kolor|StandardCost|ListPrice|Rozmiar|Waga|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|Thumbnailphoto usługa|ThumbnailPhotoFileName|ROWGUID|Data modyfikacji|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|Rama szosowa HL — czarna, 58|FR — R92B-58|Czarny|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|Rama szosowa HL — czerwona, 58|FR — R92R-58|Czerwony|1059.3100|1431.50|58|1016.04|18|6|2002-06-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Kask Sport-100, czerwony|RAMA U509-R|Czerwony|13.0863|34.99|Brak|Brak|35|33|2005-07-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|
|3|708|Kask Sport-100, czarny|RAMA U509|Czarny|13.0863|34.99|Brak|Brak|35|33|2005-07-01: 00:00:00 + 00:00|Brak|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000 + 00:00|
|4|709|Socks rowerów górski, M|TAK B909-M|Biały|3.3963|9,50|M|Brak|27|18|2005-07-01: 00:00:00 + 00:00|2006-06-30 00:00:00 + 00:00|Brak|b "GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.GIF|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000 + 00:00|

```python
df = dataflow.to_pandas_dataframe()
df.dtypes
```

Przykładowe dane wyjściowe:
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="use-azure-data-lake-storage"></a>Użyj usługi Azure Data Lake Storage
Istnieją dwa sposoby zestawu SDK można pobrać niezbędne tokenu protokołu OAuth do dostępu do usługi Azure Data Lake Storage:
-   Pobieranie tokenu dostępu z najnowszych sesji logowania użytkownika nazwy logowania z wiersza polecenia platformy Azure
-   Użyj nazwy głównej usługi (SP) i certyfikat jako wpis tajny

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Użyj tokenu dostępu z ostatnich sesji wiersza polecenia platformy Azure
Na komputerze lokalnym uruchom następujące polecenie:

> [!NOTE] 
> Jeśli Twoje konto użytkownika jest członkiem więcej niż jednej dzierżawy platformy Azure, należy określić dzierżawy w formie nazwy hosta URL usługi AAD.


Na przykład:
```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Tworzenie jednostki usługi przy użyciu wiersza polecenia platformy Azure
Można użyć wiersza polecenia platformy Azure, aby utworzyć jednostkę usługi i odpowiedni certyfikat. Jednostka usługi określonego skonfigurowano odczytu jego zakres skrócony do tylko konto usługi Azure Data Lake Storage "dpreptestfiles".  Na przykład:
```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
To polecenie generuje `appId` i ścieżkę do pliku certyfikatu (zwykle w folderze głównym). Plik .crt zawiera zarówno certyfikatu publicznego, jak i klucz prywatny w formacie PEM.

Wyodrębnij odcisk palca:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Aby skonfigurować listy ACL do systemu plików usługi Azure Data Lake Storage, użyj identyfikator obiektu użytkownika lub, w tym przykładzie nazwa główna usługi. Na przykład:
```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Aby skonfigurować `Read` i `Execute` dostępu systemu plików usługi Azure Data Lake Storage, musisz oddzielnie skonfigurować listy ACL dla plików i folderów. Jest to z faktu, że odpowiedni model listy ACL systemu plików HDFS nie obsługują dziedziczenia. Na przykład:
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
Użyj `adal` pakietu (za pośrednictwem: `pip install adal`) do tworzenia kontekstu uwierzytelniania w dzierżawie MSFT i uzyskiwanie tokenu dostępu OAuth. Azure Data Lake Store, zasobu w żądania tokenu musi być dla "https://datalake.azure.net", która różni się od najbardziej innych zasobów platformy Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Witryna sieci Web|Ulica|city|Województwo|
|----|------|-----|----|----|----|----|
|0|1012063|Rolników Kaledonia rynek skojarzenie — Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledonia|
|1|1011871|Rynek "rolników Stearns Homestead|http://Stearnshomestead.com |Drogowa ridge 6975|Parma|Cuyahoga|
|2|1011878|Rynek 100 mila|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. głównej ulicy rolników rynku|http://thetownofsixmile.wordpress.com/ |106 S. głównej ulicy|Sześć mila|||
|4|1010691|Firma 10th Steet rolników rynku|http://agrimissouri.com/mo-grown/grodetail.php... |Ulica 10 i topoli|Lamar|Barton|

## <a name="use-smart-reading"></a>Użyj "inteligentne odczytu"

Automatycznie wykrywaj typ pliku przy użyciu funkcji inteligentnego czytania zestawu SDK.
