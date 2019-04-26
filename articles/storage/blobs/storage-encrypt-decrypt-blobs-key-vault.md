---
title: 'Samouczek: Szyfrowanie i odszyfrowywanie obiektów blob w usłudze Azure Storage przy użyciu usługi Azure Key Vault | Dokumentacja firmy Microsoft'
description: Jak szyfrowanie i odszyfrowywanie obiektów blob za pomocą szyfrowania po stronie klienta dla usługi Microsoft Azure Storage z usługą Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c7a185e1c7f271cdca0c688ce7838f6390594da5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330333"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Samouczek: Szyfrowanie i odszyfrowywanie obiektów blob w Microsoft Azure Storage przy użyciu usługi Azure Key Vault

## <a name="introduction"></a>Wprowadzenie
W tym samouczku opisano, jak korzystać z szyfrowania magazynu po stronie klienta za pomocą usługi Azure Key Vault. Przeprowadzi Cię on jak szyfrowanie i odszyfrowywanie obiektów blob, w aplikacji konsolowej przy użyciu tych technologii.

**Szacowany czas trwania:** 20 minut

Aby uzyskać informacje ogólne o usłudze Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Aby uzyskać przegląd informacji o szyfrowaniu po stronie klienta dla usługi Azure Storage, zobacz [szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto usługi Azure Storage
* Visual Studio 2013 lub nowszy
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Omówienie szyfrowania po stronie klienta

Aby uzyskać omówienie szyfrowania po stronie klienta dla usługi Azure Storage, zobacz [szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Poniżej przedstawiono krótki opis sposobu działania szyfrowania po stronie klienta:

1. Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), czyli jeden jednorazowej Użyj klucza symetrycznego.
2. Dane klienta są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowywany (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). Klucza KEK jest identyfikowana przez identyfikator klucza i można asymetryczną parę kluczy lub klucz symetryczny i mogą być zarządzane lokalnie lub przechowywane w usłudze Azure Key Vault. Klienta usługi Storage, sama nigdy nie ma dostępu do klucza KEK. Po prostu wywołuje algorytmu zawijanie kluczy, który znajduje się za pomocą usługi Key Vault. Klienci mogą wybrać opcję użycia niestandardowych dostawców klucza zawijania/rozpakowanie chcący.
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

## <a name="set-up-your-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Aby kontynuować z tego samouczka, należy wykonać następujące kroki, które są opisane w tym samouczku [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-overview.md):

* Tworzenie magazynu kluczy.
* Dodawanie klucza lub wpisu tajnego do magazynu kluczy.
* Rejestrowanie aplikacji w usłudze Azure Active Directory.
* Zezwól aplikacji na używanie klucza lub klucza tajnego.

Zanotuj ClientID i ClientSecret, które zostały wygenerowane podczas rejestrowania aplikacji w usłudze Azure Active Directory.

Utwórz oba klucze w magazynie kluczy. Przyjęto założenie, że w pozostałej części tego samouczka użyto następujących nazw: ContosoKeyVault i TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Tworzenie aplikacji konsolowej przy użyciu pakietów i AppSettings

W programie Visual Studio Utwórz nową aplikację konsoli.

Dodaj pakiety nuget niezbędne w konsoli Menedżera pakietów.

```powershell
Install-Package WindowsAzure.Storage
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Dodaj AppSettings App.config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Dodaj następujący kod `using` dyrektywy i upewnij się, że można dodać odwołania do System.Configuration do projektu.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;        
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Dodaj metodę, aby uzyskać token do aplikacji konsoli

Poniższa metoda jest używana przez klasy usługi Key Vault, które muszą zostać uwierzytelniony na potrzeby dostępu do magazynu kluczy.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        ConfigurationManager.AppSettings["clientId"],
        ConfigurationManager.AppSettings["clientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-storage-and-key-vault-in-your-program"></a>Dostęp do magazynu oraz usługi Key Vault w programie

W funkcji Main Dodaj następujący kod.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    ConfigurationManager.AppSettings["accountName"],
       ConfigurationManager.AppSettings["accountKey"]);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modele obiektów magazynu kluczy
> 
> Jest ważne dowiedzieć się, że istnieją faktycznie dwie usługi Key Vault modele obiektów pod uwagę: jeden opiera się na interfejsie API REST (przestrzeń nazw magazynu kluczy), a drugi to rozszerzenie do szyfrowania po stronie klienta.
> 
> Klucz klienta magazynu korzysta z interfejsu API REST i rozumie JSON Web kluczy i wpisów tajnych dla dwa rodzaje elementów, które są zawarte w usłudze Key Vault.
> 
> Rozszerzenia magazynu klucza to klasy, które wydają się być utworzone specjalnie na potrzeby szyfrowania po stronie klienta w usłudze Azure Storage. Zawierają one interfejs dla kluczy (klucz Instrumentacji) i oparty na koncepcji program rozpoznawania nazw klucz klasy. Istnieją dwie implementacje klucz instrumentacji, które należy znać: RSAKey i SymmetricKey. Teraz się zdarzyć się z rzeczy, które znajdują się w usłudze Key Vault, ale w tym momencie są one niezależne klasy (tak, aby klucz i wpis tajny pobrany przez klienta klucza magazynu nie należy implementować klucz Instrumentacji).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Szyfrowanie obiektów blob i przekazywanie

Dodaj następujący kod do szyfrowania obiektów blob i przekaż go do konta usługi Azure storage. **ResolveKeyAsync** metodę, która jest używana funkcja zwraca klucz instrumentacji.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
// Remember that we used the names contosokeyvault and testrsakey1.
var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Jeśli przyjrzymy się Konstruktor BlobEncryptionPolicy, pojawi się akceptowało klucza i/lub program rozpoznawania nazw. Należy pamiętać, obecnie nie można użyć program rozpoznawania nazw dla celów szyfrowania, ponieważ aktualnie nie robi obsługujące domyślnego klucza.
> 
> 

## <a name="decrypt-blob-and-download"></a>Odszyfrowywanie obiektów blob i pobieranie

Jest tak naprawdę gdy przy użyciu klas programu rozpoznawania nazw ma sensu. Identyfikator klucza używanego do szyfrowania jest skojarzone z obiektu blob w jego metadane, więc nie ma powodu do pobierania klucza i Zapamiętaj skojarzenia między kluczem i obiektów blob. Masz upewnić się, że klucz pozostanie w usłudze Key Vault.   

Klucz prywatny klucz RSA pozostaje w usłudze Key Vault, więc do odszyfrowywania wystąpią, zaszyfrowany klucz z metadane obiektu blob, który zawiera CEK są wysyłane do usługi Key Vault do odszyfrowywania.

Dodaj następujący kod do odszyfrowania obiektów blob, który właśnie został przekazany.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Istnieje kilka innych rodzajów rozpoznawania nazw, aby ułatwić zarządzanie kluczami, w tym: AggregateKeyResolver i CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Użyj wpisy tajne usługi Key Vault

Sposób używania wpisu tajnego za pomocą szyfrowania po stronie klienta jest za pośrednictwem klasy SymmetricKey, ponieważ klucz tajny jest zasadniczo klucza symetrycznego. Jednakże jak wspomniano powyżej, klucz tajny w usłudze Key Vault nie została zamapowana dokładnie SymmetricKey. Istnieje kilka rzeczy, aby zrozumieć:

* Klucz w SymmetricKey musi być stałą długość: 128, 192, 256, 384 lub 512 bitów.
* Klucz w SymmetricKey powinien być zakodowane w formacie Base64.
* Klucz tajny usługi Key Vault, która będzie służyć jako SymmetricKey musi mieć typ zawartości "application/octet-stream" w usłudze Key Vault.

Oto przykład w programie PowerShell tworzenia klucz tajny w usłudze Key Vault, który może służyć jako SymmetricKey.
Należy pamiętać, że wartość zakodowany $key, jest tylko w celach demonstracyjnych. We własnym kodzie można wygenerować ten klucz.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

W całej aplikacji konsolowej to samo wywołanie jako przed służy do pobierania tego wpisu tajnego jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
Gotowe. Owocnej pracy.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat przy użyciu usługi Microsoft Azure Storage w języku C#, zobacz [biblioteki klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Aby uzyskać więcej informacji na temat interfejsu API REST obiektów Blob, zobacz [interfejsu API REST usługi Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Aby uzyskać najnowsze informacje dotyczące usługi Microsoft Azure Storage, przejdź do [Blog zespołu programu Microsoft Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/).