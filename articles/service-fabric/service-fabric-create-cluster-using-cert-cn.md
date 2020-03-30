---
title: Tworzenie klastra przy użyciu nazwy pospolitej certyfikatu
description: Dowiedz się, jak utworzyć klaster sieci szkieletowej usług przy użyciu nazwy pospolitej certyfikatu z szablonu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 4a4448c88fa9493979f075f6b9c669927dd1d39e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614557"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Wdrażanie klastra sieci szkieletowej usług, który używa nazwy pospolitej certyfikatu zamiast odcisku palca
Żadne dwa certyfikaty nie mogą mieć tego samego odcisku palca, co utrudnia najazd lub zarządzanie certyfikatami klastra. Wiele certyfikatów może jednak mieć tę samą nazwę pospolitą lub podmiot.  Klaster używający wspólnych nazw certyfikatów znacznie upraszcza zarządzanie certyfikatami. W tym artykule opisano sposób wdrażania klastra sieci szkieletowej usług w celu używania nazwy pospolitej certyfikatu zamiast odcisku palca certyfikatu.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Uzyskaj certyfikat
Najpierw uzyskaj certyfikat od [urzędu certyfikacji](https://wikipedia.org/wiki/Certificate_authority).  Wspólna nazwa certyfikatu powinna być dla domeny niestandardowej, której jesteś właścicielem, i kupiona od rejestratora domeny. Na przykład "azureservicefabricbestpractices.com"; ci, którzy nie są pracownikami firmy Microsoft, nie mogą aprowizować certyfikatów dla domen MS, więc nie można używać nazw DNS usługi LB lub Traffic Manager jako nazw pospolitych dla certyfikatu i konieczne będzie zainicjowanie obsługi [administracyjnej strefy DNS platformy Azure,](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) jeśli domena niestandardowa ma być rozpoznawana na platformie Azure. Należy również zadeklarować domenę niestandardową, której jesteś właścicielem, jako "managementEndpoint" klastra, jeśli chcesz, aby portal odzwierciedlał niestandardowy alias domeny dla klastra.

Do celów testowych można uzyskać certyfikat podpisany przez urząd certyfikacji z bezpłatnego lub otwartego urzędu certyfikacji.

> [!NOTE]
> Certyfikaty z podpisem własnym, w tym certyfikaty wygenerowane podczas wdrażania klastra sieci szkieletowej usług w witrynie Azure portal, nie są obsługiwane. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Przekazywanie certyfikatu do magazynu kluczy
Na platformie Azure klaster sieci szkieletowej usług jest wdrażany na zestawie skalowania maszyny wirtualnej.  Przekaż certyfikat do magazynu kluczy.  Po wdrożeniu klastra certyfikat jest instalowany na zestawie skalowania maszyny wirtualnej, na którym jest uruchomiony klaster.

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

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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
```

## <a name="download-and-update-a-sample-template"></a>Pobieranie i aktualizowanie przykładowego szablonu
W tym artykule użyto [5-węzłowego bezpiecznego szablonu klastra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) i parametrów szablonu. Pobierz pliki *azuredeploy.json* i *azuredeploy.parameters.json* na komputer.

### <a name="update-parameters-file"></a>Aktualizuj plik parametrów
Najpierw otwórz plik *azuredeploy.parameters.json* w edytorze tekstu i dodaj następującą wartość parametru:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Następnie ustaw *wartości parametrów certificateCommonName*, *sourceVaultValue*i *certificateUrlValue* na wartości zwracane przez poprzedni skrypt:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Aktualizowanie pliku szablonu
Następnie otwórz plik *azuredeploy.json* w edytorze tekstu i włóż trzy aktualizacje do obsługi nazwy pospolitej certyfikatu.

1. W sekcji **parametry** dodaj *parametr certificateCommonName:*
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Należy również rozważyć usunięcie *certyfikatuThumbprint*, może nie być już potrzebne.

2. Ustaw wartość zmiennej *sfrpApiVersion* na "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. W zasobie **Microsoft.Compute/virtualMachineScaleSets** zaktualizuj rozszerzenie maszyny wirtualnej, aby używało nazwy pospolitej w ustawieniach certyfikatu zamiast odcisku palca.  W **virtualMachineProile**->**extensionProfiles**->**properties**->**properties**->**settings**->**certificate**, add 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    i `"thumbprint": "[parameters('certificateThumbprint')]",`usuń .

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

4. W zasobie **Microsoft.ServiceFabric/clusters** zaktualizuj wersję interfejsu API do "2018-02-01".  Dodaj również **ustawienie certificateCommonNames** z **właściwość commonNames** i usuń ustawienie **certyfikatu** (z właściwością odcisku palca), jak w poniższym przykładzie:
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
   > Pole "certificateIssuerThumbprint" umożliwia określenie oczekiwanych wystawców certyfikatów o podanej nazwie pospolitej podmiotu. To pole akceptuje wyliczenie odcisków palców SHA1 oddzielone przecinkami. Należy zauważyć, że jest to wzmocnienie sprawdzania poprawności certyfikatu — w przypadku, gdy wystawca nie jest określony lub pusty, certyfikat zostanie zaakceptowany do uwierzytelniania, jeśli jego łańcuch może zostać utworzony, a kończy się w katalogu głównym zaufanym przez walidatora. Jeśli wystawca jest określony, certyfikat zostanie zaakceptowany, jeśli odcisk palca jego bezpośredniego wystawcy pasuje do dowolnej wartości określonej w tym polu — niezależnie od tego, czy katalog główny jest zaufany, czy nie. Należy pamiętać, że pKI może używać różnych urzędów certyfikacji do wydawania certyfikatów dla tego samego tematu, dlatego ważne jest, aby określić wszystkie oczekiwane odciski palców wystawcy dla danego tematu.
   >
   > Określenie wystawcy jest uważane za najlepszą praktykę; pomijając to będzie nadal działać - dla certyfikatów łączących się z zaufanym rootem - to zachowanie ma ograniczenia i może zostać wycofane w najbliższej przyszłości. Należy również pamiętać, że klastry wdrożone na platformie Azure i zabezpieczone certyfikatami X509 wystawionymi przez prywatną pki i zadeklarowaną przez podmiot mogą nie być w stanie zweryfikować ich przez usługę Azure Service Fabric (w przypadku komunikacji między klastrami a usługami), jeśli zasady certyfikatów infrastruktury Klucz pKI mogą być niesładzone nie jest wykrywalna, dostępna i dostępna. 

## <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowanego szablonu
Ponowne wdrożenie zaktualizowanego szablonu po wszczęciem zmian.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* Dowiedz się, jak [przerzucić certyfikat klastra](service-fabric-cluster-rollover-cert-cn.md)
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)
* Uproszczenie zarządzania certyfikatami przez [zmianę klastra z odcisku palca certyfikatu na nazwę pospolitą](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
