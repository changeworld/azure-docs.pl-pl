---
title: Zarządzanie wygaśnięciem magazynu obiektów Blob platformy Azure
titleSuffix: Azure Content Delivery Network
description: Dowiedz się więcej o opcjach kontrolowania czasu do żywo dla obiektów blob w buforowaniu usługi Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083154"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Zarządzanie wygaśnięciem magazynu obiektów Blob platformy Azure w usłudze Azure CDN
> [!div class="op_single_selector"]
> * [Zawartość sieci Web platformy Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Magazyn obiektów Blob platformy Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Usługa magazynu obiektów Blob](../storage/common/storage-introduction.md#blob-storage) w usłudze Azure Storage jest jednym z kilku źródeł opartych na platformie Azure zintegrowanych z usługą Azure Content Delivery Network (CDN). Wszelkie publicznie dostępne zawartości obiektu blob mogą być buforowane w usłudze Azure CDN, dopóki nie upłynie jego czas wygaśnięcia (TTL). Czas wygaśnięcia jest `Cache-Control` określany przez nagłówek w odpowiedzi HTTP z serwera pochodzenia. W tym artykule opisano kilka `Cache-Control` sposobów, które można ustawić nagłówek na obiekt blob w usłudze Azure Storage.

Można również kontrolować ustawienia pamięci podręcznej z witryny Azure portal, ustawiając reguły buforowania usługi CDN. Jeśli utworzysz regułę buforowania i ustawisz jej zachowanie buforowania na **Zastąpal** lub **Pomiń pamięć podręczną,** ustawienia buforowania podane przez źródło, omówione w tym artykule, są ignorowane. Aby uzyskać informacje na temat ogólnych pojęć buforowania, zobacz [Jak działa buforowanie](cdn-how-caching-works.md).

> [!TIP]
> Można ustawić brak czasu wygaśnięcia obiektu blob. W takim przypadku usługa Azure CDN automatycznie stosuje domyślny czas wygaśnięcia z siedmiu dni, chyba że skonfigurowane są reguły buforowania w witrynie Azure portal. Ten domyślny czas wygaśnięcia ma zastosowanie tylko do ogólnych optymalizacji dostarczania w sieci Web. W przypadku optymalizacji dużych plików domyślny czas wygaśnięcia wynosi jeden dzień, a w przypadku optymalizacji przesyłania strumieniowego multimediów domyślny czas wygaśnięcia wynosi jeden rok.
> 
> Aby uzyskać więcej informacji na temat działania usługi Azure CDN w celu przyspieszenia dostępu do obiektów blob i innych plików, zobacz [Omówienie sieci dostarczania zawartości platformy Azure](cdn-overview.md).
> 
> Aby uzyskać więcej informacji na temat magazynu obiektów Blob platformy Azure, zobacz [Wprowadzenie do magazynu obiektów Blob.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Ustawianie nagłówków cache-control przy użyciu reguł buforowania sieci CDN
Preferowaną metodą ustawiania nagłówka `Cache-Control` obiektu blob jest użycie reguł buforowania w witrynie Azure portal. Aby uzyskać więcej informacji na temat reguł buforowania sieci CDN, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](cdn-caching-rules.md).

> [!NOTE] 
> Reguły buforowania są dostępne tylko dla **usługi Azure CDN Standard firmy Verizon** i Azure **CDN Standard z profilów Akamai.** W przypadku profilów **usługi Azure CDN Premium z verizon** należy użyć aparatu reguł usługi Azure [CDN](cdn-rules-engine.md) w portalu **zarządzania** dla podobnych funkcji.

**Aby przejść do strony reguł buforowania sieci CDN:**

1. W witrynie Azure portal wybierz profil usługi CDN, a następnie wybierz punkt końcowy obiektu blob.

2. W lewym okienku w obszarze Ustawienia zaznacz pole **Reguły buforowania**.

   ![Przycisk reguł buforowania sieci CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Zostanie wyświetlona strona **Reguły buforowania**.

   ![Strona buforowania sieci CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Aby ustawić nagłówki cache-control usługi magazynu obiektów blob przy użyciu globalnych reguł buforowania:**

1. W obszarze **Globalne reguły buforowania**ustaw **zachowanie buforowania ciągów zapytań** tak, aby **ignoruj ciągi zapytań** i ustaw **zachowanie buforowania** na **Zastąpalanie**.
      
2. W przypadku **czasu wygaśnięcia pamięci podręcznej**wprowadź 3600 w polu **Sekundy** lub 1 w polu **Godziny.** 

   ![Przykład globalnych reguł buforowania sieci CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Ta globalna reguła buforowania ustawia czas trwania pamięci podręcznej jednej godziny i wpływa na wszystkie żądania do punktu końcowego. Zastępuje wszystkie `Cache-Control` nagłówki `Expires` lub HTTP, które są wysyłane przez serwer pochodzenia określony przez punkt końcowy.   

3. Wybierz **pozycję Zapisz**.
 
**Aby ustawić nagłówki cache-control pliku obiektu blob przy użyciu niestandardowych reguł buforowania:**

1. W obszarze **Niestandardowe reguły buforowania**utwórz dwa warunki dopasowania:

     A. Dla pierwszego warunku dopasowania ustaw warunek `/blobcontainer1/*` **dopasowania** na **Ścieżkę** i wprowadź wartość **Dopasowania**. Ustaw **zachowanie buforowania,** aby **zastąpić** i wprowadź 4 w polu **Godziny.**

    B. W przypadku drugiego warunku dopasowania ustaw `/blobcontainer1/blob1.txt` warunek **dopasowania** na **Ścieżkę** i wprowadź wartość **Dopasowania**. Ustaw **zachowanie buforowania,** aby **zastąpić** i wprowadź 2 w polu **Godziny.**

    ![Przykład niestandardowych reguł buforowania usługi CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Pierwsza niestandardowa reguła buforowania ustawia czas trwania pamięci podręcznej `/blobcontainer1` wynoszący cztery godziny dla wszystkich plików obiektów blob w folderze na serwerze pochodzenia określonym przez punkt końcowy. Druga reguła zastępuje pierwszą regułę `blob1.txt` tylko dla pliku obiektu blob i ustawia czas trwania pamięci podręcznej wynoszący dwie godziny.

2. Wybierz **pozycję Zapisz**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Ustawianie nagłówków kontroli pamięci podręcznej przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Usługa Azure PowerShell](/powershell/azure/overview) to jeden z najszybszych i najpotężniejszych sposobów administrowania usługami platformy Azure. Użyj `Get-AzStorageBlob` polecenia cmdlet, aby uzyskać odwołanie do `.ICloudBlob.Properties.CacheControl` obiektu blob, a następnie ustawić właściwość. 

Przykład:

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
> Za pomocą programu PowerShell można również [zarządzać profilami i punktami końcowymi sieci CDN.](cdn-manage-powershell.md)
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Ustawianie nagłówków kontroli pamięci podręcznej przy użyciu platformy .NET
Aby określić `Cache-Control` nagłówek obiektu blob przy użyciu kodu .NET, użyj [biblioteki klienta usługi Azure Storage dla platformy .NET,](../storage/blobs/storage-dotnet-how-to-use-blobs.md) aby ustawić właściwość [CloudBlob.Properties.CacheControl.](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol)

Przykład:

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
> W przykładach usługi [Azure Blob Storage dla platformy .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)dostępnych jest więcej przykładów kodu platformy .NET.
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Ustawianie nagłówków cache-control przy użyciu innych metod

### <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage
Za [pomocą Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)można wyświetlać i edytować zasoby magazynu obiektów blob, w tym właściwości, takie jak *właściwość CacheControl.* 

Aby zaktualizować *właściwość CacheControl* obiektu blob za pomocą Eksploratora usługi Azure Storage:
   1. Wybierz obiekt blob, a następnie wybierz **polecenie Właściwości** z menu kontekstowego. 
   2. Przewiń w dół do *CacheControl* właściwości.
   3. Wprowadź wartość, a następnie wybierz pozycję **Zapisz**.


![Właściwości Eksploratora usługi Azure Storage](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interfejs wiersza polecenia platformy Azure
Za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) (CLI) można zarządzać zasobami obiektów blob platformy Azure z wiersza polecenia. Aby ustawić nagłówek kontroli pamięci podręcznej podczas przekazywania obiektu blob z interfejsu `-p` wiersza polecenia platformy Azure, należy ustawić *cacheControl* właściwości przy użyciu przełącznika. W poniższym przykładzie pokazano, jak ustawić czas wygaśnięcia na jedną godzinę (3600 sekund):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Interfejs API REST usług magazynu platformy Azure
Za pomocą [interfejsu API REST usług magazynu platformy Azure](/rest/api/storageservices/) można jawnie ustawić właściwość *x-ms-blob-cache-control* przy użyciu następujących operacji na żądanie:
  
   - [Wstawianie obiektu blob](/rest/api/storageservices/Put-Blob)
   - [Umieść listę zablokowanych](/rest/api/storageservices/Put-Block-List)
   - [Ustawianie właściwości obiektu blob](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testowanie nagłówka Cache-Control
Można łatwo zweryfikować ustawienia czasu wygaśnięcia obiektów blob. Za pomocą [narzędzi programistycznych](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)przeglądarki sprawdź, czy `Cache-Control` obiekt blob zawiera nagłówek odpowiedzi. Można również użyć narzędzia, takiego jak [Wget,](https://www.gnu.org/software/wget/) [Listonosz](https://www.getpostman.com/)lub [Skrzypek,](https://www.telerik.com/fiddler) aby sprawdzić nagłówki odpowiedzi.

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak zarządzać wygaśnięciem zawartości usługi w chmurze w usłudze Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Dowiedz się więcej o pojęciach dotyczących buforowania](cdn-how-caching-works.md)

