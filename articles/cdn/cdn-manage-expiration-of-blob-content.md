---
title: Zarządzanie wygasaniem usługi Azure Blob storage w usłudze Azure Content Delivery Network | Dokumentacja firmy Microsoft
description: Poznaj opcje do kontrolowania czasu wygaśnięcia dla obiektów blob w usłudze Azure CDN caching.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f7fc11af8cd2574271b26f7dec62072692685672
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916805"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Zarządzanie wygasaniem usługi Azure Blob storage w usłudze Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Obiektów Blob usługi storage](../storage/common/storage-introduction.md#blob-storage) w usłudze Azure Storage jest jeden z kilku źródeł oparte na platformie Azure zintegrowany z usługi Azure Content Delivery Network (CDN). Mogą być buforowane żadnej zawartości publicznie dostępnych obiektów blob w usłudze Azure CDN, dopóki nie upłynie jego czas wygaśnięcia (TTL). Czas wygaśnięcia jest określany przez `Cache-Control` nagłówka odpowiedzi HTTP z serwera pochodzenia. W tym artykule opisano kilka metod, które można ustawić `Cache-Control` nagłówek obiektu blob w usłudze Azure Storage.

Ustawienia pamięci podręcznej w witrynie Azure portal można też sterować przez reguły buforowania sieci CDN ustawienie. Możesz utworzyć regułę buforowania i ustaw jego zachowanie buforowania **zastąpienia** lub **Pomiń pamięć podręczną**, ustawienia buforowania dostarczone do źródła omówionych w tym artykule są ignorowane. Aby uzyskać informacje o ogólnych pojęciach buforowania, zobacz [jak działa buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Można ustawić nie czasu wygaśnięcia na obiekcie blob. W tym przypadku sieć CDN systemu Azure automatycznie stosuje jako domyślny czas wygaśnięcia siedem dni, chyba że zostały skonfigurowane reguły w witrynie Azure portal buforowania. To ustawienie domyślne TTL dotyczy tylko optymalizacji dostarczania ogólnych sieci web. Optymalizacja dużych plików, aby uzyskać domyślny czas wygaśnięcia wynosi jeden dzień, a dla multimediów, przesyłanie strumieniowe optymalizacje, domyślny czas wygaśnięcia wynosi jeden rok.
> 
> Aby uzyskać więcej informacji na temat współdziałania usługi Azure CDN aby przyspieszyć dostęp do obiektów blob i innych plików, zobacz [Omówienie usługi Azure Content Delivery Network](cdn-overview.md).
> 
> Aby uzyskać więcej informacji na temat usługi Azure Blob storage, zobacz [wprowadzenie do usługi Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawienie nagłówki Cache-Control przy użyciu reguł buforowania usługi CDN
Preferowana metoda do ustawiania obiektu blob `Cache-Control` nagłówek jest użycie reguły buforowania w witrynie Azure portal. Aby uzyskać więcej informacji na temat zasad buforowania usługi CDN, zobacz [kontroli Azure działanie buforowania usługi CDN przy użyciu reguł buforowania](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profilów. Dla **Azure CDN Premium from Verizon** profile, należy użyć [aparat reguł wysokiej dostępności treści Azure](cdn-rules-engine.md) w **Zarządzaj** portal dla podobne funkcje.

**Aby przejść do strony reguł buforowania usługi CDN**:

1. W witrynie Azure portal wybierz profil usługi CDN, a następnie wybierz punkt końcowy obiektu blob.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk reguły z buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Aby ustawić nagłówki Cache-Control to usługa magazynu obiektów Blob przy użyciu globalne reguły buforowania:**

1. W obszarze **globalne reguły buforowania**ustaw **zachowanie buforowania ciągu kwerendy** do **Ignoruj ciągi zapytań** i ustaw **zachowanie buforowania** do  **Zastąp**.
      
2. Dla **czas wygasania pamięci podręcznej**, wprowadź 3600 w **sekund** pola lub 1 w **godzin** pole. 

   ![Przykład globalne reguły buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   To globalne reguły buforowania ustawia czas trwania pamięci podręcznej jednej godziny i ma wpływ na wszystkie żądania do punktu końcowego. Ustawienie to zastępuje dowolny `Cache-Control` lub `Expires` nagłówki HTTP, które są wysyłane przez serwer pochodzenia, określony przez punkt końcowy.   

3. Wybierz pozycję **Zapisz**.
 
**Aby ustawić nagłówki Cache-Control pliku obiektu blob, za pomocą niestandardowych reguł buforowania:**

1. W obszarze **niestandardowe reguły buforowania**, Utwórz dwa warunki dopasowania:

     A. Pierwszy warunek dopasowania, ustaw **dopasować stan** do **ścieżki** i wprowadź `/blobcontainer1/*` dla **odpowiada wartości**. Ustaw **zachowanie buforowania** do **zastąpienia** i wprowadź 4 w **godzin** pole.

    B. Drugi warunek dopasowania, ustaw **dopasować stan** do **ścieżki** i wprowadź `/blobcontainer1/blob1.txt` dla **odpowiada wartości**. Ustaw **zachowanie buforowania** do **zastąpienia** i wprowadź wartość 2, w **godzin** pole.

    ![Przykład niestandardowych reguł buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Pierwsze niestandardowe reguły buforowania ustawia czas trwania pamięci podręcznej czterech godzin dla wszystkich plików obiektów blob w `/blobcontainer1` folderu na serwerze źródłowym, określony przez punkt końcowy usługi. Druga reguła zastępuje pierwszej reguły dla `blob1.txt` tylko plik obiektów blob i ustawia czas trwania pamięci podręcznej z 2 godzin.

2. Wybierz pozycję **Zapisz**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Ustawianie nagłówki Cache-Control za pomocą programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Program Azure PowerShell](/powershell/azure/overview) jest jednym ze sposobów najszybszym i najbardziej wydajnymi procesorami do administrowania usługami platformy Azure. Użyj `Get-AzStorageBlob` polecenia cmdlet, aby pobrać odwołanie do obiektu blob, następnie ustawić `.ICloudBlob.Properties.CacheControl` właściwości. 

Na przykład:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Możesz również użyć programu PowerShell, aby [Zarządzanie profilami usługi CDN i punktów końcowych](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Nagłówki Cache-Control ustawienie przy użyciu platformy .NET
Aby określić obiekt blob `Cache-Control` nagłówka za pomocą kodu platformy .NET, użyj [biblioteki klienta usługi Azure Storage dla platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) można ustawić [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol#Microsoft_WindowsAzure_Storage_Blob_BlobProperties_CacheControl) właściwości.

Na przykład:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Więcej przykładów kodu platformy .NET są dostępne w [przykładów usługi Azure Blob Storage dla platformy .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Nagłówki Cache-Control ustawienie przy użyciu innych metod

### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Za pomocą [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/), mogą wyświetlać i edytować zasobów magazynu obiektów blob, takich jak tym właściwości *CacheControl* właściwości. 

Aby zaktualizować *CacheControl* właściwość obiektu blob za pomocą Eksploratora usługi Azure Storage:
   1. Wybierz obiekt blob, a następnie wybierz **właściwości** z menu kontekstowego. 
   2. Przewiń w dół do *CacheControl* właściwości.
   3. Wprowadź wartość, a następnie wybierz **Zapisz**.


![Właściwości Eksploratora usługi Storage platformy Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interfejs wiersza polecenia platformy Azure
Za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) (CLI), można zarządzać zasobami usługi Azure blob z poziomu wiersza polecenia. Aby ustawić nagłówek cache-control, podczas przekazywania obiektu blob przy użyciu wiersza polecenia platformy Azure, należy ustawić *cacheControl* właściwości przy użyciu `-p` przełącznika. Poniższy przykład pokazuje, jak ustawić czas wygaśnięcia na godziny (3600 sekund):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Interfejs API REST usługi Azure storage
Możesz użyć [interfejsu API REST usługi Azure storage](/rest/api/storageservices/) jawnie ustawić *x-ms-blob-cache-control* właściwości przy użyciu następujących operacji na żądanie:
  
   - [Put Blob](/rest/api/storageservices/Put-Blob)
   - [Umieść zablokowanych](/rest/api/storageservices/Put-Block-List)
   - [Ustaw właściwości obiektu Blob](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówek Cache-Control
Można łatwo sprawdzić ustawienia czasu wygaśnięcia obiektów blob. Przy użyciu przeglądarki [narzędzi deweloperskich](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, który zawiera obiekt blob `Cache-Control` nagłówka odpowiedzi. Można również użyć narzędzia takie jak [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), lub [Fiddler](https://www.telerik.com/fiddler) Aby sprawdzić nagłówki odpowiedzi.

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak zarządzać wygasaniem zawartości usług w chmurze w usłudze Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Dowiedz się więcej na temat buforowania pojęcia](cdn-how-caching-works.md)

