---
title: Zaktualizować klastra usługi sieć szkieletowa usług Azure umożliwia nazwa pospolita certyfikatu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przejść klastra sieci szkieletowej usług z przy użyciu odciski palców certyfikatów przy użyciu nazwa pospolita certyfikatu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 39dc5800edd743cdc950c7a96f7633fb4c0a7c45
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Zmień klastra z odcisk palca certyfikatu nazwa pospolita
Nie dwa certyfikaty mogą mieć tym samym odciskiem palca, dzięki czemu Przerzucanie certyfikatów klastra lub zarządzania trudne. Wiele certyfikatów, mogą jednak mieć tę samą nazwę pospolitą lub temat.  Przełączanie wdrożonej klastra z przy użyciu odciski palców certyfikatów przy użyciu wspólnej nazwy certyfikatów sprawia, że zarządzanie certyfikatami znacznie prostsze. W tym artykule opisano sposób aktualizacji klastra sieci szkieletowej usług uruchomionych do użycia nazwa pospolita certyfikatu zamiast odcisk palca certyfikatu.
 
## <a name="get-a-certificate"></a>Pobieranie certyfikatu
Najpierw należy uzyskać certyfikat z [certyfikatu urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority).  Nazwa pospolita certyfikatu powinna być nazwą hosta klastra.  Na przykład "myclustername.southcentralus.cloudapp.azure.com".  

Do celów testowych można pobrać certyfikatu podpisanego przez urząd certyfikacji od urzędu certyfikacji wolne lub otwarte.

> [!NOTE]
> Certyfikaty z podpisem własnym, włącznie z wygenerowanymi w przypadku wdrażania klastra sieci szkieletowej usług w portalu Azure, nie są obsługiwane.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Przekaż certyfikat, a następnie zainstaluj go w zestawie skalowania
Na platformie Azure klaster sieci szkieletowej usług jest wdrożony na zestaw skali maszyny wirtualnej.  Przekaż certyfikat do magazynu kluczy, a następnie zainstaluj go na zestaw skali maszyny wirtualnej uruchomionej na klastrze.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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

## <a name="download-and-update-the-template-from-the-portal"></a>Pobierz i aktualizację szablonu z portalu
Certyfikat został zainstalowany na podstawowy zestaw skali, ale należy również zaktualizować klaster sieci szkieletowej usług, aby użyć tego certyfikatu i jego nazwa pospolita.  Teraz Pobierz szablon wdrożenia klastra.  Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do obsługi klastra grupy zasobów.  W **ustawienia**, wybierz pozycję **wdrożeń**.  Wybierz najnowsze wdrożenie, a następnie kliknij przycisk **Wyświetl szablon**.

![Wyświetl szablony][image1]

Pobierz pliki JSON szablonu i parametrów, na komputerze lokalnym.

Po pierwsze Otwórz plik parametrów w edytorze tekstów i dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Następnie otwórz plik szablonu w edytorze tekstów i trzy aktualizacje do obsługi nazwa pospolita certyfikatu.

1. W **parametry** Dodaj *certificateCommonName* parametru:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Należy również rozważyć usunięcie *certificateThumbprint*, nie mogą być potrzebne.

2. W **Microsoft.Compute/virtualMachineScaleSets** zasobów, zaktualizuj rozszerzenie maszyny wirtualnej, aby użyć nazwy pospolitej w ustawieniach certyfikatu zamiast odcisk palca.  W **virtualMachineProfile**->**extenstionProfile**->**rozszerzenia**->**właściwości** -> **ustawienia**->**certyfikatu**, Dodaj `"commonNames": ["[parameters('certificateCommonName')]"],` i Usuń `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
    "virtualMachineProfile": {
              "extensionProfile": {
                "extensions": [
                  {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": true,
                      "protectedSettings": {
                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                      },
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[variables('vmNodeType0Name')]",
                        "dataPath": "D:\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "enableParallelJobs": true,
                        "nicPrefixOverride": "[variables('subnet0Prefix')]",
                        "certificate": {
                          "commonNames": ["[parameters('certificateCommonName')]"],                          
                          "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
                      "typeHandlerVersion": "1.0"
                    }
                  },
    ```

3.  W **Microsoft.ServiceFabric/clusters** zasobów, wersja aktualizacji interfejsu API, aby "2018-02-01".  Dodaj również **certificateCommonNames** ustawienie **commonNames** właściwości i Usuń **certyfikatu** ustawienie (za pomocą właściwości odcisk palca) w następującej przykład:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowany szablon
Po wprowadzeniu zmian, należy ponownie wdrożyć zaktualizowany szablon.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"

New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" -Verbose
```

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* Dowiedz się, jak [przerzucania certyfikatu klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie certyfikatów i zarządzania nimi klastra](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png