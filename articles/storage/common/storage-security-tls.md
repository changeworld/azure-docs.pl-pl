---
title: Włącz bezpieczne TLS z .NET
titleSuffix: Azure Storage
description: Dowiedz się, jak włączyć usługę TLS 1.2 przy użyciu biblioteki klienta platformy .NET dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371806"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage

Transport Layer Security (TLS) i Secure Sockets Layer (SSL) to protokoły kryptograficzne, które zapewniają bezpieczeństwo komunikacji za pośrednictwem sieci komputerowej. SSL 1.0, 2.0 i 3.0 okazały się podatne na ataki. Zostały one zakazane przez RFC. TLS 1.0 staje się niebezpieczny dla korzystania z niezabezpieczonych szyfru bloku (DES CBC i RC2 CBC) i szyfrowania strumienia (RC4). Rada PCI zasugerowała również migrację do wyższych wersji TLS. Aby uzyskać więcej informacji, zobacz [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Usługa Azure Storage zatrzymała SSL 3.0 od 2015 r. i używa protokołu TLS 1.2 w publicznych punktach końcowych HTTPs, ale TLS 1.0 i TLS 1.1 są nadal obsługiwane ze zgodnością z powrotem.

Aby zapewnić bezpieczne i zgodne połączenie z usługą Azure Storage, należy włączyć TLS 1.2 lub nowszą wersję po stronie klienta przed wysłaniem żądań do obsługi usługi Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Włączanie protokołu TLS 1.2 w kliencie platformy .NET

Aby klient negocjuje TLS 1.2, system operacyjny i wersja .NET Framework muszą obsługiwać TLS 1.2. Zobacz więcej szczegółów w [pomocy technicznej dla protokołu TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

W poniższym przykładzie pokazano, jak włączyć TLS 1.2 w kliencie platformy .NET.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Włączanie protokołu TLS 1.2 w kliencie programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

W poniższym przykładzie pokazano, jak włączyć TLS 1.2 w kliencie programu PowerShell.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Weryfikowanie połączenia TLS 1.2

Można użyć Fiddler, aby sprawdzić, czy TLS 1.2 jest używany w rzeczywistości. Otwórz Fiddler, aby rozpocząć przechwytywanie ruchu sieciowego klienta, a następnie wykonać powyżej próbki. Następnie można znaleźć wersję TLS w połączeniu, który sprawia, że próbka.

Poniższy zrzut ekranu jest próbką do weryfikacji.

![zrzut ekranu przedstawiający weryfikację wersji TLS w fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zobacz też

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Zgodność z PCI w tls](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Włączanie protokołu TLS w kliencie java](https://www.java.com/en/configure_crypto.html)
