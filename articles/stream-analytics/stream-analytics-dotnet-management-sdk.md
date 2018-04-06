---
title: Zarządzanie zestawu .NET SDK usługi Azure Stream Analytics | Dokumentacja firmy Microsoft
description: Rozpoczynanie pracy z Stream Analytics Management .NET SDK. Dowiedz się, jak skonfigurować i uruchomić zadania usługi analiza. Utwórz projekt, wejść, wyjść i przekształcenia.
keywords: .NET SDK, analizy interfejsu API
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeanb
ms.openlocfilehash: 8c6b0f8ef457c7d40673f27b67a6d33592908fad
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Zarządzanie zestawu .NET SDK: Skonfiguruj i uruchom zadania usługi analiza przy użyciu interfejsu API usługi analiza strumienia Azure dla platformy .NET
Dowiedz się, jak skonfigurować i uruchomić zadania usługi analiza dla platformy .NET przy użyciu zestawu .NET SDK zarządzania przy użyciu interfejsu API usługi analiza strumienia. Konfigurowanie projektu, Utwórz wejściowymi i wyjściowymi źródeł, transformacji i rozpocząć i zatrzymać zadania. Dla Twojego zadania usługi analiza może przesyłać strumieniowo dane z magazynu obiektów Blob lub Centrum zdarzeń.

Zobacz [zarządzania dokumentacji interfejsu API usługi analiza strumienia dla platformy .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Usługa Azure Stream Analytics to w pełni zarządzana usługa dostarczanie przetwarzania małych opóźnieniach, wysokiej dostępności, skalowalności, złożonych zdarzeń za pośrednictwem przesyłania strumieniowego danych w chmurze. Analiza strumienia umożliwia klientom ustawianie zadań przesyłania strumieniowego do analizowania strumieni danych oraz pozwala na dysku w pobliżu analiz w czasie rzeczywistym.  

> [!NOTE]
> Przykładowy kod w tym artykule zostały zaktualizowane z zestawu .NET SDK usługi Azure Stream Analytics zarządzania v2.x wersją. Przykładowy kod przy użyciu zestawu SDK lagecy (1.x) używa wersji, zobacz [v1.x zestawu .NET SDK zarządzania na użytek Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* Zainstaluj program Visual Studio 2017 lub 2015.
* Pobierz i zainstaluj [zestawu Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Utwórz grupy zasobów platformy Azure w ramach subskrypcji. Poniżej przedstawiono przykładowy skrypt programu PowerShell systemu Azure. Informacje programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview);  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Skonfiguruj źródło danych wejściowych i docelowego dane wyjściowe do użycia. Dodatkowe instrukcje można znaleźć [dodać dane wejściowe](stream-analytics-add-inputs.md) skonfigurować przykładowe dane wejściowe i [dodać danych wyjściowych](stream-analytics-add-outputs.md) skonfigurować przykładowe dane wyjściowe.

## <a name="set-up-a-project"></a>Konfigurowanie projektu
Aby utworzyć zadania usługi analiza, należy użyć interfejsu API usługi analiza strumienia dla platformy .NET, należy najpierw skonfigurować projektu.

1. Utwórz aplikację konsolową programu Visual Studio C# .NET.
2. W konsoli Menedżera pakietów uruchom następujące polecenia można zainstalować pakietów NuGet. Pierwsza z nich jest Azure Stream Analytics Management .NET SDK. Drugim jest do uwierzytelniania klienta usługi Azure.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
3. Dodaj następujące **appSettings** sekcji w pliku App.config:
   
        <appSettings>
          <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
          <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
        </appSettings>

    Zastąp wartości **SubscriptionId** i **ActiveDirectoryTenantId** identyfikatorami z usługi Azure subskrypcji i dzierżawcy. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu Azure PowerShell:

        Get-AzureAccount

4. Dodaj następujące informacje w pliku .csproj:

        <Reference Include="System.Configuration" />

5. Dodaj następujące **przy użyciu** instrukcje do pliku źródłowego (Program.cs) w projekcie:
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Threading;
        using System.Threading.Tasks;
        
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Rest;
6. Dodaj metodę pomocnika uwierzytelniania:

   ```
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Tworzenie klienta zarządzania usługi analiza strumienia
A **StreamAnalyticsManagementClient** obiektu umożliwia zarządzanie zadania i składniki zadania, takie jak dane wejściowe, dane wyjściowe i przekształcania.

Dodaj następujący kod na początku **Main** metody:

   ```
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

**ResourceGroupName** wartość zmiennej powinna być taka sama jak nazwa grupy zasobów został utworzony lub pobrać wstępnie wymagane kroki.

Aby zautomatyzować aspekt prezentacji poświadczenie tworzenia zadania, należy zapoznać się [uwierzytelniania nazwy głównej usługi z usługą Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Pozostałe sekcje w tym artykule założono, że ten kod jest na początku **Main** metody.

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
Poniższy kod tworzy zadanie usługi analiza strumienia w grupie zasobów, który został zdefiniowany. Zadania zostaną później dodane z danych wejściowych, wyjściowych i przekształcenie.

   ```
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

## <a name="create-a-stream-analytics-input-source"></a>Utwórz źródło danych wejściowych analiza strumienia
Poniższy kod tworzy Stream Analytics źródło danych wejściowych typu źródła danych wejściowych obiektu blob i serializacji woluminów CSV. Aby utworzyć źródło wejścia Centrum zdarzeń, użyj **EventHubStreamInputDataSource** zamiast **BlobStreamInputDataSource**. Podobnie można dostosować serializację typu źródła danych wejściowych.

   ```
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

Źródeł danych wejściowych z magazynu obiektów Blob lub Centrum zdarzeń są powiązane z konkretnym zadaniu. Aby użyć tego samego źródła danych wejściowych dla różnych zadań, należy ponownie wywołać metodę i określenia innej nazwy zadania.

## <a name="test-a-stream-analytics-input-source"></a>Testowanie usługi Stream Analytics źródło danych wejściowych
**TestConnection** metoda sprawdza, czy zadanie usługi Stream Analytics jest w stanie połączyć się źródło danych wejściowych, jak również inne aspekty specyficzne dla typu źródła danych wejściowych. Na przykład w źródło wejścia obiektów blob, utworzony w poprzednim kroku, metoda sprawdza, czy para klucza i nazwy konta magazynu może służyć do nawiązania połączenia z kontem magazynu, a także Sprawdź, czy istnieje określony kontener.

   ```
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Utworzyć cel usługi analiza strumienia wyjściowego
Tworzenie obiektu docelowego dane wyjściowe jest bardzo podobny do tworzenia usługi Stream Analytics źródło danych wejściowych. Jak źródeł danych wejściowych powiązane są elementy docelowe danych wyjściowych do określonego zadania. Aby użyć tej samej wartości docelowej danych wyjściowych dla różnych zadań, należy ponownie wywołać metodę i określenia innej nazwy zadania.

Poniższy kod tworzy obiekt docelowy danych wyjściowych (baza danych Azure SQL). Można dostosować w danych wyjściowych elementu docelowego typu danych i/lub typu serializacji.

   ```
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

## <a name="test-a-stream-analytics-output-target"></a>Testowanie elementu docelowego danych wyjściowych usługi analiza strumienia
Ma również elementu docelowego danych wyjściowych Stream Analytics **TestConnection** metody do testowania połączenia.

   ```
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Utworzyć transformację analiza strumienia
Poniższy kod tworzy transformację Stream Analytics z zapytaniem "Wybierz * z danych wejściowych" i określa przydzielić jedną jednostkę przesyłania strumieniowego zadania usługi analiza strumienia. Aby uzyskać więcej informacji dotyczących dostosowywania jednostki przesyłania strumieniowego, zobacz [zadania usługi analiza strumienia Azure skali](stream-analytics-scale-jobs.md).

   ```
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Podobnie jak dane wejściowe i wyjściowe transformację jest też powiązany z danego zadania Stream Analytics, został on utworzony w obszarze.

## <a name="start-a-stream-analytics-job"></a>Uruchom zadanie usługi analiza strumienia
Po utworzeniu zadania usługi analiza strumienia i jego input(s) wyjściami i przekształcenie, można uruchomić zadanie, wywołując **Start** metody.

Poniższy przykładowy kod uruchamia zadania usługi analiza strumienia danych wyjściowych niestandardowego czas rozpoczęcia ustawioną 12 grudnia 2012 12:12:12 UTC:

   ```
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zatrzymaj zadanie usługi analiza strumienia
Można zatrzymać uruchomione zadanie usługi Stream Analytics wywołując **zatrzymać** metody.

   ```
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Usuń zadanie usługi analiza strumienia
**Usunąć** metoda usuwa zadanie, a także podrzędne zasobów, w tym input(s), wyjściami i przekształcenie zadania.

   ```
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
Znasz już podstawy przy użyciu zestawu .NET SDK do tworzenia i uruchamiania zadania usługi analiza. Aby dowiedzieć się więcej, zobacz następujące tematy:

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management zestawu .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
