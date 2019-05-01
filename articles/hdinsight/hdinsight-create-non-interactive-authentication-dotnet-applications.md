---
title: Tworzenie aplikacji platformy .NET w usłudze Azure HDInsight uwierzytelnianiem nieinterakcyjnym
description: Dowiedz się, jak tworzyć aplikacje Microsoft .NET nieinterakcyjnych authentication w usłudze Azure HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: b07a932ef048aa894af990baa57b87529d9da3aa
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717394"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Tworzenie aplikacji HDInsight platformy .NET z uwierzytelnianiem nieinterakcyjnym
Można uruchomić aplikacji Microsoft .NET usługi Azure HDInsight przy użyciu tożsamości aplikacji (nieinterakcyjne) lub w obszarze tożsamość zalogowanego użytkownika (interakcyjne) aplikacji. W tym artykule przedstawiono sposób tworzenia nieinterakcyjnych authentication aplikacji .NET w celu połączenia z platformą Azure i zarządzanie nimi HDInsight. Przykład aplikacji interaktywnych, zobacz [nawiązywanie połączenia z usługi Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Z poziomu nieinterakcyjnych aplikacji .NET potrzebne są:

* Identyfikator dzierżawy subskrypcji platformy Azure (nazywanych również *identyfikator katalogu*). Zobacz [uzyskanie Identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
* Identyfikator klienta aplikacji usługi Azure Active Directory (Azure AD). Zobacz [utworzyć aplikację usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) i [uzyskiwanie Identyfikatora aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Klucz tajny aplikacji usługi Azure AD. Zobacz [Pobierz klucz uwierzytelniania aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi HDInsight. Zobacz [Wprowadzenie — samouczek](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Przypisywanie roli do aplikacji usługi Azure AD
Przypisywanie aplikacji usługi Azure AD [roli](../role-based-access-control/built-in-roles.md), aby przyznać jej uprawnienia do wykonania akcji. Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. (Na przykład dodawania aplikacji do roli Czytelnik dla grupy zasobów oznacza, że aplikacja może odczytać grupy zasobów i wszystkie zawarte w niej zasoby.) W tym samouczku Ustaw zakres na poziomie grupy zasobów. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).

**Aby dodać rolę właściciela do aplikacji usługi Azure AD**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
3. Wybierz grupę zasobów, która ma klaster HDInsight, na którym będzie uruchomiona zapytania Hive w dalszej części tego samouczka. Jeśli masz dużą liczbę grup zasobów, można użyć filtru, można znaleźć tego, który chcesz.
4. W menu grupy zasobów wybierz **kontrola dostępu (IAM)**.
5. Wybierz **przypisań ról** kartę, aby wyświetlić bieżące przypisania roli.
6. W górnej części strony wybierz **Dodaj przypisanie roli**.
7. Postępuj zgodnie z instrukcjami, aby dodać rolę właściciela do aplikacji usługi Azure AD. Po pomyślnym dodaniu roli, aplikacja znajduje się w ramach roli właściciela. 

## <a name="develop-an-hdinsight-client-application"></a>Tworzenie aplikacji klienckich HDInsight

1. Utwórz aplikację konsolową języka C#.
2. Dodaj następujący kod [NuGet](https://www.nuget.org/) pakiety:

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


## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie aplikacji i usługi nazwy głównej usługi Azure Active Directory w witrynie Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Dowiedz się, jak [uwierzytelniania jednostki usługi przy użyciu usługi Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Dowiedz się więcej o [kontroli dostępu opartej na rolach na platformie Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).
