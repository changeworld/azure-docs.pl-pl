---
title: Tworzenie klastra usługi Azure Service Fabric przy użyciu nazwy pospolitej certyfikatu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster usługi Service Fabric przy użyciu nazwy pospolitej certyfikatu na podstawie szablonu.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: 5f2ba517e68dcd37de44bd26f476442e80817086
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498030"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Wdrażanie klastra usługi Service Fabric, która używa nazwy pospolitej certyfikatu zamiast odcisku palca
Nie dwóch certyfikatów może mieć ten sam odcisk palca, który sprawia, że Przerzucanie certyfikatów klastra lub zarządzania trudne. Wiele certyfikatów, mogą jednak mieć tę samą nazwę pospolitą lub temat.  Klastra przy użyciu nazwy pospolite certyfikatów sprawia, że certyfikat zarządzania jest znacznie prostsze. W tym artykule opisano, jak wdrożyć klaster usługi Service Fabric do użycia nazwy pospolitej certyfikatu zamiast odcisk palca certyfikatu.
 
## <a name="get-a-certificate"></a>Uzyskaj certyfikat
Najpierw należy uzyskać certyfikat z [certyfikatu urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority).  Nazwa pospolita certyfikatu powinna być się dla domeny niestandardowej aplikacjom i zakupionych u rejestratora domen. Na przykład "azureservicefabricbestpractices.com;" te, których nie są pracownikami firmy Microsoft nie aprowizować certyfikaty dla domen MS, więc nazwy DNS modułu równoważenia obciążenia lub usługi Traffic Manager nie można użyć jako nazwy pospolite dla certyfikatu, a następnie należy aprowizować [strefy DNS platformy Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) Jeśli niestandardowe Domena jako możliwej do rozpoznania na platformie Azure. Należy również zadeklarować domenę niestandardową, pochodzącymi jako Twojego klastra "element"managementEndpoint, jeśli chcesz, aby portal w celu odzwierciedlenia domeny niestandardowej alias dla klastra.

Do celów testowych można pobrać certyfikatu podpisanego przez urząd certyfikacji od urzędu certyfikacji bezpłatnej lub otwarte.

> [!NOTE]
> Certyfikaty z podpisem własnym, włącznie z wygenerowanymi w przypadku wdrażania klastra usługi Service Fabric w witrynie Azure portal nie są obsługiwane. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Przekaż certyfikat do magazynu kluczy
Na platformie Azure klaster usługi Service Fabric jest wdrażany w zestawie skalowania maszyn wirtualnych.  Przekaż certyfikat do magazynu kluczy.  Podczas wdrażania klastra, certyfikat instaluje na zestaw skalowania maszyn wirtualnych, że klaster działa na.

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

## <a name="download-and-update-a-sample-template"></a>Pobierz i zaktualizuj przykładowy szablon
W tym artykule wykorzystano [przykład bezpiecznego klastra z 5 węzłami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) szablon i parametry szablonu. Pobierz *azuredeploy.json* i *azuredeploy.parameters.json* plików do tego komputera.

### <a name="update-parameters-file"></a>Aktualizowanie pliku parametrów
Najpierw otwórz *azuredeploy.parameters.json* plik w edytorze tekstów i dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Następnym etapem jest skonfigurowanie *certificateCommonName*, *sourceVaultValue*, i *certificateUrlValue* wartości parametrów do wartości zwracanych przez powyższy skrypt:
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
Następnie otwórz *azuredeploy.json* plik w edytorze tekstów i trzy aktualizacje do obsługi nazwy pospolitej certyfikatu.

1. W **parametry** Dodaj *certificateCommonName* parametru:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Należy również rozważyć usunięcie *certificateThumbprint*, może nie będą już potrzebne.

2. Ustaw wartość *sfrpApiVersion* zmienną "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. W **Microsoft.Compute/virtualMachineScaleSets** zasób, zaktualizuj rozszerzenie maszyny wirtualnej do użycia nazwy pospolitej w ustawieniach certyfikatu zamiast odcisku palca.  W **virtualMachineProfile**->**extensionProfile**->**rozszerzenia**->**właściwości** -> **ustawienia**->**certyfikatu**, Dodaj 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    i Usuń `"thumbprint": "[parameters('certificateThumbprint')]",`.

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

4. W **Microsoft.ServiceFabric/clusters** zasobu wersji aktualizacji interfejsu API "2018-02-01".  Również dodać **certificateCommonNames** ustawienie z **commonNames** właściwości i Usuń **certyfikatu** ustawienie (za pomocą właściwości odcisk palca), w następującej przykład:
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
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Pole "certificateIssuerThumbprint" umożliwia określenie oczekiwanego wystawców certyfikatów o nazwie wspólnej dany podmiot. To pole akceptuje rozdzielaną przecinkami wyliczenie odcisków palca SHA1. Należy pamiętać, że to wzmocnienia weryfikację certyfikatu — w przypadku, gdy wystawcy nie została określona lub pusta, certyfikat zostanie zaakceptowany do uwierzytelniania, jeśli mogą być wbudowane w łańcuchu i kończy się się w katalogu głównym zaufany przez moduł weryfikacji. Jeśli wystawca jest określony, certyfikat zostanie zaakceptowane, jeśli odcisk palca jej bezpośredniego wystawcę pasuje do żadnej wartości określonej w tym polu — niezależnie od tego, czy katalog główny jest zaufany, czy nie. Należy pamiętać, że infrastruktura kluczy publicznych może używać różnych urzędów certyfikacji do wystawiania certyfikatów dla tego samego podmiotu, a więc jest ważne określić wszystkie oczekiwanego wystawcy odciski palców dla danego podmiotu.
   >
   > Określanie wystawca jest uznawany za najlepszą praktyką. pomijając go będą nadal działać — certyfikaty łańcucha zaufany główny urząd certyfikacji — to zachowanie ma ograniczenia i może wycofać w niedalekiej przyszłości. Należy także zauważyć klastrach wdrożonych na platformie Azure i zabezpieczony za pomocą X509 certyfikaty wystawione przez prywatne PKI i zadeklarować według tematu może nie dawać możliwości ma zostać zweryfikowana przez usługę Azure Service Fabric (dla komunikacji usługi klastrowania), jeśli zasady dotyczące certyfikatów PKI nie jest dostępne i prostsze do odnalezienia, dostępne. 

## <a name="deploy-the-updated-template"></a>Wdrożyć zaktualizowany szablon
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
* Dowiedz się, jak [Przerzucanie certyfikatów klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie i zarządzanie certyfikatami klastra](service-fabric-cluster-security-update-certs-azure.md)
* Uproszczenie zarządzania certyfikatu przez [klastra zmiana z odcisk palca certyfikatu na nazwę pospolitą](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
