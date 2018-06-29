---
title: Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć protokół TLS 1.2 w kliencie usługi Azure Storage.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/25/2018
ms.author: fryu
ms.openlocfilehash: 5c21df2b3bdeee6ac7c3956fe1cafa4f947dd6dd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036436"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage

Gdy trzeba inspekcji usług za pomocą usługi Azure Storage na podstawie zgodności najnowszych i wymagania dotyczące zabezpieczeń, SSL 1.0, 2.0, 3.0 i TLS 1.0 są uznawane za protokoły komunikacji niezgodnych.

Znaleziono 1.0 protokołu SSL 2.0 i 3.0 jest zagrożony. Mają one został zabroniony w dokumencie RFC. Protokołu TLS 1.0 staje się niebezpieczne dla przy użyciu niezabezpieczonych szyfry blokowe (DES CBC i RC2 CBC) i szyfrowania strumienia (RC4). Radę PCI sugerowane również migracji do nowszej wersji protokołu TLS. Aby uzyskać więcej informacji, zobacz [zabezpieczeń TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Magazyn Azure zostało zatrzymane, ponieważ 2015 protokołu SSL 3.0 i używa protokołu TLS 1.2 na publicznych punktów końcowych HTTPs, ale protokołu TLS 1.0, TLS 1.1 nadal są obsługiwane dla zgodności z poprzednimi wersjami.

Aby zapewnić bezpieczne i zgodne połączenia do magazynu Azure, musisz włączyć protokół TLS 1.2 po stronie klienta przed wysłaniem żądania do działania usługi Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Włączanie protokołu TLS 1.2 w klienta .NET

Dla klienta w celu negocjowania protokołu TLS 1.2, systemu operacyjnego i programu .NET Framework w wersji oba muszą obsługiwać protokół TLS 1.2. Dowiedz się więcej w [obsługę protokołu TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

Poniższy przykład przedstawia sposób włączenia protokołu TLS 1.2 na kliencie .NET.

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

## <a name="enable-tls-12-in-powershell-client"></a>Włącz protokół TLS 1.2 w kliencie programu PowerShell

Poniższy przykład przedstawia sposób włączenia protokołu TLS 1.2 na kliencie programu PowerShell.

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>Sprawdź połączenie z protokołu TLS 1.2

Można użyć narzędzia Fiddler do sprawdzenia, czy protokołu TLS 1.2 są rzeczywiście używane. Otwórz Fiddler uruchomić Przechwytywanie ruchu w sieci klienta, a następnie wykonać powyżej próbki. Następnie można znaleźć wersji protokołu TLS w przykładowej sprawia, że połączenie.

Poniższy zrzut ekranu znajduje się przykład w celu weryfikacji.

![Zrzut ekranu przedstawiający Sprawdzanie wersji protokołu TLS w narzędziu Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zobacz także

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
