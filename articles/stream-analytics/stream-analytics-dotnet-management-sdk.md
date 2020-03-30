---
title: Pakiet SDK .NET zarządzania dla usługi Azure Stream Analytics
description: Wprowadzenie do usługi Stream Analytics Management .NET SDK. Dowiedz się, jak skonfigurować i uruchomić zadania analizy. Tworzenie projektu, danych wejściowych, wyjść i przekształceń.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426257"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Zarządzanie pakietem SDK .NET: konfigurowanie i uruchamianie zadań analizy przy użyciu interfejsu API usługi Azure Stream Analytics dla platformy .NET
Dowiedz się, jak skonfigurować i uruchomić zadania analizy przy użyciu interfejsu API usługi Stream Analytics dla platformy .NET przy użyciu zestawu SDK .NET. Konfigurowanie projektu, tworzenie źródeł danych wejściowych i wyjściowych, przekształceń oraz uruchamianie i zatrzymywania zadań. W przypadku zadań analizy można przesyłać strumieniowo dane z magazynu obiektów Blob lub z centrum zdarzeń.

Zobacz [dokumentację referencyjną zarządzania dla interfejsu API usługi Stream Analytics dla platformy .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Usługa Azure Stream Analytics to w pełni zarządzana usługa zapewniająca przetwarzanie zdarzeń o małych opóźnieniach, wysokiej dostępności, skalowalne i złożone za pomocą danych przesyłanych strumieniowo w chmurze. Usługa Stream Analytics umożliwia klientom konfigurowanie zadań przesyłania strumieniowego do analizowania strumieni danych i umożliwia im prowadzenie analiz w czasie zbliżonym do rzeczywistego.  

> [!NOTE]
> Zaktualizowaliśmy przykładowy kod w tym artykule o wersję usługi Azure Stream Analytics Management .NET SDK w wersji 2.x. Przykładowy kod przy użyciu wersji SDK używa lagecy (1.x), zobacz [Korzystanie z programu Management .NET SDK v1.x for Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu należy mieć następujące wymagania:

* Zainstaluj program Visual Studio 2019 lub 2015.
* Pobierz i zainstaluj zestaw [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Utwórz grupę zasobów platformy Azure w ramach subskrypcji. Poniższy przykład jest przykładowy skrypt programu Azure PowerShell. Aby uzyskać informacje o programie Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Skonfiguruj źródło wejściowe i miejsce docelowe danych wyjściowych dla zadania, z które ma się połączyć.

## <a name="set-up-a-project"></a>Konfigurowanie projektu
Aby utworzyć zadanie analizy, użyj interfejsu API usługi Stream Analytics dla platformy .NET, najpierw skonfiguruj projekt.

1. Utwórz aplikację konsoli Visual Studio C# .NET.
2. W konsoli Menedżera pakietów uruchom następujące polecenia, aby zainstalować pakiety NuGet. Pierwszym z nich jest azure stream analytics management .NET SDK. Drugi dotyczy uwierzytelniania klienta platformy Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Dodaj następującą **sekcję appSettings** do pliku App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Zamień wartości **identyfikatora subscriptionid** i **identyfikatora ActiveDirectoryTenantId** na subskrypcję platformy Azure i identyfikatory dzierżawy. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu Azure PowerShell:

   ```powershell
      Get-AzureAccount
   ```

4. Dodaj następujące odwołanie w pliku csproj:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Dodaj następujące **instrukcje do** pliku źródłowego (Program.cs) w projekcie:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Dodaj metodę pomocniczą uwierzytelniania:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Tworzenie klienta zarządzania usługi Stream Analytics
**Obiekt StreamAnalyticsManagementClient** umożliwia zarządzanie zadaniem i składnikami zadań, takimi jak dane wejściowe, dane wyjściowe i transformacja.

Dodaj następujący kod na początku **Main** metody:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

Wartość zmiennej **resourceGroupName** powinna być taka sama jak nazwa grupy zasobów utworzonej lub pobranej w krokach wymaganych.

Aby zautomatyzować aspekt prezentacji poświadczeń tworzenia zadań, zapoznaj się [z poleceniem Uwierzytelnianie jednostki usługi za pomocą usługi Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Pozostałe sekcje tego artykułu zakłada, że ten kod znajduje się na początku **Main** metody.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
Poniższy kod tworzy zadanie usługi Stream Analytics w ramach zdefiniowanej grupy zasobów. Później dodasz dane wejściowe, dane wyjściowe i transformację do zadania.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Tworzenie źródła danych usługi Stream Analytics
Poniższy kod tworzy źródło wejściowe usługi Stream Analytics z typem źródła wejściowego obiektu blob i serializacją CSV. Aby utworzyć źródło wejściowe centrum zdarzeń, użyj **EventHubStreamInputDataSource** zamiast **BlobStreamInputDataSource**. Podobnie można dostosować typ serializacji źródła wejściowego.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Źródła wejściowe, czy z magazynu obiektów Blob lub centrum zdarzeń, są powiązane z określonego zadania. Aby użyć tego samego źródła danych wejściowych dla różnych zadań, należy wywołać metodę ponownie i określić inną nazwę zadania.

## <a name="test-a-stream-analytics-input-source"></a>Testowanie źródła danych wejściowych usługi Stream Analytics
**TestConnection** Metoda sprawdza, czy zadanie usługi Stream Analytics jest w stanie połączyć się ze źródłem wejściowym, jak również inne aspekty specyficzne dla typu źródła wejściowego. Na przykład w źródle wejściowym obiektu blob utworzonego we wcześniejszym kroku metoda sprawdzi, czy nazwa konta magazynu i pary kluczy mogą służyć do łączenia się z kontem magazynu, a także sprawdza, czy określony kontener istnieje.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Tworzenie docelowego danych wyjściowych usługi Stream Analytics
Tworzenie docelowego danych wyjściowych jest podobne do tworzenia źródła wejściowego usługi Stream Analytics. Podobnie jak źródła wejściowe, cele wyjściowe są powiązane z określonym zadaniem. Aby użyć tego samego miejsca docelowego danych wyjściowych dla różnych zadań, należy wywołać metodę ponownie i określić inną nazwę zadania.

Poniższy kod tworzy miejsce docelowe danych wyjściowych (baza danych SQL platformy Azure). Można dostosować typ danych i/lub typ szeregowania obiektu docelowego danych wyjściowego.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testowanie celu wyjściowego usługi Stream Analytics
Miejsce docelowe danych wyjściowych usługi Stream Analytics ma również **metodę TestConnection** do testowania połączeń.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Tworzenie transformacji usługi Stream Analytics
Poniższy kod tworzy transformację usługi Stream Analytics z kwerendą "wybierz * z danych wejściowych" i określa przydzielenie jednej jednostki przesyłania strumieniowego dla zadania usługi Stream Analytics. Aby uzyskać więcej informacji na temat dostosowywania jednostek przesyłania strumieniowego, zobacz [Skalowanie zadań usługi Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Podobnie jak dane wejściowe i wyjściowe transformacja jest również powiązana z określonym zadaniem usługi Stream Analytics, w ramach które zostało utworzone.

## <a name="start-a-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics
Po utworzeniu zadania usługi Stream Analytics i jego danych wejściowych, danych wyjściowych i transformacji można uruchomić zadanie, wywołując metodę **Start.**

Poniższy przykładowy kod uruchamia zadanie usługi Stream Analytics z niestandardowym czasem rozpoczęcia danych wyjściowych ustawionym na 12 grudnia 2012 r., 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zatrzymywać zadanie usługi Stream Analytics
Możesz zatrzymać uruchomione zadanie usługi Stream Analytics, wywołując **stop** metody.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Usuwanie zadania usługi Stream Analytics
Delete **Delete** Metoda spowoduje usunięcie zadania, jak również podstawowych zasobów podrzędnych, w tym dane wejściowe, dane wyjściowe i transformacja zadania.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
Znasz podstawy tworzenia i uruchamiania zadań analitycznych przy użyciu sdk .NET. Aby dowiedzieć się więcej, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Zestaw SDK usługi Azure Stream Analytics Management . NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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
