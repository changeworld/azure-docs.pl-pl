---
title: Samouczek dotyczący używania usługi Azure Key Vault z poziomu aplikacji internetowej | Microsoft Docs
description: Z tego samouczka dowiesz się, jak używać usługi Azure Key Vault z poziomu aplikacji internetowej.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: barclayn
ms.openlocfilehash: b66c9912ba0b6508c2beb786d2327efa779c6645
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079467"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Samouczek: używanie usługi Azure Key Vault z poziomu aplikacji internetowej

Z tego samouczka dowiesz się, jak używać usługi Azure Key Vault z poziomu aplikacji internetowej na platformie Azure. Przedstawia on proces uzyskiwania dostępu do wpisu tajnego w usłudze Azure Key Vault w celu użycia w aplikacji internetowej. Następnie na podstawie tego procesu samouczek przedstawia sposób użycia certyfikatu zamiast klucza tajnego klienta. Ten samouczek jest przeznaczony dla deweloperów aplikacji internetowych, którzy mają podstawową wiedzę na temat tworzenia aplikacji internetowych na platformie Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Dodawanie ustawień aplikacji do pliku web.config
> * Dodawanie metody w celu pobrania tokenu dostępu
> * Pobieranie tokenu przy uruchomieniu aplikacji
> * Uwierzytelnianie za pomocą certyfikatu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Identyfikator URI wpisu tajnego w usłudze Azure Key Vault.
* Identyfikator klienta i klucz tajny klienta dla aplikacji internetowej zarejestrowanej w usłudze Azure Active Directory, która ma dostęp do usługi Key Vault.
* Aplikacja internetowa. Ten samouczek zawiera instrukcje dotyczące aplikacji ASP.NET MVC wdrożonej na platformie Azure jako aplikacja internetowa.

Wykonaj czynności opisane w artykule [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md), aby uzyskać identyfikator URI wpisu tajnego, identyfikator klienta i klucz tajny klienta oraz zarejestrować aplikację. Aplikacja internetowa będzie uzyskiwać dostęp do magazynu i musi być zarejestrowana w usłudze Azure Active Directory. Musi również mieć uprawnienia dostępu do usługi Key Vault. Jeśli tak nie jest, wróć do kroku „Rejestrowanie aplikacji” w samouczku „Rozpoczynanie pracy” i powtórz wymienione tam czynności. Aby uzyskać więcej informacji o tworzeniu aplikacji internetowych platformy Azure, zobacz [Przegląd usługi Web Apps](../app-service/app-service-web-overview.md).

Ten przykład wymaga ręcznego aprowizowania tożsamości usługi Azure Active Directory. Najlepiej jednak zamiast tego korzystać z [tożsamości zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md), co umożliwia automatyczne aprowizowanie tożsamości usługi Azure AD. Aby dowiedzieć się więcej, zobacz przykład w witrynie [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) oraz powiązany [samouczek dotyczący usług App Service i Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). Możesz także zapoznać się z dotyczącym usługi Key Vault artykułem [Konfigurowanie aplikacji internetowej platformy Azure w celu odczytu wpisu tajnego z usługi Key Vault — samouczek](tutorial-web-application-keyvault.md).

## <a id="packages"></a>Dodawanie pakietów NuGet

Aplikacja internetowa musi mieć zainstalowane dwa pakiety.

* Active Directory Authentication Library — ten pakiet zawiera metody umożliwiające interakcję z usługą Azure Active Directory oraz zarządzanie tożsamościami użytkowników.
* Biblioteka usługi Azure Key Vault — zawiera metody umożliwiające interakcję z usługą Azure Key Vault.

Oba te pakiety można zainstalować przy użyciu konsoli menedżera pakietów, uruchamiając polecenie Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modyfikowanie pliku web.config

Do pliku web.config należy dodać trzy następujące ustawienia aplikacji. Dla większego bezpieczeństwa będziemy dodawać faktyczne wartości w witrynie Azure Portal.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Dodawanie metody w celu pobrania tokenu dostępu

Aby korzystać z interfejsu API usługi Key Vault, wymagany jest token dostępu. Klient usługi Key Vault obsługuje wywołania interfejsu API usługi Key Vault, ale konieczne jest podanie funkcji pobierającej token dostępu. Poniższy przykładowy kod umożliwia pobranie tokenu dostępu z usługi Azure Active Directory. Ten kod można umieścić w dowolnym miejscu w aplikacji. Ja najczęściej dodaję klasę Utils lub EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Pobieranie wpisu tajnego przy uruchomieniu aplikacji

Teraz potrzebujemy kodu wywołującego interfejs API usługi Key Vault i pobierającego wpis tajny. Poniższy kod można umieścić w dowolnym miejscu, o ile zostanie wywołany, zanim będzie konieczne użycie wpisu. Umieszczam ten kod w zdarzeniu Application Start w pliku Global.asax, dzięki czemu ten kod zostanie wywołany raz przy uruchomieniu i udostępni aplikacji wpis tajny.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Dodawanie ustawień aplikacji w witrynie Azure Portal

Możesz teraz dodać faktyczne wartości ustawień aplikacji internetowej platformy Azure w witrynie Azure Portal. Dzięki wykonaniu tej czynności faktyczne wartości nie znajdą się w pliku web.config — będą chronione w witrynie Azure Portal, gdzie masz możliwość oddzielnej kontroli dostępu. Te wartości będą zastępować wartości wprowadzone w pliku web.config. Należy upewnić się, że ich nazwy są takie same.

![Ustawienia aplikacji wyświetlane w witrynie Azure Portal][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Uwierzytelnianie za pomocą certyfikatu zamiast klucza tajnego klienta

Wyjaśniliśmy już sposób uwierzytelniania aplikacji usługi Azure AD za pomocą identyfikatora klienta i klucza tajnego klienta. Teraz użyjemy identyfikatora klienta i certyfikatu. Aby użyć certyfikatu w aplikacji internetowej platformy Azure, wykonaj następujące czynności:

1. Pobieranie lub tworzenie certyfikatu
2. Kojarzenie certyfikatu z aplikacją usługi Azure AD
3. Dodanie do aplikacji internetowej kodu w celu użycia certyfikatu
4. Dodanie certyfikatu do aplikacji internetowej

### <a name="get-or-create-a-certificate"></a>Pobieranie lub tworzenie certyfikatu

 Na potrzeby tego samouczka utworzymy certyfikat testowy. Ten skrypt umożliwia utworzenie certyfikatu z podpisem własnym. Zmień katalog na ten, w którym chcesz utworzyć pliki certyfikatu.  Jako daty początkowej i końcowej certyfikatu możesz użyć bieżącej daty oraz daty późniejszej o rok.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = 'KVWebApp.pfx'
$CerFilePath = 'KVWebApp.cer'
$DNSName = 'MyComputer.Contoso.com'
$Password = 'MyPassword"'

$StoreLocation = 'CurrentUser' #be aware that LocalMachine requires elevated privileges
$CertBeginDate = Get-Date
$CertExpiryDate = $CertBeginDate.AddYears(1)

$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\$StoreLocation\My" -NotBefore $CertBeginDate -NotAfter $CertExpiryDate -KeySpec Signature
Export-PfxCertificate -cert $Cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $Cert -FilePath $CerFilePath 
```

Zanotuj datę końcową certyfikatu oraz hasło do pliku pfx (w tym przykładzie: 15 maja 2019 r. i MyPassword). Będą potrzebne w poniższym skrypcie. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Kojarzenie certyfikatu z aplikacją usługi Azure AD

Po utworzeniu certyfikatu należy skojarzyć go z aplikacją usługi Azure AD. Skojarzenie można utworzyć za pomocą programu PowerShell. Uruchom następujące polecenia, aby skojarzyć certyfikat z aplikacją usługi Azure AD:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Po uruchomieniu tych poleceń można wyświetlić aplikację w usłudze Azure AD. Wyszukując zarejestrowane aplikacje, wybierz w oknie dialogowym wyszukiwania pozycję **Moje aplikacje**, a nie „Wszystkie aplikacje”. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Dodanie do aplikacji internetowej kodu w celu użycia certyfikatu

Teraz dodamy do aplikacji internetowej kod w celu uzyskania dostępu do certyfikatu i użycia go do uwierzytelniania. 

Pierwszy fragment kodu umożliwia uzyskanie dostępu do certyfikatu. Zwróć uwagę, że parametr StoreLocation ma wartość CurrentUser, a nie LocalMachine. Ponadto w metodzie Find wprowadzamy wartość „false”, ponieważ używamy certyfikatu testowego.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



W kolejnym fragmencie kodu używamy elementu CertificateHelper i tworzymy element ClientAssertionCertificate wymagany do uwierzytelnienia.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Oto nowy kod służący do uzyskania tokenu dostępu. Ten kod zastępuje metodę GetToken z poprzedniego przykładu. Dla przejrzystości nadaję mu inną nazwę. Umieszczam cały ten kod w klasie Utils projektu aplikacji internetowej, aby ułatwić korzystanie z niego.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



Ostatnia zmiana kodu dotyczy metody Application_Start. Najpierw wywołamy metodę GetCert() w celu załadowania elementu ClientAssertionCertificate. Następnie zmienimy metodę wywołania zwrotnego podaną podczas tworzenia nowego elementu KeyVaultClient. Ten kod zastępuje kod, którego używaliśmy w poprzednim przykładzie.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Dodawanie certyfikatu do aplikacji internetowej w witrynie Azure Portal

Dodawanie certyfikatu do aplikacji internetowej to prosty, dwuetapowy proces. Najpierw przejdź do swojej aplikacji internetowej w witrynie Azure Portal. W obszarze Ustawienia aplikacji internetowej kliknij wpis **Ustawienia protokołu SSL**. Po jego otwarciu przekaż certyfikat utworzony w poprzednim przykładzie, KVWebApp.pfx. Upewnij się, że pamiętasz hasło do pliku pfx.

![Dodawanie certyfikatu do aplikacji internetowej w witrynie Azure Portal][2]

Ostatnim zadaniem do wykonania jest dodanie do aplikacji internetowej ustawienia aplikacji o nazwie WEBSITE\_LOAD\_CERTIFICATES, z wartością *. To zapewni załadowanie wszystkich certyfikatów. Jeśli chcesz załadować tylko certyfikaty przekazane przez Ciebie, wprowadź listę ich odcisków palca oddzielonych przecinkami.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy usługa App Service, magazyn kluczy i aplikacja usługi Azure AD używane w tym samouczku nie będą już potrzebne, usuń je.  


## <a id="next"></a>Następne kroki
> [!div class="nextstepaction"]
>[Dokumentacja interfejsu API zarządzania usługi Azure Key Vault](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 