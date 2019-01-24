---
title: 'Samouczek: Uzyskiwanie dostępu do danych usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej za pomocą usługi Azure Databricks i platformy Spark | Microsoft Docs'
description: Z tego samouczka dowiesz się, jak uruchamiać zapytania platformy Spark w klastrze usługi Azure Databricks w celu uzyskania dostępu do danych na koncie magazynu usługi Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: dineshm
ms.openlocfilehash: e72a4f71a42a892d14fad076b124426f0c32ac7d
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321810"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Samouczek: uzyskiwanie dostępu do danych usługi Data Lake Storage Gen2 w wersji zapoznawczej za pomocą usługi Azure Databricks i platformy Spark

W tym samouczku pokazano, jak połączyć klaster usługi Azure Databricks z danymi przechowywanymi na koncie magazynu platformy Azure z włączoną usługą Azure Data Lake Storage Gen2 w wersji zapoznawczej. Takie połączenie umożliwia natywne wykonywanie w klastrze zapytań i analiz dotyczących tych danych.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie klastra usługi Databricks
> * Pozyskiwanie danych bez struktury na koncie magazynu
> * Uruchamianie analiz dotyczących danych w magazynie obiektów blob

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przedstawiono sposób używania danych dotyczących lotów udostępnianych przez [Departament Transportu Stanów Zjednoczonych](https://transtats.bts.gov/DL_SelectFields.asp) i wykonywania na nich zapytań. 

1. Zaznacz pole wyboru **Prezipped file** (Wstępnie spakowany plik), aby wybrać wszystkie pola danych.
2. Wybierz pozycję **Download** (Pobierz) i zapisz wyniki na swojej maszynie.
3. Zanotuj nazwę i ścieżkę pobranego pliku. Te informacje będą potrzebne później.

Do pracy z tym samouczkiem potrzebujesz konta magazynu z funkcjami analizy. Aby utworzyć to konto, zalecamy ukończenie [przewodnika szybki start](data-lake-storage-quickstart-create-account.md) dotyczącego tego tematu. 

## <a name="set-aside-storage-account-configuration"></a>Zapisywanie konfiguracji konta magazynu na później

Potrzebna będzie nazwa konta magazynu i identyfikator URI punktu końcowego systemu plików.

Aby uzyskać nazwę konta magazynu w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi** i filtruj listę według terminu *Magazyn*. Następnie wybierz pozycję **Konta magazynu** i znajdź swoje konto magazynu.

Aby uzyskać identyfikator URI punktu końcowego systemu plików, wybierz pozycję **Właściwości**, a w okienku właściwości znajdź wartość pola **Podstawowy punkt końcowy systemu plików usługi ADLS**.

Wklej obie te wartości do pliku tekstowego. Wkrótce będą potrzebne.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Utwórz jednostkę usługi, wykonując czynności opisane w temacie: [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Istnieje kilka określonych czynności, o których należy pamiętać podczas wykonywania instrukcji przedstawionych w tym artykule.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Tworzenie aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) tego artykułu, należy ustawić pole **Adres URL logowania** w oknie dialogowym **Tworzenie** na uzyskany uprzednio identyfikator URI punktu końcowego.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) tego artykułu, upewnij się, że przypisano aplikację do **roli Współautor usługi Blob Storage**.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Pobieranie wartości podczas logowania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) tego artykułu, wklej identyfikator dzierżawy, identyfikator aplikacji i wartości klucza uwierzytelniania do pliku tekstowego. Wkrótce będą potrzebne.

## <a name="create-a-databricks-cluster"></a>Tworzenie klastra usługi Databricks

Następnym krokiem jest utworzenie klastra usługi Databricks jako obszaru roboczego danych.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz zasób**.
2. Wprowadź ciąg **Azure Databricks** w polu wyszukiwania.
3. Wybierz pozycję **Utwórz** w bloku usługi Azure Databricks.
4. Nadaj wystąpieniu usługi Databricks nazwę **myFlightDataService** (pamiętaj o zaznaczeniu pola wyboru *Przypnij do pulpitu nawigacyjnego* podczas tworzenia usługi).
5. Wybierz pozycję **Uruchom obszar roboczy**, aby otworzyć obszar roboczy w nowym oknie przeglądarki.
6. Wybierz pozycję **Klastry** na pasku nawigacyjnym po lewej stronie.
7. Wybierz pozycję **Utwórz klaster**.
8. Wpisz **myFlightDataCluster** w polu **Nazwa klastra**.
9. Wybierz pozycję **Standardowa_D8s_v3** w polu **Typ procesu roboczego**.
10. Zmień wartość **Minimalna liczba procesów roboczych** na **4**.
11. Wybierz pozycję **Utwórz klaster** w górnej części strony. Ukończenie tego procesu może zająć do 5 minut.
12. Po zakończeniu procesu wybierz pozycję **Azure Databricks** w lewym górnym rogu paska nawigacji.
13. Wybierz pozycję **Notes** w sekcji **Nowy** w dolnej części strony.
14. Wprowadź wybraną nazwę w polu **Nazwa**, a następnie wybierz język **Python**.
15. Możesz pozostawić wartości domyślne we wszystkich pozostałych polach.
16. Wybierz pozycję **Utwórz**.
17. Skopiuj i wklej następujący blok kodu do pierwszej komórki, ale jeszcze nie uruchamiaj kodu.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```
18. W tym bloku kodu zastąp symbole zastępcze `storage-account-name`, `application-id`, `authentication-id` i `tenant-id` wartościami uzyskanymi podczas wykonywaniu kroków opisanych w sekcjach [Zapisywanie konfiguracji konta magazynu na później](#config) i [Tworzenie jednostki usługi](#service-principal) tego artykułu. Zastąp symbol zastępczy `file-system-name` dowolną nazwą, którą chcesz nadać systemowi plików.

19. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

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
6. Wklej następujący kod do komórki **Cmd 1**. Ten kod jest automatycznie zapisywany w edytorze.

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Eksplorowanie danych

Wróć do obszaru roboczego usługi Databricks i wybierz ikonę **Ostatnie** na pasku nawigacji po lewej stronie.

1. Wybierz notes **Flight Data Analytics**.
2. Naciśnij klawisze **Ctrl + Alt + N**, aby utworzyć nową komórkę.

Wprowadź każdy z poniższych bloków kodu w komórce **Cmd 1** i naciśnij klawisze **Cmd + Enter**, aby uruchomić skrypt języka Python.

Aby uzyskać listę plików CSV przekazanych za pomocą narzędzia AzCopy, uruchom następujący skrypt:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Aby utworzyć nowy plik i uzyskać listę plików w folderze *parquet/flights*, uruchom następujący skrypt:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Przy użyciu tego przykładowego kodu możesz eksplorować hierarchię systemu plików HDFS na przykładzie danych przechowywanych na koncie magazynu z włączoną usługą Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Wykonywanie zapytań na danych

Następnie możesz rozpocząć wykonywanie zapytań dotyczących danych przekazanych na swoje konto magazynu. Wprowadź każdy z poniższych bloków kodu w komórce **Cmd 1** i naciśnij klawisze **Cmd + Enter**, aby uruchomić skrypt języka Python.

### <a name="run-simple-queries"></a>Uruchamianie prostych zapytań

Aby utworzyć ramki danych dla źródeł danych, uruchom następujący skrypt:

> [!IMPORTANT]
> Pamiętaj o zastąpieniu symbolu zastępczego **<YOUR_CSV_FILE_NAME>** nazwą pliku pobranego na początku pracy z tym samouczkiem.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

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
#preferably run this in a separate cmd cell
display(flightDF)
```

Aby wykonywać zapytania w celu analizy danych, uruchom następujący skrypt:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
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

### <a name="run-complex-queries"></a>Uruchamianie złożonych zapytań

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

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta magazynu i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

[!div class="nextstepaction"] 
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)

