---
title: Przerzucanie certyfikatów klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak na Przerzucanie certyfikatów klastra usługi sieć szkieletowa identyfikowane przez nazwa pospolita certyfikatu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: df919e23fd608cdf41e93844f13342ca00657adb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205148"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ręcznie przerzucane certyfikatu klastra sieci szkieletowej usług
Gdy certyfikat z klastra usługi sieć szkieletowa jest bliski wygaśnięcia, musisz zaktualizować certyfikat.  Przerzucanie certyfikatów jest proste, jeśli klaster został [Ustaw maksymalnie Użyj certyfikatów na podstawie nazwy wspólnej](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (zamiast odcisk palca).  Uzyskaj nowy certyfikat od urzędu certyfikacji z nową datą wygaśnięcia.  Certyfikaty z podpisem własnym, włącznie z wygenerowanymi w przypadku wdrażania klastra sieci szkieletowej usług w portalu Azure, nie są obsługiwane.  Nowy certyfikat musi mieć tę samą nazwę pospolitą jako starszych certyfikatów. 

Poniższy skrypt przekazuje nowy certyfikat do magazynu kluczy, a następnie instaluje certyfikat na zestaw skali maszyny wirtualnej.  Klaster sieci szkieletowej usług będą automatycznie używać certyfikatu przy użyciu najnowszej daty wygaśnięcia.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

Aby dowiedzieć się więcej, przeczytaj następujące czynności:
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Aktualizowanie certyfikatów i zarządzania nimi klastra](service-fabric-cluster-security-update-certs-azure.md)

