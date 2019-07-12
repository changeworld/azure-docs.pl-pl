---
title: Monitorowanie i programowe zarządzanie zadaniami usługi Azure Stream Analytics
description: W tym artykule opisano, jak programowe monitorowanie zadań usługi Stream Analytics utworzone za pomocą interfejsów API REST, zestaw SDK usługi Azure lub programu PowerShell.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: bf84a5b89e4769e37c45714a30d5d98300a4328d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612253"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programowe tworzenie monitor zadania usługi Stream Analytics

W tym artykule przedstawiono sposób włączania monitorowania dla zadania usługi Stream Analytics. Zadania usługi Stream Analytics, które są tworzone za pomocą interfejsów API REST, zestaw SDK usługi Azure lub programu PowerShell monitorowania, domyślnie nie jest konieczne. Można ręcznie włączyć ją w witrynie Azure portal, przechodząc do strony Monitor zadania i klikając przycisk Włącz, lub możesz zautomatyzować ten proces, wykonując kroki opisane w tym artykule. Dane monitorowania będzie wyświetlany w obszarze metryki w witrynie Azure portal. zadania usługi Stream Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do wykonywania tego procesu, musisz mieć następujące wymagania wstępne:

* Visual Studio 2019 or 2015
* [Zestaw Azure .NET SDK](https://azure.microsoft.com/downloads/) pobrane i zainstalowane
* Istniejące zadanie usługi Stream Analytics, która musi mieć monitorowanie jest włączone

## <a name="create-a-project"></a>Tworzenie projektu

1. Utwórz aplikację konsolową programu Visual Studio C# .NET.
2. W konsoli Menedżera pakietów uruchom następujące polecenia, aby zainstalować pakiety NuGet. Pierwsza z nich jest usługi Azure Stream Analytics Management .NET SDK. Drugim jest SDK monitora platformy Azure, która będzie służyć do umożliwienia monitorowania. Klienta usługi Azure Active Directory, który będzie używany do uwierzytelniania jest ostatni z nich.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Dodaj następującą sekcję appSettings w pliku App.config.
   
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
   Zastąp wartości *SubscriptionId* i *ActiveDirectoryTenantId* za pomocą usługi Azure identyfikatory subskrypcji i dzierżawy. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu PowerShell:
   
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

Poniższy kod zostanie skonfigurowany niezbędne zmiennych i zarządzania klientami.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Włącz monitorowanie dla istniejącego zadania usługi Stream Analytics

Poniższy kod umożliwia monitorowanie **istniejących** zadania usługi Stream Analytics. Pierwsza część kod wykonuje żądanie GET względem usługi Stream Analytics można pobrać informacji dotyczących określonego zadania usługi Stream Analytics. Używa ona *identyfikator* właściwości (pobieranymi z żądania GET) jako parametr do metody Put w drugiej połowie kod, który wysyła PUT żądanie do usługi Insights Włącz monitorowanie dla zadania usługi Stream Analytics.

> [!WARNING]
> Jeśli zostało wcześniej włączone monitorowanie dla innego zadania usługi Stream Analytics, za pośrednictwem witryny Azure portal lub programowo przy użyciu poniższego kodu, **firma Microsoft zaleca, aby zapewnić taką samą nazwę konta magazynu, który był używany podczas wcześniej włączone monitorowanie.**
> 
> Konto magazynu jest połączony z regionu, utworzone zadanie usługi Stream Analytics, w, a nie do samo zadanie.
> 
> Wszystkie Stream Analytics zadania (i innych zasobów platformy Azure), w tym samym regionie udostępnić to konto magazynu do przechowywania danych monitorowania. Jeśli podasz innego konta magazynu, może spowodować niezamierzone efekty uboczne w monitorowaniu inne zadania usługi Stream Analytics lub innych zasobów platformy Azure.
> 
> Nazwa konta magazynu, która umożliwia zastąpienie `<YOUR STORAGE ACCOUNT NAME>` w poniższym kodzie powinna być w tej samej subskrypcji co zadanie usługi Stream Analytics, które są Włączanie monitorowania dla konta magazynu.
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

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
