---
title: Azure Active Directory opartego na certyfikatach uwierzytelniania za pomocą usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować tożsamości usługi Azure AD do uwierzytelniania opartego na certyfikatach w celu klucze dostępu z usługi Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: eb8c98df0f015244adf06a9b57f2223509f1f081
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082965"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-account"></a>Uwierzytelnianie oparte na certyfikatach dla tożsamości usługi Azure AD, aby klucze dostępu z konta usługi Azure Cosmos

Uwierzytelnianie oparte na certyfikatach umożliwia aplikacji klienta uwierzytelniali się przy użyciu usługi Azure Active Directory (Azure AD) przy użyciu certyfikatu klienta. Na komputerze, na którym ma być tożsamości, takie jak maszyny w środowisku lokalnym lub maszynie wirtualnej platformy Azure, mogą przeprowadzać uwierzytelnianie oparte na certyfikatach. Aplikacja odczytywać kluczy Azure DB kosmetyczka bez kluczy bezpośrednio w aplikacji. W tym artykule opisano sposób tworzenia przykładowej aplikacji usługi Azure AD, skonfiguruj go pod kątem uwierzytelniania opartego na certyfikatach, zaloguj się do platformy Azure przy użyciu nowej tożsamości aplikacji, a następnie pobiera klucze z konta usługi Azure Cosmos. W tym artykule używany program Azure PowerShell do konfigurowania tożsamości i zapewnia C# przykładową aplikację, która uwierzytelnia i uzyskuje dostęp do kluczy z poziomu konta usługi Azure Cosmos.  

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszej wersji](/powershell/azure/install-az-ps) programu Azure PowerShell.

* Jeśli nie masz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-an-app-in-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD

W tym kroku zostanie zarejestrować przykładową aplikację sieci web na swoim koncie usługi Azure AD. Ta aplikacja jest później używana odczytywanie kluczy konta usługi Azure Cosmos. Aby zarejestrować aplikację, wykonaj następujące kroki: 

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Otwórz Azure **usługi Active Directory** okienko, przejdź do okienka rejestracje aplikacji, a następnie wybierz **nowej rejestracji**. 

   ![Rejestrowanie nowej aplikacji w usłudze Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Wypełnij **rejestrowania aplikacji** formularza z następującymi szczegółami:  

   * **Nazwa** — Podaj nazwę aplikacji, może być dowolną nazwę, np. "aplikacja".
   * **Obsługiwane typy kont** — wybierz **kont w tej organizacji tylko katalog (katalog domyślny)** umożliwia zasobów w bieżącym katalogu dostępu do tej aplikacji. 
   * **Adres URL przekierowania** — wybierz aplikację typu **Web** i podaj adres URL, w którym hostowana jest aplikacja, może być dowolny adres URL. W tym przykładzie można Podaj adres URL do testowania taki jak `https://sampleApp.com` nie szkodzi, nawet jeśli aplikacja nie istnieje.

   ![Rejestrowanie przykładowej aplikacji sieci web](./media/certificate-based-authentication/register-sample-web-app.png)

1. Wybierz **zarejestrować** po wypełnieniu formularza.

1. Po rejestracji aplikacji Zanotuj **identyfikator Application(client)** i **obiektu o identyfikatorze**, użyje tych informacji w następnych krokach. 

   ![Pobierz identyfikatory aplikacji i obiektów](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instalowanie modułu usługi Azure AD

W tym kroku zostanie zainstalowany moduł Azure AD PowerShell. Ten moduł jest wymagana, aby uzyskać identyfikator aplikacji, która została zarejestrowana w poprzednim kroku i skojarzyć certyfikat z podpisem własnym do tej aplikacji. 

1. Otwórz program Windows PowerShell ISE z uprawnieniami administratora. Jeśli nie masz jeszcze ustanowionego, zainstalować moduł AZ PowerShell i nawiązać połączenie z subskrypcją. Jeśli masz wiele subskrypcji, można ustawić kontekstu bieżącej subskrypcji, jak pokazano w poniższych poleceniach:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Zainstaluj i zaimportuj [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) modułu

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Zaloguj się do usługi Azure AD

Zaloguj się do usługi Azure AD, w którym zarejestrowano aplikacji. Użyj polecenia Connect-AzureAD, aby zalogować się do konta, wprowadź swoje poświadczenia konta platformy Azure, w oknie podręcznym. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Otwórz inne wystąpienie programu Windows PowerShell ISE, a następnie uruchom następujące polecenia, aby utworzyć certyfikat z podpisem własnym i odczytać klucz skojarzony z certyfikatem:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Tworzenie poświadczeń opartego na certyfikatach 

Następnie uruchom następujące polecenia, aby uzyskać identyfikator obiektu aplikacji i Utwórz poświadczenia oparte na certyfikatach. W tym przykładzie ustawimy certyfikat wygasa po roku, można ją ustawić na dowolną datę zakończenia wymagane.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Powyższe polecenie powoduje zwrócenie dane wyjściowe są podobne do następującego zrzutu:

![Tworzenie poświadczeń opartego na certyfikatach w danych wyjściowych](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Skonfiguruj swoje konto usługi Azure Cosmos, aby użyć nowej tożsamości

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Przejdź do swojego konta usługi Azure Cosmos, otwórz **kontrola dostępu (IAM)** bloku.

1. Wybierz **Dodaj** i **Dodaj przypisanie roli**. Dodaj Przykładowa aplikacja utworzona w poprzednim kroku przy użyciu **Współautor** roli, jak pokazano na poniższym zrzucie ekranu:

   ![Konfigurowanie konta usługi Azure Cosmos do używania nowej tożsamości](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Wybierz **Zapisz** po wypełnieniu formularza


## <a name="access-the-keys-from-powershell"></a>Klucze dostępu z poziomu programu PowerShell

W tym kroku zostanie Zaloguj się do platformy Azure za pomocą aplikacji i certyfikat został utworzony i uzyskać dostęp do kluczy konta usługi Azure Cosmos. 

1. Początkowo wyczyść poświadczeń konta platformy Azure, używane do logowania na koncie. Poświadczenia można wyczyścić przy użyciu następującego polecenia:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Następnie sprawdź poprawność, aby zalogować się do witryny Azure portal przy użyciu poświadczeń aplikacji i uzyskać dostęp do kluczy usługi Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Poprzednie polecenie powoduje wyświetlenie podstawowe i pomocnicze klucze główne konta usługi Azure Cosmos. Można wyświetlić dziennik aktywności konta usługi Azure Cosmos można zweryfikować, że żądanie get kluczy zakończyła się pomyślnie, a zdarzenie jest inicjowane przez aplikację "Przykładowa aplikacja". 
 
![Sprawdź poprawność wywołanie get kluczy w usłudze Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Dostęp do kluczy z C# aplikacji 

Możesz także potwierdzić występowanie tego scenariusza, uzyskując dostęp do kluczy z C# aplikacji. Następujące C# konsoli aplikacji, które mogą uzyskiwać dostęp do kluczy usługi Azure Cosmos DB przy użyciu aplikacji zarejestrowanych w usłudze Active Directory. Upewnij się, że Aktualizowanie identyfikatora dzierżawcy, identyfikatora klienta, certName, nazwę grupy zasobów, identyfikator subskrypcji usługi Azure Cosmos szczegóły nazwy konta, przed uruchomieniem kodu. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Ten skrypt generuje podstawowe i pomocnicze klucze główne, jak pokazano na poniższym zrzucie ekranu:

![dane wyjściowe aplikacji CSharp](./media/certificate-based-authentication/csharp-application-output.png)

Podobnie jak w poprzedniej sekcji, można wyświetlić dziennik aktywności konta usługi Azure Cosmos można zweryfikować, że zdarzenia żądania get kluczy jest inicjowane przez aplikację "Przykładowa aplikacja". 


## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczanie kluczy Azure Cosmos przy użyciu usługi Azure Key Vault](access-secrets-from-keyvault.md)

* [Atrybuty zabezpieczeń dla usługi Azure Cosmos DB](cosmos-db-security-attributes.md)