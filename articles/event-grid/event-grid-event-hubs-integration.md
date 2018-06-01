---
title: Integracja usług Azure Event Grid i Event Hubs
description: Informacje na temat migrowania danych do usługi SQL Data Warehouse przy użyciu usług Azure Event Grid i Event Hubs
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 41cd2f1081cbe8d8fca9d6afa77b87f9aa1017d3
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302943"
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Przesyłanie strumieniowe danych Big Data do magazynu danych

Usługa Azure [Event Grid](overview.md) jest inteligentną usługą routingu zdarzeń, która umożliwia reagowanie na powiadomienia z aplikacji i usług. W [przykładzie dotyczącym funkcji Event Hubs Capture i usługi Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) pokazano, jak bezproblemowo przeprowadzić migrację danych z centrum zdarzeń do usługi SQL Data Warehouse przy użyciu funkcji Azure Event Hubs Capture i usługi Azure Event Grid.

![Omówienie aplikacji](media/event-grid-event-hubs-integration/overview.png)

Gdy dane są wysyłane do centrum zdarzeń, funkcja Capture pobiera dane ze strumienia i generuje obiekty blob magazynu z danymi w formacie Avro. Generowanie obiektu blob przez funkcję Capture powoduje wyzwolenie zdarzenia. Usługa Event Grid rozsyła dane dotyczące tego zdarzenia do subskrybentów. W takim przypadku dane zdarzenia są wysyłane do punktu końcowego rozwiązania Azure Functions. Dane zdarzenia zawierają ścieżkę wygenerowanego obiektu blob. Funkcja używa tego adresu URL w celu pobrania pliku, a następnie wysyła go do magazynu danych.

W tym artykule opisano następujące zagadnienia:

* Wdrażanie poniższej infrastruktury:
  * Centrum zdarzeń z włączoną funkcją Capture
  * Konto magazynu na potrzeby plików z funkcji Capture
  * Plan usługi Azure App Service do hostowania aplikacji funkcji
  * Aplikacja funkcji do przetwarzania zdarzeń
  * Rozwiązanie SQL Server do hostowania magazynu danych
  * Usługa SQL Data Warehouse do przechowywania migrowanych danych
* Tworzenie tabeli w magazynie danych
* Dodawanie kodu do funkcji aplikacji
* Subskrybowanie zdarzenia
* Uruchamianie aplikacji, która wysyła dane do centrum zdarzeń
* Wyświetlanie zmigrowanych danych w magazynie danych

## <a name="about-the-event-data"></a>Informacje o danych zdarzenia

Usługa Event Grid dystrybuuje dane zdarzenia do subskrybentów. W poniższym przykładzie przedstawiono dane zdarzenia na potrzeby utworzenia pliku funkcji Capture. W szczególności należy zwrócić uwagę na właściwość `fileUrl` w obiekcie `data`. Aplikacja funkcji uzyskuje tę wartość i używa jej do pobrania pliku funkcji Capture.

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
* Program [Visual Studio 2017 w wersji 15.3.2 lub nowszy](https://www.visualstudio.com/vs/) z następującymi pakietami roboczymi: programowanie aplikacji klasycznych dla platformy .NET, programowanie na platformie Azure, tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych, programowanie na platformie Node.js i programowanie w języku Python.
* [Przykładowy projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) pobrany na komputer.

## <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury

Aby uprościć ten artykuł, wdrożysz wymaganą infrastrukturę za pomocą szablonu usługi Resource Manager. Aby zobaczyć wdrażane zasoby, wyświetl [szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Użyj jednego z [obsługiwanych zasobów](overview.md) dla odpowiedniej lokalizacji grupy zasobów.

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

Po wyświetleniu monitu wprowadź wartość hasła.

## <a name="create-a-table-in-sql-data-warehouse"></a>Tworzenie tabeli w usłudze SQL Data Warehouse

Dodaj tabelę do swojego magazynu danych, uruchamiając skrypt [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Aby uruchomić skrypt, użyj programu Visual Studio lub Edytora zapytań w portalu.

Skrypt do uruchomienia jest następujący:

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

## <a name="publish-the-azure-functions-app"></a>Publikowanie aplikacji usługi Azure Functions

1. Otwórz [przykładowy projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) w programie Visual Studio 2017 (wersja 15.3.2 lub nowsza).

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **FunctionEGDWDumper** i wybierz polecenie **Opublikuj**.

   ![Publikowanie aplikacji funkcji](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Wybierz pozycję **Aplikacja funkcji platformy Azure**, a następnie pozycję **Wybierz istniejące**. Wybierz pozycję **Publikuj**.

   ![Docelowa aplikacja funkcji](media/event-grid-event-hubs-integration/pick-target.png)

1. Wybierz aplikację funkcji, która została wdrożona za pomocą szablonu. Kliknij przycisk **OK**.

   ![Wybieranie aplikacji funkcji](media/event-grid-event-hubs-integration/select-function-app.png)

1. Gdy program Visual Studio skonfiguruje profil, wybierz pozycję **Opublikuj**.

   ![Wybieranie pozycji Opublikuj](media/event-grid-event-hubs-integration/select-publish.png)

Po opublikowaniu funkcji możesz przystąpić do subskrybowania zdarzenia.

## <a name="subscribe-to-the-event"></a>Subskrybowanie zdarzenia

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Wybierz swoją grupę zasobów i aplikację funkcji.

   ![Wyświetlanie aplikacji funkcji](media/event-grid-event-hubs-integration/view-function-app.png)

1. Wybierz funkcję.

   ![Wybieranie funkcji](media/event-grid-event-hubs-integration/select-function.png)

1. Wybierz pozycję **Dodaj subskrypcję siatki zdarzeń**.

   ![Dodawanie subskrypcji](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Nadaj nazwę subskrypcji siatki zdarzeń. Jako typu zdarzenia użyj wartości **Przestrzenie nazw usługi Event Hubs**. Podaj wartości, aby wybrać swoje wystąpienie przestrzeni nazw usługi Event Hubs. W polu punktu końcowego subskrybenta pozostaw podaną wartość. Wybierz pozycję **Utwórz**.

   ![Tworzenie subskrypcji](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Uruchamianie aplikacji w celu wygenerowania danych

Konfiguracja centrum zdarzeń, magazynu danych SQL, aplikacji funkcji platformy Azure i subskrypcji zdarzeń została już ukończona. Rozwiązanie jest gotowe do migracji danych z centrum zdarzeń do magazynu danych. Przed uruchomieniem aplikacji, która generuje dane dla centrum zdarzeń, należy skonfigurować kilka wartości.

1. W portalu wybierz przestrzeń nazw swojego centrum zdarzeń. Wybierz pozycję **Parametry połączenia**.

   ![Wybieranie parametrów połączenia](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Wybierz pozycję **RootManageSharedAccessKey**

   ![Wybieranie klucza](media/event-grid-event-hubs-integration/show-root-key.png)

3. Skopiuj zawartość pola **Parametry połączenia — klucz podstawowy**

   ![Kopiowanie klucza](media/event-grid-event-hubs-integration/copy-key.png)

4. Wróć do projektu programu Visual Studio. W projekcie WindTurbineDataGenerator otwórz plik **program.cs**.

5. Zastąp dwie wartości stałe. Dla obiektu **EventHubConnectionString** użyj wartości skopiowanej. Jako nazwy centrum zdarzeń użyj wartości **hubdatamigration**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skompiluj rozwiązanie. Uruchom aplikację WindTurbineGenerator.exe. Po kilku minutach wyślij zapytanie do tabeli w magazynie danych dla migrowanych danych.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby zapoznać się z wprowadzeniem do funkcji Event Hubs Capture, zobacz [Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Aby uzyskać więcej informacji na temat konfigurowania i uruchamiania przykładu, zobacz [Przykład funkcji Event Hubs Capture i usługi Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).