---
title: 'Tutorial: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft Docs'
description: This tutorial shows how to run Spark queries on an Azure Databricks cluster to access data in an Azure Data Lake Storage Gen2 storage account.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: bbe936fd572a8e23fb6e7c5da4a4bffef1c8bf7e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327538"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Tutorial: Azure Data Lake Storage Gen2, Azure Databricks & Spark

W tym samouczku pokazano, jak połączyć klaster usługi Azure Databricks z danymi przechowywanymi na koncie magazynu platformy Azure z włączoną usługą Azure Data Lake Storage Gen2. Takie połączenie umożliwia natywne wykonywanie w klastrze zapytań i analiz dotyczących tych danych.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie klastra usługi Databricks
> * Pozyskiwanie danych bez struktury na koncie magazynu
> * Uruchamianie analiz dotyczących danych w magazynie obiektów blob

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Data Lake Storage Gen2.

  See [Create an Azure Data Lake Storage Gen2 account](data-lake-storage-quickstart-create-account.md).

* Upewnij się, że Twoje konto użytkownika ma przypisaną [rolę Współautor danych obiektu blob magazynu](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

* Zainstaluj narzędzie AzCopy w wersji 10. Zobacz [Transferowanie danych za pomocą narzędzia AzCopy w wersji 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Tworzenie jednostki usługi. See [How to: Use the portal to create an Azure AD application and service principal that can access resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Jest kilka rzeczy, o których należy pamiętać podczas wykonywania kroków przedstawionych w tym artykule.

  :heavy_check_mark: When performing the steps in the [Assign the application to a role](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) section of the article, make sure to assign the **Storage Blob Data Contributor** role to the service principal.

  > [!IMPORTANT]
  > Upewnij się, że przypisano rolę w zakresie konta magazynu usługi Data Lake Storage Gen2. Możesz przypisać rolę do nadrzędnej grupy zasobów lub subskrypcji, ale będzie zgłaszany błąd dotyczący uprawnień do momentu rozpropagowania przypisań roli do konta magazynu.

  :heavy_check_mark: When performing the steps in the [Get values for signing in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) section of the article, paste the tenant ID, app ID, and password values into a text file. Wkrótce będą potrzebne.

### <a name="download-the-flight-data"></a>Pobieranie danych lotów

W tym samouczku w celu zademonstrowania sposobu wykonywania operacji ETL są używane dane dotyczące lotów opracowane przez agencję Bureau of Transportation Statistics. Aby ukończyć samouczek, musisz pobrać te dane.

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Zaznacz pole wyboru **Prezipped file** (Wstępnie spakowany plik), aby zaznaczyć wszystkie pola danych.

3. Wybierz przycisk **Download** (Pobierz) i zapisz wyniki na swoim komputerze. 

4. Rozpakuj zawartość pliku zip i zanotuj nazwę pliku oraz jego ścieżkę. Te informacje będą potrzebne w późniejszym kroku.

## <a name="create-an-azure-databricks-service"></a>Tworzenie usługi Azure Databricks

W tej sekcji utworzysz usługę Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Analiza** > **Azure Databricks**.

    ![Databricks on Azure portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks on Azure portal")

2. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć usługę Databricks:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.  |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**. Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).       |
    |**Warstwa cenowa**     |  Select **Standard**.     |

    ![Create an Azure Databricks workspace](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Tworzenie usługi Azure Databricks")

3. Tworzenie konta potrwa kilka minut. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Tworzenie klastra Spark w usłudze Azure Databricks

1. W witrynie Azure Portal przejdź do utworzonej usługi Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Databricks on Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks on Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Create Databricks Spark cluster on Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    - Wprowadź nazwę klastra.
     
    - Upewnij się, że pole wyboru **Zakończ po 120 min aktywności** zostało zaznaczone. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.

4. Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączać do niego notesy i uruchamiać zadania Spark.

## <a name="ingest-data"></a>Pobieranie danych

### <a name="copy-source-data-into-the-storage-account"></a>Kopiowanie danych źródłowych na konto magazynu

Korzystanie z narzędzia AzCopy do kopiowania danych z pliku *csv* na konto usługi Data Lake Storage Gen2.

1. Otwórz okno wiersza polecenia, a następnie wprowadź następujące polecenie, aby zalogować się na swoje konto magazynu.

   ```bash
   azcopy login
   ```

   Follow the instructions that appear in the command prompt window to authenticate your user account.

2. Aby skopiować dane z pliku *csv*, wprowadź następujące polecenie.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Replace the `<csv-folder-path>` placeholder value with the path to the *.csv* file.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Replace the `<container-name>` placeholder with any name that you want to give your container.

## <a name="create-a-container-and-mount-it"></a>Create a container and mount it

In this section, you'll create a container and a folder in your storage account.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonej usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Po lewej stronie wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Create a notebook in Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Create notebook in Databricks")

3. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Python**, a następnie wybierz utworzony wcześniej klaster Spark.

4. Wybierz pozycję **Utwórz**.

5. Skopiuj i wklej następujący blok kodu do pierwszej komórki, ale jeszcze nie uruchamiaj kodu.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. W tym bloku kodu zamień symbole zastępcze `appId`, `password`, `tenant` i `storage-account-name` na wartości zebrane podczas wykonywania kroków wymagań wstępnych. Replace the `container-name` placeholder value with the name that you gave to the container on the previous step.

Use these values to replace the mentioned placeholders.

   * Parametry `appId` i `password` pochodzą z aplikacji zarejestrowanej w usłudze Active Directory podczas tworzenia jednostki usługi.

   * Parametr `tenant-id` pochodzi z subskrypcji.

   * Parametr `storage-account-name` to nazwa konta magazynu usługi Azure Data Lake Storage Gen2.

   * Replace the `container-name` placeholder with any name that you want to give your container.

   > [!NOTE]
   > In a production setting, consider storing your password in Azure Databricks. Then, add a look up key to your code block instead of the password. Po zakończeniu tego samouczka Szybki start zobacz artykuł na temat usługi [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) w witrynie internetowej usługi Azure Databricks, aby zapoznać się z przykładami tego podejścia.

19. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

   Nie zamykaj tego notesu, ponieważ później będziesz jeszcze dodawać do niego polecenia.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Konwertowanie formatu CSV na format Parquet za pomocą notesu usługi Databricks

We wcześniej utworzonym notesie dodaj nową komórkę i wklej do niej następujący kod. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Eksplorowanie danych

W nowej komórce wklej następujący kod, aby uzyskać listę plików CSV przekazanych za pomocą narzędzia AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
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

* Replace the `<csv-folder-path>` placeholder value with the path to the *.csv* file.

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Wprowadź ten skrypt, aby uruchomić pewne podstawowe zapytania analizy na danych.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta magazynu i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
