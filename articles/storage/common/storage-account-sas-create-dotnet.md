---
title: Tworzenie sygnatury dostępu Współdzielonego konta z siecią .NET
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć sygnaturę dostępu współdzielonego konta (SAS) przy użyciu biblioteki klienta platformy .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9da27cef7bafa94715a42db86fc5a5675a049eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137873"
---
# <a name="create-an-account-sas-with-net"></a>Tworzenie sygnatury dostępu Współdzielonego konta z siecią .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

W tym artykule pokazano, jak używać klucza konta magazynu do tworzenia sygnatury dostępu Współdzielonego konta za pomocą [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="create-an-account-sas"></a>Tworzenie sygnatury dostępu współdzielonego konta

Aby utworzyć sygnaturę dostępu Współdzielonego konta dla kontenera, należy wywołać metodę [CloudStorageAccount.GetSharedAccessSignature.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature)

Poniższy przykład kodu tworzy sygnatury dostępu Współdzielonego konta, który jest prawidłowy dla usług obiektów Blob i File i daje uprawnienia klienta do odczytu, zapisu i listy uprawnień dostępu do interfejsów API na poziomie usługi. Sygnatura dostępu Współdzielonego konta ogranicza protokół do protokołu HTTPS, więc żądanie musi być wykonane za pomocą protokołu HTTPS. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>Używanie sygnatury dostępu Współdzielonego konta od klienta

Aby użyć sygnatury dostępu współdzielonego konta, aby uzyskać dostęp do interfejsów API na poziomie usługi dla usługi Blob, należy skonstruować obiekt klienta usługi obiektów blob przy użyciu sygnatury dostępu Współdzielonego i punktu końcowego magazynu obiektów blob dla konta magazynu. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="next-steps"></a>Następne kroki

- [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md)
- [Tworzenie sygnatury dostępu współdzielonego konta](/rest/api/storageservices/create-account-sas)
