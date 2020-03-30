---
title: Aplikacja net uwierzytelniania nieinterakcyjnego — Usługa Azure HDInsight
description: Dowiedz się, jak utworzyć nieinterakcyjne uwierzytelnianie aplikacji Microsoft .NET w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371277"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Tworzenie nieinteraktywnej aplikacji uwierzytelniania .NET HDInsight

Uruchom aplikację Microsoft .NET Azure HDInsight w ramach własnej tożsamości aplikacji (nieinteraktywnej) lub pod tożsamością zalogowanego użytkownika aplikacji (interaktywnej). W tym artykule pokazano, jak utworzyć nieinteraktywną aplikację net uwierzytelniania, aby połączyć się z platformą Azure i zarządzać hdinsight. Aby uzyskać przykład aplikacji interaktywnej, zobacz [Łączenie się z usługą Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Z nieinterakcyjnej aplikacji .NET potrzebujesz:

* Identyfikator dzierżawy subskrypcji platformy Azure (nazywany również identyfikatorem *katalogu).* Zobacz [Pobierz identyfikator dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Identyfikator klienta aplikacji usługi Azure Active Directory (Azure AD). Zobacz [Tworzenie aplikacji usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) i uzyskaj identyfikator [aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Klucz tajny aplikacji usługi Azure AD. Zobacz [Pobierz klucz uwierzytelniania aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Wymagania wstępne

Klaster HDInsight. Zobacz [samouczek wprowadzenie](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Przypisywanie roli do aplikacji usługi Azure AD

Przypisz aplikacji usługi Azure AD [rolę](../role-based-access-control/built-in-roles.md), aby udzielić jej uprawnienia do wykonywania akcji. Można ustawić zakres na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone do niższych poziomów zakresu. Na przykład dodanie aplikacji do roli czytelnika dla grupy zasobów oznacza, że aplikacja może odczytać grupę zasobów i wszystkie zasoby w niej. W tym artykule można ustawić zakres na poziomie grupy zasobów. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról.](../role-based-access-control/role-assignments-portal.md)

**Aby dodać rolę właściciela do aplikacji usługi Azure AD**

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Przejdź do grupy zasobów, w której jest uruchomiony klaster HDInsight, na którym zostanie uruchomione kwerenda hive w dalszej części tego artykułu. Jeśli masz dużą liczbę grup zasobów, możesz użyć filtru, aby znaleźć żądaną grupę.
1. W menu grupy zasobów wybierz pozycję **Kontrola dostępu (IAM)**.
1. Wybierz kartę **Przypisania ról,** aby wyświetlić bieżące przypisania ról.
1. U góry strony wybierz pozycję **+ Dodaj**.
1. Postępuj zgodnie z instrukcjami, aby dodać rolę właściciela do aplikacji usługi Azure AD. Po pomyślnym dodaniu roli aplikacja jest wyświetlana w roli Właściciel.

## <a name="develop-an-hdinsight-client-application"></a>Tworzenie aplikacji klienckiej HDInsight

1. Utwórz aplikację konsolową języka C#.
2. Dodaj następujące pakiety [NuGet:](https://www.nuget.org/)

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Uruchom następujący kod:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Następne kroki

* [Utwórz aplikację i jednostkę usługi Azure Active Directory w witrynie Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Dowiedz się, jak [uwierzytelnić podmiot usługi za pomocą usługi Azure Resource Manager.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* Dowiedz się więcej o [kontroli dostępu opartej na rolach platformy Azure (RBAC).](../role-based-access-control/role-assignments-portal.md)
