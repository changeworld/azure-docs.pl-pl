---
title: 'Samouczek: Wykonywanie operacji ETL za pomocą usługi Azure Databricks'
description: Dowiedz się, jak wyodrębniać dane z usługi Data Lake Storage Gen2 do usługi Azure Databricks, przekształcać je, a następnie załadować do usługi Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 02/15/2019
ms.openlocfilehash: e306245da2c76560ad447358fa1a57e491c370ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239349"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks

W ramach tego samouczka wykonasz operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks. Wyodrębnisz dane z usługi Azure Data Lake Storage Gen2 do usługi Azure Databricks, uruchomisz przekształcenia danych w usłudze Azure Databricks, a następnie załadujesz przekształcone dane do usługi Azure SQL Data Warehouse.

W procedurach opisanych w tym samouczku do przesyłania danych do usługi Azure Databricks służy łącznik SQL Data Warehouse dla usługi Azure Databricks. Ten łącznik z kolei używa usługi Azure Blob Storage jako magazynu tymczasowego dla danych przesyłanych między klastrem usługi Azure Databricks a usługą Azure SQL Data Warehouse.

Poniższa ilustracja przedstawia przepływ aplikacji:

![Usługa Azure Databricks z usługami Data Lake Store i SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Usługa Azure Databricks z usługami Data Lake Store i SQL Data Warehouse")

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie usługi Azure Databricks.
> * Tworzenie klastra Spark w usłudze Azure Databricks.
> * Tworzenie systemu plików na koncie usługi Data Lake Storage Gen2.
> * Przekazywanie danych przykładowych na konto usługi Azure Data Lake Storage Gen2.
> * Tworzenie jednostki usługi.
> * Wyodrębnianie danych z konta usługi Azure Data Lake Storage Gen2.
> * Przekształcanie danych w usłudze Azure Databricks.
> * Ładowanie danych do usługi Azure SQL Data Warehouse.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!Note]
> W tym samouczku nie może być przeprowadzone przy użyciu **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Aby użyć bezpłatnego konta do utworzenia klastra usługi Azure Databricks, przed utworzeniem klastra przejdź do swojego profilu i zmień swoją subskrypcję na **płatność zgodnie z rzeczywistym użyciem**. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
     
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj następujące zadania:

* Utwórz magazyn danych Azure SQL Data Warehouse, utwórz regułę zapory na poziomie serwera i nawiąż połączenie z serwerem jako administrator serwera. Zobacz [Szybki start: tworzenie bazy danych w usłudze Azure SQL Data Warehouse](../sql-data-warehouse/create-data-warehouse-portal.md).

* Utwórz klucz główny bazy danych dla magazynu danych Azure SQL Data Warehouse. Zobacz [Tworzenie klucza głównego bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Utwórz konto usługi Azure Blob Storage i zawarty w nim kontener. Ponadto pobierz klucz dostępu, aby uzyskać dostęp do konta magazynu. Zobacz [Szybki start: tworzenie konta usługi Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-portal.md).

* Utwórz konto usługi Azure Data Lake Storage Gen2. Zobacz [Tworzenie konta usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

*  Tworzenie jednostki usługi. Zobacz [Instrukcje: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Jest kilka rzeczy, o których należy pamiętać podczas wykonywania kroków przedstawionych w tym artykule.

   * Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) tego artykułu, upewnij się, że przypisano rolę **Współautor danych obiektu blob magazynu** do jednostki usługi.

     > [!IMPORTANT]
     > Upewnij się, że przypisano rolę w zakresie konta magazynu usługi Data Lake Storage Gen2. Możesz przypisać rolę do nadrzędnej grupy zasobów lub subskrypcji, ale będzie zgłaszany błąd dotyczący uprawnień do momentu rozpropagowania przypisań roli do konta magazynu.

   * Wykonując kroki opisane w sekcji [Pobieranie wartości podczas logowania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) tego artykułu, wklej identyfikator dzierżawy, identyfikator aplikacji i wartości klucza uwierzytelniania do pliku tekstowego. Wkrótce będą potrzebne.

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Zbieranie potrzebnych informacji

Upewnij się, że zostały spełnione wszystkie wymagania wstępne tego samouczka.

   Przed rozpoczęciem należy zebrać następujące informacje:

   :heavy_check_mark:  Nazwa bazy danych, nazwa serwera baz danych, nazwa użytkownika i hasło magazynu Azure SQL Data Warehouse.

   :heavy_check_mark:  Klucz dostępu konta usługi Blob Storage.

   :heavy_check_mark:  Nazwa konta magazynu usługi Data Lake Storage Gen2.

   :heavy_check_mark:  Identyfikator dzierżawy subskrypcji.

   :heavy_check_mark:  Identyfikator aplikacji, która została zarejestrowana w usłudze Azure Active Directory (Azure AD).

   :heavy_check_mark:  Klucz uwierzytelniania aplikacji, która została zarejestrowana w usłudze Azure AD.

## <a name="create-an-azure-databricks-service"></a>Tworzenie usługi Azure Databricks

W tej sekcji utworzysz usługę Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Analiza** > **Azure Databricks**.

    ![Usługa Databricks w witrynie Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Usługa Databricks w witrynie Azure Portal")

2. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć usługę Databricks:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**.  Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).      |
    |**Warstwa cenowa**     |  Wybierz opcję **Standardowa**.     |

3. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

4. Tworzenie konta potrwa kilka minut. Podczas tworzenia konta po prawej stronie portalu jest wyświetlany kafelek **Przesyłanie wdrożenia dla usługi Azure Databricks**. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

    ![Kafelek wdrażania usługi Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Kafelek wdrażania usługi Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Tworzenie klastra Spark w usłudze Azure Databricks

1. W witrynie Azure Portal przejdź do utworzonej usługi Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Usługa Databricks na platformie Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Usługa Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Spark usługi Databricks na platformie Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Tworzenie klastra Spark usługi Databricks na platformie Azure")

4. Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    * Wprowadź nazwę klastra.

    * Na potrzeby tego artykułu utwórz klaster ze środowiskiem uruchomieniowym w wersji **5.1**.

    * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_\_ min nieaktywności**. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest on używany.

    * Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączać do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Tworzenie systemu plików na koncie usługi Azure Data Lake Storage Gen2

W tej sekcji utworzysz notes w obszarze roboczym usługi Azure Databricks, a następnie uruchomisz fragmenty kodu, aby skonfigurować konto magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonej usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Po lewej stronie wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w usłudze Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Tworzenie notesu w usłudze Databricks")

3. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Scala**, a następnie wybierz utworzony wcześniej klaster Spark.

    ![Podawanie szczegółów dotyczących notesu w usłudze Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Podawanie szczegółów dotyczących notesu w usłudze Databricks")

4. Wybierz pozycję **Utwórz**.

5. Skopiuj i wklej następujący blok kodu do pierwszej komórki.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. W tym bloku kodu zamień symbole zastępcze `application-id`, `authentication-id`, `tenant-id` i `storage-account-name` na wartości zebrane podczas wykonywania kroków wymagań wstępnych. Zastąp symbol zastępczy `file-system-name` dowolną nazwą, którą chcesz nadać systemowi plików.

   * Parametry `application-id` i `authentication-id` pochodzą z aplikacji zarejestrowanej w usłudze Active Directory podczas tworzenia jednostki usługi.

   * Parametr `tenant-id` pochodzi z subskrypcji.

   * Parametr `storage-account-name` to nazwa konta magazynu usługi Azure Data Lake Storage Gen2.

7. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Pozyskiwanie danych przykładowych na konto usługi Azure Data Lake Storage Gen2

Przed przystąpieniem do pracy z tą sekcją musisz zapewnić spełnienie następujących wymagań wstępnych:

Wprowadź następujący kod w komórce notesu:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

W tej komórce naciśnij klawisze **SHIFT + ENTER**, aby uruchomić kod.

Teraz w nowej komórce pod tą komórką wprowadź następujący kod, zastępując wartości w nawiasach tymi samymi wartościami, których użyto wcześniej:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

W tej komórce naciśnij klawisze **SHIFT + ENTER**, aby uruchomić kod.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Wyodrębnianie danych z konta usługi Azure Data Lake Storage Gen2

1. Teraz możesz załadować przykładowy plik json jako ramkę danych w usłudze Azure Databricks. Wklej następujący kod w nowej komórce. Zastąp symbole zastępcze umieszczone w nawiasach ostrokątnych własnymi wartościami.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Zastąp wartość symbolu zastępczego `file-system-name` nazwą nadaną systemowi plików w Eksploratorze usługi Storage.

   * Zastąp symbol zastępczy `storage-account-name` nazwą konta magazynu.

2. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

3. Uruchom poniższy kod, aby wyświetlić zawartość ramki danych:

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

Jak wspomniano wcześniej, łącznik magazynu danych SQL korzysta z usługi Azure Blob Storage jako magazynu tymczasowego do przekazywania danych między usługami Azure Databricks i Azure SQL Data Warehouse. Możesz rozpocząć od podania konfiguracji umożliwiającej nawiązanie połączenia z kontem magazynu. Musisz już mieć utworzone konto w ramach wymagań wstępnych dotyczących tego artykułu.

1. Podaj konfigurację umożliwiającą uzyskanie dostępu do konta usługi Azure Storage z usługi Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Określ folder tymczasowy do użycia podczas przenoszenia danych między usługami Azure Databricks i Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Uruchom poniższy fragment kodu, aby zapisać klucze dostępu usługi Azure Blob Storage w konfiguracji. Dzięki tej akcji nie będzie trzeba przechowywać klucza dostępu w notesie w postaci zwykłego tekstu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Podaj wartości, aby nawiązać połączenie z wystąpieniem usługi Azure SQL Data Warehouse. Musisz mieć magazyn danych SQL Data Warehouse utworzony w ramach wymagań wstępnych.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
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

   ![Sprawdzanie przykładowej tabeli](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Sprawdzanie przykładowej tabeli")

7. Uruchom wybrane zapytanie, aby sprawdzić zawartość tabeli. Tabela powinna zawierać takie same dane jak ramka danych **renamedColumnsDF**.

    ![Sprawdzanie zawartości przykładowej tabeli](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Sprawdzanie zawartości przykładowej tabeli")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz przerwać działanie klastra. W obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** po lewej stronie. Aby przerwać działanie klastra, w obszarze **Akcje** wskaż wielokropek (...) i wybierz ikonę **Przerwij**.

![Zatrzymywanie klastra usługi Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zatrzymywanie klastra usługi Databricks")

Jeśli nie przerwiesz działania klastra ręcznie, zostanie on automatycznie zatrzymany, o ile podczas tworzenia klastra zaznaczono pole wyboru **Zakończ po \_\_ min nieaktywności**. W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie notesu w usłudze Azure Databricks
> * Wyodrębnianie danych z konta usługi Data Lake Storage Gen2
> * Przekształcanie danych w usłudze Azure Databricks
> * Ładowanie danych do usługi Azure SQL Data Warehouse

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat przesyłania strumieniowego danych w czasie rzeczywistym do usługi Azure Databricks przy użyciu usługi Azure Event Hubs.

> [!div class="nextstepaction"]
>[Przesyłanie strumieniowe danych do usługi Azure Databricks przy użyciu usługi Event Hubs](databricks-stream-from-eventhubs.md)
