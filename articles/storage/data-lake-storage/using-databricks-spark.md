---
title: Uzyskiwanie dostępu do danych usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej za pomocą usługi Azure Databricks i platformy Spark | Microsoft Docs
description: Dowiedz się, jak uruchamiać zapytania platformy Spark w klastrze usługi Azure Databricks w celu uzyskania dostępu do danych na koncie magazynu usługi Azure Data Lake Storage Gen2.
services: hdinsight,storage
tags: azure-portal
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 42bf3fca0e30c21e5f8737e37f4ac35db188ee90
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616494"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Samouczek: uzyskiwanie dostępu do danych usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej za pomocą usługi Azure Databricks i platformy Spark

Z tego samouczka dowiesz się, jak uruchamiać w klastrze usługi Azure Databricks zapytania platformy Spark dotyczące danych na koncie z możliwością obsługi usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej.

> [!div class="checklist"]
> * Tworzenie klastra usługi Databricks
> * Pozyskiwanie danych bez struktury na koncie magazynu
> * Wyzwalanie funkcji platformy Azure w celu przetwarzania danych
> * Uruchamianie analiz dotyczących danych w magazynie obiektów blob

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono sposób używania danych dotyczących lotów udostępnianych przez [Departament Transportu Stanów Zjednoczonych](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time) i wykonywania na nich zapytań. Pobierz dane dotyczące linii lotniczych obejmujące co najmniej dwa lata (zaznaczając wszystkie pola) i zapisz wyniki na maszynie. Pamiętaj o zanotowaniu nazwy i ścieżki pobranego pliku. Te informacje będą potrzebne później.

> [!NOTE]
> Kliknij pole wyboru **Prezipped file** (Wstępnie spakowany plik), aby zaznaczyć wszystkie pola danych. Pobrane dane będą miały rozmiar wielu gigabajtów, ale taka ilość danych jest potrzebna do analizy.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Tworzenie konta usługi Azure Data Lake Storage 2. generacji

Aby rozpocząć, utwórz nowe [konto usługi Azure Data Lake Storage 2. generacji](quickstart-create-account.md) i nadaj mu unikatową nazwę. Następnie przejdź do tego konta magazynu i pobierz ustawienia konfiguracji.

> [!IMPORTANT]
> W wersji zapoznawczej funkcje Azure Functions działają tylko z kontami usługi Azure Data Lake Storage 2. generacji utworzonymi przy użyciu płaskiego obszaru nazw.

1. W obszarze **Ustawienia** kliknij pozycję **Klucze dostępu**.
3. Kliknij przycisk **Kopiuj** obok pozycji **klucz1**, aby skopiować wartość klucza.

Nazwa konta i klucz są wymagane do wykonania kolejnych kroków w tym samouczku. Otwórz edytor tekstu i zapisz nazwę konta oraz klucz do późniejszego użycia.

## <a name="create-a-databricks-cluster"></a>Tworzenie klastra usługi Databricks

Następnym krokiem jest utworzenie [klastra usługi Databricks](https://docs.azuredatabricks.net/) jako obszaru roboczego danych.

1. Utwórz wystąpienie [usługi Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) i nadaj mu nazwę **myFlightDataService** (pamiętaj o zaznaczeniu pola wyboru *Przypnij do pulpitu nawigacyjnego* podczas tworzenia usługi).
2. Kliknij pozycję **Uruchom obszar roboczy**, aby otworzyć obszar roboczy w nowym oknie przeglądarki.
3. Kliknij pozycję **Klastry** na pasku nawigacji po lewej stronie.
4. Kliknij pozycję **Utwórz klaster**.
5. Wpisz **myFlightDataCluster** w polu *Nazwa klastra*.
6. Wybierz pozycję **Standardowa_D8s_v3** w polu *Typ procesu roboczego*.
7. Zmień wartość **Minimalna liczba procesów roboczych** na *4*.
8. Kliknij przycisk **Utwórz klaster** w górnej części strony (ukończenie tego procesu może potrwać do 5 minut).
9. Po zakończeniu procesu wybierz pozycję **Azure Databricks** w lewym górnym rogu paska nawigacji.
10. Wybierz pozycję **Notes** w sekcji **Nowy** w dolnej części strony.
11. Wprowadź wybraną nazwę w polu **Nazwa**, a następnie wybierz język **Python**.
12. Możesz pozostawić wartości domyślne we wszystkich pozostałych polach.
13. Wybierz pozycję **Utwórz**.
14. Wklej poniższy kod w komórce **Cmd 1**, zastępując odpowiednie wartości zapisanymi wartościami dotyczącymi konta magazynu.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Pozyskiwanie danych

### <a name="copy-source-data-into-the-storage-account"></a>Kopiowanie danych źródłowych na konto magazynu

Następnym zadaniem jest skopiowanie danych z pliku *csv* do usługi Azure Storage za pomocą narzędzia AzCopy. Otwórz okno wiersza polecenia i wpisz następujące polecenia. Pamiętaj o zastąpieniu symboli zastępczych `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` i `<ACCOUNT_KEY>` odpowiednimi wartościami zapisanymi w poprzednim kroku.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Konwertowanie formatu CSV na format Parquet za pomocą notesu usługi Databricks

W przeglądarce otwórz ponownie usługę Databricks i wykonaj następujące kroki:

1. Wybierz pozycję **Azure Databricks** w lewym górnym rogu paska nawigacji.
2. Wybierz pozycję **Notes** w sekcji **Nowy** w dolnej części strony.
3. Wpisz **CSV2Parquet** w polu **Nazwa**.
4. Możesz pozostawić wartości domyślne we wszystkich pozostałych polach.
5. Wybierz pozycję **Utwórz**.
6. Wklej następujący kod w komórce **Cmd 1** (ten kod jest zapisywany automatycznie w edytorze).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Eksplorowanie danych przy użyciu rozproszonego systemu plików usługi Hadoop

Wróć do obszaru roboczego usługi Databricks i kliknij ikonę **Ostatnie** na pasku nawigacji po lewej stronie.

1. Kliknij notes **Flight Data Analytics**.
2. Naciśnij klawisze **Ctrl + Alt + N**, aby utworzyć nową komórkę.

Wprowadź każdy z poniższych bloków kodu w komórce **Cmd 1** i naciśnij klawisze **Cmd + Enter**, aby uruchomić skrypt języka Python.

Aby uzyskać listę plików CSV przekazanych za pomocą narzędzia AzCopy, uruchom następujący skrypt:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Aby utworzyć nowy plik i uzyskać listę plików w folderze *parquet/flights*, uruchom następujący skrypt:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Przy użyciu tego przykładowego kodu możesz eksplorować hierarchię systemu plików HDFS na przykładzie danych przechowywanych na koncie usługi Azure Data Lake Storage 2. generacji.

## <a name="query-the-data"></a>Wykonywanie zapytań na danych

Następnie możesz rozpocząć wykonywanie zapytań dotyczących danych przekazanych do magazynu usługi Azure Data Lake. Wprowadź każdy z poniższych bloków kodu w komórce **Cmd 1** i naciśnij klawisze **Cmd + Enter**, aby uruchomić skrypt języka Python.

### <a name="simple-queries"></a>Proste zapytania

Aby utworzyć ramki danych dla źródeł danych, uruchom następujący skrypt:

> [!IMPORTANT]
> Pamiętaj o zastąpieniu symbolu zastępczego **<YOUR_CSV_FILE_NAME>** nazwą pliku pobranego na początku pracy z tym samouczkiem.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferrably run this in a separate cmd cell
display(flightDF)
```

Aby wykonywać zapytania w celu analizy danych, uruchom następujący skrypt:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```
### <a name="complex-queries"></a>Złożone zapytania

Aby wykonać następujące, bardziej złożone zapytania, uruchom kolejno poszczególne segmenty w notesie i sprawdź wyniki.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="next-steps"></a>Następne kroki

* [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](tutorial-extract-transform-load-hive.md)