---
title: Zabezpieczanie dostępu do danych aplikacji w chmurze za pomocą usługi Azure Storage | Microsoft Docs
description: Używanie tokenów SAS, szyfrowania i protokołu HTTPS w celu zabezpieczenia danych aplikacji w chmurze.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: ea865fe246bcb414dd1ab3f83b7135989bc0adf3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391518"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Zabezpieczanie dostępu do danych aplikacji w chmurze

Ten samouczek jest trzecią częścią serii. Zawiera informacje o sposobach zabezpieczania dostępu do konta magazynu. 

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Używanie tokenów SAS do uzyskiwania dostępu do obrazów miniatury
> * Włączanie szyfrowania po stronie serwera
> * Włączanie transportu tylko przy użyciu protokołu HTTPS

Usługa [Azure Blob Storage](../common/storage-introduction.md#blob-storage) to niezawodna usługa do przechowywania plików dla aplikacji. Ten samouczek uzupełnia informacje zawarte w [poprzednim temacie][previous-tutorial] i pokazuje, jak zabezpieczyć dostęp do konta magazynu z poziomu aplikacji internetowej. Po zakończeniu obrazy będą szyfrowane, a aplikacja internetowa będzie używać tokenów SAS w celu uzyskania dostępu do obrazów miniatury.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, konieczne jest ukończenie poprzedniego samouczka na temat usługi Storage: [Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid][previous-tutorial]. 

## <a name="set-container-public-access"></a>Włączanie dostępu publicznego do kontenera

W tej części serii samouczków do uzyskania dostępu do miniatur zostaną użyte tokeny SAS. W tym kroku ustawisz wartość `off` w konfiguracji publicznego dostępu do kontenera _thumbnails_.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurowanie tokenów SAS dla miniatur

W części pierwszej serii tej serii samouczków aplikacja internetowa wyświetlała obrazy z publicznego kontenera. W tej części użyjesz tokenów [sygnatury dostępu współdzielonego (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature), aby pobrać obrazy miniatur. Tokeny SAS umożliwiają ograniczenie dostępu do kontenera lub obiektu blob na podstawie adresu IP, protokołu, interwałów czasowych lub przyznanych uprawnień.

W tym przykładzie repozytorium kodu źródłowego korzysta z gałęzi `sasTokens`, która zawiera zaktualizowany kod przykładowy. Usuń istniejące wdrożenie kodu z usługi GitHub za pomocą polecenia [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Następnie skonfiguruj wdrożenie aplikacji internetowej z usługi GitHub za pomocą polecenia [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

W poniższym poleceniu wartość `<web-app>` to nazwa aplikacji internetowej.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Gałąź `sasTokens` repozytorium aktualizuje plik `StorageHelper.cs`. Zastępuje zadanie `GetThumbNailUrls` przykładowym kodem pokazanym poniżej. Zaktualizowane zadanie pobiera adresy URL miniatur przez skonfigurowanie zasad [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet), określających czas rozpoczęcia, czas wygaśnięcia i uprawnienia tokenu SAS. Po wdrożeniu aplikacja internetowa pobiera miniatury przy użyciu adresu URL i tokenu SAS. Zaktualizowane zadanie jest pokazane na poniższym przykładzie:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

W poprzednim zadaniu użyto następujących klas, właściwości i metod:

|Klasa  |Właściwości| Metody  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Results](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Uprawnienia](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

[Szyfrowanie usługi Azure Storage (SSE)](../common/storage-service-encryption.md) pomaga chronić dane. Usługa SSE szyfruje dane magazynowane, obsługując szyfrowanie, odszyfrowywanie i zarządzanie kluczami. Wszystkie dane są szyfrowane za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych.

Usługa SSE automatycznie szyfruje dane we wszystkich warstwach wydajności (Standardowa i Premium), wszystkich modelach wdrażania (model usługi Azure Resource Manager i model klasyczny) oraz wszystkich usługach Azure Storage (Blob, Queue, Table i File). 

## <a name="enable-https-only"></a>Włączanie używania tylko protokołu HTTPS

Aby mieć pewność, że żądania danych wysyłane do i z konta magazynu są bezpieczne, można zezwolić tylko na żądania używające protokołu HTTPS. Zaktualizuj protokół wymagany przez konto magazynu przy użyciu polecenia [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Sprawdź połączenie, używając polecenia `curl` i protokołu `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Teraz, gdy wymagany jest bezpieczny transfer, otrzymasz następujący komunikat:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Kolejne kroki

W trzeciej części serii przedstawiono, sposób zabezpieczania dostępu do konta magazynu, w tym następujące czynności:

> [!div class="checklist"]
> * Używanie tokenów SAS do uzyskiwania dostępu do obrazów miniatury
> * Włączanie szyfrowania po stronie serwera
> * Włączanie transportu tylko przy użyciu protokołu HTTPS

Przejdź do czwartej części serii, aby dowiedzieć się, jak monitorować aplikację magazynu w chmurze i rozwiązywać problemy.

> [!div class="nextstepaction"]
> [Monitorowanie i rozwiązywanie problemów aplikacji magazynu w chmurze](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
