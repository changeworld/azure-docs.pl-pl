---
title: Bezpieczne łączenie z klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób uwierzytelniania dostępu klienta do klastra usługi sieć szkieletowa usług oraz sposobem zabezpieczenia komunikacji między klientami i klastra.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2018
ms.author: ryanwi
ms.openlocfilehash: f2a181fbae8ab1e08669021c42c5b4be08f66172
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem

Gdy klient nawiąże połączenie z węzłem klastra usługi sieć szkieletowa, klient może być uwierzytelniane i bezpieczne komunikacji za pomocą certyfikatu zabezpieczeń lub Azure Active Directory (AAD). To uwierzytelnianie gwarantuje, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożone aplikacje i wykonywanie zadań zarządzania.  Certyfikat lub zabezpieczeń usługi AAD musi być wcześniej włączone w klastrze podczas tworzenia klastra.  Aby uzyskać więcej informacji na temat scenariuszy zabezpieczeń klastra zobacz [klastra zabezpieczeń](service-fabric-cluster-security.md). Jeśli łączysz się z klastrem zabezpieczone za pomocą certyfikatów, [skonfigurować certyfikat klienta](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) na komputerze, który łączy się z klastrem. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Nawiązać bezpiecznego klastra przy użyciu wiersza polecenia platformy Azure Usługa sieci szkieletowej (sfctl)

Istnieje kilka różnych sposobów łączenia do bezpiecznego klastra przy użyciu usługi sieci szkieletowej interfejsu wiersza polecenia (sfctl). Podczas uwierzytelniania przy użyciu certyfikatu klienta szczegóły certyfikatu muszą być zgodne z certyfikatem wdrożonym w węzłach klastra. Jeśli certyfikat ma urzędy certyfikacji (CA), należy również określić zaufanych urzędów certyfikacji.

Możesz połączyć się przy użyciu klastra `sfctl cluster select` polecenia.

Certyfikaty klienta można określić w dwóch różnych fashions, jako para certyfikatu i klucza, lub jako plik pem pojedynczego. Dla chronionych hasłem `pem` pliki, zostanie wyświetlony monit automatycznie o wprowadzenie hasła.

Aby określić certyfikat klienta jako plik pem, określ ścieżkę do pliku w `--pem` argumentu. Na przykład:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Pem, które pliki wyświetli monit o podanie hasła przed uruchomieniem polecenia chroniony hasłem.

Aby określić certyfikatu, klucza Użyj pary `--cert` i `--key` argumentów, aby określić ścieżki do plików do każdego odpowiedniego pliku.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Czasami certyfikatów służących do zabezpieczania klastrów testu lub deweloperów nie sprawdzanie poprawności certyfikatu. Aby pominąć weryfikację certyfikatu, określ `--no-verify` opcji. Na przykład:

> [!WARNING]
> Nie używaj `no-verify` podczas nawiązywania połączenia z klastrów sieci szkieletowej usług w środowisku produkcyjnym.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Ponadto można określić ścieżek do katalogów zaufane certyfikaty urzędu certyfikacji lub poszczególne certyfikaty. Aby określić tych ścieżek, użyj `--ca` argumentu. Na przykład:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Po nawiązaniu połączenia powinny mieć możliwość [innych poleceń sfctl](service-fabric-cli.md) wchodzić w interakcje z klastrem.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Połącz z klastrem przy użyciu programu PowerShell
Przed wykonaniem operacji w klastrze za pomocą programu PowerShell, najpierw nawiąż połączenie z klastrem. Połączenia klastra jest używany do wszystkich kolejnych poleceń w danej sesji programu PowerShell.

### <a name="connect-to-an-unsecure-cluster"></a>Łączenie z klastrem niezabezpieczone

Nawiązać klastra niezabezpieczone, podaj adres punktu końcowego klastra, aby **Connect-ServiceFabricCluster** polecenia:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Nawiązać bezpiecznego klastra przy użyciu usługi Azure Active Directory

Do nawiązania bezpiecznego klastra, który używa usługi Azure Active Directory do autoryzowania dostępu administratora klastra, podaj odcisk palca certyfikatu klastra i użyj *usługi AzureActiveDirectory* flagi.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązać bezpiecznego klastra przy użyciu certyfikatu klienta
Uruchom następujące polecenie programu PowerShell do nawiązania bezpiecznego klastra, który używa certyfikatów klienta do autoryzowania dostępu administratora. 

#### <a name="connect-using-certificate-common-name"></a>Połącz, używając nazwa pospolita certyfikatu
Podaj klastra Nazwa pospolita certyfikatu i nazwa pospolita certyfikatu klienta, które zostały przyznane uprawnienia do zarządzania klastrem. Szczegóły certyfikatu musi odpowiadać certyfikatu w węzłach klastra.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*Wartości: ServerCommonName* jest nazwa pospolita certyfikatu serwera zainstalowanych w węzłach klastra. *FindValue* jest nazwa pospolita certyfikatu klienta administratora. Po wypełnieniu parametry polecenia wygląda następująco:
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

#### <a name="connect-using-certificate-thumbprint"></a>Połącz, używając odcisku palca certyfikatu
Podaj odcisk palca certyfikatu klastra i odcisk palca certyfikatu klienta, które zostały przyznane uprawnienia do zarządzania klastrem. Szczegóły certyfikatu musi odpowiadać certyfikatu w węzłach klastra.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* jest odcisk palca certyfikatu serwera zainstalowanych w węzłach klastra. *FindValue* jest odcisk palca certyfikatu klienta administratora.  Po wypełnieniu parametry polecenia wygląda następująco:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Nawiązać bezpiecznego klastra przy użyciu usługi Active Directory systemu Windows
Jeśli klaster autonomiczny jest wdrażana przy użyciu zabezpieczeń usługi AD, połącz się z klastrem przez dodanie przełącznika "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Połącz z klastrem przy użyciu interfejsów API klienta fabricclient z rolą
Zawiera zestaw SDK sieci szkieletowej usług [klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) klasy do zarządzania klastrem. Aby używać interfejsów API klienta fabricclient z rolą, Pobierz pakiet Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Łączenie z klastrem niezabezpieczone

Aby podłączyć się do zdalnego klastra niezabezpieczoną, Utwórz wystąpienie klienta fabricclient z rolą i podaj adres klastra:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Kod, który działa z w klastrze, na przykład w niezawodnej usługi, utworzyć klienta fabricclient z rolą *bez* określający adres klastra. Klienta fabricclient z rolą łączy się z bramą zarządzania lokalnego na węźle, który jest obecnie uruchomiony jest kod, unikając przeskoku dodatkowe sieci.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązać bezpiecznego klastra przy użyciu certyfikatu klienta

Węzły w klastrze musi mieć prawidłowe certyfikaty, których nazwa pospolita lub nazwa DNS w sieci SAN jest wyświetlana w [właściwości RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials#System_Fabric_X509Credentials_RemoteCommonNames) ustawiać [klienta fabricclient z rolą](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Następujące ten proces umożliwia uwierzytelnianie wzajemne między klientem a węzłów klastra.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Łączenie z klastrem bezpieczny, interaktywnego przy użyciu usługi Azure Active Directory

W poniższym przykładzie użyto usługi Azure Active Directory dla tożsamości i serwer certyfikat klienta dla tożsamości serwera.

Okno dialogowe automatycznie wyskakującej do interakcyjnego logowania po połączeniu się z klastrem.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Łączenie z klastrem bezpieczny, nieinteraktywnie przy użyciu usługi Azure Active Directory

Poniższy przykład zależy od Microsoft.IdentityModel.Clients.ActiveDirectory, wersja: 2.19.208020213.

Aby uzyskać więcej informacji na przejęcie tokenu usługi AAD, zobacz [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Nawiązać bezpiecznego klastra bez uprzedniego metadanych wiedzy przy użyciu usługi Azure Active Directory

W poniższym przykładzie użyto nieinterakcyjnym nabycia tokenu, ale same podejście może służyć do tworzenia środowisko niestandardowych interakcyjne nabycia tokenu. Metadane usługi Azure Active Directory wymagane uzyskania tokenu są odczytywane z konfiguracji klastra.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Łączenie z klastrem bezpieczny, za pomocą Eksploratora usługi sieć szkieletowa
Aby osiągnąć [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) danego klastra można wskazać w przeglądarce do:

`http://<your-cluster-endpoint>:19080/Explorer`

Pełny adres URL jest również dostępna w okienku essentials klastra w portalu Azure.

W celu nawiązania bezpiecznego klastra w systemie Windows lub OS X przy użyciu przeglądarki, można zaimportować certyfikatu klienta, a przeglądarka wyświetli monit o certyfikat używany do łączenia się z klastrem.  Na komputerach z systemem Linux certyfikat będzie mieć do zaimportowania przy użyciu ustawień przeglądarki Zaawansowane (każdą przeglądarkę ma różne mechanizmy) i wskaż lokalizację certyfikatu na dysku.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Nawiązać bezpiecznego klastra przy użyciu usługi Azure Active Directory

Aby połączyć się z klastra, który jest zabezpieczony przy użyciu usługi AAD, punktu przeglądarkę, aby:

`https://<your-cluster-endpoint>:19080/Explorer`

Są automatycznie monitowani do logowania się przy użyciu usługi AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Nawiązać bezpiecznego klastra przy użyciu certyfikatu klienta

Aby połączyć się z klastra, który jest zabezpieczony za pomocą certyfikatów, punkt przeglądarkę, aby:

`https://<your-cluster-endpoint>:19080/Explorer`

Są automatycznie monitowani o wybranie certyfikatu klienta.

<a id="connectsecureclustersetupclientcert"></a>
## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Konfigurowanie certyfikatu klienta na komputerze zdalnym
Co najmniej dwa certyfikaty powinny być używane do zabezpieczania klastra, jeden dla klastra i serwera certyfikat i drugi dla dostępu klientów.  Zalecamy również użycie dodatkowych dodatkowej certyfikatów i certyfikaty dostępu klienta.  Do zabezpieczenia komunikacji między klientem a węzłem klastra przy użyciu certyfikatu zabezpieczeń, należy najpierw uzyskać i zainstalować certyfikat klienta. Można zainstalować certyfikatu do osobistego (Mój) magazynu komputera lokalnego lub bieżącego użytkownika.  Należy również odcisk palca certyfikatu serwera, aby klient mógł uwierzytelnić klastra.

Uruchom następujące polecenie cmdlet programu PowerShell, aby skonfigurować certyfikat klienta na komputerze, z którego można uzyskać dostęp do klastra.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Jeśli certyfikat z podpisem własnym, należy zaimportować w magazynie "Zaufane osoby" na komputerze, zanim ten certyfikat służy do nawiązania bezpiecznego klastra.

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

## <a name="next-steps"></a>Kolejne kroki

* [Proces uaktualniania klastra sieci szkieletowej usług oraz oczekiwań od użytkownika](service-fabric-cluster-upgrade.md)
* [Zarządzanie aplikacji usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Wprowadzenie modelu kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)
* [Zabezpieczenia aplikacji i Uruchom jako](service-fabric-application-runas-security.md)
* [Getting started with Service Fabric CLI (Wprowadzenie do interfejsu wiersza polecenia usługi Service Fabric)](service-fabric-cli.md)
