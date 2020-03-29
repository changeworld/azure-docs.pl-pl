---
title: Programowo monitoruj zadania usługi Azure Stream Analytics i zarządzaj nimi
description: W tym artykule opisano sposób programowego monitorowania zadań usługi Stream Analytics utworzonych za pośrednictwem interfejsów API REST, azure SDK lub PowerShell.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 23c0cc0d0e4a007fdf46021f857b559266f6a193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431667"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programowo tworzenie monitora zadań usługi Stream Analytics

W tym artykule pokazano, jak włączyć monitorowanie zadania usługi Stream Analytics. Zadania usługi Stream Analytics, które są tworzone za pośrednictwem interfejsów API REST, Azure SDK lub PowerShell nie mają domyślnie włączone monitorowanie. Możesz ręcznie włączyć go w witrynie Azure portal, przechodząc do strony Monitor zadania i klikając przycisk Włącz lub możesz zautomatyzować ten proces, wykonując kroki opisane w tym artykule. Dane monitorowania będą wyświetlane w obszarze Metryki witryny Azure portal dla zadania usługi Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego procesu należy mieć następujące wymagania wstępne:

* Visual Studio 2019 lub 2015
* Pobrany i zainstalowany [zestaw SDK platformy Azure .NET](https://azure.microsoft.com/downloads/)
* Istniejące zadanie usługi Stream Analytics, które musi mieć włączone monitorowanie

## <a name="create-a-project"></a>Tworzenie projektu

1. Utwórz aplikację konsoli Visual Studio C# .NET.
2. W konsoli Menedżera pakietów uruchom następujące polecenia, aby zainstalować pakiety NuGet. Pierwszym z nich jest azure stream analytics management .NET SDK. Drugi to zestaw SDK usługi Azure Monitor, który będzie używany do włączania monitorowania. Ostatni to klient usługi Azure Active Directory, który będzie używany do uwierzytelniania.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Dodaj następującą sekcję appSettings do pliku App.config.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Zamień wartości *identyfikatora subscriptionid* i *identyfikatora ActiveDirectoryTenantId* na subskrypcję platformy Azure i identyfikatory dzierżawy. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu PowerShell:
   
   ```powershell
   Get-AzureAccount
   ```
4. Dodaj następujące instrukcje using do pliku źródłowego (Program.cs) w projekcie.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Dodaj metodę pomocnika uwierzytelniania.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Tworzenie klientów zarządzania

Poniższy kod skonfiguruje niezbędne zmienne i klientów zarządzania.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Włączanie monitorowania dla istniejącego zadania usługi Stream Analytics

Poniższy kod umożliwia monitorowanie **istniejącego** zadania usługi Stream Analytics. Pierwsza część kodu wykonuje żądanie GET względem usługi Usługi Usługi Usługi Stream Analytics w celu pobrania informacji o określonym zadaniu usługi Usługi Usługi Stream Analytics. Używa właściwości *Identyfikator* (pobrane z żądania GET) jako parametr dla Put metody w drugiej połowie kodu, który wysyła put żądania do usługi Insights, aby włączyć monitorowanie zadania usługi Analizy strumienia.

> [!WARNING]
> Jeśli wcześniej włączono monitorowanie dla innego zadania usługi Stream Analytics za pośrednictwem witryny Azure portal lub programowo za pomocą poniższego kodu, **zaleca się podanie tej samej nazwy konta magazynu, która została użyta podczas wcześniej włączonego monitorowania.**
> 
> Konto magazynu jest połączone z regionem, w którego utworzono zadanie usługi Stream Analytics, a nie specjalnie z samym zadaniem.
> 
> Wszystkie zadania usługi Stream Analytics (i wszystkie inne zasoby platformy Azure) w tym samym regionie współużytkują to konto magazynu do przechowywania danych monitorowania. Jeśli podasz inne konto magazynu, może to spowodować niezamierzone skutki uboczne w monitorowaniu innych zadań usługi Stream Analytics lub innych zasobów platformy Azure.
> 
> Nazwa konta magazynu, której `<YOUR STORAGE ACCOUNT NAME>` używasz do zastąpienia w poniższym kodzie, powinna być kontem magazynu, które znajduje się w tej samej subskrypcji, co zadanie usługi Stream Analytics, dla którego włączono monitorowanie.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
