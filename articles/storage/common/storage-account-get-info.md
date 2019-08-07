---
title: Pobieranie typu konta magazynu i nazwy jednostki SKU przy użyciu platformy .NET — Azure Storage
description: Dowiedz się, jak uzyskać typ konta usługi Azure Storage i nazwę jednostki SKU przy użyciu biblioteki klienckiej platformy .NET.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 21ecaae679ad4a5f21107ef53d3899cf03593c9d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829205"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Pobierz typ konta magazynu i nazwę jednostki SKU z platformą .NET

W tym artykule pokazano, jak uzyskać typ konta usługi Azure Storage i nazwę jednostki SKU dla obiektu BLOB przy użyciu [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage/client).

Informacje o koncie są dostępne w wersjach usługi począwszy od wersji 2018-03-28.

## <a name="about-account-type-and-sku-name"></a>Informacje o typie konta i nazwie jednostki SKU

**Typ konta**: Prawidłowe typy kont to `BlobStorage`m.in `BlockBlobStorage` `FileStorage`.,,, `StorageV2`i. `Storage` [Omówienie konta usługi Azure Storage](storage-account-overview.md) zawiera więcej informacji, w tym opisy różnych kont magazynu.

**Nazwa jednostki SKU**: Prawidłowe nazwy jednostek SKU `Premium_LRS`obejmują `Premium_ZRS`, `Standard_GRS` `Standard_GZRS` ,,`Standard_LRS` ,,`Standard_ZRS`, ,i.`Standard_RAGZRS` `Standard_RAGRS` Nazwy jednostek SKU są rozróżniane wielkości liter i są polami ciągów w [klasie SkuName](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet).

## <a name="retrieve-account-information"></a>Pobierz informacje o koncie

Aby uzyskać typ konta magazynu i nazwę jednostki SKU skojarzone z obiektem BLOB, wywołaj metodę [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) lub [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) .

Poniższy przykład kodu pobiera i wyświetla właściwości konta tylko do odczytu.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych operacjach, które można wykonać na koncie magazynu za pomocą [Azure Portal](https://portal.azure.com) i interfejsu API REST platformy Azure.

- [Zarządzanie kontem magazynu](storage-account-manage.md)
- [Uaktualnianie konta magazynu](storage-account-upgrade.md)
- [Operacja pobrania informacji o koncie (REST)](/rest/api/storageservices/get-account-information)
