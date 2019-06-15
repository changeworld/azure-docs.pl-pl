---
title: Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć protokół TLS 1.2 w kliencie usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 218708ffc9a680150d7b6bf559a00ca87054bbe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65152968"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Włączanie bezpiecznego protokołu TLS dla klienta usługi Azure Storage

Zabezpieczeń TLS (Transport Layer) i Secure Sockets Layer (SSL) są protokołów kryptograficznych, które zapewniają zabezpieczenia komunikacji za pośrednictwem sieci komputerowej. Znaleziono SSL 1.0, 2.0 i 3.0 są narażone. Mają one został zabroniony RFC. Protokół TLS 1.0, staje się niezabezpieczone niezabezpieczone cipher Block (DES-CBC i RC2 CBC) i Stream szyfrowania (RC4). PCI Rady sugerowane również migrację do nowszych wersji protokołu TLS. Aby uzyskać więcej informacji, zobacz [zabezpieczeń TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Usługa Azure Storage została zatrzymana protokołu SSL 3.0 w stosunku do 2015 i używa protokołu TLS 1.2 na publiczne punkty końcowe HTTPs, ale protokół TLS 1.0 i TLS 1.1 są nadal obsługiwane w przypadku zgodności z poprzednimi wersjami.

Aby zapewnić bezpieczeństwo i zgodność połączenie do usługi Azure Storage, musisz włączyć protokół TLS 1.2 lub nowszej wersji po stronie klienta przed wysłaniem żądania do działania usługi Azure Storage.

## <a name="enable-tls-12-in-net-client"></a>Włącz szyfrowanie TLS 1.2 w klienta platformy .NET

Klienta w celu negocjowania protokołu TLS 1.2, systemu operacyjnego i wersji programu .NET Framework zarówno na potrzeby obsługi protokołu TLS 1.2. Zobacz więcej szczegółów w [obsługę protokołu TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

Poniższy przykład pokazuje sposób włączania protokołu TLS 1.2 w kliencie programu PowerShell.

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

## <a name="verify-tls-12-connection"></a>Weryfikowanie połączenia protokołu TLS 1.2

Za pomocą narzędzia Fiddler do sprawdzenia, czy protokół TLS 1.2 są rzeczywiście używane. Otwórz program Fiddler Rozpocznij przechwytywanie ruchu sieciowego klienta, a następnie wykonaj powyższe przykładowe. Następnie można znaleźć wersji protokołu TLS w połączeniu, który sprawia, że plik.

Poniższy zrzut ekranu przedstawia przykład weryfikacji.

![Zrzut ekranu przedstawiający sprawdzania wersji protokołu TLS w narzędziu Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Zobacz także

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [Zgodności ze standardami PCI w protokołu TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Włącz szyfrowanie TLS w kliencie języka Java](https://www.java.com/en/configure_crypto.html)
