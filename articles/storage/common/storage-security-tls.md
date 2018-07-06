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
ms.openlocfilehash: 6c313b6015a8a6dcc4ca5befb5fef70b047d0410
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866529"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage

Gdy potrzebne do inspekcji usługi przy użyciu usługi Azure Storage na podstawie najnowszych zgodności i wymagań dotyczących zabezpieczeń, protokołu SSL w wersji 1.0, 2.0, 3.0 i TLS 1.0 są uznawane za protokoły komunikacyjne braku zgodności.

Znaleziono SSL 1.0, 2.0 i 3.0 są narażone. Mają one został zabroniony RFC. Protokół TLS 1.0, staje się niezabezpieczone niezabezpieczone cipher Block (DES-CBC i RC2 CBC) i Stream szyfrowania (RC4). PCI Rady sugerowane również migrację do nowszych wersji protokołu TLS. Aby uzyskać więcej informacji, zobacz [zabezpieczeń TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Usługa Azure Storage została zatrzymana protokołu SSL 3.0 w stosunku do 2015 i używa protokołu TLS 1.2 na publiczne punkty końcowe HTTPs, ale protokół TLS 1.0 i TLS 1.1 są nadal obsługiwane w przypadku zgodności z poprzednimi wersjami.

Aby zapewnić bezpieczeństwo i zgodność połączenie do usługi Azure Storage, musisz włączyć protokół TLS 1.2 w po stronie klienta przed wysłaniem żądania do działania usługi Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Włącz szyfrowanie TLS 1.2 w klienta platformy .NET

Klienta w celu negocjowania protokołu TLS 1.2, systemu operacyjnego i wersji programu .NET Framework zarówno na potrzeby obsługi protokołu TLS 1.2. Zobacz więcej szczegółów w [obsługę protokołu TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

Poniższy przykład pokazuje sposób włączania protokołu TLS 1.2 w swoim kliencie .NET.

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

## <a name="enable-tls-12-in-powershell-client"></a>Włącz szyfrowanie TLS 1.2 w kliencie programu PowerShell

Poniższy przykład pokazuje sposób włączania protokołu TLS 1.2 w kliencie programu PowerShell.

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

## <a name="verify-tls-12-connection"></a>Weryfikowanie połączenia protokołu TLS 1.2

Za pomocą narzędzia Fiddler do sprawdzenia, czy protokół TLS 1.2 są rzeczywiście używane. Otwórz program Fiddler Rozpocznij przechwytywanie ruchu sieciowego klienta, a następnie wykonaj powyższe przykładowe. Następnie można znaleźć wersji protokołu TLS w połączeniu, który sprawia, że plik.

Poniższy zrzut ekranu przedstawia przykład weryfikacji.

![Zrzut ekranu przedstawiający sprawdzania wersji protokołu TLS w narzędziu Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zobacz także

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Włącz szyfrowanie TLS w kliencie języka Java](https://www.java.com/en/configure_crypto.html)
