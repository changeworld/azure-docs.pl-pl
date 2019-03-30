---
title: Bezpieczne łączenie się z klastrem usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uwierzytelniania klientów dostępu do klastra usługi Service Fabric oraz do zabezpieczania komunikacji między klientami a klastrem.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2019
ms.author: aljo
ms.openlocfilehash: 42c8fa15c6b1e7c98ae47180bec5cc61236a7c44
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666532"
---
# <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Gdy klient nawiąże połączenie z węzłem klastra usługi Service Fabric, klient może być uwierzytelnione i bezpieczną komunikację, do których nawiązane przy użyciu certyfikatu zabezpieczeń lub Azure Active Directory (AAD). To uwierzytelnianie zapewnia, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożonych aplikacji i wykonywać zadania zarządzania.  Certyfikat lub zabezpieczeń usługi AAD musi być wcześniej włączone w klastrze podczas tworzenia klastra.  Aby uzyskać więcej informacji na temat scenariusze zabezpieczeń klastra, zobacz [klastra zabezpieczeń](service-fabric-cluster-security.md). Jeśli łączysz się z klastrem zabezpieczony za pomocą certyfikatów, [skonfigurować certyfikat klienta](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na komputerze, który nawiązuje połączenie z klastrem. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu wiersza polecenia platformy Azure usługi Service Fabric (sfctl)

Istnieje kilka różnych sposobów, aby nawiązać połączenie z bezpiecznym klastrem za pomocą usługi Service FABRIC (sfctl). Podczas uwierzytelniania przy użyciu certyfikatu klienta szczegóły certyfikatu muszą być zgodne z certyfikatem wdrożonym w węzłach klastra. Jeśli certyfikat ma urzędy certyfikacji (CA), należy również określić zaufanych urzędów certyfikacji.

Możesz nawiązać połączenie przy użyciu klastra `sfctl cluster select` polecenia.

Certyfikaty klienta można określić w dwóch różnych fashions, jako para certyfikat i klucz lub jako pojedynczy plik PFX. Dla plików PEM chroniony hasłem, zostanie możesz automatycznie monit o wprowadzenie hasła. Jeśli użytkownik uzyskał certyfikat klienta jako plik PFX, należy najpierw przekonwertować plik PFX na plik PEM przy użyciu następującego polecenia. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Jeśli plik PFX nie jest chroniony hasłem, użyj - passin — dostęp próbny: ostatniego parametru.

Aby określić certyfikat klienta jako plik pem, określ ścieżkę pliku w `--pem` argumentu. Na przykład:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pem, które pliki wyświetli monit o podanie hasła przed uruchomieniem polecenia żadnych chronionych hasłem.

Aby określić certyfikat, klucz Użyj pary `--cert` i `--key` argumenty do określenia ścieżki do plików do każdego odpowiedniego pliku.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Czasami certyfikatów służących do zabezpieczania klastry testowe lub deweloperskie wystąpi niepowodzenie weryfikacji certyfikatu. Aby pominąć weryfikację certyfikatu, określ `--no-verify` opcji. Na przykład:

> [!WARNING]
> Nie używaj `no-verify` podczas nawiązywania połączenia z klastrami usługi Service Fabric w środowisku produkcyjnym.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Ponadto można określić ścieżki do katalogów zaufane certyfikaty urzędu certyfikacji lub poszczególne certyfikaty. Aby określić ścieżki, użyj `--ca` argumentu. Na przykład:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po nawiązaniu połączenia, powinno być możliwe do [innych poleceń interfejsu sfctl](service-fabric-cli.md) do interakcji z klastrem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Nawiąż połączenie z klastrem przy użyciu programu PowerShell
Przed wykonaniem operacji w klastrze za pomocą programu PowerShell, najpierw nawiąż połączenie z klastrem. Połączenie klastra jest używany dla wszystkich kolejnych poleceń w danej sesji programu PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Nawiązać połączenie z niezabezpieczonym klastrem

Aby połączyć się niezabezpieczonym klastrem, należy podać adres punktu końcowego klastra z **Connect-ServiceFabricCluster** polecenia:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu usługi Azure Active Directory

Aby połączyć z zabezpieczonym klastrem, który używa usługi Azure Active Directory do autoryzowania dostępu administratora klastra, odcisk palca certyfikatu klastra i *usługi AzureActiveDirectory* flagi.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu certyfikatu klienta
Uruchom następujące polecenie programu PowerShell, aby nawiązać połączenie z zabezpieczonym klastrem, korzystającą z certyfikatów klienta w celu autoryzowania dostępu administratora. 

#### <a name="connect-using-certificate-common-name"></a>Nawiązywanie połączenia przy użyciu nazwy pospolitej certyfikatu
Podaj nazwy pospolitej certyfikatu klastra i nazwa pospolita certyfikatu klienta, który ma odpowiednie uprawnienia do zarządzania klastrem. Szczegóły certyfikatu muszą być zgodne certyfikatu w węzłach klastra.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* jest to nazwa pospolita certyfikatu serwera zainstalowanego na węzłach klastra. *FindValue* jest to nazwa pospolita certyfikatu klienta administratora. Po wypełnieniu parametry polecenia wygląda następująco:
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

#### <a name="connect-using-certificate-thumbprint"></a>Nawiązywanie połączenia przy użyciu odcisku palca certyfikatu
Podaj odcisk palca certyfikatu klastra i odcisk palca certyfikatu klienta, który ma odpowiednie uprawnienia do zarządzania klastrem. Szczegóły certyfikatu muszą być zgodne certyfikatu w węzłach klastra.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* to odcisk palca certyfikatu serwera zainstalowanego na węzłach klastra. *FindValue* to odcisk palca certyfikatu klienta administratora.  Po wypełnieniu parametry polecenia wygląda następująco:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu usługi Windows Active Directory
Jeśli klastra autonomicznego jest wdrażana przy użyciu zabezpieczeń AD, połącz się z klastrem, dodając przełącznik "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Nawiąż połączenie z klastrem przy użyciu interfejsów API FabricClient
Zestaw SDK usługi Service Fabric udostępnia [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) klasy do zarządzania klastrem. Aby korzystać z interfejsów API FabricClient, Pobierz pakiet Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Nawiązać połączenie z niezabezpieczonym klastrem

Aby nawiązać połączenie zdalne niezabezpieczonym klastrem, Utwórz wystąpienie FabricClient i podaj adres klastra:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Dla kodu, która jest uruchamiana z w ramach klastra, na przykład w usługi Reliable Service Utwórz FabricClient *bez* określając adres klastra. FabricClient łączy się z bramy zarządzania lokalnego, w węźle, który obecnie działa kod, unikając przeskoków sieciowych.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu certyfikatu klienta

Węzły w klastrze musi mieć prawidłowe certyfikaty, których nazwa pospolita lub nazwę DNS w sieci SAN, zostanie wyświetlony w [właściwość RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) nastavit [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Następujący proces umożliwia uwierzytelnianie wzajemne między klientem i węzłów klastra.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Nawiązać połączenie z zabezpieczonym klastrem interaktywnie przy użyciu usługi Azure Active Directory

W poniższym przykładzie użyto usługi Azure Active Directory dla tożsamości i serwera certyfikatu klienta dla serwera tożsamości.

Okno dialogowe automatycznie pojawia się dla logowania interaktywnego po połączeniu się z klastrem.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Nawiązać połączenie z zabezpieczonym klastrem nieinterakcyjny za pomocą usługi Azure Active Directory

Poniższy przykład opiera się na Microsoft.IdentityModel.Clients.ActiveDirectory, wersja: 2.19.208020213.

Aby uzyskać więcej informacji na temat uzyskanie tokenu usługi AAD, zobacz [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Łączenie z zabezpieczonym klastrem bez wiedzy wcześniejsze metadanych przy użyciu usługi Azure Active Directory

W poniższym przykładzie użyto nieinterakcyjnych uzyskanie tokenu, ale to samo podejście może służyć do tworzenia środowiska niestandardowego interaktywne uzyskanie tokenu. Metadane usługi Azure Active Directory służące do tokenu są odczytywane z konfiguracji klastra.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu narzędzia Service Fabric Explorer
Aby osiągnąć [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) dla danego klastra należy wskazać w przeglądarce do:

`http://<your-cluster-endpoint>:19080/Explorer`

Pełny adres URL jest również dostępna w okienku podstawy klastra w witrynie Azure Portal.

W przypadku nawiązywania połączenia z zabezpieczonym klastrem w Windows lub OS X przy użyciu przeglądarki, można zaimportować certyfikatu klienta i przeglądarka wyświetli monit o certyfikat do użycia podczas łączenia się z klastrem.  Na komputerach z systemem Linux certyfikat musi zaimportować przy użyciu ustawień przeglądarki Zaawansowane (każdą przeglądarkę ma różne mechanizmy) i wskaż lokalizację certyfikatu na dysku. Odczyt [skonfigurować certyfikat klienta](#connectsecureclustersetupclientcert) Aby uzyskać więcej informacji.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu usługi Azure Active Directory

Aby połączyć z klastra, który jest zabezpieczony za pomocą usługi AAD, wskazać w przeglądarce do:

`https://<your-cluster-endpoint>:19080/Explorer`

Są automatycznie monitowani logowanie się przy użyciu usługi AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązać połączenie z zabezpieczonym klastrem przy użyciu certyfikatu klienta

Aby połączyć z klastra, który jest zabezpieczony za pomocą certyfikatów, punkt przeglądarkę, aby:

`https://<your-cluster-endpoint>:19080/Explorer`

Są automatycznie monitowani o wybranie certyfikatu klienta.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurowanie certyfikatu klienta na komputerze zdalnym

Co najmniej dwa certyfikaty powinny być używane do zabezpieczania klastra, jeden dla certyfikatu klastra i serwera, a drugi dla dostępu klientów.  Firma Microsoft zaleca również korzystanie z dodatkowych dodatkowych certyfikatów i certyfikaty dostępu klienta.  Aby zabezpieczyć komunikację między klientem a węzłem klastra przy użyciu certyfikatu zabezpieczeń, należy najpierw uzyskać i zainstalować certyfikat klienta. Certyfikat może być zainstalowany w osobistym (Mój) magazynie komputera lokalnego lub bieżącego użytkownika.  Należy również odcisk palca certyfikatu serwera, aby klient mógł uwierzytelnić klastra.

* W systemie Windows: kliknij dwukrotnie plik PFX i postępuj zgodnie z monitami, aby zainstalować certyfikat w magazynie osobistym `Certificates - Current User\Personal\Certificates`. Alternatywnie można użyć polecenia programu PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Jeśli certyfikat z podpisem własnym, należy zaimportować go do magazynu "Zaufane osoby" na komputerze, korzystać z tego certyfikatu, aby nawiązać połączenie z zabezpieczonym klastrem.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Na komputerze Mac: kliknij dwukrotnie plik PFX, a następnie postępuj zgodnie z monitami, aby zainstalować certyfikat w pęku kluczy.

## <a name="next-steps"></a>Kolejne kroki

* [Proces uaktualniania klastra usługi Service Fabric i oczekiwania ze strony użytkownika](service-fabric-cluster-upgrade.md)
* [Zarządzanie aplikacje usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Wprowadzenie do modelu kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)
* [Bezpieczeństwo aplikacji i Uruchom jako](service-fabric-application-runas-security.md)
* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
