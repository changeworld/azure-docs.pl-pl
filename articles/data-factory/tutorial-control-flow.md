---
title: Rozgałęzianie w potoku usługi Azure Data Factory | Microsoft Docs
description: W tym artykule przedstawiono sposób sterowania przepływem danych w usłudze Azure Data Factory przez rozgałęzianie działań i tworzenie łańcuchów działań.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: shlo
ms.openlocfilehash: 9a03094683a973db16aa949f0610bc7f9914be45
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649224"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Rozgałęzianie działań i tworzenie łańcuchów działań w potoku usługi Data Factory

W tym samouczku pokazano, jak utworzyć potok usługi Data Factory przedstawiający niektóre funkcje przepływu sterowania. Ten potok tworzy prostą kopię z kontenera w usłudze Azure Blob Storage w innym kontenerze na tym samym koncie magazynu. Jeśli działanie kopiowania zakończy się powodzeniem, warto wysłać szczegóły zakończonej pomyślnie operacji kopiowania (takie jak ilość zapisanych danych) w wiadomości e-mail z informacją o powodzeniu. W przypadku niepowodzenia działania kopiowania warto wysłać szczegóły błędu kopiowania (np. komunikat o błędzie) w wiadomości e-mail z informacją o niepowodzeniu. W samouczku pokazano, jak przekazać parametry.

Ogólne omówienie scenariusza: ![Omówienie](media/tutorial-control-flow/overview.png)

Ten samouczek obejmuje następujące procedury:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Korzystanie z przekazywania parametrów i ze zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

W tym samouczku jest używany zestaw SDK platformy .NET. Aby uzyskać informacje o innych mechanizmach interakcji z usługą Azure Data Factory, przejdź do przewodników Szybki start w spisie treści.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi Azure Storage**. Magazyn obiektów blob jest używany jako **źródłowy** magazyn danych. Jeśli nie masz konta usługi Azure Storage, utwórz je, wykonując czynności przedstawione w artykule [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).
* **Usługa Azure SQL Database**. Baza danych jest używana jako magazyn danych **ujścia**. Jeśli nie masz bazy danych Azure SQL Database, utwórz ją, wykonując czynności przedstawione w artykule [Create an Azure SQL database (Tworzenie bazy danych Azure SQL Database)](../sql-database/sql-database-get-started-portal.md).
* Program **Visual Studio** w wersji 2013, 2015 lub 2017. W przewodniku w tym artykule jest używany program Visual Studio 2017.
* **Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/)**.
* **Utwórz aplikację w usłudze Azure Active Directory**, wykonując [te instrukcje](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Zapisz następujące wartości, których użyjesz w kolejnych krokach: **identyfikator aplikacji**, **klucz uwierzytelniania** i **identyfikator dzierżawy**. Przypisz aplikację do roli „**Współautor**”, wykonując instrukcje przedstawione w tym samym artykule.

### <a name="create-blob-table"></a>Tworzenie tabeli obiektów blob

1. Uruchom program Notatnik. Skopiuj poniższy tekst i zapisz go na dysku jako plik **input.txt**.

    ```
    John|Doe
    Jane|Doe
    ```

2. Użyj narzędzi, takich jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/), aby utworzyć kontener **adfv2branch** i przekazać plik **input.txt** do kontenera.

## <a name="create-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

Za pomocą programu Visual Studio 2015/2017 utwórz aplikację konsolową .NET C#.

1. Uruchom program **Visual Studio**.
2. Kliknij pozycję **Plik**, wskaż polecenie **Nowy** i kliknij pozycję **Projekt**. Wymagana jest platforma .NET w wersji 4.5.2 lub nowszej.
3. Wybierz pozycję **Visual C#** -> **Aplikacja konsolowa (.NET Framework)** z listy typów projektów po prawej stronie.
4. Wprowadź nazwę **ADFv2BranchTutorial**.
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

2. Dodaj następujące zmienne statyczne do **klasy Program**. Zastąp symbole zastępcze własnymi wartościami. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
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

Utwórz funkcję „CreateOrUpdateDataFactory” w pliku Program.cs:

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Dodaj do metody **Main** poniższy kod, który tworzy **fabrykę danych**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Tworzenie połączonej usługi Azure Storage

Utwórz funkcję „StorageLinkedServiceDefinition” w pliku Program.cs:

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```

Dodaj do metody **Main** poniższy kod, który tworzy **połączoną usługę Azure Storage**. Szczegółowe informacje o obsługiwanych właściwościach można znaleźć w artykule [Właściwości połączonej usługi obiektów blob platformy Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Tworzenie zestawów danych

W tej sekcji utworzysz zestaw danych źródła i ujścia. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Tworzenie zestawu danych źródłowego obiektu blob platformy Azure

Dodaj do metody **Main** poniższy kod, który tworzy **zestaw danych obiektu blob platformy Azure**. Informacje o obsługiwanych właściwościach można znaleźć w artykule [Właściwości zestawu danych obiektu Blob platformy Azure](connector-azure-blob-storage.md#dataset-properties).

Należy zdefiniować zestaw danych reprezentujący źródło danych w obiekcie blob platformy Azure. Ten zestaw danych obiektu blob odwołuje się do połączonej usługi Azure Storage utworzonej w poprzednim kroku i zawiera opis następujących elementów:

- Lokalizacja obiektu blob do skopiowania: **FolderPath** i **FileName**.
- Zwróć uwagę na użycie parametrów elementu FolderPath. Wartość „sourceBlobContainer” to nazwa parametru, a wyrażenie jest zamieniane na wartości przekazane w ramach uruchomienia potoku. Składnia umożliwiająca zdefiniowanie parametrów: `@pipeline().parameters.<parameterName>`

Utwórz funkcję „SourceBlobDatasetDefinition” w pliku Program.cs

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Tworzenie zestawu danych obiektu blob platformy Azure ujścia

Utwórz funkcję „SourceBlobDatasetDefinition” w pliku Program.cs

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Dodaj do metody **Main** poniższy kod, który tworzy zestaw danych źródła i ujścia obiektu blob platformy Azure. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Tworzenie klasy języka C#: EmailRequest

W projekcie języka C# utwórz klasę o nazwie **EmailRequest**. Definiuje ona, jakie właściwości są wysyłane w potoku w treści żądania podczas wysyłania wiadomości e-mail. W tym samouczku potok wysyła cztery właściwości z potoku do wiadomości e-mail:

- **Wiadomość**: treść wiadomości e-mail. W przypadku kopiowania zakończonego powodzeniem ta właściwość zawiera szczegóły uruchomienia (ilość zapisanych danych). W przypadku kopiowania zakończonego niepowodzeniem ta właściwość zawiera szczegóły błędu.
- **Nazwa fabryki danych**: nazwa fabryki danych
- **Nazwa potoku**: nazwa potoku
- **Odbiorca**: przekazywany parametr. Ta właściwość określa odbiorcę wiadomości e-mail.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Tworzenie punktów końcowych przepływu pracy poczty e-mail

Aby wyzwolić wysyłanie wiadomości e-mail, zdefiniuj przepływ pracy przy użyciu usługi [Logic Apps](../logic-apps/logic-apps-overview.md). Aby uzyskać szczegółowe informacje na temat tworzenia przepływu pracy aplikacji logiki, zobacz [Jak utworzyć aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o powodzeniu 

Utwórz przepływ pracy aplikacji logiki o nazwie `CopySuccessEmail`. Zdefiniuj wyzwalacz przepływu pracy jako `When an HTTP request is received` i dodaj akcję `Office 365 Outlook – Send an email`.

![Przepływ pracy wiadomości e-mail z informacją o powodzeniu](media/tutorial-control-flow/success-email-workflow.png)

W wyzwalaczu żądania wypełnij pole `Request Body JSON Schema` przy użyciu następującego kodu JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Jest to zgodne z klasą **EmailRequest** utworzoną w poprzedniej sekcji. 

Żądanie powinno wyglądać następująco w projektancie aplikacji logiki:

![Projektant aplikacji logiki — żądanie](media/tutorial-control-flow/logic-app-designer-request.png)

W przypadku akcji **Wyślij wiadomość e-mail** dostosuj formatowanie wiadomości e-mail przy użyciu właściwości przekazywanych w schemacie JSON w treści żądania. Oto przykład:

![Projektant aplikacji logiki — Akcja Wyślij wiadomość e-mail](media/tutorial-control-flow/send-email-action.png)

Zapisz adres URL żądania HTTP POST dla przepływu pracy wiadomości e-mail z informacją o powodzeniu:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Przepływ pracy wiadomości e-mail z informacją o niepowodzeniu 

Sklonuj element **CopySuccessEmail** i utwórz inny przepływ pracy usługi Logic Apps o nazwie **CopyFailEmail**. W wyzwalaczu żądania element `Request Body JSON schema` jest taki sam. Wystarczy zmienić format wiadomości e-mail, na przykład element `Subject`, aby przekształcić wiadomość e-mail w wiadomość z informacją o niepowodzeniu. Oto przykład:

![Projektant aplikacji logiki — przepływ pracy wiadomości e-mail z informacją o niepowodzeniu](media/tutorial-control-flow/fail-email-workflow.png)

Zapisz adres URL żądania HTTP POST dla przepływu pracy wiadomości e-mail z informacją o niepowodzeniu:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Teraz powinny być zapisane dwa adresy URL przepływów pracy:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Tworzenie potoku

Dodaj do metody Main poniższy kod, który tworzy potok z działaniem kopiowania i właściwością dependsOn. W tym samouczku potok zawiera jedno działanie: działanie kopiowania, które używa zestawu danych obiektu blob jako źródła i innego zestawu danych obiektu blob jako ujścia. Działanie kopiowania wywołuje różne zadania poczty e-mail w zależności od tego, czy kończy się powodzeniem czy niepowodzeniem.

W tym potoku użyto następujących funkcji:

- Parametry
- Działanie internetowe
- Zależność działania
- Używanie danych wyjściowe działania jako danych wejściowych kolejnego działania

Podzielmy następujący potok na sekcje:

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```

Dodaj do metody **Main** następujący kod, który tworzy potok:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```

### <a name="parameters"></a>Parametry

Pierwsza sekcja potoku definiuje parametry. 

- sourceBlobContainer — parametr w potoku używany przez zestaw danych obiektu blob źródła.
- sinkBlobContainer — parametr w potoku używany przez zestaw danych obiektu blob ujścia.
- receiver — parametr w potoku używany przez dwa działania internetowe, w przypadku których do odbiorcy z podanym w tym parametrze adresem e-mail są wysyłane wiadomości e-mail o powodzeniu lub niepowodzeniu.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Działanie internetowe

Działanie internetowe umożliwia wywołanie dowolnego punktu końcowego REST. Aby uzyskać więcej informacji na temat działania, zobacz [Działanie internetowe](control-flow-web-activity.md). Ten potok używa działania internetowego w celu wywołania przepływu pracy poczty e-mail usługi Logic Apps. Są tworzone dwa działania internetowe: działanie wywołujące przepływ pracy **CopySuccessEmail** i działanie wywołujące przepływ pracy **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

We właściwości „Url” wklej odpowiednio punkty końcowe adresu URL żądania z przepływu pracy usługi Logic Apps. We właściwości „Body” przekaż wystąpienie klasy „EmailRequest”. Żądanie wiadomości e-mail zawiera następujące właściwości:

- Wiadomość — przekazywanie wartości elementu `@{activity('CopyBlobtoBlob').output.dataWritten`. Uzyskuje dostęp do właściwości poprzedniego działania kopiowania i przekazuje wartość elementu dataWritten. W przypadku wiadomości dotyczącej niepowodzenia przekaż dane wyjściowe błędu zamiast elementu `@{activity('CopyBlobtoBlob').error.message`.
- Nazwa fabryki danych — przekazywanie wartości elementu `@{pipeline().DataFactory}`. Jest to zmienna systemowa, która umożliwia dostęp do odpowiedniej nazwy fabryki danych. Lista zmiennych systemowych jest dostępna w artykule [Zmienne systemowe](control-flow-system-variables.md).
- Nazwa potoku — przekazywanie wartości elementu `@{pipeline().Pipeline}`. Jest to również zmienna systemowa, która umożliwia dostęp do odpowiedniej nazwy potoku. 
- Odbiorca — przekazywanie wartości elementu \@pipeline().parameters.receiver). Uzyskuje dostęp do parametrów potoku.
 
Ten kod tworzy nową zależność działania w zależności od tego, czy poprzednie działanie kopiowania się powiodło.

## <a name="create-a-pipeline-run"></a>Tworzenie uruchomienia potoku

Dodaj do metody **Main** poniższy kod, który **wyzwala uruchomienie potoku**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Klasa metody Main 

Końcowa metoda Main powinna wyglądać jak poniżej. Skompiluj i uruchom program, aby wyzwolić uruchomienie potoku.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Uruchamianie kodu

Skompiluj i uruchom aplikację, a następnie zweryfikuj wykonywanie potoku.
Konsola wypisuje postęp tworzenia fabryki danych, połączonej usługi, zestawów danych, potoku i uruchomienia potoku. Następnie sprawdza stan uruchomienia potoku. Poczekaj na wyświetlenie szczegółów uruchomienia działania kopiowania z rozmiarem odczytanych/zapisanych danych. Następnie sprawdź przy użyciu narzędzi, takich jak eksplorator usługi Azure Storage, czy obiekty blob zostały skopiowane do lokalizacji „outputBlobPath” z lokalizacji „inputBlobPath”, jak określono w zmiennych.

**Przykładowe dane wyjściowe:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Kolejne kroki

W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Tworzenie połączonej usługi Azure Storage
> * Tworzenie zestawu danych obiektów blob platformy Azure
> * Tworzenie potoku zawierającego działanie kopiowania i działanie internetowe
> * Wysyłanie danych wyjściowych działań do kolejnych działań
> * Korzystanie z przekazywania parametrów i ze zmiennych systemowych
> * Uruchamianie potoku
> * Monitorowanie uruchomień działań i potoku

Teraz możesz przejść do sekcji Pojęcia, aby uzyskać więcej informacji na temat usługi Azure Data Factory.
> [!div class="nextstepaction"]
>[Potoki i działania](concepts-pipelines-activities.md)