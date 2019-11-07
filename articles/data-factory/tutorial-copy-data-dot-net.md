---
title: 'Skopiuj dane z usługi Azure Blob Storage do SQL Database '
description: Ten samouczek zawiera instrukcje krok po kroku dotyczące kopiowania danych z usługi Azure Blob Storage do bazy danych Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 96b1e5a9633ee141fd4aa369468a8866d87f27f2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683650"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopiowanie danych z obiektu blob platformy Azure do bazy danych Azure SQL Database przy użyciu usługi Azure Data Factory

W tym samouczku utworzysz potok fabryki danych, który kopiuje dane z usługi Azure Blob Storage do bazy danych Azure SQL Database. Wzorzec konfiguracji w tym samouczku ma zastosowanie do kopiowania danych z magazynu opartego na plikach do relacyjnego magazynu danych. Aby zapoznać się z listą magazynów danych obsługiwanych jako źródła i ujścia, zobacz tabelę zawierającą [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure Storage i Azure SQL Database.
> * Tworzenie zestawów danych obiektu Blob platformy Azure i bazy danych Azure SQL Database.
> * Tworzenie potoku zawierającego działanie kopiowania.
> * Uruchamianie przebiegu potoku.
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używany zestaw SDK platformy .NET. Aby uzyskać informacje o innych mechanizmach interakcji z usługą Azure Data Factory, przejdź do próbek w obszarze „Samouczki Szybki start”.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako **źródłowy** magazyn danych. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).
* **Usługa Azure SQL Database**. Baza danych jest używana jako magazyn danych **ujścia**. Jeśli nie masz bazy danych Azure SQL Database, utwórz ją, wykonując czynności przedstawione w artykule [Create an Azure SQL database (Tworzenie bazy danych Azure SQL Database)](../sql-database/sql-database-get-started-portal.md).
* Program **Visual Studio** w wersji 2015 lub 2017. W przewodniku w tym artykule jest używany program Visual Studio 2017.
* **Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/)** .
* **Utwórz aplikację w usłudze Azure Active Directory**, wykonując [tę instrukcję](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Zapisz następujące wartości, których użyjesz w kolejnych krokach: **identyfikator aplikacji**, **klucz uwierzytelniania** i **identyfikator dzierżawy**. Przypisz aplikację do roli „**Współautor**”, wykonując instrukcje przedstawione w tym samym artykule.

### <a name="create-a-blob-and-a-sql-table"></a>Tworzenie obiektu blob i tabeli SQL

Teraz przygotuj swój obiekt Blob platformy Azure i bazę danych Azure SQL Database w ramach tego samouczka, wykonując następujące kroki:

#### <a name="create-a-source-blob"></a>Tworzenie źródłowego obiektu Blob

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **inputEmp.txt**.

    ```
    John|Doe
    Jane|Doe
    ```

2. Użyj narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/), aby utworzyć kontener **adfv2tutorial** i przekazać plik **inputEmp.txt** do kontenera.

#### <a name="create-a-sink-sql-table"></a>Tworzenie tabeli SQL ujścia

1. Poniższy skrypt SQL umożliwia utworzenie tabeli **dbo.emp** w bazie danych Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Zezwól usługom platformy Azure na dostęp do serwera SQL. Upewnij się, że ustawienie **Zezwalaj na dostęp do usług Azure** jest włączone i ma wartość **WŁĄCZ** dla serwera SQL platformy Azure, aby usługa Data Factory mogła zapisywać dane na serwerze Azure SQL. W celu sprawdzenia i włączenia tego ustawienia wykonaj następujące kroki:

    1. Kliknij centrum **Więcej usług** po lewej stronie, a następnie kliknij przycisk **Serwery SQL**.
    2. Wybierz swój serwer, a następnie kliknij przycisk **Zapora** w obszarze **USTAWIENIA**.
    3. Na stronie **Ustawienia zapory** kliknij pozycję **WŁĄCZ** dla ustawienia **Zezwalaj na dostęp do usług platformy Azure**.


## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Za pomocą programu Visual Studio 2015/2017 utwórz aplikację konsolową .NET C#.

1. Uruchom program **Visual Studio**.
2. Kliknij pozycję **Plik**, wskaż polecenie **Nowy** i kliknij pozycję **Projekt**.
3. Wybierz pozycję **Visual C#**  -> **Aplikacja konsolowa (.NET Framework)** z listy typów projektów po prawej stronie. Wymagana jest platforma .NET w wersji 4.5.2 lub nowszej.
4. Wprowadź nazwę **ADFv2Tutorial**.
5. Kliknij przycisk **OK**, aby utworzyć projekt.

## <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

1. Kliknij pozycję **Narzędzia** -> **Menedżer pakietów NuGet** -> **Konsola menedżera pakietów**.
2. W oknie **Konsola menedżera pakietów** uruchom następujące polecenia, aby zainstalować pakiety. Aby uzyskać szczegóły, zobacz [pakiet nuget Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Tworzenie klienta fabryki danych

1. Otwórz plik **Program.cs**, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

    
2. Dodaj do metody **Main** następujący kod, który określa zmienne. Zastąp symbole zastępcze własnymi wartościami. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Dodaj do metody **Main** poniższy kod, który tworzy wystąpienie klasy **DataFactoryManagementClient**. Ten obiekt jest używany do tworzenia fabryki danych, połączonej usługi, zestawów danych i potoku. Umożliwia on również monitorowanie szczegółów uruchomienia potoku.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Dodaj do metody **Main** poniższy kod, który tworzy **fabrykę danych**.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W tym samouczku utworzysz dwie połączone usługi dla źródła i ujścia odpowiednio:

### <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

Dodaj do metody **Main** poniższy kod, który tworzy **połączoną usługę Azure Storage**. Szczegółowe informacje o obsługiwanych właściwościach można znaleźć w artykule [Właściwości połączonej usługi obiektów blob platformy Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Tworzenie połączonej usługi Azure SQL Database

Dodaj do metody **Main** poniższy kod, który tworzy **połączoną usługę Azure SQL Database**. Szczegółowe informacje o obsługiwanych właściwościach można znaleźć w artykule [Właściwości połączonej usługi Azure SQL Database](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Utwórz zestawy danych

W tej sekcji utworzysz zestaw danych źródła i ujścia. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Tworzenie zestawu danych źródłowego obiektu blob platformy Azure

Dodaj do metody **Main** poniższy kod, który tworzy **zestaw danych obiektu blob platformy Azure**. Informacje o obsługiwanych właściwościach można znaleźć w artykule [Właściwości zestawu danych obiektu Blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu blob odwołuje się do połączonej usługi Azure Storage utworzonej w poprzednim kroku i zawiera opis następujących elementów:

- Lokalizacja źródłowa obiektu blob do skopiowania: **FolderPath** i **FileName**.
- Format obiektów Blob wskazujący, jak przeanalizować zawartość: **TextFormat** i jego ustawienia (na przykład ogranicznik kolumny).
- Struktura danych, taka jak nazwy kolumn i typy danych, które w tym przypadku są mapowane na tabelę SQL ujścia.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Tworzenie zestawu danych ujścia obiektu Blob platformy Azure

Dodaj do metody **Main** poniższy kod, który tworzy **zestaw danych bazy danych Azure SQL Database**. Informacje o obsługiwanych właściwościach można znaleźć w artykule [Właściwości zestawu danych bazy danych Azure SQL Dataset](connector-azure-sql-database.md#dataset-properties).

Zdefiniuj zestaw danych reprezentujący ujście danych w bazie danych Azure SQL Database. Ten zestaw danych odwołuje się do połączonej usługi Azure SQL Database utworzonej w poprzednim kroku. Określa on również tabelę SQL, która przechowuje skopiowane dane. 

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Dodaj do metody **Main** poniższy kod, który tworzy **potok z działaniem kopiowania**. W tym samouczku ten potok zawiera jedno działanie: działanie kopiowania, które pobiera zestaw danych obiektu Blob jako źródło i zestaw danych SQL jako ujście. Dowiedz się więcej o szczegółach działania kopiowania w [omówieniu działania kopiowania](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj do metody **Main** poniższy kod, który **wyzwala uruchomienie potoku**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorowanie uruchomienia potoku

1. Dodaj do metody **Main** poniższy kod, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Dodaj do metody **Main** poniższy kod, który pobiera szczegóły uruchomienia działania kopiowania, na przykład rozmiar odczytanych/zapisanych danych.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.

Konsola wypisuje postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i działania potoku. Następnie sprawdza stan uruchomienia potoku. Poczekaj na wyświetlenie szczegółów uruchomienia działania kopiowania z rozmiarem odczytanych/zapisanych danych. Następnie użyj narzędzi, takich jak program SSMS (SQL Server Management Studio) lub program Visual Studio, aby podłączyć się do docelowej bazy danych Azure SQL i sprawdzić, czy dane są kopiowane do określonej przez Ciebie tabeli.

### <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Następne kroki

Potok w tym przykładzie kopiuje dane z jednej lokalizacji do innej lokalizacji w usłudze Azure Blob Storage. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonych usług Azure Storage i Azure SQL Database.
> * Tworzenie zestawów danych obiektu Blob platformy Azure i bazy danych Azure SQL Database.
> * Tworzenie potoku zawierającego działanie kopiowania.
> * Uruchamianie przebiegu potoku.
> * Monitorowanie uruchomień potoku i działań.


Przejdź do następującego samouczka, aby dowiedzieć się więcej o kopiowaniu danych lokalnych do chmury: 

> [!div class="nextstepaction"]
>[Kopiowanie danych ze środowiska lokalnego do chmury](tutorial-hybrid-copy-powershell.md)
