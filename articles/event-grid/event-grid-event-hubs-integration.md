---
title: Integracja Azure siatki zdarzeń i usługi Event Hubs
description: Informacje dotyczące używania do migracji danych do magazynu danych SQL Azure zdarzeń siatki i usługi Event Hubs
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 60857327685fca9a5f97588ab51909ce2537d68f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Strumień danych big data do magazynu danych

Azure [siatki zdarzeń](overview.md) jest usługą inteligentnego zdarzeń routingu, która umożliwia reagowania na powiadomienia z aplikacji i usług. [Przechwytywania centra zdarzeń i siatki zdarzeń próbka](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) przedstawia sposób użycia przechwytywania centra zdarzeń Azure siatki zdarzeń Azure bezproblemowo migracji danych z Centrum zdarzeń do magazynu danych SQL.

![Omówienie aplikacji](media/event-grid-event-hubs-integration/overview.png)

Ponieważ dane są wysyłane do Centrum zdarzeń, przechwytywania pobiera dane ze strumienia i generuje obiektów blob magazynu z danymi w formacie Avro. Podczas przechwytywania generuje obiektu blob, wyzwala zdarzenie. Zdarzenie siatki dystrybuuje dane dotyczące zdarzeń do subskrybentów. W takim przypadku dane zdarzenia są wysyłane do punktu końcowego usługi Azure Functions. Dane zdarzenia zawiera ścieżkę wygenerowanego obiektu blob. Funkcja używa tego adresu URL pobierania pliku i wysyłania go do magazynu danych.

W tym artykule możesz:

* Wdrażanie infrastruktury następujące:
  * Centrum zdarzeń z włączonym
  * Konto magazynu dla plików z przechwytywania
  * Plan usługi aplikacji Azure do hostowania aplikacji — funkcja
  * Aplikacja funkcji przetwarzania zdarzeń
  * SQL Server do obsługi magazynu danych
  * Magazyn danych SQL do przechowywania migrowanych danych
* Utwórz tabelę w magazynie danych
* Dodaj kod, aby aplikacja — funkcja
* Subskrybowanie zdarzeń
* Uruchom aplikację, która wysyła dane do Centrum zdarzeń
* Wyświetl migrowanych danych w magazynie danych

## <a name="about-the-event-data"></a>Dane zdarzenia — informacje

Zdarzenie siatki dystrybuuje dane zdarzenia do subskrybentów. Poniższy przykład przedstawia dane zdarzeń na potrzeby tworzenia pliku przechwytywania. W szczególności, zwróć uwagę, `fileUrl` właściwości w `data` obiektu. Aplikacja funkcja pobiera tę wartość i używa go do pobrania pliku przechwytywania.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual studio wersja 2017 15.3.2 lub większą](https://www.visualstudio.com/vs/) z obciążeniami dla: tworzenia klasycznych aplikacji .NET, Azure programowanie ASP.NET i sieć web development, Node.js programowanie i programowania Python.
* [EventHubsCaptureEventGridDemo przykładowy projekt](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) pobrana na komputer.

## <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury

Aby uprościć w tym artykule, możesz wdrożyć wymaganej infrastruktury przy użyciu szablonu usługi Resource Manager. Aby wyświetlić zasoby, które zostały wdrożone, Wyświetl [szablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Użyj jednej z [obsługiwane regiony](overview.md) dla lokalizacja grupy zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

W przypadku programu PowerShell użyj polecenia:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

Podaj wartość hasło po wyświetleniu monitu.

## <a name="create-a-table-in-sql-data-warehouse"></a>Utwórz tabelę w usłudze SQL Data Warehouse

Dodawanie tabeli do magazynu danych, uruchamiając [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) skryptu. Aby uruchomić skrypt, należy użyć programu Visual Studio lub edytora zapytań w portalu.

Uruchomienie skryptu jest:

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Publikowanie aplikacji Azure Functions

1. Otwórz [EventHubsCaptureEventGridDemo przykładowy projekt](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) w programie Visual Studio 2017 (15.3.2 lub nowszej).

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **FunctionEGDWDumper**i wybierz **publikowania**.

   ![Publikowanie aplikacji — funkcja](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Wybierz **aplikacji Azure funkcji** i **wybierz istniejącą**. Wybierz **publikowania**.

   ![Docelowy funkcji aplikacji](media/event-grid-event-hubs-integration/pick-target.png)

1. Wybierz aplikację funkcja, która została wdrożona za pomocą szablonu. Kliknij przycisk **OK**.

   ![Wybierz aplikację, funkcja](media/event-grid-event-hubs-integration/select-function-app.png)

1. Po skonfigurowaniu profilu programu Visual Studio wybierz **publikowania**.

   ![Wybierz publikowania](media/event-grid-event-hubs-integration/select-publish.png)

Po opublikowaniu funkcji, możesz przystąpić do subskrybowania zdarzenia.

## <a name="subscribe-to-the-event"></a>Subskrybowanie zdarzeń

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Wybierz zasób grupy i funkcji aplikacji.

   ![Wyświetl aplikację — funkcja](media/event-grid-event-hubs-integration/view-function-app.png)

1. Wybierz funkcję.

   ![Wybierz funkcję](media/event-grid-event-hubs-integration/select-function.png)

1. Wybierz **subskrypcji dodać siatki zdarzeń**.

   ![Dodawanie subskrypcji](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Nadaj nazwę subskrypcji zdarzeń siatki. Użyj **centra zdarzeń w przestrzeni nazw** jako typ zdarzenia. Podaj wartości, aby wybrać wystąpienia przestrzeni nazw usługi Event Hubs. Pozostaw punktu końcowego subskrybenta podanej wartości. Wybierz pozycję **Utwórz**.

   ![Tworzenie subskrypcji](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Uruchamianie aplikacji do wygenerowania danych

Po zakończeniu konfigurowania Centrum zdarzeń, Magazyn danych SQL, aplikacji funkcji platformy Azure i subskrypcji zdarzeń. Rozwiązanie jest gotowy do migracji danych z Centrum zdarzeń w magazynie danych. Przed uruchomieniem aplikacji, która generuje dane do Centrum zdarzeń, musisz skonfigurować kilka wartości.

1. W portalu wybierz obszar nazw Centrum zdarzeń. Wybierz **parametry połączenia**.

   ![Wybierz parametry połączenia](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Wybierz **RootManageSharedAccessKey**

   ![Wybierz klucz](media/event-grid-event-hubs-integration/show-root-key.png)

3. Kopiuj **parametry połączenia - klucz podstawowy**

   ![Kopiowanie klucza](media/event-grid-event-hubs-integration/copy-key.png)

4. Wróć do projektu programu Visual Studio. W projekcie WindTurbineDataGenerator Otwórz **program.cs**.

5. Zastąp te dwie wartości stałej. Użyj wartości skopiowanych **EventHubConnectionString**. Użyj **hubdatamigration** nazwy Centrum zdarzeń.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skompiluj rozwiązanie. Uruchom aplikację WindTurbineGenerator.exe. Po kilku minutach Wyślij zapytanie do tabeli w magazynie danych dla migrowanych danych.

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby obejrzeć wprowadzenie do przechwytywania centra zdarzeń, zobacz [włączyć zdarzeń koncentratory przechwytywania przy użyciu portalu Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Aby uzyskać więcej informacji na temat konfigurowania i uruchamiania przykładowych, zobacz [przechwytywania centra zdarzeń i siatki zdarzeń próbka](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).