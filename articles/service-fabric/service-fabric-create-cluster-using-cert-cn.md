---
title: Tworzenie klastra usługi sieć szkieletowa usług Azure przy użyciu nazwa pospolita certyfikatu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster sieci szkieletowej usług za pomocą nazwa pospolita certyfikatu na podstawie szablonu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 8725dd1931b120b0369d0810fa49108a00c71e8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211069"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Wdrażanie klastra usługi sieć szkieletowa usług używającej nazwa pospolita certyfikatu zamiast odcisk palca
Nie dwa certyfikaty mogą mieć tym samym odciskiem palca, dzięki czemu Przerzucanie certyfikatów klastra lub zarządzania trudne. Wiele certyfikatów, mogą jednak mieć tę samą nazwę pospolitą lub temat.  Klastra przy użyciu wspólnej nazwy certyfikatów sprawia, że certyfikat zarządzania jest znacznie prostsza. W tym artykule opisano sposób wdrażania klastra sieci szkieletowej usług, aby użyć nazwa pospolita certyfikatu zamiast odcisk palca certyfikatu.
 
## <a name="get-a-certificate"></a>Pobieranie certyfikatu
Najpierw należy uzyskać certyfikat z [certyfikatu urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority).  Nazwa pospolita certyfikatu powinna być nazwą hosta klastra.  Na przykład "myclustername.southcentralus.cloudapp.azure.com".  

Do celów testowych można pobrać certyfikatu podpisanego przez urząd certyfikacji od urzędu certyfikacji wolne lub otwarte.

> [!NOTE]
> Certyfikaty z podpisem własnym, włącznie z wygenerowanymi w przypadku wdrażania klastra sieci szkieletowej usług w portalu Azure, nie są obsługiwane.

## <a name="upload-the-certificate-to-a-key-vault"></a>Przekaż certyfikat do magazynu kluczy
Na platformie Azure klaster sieci szkieletowej usług jest wdrożony na zestaw skali maszyny wirtualnej.  Przekaż certyfikat do magazynu kluczy.  Podczas wdrażania klastra, certyfikat instaluje na zestaw skali maszyny wirtualnej uruchomionej na klastrze.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

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
```

## <a name="download-and-update-a-sample-template"></a>Pobieranie i aktualizowanie przykładowego szablonu
W tym artykule wykorzystano [przykład 5 węzłów klastra bezpiecznego](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) szablon i parametrów szablonu. Pobierz *azuredeploy.json* i *azuredeploy.parameters.json* plików do tego komputera.

### <a name="update-parameters-file"></a>Aktualizowanie pliku parametrów
Najpierw otwórz *azuredeploy.parameters.json* plik w edytorze tekstów i dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Następnie należy ustawić *certificateCommonName*, *sourceVaultValue*, i *certificateUrlValue* wartości parametrów do wartości zwracanych przez powyższy skrypt:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Zaktualizuj plik szablonu
Następnie otwórz folder *azuredeploy.json* plik w edytorze tekstów i trzy aktualizacje do obsługi nazwa pospolita certyfikatu.

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

2. Ustaw wartość *sfrpApiVersion* zmienną "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. W **Microsoft.Compute/virtualMachineScaleSets** zasobów, zaktualizuj rozszerzenie maszyny wirtualnej, aby użyć nazwy pospolitej w ustawieniach certyfikatu zamiast odcisk palca.  W **virtualMachineProfile**->**extenstionProfile**->**rozszerzenia**->**właściwości** -> **ustawienia**->**certyfikatu**, Dodaj `"commonNames": ["[parameters('certificateCommonName')]"],` i Usuń `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

4.  W **Microsoft.ServiceFabric/clusters** zasobów, wersja aktualizacji interfejsu API, aby "2018-02-01".  Dodaj również **certificateCommonNames** ustawienie **commonNames** właściwości i Usuń **certyfikatu** ustawienie (za pomocą właściwości odcisk palca) w następującej przykład:
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
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [klastra zabezpieczeń](service-fabric-cluster-security.md).
* Dowiedz się, jak [przerzucania certyfikatu klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie certyfikatów i zarządzania nimi klastra](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png