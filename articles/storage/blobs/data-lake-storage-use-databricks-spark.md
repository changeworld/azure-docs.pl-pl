---
title: 'Samouczek: Uzyskiwanie dostępu do danych usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej za pomocą usługi Azure Databricks i platformy Spark | Microsoft Docs'
description: Z tego samouczka dowiesz się, jak uruchamiać zapytania platformy Spark w klastrze usługi Azure Databricks w celu uzyskania dostępu do danych na koncie magazynu usługi Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: dineshm
ms.openlocfilehash: 533665ebfa3d35ed5f03326cf5614e37056b7713
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813606"
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

* Utwórz konto usługi Azure Data Lake Storage Gen2.

  Zobacz [Tworzenie konta usługi Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Upewnij się, że Twoje konto użytkownika ma przypisaną [rolę Współautor danych obiektu blob magazynu](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

* Zainstaluj narzędzie AzCopy w wersji 10. Zobacz [Transferowanie danych za pomocą narzędzia AzCopy w wersji 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="download-the-flight-data"></a>Pobieranie danych lotów

W tym samouczku w celu zademonstrowania sposobu wykonywania operacji ETL są używane dane dotyczące lotów opracowane przez agencję Bureau of Transportation Statistics. Aby ukończyć samouczek, musisz pobrać te dane.

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Zaznacz pole wyboru **Prezipped file** (Wstępnie spakowany plik), aby zaznaczyć wszystkie pola danych.

3. Wybierz przycisk **Download** (Pobierz) i zapisz wyniki na swoim komputerze. 

4. Rozpakuj zawartość pliku zip i zanotuj nazwę pliku oraz jego ścieżkę. Te informacje będą potrzebne w późniejszym kroku.

## <a name="get-your-storage-account-name"></a>Uzyskiwanie nazwy konta magazynu

Będziesz potrzebować nazwy swojego konta magazynu. Aby ją uzyskać, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/), wybierz pozycję **Wszystkie usługi** i przefiltruj zawartość według terminu *magazyn*. Następnie wybierz pozycję **Konta magazynu** i znajdź swoje konto magazynu.

Wklej nazwę do pliku tekstowego. Wkrótce będziesz jej potrzebować.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Utwórz jednostkę usługi, wykonując czynności opisane w temacie: [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Jest kilka rzeczy, o których należy pamiętać podczas wykonywania kroków przedstawionych w tym artykule.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) tego artykułu, upewnij się, że przypisano aplikację do **roli Współautor usługi Blob Storage**.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Pobieranie wartości podczas logowania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) tego artykułu, wklej identyfikator dzierżawy, identyfikator aplikacji i wartości klucza uwierzytelniania do pliku tekstowego. Wkrótce będą potrzebne.

## <a name="create-an-azure-databricks-service"></a>Tworzenie usługi Azure Databricks

W tej sekcji utworzysz usługę Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Analiza** > **Azure Databricks**.

    ![Usługa Databricks w witrynie Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Usługa Databricks w witrynie Azure Portal")

2. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć usługę Databricks:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.  |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**. Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).       |
    |**Warstwa cenowa**     |  Wybierz opcję **Standardowa**.     |

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Tworzenie usługi Azure Databricks")

3. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

4. Tworzenie konta potrwa kilka minut. Podczas tworzenia konta po prawej stronie portalu jest wyświetlany kafelek **Przesyłanie wdrożenia dla usługi Azure Databricks**. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

    ![Kafelek wdrażania usługi Databricks](./media/data-lake-storage-use-databricks-spark/databricks-deployment-tile.png "Kafelek wdrażania usługi Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Tworzenie klastra Spark w usłudze Azure Databricks

1. W witrynie Azure Portal przejdź do utworzonej usługi Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Usługa Databricks na platformie Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Usługa Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Spark usługi Databricks na platformie Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Tworzenie klastra Spark usługi Databricks na platformie Azure")

4. Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    * Wprowadź nazwę klastra.

    * Na potrzeby tego artykułu utwórz klaster ze środowiskiem uruchomieniowym w wersji **5.1**.

    * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_\_ min nieaktywności**. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest on używany.

    * Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączać do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-file-system-and-mount-it"></a>Tworzenie systemu plików i instalowanie go

W tej sekcji utworzysz system plików i folder na koncie magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonej usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Po lewej stronie wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w usłudze Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Tworzenie notesu w usłudze Databricks")

3. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Python**, a następnie wybierz utworzony wcześniej klaster Spark.

4. Wybierz pozycję **Utwórz**.

5. Skopiuj i wklej następujący blok kodu do pierwszej komórki, ale jeszcze nie uruchamiaj kodu.

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

    Nie zamykaj tego notesu, ponieważ później będziesz jeszcze dodawać do niego polecenia.

## <a name="ingest-data"></a>Pozyskiwanie danych

### <a name="copy-source-data-into-the-storage-account"></a>Kopiowanie danych źródłowych na konto magazynu

Korzystanie z narzędzia AzCopy do kopiowania danych z pliku *csv* na konto usługi Data Lake Storage Gen2.

1. Otwórz okno wiersza polecenia, a następnie wprowadź następujące polecenie, aby zalogować się na swoje konto magazynu.

   ```bash
   azcopy login
   ```

   Wykonaj instrukcje wyświetlane w oknie wiersza polecenia, aby uwierzytelnić swoje konto użytkownika.

2. Aby skopiować dane z pliku *csv*, wprowadź następujące polecenie.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time
   ```
   * Zamień wartość zastępczą `<csv-folder-path>` na ścieżkę do katalogu z plikiem *csv* (bez nazwy pliku).

   * Zastąp wartość symbolu zastępczego `storage-account-name` nazwą konta magazynu.

   * Zastąp symbol zastępczy `file-system-name` dowolną nazwą, którą chcesz nadać systemowi plików.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Konwertowanie formatu CSV na format Parquet za pomocą notesu usługi Databricks

We wcześniej utworzonym notesie dodaj nową komórkę i wklej do niej następujący kod. Zamień wartość zastępczą `storage-account-name` w tym fragmencie kodu na nazwę folderu, w którym został zapisany plik csv.

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time/<your-folder-name>/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
 flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
 print("Done")
 ```

## <a name="explore-data"></a>Eksplorowanie danych

W nowej komórce wklej następujący kod, aby uzyskać listę plików CSV przekazanych za pomocą narzędzia AzCopy. Zamień wartość zastępczą `<csv-folder-path>` na taką samą wartość, jakiej użyto wcześniej dla tej wartości zastępczej.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/On_Time/<your-folder-name>"))
```

Aby utworzyć nowy plik i uzyskać listę plików w folderze *parquet/flights*, uruchom następujący skrypt:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Przy użyciu tego przykładowego kodu możesz eksplorować hierarchię systemu plików HDFS na przykładzie danych przechowywanych na koncie magazynu z włączoną usługą Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Wykonywanie zapytań na danych

Następnie możesz rozpocząć wykonywanie zapytań dotyczących danych przekazanych na swoje konto magazynu. Wprowadź każdy z poniższych bloków kodu w komórce **Cmd 1** i naciśnij klawisze **Cmd + Enter**, aby uruchomić skrypt języka Python.

Aby utworzyć ramki danych dla źródeł danych, uruchom następujący skrypt:

* Zamień wartość zastępczą `<csv-folder-path>` na ścieżkę do katalogu z plikiem *csv* (bez nazwy pliku).

* Zamień wartość symbolu zastępczego `<your-csv-file-name` na nazwę pliku *csv*.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time/<your-folder-name>/<your-csv-file-name>.csv")
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

Wprowadź ten skrypt, aby uruchomić pewne podstawowe zapytania analizy na danych.

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
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta magazynu i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

[!div class="nextstepaction"] 
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)