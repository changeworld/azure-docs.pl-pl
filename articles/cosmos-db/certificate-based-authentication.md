---
title: Uwierzytelnianie oparte na certyfikatach za pomocą usługi Azure Cosmos DB i usługi Active Directory
description: Dowiedz się, jak skonfigurować tożsamość usługi Azure AD do uwierzytelniania opartego na certyfikatach, aby uzyskać dostęp do kluczy z usługi Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365781"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Uwierzytelnianie oparte na certyfikatach dla tożsamości usługi Azure AD w celu uzyskania dostępu do kluczy z konta usługi Azure Cosmos DB

Uwierzytelnianie oparte na certyfikatach umożliwia uwierzytelnienie aplikacji klienckiej przy użyciu usługi Azure Active Directory (Azure AD) z certyfikatem klienta. Uwierzytelnianie oparte na certyfikatach można wykonywać na maszynie, na której jest potrzebna tożsamość, np. maszynie lokalnej lub maszynie wirtualnej na platformie Azure. Aplikacja może następnie odczytać klucze usługi Azure Cosmos DB bez kluczy bezpośrednio w aplikacji. W tym artykule opisano sposób tworzenia przykładowej aplikacji usługi Azure AD, konfigurowania jej do uwierzytelniania opartego na certyfikatach, logowania się na platformie Azure przy użyciu nowej tożsamości aplikacji, a następnie pobierania kluczy z konta usługi Azure Cosmos. W tym artykule używa programu Azure PowerShell do konfigurowania tożsamości i udostępnia przykładową aplikację języka C#, która uwierzytelnia klucze i uzyskuje do nich dostęp z konta usługi Azure Cosmos.  

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszą wersję](/powershell/azure/install-az-ps) programu Azure PowerShell.

* Jeśli nie masz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="register-an-app-in-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD

W tym kroku zarejestrujesz przykładową aplikację sieci web na koncie usługi Azure AD. Ta aplikacja jest później używana do odczytywania kluczy z konta usługi Azure Cosmos DB. Aby zarejestrować aplikację, należy wykonać następujące czynności: 

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Otwórz okienko **usługi Azure Active Directory,** przejdź do **okienka Rejestracje aplikacji** i wybierz pozycję Nowa **rejestracja**. 

   ![Rejestracja nowych aplikacji w usłudze Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Wypełnij formularz **zgłoszenia Zarejestruj** się, pod następującymi szczegółami:  

   * **Nazwa** — podaj nazwę aplikacji, może to być dowolna nazwa, taka jak "sampleApp".
   * **Obsługiwane typy kont** — wybierz **konta tylko w tym katalogu organizacyjnym (katalog domyślny),** aby zezwolić zasobom w bieżącym katalogu na dostęp do tej aplikacji. 
   * **Przekierowanie adresu URL** — wybierz aplikację typu **Web** i podaj adres URL, w którym aplikacja jest hostowana, może to być dowolny adres URL. W tym przykładzie możesz podać testowy adres URL, taki jak `https://sampleApp.com` jest w porządku, nawet jeśli aplikacja nie istnieje.

   ![Rejestrowanie przykładowej aplikacji sieci web](./media/certificate-based-authentication/register-sample-web-app.png)

1. Po wypełnieniu formularza wybierz **pozycję Zarejestruj** się.

1. Po zarejestrowaniu aplikacji zanotuj **identyfikator aplikacji (klienta)** i **identyfikator obiektu**, użyjesz tych danych w następnych krokach. 

   ![Pobierz identyfikatory aplikacji i obiektów](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instalowanie modułu AzureAD

W tym kroku zainstalujesz moduł programu Azure AD PowerShell. Ten moduł jest wymagany do uzyskania identyfikatora aplikacji zarejestrowanej w poprzednim kroku i skojarzenia certyfikatu z podpisem własnym z tą aplikacją. 

1. Otwórz program Windows PowerShell ISE z uprawnieniami administratora. Jeśli jeszcze tego nie zrobiono, zainstaluj moduł AZ PowerShell i połącz się z subskrypcją. Jeśli masz wiele subskrypcji, możesz ustawić kontekst bieżącej subskrypcji, jak pokazano w następujących poleceniach:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalowanie i importowanie modułu [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Zaloguj się do usługi Azure AD

Zaloguj się do usługi Azure AD, gdzie została zarejestrowana aplikacja. Użyj polecenia Connect-AzureAD, aby zalogować się na swoje konto, wprowadź poświadczenia konta platformy Azure w wyskakującym oknie. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Otwórz inne wystąpienie środowiska Ise programu Windows PowerShell i uruchom następujące polecenia, aby utworzyć certyfikat z podpisem własnym i odczytać klucz skojarzony z certyfikatem:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Tworzenie poświadczeń opartych na certyfikatach 

Następnie uruchom następujące polecenia, aby uzyskać identyfikator obiektu aplikacji i utworzyć poświadczenia oparte na certyfikatach. W tym przykładzie ustawimy certyfikat na wygaśnięcie po roku, można ustawić go na dowolną wymaganą datę zakończenia.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Powyższe polecenie powoduje wyjście podobne do poniższego zrzutu ekranu:

![Dane wyjściowe tworzenia poświadczeń oparte na certyfikatach](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurowanie konta usługi Azure Cosmos do używania nowej tożsamości

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Przejdź do konta usługi Azure Cosmos, otwórz blok **kontroli dostępu (IAM).**

1. Wybierz **pozycję Dodaj** i dodaj **przypisanie roli**. Dodaj przykładowąapp utworzoną w poprzednim kroku z rolą **współautora,** jak pokazano na poniższym zrzucie ekranu:

   ![Konfigurowanie konta usługi Azure Cosmos do używania nowej tożsamości](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Wybierz **pozycję Zapisz** po wypełnieniu formularza

## <a name="register-your-certificate-with-azure-ad"></a>Rejestrowanie certyfikatu za pomocą usługi Azure AD

Poświadczenia oparte na certyfikatach można skojarzyć z aplikacją kliencką w usłudze Azure AD z witryny Azure portal. Aby skojarzyć poświadczenia, należy przekazać plik certyfikatu z następującymi krokami:

W rejestracji aplikacji platformy Azure dla aplikacji klienckiej:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Otwórz okienko **usługi Azure Active Directory,** przejdź do okienka **rejestracje aplikacji** i otwórz przykładową aplikację utworzoną w poprzednim kroku. 

1. Wybierz **pozycję Certyfikaty & wpisy tajne,** a następnie **przekaż certyfikat**. Przejrzyj plik certyfikatu utworzony w poprzednim kroku, aby przekazać.

1. Wybierz pozycję **Dodaj**. Po przekazaniu certyfikatu są wyświetlane odcisk palca, data rozpoczęcia i wartości wygaśnięcia.

## <a name="access-the-keys-from-powershell"></a>Uzyskiwanie dostępu do kluczy z programu PowerShell

W tym kroku zalogujesz się do platformy Azure przy użyciu aplikacji i certyfikatu, który został utworzony i uzyskać dostęp do kluczy konta usługi Azure Cosmos. 

1. Początkowo wyczyść poświadczenia konta platformy Azure używane do logowania się do konta. Poświadczenia można wyczyścić za pomocą następującego polecenia:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Następnie sprawdź, czy można zalogować się do witryny Azure portal przy użyciu poświadczeń aplikacji i uzyskać dostęp do kluczy usługi Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Poprzednie polecenie wyświetli podstawowe i pomocnicze klucze główne konta usługi Azure Cosmos. Można wyświetlić dziennik aktywności konta usługi Azure Cosmos, aby sprawdzić, czy żądanie kluczy dostępu powiodło się, a zdarzenie jest inicjowane przez aplikację "sampleApp".

![Sprawdzanie poprawności wywołania kluczy pobierz w usłudze Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Uzyskiwanie dostępu do kluczy z aplikacji języka C# 

Można również sprawdzić poprawność tego scenariusza, uzyskując dostęp do kluczy z aplikacji języka C#. Następująca aplikacja konsoli języka C#, która może uzyskać dostęp do kluczy usługi Azure Cosmos DB przy użyciu aplikacji zarejestrowanej w usłudze Active Directory. Przed uruchomieniem kodu należy zaktualizować identyfikator dzierżawcy, identyfikator klienta, certName, nazwę grupy zasobów, identyfikator subskrypcji, szczegóły nazwy konta usługi Azure Cosmos. 

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
    }
}
```

Ten skrypt wyprowadza podstawowe i pomocnicze klucze główne, jak pokazano na poniższym zrzucie ekranu:

![dane wyjściowe aplikacji csharp](./media/certificate-based-authentication/csharp-application-output.png)

Podobnie jak w poprzedniej sekcji, można wyświetlić dziennik aktywności konta usługi Azure Cosmos, aby sprawdzić, czy zdarzenie żądania kluczy pobierz jest inicjowane przez aplikację "sampleApp". 


## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie kluczy usługi Azure Cosmos przy użyciu usługi Azure Key Vault](access-secrets-from-keyvault.md)

* [Linia bazowa zabezpieczeń usługi Azure Cosmos DB](security-baseline.md)
