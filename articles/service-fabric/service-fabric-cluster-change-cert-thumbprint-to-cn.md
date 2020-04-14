---
title: Aktualizowanie klastra w celu używania nazwy pospolitej certyfikatu
description: Dowiedz się, jak przełączyć klaster sieci szkieletowej usług z używania odcisków palców certyfikatów na używanie nazwy pospolitej certyfikatu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 1926b0501766eb0a5fe086ceada0c9bf45e3dcf6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272631"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Change cluster from certificate thumbprint to common name (Zmienianie klastra z odcisku palca certyfikatu na nazwę pospolitą)
Żadne dwa certyfikaty nie mogą mieć tego samego odcisku palca, co utrudnia najazd lub zarządzanie certyfikatami klastra. Wiele certyfikatów może jednak mieć tę samą nazwę pospolitą lub podmiot.  Przełączenie wdrożonego klastra z używania odcisków palca certyfikatu na używanie nazw pospolitych certyfikatów sprawia, że zarządzanie certyfikatami jest znacznie prostsze. W tym artykule opisano sposób aktualizowania uruchomionego klastra sieci szkieletowej usług, aby używać nazwy pospolitej certyfikatu zamiast odcisku palca certyfikatu.

>[!NOTE]
> Jeśli masz dwa odcisk palca zadeklarowane w szablonie, należy wykonać dwa wdrożenia.  Pierwsze wdrożenie odbywa się przed wykonaniu kroków w tym artykule.  Pierwsze wdrożenie ustawia właściwość **odcisk palca** w szablonie do używanego certyfikatu i usuwa **właściwość thumbprintSecondary.**  W przypadku drugiego wdrożenia wykonaj kroki opisane w tym artykule.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Uzyskaj certyfikat
Najpierw uzyskaj certyfikat od [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority).  Wspólna nazwa certyfikatu powinna być dla domeny niestandardowej, której jesteś właścicielem, i kupiona od rejestratora domeny. Na przykład "azureservicefabricbestpractices.com"; ci, którzy nie są pracownikami firmy Microsoft, nie mogą aprowizować certyfikatów dla domen MS, więc nie można używać nazw DNS usługi LB lub Traffic Manager jako nazw pospolitych dla certyfikatu i konieczne będzie zainicjowanie obsługi [administracyjnej strefy DNS platformy Azure,](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) jeśli domena niestandardowa ma być rozpoznawana na platformie Azure. Należy również zadeklarować domenę niestandardową, której jesteś właścicielem, jako "managementEndpoint" klastra, jeśli chcesz, aby portal odzwierciedlał niestandardowy alias domeny dla klastra.

Do celów testowych można uzyskać certyfikat podpisany przez urząd certyfikacji z bezpłatnego lub otwartego urzędu certyfikacji.

> [!NOTE]
> Certyfikaty z podpisem własnym, w tym certyfikaty wygenerowane podczas wdrażania klastra sieci szkieletowej usług w witrynie Azure portal, nie są obsługiwane. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Prześlij certyfikat i zainstaluj go w zestawie skalowania
Na platformie Azure klaster sieci szkieletowej usług jest wdrażany na zestawie skalowania maszyny wirtualnej.  Przekaż certyfikat do magazynu kluczy, a następnie zainstaluj go w zestawie skalowania maszyny wirtualnej, na który jest uruchomiony klaster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

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
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> Wpisy tajne zestawu skalowania nie obsługują tego samego identyfikatora zasobu dla dwóch oddzielnych wpisów tajnych, ponieważ każdy klucz tajny jest wersjonawczonym, unikatowym zasobem. 

## <a name="download-and-update-the-template-from-the-portal"></a>Pobieranie i aktualizowanie szablonu z portalu
Certyfikat został zainstalowany w podstawowym zestawie skalowania, ale należy również zaktualizować klaster sieci szkieletowej usług, aby użyć tego certyfikatu i jego nazwy pospolitej.  Teraz pobierz szablon dla wdrożenia klastra.  Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do grupy zasobów hostującej klaster.  W **obszarze Ustawienia**wybierz pozycję **Wdrożenia**.  Wybierz najnowsze wdrożenie i kliknij pozycję **Wyświetl szablon**.

![Wyświetlanie szablonów][image1]

Pobierz szablon i parametry plików JSON na komputer lokalny.

Najpierw otwórz plik parametrów w edytorze tekstu i dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Następnie otwórz plik szablonu w edytorze tekstu i włóż trzy aktualizacje do obsługi nazwy pospolitej certyfikatu.

1. W sekcji **parametry** dodaj *parametr certificateCommonName:*
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Należy również rozważyć usunięcie *certyfikatuThumbprint*, może nie być już odwołuje się w szablonie Menedżera zasobów.

2. W zasobie **Microsoft.Compute/virtualMachineScaleSets** zaktualizuj rozszerzenie maszyny wirtualnej, aby używało nazwy pospolitej w ustawieniach certyfikatu zamiast odcisku palca.  W **virtualMachineProile**->**extensionProile**->**rozszerzenia**->**certyfikat**->ustawień**właściwości**->**,** dodaj `"commonNames": ["[parameters('certificateCommonName')]"],` i usuń `"thumbprint": "[parameters('certificateThumbprint')]",`.
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
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  W zasobie **Microsoft.ServiceFabric/clusters** zaktualizuj wersję interfejsu API do "2018-02-01".  Dodaj również **ustawienie certificateCommonNames** z **właściwość commonNames** i usuń ustawienie **certyfikatu** (z właściwością odcisku palca), jak w poniższym przykładzie:
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

Aby uzyskać dodatkowe informacje, zobacz [Wdrażanie klastra sieci szkieletowej usług, który używa nazwy pospolitej certyfikatu zamiast odcisku palca.](https://docs.microsoft.com/azure/service-fabric/service-fabric-create-cluster-using-cert-cn)

## <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowanego szablonu
Ponowne wdrożenie zaktualizowanego szablonu po wszczęciem zmian.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* Dowiedz się, jak [przerzucić certyfikat klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
