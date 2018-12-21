---
title: 'Samouczek: Wykonywanie operacji ETL za pomocą usługi Azure Databricks'
description: Dowiedz się, jak wyodrębniać dane z usługi Data Lake Store do usługi Azure Databricks, przekształcać je, a następnie załadować do usługi Azure SQL Data Warehouse.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 11/19/2018
ms.openlocfilehash: 48b2cdb26994d01dfced8216bb70493802f672a7
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413680"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks

W ramach tego samouczka wykonasz operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks. Wyodrębnisz dane z usługi Azure Data Lake Store do usługi Azure Databricks, uruchomisz przekształcenia danych w usłudze Azure Databricks, a następnie załadujesz przekształcone dane do usługi Azure SQL Data Warehouse.

W procedurach opisanych w tym samouczku do przesyłania danych do usługi Azure Databricks służy łącznik SQL Data Warehouse dla usługi Azure Databricks. Ten łącznik z kolei używa usługi Azure Blob Storage jako magazynu tymczasowego dla danych przesyłanych między klastrem usługi Azure Databricks a usługą Azure SQL Data Warehouse.

Poniższa ilustracja przedstawia przepływ aplikacji:

![Usługa Azure Databricks z usługami Data Lake Store i SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Usługa Azure Databricks z usługami Data Lake Store i SQL Data Warehouse")

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie konta usługi Azure Data Lake Store
> * Przekazywanie danych do usługi Azure Data Lake Store
> * Tworzenie notesu w usłudze Azure Databricks
> * Wyodrębnianie danych z usługi Data Lake Store
> * Przekształcanie danych w usłudze Azure Databricks
> * Ładowanie danych do usługi Azure SQL Data Warehouse

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem upewnij się, że zostały spełnione następujące wymagania:
- Utwórz magazyn danych Azure SQL Data Warehouse, utwórz regułę zapory na poziomie serwera i nawiąż połączenie z serwerem jako administrator serwera. Postępuj zgodnie z instrukcjami w artykule [Szybki start: tworzenie bazy danych w usłudze Azure SQL Data Warehouse](../sql-data-warehouse/create-data-warehouse-portal.md)
- Utwórz klucz główny bazy danych dla magazynu danych Azure SQL Data Warehouse. Postępuj zgodnie z instrukcjami w artykule [Tworzenie klucza głównego bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
- Utwórz konto usługi Azure Blob Storage i zawarty w nim kontener. Ponadto pobierz klucz dostępu, aby uzyskać dostęp do konta magazynu. Postępuj zgodnie z instrukcjami w artykule [Szybki start: tworzenie konta usługi Azure Blob Storage](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** > **Dane i analiza** > **Azure Databricks**.

    ![Usługa Databricks w witrynie Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Usługa Databricks w witrynie Azure Portal")

3. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Podaj następujące wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **East US 2** (Wschodnie stany USA 2). Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

4. Tworzenie konta potrwa kilka minut. Podczas tworzenia konta po prawej stronie portalu jest wyświetlany kafelek **Przesyłanie wdrożenia dla usługi Azure Databricks**. Aby go zobaczyć, być może trzeba będzie przesunąć pulpit nawigacyjny w prawo. W górnej części ekranu jest również wyświetlany pasek postępu. Postęp można obserwować w dowolnym z tych obszarów.

    ![Kafelek wdrażania usługi Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Kafelek wdrażania usługi Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Usługa Databricks na platformie Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Usługa Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Spark usługi Databricks na platformie Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Tworzenie klastra Spark usługi Databricks na platformie Azure")

    Zaakceptuj wszystkie pozostałe wartości domyślne poza następującymi:

    * Wprowadź nazwę klastra.
    * W tym artykule należy utworzyć klaster ze środowiskiem uruchomieniowym **4.0**.
    * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_\_ min nieaktywności**. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.
    
    Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

## <a name="create-an-azure-data-lake-store-account"></a>Tworzenie konta usługi Azure Data Lake Store

W tej sekcji utworzysz konto usługi Azure Data Lake Store i skojarzysz z nim jednostkę usługi Azure Active Directory. W dalszej części tego samouczka użyjesz tej jednostki usługi w usłudze Azure Databricks, aby uzyskać dostęp do usługi Azure Data Lake Store.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Magazyn** > **Data Lake Store**.
3. W bloku **Nowa usługa Data Lake Store** podaj wartości, jak pokazano na poniższym zrzucie ekranu:

    ![Tworzenie nowego konta usługi Azure Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Tworzenie nowego konta usługi Azure Data Lake")

    Podaj następujące wartości:
    
    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa**     | Wprowadź unikatową nazwę konta usługi Data Lake Store.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Na potrzeby tego samouczka wybierz grupę zasobów, która była używana podczas tworzenia obszaru roboczego usługi Azure Databricks.  |
    |**Lokalizacja**     | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).  |
    |**Pakiet cenowy**     |  Wybierz pozycję **Płatność zgodnie z rzeczywistym użyciem**. |
    | **Ustawienia szyfrowania** | Zachowaj ustawienia domyślne. |

    Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

Teraz utworzysz jednostkę usługi Azure Active Directory i skojarzysz ją z utworzonym kontem usługi Data Lake Store.

### <a name="create-an-azure-active-directory-service-principal"></a>Tworzenie jednostki usługi Azure Active Directory

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**, a następnie wyszukaj usługę **Azure Active Directory**.

2. Wybierz pozycję **Rejestracje aplikacji**.

   ![Wybieranie rejestracji aplikacji](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Wybierz pozycję **Rejestrowanie nowej aplikacji**.

   ![Dodawanie aplikacji](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Podaj nazwę i adres URL aplikacji. Wybierz **aplikację internetową/interfejs API** dla typu aplikacji, którą chcesz utworzyć. Podaj adres URL logowania, a następnie wybierz pozycję **Utwórz**.

   ![Nadawanie nazwy aplikacji](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Aby uzyskać dostęp do konta usługi Data Lake Store z usługi Azure Databricks, musisz mieć następujące wartości utworzonej jednostki usługi Azure Active Directory:
- Identyfikator aplikacji
- Klucz uwierzytelniania
- Identyfikator dzierżawy

W poniższych sekcjach pobierzesz te wartości dla jednostki usługi Azure Active Directory, która została utworzona wcześniej.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>Pobieranie identyfikatora aplikacji i klucza uwierzytelniania dla jednostki usługi

Gdy logujesz się w sposób programowy, potrzebujesz identyfikatora aplikacji i klucza uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. W oknie **Rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

   ![Wybieranie aplikacji](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. W niektórych [przykładowych aplikacjach](#log-in-as-the-application) ta wartość występuje jako identyfikator klienta.

   ![Identyfikator klienta](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Ustawienia**.

   ![Wybieranie pozycji Ustawienia](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

   ![Wybieranie pozycji Klucze](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

   ![Zapisywanie klucza](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość, ponieważ później nie będzie można pobrać klucza. Wartość klucza podaje się z identyfikatorem aplikacji w celu zalogowania się jako aplikacja. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

   ![Zapisany klucz](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>Pobieranie identyfikatora dzierżawy

Gdy logujesz się w sposób programowy, musisz przekazać identyfikator dzierżawy z żądaniem uwierzytelniania.

1. Wybierz pozycję **Azure Active Directory**.

   ![Wybieranie pozycji Azure Active Directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.

   ![Wybieranie pozycji Właściwości usługi Azure AD](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Skopiuj **identyfikator katalogu**. Ta wartość jest Twoim identyfikatorem dzierżawy.

   ![Identyfikator dzierżawy](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png)

## <a name="upload-data-to-data-lake-store"></a>Przekazywanie danych do usługi Data Lake Store

W tej sekcji przekażesz przykładowy plik danych do usługi Data Lake Store. Później użyjesz tego pliku w usłudze Azure Databricks do uruchomienia niektórych przekształceń. Przykładowe dane (**small_radio_json.json**) używane w tym samouczku są dostępne w tym [repozytorium GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz utworzone konto usługi Data Lake Store.

2. Na karcie **Omówienie** kliknij pozycję **Eksplorator danych**.

    ![Otwieranie Eksploratora danych](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Otwieranie Eksploratora danych")

3. W Eksploratorze danych kliknij pozycję **Przekaż**.

    ![Opcja Przekaż](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Opcja Przekaż")

4. W oknie **Przekazywanie plików** przejdź do lokalizacji przykładowego pliku danych, a następnie wybierz pozycję **Dodaj wybrane pliki**.

    ![Opcja Przekaż](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Opcja Przekaż")

5. Na potrzeby tego samouczka plik danych został przekazany do katalogu głównego usługi Data Lake Store. Plik jest teraz dostępny pod adresem `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="associate-service-principal-with-azure-data-lake-store"></a>Kojarzenie jednostki usługi z usługą Azure Data Lake Store

W tej sekcji skojarzysz dane konta usługi Azure Data Lake Store z utworzoną jednostką usługi Azure Active Directory. Dzięki temu będziesz mieć dostęp do konta usługi Data Lake Store z usługi Azure Databricks. Na potrzeby scenariusza przedstawionego w tym artykule zostaną odczytane dane z usługi Data Lake Store, którymi następnie zostanie wypełniona tabela w usłudze SQL Data Warehouse. Zgodnie z opisem w sekcji [Omówienie kontroli dostępu w usłudze Data Lake Store](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions), aby mieć dostęp do odczytu pliku w usłudze Data Lake Store, musisz mieć następujące uprawnienia:

- **Wykonywanie** — uprawnienia do wszystkich folderów w strukturze folderów wiodącej do pliku.
- **Odczyt** — uprawnienia do samego pliku.

Wykonaj poniższe kroki, aby przyznać te uprawnienia.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz utworzone konto usługi Data Lake Store, a następnie wybierz pozycję **Eksplorator danych**.

    ![Uruchamianie Eksploratora danych](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-data-explorer.png "Uruchamianie Eksploratora danych")

2. W tym scenariuszu przykładowy plik danych jest w folderze głównym struktury folderów, dlatego wystarczy przypisać uprawnienia do **wykonywania** dla folderu głównego. W tym celu w katalogu głównym w Eksploratorze danych wybierz pozycję **Dostęp**.

    ![Dodawanie list ACL dla folderu](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-1.png "Dodawanie list ACL dla folderu")

3. W obszarze **Dostęp** wybierz polecenie **Dodaj**.

    ![Dodawanie list ACL dla folderu](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-2.png "Dodawanie list ACL dla folderu")

4. W obszarze **Przypisywanie uprawnień** kliknij pozycję **Wybieranie użytkownika lub grupy** i wyszukaj utworzoną wcześniej jednostkę usługi Azure Active Directory.

    ![Dodawanie dostępu do usługi Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Dodawanie dostępu do usługi Data Lake Store")

    Zaznacz jednostkę usługi AAD, którą chcesz przypisać, a następnie kliknij pozycję **Wybierz**.

5. W obszarze **Przypisywanie uprawnień**, kliknij kolejno pozycje **Wybierz uprawnienia** > **Wykonywanie**. Zachowaj pozostałe wartości domyślne i wybierz przycisk **OK** w obszarze **Wybierz uprawnienia**, a następnie w obszarze **Przypisywanie uprawnień**.

    ![Dodawanie dostępu do usługi Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-4.png "Dodawanie dostępu do usługi Data Lake Store")

6. Wróć do Eksploratora danych i kliknij plik, do którego chcesz przypisać uprawnienia do odczytu. W obszarze **Podgląd pliku** wybierz opcję **Dostęp**.

    ![Dodawanie dostępu do usługi Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-1.png "Dodawanie dostępu do usługi Data Lake Store")

7. W obszarze **Dostęp** wybierz polecenie **Dodaj**. W obszarze **Przypisywanie uprawnień** kliknij pozycję **Wybieranie użytkownika lub grupy** i wyszukaj utworzoną wcześniej jednostkę usługi Azure Active Directory.

    ![Dodawanie dostępu do usługi Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Dodawanie dostępu do usługi Data Lake Store")

    Zaznacz jednostkę usługi AAD, którą chcesz przypisać, a następnie kliknij pozycję **Wybierz**.

8. W obszarze **Przypisywanie uprawnień** kliknij kolejno pozycje **Wybierz uprawnienia** > **Odczyt**. Wybierz przycisk **OK** w obszarze **Wybierz uprawnienia**, a następnie w obszarze **Przypisywanie uprawnień**.

    ![Dodawanie dostępu do usługi Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-2.png "Dodawanie dostępu do usługi Data Lake Store")

    Jednostka usługi ma teraz wystarczające uprawnienia do odczytu przykładowego pliku danych z usługi Azure Data Lake Store.

## <a name="extract-data-from-data-lake-store"></a>Wyodrębnianie danych z usługi Data Lake Store

W tej sekcji utworzysz notes w obszarze roboczym usługi Azure Databricks, a następnie uruchomisz fragmenty kodu, aby wyodrębnić dane z usługi Data Lake Store do usługi Azure Databricks.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonego obszaru roboczego usługi Azure Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. W lewym okienku wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w usłudze Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Tworzenie notesu w usłudze Databricks")

2. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Scala**, a następnie wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w usłudze Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Tworzenie notesu w usłudze Databricks")

    Wybierz pozycję **Utwórz**.

3. W pustej komórce kodu dodaj poniższy fragment kodu i zastąp wartości symboli zastępczych zapisanymi wcześniej wartościami jednostki usługi Azure Active Directory.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    Naciśnij klawisze **SHIFT + ENTER**, aby uruchomić komórkę kodu.

4. Teraz możesz załadować przykładowy plik json w usłudze Data Lake Store jako ramkę danych w usłudze Azure Databricks. Wklej poniższy fragment kodu w nowej komórce kodu, zastąp wartość symbolu zastępczego, a następnie naciśnij klawisze **SHIFT + ENTER**.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Uruchom poniższy fragment kodu, aby wyświetlić zawartość ramki danych.

        df.show()

    Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Masz teraz dane wyodrębnione z usługi Azure Data Lake Store do usługi Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Przekształcanie danych w usłudze Azure Databricks

Przykładowe dane nieprzetworzone **small_radio_json.json** rejestrują odbiorców stacji radiowej i mają wiele kolumn. W tej sekcji przekształcisz dane tak, aby z zestawu danych były pobierane tylko określone kolumny.

1. Zacznij od pobrania tylko kolumn *firstName*, *lastName*, *gender*, *location* i *level* z utworzonej wcześniej ramki danych.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
        specificColumnsDf.show()

    Otrzymasz dane wyjściowe podobne do następującego fragmentu kodu:

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

2. Możesz dalej przekształcać te dane, aby zmienić nazwę kolumny **level** na **subscription_type**.

        val renamedColumnsDf = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDf.show()

    Otrzymasz dane wyjściowe podobne do poniższego fragmentu kodu.

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse

W tej sekcji przekażesz przekształcone dane do magazynu danych Azure SQL Data Warehouse. Używając łącznika usługi Azure SQL Data Warehouse dla usługi Azure Databricks, możesz bezpośrednio przekazać ramkę danych jako tabelę w magazynie danych SQL.

Jak wspomniano wcześniej, łącznik magazynu danych SQL korzysta z usługi Azure Blob Storage jako tymczasowej lokalizacji magazynu do przekazywania danych między usługami Azure Databricks i Azure SQL Data Warehouse. Możesz rozpocząć od podania konfiguracji umożliwiającej nawiązanie połączenia z kontem magazynu. Musisz już mieć utworzone konto w ramach wymagań wstępnych dotyczących tego artykułu.

1. Podaj konfigurację umożliwiającą uzyskanie dostępu do konta usługi Azure Storage z usługi Azure Databricks. Jeśli kopiujesz adres URL usługi Blob Storage z portalu, usuń z niego prefiks *https://*.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey = "<ACCESS KEY>"

2. Określ folder tymczasowy, który będzie używany podczas przenoszenia danych między usługami Azure Databricks i Azure SQL Data Warehouse.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Uruchom poniższy fragment kodu, aby zapisać klucze dostępu usługi Azure Blob Storage w konfiguracji. Dzięki temu nie trzeba będzie przechowywać klucza dostępu w notesie w postaci zwykłego tekstu.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Podaj wartości, aby nawiązać połączenie z wystąpieniem usługi Azure SQL Data Warehouse. Musisz mieć utworzony magazyn danych SQL w ramach wymagań wstępnych.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>"
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort = "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Uruchom poniższy fragment kodu, aby załadować przekształconą ramkę danych **renamedColumnsDf** jako tabelę w magazynie danych SQL. Ten fragment kodu tworzy tabelę o nazwie **SampleTable** w bazie danych SQL. Należy pamiętać, że usługa Azure SQL DW wymaga klucza głównego. Klucz główny można utworzyć, wykonując polecenie „CREATE MASTER KEY;” w programie SQL Server Management Studio.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
    
        renamedColumnsDf.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall)
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Połącz się z bazą danych SQL i sprawdź, czy widzisz tabelę **SampleTable**.

    ![Sprawdzanie przykładowej tabeli](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Sprawdzanie przykładowej tabeli")

7. Uruchom wybrane zapytanie, aby sprawdzić zawartość tabeli. Powinna ona zawierać takie same dane jak ramka danych **renamedColumnsDf**.

    ![Sprawdzanie zawartości przykładowej tabeli](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Sprawdzanie zawartości przykładowej tabeli")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu tego samouczka możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**.

![Zatrzymywanie klastra usługi Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zatrzymywanie klastra usługi Databricks")

Jeśli nie zakończysz działania klastra ręcznie, zostanie on automatycznie zatrzymany, o ile podczas tworzenia klastra zaznaczono pole wyboru **Zakończ po \_\_ min nieaktywności**. W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie konta usługi Azure Data Lake Store
> * Przekazywanie danych do usługi Azure Data Lake Store
> * Tworzenie notesu w usłudze Azure Databricks
> * Wyodrębnianie danych z usługi Data Lake Store
> * Przekształcanie danych w usłudze Azure Databricks
> * Ładowanie danych do usługi Azure SQL Data Warehouse

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat przesyłania strumieniowego danych w czasie rzeczywistym do usługi Azure Databricks przy użyciu usługi Azure Event Hubs.

> [!div class="nextstepaction"]
>[Przesyłanie strumieniowe danych do usługi Azure Databricks przy użyciu usługi Event Hubs](databricks-stream-from-eventhubs.md)
