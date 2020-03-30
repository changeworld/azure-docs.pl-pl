---
title: Bezpieczne łączenie się z klastrem sieci szkieletowej usług Azure
description: W tym artykule opisano sposób uwierzytelniania dostępu klienta do klastra sieci szkieletowej usług i zabezpieczania komunikacji między klientami a klastrem.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258579"
---
# <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Gdy klient łączy się z węzłem klastra sieci szkieletowej usług, klient może być uwierzytelniony i zabezpieczyć komunikację ustanowioną przy użyciu zabezpieczeń certyfikatów lub usługi Azure Active Directory (AAD). To uwierzytelnianie gwarantuje, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożonych aplikacji oraz wykonywać zadania zarządzania.  Podczas tworzenia klastra certyfikat lub zabezpieczenia usługi AAD muszą być wcześniej włączone w klastrze.  Aby uzyskać więcej informacji na temat scenariuszy zabezpieczeń [klastra,](service-fabric-cluster-security.md)zobacz Zabezpieczenia klastra . Jeśli łączysz się z klastrem zabezpieczonym certyfikatami, [skonfiguruj certyfikat klienta](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na komputerze, który łączy się z klastrem. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Łączenie się z bezpiecznym klastrem przy użyciu interfejsu wiersza polecenia sieci szkieletowej usługi Azure (sfctl)

Istnieje kilka różnych sposobów łączenia się z bezpiecznym klastrem przy użyciu interfejsu wiersza polecenia sieci szkieletowej usług (sfctl). Podczas uwierzytelniania przy użyciu certyfikatu klienta szczegóły certyfikatu muszą być zgodne z certyfikatem wdrożonym w węzłach klastra. Jeśli certyfikat ma urzędy certyfikacji ( CA), należy dodatkowo określić zaufane urzędy certyfikacji.

Za pomocą polecenia można `sfctl cluster select` połączyć się z klastrem.

Certyfikaty klienta można określić w dwóch różnych formach: jako parę certyfikatów i kluczy lub jako pojedynczy plik PFX. W przypadku chronionych hasłem plików PEM zostaniesz automatycznie poproszony o wprowadzenie hasła. Jeśli certyfikat klienta został uzyskany jako plik PFX, należy najpierw przekonwertować plik PFX na plik PEM za pomocą następującego polecenia. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Jeśli plik .pfx nie jest chroniony hasłem, użyj -passin pass: dla ostatniego parametru.

Aby określić certyfikat klienta jako plik pem, `--pem` określ ścieżkę pliku w argumie. Przykład:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Chronione hasłem pliki pem będą monitować o hasło przed uruchomieniem dowolnego polecenia.

Aby określić certyfikat, para `--cert` kluczy użyj argumentów i, `--key` aby określić ścieżki pliku do każdego odpowiedniego pliku.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Czasami certyfikaty używane do zabezpieczania klastrów testowych lub deweloperskich nie sprawdzają poprawności certyfikatów. Aby pominąć weryfikację `--no-verify` certyfikatu, określ tę opcję. Przykład:

> [!WARNING]
> Nie należy `no-verify` używać tej opcji podczas łączenia się z klastrami produkcyjnych sieci szkieletowej usług.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Ponadto można określić ścieżki do katalogów zaufanych certyfikatów urzędu certyfikacji lub poszczególnych certyfikatów. Aby określić te ścieżki, użyj argumentu. `--ca` Przykład:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po nawiązaniu połączenia powinno być możliwe [uruchomienie innych poleceń sfctl](service-fabric-cli.md) w celu interakcji z klastrem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Łączenie się z klastrem przy użyciu programu PowerShell
Przed wykonaniem operacji w klastrze za pośrednictwem programu PowerShell należy najpierw ustanowić połączenie z klastrem. Połączenie klastra jest używane dla wszystkich kolejnych poleceń w danej sesji programu PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Łączenie się z niezabezpieczonym klastrem

Aby połączyć się z niezabezpieczonym klastrem, podaj adres punktu końcowego klastra do polecenia **Connect-ServiceFabricCluster:**

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Łączenie się z bezpiecznym klastrem przy użyciu usługi Azure Active Directory

Aby połączyć się z bezpiecznym klastrem, który używa usługi Azure Active Directory do autoryzowania dostępu administratora klastra, podaj odcisk palca certyfikatu klastra i użyj flagi *AzureActiveDirectory.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Łączenie się z bezpiecznym klastrem przy użyciu certyfikatu klienta
Uruchom następujące polecenie programu PowerShell, aby połączyć się z bezpiecznym klastrem, który używa certyfikatów klientów do autoryzowania dostępu administratora. 

#### <a name="connect-using-certificate-common-name"></a>Łączenie przy użyciu nazwy pospolitej certyfikatu
Podaj nazwę pospolitą certyfikatu klastra i wspólną nazwę certyfikatu klienta, któremu przyznano uprawnienia do zarządzania klastrem. Szczegóły certyfikatu muszą być zgodne z certyfikatem w węzłach klastra.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* to nazwa pospolita certyfikatu serwera zainstalowanego w węzłach klastra. *FindValue* to nazwa pospolita certyfikatu klienta administratora. Po wypełnieniu parametrów polecenie wygląda następująco:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Łączenie za pomocą odcisku palca certyfikatu
Podaj odcisk palca certyfikatu klastra i odcisk palca certyfikatu klienta, któremu przyznano uprawnienia do zarządzania klastrem. Szczegóły certyfikatu muszą być zgodne z certyfikatem w węzłach klastra.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* to odcisk palca certyfikatu serwera zainstalowanego w węzłach klastra. *FindValue* to odcisk palca certyfikatu klienta administratora.  Po wypełnieniu parametrów polecenie wygląda następująco:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Łączenie się z bezpiecznym klastrem przy użyciu usługi Windows Active Directory
Jeśli klaster autonomiczny jest wdrażany przy użyciu zabezpieczeń usługi AD, połącz się z klastrem, dołączając przełącznik "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Łączenie się z klastrem przy użyciu interfejsów API FabricClient
SDK sieci szkieletowej usług udostępnia [klasę FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) do zarządzania klastrem. Aby użyć interfejsów API FabricClient, pobierz pakiet Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Łączenie się z niezabezpieczonym klastrem

Aby połączyć się ze zdalnym klastrem niezabezpieczonym, utwórz wystąpienie FabricClient i podaj adres klastra:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Dla kodu, który jest uruchomiony z wewnątrz klastra, na przykład w niezawodnej usługi, należy utworzyć FabricClient *bez* określania adresu klastra. FabricClient łączy się z lokalną bramą zarządzania w węźle, na których jest obecnie uruchomiony kod, unikając dodatkowego przeskoku sieciowego.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Łączenie się z bezpiecznym klastrem przy użyciu certyfikatu klienta

Węzły w klastrze muszą mieć prawidłowe certyfikaty, których nazwa pospolita lub nazwa DNS w sieci SAN jest wyświetlana we [właściwości RemoteCommonNames ustawionej](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) na [stronie FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Po tym procesie umożliwia wzajemne uwierzytelnianie między klientem a węzłami klastra.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Interakcyjnie łączenie się z bezpiecznym klastrem przy użyciu usługi Azure Active Directory

W poniższym przykładzie użyto usługi Azure Active Directory dla tożsamości klienta i certyfikatu serwera dla tożsamości serwera.

Okno dialogowe zostanie automatycznie wyświetlone w celu interaktywnego logowania po nawiązaniu połączenia z klastrem.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Łączenie się z bezpiecznym klastrem nieinteraktywnie przy użyciu usługi Azure Active Directory

Poniższy przykład opiera się na Microsoft.IdentityModel.Clients.ActiveDirectory, wersja: 2.19.208020213.

Aby uzyskać więcej informacji na temat pozyskiwania tokenów usługi AAD, zobacz [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Łączenie się z bezpiecznym klastrem bez wcześniejszej wiedzy o metadanych przy użyciu usługi Azure Active Directory

W poniższym przykładzie użyto nabycia tokenu nieinterakcyjnego, ale tego samego podejścia można użyć do tworzenia środowiska pozyskiwania tokenów interaktywnych. Metadane usługi Azure Active Directory potrzebne do pozyskiwania tokenów są odczytywane z konfiguracji klastra.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Łączenie się z bezpiecznym klastrem przy użyciu Eksploratora sieci szkieletowej usług
Aby uzyskać do Niego dotarcie do [Eksploratora sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md) dla danego klastra, wskaż przeglądarkę na:

`http://<your-cluster-endpoint>:19080/Explorer`

Pełny adres URL jest również dostępny w okienku podstawowe klastra w witrynie Azure portal.

Aby połączyć się z bezpiecznym klastrem w systemie Windows lub OS X za pomocą przeglądarki, można zaimportować certyfikat klienta, a przeglądarka wyświetli monit o użycie certyfikatu do nawiązania połączenia z klastrem.  Na komputerach z systemem Linux certyfikat będzie musiał zostać zaimportowany przy użyciu zaawansowanych ustawień przeglądarki (każda przeglądarka ma inne mechanizmy) i wskażenie go do lokalizacji certyfikatu na dysku. Przeczytaj artykuł [Konfigurowanie certyfikatu klienta,](#connectsecureclustersetupclientcert) aby uzyskać więcej informacji.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Łączenie się z bezpiecznym klastrem przy użyciu usługi Azure Active Directory

Aby połączyć się z klastrem zabezpieczonym za pomocą usługi AAD, wskaż przeglądarkę na:

`https://<your-cluster-endpoint>:19080/Explorer`

Zostaniesz automatycznie wyświetlony monit o zalogowanie się za pomocą aad.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Łączenie się z bezpiecznym klastrem przy użyciu certyfikatu klienta

Aby połączyć się z klastrem zabezpieczonym certyfikatami, wskaż przeglądarkę na:

`https://<your-cluster-endpoint>:19080/Explorer`

Zostaniesz automatycznie wyświetlony monit o wybranie certyfikatu klienta.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurowanie certyfikatu klienta na komputerze zdalnym

Do zabezpieczania klastra powinny być używane co najmniej dwa certyfikaty, jeden dla certyfikatu klastra i serwera, a drugi dla dostępu klienta.  Zaleca się również użycie dodatkowych certyfikatów pomocniczych i certyfikatów dostępu klienta.  Aby zabezpieczyć komunikację między klientem a węzłem klastra przy użyciu zabezpieczeń certyfikatów, należy najpierw uzyskać i zainstalować certyfikat klienta. Certyfikat można zainstalować w magazynie osobistym (Mój) komputera lokalnego lub bieżącego użytkownika.  Potrzebny jest również odcisk palca certyfikatu serwera, aby klient mógł uwierzytelnić klaster.

* W systemie Windows: kliknij dwukrotnie plik PFX i postępuj zgodnie z monitami, aby zainstalować certyfikat w magazynie osobistym `Certificates - Current User\Personal\Certificates`. Alternatywnie można użyć polecenia programu PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Jeśli jest to certyfikat z podpisem własnym, należy zaimportować go do magazynu "zaufane osoby" komputera, zanim będzie można użyć tego certyfikatu do nawiązania połączenia z bezpiecznym klastrem.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Na komputerach Mac: kliknij dwukrotnie plik PFX, a następnie postępuj zgodnie z monitami, aby zainstalować certyfikat w pęku kluczy.

## <a name="next-steps"></a>Następne kroki

* [Proces uaktualniania klastra sieci szkieletowej usług i oczekiwania od Ciebie](service-fabric-cluster-upgrade.md)
* [Zarządzanie aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Wprowadzenie do modelu kondycji sieci szkieletowej usługi](service-fabric-health-introduction.md)
* [Zabezpieczenia aplikacji i runAs](service-fabric-application-runas-security.md)
* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
