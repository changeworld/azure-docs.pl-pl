---
title: Wdrożenia usługi Azure Databricks przy użyciu punktu końcowego usługi Cosmos DB
description: W tym samouczku opisano sposób wdrażania usługi Azure Databricks w sieci wirtualnej przy użyciu punktu końcowego usługi obsługującego usługi Cosmos DB.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 0d5442a63680227f3a6186330502666c92dc3129
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126702"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Samouczek: Wdrożenia usługi Azure Databricks przy użyciu punktu końcowego usługi Cosmos DB

W tym samouczku opisano, jak wdrożyć sieć wirtualną wprowadzonego środowiska usługi Databricks za pomocą punktu końcowego usługi jest włączone dla usługi Cosmos DB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks w sieci wirtualnej
> * Tworzenie punktu końcowego usługi Cosmos DB
> * Utwórz konto usługi Cosmos DB i zaimportuj dane
> * Tworzenie klastra usługi Azure Databricks
> * Zapytanie usługi Cosmos DB z notesu usługi Azure Databricks

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

* Tworzenie [obszaru roboczego usługi Azure Databricks w sieci wirtualnej](quickstart-create-databricks-workspace-vnet-injection.md).

* Pobierz [łącznika usługi Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Pobierz przykładowe dane z [NOAA National centra informacji o środowisku](https://www.ncdc.noaa.gov/stormevents/). Wybierz stan lub obszaru i wybierz **wyszukiwania**. Na następnej stronie, zaakceptuj wartości domyślne i wybierz pozycję **wyszukiwania**. Następnie wybierz pozycję **Pobierz CSV** w lewej części strony Aby pobrać wyniki.

* Pobierz [wstępnie skompilowanym plikiem binarnym](https://aka.ms/csdmtool) z usługi Azure Cosmos DB narzędzia do migracji danych.

## <a name="create-a-cosmos-db-service-endpoint"></a>Tworzenie punktu końcowego usługi Cosmos DB

1. Po wdrożeniu obszar roboczy usługi Databricks na platformie Azure do sieci wirtualnej przejdź do sieci wirtualnej w [witryny Azure portal](https://portal.azure.com). Zwróć uwagę, publiczne i prywatne podsieci, które zostały utworzone za pomocą wdrażania usługi Databricks.

   ![Podsieci sieci wirtualnej](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Wybierz *podsieci publicznej* i tworzenie punktu końcowego usługi Cosmos DB. Następnie **Zapisz**.
   
   ![Dodawanie punktu końcowego usługi Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB

1. Otwórz witrynę Azure Portal. W lewym górnym rogu ekranu wybierz **Utwórz zasób > bazy danych > usługi Azure Cosmos DB**.

2. Wypełnij **Szczegóły wystąpienia** na **podstawy** kartę z następującymi ustawieniami:

   |Ustawienie|Wartość|
   |-------|-----|
   |Subskrypcja|*Twoja subskrypcja*|
   |Grupa zasobów|*grupy zasobów*|
   |Nazwa konta|db-vnet-service-endpoint|
   |Interfejs API|Core (SQL)|
   |Lokalizacja|Zachodnie stany USA|
   |Nadmiarowość geograficzna|Wyłączanie|
   |Zapisy w wielu regionach|Włączanie|

   ![Dodawanie punktu końcowego usługi Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Wybierz **sieci** kartę i konfigurowanie sieci wirtualnej. 

   a. Wybierz sieć wirtualną utworzono jako warunek wstępny, a następnie wybierz *podsieci publicznej*. Należy zauważyć, że *podsieci prywatnej* ma notatki *punktu końcowego "Microsoft AzureCosmosDB" Brak "*. Jest to spowodowane punktu końcowego usługi Cosmos DB można włączyć tylko na *podsieci publicznej*.

   b. Upewnij się, że **zezwolić na dostęp z witryny Azure portal** włączone. To ustawienie umożliwia dostęp do konta usługi Cosmos DB w witrynie Azure portal. Jeśli ta opcja jest ustawiona na **Odmów**, zostaną zwrócone błędy podczas próby dostępu do Twojego konta. 

   > [!NOTE]
   > Nie jest konieczne, w tym samouczku, ale można również włączyć *zezwolić na dostęp z Mój adres IP* chcącym możliwość dostępu do konta usługi Cosmos DB na maszynie lokalnej. Na przykład jeśli łączysz się z kontem za pomocą zestawu SDK Cosmos DB, należy włączyć to ustawienie. Jeśli jest ono wyłączone, zostaną zwrócone błędy "Odmowa dostępu".

   ![Ustawienia sieci konto bazy danych cosmos](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Wybierz **przeglądu + Utwórz**, a następnie **Utwórz** do utworzenia konta usługi Cosmos DB w sieci wirtualnej.

5. Po utworzeniu konta usługi Cosmos DB, przejdź do **klucze** w obszarze **ustawienia**. Skopiuj podstawowe parametry połączenia i zapisz go w edytorze tekstu do późniejszego użycia.

    ![Strona klucze konta usługi cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Wybierz **Eksplorator danych** i **Nowa kolekcja** Aby dodać nową bazę danych i kolekcji do konta usługi Cosmos DB.

    ![Nowa kolekcja usługi cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Przekazywanie danych do usługi Cosmos DB

1. Otwórz wersję interfejsu graficznego [narzędzia migracji danych usługi Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Narzędzie migracji danych usługi cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na **informacji o źródle** zaznacz **pliki CSV** w **importowanego** listy rozwijanej. Następnie wybierz pozycję **Dodaj pliki** i dodać dane storm CSV można pobrać jako warunek wstępny.

    ![Informacje o źródle narzędzia migracji danych bazy danych cosmos](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na **informacji o docelowej** kartę, wprowadź parametry połączenia. Format ciągu połączenia jest `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint i AccountKey są uwzględnione w ciągu połączenia podstawowego, który został zapisany w poprzedniej sekcji. Dołącz `Database=<your database name>` na końcu ciągu połączenia, a następnie wybierz **Sprawdź**. Następnie dodaj klucz kolekcji nazwy i partycji.

    ![Informacji docelowej narzędzia migracji danych bazy danych cosmos](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Wybierz **dalej** aż dojdziesz do strony podsumowania. Następnie wybierz **importu**.

## <a name="create-a-cluster-and-add-library"></a>Tworzenie klastra i dodaj bibliotekę

1. Przejdź do usługi Azure Databricks w [witryny Azure portal](https://portal.azure.com) i wybierz **Uruchom obszar roboczy**.

   ![Uruchom obszar roboczy usługi Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Utwórz nowy klaster. Wybierz nazwę klastra i zaakceptuj pozostałe domyślne ustawienia.

   ![Nowe ustawienia klastra](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Po utworzeniu klastra, przejdź do strony klastra i wybierz **bibliotek** kartę. Wybierz **zainstalować nowy** i przekaż plik jar łącznika Spark, aby zainstalować bibliotekę.

    ![Zainstaluj bibliotekę łącznika Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Możesz sprawdzić, czy biblioteka została zainstalowana na **bibliotek** kartę.

    ![Karta biblioteki klastra usługi Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Zapytanie usługi Cosmos DB z notesu usługi Databricks

1. Przejdź do obszaru roboczego usługi Azure Databricks i Utwórz nowy notes python.

    ![Tworzenie nowego notesu usługi Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Uruchom python następujący kod, aby ustawić konfigurację połączenia usługi Cosmos DB. Zmiana **punktu końcowego**, **Masterkey**, **bazy danych**, i **kolekcji** odpowiednio.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Użyj poniższego kodu języka python do ładowania danych i utworzenie tymczasowego widoku.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Użyj następującego polecenia magic do wykonania instrukcji SQL zwracającej dane.

    ```python
    %sql
    select * from storm
    ```

    Pomyślnie nawiązano obszar roboczy usługi Databricks wprowadzonym w sieci wirtualnej z zasobem usługi Cosmos DB włączony punkt końcowy usługi. Aby przeczytać więcej na temat łączenia z usługą Cosmos DB, zobacz [łącznika usługi Azure Cosmos DB dla platformy Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, obszar roboczy usługi Azure Databricks i wszystkie powiązane zasoby. Usunięcie zadania pozwala uniknąć niepotrzebnych rozliczeń. Jeśli planujesz używać obszaru roboczego usługi Azure Databricks w przyszłości, możesz zatrzymać klaster i uruchomić ponownie później. Jeśli nie zamierzasz nadal korzystać z tego obszaru roboczego usługi Azure Databricks, należy usunąć wszystkie zasoby utworzone w ramach tego samouczka wykonując następujące kroki:

1. W menu po lewej stronie w witrynie Azure portal, kliknij **grup zasobów** a następnie kliknij nazwę utworzoną grupę zasobów.

2. Na stronie grupy zasobów, wybierz **Usuń**, wpisz nazwę zasobu do usunięcia w polu tekstowym, a następnie wybierz pozycję **Usuń** ponownie.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku został wdrożony obszar roboczy usługi Azure Databricks z siecią wirtualną i łącznik usługi Cosmos DB Spark umożliwia zapytania danych usługi Cosmos DB z usługi Databricks. Aby dowiedzieć się więcej na temat pracy z usługą Azure Databricks w sieci wirtualnej, przejdź do samouczka dotyczącego przy użyciu programu SQL Server z usługą Azure Databricks.

> [!div class="nextstepaction"]
> [Samouczek: Zapytanie SQL Server Linux narzędziem w sieci wirtualnej z notesu usługi Azure Databricks](vnet-injection-sql-server.md)