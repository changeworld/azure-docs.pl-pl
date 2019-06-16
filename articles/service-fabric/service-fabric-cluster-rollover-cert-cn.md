---
title: Przerzucanie certyfikatów klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak na Przerzucanie certyfikatów klastra usługi Service Fabric identyfikowane przez nazwy pospolitej certyfikatu.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: dd4b6026772a20c522532e1ba65c6846addfa161
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159896"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ręcznie przechodzą certyfikatu klastra usługi Service Fabric
Gdy certyfikat klastra usługi Service Fabric jest bliski wygaśnięcia, musisz zaktualizować certyfikat.  Przerzucanie certyfikatów jest proste, jeśli klaster został [do Użyj certyfikatów na podstawie nazwy wspólnej](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (zamiast odcisk palca).  Uzyskaj nowy certyfikat z urzędu certyfikacji z nową datę wygaśnięcia.  Certyfikaty z podpisem własnym nie są obsługiwane w środowisku produkcyjnym klastrów usługi Service Fabric, aby uwzględnić certyfikaty generowane podczas Azure portal klastra przepływ pracy tworzenia. Nowy certyfikat musi mieć tę samą nazwę pospolitą jako starszych certyfikatów. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Klaster usługi Service Fabric będzie automatycznie używać certyfikatu zadeklarowane z następnych na przyszłą datę wygaśnięcia; podczas sprawdzania poprawności więcej niż jeden certyfikat jest zainstalowany na hoście. Najlepszym rozwiązaniem jest użycie szablonu usługi Resource Manager do aprowizacji zasobów platformy Azure. W środowisku nieprodukcyjnym poniższy skrypt może służyć do przekazania nowego certyfikatu do magazynu kluczy, a następnie instaluje certyfikat na zestaw skalowania maszyn wirtualnych: 

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
> Oblicza hasła zestawu skali maszyny wirtualnej nie obsługują ten sam identyfikator zasobu dla dwóch osobnych wpisów tajnych oraz ich każdego wpisu tajnego jest wersjonowany unikatowy zasób. 

Aby dowiedzieć się więcej, przeczytaj następujące czynności:
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* [Aktualizowanie i zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)

