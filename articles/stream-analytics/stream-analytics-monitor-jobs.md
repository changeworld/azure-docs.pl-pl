---
title: Monitorowanie i programowe zarządzanie zadania usługi analiza strumienia Azure
description: W tym artykule opisano, jak programowo monitorować zadania Stream Analytics utworzone za pośrednictwem interfejsów API REST, zestawu SDK platformy Azure lub programu PowerShell.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 2688f148185b1c1523178d190a7a2a76e6ceabef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programowe tworzenie monitor zadania usługi analiza strumienia

W tym artykule przedstawiono sposób włączania monitorowania dla zadania usługi analiza strumienia. Zadania usługi analiza strumienia, które są tworzone za pomocą interfejsów API REST, zestawu SDK platformy Azure lub programu PowerShell monitorowania, domyślnie nie jest konieczne. Można ręcznie włączyć go w portalu Azure, przechodząc do strony Monitor zadania i klikając przycisk włączania lub można zautomatyzować ten proces, wykonując kroki opisane w tym artykule. Dane monitorowania zostaną wyświetlone w obszarze metryki w portalu Azure dla zadania usługi analiza strumienia.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego procesu należy dysponować następującymi elementami:

* Visual Studio 2017 lub 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) pobrane i zainstalowane
* Istniejące zadanie usługi Stream Analytics, która musi mieć włączone monitorowanie

## <a name="create-a-project"></a>Tworzenie projektu

1. Utwórz aplikację konsolową programu Visual Studio C# .NET.
2. W konsoli Menedżera pakietów uruchom następujące polecenia można zainstalować pakietów NuGet. Pierwsza z nich jest Azure Stream Analytics Management .NET SDK. Drugim jest zestawu SDK Monitor Azure, która będzie służyć do umożliwienia monitorowania. Klienta usługi Azure Active Directory, który będzie używany do uwierzytelniania jest ostatni z nich.
   
   ```
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Dodaj poniższą sekcję appSettings w pliku App.config.
   
   ```
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
   Zastąp wartości *SubscriptionId* i *ActiveDirectoryTenantId* identyfikatorami z usługi Azure subskrypcji i dzierżawcy. Te wartości można uzyskać, uruchamiając następujące polecenie cmdlet programu PowerShell:
   
   ```
   Get-AzureAccount
   ```
4. Dodaj następujące instrukcje using do pliku źródłowego (Program.cs) w projekcie.
   
   ```
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
   
     ciąg statycznego publicznego GetAuthorizationHeader()
   
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

## <a name="create-management-clients"></a>Utwórz zarządzania klientami

Poniższy kod skonfiguruje niezbędne zmiennych i zarządzania klientami.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Aby włączyć monitorowanie dla istniejącego zadania usługi analiza strumienia

Poniższy kod umożliwia monitorowanie **istniejących** zadania usługi analiza strumienia. Pierwsza część kod wykonuje żądanie GET względem usługi Stream Analytics można pobrać informacji o konkretnym zadaniu Stream Analytics. Używa *identyfikator* właściwości (pobierane z żądania GET) jako parametru metody Put w drugiej połowie kod, który wysyła PUT żądania usługi Insights Aby włączyć monitorowanie zadania usługi analiza strumienia.

>[!WARNING]
>Jeśli uprzednio włączono monitorowania dla różnych zadania Stream Analytics, za pośrednictwem portalu Azure lub programistycznie za pomocą poniższego kodu, **zalecamy podanie tego samego nazwa konta magazynu, który został użyty podczas był wcześniej włączone monitorowanie.**
> 
> Konto magazynu jest połączony w regionie utworzonego zadania usługi analiza strumienia, nie jest przeznaczony do samego zadania.
> 
> Wszystkie usługi analiza strumienia zadania (i innych zasobów platformy Azure), w tym samym regionie udostępnić to konto magazynu do przechowywania danych monitorowania. Jeśli podasz innego konta magazynu może spowodować niezamierzone skutki uboczne w monitorowaniu inne zadania usługi analiza strumienia lub innych zasobów platformy Azure.
> 
> Nazwa konta magazynu, który umożliwia zastąpienie `<YOUR STORAGE ACCOUNT NAME>` następujący kod powinien być konto magazynu, który znajduje się w tej samej subskrypcji co zadanie usługi Stream Analytics, który jest włączane monitorowania.
> 
> 

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

