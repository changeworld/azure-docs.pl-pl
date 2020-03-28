---
title: 'Samouczek: Zaimplementowanie wzorca przechwytywania usługi Data Lake w celu zaktualizowania tabeli Delta usługi Azure Databricks | Dokumenty firmy Microsoft'
description: W tym samouczku pokazano, jak używać subskrypcji usługi Event Grid, funkcji platformy Azure i zadania usługi Azure Databricks do wstawiania wierszy danych do tabeli przechowywanej w usłudze Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 85fad873b6c176d2278ea48709d2892ab515a025
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303311"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Samouczek: Zaimplementowanie wzorca przechwytywania jeziora danych w celu zaktualizowania tabeli Databricks Delta

W tym samouczku pokazano, jak obsługiwać zdarzenia na koncie magazynu, które ma hierarchiczny obszar nazw.

Zbudujemy małe rozwiązanie, które umożliwia użytkownikowi wypełnianie tabeli Databricks Delta przez przesłanie pliku wartości oddzielonych przecinkami (csv), który opisuje zamówienie sprzedaży. Skompilujesz to rozwiązanie, łącząc ze sobą subskrypcję usługi Event Grid, funkcję platformy Azure i [zadanie](https://docs.azuredatabricks.net/user-guide/jobs.html) w usłudze Azure Databricks.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz subskrypcję usługi Event Grid, która wywołuje funkcję platformy Azure.
> * Utwórz funkcję platformy Azure, która odbiera powiadomienie ze zdarzenia, a następnie uruchamia zadanie w usłudze Azure Databricks.
> * Utwórz zadanie Databricks, które wstawia zamówienie klienta do tabeli Databricks Delta znajdującej się na koncie magazynu.

Skompilujemy to rozwiązanie w odwrotnej kolejności, począwszy od obszaru roboczego usługi Azure Databricks.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

* Utwórz konto magazynu, które ma hierarchiczny obszar nazw (Azure Data Lake Storage Gen2). W tym samouczku użyto konta magazynu o nazwie `contosoorders`. Upewnij się, że Twoje konto użytkownika ma przypisaną [rolę Współautor danych obiektu blob magazynu](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac).

  Zobacz [Tworzenie konta usługi Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Tworzenie jednostki usługi. Zobacz [Jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Jest kilka rzeczy, o których należy pamiętać podczas wykonywania kroków przedstawionych w tym artykule.

  :heavy_check_mark: Podczas wykonywania czynności w sekcji [Przypisywanie aplikacji do](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) roli w artykule należy przypisać rolę **współautora danych obiektów blob magazynu** do jednostki usługi.

  > [!IMPORTANT]
  > Upewnij się, że przypisano rolę w zakresie konta magazynu usługi Data Lake Storage Gen2. Możesz przypisać rolę do nadrzędnej grupy zasobów lub subskrypcji, ale będzie zgłaszany błąd dotyczący uprawnień do momentu rozpropagowania przypisań roli do konta magazynu.

  :heavy_check_mark: Podczas wykonywania czynności w sekcji [Pobierz wartości do logowania się w](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artykule wklej identyfikator dzierżawy, identyfikator aplikacji i wartości haseł do pliku tekstowego. Te wartości będą potrzebne później.

## <a name="create-a-sales-order"></a>Tworzenie zamówienia sprzedaży

Najpierw utwórz plik csv opisujący zamówienie sprzedaży, a następnie przekaż go na konto magazynu. Później użyjesz danych z tego pliku, aby wypełnić pierwszy wiersz w naszej tabeli Databricks Delta.

1. Otwórz Eksploratora usługi Azure Storage. Następnie przejdź do konta magazynu, a następnie w sekcji **Kontenery obiektów blob** utwórz nowy kontener o nazwie **data**.

   ![folder danych](./media/data-lake-storage-events/data-container.png "folder danych")

   Aby uzyskać więcej informacji na temat korzystania z Eksploratora magazynu, zobacz [Zarządzanie danymi na koncie Usługi Azure Data Storage Gen2 za pomocą Eksploratora usługi Azure Storage.](data-lake-storage-explorer.md)

2. W kontenerze **danych** utwórz folder o nazwie **input**.

3. Wklej następujący tekst do edytora tekstu.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Zapisz ten plik na komputerze lokalnym i nadaj mu nazwę **data.csv**.

5. W Eksploratorze magazynu przekaż ten plik do folderu **wejściowego.**  

## <a name="create-a-job-in-azure-databricks"></a>Tworzenie zadania w usłudze Azure Databricks

W tej sekcji wykonasz następujące zadania:

* Tworzenie obszaru roboczego usługi Azure Databricks.
* Utwórz notes.
* Tworzenie i wypełnianie tabeli Databricks Delta.
* Dodaj kod, który wstawia wiersze do tabeli Databricks Delta.
* Tworzenie zadania.

### <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** > **Analytics** > **Azure Databricks**.

    ![Databricks w witrynie Azure portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks w witrynie Azure portal")

2. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Tworzenie obszaru roboczego trwa kilka minut. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

### <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do utworzonego obszaru roboczego Usługi Azure Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Nowy** > **klaster**.

    ![Databricks na platformie Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra Databricks Spark na platformie Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Tworzenie klastra Databricks Spark na platformie Azure")

    Zaakceptuj pozostałe wartości domyślne poza następującymi:

    * Wprowadź nazwę klastra.
    * Upewnij się, że pole wyboru **Zakończ po 120 min aktywności** zostało zaznaczone. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.

4. Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Create a Spark cluster in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) (Tworzenie klastra Spark w usłudze Azure Databricks).

### <a name="create-a-notebook"></a>Tworzenie notesu

1. W lewym okienku wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w umiań danych](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Tworzenie notesu w umiań danych")

2. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Python**, a następnie wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w umiań danych](./media/data-lake-storage-events/new-databricks-notebook.png "Tworzenie notesu w umiań danych")

    Wybierz **pozycję Utwórz**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Tworzenie i wypełnianie tabeli Databricks Delta

1. W utworzonym notesie skopiuj i wklej następujący blok kodu do pierwszej komórki, ale nie uruchamiaj jeszcze tego kodu.  

   Zastąp `appId` `password`wartości `tenant` zastępcze w tym bloku kodu wartościami zebranymi podczas wypełniania wymagań wstępnych tego samouczka.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Ten kod tworzy widżet o nazwie **source_file**. Później utworzysz funkcję platformy Azure, która wywołuje ten kod i przekazuje ścieżkę pliku do tego widżetu.  Ten kod uwierzytelnia również jednostkę usługi za pomocą konta magazynu i tworzy niektóre zmienne, które będą używane w innych komórkach.

    > [!NOTE]
    > W środowisku produkcyjnym rozważ przechowywanie klucza uwierzytelniania w usłudze Azure Databricks. Następnie dodaj do bloku kodu klucz wyszukiwania zamiast klucza uwierzytelniania. <br><br>Na przykład zamiast używać tego wiersza `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`kodu: , należy użyć `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`następującego wiersza kodu: . <br><br>Po zakończeniu tego samouczka zobacz artykuł [usługi Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) w witrynie Azure Databricks w witrynie sieci Web, aby zobaczyć przykłady tego podejścia.

2. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

3. Skopiuj i wklej następujący blok kodu do innej komórki, a następnie naciśnij **klawisze SHIFT + ENTER,** aby uruchomić kod w tym bloku.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Ten kod tworzy tabelę Databricks Delta na koncie magazynu, a następnie ładuje niektóre dane początkowe z pliku csv, który został przekazany wcześniej.

4. Po pomyślnym uruchomieniu tego bloku kodu usuń ten blok kodu z notesu.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Dodawanie kodu wstawiago wiersze do tabeli Databricks Delta

1. Skopiuj i wklej następujący blok kodu do innej komórki, ale nie uruchamiaj tej komórki.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Ten kod wstawia dane do tymczasowego widoku tabeli przy użyciu danych z pliku csv. Ścieżka do tego pliku csv pochodzi z widżetu wejściowego utworzonego we wcześniejszym kroku.

2. Dodaj następujący kod, aby scalić zawartość tymczasowego widoku tabeli z tabelą Databricks Delta.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Tworzenie zadania

Utwórz zadanie uruchamiane wcześniej utworzony notes. Później utworzysz funkcję platformy Azure, która uruchamia to zadanie, gdy zdarzenie jest wywoływane.

1. Kliknij pozycję **Zadania**.

2. Na stronie **Zadania** kliknij pozycję **Utwórz zadanie**.

3. Nadaj zadaniu nazwę, `upsert-order-data` a następnie wybierz skoroszyt.

   ![Tworzenie zadania](./media/data-lake-storage-events/create-spark-job.png "Tworzenie zadania")

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Utwórz funkcję platformy Azure, która uruchamia zadanie.

1. W górnym rogu obszaru roboczego Databricks wybierz ikonę Osoby, a następnie wybierz pozycję **Ustawienia użytkownika**.

   ![Zarządzanie kontem](./media/data-lake-storage-events/generate-token.png "Ustawienia użytkownika")

2. Kliknij przycisk **Generuj nowy token,** a następnie kliknij przycisk **Generuj.**

   Pamiętaj, aby skopiować token do bezpiecznego miejsca. Funkcja platformy Azure potrzebuje tego tokenu do uwierzytelniania przy użyciu funkcji Databricks, aby można było uruchomić zadanie.
  
3. Wybierz przycisk **Utwórz zasób** znaleziony w lewym górnym rogu witryny Azure portal, a następnie wybierz pozycję **Oblicz > aplikację funkcji**.

   ![Tworzenie funkcji platformy Azure](./media/data-lake-storage-events/function-app-create-flow.png "Tworzenie funkcji platformy Azure")

4. Na stronie **Tworzenie** aplikacji funkcji upewnij się, że wybierzesz **.NET Core** dla stosu środowiska wykonawczego i upewnij się, że skonfiguruj wystąpienie usługi Application Insights.

   ![Konfigurowanie aplikacji funkcji](./media/data-lake-storage-events/new-function-app.png "Konfigurowanie aplikacji funkcji")

5. Na stronie **Przegląd** aplikacji funkcji kliknij pozycję **Konfiguracja**.

   ![Konfigurowanie aplikacji funkcji](./media/data-lake-storage-events/configure-function-app.png "Konfigurowanie aplikacji funkcji")

6. Na stronie **Ustawienia aplikacji** wybierz przycisk Nowe **ustawienie aplikacji,** aby dodać każde ustawienie.

   ![Dodaj ustawienie konfiguracji](./media/data-lake-storage-events/add-application-setting.png "Dodaj ustawienie konfiguracji")

   Dodaj następujące ustawienia:

   |Nazwa ustawienia | Wartość |
   |----|----|
   |**DBX_INSTANCE**| Obszar obszaru roboczego databricks. Na przykład: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Token dostępu osobistego, który został wygenerowany wcześniej. |
   |**DBX_JOB_ID**|Identyfikator uruchomionego zadania. W naszym przypadku wartość `1`ta jest .|
7. Na stronie przeglądu aplikacji funkcji kliknij przycisk **Nowa funkcja.**

   ![Nowa funkcja](./media/data-lake-storage-events/new-function.png "Nowa funkcja")

8. Wybierz pozycję **Azure Event Grid Trigger**.

   Zainstaluj rozszerzenie **Microsoft.Azure.WebJobs.Extensions.EventGrid,** jeśli zostanie wyświetlony monit o to. Jeśli musisz go zainstalować, musisz ponownie wybrać **wyzwalacz usługi Azure Event Grid,** aby utworzyć tę funkcję.

   Pojawi się okienko **Nowa funkcja.**

9. W okienku **Nowa funkcja** nazwij funkcję **UpsertOrder**, a następnie kliknij przycisk **Utwórz.**

10. Zastąp zawartość pliku kodu tym kodem, a następnie kliknij przycisk **Zapisz:**

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Ten kod analizuje informacje o zdarzeniu magazynu, który został podniesiony, a następnie tworzy komunikat żądania z adresem URL pliku, który wyzwolił zdarzenie. W ramach wiadomości funkcja przekazuje wartość do widżetu **source_file** utworzonego wcześniej. kod funkcji wysyła komunikat do zadania Databricks i używa tokenu, który został uzyskany wcześniej jako uwierzytelnienie.

## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

W tej sekcji utworzysz subskrypcję usługi Event Grid, która wywołuje funkcję platformy Azure, gdy pliki są przekazywane do konta magazynu.

1. Na stronie kodowej funkcji kliknij przycisk **Dodaj subskrypcję siatki zdarzeń.**

   ![Nowa subskrypcja wydarzeń](./media/data-lake-storage-events/new-event-subscription.png "Nowa subskrypcja wydarzeń")

2. Na stronie **Utwórz subskrypcję zdarzeń** nazwij subskrypcję, a następnie użyj pól na stronie, aby wybrać konto magazynu.

   ![Nowa subskrypcja wydarzeń](./media/data-lake-storage-events/new-event-subscription-2.png "Nowa subskrypcja wydarzeń")

3. Z listy rozwijanej **Filtrowanie do typów zdarzeń** wybierz zdarzenia **Utworzone obiekty Blob**i **Blob Usunięte,** a następnie kliknij przycisk **Utwórz.**

## <a name="test-the-event-grid-subscription"></a>Testowanie subskrypcji usługi Event Grid

1. Utwórz plik `customer-order.csv`o nazwie , wklej następujące informacje do tego pliku i zapisz go na komputerze lokalnym.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. W Eksploratorze magazynu przekaż ten plik do folderu **wejściowego** konta magazynu.

   Przekazywanie pliku wywołuje zdarzenie **Microsoft.Storage.BlobCreated.** Usługa Event Grid powiadamia wszystkich subskrybentów tego zdarzenia. W naszym przypadku funkcja platformy Azure jest jedynym subskrybentem. Funkcja platformy Azure analizuje parametry zdarzenia, aby określić, które zdarzenie wystąpiło. Następnie przekazuje adres URL pliku do zadania Databricks. Zadanie Databricks odczytuje plik i dodaje wiersz do tabeli Databricks Delta, która znajduje się na koncie magazynu.

3. Aby sprawdzić, czy zadanie zakończyło się pomyślnie, otwórz obszar roboczy databricks, kliknij przycisk **Zadania,** a następnie otwórz zadanie.

4. Wybierz zadanie, aby otworzyć stronę zadania.

   ![Praca iskra](./media/data-lake-storage-events/spark-job.png "Praca iskra")

   Po zakończeniu zadania zostanie wyświetlony stan ukończenia.

   ![Pomyślnie ukończone zadanie](./media/data-lake-storage-events/spark-job-completed.png "Pomyślnie ukończone zadanie")

5. W nowej komórce skoroszytu uruchom tę kwerendę w komórce, aby wyświetlić zaktualizowaną tabelę różnicową.

   ```
   %sql select * from customer_data
   ```

   Zwrócona tabela zawiera najnowszy rekord.

   ![Najnowszy rekord pojawia się w tabeli](./media/data-lake-storage-events/final_query.png "Najnowszy rekord pojawia się w tabeli")

6. Aby zaktualizować ten rekord, `customer-order-update.csv`utwórz plik o nazwie , wklej następujące informacje do tego pliku i zapisz go na komputerze lokalnym.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Ten plik csv jest prawie identyczny z poprzednim, z `228` `22`wyjątkiem ilości zamówienia zmienionego z .

7. W Eksploratorze magazynu przekaż ten plik do folderu **wejściowego** konta magazynu.

8. Uruchom `select` kwerendę ponownie, aby wyświetlić zaktualizowaną tabelę delta.

   ```
   %sql select * from customer_data
   ```

   Zwrócona tabela zawiera zaktualizowany rekord.

   ![Zaktualizowany rekord pojawia się w tabeli](./media/data-lake-storage-events/final_query-2.png "Zaktualizowany rekord pojawia się w tabeli")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta magazynu i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia usługi Blob Storage](storage-blob-event-overview.md)
