---
title: .NET — Zarządzanie v1.x zestawu SDK usługi Azure Stream Analytics
description: Rozpoczynanie pracy z usługą Stream Analytics Management .NET SDK. Dowiedz się, jak skonfigurować i uruchomić zadania usługi analytics. Utwórz projekt, danych wejściowych, danych wyjściowych i przekształcenia.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9c4ddb1375154fef3290008a7b981a4e9069ae94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104827"
---
# <a name="set-up-and-run-analytics-jobs-using-azure-stream-analytics-api-for-net"></a>Konfigurowanie i uruchamianie zadań analizy przy użyciu interfejsu API usługi Azure Stream Analytics dla platformy .NET
Dowiedz się, jak skonfigurować i uruchomić zadania analizy przy użyciu interfejsu API analizy Stream dla platformy .NET przy użyciu zestawu SDK platformy .NET zarządzania. Konfigurowanie projektu i tworzenia źródeł wejściowych i wyjściowych, transformacji i rozpoczęcia zatrzymanie zadań. Dla zadań analizy przesyłanie strumieniowe danych z magazynu obiektów Blob lub Centrum zdarzeń.

Zobacz [zarządzania dokumentacja Stream Analytics interfejsu API dla platformy .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Usługa Azure Stream Analytics jest w pełni zarządzaną usługę, zapewniając przetwarzanie zdarzeń o małych opóźnieniach wysoko dostępnych, skalowalnych, złożonych za pośrednictwem przesyłania strumieniowego danych w chmurze. Stream Analytics umożliwia klientom Konfigurowanie zadań przesyłania strumieniowego do analizowania strumieni danych i pozwala na dysku w pobliżu analizy w czasie rzeczywistym.  

> [!NOTE]
> Przykładowy kod w tym artykule nadal używa starszej wersji (1.x) wersję zestawu .NET SDK usługi Azure Stream Analytics Management. Przykładowy kod przy użyciu zaktualizowanej wersji zestawu SDK, zobacz [używać zestawu SDK platformy .NET zarządzania dla usługi Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* Zainstaluj program Visual Studio 2017 lub 2015.
* Pobierz i zainstaluj [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Utwórz grupę zasobów platformy Azure w ramach subskrypcji. Poniżej przedstawiono przykładowy skrypt programu Azure PowerShell. Uzyskać programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
       # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the    Register-AzProvider cmdlet to register the provider namespace
       #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Konfigurowanie źródła danych wejściowych i celem danych wyjściowych dla zadania nawiązać połączenie.

## <a name="set-up-a-project"></a>Konfigurowanie projektu
Aby utworzyć zadanie usługi analytics, należy użyć interfejsu API analizy Stream dla platformy .NET, najpierw skonfigurować projekt.

1. Utwórz aplikację konsolową programu Visual Studio C# .NET.
2. W konsoli Menedżera pakietów uruchom następujące polecenia, aby zainstalować pakiety NuGet. Pierwsza z nich jest usługi Azure Stream Analytics Management .NET SDK. Drugim jest klienta usługi Azure Active Directory, który będzie używany do uwierzytelniania.

```powershell
Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
```

1. Dodaj następujący kod **appSettings** sekcji w pliku App.config:

   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
     <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
   </appSettings>
   ```

    Zastąp wartości **SubscriptionId** i **ActiveDirectoryTenantId** za pomocą usługi Azure identyfikatory subskrypcji i dzierżawy. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu Azure PowerShell:

        Get-AzureAccount

1. Dodaj następujące odwołanie w pliku csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

1. Dodaj następujący kod **przy użyciu** instrukcje pliku źródłowego (Program.cs) w projekcie:

```csharp
using System;
using System.Configuration;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.StreamAnalytics;
using Microsoft.Azure.Management.StreamAnalytics.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

1. Dodaj metodę pomocnika uwierzytelniania:

   ```csharp
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Tworzenie klienta zarządzania usługi Stream Analytics
A **StreamAnalyticsManagementClient** obiekt umożliwia zarządzanie zadania i składniki zadania, takie jak dane wejściowe, dane wyjściowe i przekształcania.

Dodaj następujący kod na początku **Main** metody:

   ```csharp
   string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
   string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
   string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
   string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
   string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
   // Get authentication token
   TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
       ConfigurationManager.AppSettings["SubscriptionId"],
       GetAuthorizationHeader().Result);
   // Create Stream Analytics management client
   StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);
   ```

**ResourceGroupName** wartość zmiennej powinna być taka sama jak nazwa grupy zasobów, utworzyć lub pobrać wstępnie wymagane kroki.

Aby zautomatyzować prezentacji poświadczeń aspektów tworzenia zadania, zobacz [uwierzytelniania jednostki usługi przy użyciu usługi Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Pozostałe sekcje w tym artykule przyjęto założenie, że ten kod jest na początku **Main** metody.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
Poniższy kod tworzy zadanie usługi Stream Analytics w ramach grupy zasobów, które zostały zdefiniowane. Do zadania będzie później dodać z danych wejściowych, wyjściowych i przekształcania.

   ```csharp
   // Create a Stream Analytics job
   JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
   {
       Job = new Job()
       {
           Name = streamAnalyticsJobName,
           Location = "<LOCATION>",
           Properties = new JobProperties()
           {
               EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
               Sku = new Sku()
               {
                   Name = "Standard"
               }
           }
       }
   };
   JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Utwórz źródło danych wejściowych usługi Stream Analytics
Poniższy kod tworzy źródła danych wejściowych usługi Stream Analytics przy użyciu typu źródła danych wejściowych obiektów blob i serializacji woluminów CSV. Aby utworzyć źródło danych wejściowych z Centrum zdarzeń, użyj **EventHubStreamInputDataSource** zamiast **BlobStreamInputDataSource**. Podobnie można dostosować typ serializacji źródła danych wejściowych.

   ```csharp
   // Create a Stream Analytics input source
   InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
   {
       Input = new Input()
       {
           Name = streamAnalyticsInputName,
           Properties = new StreamInputProperties()
           {
               Serialization = new CsvSerialization
               {
                   Properties = new CsvSerializationProperties
                   {
                       Encoding = "UTF8",
                       FieldDelimiter = ","
                   }
               },
               DataSource = new BlobStreamInputDataSource
               {
                   Properties = new BlobStreamInputDataSourceProperties
                   {
                       StorageAccounts = new StorageAccount[]
                       {
                           new StorageAccount()
                           {
                               AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                               AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                           }
                       },
                       Container = "samples",
                       PathPattern = ""
                   }
               }
           }
       }
   };
   InputCreateOrUpdateResponse inputCreateResponse =
   client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);
   ```

Źródeł danych wejściowych z usługi Blob storage lub Centrum zdarzeń, są powiązane z konkretnym zadaniu. Aby użyć tego samego źródła danych wejściowych dla różnych zadań, należy ponownie wywołaj metodę i określić innej nazwy zadania.

## <a name="test-a-stream-analytics-input-source"></a>Testowanie źródła danych wejściowych usługi Stream Analytics
**TestConnection** metoda sprawdza, czy zadanie usługi Stream Analytics jest w stanie połączyć się z źródła danych wejściowych oraz innych aspektów, które są specyficzne dla typu źródła danych wejściowych. Na przykład w źródle danych wejściowych obiektów blob, utworzonej w poprzednim kroku, metoda sprawdzi, czy parę klucza i nazwy konta magazynu można połączyć się z kontem magazynu, a także Sprawdź, czy istnieje określony kontener.

   ```csharp
   // Test input source connection
   DataSourceTestConnectionResponse inputTestResponse =
       client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Utwórz obiekt docelowy danych wyjściowych usługi Stream Analytics
Tworzenie obiektu docelowego dane wyjściowe jest bardzo podobne do tworzenia źródła danych wejściowych usługi Stream Analytics. Takie jak źródła danych wejściowych do określonego zadania powiązane są elementy docelowe danych wyjściowych. Aby użyć tej samej wartości docelowej danych wyjściowych dla różnych zadań, należy ponownie wywołaj metodę i określić innej nazwy zadania.

Poniższy kod tworzy obiekt docelowy danych wyjściowych (usługa Azure SQL database). Można dostosować typ danych celem danych wyjściowych i/lub typu serializacji.

   ```csharp
   // Create a Stream Analytics output target
   OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
   {
       Output = new Output()
       {
           Name = streamAnalyticsOutputName,
           Properties = new OutputProperties()
           {
               DataSource = new SqlAzureOutputDataSource()
               {
                   Properties = new SqlAzureOutputDataSourceProperties()
                   {
                       Server = "<YOUR DATABASE SERVER NAME>",
                       Database = "<YOUR DATABASE NAME>",
                       User = "<YOUR DATABASE LOGIN>",
                       Password = "<YOUR DATABASE LOGIN PASSWORD>",
                       Table = "<YOUR DATABASE TABLE NAME>"
                   }
               }
           }
       }
   };
   OutputCreateOrUpdateResponse outputCreateResponse =
       client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testowanie celem danych wyjściowych usługi Stream Analytics
Celem danych wyjściowych usługi Stream Analytics ma również **TestConnection** metodę do testowania połączeń.

   ```csharp
   // Test output target connection
   DataSourceTestConnectionResponse outputTestResponse =
       client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Utworzyć przekształcenie usługi Stream Analytics
Poniższy kod tworzy przekształcania usługi Stream Analytics z zapytaniem "Wybierz * z danych wejściowych" i określa, aby przydzielić jedną jednostkę przesyłania strumieniowego dla zadania usługi Stream Analytics. Aby uzyskać więcej informacji na temat Dostosowywanie jednostek przesyłania strumieniowego, zobacz [zadań skalowania usługi Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a Stream Analytics transformation
   TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
   {
       Transformation = new Transformation()
       {
           Name = streamAnalyticsTransformationName,
           Properties = new TransformationProperties()
           {
               StreamingUnits = 1,
               Query = "select * from Input"
           }
       }
   };
   var transformationCreateResp =
       client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);
   ```

Podobnie jak dane wejściowe i wyjściowe transformacji jest też powiązany z określonego zadania usługi Stream Analytics, który został utworzony w obszarze.

## <a name="start-a-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics
Po utworzeniu zadania usługi Stream Analytics i jej input(s), danych wyjściowych i transformacji, można uruchomić zadania, wywołując **Start** metody.

Poniższy przykładowy kod uruchamia zadanie usługi Stream Analytics z godziną rozpoczęcia niestandardowe dane wyjściowe równa 12 grudnia 2012 r., 12:12:12 UTC:

   ```csharp
   // Start a Stream Analytics job
   JobStartParameters jobStartParameters = new JobStartParameters
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
   };
   
   LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName,    jobStartParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zatrzymaj zadanie usługi Stream Analytics
Uruchamianie zadania usługi Stream Analytics można zatrzymać, wywołując **zatrzymać** metody.

   ```csharp
   // Stop a Stream Analytics job
   LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Usuń zadanie usługi Stream Analytics
**Usuń** metoda spowoduje usunięcie zadania, a także podstawowych zasobów podrzędnych, w tym input(s), danych wyjściowych i przekształcenia zadania.

   ```csharp
   // Delete a Stream Analytics job
   LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);
   ```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
Znasz już podstawowe informacje dotyczące tworzenia i uruchamiać zadania analizy przy użyciu zestawu .NET SDK. Aby dowiedzieć się więcej, zobacz następujące tematy:

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Usługa Azure Stream Analytics Management zestawu .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
