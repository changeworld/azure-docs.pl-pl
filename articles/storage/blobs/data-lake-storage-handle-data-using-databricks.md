---
title: 'Samouczek: Dowiedz się, jak wykonywać operacje wyodrębniania, ładowania i przekształcania danych przy użyciu usługi Azure Databricks'
description: W ramach tego samouczka dowiesz się, jak wyodrębniać dane z usługi Azure Data Lake Storage Gen2 w wersji zapoznawczej do usługi Azure Databricks, przekształcać je, a następnie ładować do usługi Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0bb2e9a91890f88466b27439b55d516848fd2270
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438832"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks

W ramach tego samouczka wykonasz operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks. Wyodrębnisz dane z usługi Azure Data Lake Storage Gen2 do usługi Azure Databricks, uruchomisz przekształcenia danych w usłudze Azure Databricks, a następnie załadujesz przekształcone dane do usługi Azure SQL Data Warehouse.

W procedurach opisanych w tym samouczku do przesyłania danych do usługi Azure Databricks służy łącznik SQL Data Warehouse dla usługi Azure Databricks. Ten łącznik z kolei używa usługi Azure Blob Storage jako magazynu tymczasowego dla danych przesyłanych między klastrem usługi Azure Databricks a usługą Azure SQL Data Warehouse.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks.
> * Tworzenie klastra Spark w usłudze Azure Databricks.
> * Tworzenie systemu plików i przekazywanie danych do usługi Azure Data Lake Storage Gen2.
> * Tworzenie jednostki usługi.
> * Wyodrębnianie danych z usługi Data Lake Store.
> * Przekształcanie danych w usłudze Azure Databricks.
> * Ładowanie danych do usługi Azure SQL Data Warehouse.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

> [!div class="checklist"]
> * Utwórz magazyn danych Azure SQL Data Warehouse, utwórz regułę zapory na poziomie serwera i nawiąż połączenie z serwerem jako administrator serwera. Zobacz [Szybki start: tworzenie bazy danych w usłudze Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md).
> * Utwórz klucz główny bazy danych dla magazynu danych Azure SQL Data Warehouse. Zobacz [Tworzenie klucza głównego bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
> * Utwórz konto usługi Azure Data Lake Storage Gen2. Zobacz [Tworzenie konta usługi Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
> * Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Analiza** > **Azure Databricks**.

    ![Usługa Databricks w witrynie Azure Portal](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Usługa Databricks w witrynie Azure Portal")

1. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć obszar roboczy usługi Databricks:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**.        |
    |**Warstwa cenowa**     |  Wybierz opcję **Standardowa**.     |

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

1. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

1. Tworzenie konta potrwa kilka minut. Podczas tworzenia konta po prawej stronie portalu jest wyświetlany kafelek **Przesyłanie wdrożenia dla usługi Azure Databricks**. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

    ![Kafelek wdrażania usługi Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Kafelek wdrażania usługi Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Tworzenie klastra Spark w usłudze Azure Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Usługa Databricks na platformie Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Usługa Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Spark usługi Databricks na platformie Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Tworzenie klastra Spark usługi Databricks na platformie Azure")

4. Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    * Wprowadź nazwę klastra.

    * Na potrzeby tego artykułu utwórz klaster ze środowiskiem uruchomieniowym w wersji **5.1**.

    * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_\_ min nieaktywności**. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest on używany.

    * Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączać do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-file-system-and-upload-sample-data"></a>Tworzenie systemu plików i przekazywanie danych przykładowych

Najpierw należy utworzyć system plików na koncie usługi Data Lake Storage Gen2. Następnie można przekazać przykładowy plik danych do usługi Data Lake Store. Później użyjesz tego pliku w usłudze Azure Databricks do uruchomienia niektórych przekształceń.

1. Pobierz przykładowy plik danych [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) do lokalnego systemu plików.

2. Z witryny [Azure Portal](https://portal.azure.com/) przejdź do konta usługi Data Lake Storage Gen2, które zostało utworzone w ramach spełniania warunków wstępnych do tego samouczka.

3. Na stronie **Przegląd** konta magazynu wybierz pozycję **Otwórz w programie Explorer**.

   ![Otwieranie Eksploratora usługi Storage](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer.png "Otwieranie Eksploratora usługi Storage")

4. Wybierz pozycję **Otwórz Eksploratora usługi Azure Storage**, aby otworzyć Eksploratora usługi Storage.

   ![Drugi monit o otworzenie Eksploratora usługi Storage](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer-2.png "Drugi monit o otworzenie Eksploratora usługi Storage")

   Zostanie otwarty Eksplorator usługi Storage. Możesz utworzyć system plików i przekazać dane przykładowe, korzystając ze wskazówek zawartych w tym temacie: [Szybki start: zarządzanie danymi na koncie usługi Azure Data Lake Storage Gen2 przy użyciu Eksploratora usługi Azure Storage](data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Utwórz jednostkę usługi, wykonując czynności opisane w temacie: [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Istnieje kilka określonych czynności, o których należy pamiętać podczas wykonywania instrukcji przedstawionych w tym artykule.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Tworzenie aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) tego artykułu, należy ustawić pole **Adres URL logowania** w oknie dialogowym **Tworzenie** na uzyskany uprzednio identyfikator URI punktu końcowego.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) tego artykułu, upewnij się, że przypisano aplikację do **roli Współautor usługi Blob Storage**.

:heavy_check_mark: Wykonując kroki opisane w sekcji [Pobieranie wartości podczas logowania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) tego artykułu, wklej identyfikator dzierżawy, identyfikator aplikacji i wartości klucza uwierzytelniania do pliku tekstowego. Wkrótce będą potrzebne.
Najpierw utworzysz notes w obszarze roboczym usługi Azure Databricks, a następnie uruchomisz fragmenty kodu, aby utworzyć system plików w ramach konta magazynu.

## <a name="extract-data-from-the-data-lake-store"></a>Wyodrębnianie danych z usługi Data Lake Store

W tej sekcji utworzysz notes w obszarze roboczym usługi Azure Databricks, a następnie uruchomisz fragmenty kodu, aby wyodrębnić dane z usługi Data Lake Store do usługi Azure Databricks.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonego obszaru roboczego usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Po lewej stronie wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w usłudze Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "Tworzenie notesu w usłudze Databricks")

3. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Scala**, a następnie wybierz utworzony wcześniej klaster Spark.

    ![Podawanie szczegółów dotyczących notesu w usłudze Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "Podawanie szczegółów dotyczących notesu w usłudze Databricks")

4. Wybierz pozycję **Utwórz**.

5. Skopiuj i wklej następujący blok kodu do pierwszej komórki.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

5. W tym bloku kodu zastąp symbole zastępcze `application-id`, `authentication-id` i `tenant-id` wartościami uzyskanymi podczas wykonywania kroków opisanych w sekcji [Zapisywanie konfiguracji konta magazynu na później](#config). Zastąp wartość symbolu zastępczego `storage-account-name` nazwą konta magazynu.

6. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

7. Teraz możesz załadować przykładowy plik json jako ramkę danych w usłudze Azure Databricks. Wklej następujący kod w nowej komórce. Zastąp symbole zastępcze umieszczone w nawiasach ostrokątnych własnymi wartościami.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Zastąp wartość symbolu zastępczego `file-system-name` nazwą nadaną systemowi plików w Eksploratorze usługi Storage.

   * Zastąp symbol zastępczy `storage-account-name` nazwą konta magazynu.

8. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

9. Uruchom poniższy kod, aby wyświetlić zawartość ramki danych:

    ```scala
    df.show()
    ```
   Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

   ```bash
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Masz teraz dane wyodrębnione z usługi Azure Data Lake Storage 2. generacji do usługi Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Przekształcanie danych w usłudze Azure Databricks

Plik przykładowych danych pierwotnych, **small_radio_json.json**, przechwytuje odbiorców stacji radiowej i ma wiele kolumn. W tej sekcji przekształcisz dane tak, aby z zestawu danych były pobierane tylko określone kolumny.

1. Zacznij od pobrania tylko kolumn **firstName**, **lastName**, **gender**, **location** i **level** z utworzonej ramki danych.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Otrzymasz dane wyjściowe pokazane w poniższym fragmencie kodu:

   ```bash
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Możesz dalej przekształcać te dane, aby zmienić nazwę kolumny **level** na **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Otrzymasz dane wyjściowe pokazane w poniższym fragmencie kodu.

   ```bash
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse

W tej sekcji przekażesz przekształcone dane do magazynu danych Azure SQL Data Warehouse. Używając łącznika usługi Azure SQL Data Warehouse dla usługi Azure Databricks, bezpośrednio przekażesz ramkę danych jako tabelę w usłudze SQL Data Warehouse.

Łącznik usługi SQL Data Warehouse korzysta z usługi Azure Blob Storage jako magazynu tymczasowego do przekazywania danych między usługami Azure Databricks i Azure SQL Data Warehouse. Możesz rozpocząć od podania konfiguracji umożliwiającej nawiązanie połączenia z kontem magazynu. Musisz już mieć utworzone konto w ramach wymagań wstępnych dotyczących tego artykułu.

1. Podaj konfigurację umożliwiającą uzyskanie dostępu do konta usługi Azure Storage z usługi Azure Databricks.

   ```scala
   val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
   val fileSystemName = "<FILE_SYSTEM_NAME>"
   val accessKey =  "<ACCESS_KEY>"
   ```

2. Określ folder tymczasowy do użycia podczas przenoszenia danych między usługami Azure Databricks i Azure SQL Data Warehouse.

   ```scala
   val tempDir = "abfss://" + fileSystemName + "@" + storageURI +"/tempDirs"
   ```

3. Uruchom poniższy fragment kodu, aby zapisać klucze dostępu usługi Azure Blob Storage w konfiguracji. Dzięki tej akcji nie będzie trzeba przechowywać klucza dostępu w notesie w postaci zwykłego tekstu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ storageURI
   sc.hadoopConfiguration.set(acntInfo, accessKey)
   ```

4. Podaj wartości, aby nawiązać połączenie z wystąpieniem usługi Azure SQL Data Warehouse. Musisz mieć magazyn danych SQL Data Warehouse utworzony w ramach wymagań wstępnych.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<DATABASE NAME>"
   val dwServer = "<DATABASE SERVER NAME>" 
   val dwUser = "<USER NAME>"
   val dwPass = "<PASSWORD>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Uruchom poniższy fragment kodu, aby załadować przekształconą ramkę danych **renamedColumnsDF** jako tabelę w magazynie danych SQL Data Warehouse. Ten fragment kodu tworzy tabelę o nazwie **SampleTable** w bazie danych SQL.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write
       .format("com.databricks.spark.sqldw")
       .option("url", sqlDwUrlSmall) 
       .option("dbtable", "SampleTable")
       .option( "forward_spark_azure_storage_credentials","True")
       .option("tempdir", tempDir)
       .mode("overwrite")
       .save()
   ```

6. Połącz się z usługą SQL Database i sprawdź, czy jest widoczna baza danych o nazwie **SampleTable**.

   ![Sprawdzanie przykładowej tabeli](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "Sprawdzanie przykładowej tabeli")

7. Uruchom wybrane zapytanie, aby sprawdzić zawartość tabeli. Tabela powinna zawierać takie same dane jak ramka danych **renamedColumnsDF**.

    ![Sprawdzanie zawartości przykładowej tabeli](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "Sprawdzanie zawartości przykładowej tabeli")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz przerwać działanie klastra. W obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** po lewej stronie. Aby przerwać działanie klastra, w obszarze **Akcje** wskaż wielokropek (...) i wybierz ikonę **Przerwij**.

![Zatrzymywanie klastra usługi Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Zatrzymywanie klastra usługi Databricks")

Jeśli nie przerwiesz działania klastra ręcznie, zostanie on automatycznie zatrzymany, o ile podczas tworzenia klastra zaznaczono pole wyboru **Zakończ po \_\_ min nieaktywności**. W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak przesyłać strumieniowo dane w czasie rzeczywistym do usługi Azure Databricks przy użyciu usługi Azure Event Hubs.

> [!div class="nextstepaction"]
>[Przesyłanie strumieniowe danych do usługi Azure Databricks przy użyciu usługi Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
