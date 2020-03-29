---
title: Określ klucz dostarczony przez klienta w żądaniu magazynu obiektów Blob za pomocą platformy .NET — Usługa Azure Storage
description: Dowiedz się, jak określić klucz dostarczony przez klienta w żądaniu magazynu obiektów Blob przy użyciu platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807004"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Określ klucz dostarczony przez klienta w żądaniu magazynu obiektów Blob za pomocą platformy .NET

Klienci żądani względem usługi Azure Blob Storage mają możliwość podania klucza szyfrowania dla pojedynczego żądania. Włączenie klucza szyfrowania w żądaniu zapewnia szczegółową kontrolę nad ustawieniami szyfrowania dla operacji magazynu obiektów Blob. Klucze dostarczone przez klienta (wersja zapoznawcza) mogą być przechowywane w usłudze Azure Key Vault lub w innym magazynie kluczy.

W tym artykule pokazano, jak określić klucz dostarczony przez klienta w żądaniu z .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Aby dowiedzieć się więcej o tym, jak uwierzytelniać się przy użyciu biblioteki klienta usługi Azure Identity z usługi Azure Storage, zobacz sekcję **"Uwierzytelnij za pomocą biblioteki tożsamości platformy Azure** w [obszarze Autoryzowanie dostępu do obiektów blob i kolejek za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Przykład: przekazywanie obiektu blob za pomocą klucza dostarczonego przez klienta

Poniższy przykład tworzy klucz dostarczony przez klienta i używa tego klucza do przekazania obiektu blob. Kod przekazuje blok, a następnie zatwierdza listę zablokowanych, aby napisać obiekt blob do usługi Azure Storage.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](../common/storage-service-encryption.md)
- [Autoryzuj dostęp do obiektów blob i kolejek za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure](../common/storage-auth-aad-msi.md)
