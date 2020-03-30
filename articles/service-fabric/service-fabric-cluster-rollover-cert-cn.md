---
title: Przewracaj certyfikat klastra sieci szkieletowej usługi Azure
description: Dowiedz się, jak przerzucić certyfikat klastra sieci szkieletowej usług identyfikowany przez nazwę pospolitą certyfikatu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451959"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ręczne przewracanie certyfikatu klastra sieci szkieletowej usług
Gdy certyfikat klastra sieci szkieletowej usług jest bliski wygaśnięcia, należy zaktualizować certyfikat.  Narzucie certyfikatu jest proste, jeśli klaster został [skonfigurowany do używania certyfikatów opartych na nazwie pospolitej](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (zamiast odcisku palca).  Uzyskaj nowy certyfikat od urzędu certyfikacji z nową datą wygaśnięcia.  Certyfikaty z podpisem własnym nie są obsługiwane dla klastrów produkcyjnych sieci szkieletowej usług, aby uwzględnić certyfikaty wygenerowane podczas przepływu pracy tworzenia klastra usługi Azure portal. Nowy certyfikat musi mieć taką samą nazwę pospolitą jak starszy certyfikat. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Klaster sieci szkieletowej usług automatycznie użyje zadeklarowanego certyfikatu z dalszą datą wygaśnięcia; gdy na hoście jest zainstalowany więcej niż jeden certyfikat sprawdzania poprawności. Najlepszym rozwiązaniem jest użycie szablonu Usługi Resource Manager do aprowizowania zasobów platformy Azure. W środowisku nieprodukcyjnym można użyć następującego skryptu do przekazania nowego certyfikatu do magazynu kluczy, a następnie zainstalowania certyfikatu w zestawie skalowania maszyny wirtualnej: 

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
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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
> Oblicza, że klucze tajności zestawu skalowania maszyny wirtualnej nie obsługują tego samego identyfikatora zasobu dla dwóch oddzielnych wpisów tajnych, ponieważ każdy klucz tajny jest wersjonawki unikatowym zasobem. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)
