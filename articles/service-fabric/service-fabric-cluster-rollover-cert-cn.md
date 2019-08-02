---
title: Przerzucanie certyfikatu klastra usługi Azure Service Fabric | Microsoft Docs
description: Dowiedz się, jak przerzucać Service Fabric certyfikat klastra identyfikowany przy użyciu nazwy pospolitej certyfikatu.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: atsenthi
ms.openlocfilehash: 5d11054ca8eb684f1f25a25ddeac1b53e82b3775
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599919"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ręczne przewinięcie certyfikatu klastra Service Fabric
Gdy certyfikat klastra Service Fabric jest bliski wygaśnięcia, należy zaktualizować certyfikat.  Przerzucanie certyfikatów jest proste, Jeśli klaster został [skonfigurowany do używania certyfikatów na podstawie nazwy pospolitej](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (zamiast odcisku palca).  Pobierz nowy certyfikat z urzędu certyfikacji z nową datą wygaśnięcia.  Certyfikaty z podpisem własnym nie obsługują klastrów Service Fabric produkcyjnych w celu uwzględnienia certyfikatów wygenerowanych podczas przepływu pracy tworzenia klastra Azure Portal. Nowy certyfikat musi mieć taką samą nazwę pospolitą jak certyfikat starszej wersji. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Klaster Service Fabric automatycznie będzie używać zadeklarowanego certyfikatu z dalszą datą wygaśnięcia w przyszłości. Jeśli na hoście jest zainstalowany więcej niż jeden certyfikat weryfikacji. Najlepszym rozwiązaniem jest użycie szablonu Menedżer zasobów w celu udostępnienia zasobów platformy Azure. W przypadku środowiska nieprodukcyjnego można użyć następującego skryptu do przekazania nowego certyfikatu do magazynu kluczy, a następnie zainstalowania certyfikatu na zestawie skalowania maszyn wirtualnych: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Oblicza wpisy tajne zestawu skalowania maszyn wirtualnych nie obsługują tego samego identyfikatora zasobu dla dwóch oddzielnych wpisów tajnych, ponieważ każdy klucz tajny jest unikatowym zasobem w wersji. 

Aby dowiedzieć się więcej, przeczytaj następujące tematy:
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)

