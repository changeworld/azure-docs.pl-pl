---
title: Tworzenie aplikacji platformy .NET w usłudze Azure HDInsight uwierzytelnianie nieinteraktywne | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje platformy Microsoft .NET nieinterakcyjnym uwierzytelniania w usłudze Azure HDInsight.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 5672dae5600634b53cfbc8d677f6e1b8e3fa327d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Utwórz uwierzytelnianie nieinteraktywne aplikacji .NET HDInsight
Można uruchomić aplikacji Microsoft .NET Azure HDInsight przy użyciu tożsamości aplikacji (nieinterakcyjnym) lub tożsamością zalogowany użytkownik aplikacji (interaktywne). W tym artykule przedstawiono sposób tworzenia uwierzytelnianie nieinteraktywne aplikacji .NET w celu połączenia z platformą Azure i zarządzanie nimi HDInsight. Przykładowy interaktywna aplikacja, [nawiązywanie połączenia z usługi Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Z poziomu innego nieinteraktywnego aplikacji .NET potrzebne są:

* Identyfikator subskrypcji platformy Azure dzierżawy (nazywane również *identyfikator katalogu*). Zobacz [uzyskanie Identyfikatora dzierżawy](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* Identyfikator klienta aplikacji usługi Azure Active Directory (Azure AD). Zobacz [tworzy aplikację usługi Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) i [uzyskiwanie Identyfikatora aplikacji](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* Klucz tajny aplikacji usługi Azure AD. Zobacz [klucz uwierzytelniania aplikacji Get](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi HDInsight. Zobacz [Wprowadzenie — samouczek](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Przypisywanie roli do aplikacji usługi Azure AD
Przypisz aplikację usługi Azure AD [roli](../role-based-access-control/built-in-roles.md), udzielenia uprawnienia do wykonania akcji. Na poziomie subskrypcji, grupy zasobów lub zasobów można ustawić zakresu. Uprawnienia są dziedziczone na niższe poziomy zakresu. (Na przykład dodawanie aplikacji do roli czytnik dla grupy zasobów oznacza, że aplikacja może odczytać grupy zasobów i wszystkie zasoby w nim.) W tym samouczku Ustaw zakres na poziomie grupy zasobów. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą przypisań ról](../role-based-access-control/role-assignments-portal.md).

**Aby dodać rolę właściciela do aplikacji usługi Azure AD**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz **grupy zasobów**.
3. Wybierz grupę zasobów, która ma klastra usługi HDInsight, na którym będzie uruchomiona zapytanie Hive w dalszej części tego samouczka. Jeśli masz dużą liczbę grup zasobów, można użyć filtru, aby znaleźć ten, który ma.
4. W menu grupy zasobów, wybierz **(IAM) kontroli dostępu**.
5. W obszarze **użytkowników**, wybierz pozycję **Dodaj**.
6. Postępuj zgodnie z instrukcjami, aby dodać rolę właściciela do aplikacji usługi Azure AD. Po pomyślnym dodaniu roli, aplikacja jest wyświetlana w obszarze **użytkowników**, przy użyciu roli właściciela. 

## <a name="develop-an-hdinsight-client-application"></a>Tworzenie aplikacji klienckich usługi HDInsight

1. Utwórz aplikację konsolową języka C#.
2. Dodaj następujące pakiety NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Uruchom poniższy kod:

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
* [Utworzyć podmiot zabezpieczeń aplikacji i usług Azure Active Directory w portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).
* Dowiedz się, jak [uwierzytelnienia nazwy głównej usługi z usługą Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Dowiedz się więcej o [kontroli dostępu opartej na rolach na platformie Azure (RBAC)](../role-based-access-control/role-assignments-portal.md).
