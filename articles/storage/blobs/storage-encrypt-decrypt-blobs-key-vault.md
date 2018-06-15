---
title: 'Samouczek: Szyfrowanie i odszyfrowywanie obiektów blob w usłudze Azure Storage za pomocą usługi Azure Key Vault | Dokumentacja firmy Microsoft'
description: Jak szyfrowanie i odszyfrowywanie obiektów blob za pomocą szyfrowania po stronie klienta dla usługi Magazyn Microsoft Azure z usługą Azure Key Vault.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: adhurwit
ms.openlocfilehash: 405ccb44c9daf8d555946e6c68ef318ed2b82505
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2018
ms.locfileid: "27815817"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Samouczek: Szyfrowanie i odszyfrowywanie obiektów blob w magazynie platformy Microsoft Azure przy użyciu usługi Azure Key Vault
## <a name="introduction"></a>Wprowadzenie
W tym samouczku opisano sposób należy korzystać z szyfrowania magazynu po stronie klienta z usługi Azure Key Vault. Przeprowadza użytkownika przez proces szyfrowania i odszyfrowywania obiektu blob w aplikacji konsoli przy użyciu tych technologii.

**Szacowany czas trwania:** 20 minut

Aby uzyskać przegląd informacji dotyczących usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Aby uzyskać przegląd informacji dotyczących szyfrowania po stronie klienta dla usługi Azure Storage, zobacz [szyfrowania po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto usługi Azure Storage
* Visual Studio 2013 lub nowszy
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Omówienie szyfrowania po stronie klienta
Omówienie szyfrowania po stronie klienta dla usługi Azure Storage, zobacz [szyfrowania po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Poniżej przedstawiono krótki opis działania szyfrowania po stronie klienta:

1. Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), jest jeden jednorazowej użycia klucza symetrycznego.
2. Dane klienta są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowane (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i można parę klucza asymetrycznego lub klucza symetrycznego i można być zarządzany lokalnie lub przechowywane w usłudze Azure Key Vault. Klienta magazynu nigdy nie ma dostępu do klucza KEK. Wywołuje po prostu algorytm klucza zawijania zapewnianej przez usługi Key Vault. Klienci mogą wybrać do używania niestandardowych dostawców dla zawijania/odkodowywania chcący klucza.
4. Zaszyfrowane dane są następnie przekazywane do usługi Magazyn Azure.

## <a name="set-up-your-azure-key-vault"></a>Skonfiguruj magazyn kluczy Azure
Aby kontynuować z tego samouczka, należy wykonać następujące kroki, które zostały opisane w samouczku [wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md):

* Tworzenie magazynu kluczy.
* Dodawanie klucza lub klucza tajnego do magazynu kluczy.
* Rejestrowanie aplikacji w usłudze Azure Active Directory.
* Zezwolić aplikacji na używanie klucza lub klucza tajnego.

Zanotuj ClientID i ClientSecret, które zostały wygenerowane podczas rejestrowania aplikacji w usłudze Azure Active Directory.

Utwórz obydwu kluczy w magazynie kluczy. W pozostałej części samouczka przyjmujemy, użycie następujących nazw: ContosoKeyVault i TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Utwórz aplikację konsoli z pakietów i AppSettings
W programie Visual Studio Utwórz nową aplikację konsoli.

Dodawanie pakietów nuget niezbędne w konsoli Menedżera pakietów.

```
Install-Package WindowsAzure.Storage

// This is the latest stable release for ADAL.
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Dodaj AppSettings do pliku App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Dodaj następujące `using` dyrektywy i upewnij się, że można dodać odwołania do System.Configuration do projektu.

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
Następująca metoda jest używana przez klasy magazynu kluczy, które muszą zostać uwierzytelniony na potrzeby dostępu do magazynu kluczy.

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

## <a name="access-storage-and-key-vault-in-your-program"></a>Dostęp do magazynu i klucza magazynu w programie
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
> Ważne jest, aby dowiedzieć się, że są faktycznie dwie usługi Key Vault modele obiektów należy pamiętać o: jeden jest oparta na interfejsu API REST (przestrzeń nazw KeyVault) i innych to rozszerzenie szyfrowania po stronie klienta.
> 
> Klucz magazynu klienta współdziała z interfejsu API REST i rozumie JSON Web kluczy i kluczy tajnych na dwa rodzaje rzeczy, które są zawarte w magazynie kluczy.
> 
> Rozszerzenia magazynu kluczy są klasy, które wydają się utworzone specjalnie do szyfrowania po stronie klienta w usłudze Azure Storage. Zawierają one interfejs dla kluczy (IKey) i oparty na koncepcji rozpoznawania klucz klasy. Istnieją dwa implementacje IKey, które trzeba znać: RSAKey i SymmetricKey. Teraz się zdarzyć się z rzeczy, które są zawarte w magazynie kluczy, ale w tym momencie są niezależne klasy (tak, aby klucz i klucz tajny pobierane przez klienta klucza magazynu nie implementują IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Szyfrowanie obiektów blob i przekaż
Dodaj następujący kod do szyfrowania obiektu blob i przekaż go do konta magazynu Azure. **ResolveKeyAsync** IKey zwraca metodę, która jest używana.

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
> Jeśli przyjrzymy się konstruktora BlobEncryptionPolicy, pojawi się że można zaakceptować klucza i/lub program rozpoznawania nazw. Należy pamiętać, że w tej chwili nie można użyć program rozpoznawania nazw dla celów szyfrowania, ponieważ tak nie jest obecnie obsługuje domyślny klucz.
> 
> 

## <a name="decrypt-blob-and-download"></a>Odszyfrowywanie obiektów blob i pobierania
Odszyfrowywanie jest w rzeczywistości podczas używania klas programu rozpoznawania nazw sensu. Identyfikator używany do szyfrowania klucza jest skojarzony z obiektem blob w metadanych, więc nie ma powodu nie można pobrać klucza i zapamiętać skojarzenie obiektu blob i kluczy. Masz upewnić się, że klucz pozostanie w magazynie kluczy.   

Klucza prywatnego klucza RSA pozostaje w magazynie kluczy, więc do odszyfrowywania nastąpić, zaszyfrowany klucz z metadane obiektu blob, który zawiera CEK są wysyłane do usługi Key Vault do odszyfrowywania.

Dodaj następujący kod do odszyfrowania obiektu blob, który został przekazany.

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

## <a name="use-key-vault-secrets"></a>Użyj kluczy tajnych magazyn kluczy
Sposób używać hasła przy użyciu szyfrowania po stronie klienta jest za pomocą klasy SymmetricKey, ponieważ klucz tajny jest zasadniczo klucza symetrycznego. Jednak jak wspomniano powyżej, klucza tajnego w magazynie kluczy nie jest zmapowany dokładnie SymmetricKey. Istnieje kilka rzeczy, aby zrozumieć:

* Klucz w SymmetricKey musi być stałą długość: 128, 192, 256, 384 lub 512 bitów.
* Klucz w SymmetricKey powinien być kodowany w standardzie Base64.
* Klucz tajny usługi Key Vault, która będzie służyć jako SymmetricKey musi mieć typ zawartości "application/octet-stream" w magazynie kluczy.

Oto przykład w programie PowerShell tworzenia klucza tajnego w magazynie kluczy, który może służyć jako SymmetricKey.
Należy pamiętać, że wartość zakodowany $key, jest tylko w celach demonstracyjnych. W swoim własnym kodem należy do wygenerowania tego klucza.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

W aplikacji konsoli można użyć to samo wywołanie jako przed pobrać ten klucz tajny jako SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
To już wszystko. Owocnej pracy.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji o korzystaniu z usługi Magazyn Microsoft Azure w języku C#, zobacz [Biblioteka klienta usługi Microsoft Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Aby uzyskać więcej informacji na temat interfejsu API REST obiektów Blob, zobacz [interfejsu API REST usługi Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Aby uzyskać najnowsze informacje dotyczące usługi Magazyn Microsoft Azure, przejdź do [Blog zespołu usługi Magazyn Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/).
