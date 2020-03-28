---
title: Samouczek — implementowanie usługi Azure Databricks z punktem końcowym usługi Cosmos DB
description: W tym samouczku opisano sposób implementowania usługi Azure Databricks w sieci wirtualnej z włączoną usługą Punktu Końcowego dla usługi Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: 4ac8c01e986cf1f3158c615a0791ba476e5bf1bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706161"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Samouczek: Implementowanie usługi Azure Databricks z punktem końcowym usługi Cosmos DB

W tym samouczku opisano sposób implementacji środowiska Databricks wstrzykuje się sieci wirtualnej z włączonym punktem końcowym usługi dla usługi Cosmos DB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Databricks w sieci wirtualnej
> * Tworzenie punktu końcowego usługi usługi Usługi Cosmos DB
> * Tworzenie konta usługi Cosmos DB i importowanie danych
> * Tworzenie klastra usługi Azure Databricks
> * Zapytanie usługi Cosmos DB z notesu usługi Azure Databricks

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wykonaj następujące czynności:

* Utwórz [obszar roboczy usługi Azure Databricks w sieci wirtualnej](quickstart-create-databricks-workspace-vnet-injection.md).

* Pobierz [łącznik Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Pobierz przykładowe dane z [Noaa National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/). Wybierz stan lub obszar i wybierz pozycję **Wyszukaj**. Na następnej stronie zaakceptuj ustawienia domyślne i wybierz pozycję **Wyszukaj**. Następnie wybierz **polecenie Pobierz CSV** po lewej stronie strony, aby pobrać wyniki.

* Pobierz [wstępnie skompilowany plik binarny](https://aka.ms/csdmtool) narzędzia do migracji danych usługi Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Tworzenie punktu końcowego usługi usługi Usługi Cosmos DB

1. Po wdrożeniu obszaru roboczego usługi Azure Databricks w sieci wirtualnej przejdź do sieci wirtualnej w [portalu Azure.](https://portal.azure.com) Zwróć uwagę na podsieci publiczne i prywatne, które zostały utworzone za pośrednictwem wdrożenia Databricks.

   ![Podsieci sieci wirtualnej](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Wybierz *podsieć publiczną* i utwórz punkt końcowy usługi Usługi Cosmos DB. Następnie **zapisz**.
   
   ![Dodawanie punktu końcowego usługi usługi Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB

1. Otwórz witrynę Azure Portal. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > bazach danych > usługi Azure Cosmos DB**.

2. Wypełnij **szczegóły wystąpienia** na karcie **Podstawy,** za pomocą następujących ustawień:

   |Ustawienie|Wartość|
   |-------|-----|
   |Subskrypcja|*subskrypcji*|
   |Grupa zasobów|*twoja grupa zasobów*|
   |Nazwa konta|db-vnet-service-punkt końcowy|
   |interfejs API|Core (SQL)|
   |Lokalizacja|Zachodnie stany USA|
   |Nadmiarowość geograficzna|Wyłączanie|
   |Moduły zapisujące obsługujące wiele regionów|Włączanie|

   ![Dodawanie punktu końcowego usługi usługi Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Wybierz kartę **Sieć** i skonfiguruj sieć wirtualną. 

   a. Wybierz sieć wirtualną utworzoną jako warunek wstępny, a następnie wybierz *podsieć publiczną*. Należy zauważyć, że *w podsieci prywatnej* brakuje punktu *końcowego "Microsoft AzureCosmosDB".* Dzieje się tak, ponieważ włączono tylko punkt końcowy usługi Usługi Cosmos DB w *podsieci publicznej*.

   b. Upewnij się, że masz **włączoną funkcję Zezwalaj na dostęp z witryny Azure Portal.** To ustawienie umożliwia dostęp do konta usługi Cosmos DB z witryny Azure portal. Jeśli ta opcja jest ustawiona na **Odmów,** podczas próby uzyskania dostępu do konta zostaną wyświetleni błędy. 

   > [!NOTE]
   > Nie jest to konieczne w tym samouczku, ale można również włączyć *zezwalaj na dostęp z mojego adresu IP,* jeśli chcesz mieć możliwość dostępu do konta usługi Cosmos DB z komputera lokalnego. Na przykład jeśli łączysz się z kontem przy użyciu sdk usługi Cosmos DB, należy włączyć to ustawienie. Jeśli jest wyłączona, zostaną wyświetlona błędy "Odmowa dostępu".

   ![Ustawienia sieciowe konta usługi Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Wybierz **pozycję Recenzja + Utwórz**, a następnie **utwórz,** aby utworzyć konto usługi Cosmos DB w sieci wirtualnej.

5. Po utworzeniu konta usługi Cosmos DB przejdź do **pozycji Klucze** w obszarze **Ustawienia**. Skopiuj podstawowy ciąg połączenia i zapisz go w edytorze tekstu do późniejszego użycia.

    ![Strona kluczy konta usługi Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Wybierz **Eksploratora danych** i **nową kolekcję,** aby dodać nową bazę danych i kolekcję do konta usługi Cosmos DB.

    ![Cosmos DB nowa kolekcja](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Przekazywanie danych do usługi Cosmos DB

1. Otwórz graficzną wersję interfejsu [narzędzia do migracji danych dla usługi Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Narzędzie do migracji danych usługi Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na karcie **Informacje o źródle** wybierz pozycję Pliki **CSV** z listy rozwijanej **Importuj z.** Następnie wybierz **pozycję Dodaj pliki** i dodaj pobrany plik CSV z danymi burzowymi jako warunek wstępny.

    ![Informacje o źródle narzędzia migracji danych usługi Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na karcie **Informacje o celu** wprowadź parametry połączenia. Format ciągu połączenia `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`to . AccountEndpoint i AccountKey są zawarte w podstawowym ciągu połączenia zapisane w poprzedniej sekcji. Dołącz `Database=<your database name>` na końcu ciągu połączenia i wybierz pozycję **Sprawdź**. Następnie dodaj nazwę kolekcji i klucz partycji.

    ![Informacje docelowe narzędzia migracji danych usługi Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Wybierz **pozycję Dalej,** aż dojdziesz do strony Podsumowanie. Następnie wybierz pozycję **Importuj**.

## <a name="create-a-cluster-and-add-library"></a>Tworzenie klastra i dodawanie biblioteki

1. Przejdź do usługi Azure Databricks w [portalu Azure](https://portal.azure.com) i wybierz pozycję **Uruchom obszar roboczy**.

   ![Uruchamianie obszaru roboczego Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Utwórz nowy klaster. Wybierz nazwę klastra i zaakceptuj pozostałe ustawienia domyślne.

   ![Nowe ustawienia klastra](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Po utworzeniu klastra przejdź do strony klastra i wybierz kartę **Biblioteki.** Wybierz pozycję **Zainstaluj nowy** i przekaż plik jar łącznika platformy Spark, aby zainstalować bibliotekę.

    ![Instalowanie biblioteki łączników platformy Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Można sprawdzić, czy biblioteka została zainstalowana na karcie **Biblioteki.**

    ![Karta Biblioteki klastrów databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Zapytanie usługi Cosmos DB z notesu databricks

1. Przejdź do obszaru roboczego usługi Azure Databricks i utwórz nowy notes języka Python.

    ![Tworzenie nowego notesu databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Uruchom następujący kod języka Python, aby ustawić konfigurację połączenia usługi Cosmos DB. Odpowiednio zmień **punkt końcowy**, **Masterkey**, **Database**i **Collection.**

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

3. Użyj następującego kodu języka Python, aby załadować dane i utworzyć widok tymczasowy.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Użyj następującego magicznego polecenia, aby wykonać instrukcję SQL, która zwraca dane.

    ```python
    %sql
    select * from storm
    ```

    Pomyślnie połączono obszar roboczy Databricks wstrzyknięty przez witrynę wirtualną z zasóbem usługi końcowej obsługującym usługę DB. Aby dowiedzieć się więcej o tym, jak połączyć się z usługą Cosmos DB, zobacz [Łącznik usługi Azure Cosmos DB dla platformy Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, usuń grupę zasobów, obszar roboczy usługi Azure Databricks i wszystkie powiązane zasoby. Usunięcie zadania pozwala uniknąć niepotrzebnych rozliczeń. Jeśli planujesz używać obszaru roboczego usługi Azure Databricks w przyszłości, możesz zatrzymać klaster i ponownie uruchomić go później. Jeśli nie zamierzasz nadal korzystać z tego obszaru roboczego usługi Azure Databricks, usuń wszystkie zasoby utworzone w tym samouczku, wykonując następujące kroki:

1. Z menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów,** a następnie kliknij nazwę utworzonej grupy zasobów.

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz nazwę zasobu do usunięcia w polu tekstowym, a następnie wybierz pozycję **Usuń** ponownie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono obszar roboczy usługi Azure Databricks w sieci wirtualnej i użyto łącznika platformy Spark usługi Cosmos DB do wykonywania zapytań o dane usługi Cosmos DB z databricks. Aby dowiedzieć się więcej na temat pracy z usługą Azure Databricks w sieci wirtualnej, przejdź do samouczka korzystania z programu SQL Server z usługą Azure Databricks.

> [!div class="nextstepaction"]
> [Samouczek: Wykonywanie zapytań o kontener platformy Docker systemu SQL Server Linux w sieci wirtualnej z notesu usługi Azure Databricks](vnet-injection-sql-server.md)
