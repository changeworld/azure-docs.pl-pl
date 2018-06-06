---
title: Użyj usługi Azure Key Vault z aplikacji sieci Web | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby uzyskać więcej informacji, jak używać usługi Azure Key Vault z aplikacji sieci web.
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: adhurwit
ms.openlocfilehash: 3a191c3ee7eea641aab81008a6da801b609fb4c5
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802106"
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Użyj usługi Azure Key Vault z aplikacji sieci Web

## <a name="introduction"></a>Wprowadzenie

Użyj tego samouczka, aby uzyskać więcej informacji, jak używać usługi Azure Key Vault z aplikacji sieci web na platformie Azure. Przeprowadza użytkownika przez proces uzyskiwania dostępu do klucza tajnego z magazynu kluczy Azure, dzięki czemu mogą być używane w aplikacji sieci web.

**Szacowany czas trwania:** 15 minut

Aby uzyskać ogólne informacje na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Identyfikator URI klucza tajnego w magazynie kluczy Azure
* Identyfikator klienta i klucz tajny klienta dla aplikacji sieci web w zarejestrowany w usłudze Azure Active Directory, który ma dostęp do Twojego magazynu kluczy
* Aplikacja sieci web. Firma Microsoft będzie wyświetlane kroki dla aplikacji platformy ASP.NET MVC wdrożona na platformie Azure jako aplikacji sieci Web.

>[!IMPORTANT]
>* Ten przykład jest zależna od starszych sposób ręcznego inicjowania obsługi administracyjnej tożsamości usługi AAD. Obecnie jest nowa funkcja w wersji zapoznawczej o nazwie [zarządzane tożsamości usługi (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), mogą automatycznie obsługiwać tożsamości usługi AAD. Zapoznaj się z następującym przykładem na [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) uzyskać więcej szczegółowych informacji.

> [!NOTE]
>* Jest to, że zostały wykonane wszystkie czynności opisane w [wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md) w tym samouczku, aby mieć identyfikatora URI, klucz tajny i identyfikator klienta i klucz tajny klienta dla aplikacji sieci web.


Aplikacja sieci web, które będą uzyskiwać dostęp do magazynu kluczy jest ten, który jest zarejestrowany w usłudze Azure Active Directory i ma dostęp do Twojego magazynu kluczy. Jeśli nie jest to możliwe, przejdź wstecz, aby zarejestrować aplikację w Samouczek wprowadzający i powtórz kroki opisane na liście.

Ten samouczek jest przeznaczony dla deweloperów sieci web, które podstawy tworzenia aplikacji sieci web na platformie Azure. Aby uzyskać więcej informacji o usłudze Azure Web Apps, zobacz [Przegląd usługi Web Apps](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Dodawanie pakietów NuGet

Istnieją dwa pakiety, które aplikacji sieci web musi mieć zainstalowany.

* Biblioteki uwierzytelniania usługi Active Directory - zawiera metody interakcji z usługą Azure Active Directory i Zarządzanie tożsamościami użytkowników
* Biblioteka magazynu Azure klucza — zawiera metody interakcji z usługą Azure Key Vault

Oba te pakiety można zainstalować przy użyciu konsoli Menedżera pakietów, za pomocą polecenia Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modyfikowanie pliku Web.Config

Dostępne są trzy ustawienia aplikacji, które mają zostać dodane do pliku web.config w następujący sposób.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Jeśli nie ma hosta aplikacji jako aplikacji sieci Web platformy Azure, należy dodać rzeczywistymi wartościami ClientId, klucz tajny klienta i identyfikator URI klucza tajnego do pliku web.config. W przeciwnym razie pozostaw te wartości fikcyjny ponieważ dodamy wartości rzeczywistych w portalu Azure, aby zapewnić dodatkowy poziom zabezpieczeń.

## <a id="gettoken"></a>Dodaj metodę do Uzyskaj Token dostępu

Aby można było używać klucza interfejsu API magazynu należy tokenu dostępu. Klucz magazynu klienta obsługuje wywołania interfejsu API Key Vault, ale należy dostarczyć funkcji, który pobiera token dostępu.  

Poniżej znajduje się kod, aby Uzyskaj token dostępu z usługą Azure Active Directory. Ten kod może przejść w dowolne miejsce w aplikacji. Czy chcesz dodać witryny lub EncryptionHelper klasy.  

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
```

> [!NOTE]
>* Obecnie najprostszym sposobem uwierzytelniania jest użycie nowej funkcji Tożsamość usługi zarządzanej (MSI, Managed Service Identity). Więcej szczegółowych informacji można znaleźć za pomocą następującego linku prowadzącego do przykładu demonstrującego użycie [usługi Key Vault z tożsamością usługi zarządzanej w aplikacji w środowisku .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/). Pomocny będzie też pokrewny [samouczek dotyczący używania tożsamości usługi zarządzanej z usługami App Service i Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* Przy użyciu Identyfikatora klienta i klucz tajny klienta jest innym sposobem uwierzytelniania aplikacji usługi Azure AD. I używania go w aplikacji sieci web umożliwia rozdzielenie obowiązków i większą kontrolę nad zarządzania infrastrukturą kluczy. Jednak zależne umieszczanie klucz tajny klienta w ustawieniach konfiguracji, które może być ryzykowne jako jako wprowadzanie hasła, które mają być chronione w ustawieniach konfiguracji dla niektórych. Zobacz poniżej, aby uzyskać informacje na temat korzystania z Identyfikatorem klienta i certyfikat zamiast identyfikator klienta i klucz tajny klienta do uwierzytelniania aplikacji usługi Azure AD.

## <a id="appstart"></a>Pobierz klucz tajny aplikacji Start

Teraz potrzebujemy kodu w celu wywołania interfejsu API magazynu kluczy i pobrać klucz tajny. Poniższy kod mogą być przełączane dowolnym tak długo, jak jest ona wywoływana przed należy go używać. Zostało umieszczone ten kod w przypadku uruchomienia aplikacji w pliku Global.asax, tak aby jest uruchamiane jeden raz na początku i udostępnia klucz tajny aplikacji.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Dodawanie ustawienia aplikacji w portalu Azure (opcjonalnie)

Jeśli aplikacja sieci Web platformy Azure, można teraz dodawać rzeczywistymi wartościami dla AppSettings w portalu Azure. Dzięki temu rzeczywiste wartości nie będą w pliku web.config, ale chronione za pomocą portalu, gdzie masz dostęp do osobnych możliwości kontroli. Te wartości zostaną zastąpione dla wartości, które zostały wprowadzone w pliku web.config. Upewnij się, że nazwy są takie same.

![Ustawienia aplikacji wyświetlane w portalu Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Uwierzytelnianie przy użyciu certyfikatu zamiast klucz tajny klienta

Innym sposobem uwierzytelniania aplikacji usługi Azure AD jest przy użyciu Identyfikatora klienta oraz certyfikatu zamiast z Identyfikatorem klienta i klucz tajny klienta. Poniżej przedstawiono kroki do używania certyfikatu w aplikacji sieci Web platformy Azure:

1. Uzyskaj lub Utwórz certyfikat
2. Skojarz certyfikat przy użyciu aplikacji do usługi Azure AD
3. Dodawanie kodu do aplikacji sieci Web do używania certyfikatu
4. Dodawanie certyfikatu do aplikacji sieci Web

### <a name="get-or-create-a-certificate"></a>Uzyskaj lub Utwórz certyfikat

Dla naszych celów firma Microsoft będzie wprowadzać certyfikatu testowego. Oto kilka poleceń, których można użyć w wierszu polecenia dewelopera, można utworzyć certyfikatu. Zmień katalog, w którym ma zostać utworzone pliki certyfikatu.  Ponadto do rozpoczęcia i zakończenia Data certyfikatu, użyj data bieżąca plus 1 rok.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Zwróć uwagę na hasło pliku PFX i Data zakończenia (w tym przykładzie: 07/31/2018 i test123). Należy je poniżej.

Aby uzyskać więcej informacji na temat tworzenia certyfikatu testowego, zobacz [porady: tworzenie swój własny testu certyfikatu](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Skojarz certyfikat przy użyciu aplikacji do usługi Azure AD

Teraz, certyfikatu, należy ją skojarzyć z aplikacją usługi Azure AD. Obecnie Usługa portalu Azure nie obsługuje tego przepływu pracy; Można to wykonać za pomocą programu PowerShell. Uruchom następujące polecenia, aby powiązać certyfikat z aplikacji usługi Azure AD:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Po uruchomieniu tych poleceń, można wyświetlić aplikacji w usłudze Azure AD. Podczas wyszukiwania, upewnij się, że wybierz "Moja firma jest właścicielem aplikacji" zamiast "Korzysta z aplikacji firmy" w oknie dialogowym wyszukiwania.

Aby dowiedzieć się więcej na temat obiekty aplikacji usługi Azure AD i ServicePrincipal, zobacz [obiekty aplikacji i nazwy głównej usługi](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Dodawanie kodu do aplikacji sieci Web do używania certyfikatu

Teraz dodamy kod do aplikacji sieci Web dostępu cert i użyć go do uwierzytelniania.

Najpierw jest kod, aby uzyskać dostępu do certyfikatu.

```cs
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

Należy pamiętać, że StoreLocation CurrentUser zamiast LocalMachine. I czy możemy są udostępnia metody Znajdź ' false', ponieważ użyto certyfikatu testowego.

Następnie jest kod, który używa CertificateHelper i tworzy ClientAssertionCertificate, który jest wymagany do uwierzytelniania.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Oto nowy kod można pobrać tokenu dostępu. Spowoduje to zastąpienie metody GetToken w poprzednim przykładzie. Przyznanymi I on inną nazwę dla wygody.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

I umieszczono wszystkie ten kod do klasy witryny projektu aplikacji sieci Web dla łatwość użycia.

Ostatnia zmiana kodu jest metody Application_Start. Najpierw należy wywołać metodę GetCert() załadować ClientAssertionCertificate. A następnie zmienimy metody wywołania zwrotnego, który dostarczamy podczas tworzenia nowego KeyVaultClient. Należy pamiętać, że spowoduje to zastąpienie kod, który wystąpił w poprzednim przykładzie.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Dodawanie certyfikatu do aplikacji sieci Web za pośrednictwem portalu Azure

Dodawanie certyfikatu do aplikacji sieci Web jest procesem dwuetapowym proste. Po pierwsze przejdź do portalu Azure i przejdź do aplikacji sieci Web. W bloku ustawień aplikacji sieci Web kliknij pozycję "domen niestandardowych i protokołu SSL". W bloku, że zostanie otwarta, którą można przekazać certyfikatu, który został utworzony w poprzednim przykładzie KVWebApp.pfx, upewnij się, że użytkownik pamięta hasła dla profilu pfx.

![Dodawanie certyfikatu do aplikacji sieci Web w portalu Azure][2]

Jest ostatnim zadaniem, które należy wykonać, aby dodać ustawienie aplikacji do aplikacji sieci Web, która ma nazwę witryny sieci Web\_obciążenia\_certyfikaty i wartość *. Daje to pewność, że wszystkie certyfikaty zostały załadowane. Jeśli chcesz załadować tylko certyfikaty, które zostały przekazane, można wprowadzić rozdzielana przecinkami lista ich odciski palców.

Aby dowiedzieć się więcej na temat dodawania certyfikatu do aplikacji sieci Web, zobacz [przy użyciu certyfikatów w aplikacjach witryn sieci Web Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Dodawanie certyfikatu do magazynu kluczy jako klucz tajny

Zamiast bezpośrednio przekazywania certyfikatu do usługi sieci Web aplikacji, należy go przechowywać w Key Vault jako klucz tajny i wdrożyć ją stamtąd. To jest procesem dwuetapowym, opisaną w wpis w blogu, [wdrażanie Azure certyfikatu aplikacji sieci Web za pomocą usługi Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Następne kroki

Odwołania dotyczące programowania, zobacz [Azure Key Vault klienta interfejsu API odwołanie w C#](https://msdn.microsoft.com/en-us/library/azure/mt430941.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
