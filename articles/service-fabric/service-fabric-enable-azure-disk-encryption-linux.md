---
title: Włącz szyfrowanie dysków dla klastrów systemu Linux usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania szyfrowania dysku dla węzłów klastra usługi Azure Service Fabric w systemie Linux przy użyciu usługi Azure Resource Manager i usługi Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258763"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Włącz szyfrowanie dysków dla węzłów klastra usługi Azure Service Fabric w systemie Linux 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Szyfrowanie dysków dla Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

W tym samouczku dowiesz się, jak włączyć szyfrowanie dysków w węzłach klastra usługi Azure Service Fabric w systemie Linux. Należy wykonać następujące kroki dla każdego z typów węzłów i zestawy skalowania maszyn wirtualnych. Szyfrowanie na węzłach, użyjemy funkcji usługi Azure Disk Encryption na zestawach skalowania maszyn wirtualnych.

Przewodnik obejmuje następujące tematy:

* Podstawowe pojęcia dotyczące pod uwagę podczas ustawia włączenie szyfrowania dysków na skalowania maszyn wirtualnych z klastra usługi Service Fabric w systemie Linux.
* Kroki, aby wykonać przed włączeniem szyfrowania dysków w usłudze Service Fabric węzły klastra w systemie Linux.
* Kroki, aby wykonać umożliwia włączenie szyfrowania dysku na węzłach klastra usługi Service Fabric w systemie Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

 **Autorejestracja**

Podgląd szyfrowania dysku dla zestawu skalowania maszyn wirtualnych wymaga rejestracji automatycznej. Wykonaj następujące czynności:

1. Uruchom następujące polecenie: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Odczekaj około 10 minut, aż stan odczytuje *zarejestrowanej*. Stan można sprawdzić, uruchamiając następujące polecenie:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Usługa Azure Key Vault**

1. Tworzenie magazynu kluczy w tej samej subskrypcji i regionie co zestaw skalowania. Następnie wybierz pozycję **EnabledForDiskEncryption** dostęp do zasad w magazynie kluczy przy użyciu jego polecenia cmdlet programu PowerShell. Można także ustawić zasady przy użyciu interfejsu użytkownika z magazynu Key w witrynie Azure portal, za pomocą następującego polecenia:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), który udostępnia nowe polecenia szyfrowania.

3. Zainstaluj najnowszą wersję [zestawu Azure SDK za pomocą programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) wydania. Poniżej przedstawiono polecenia cmdlet usługi Azure Disk Encryption w celu włączenia zestawu skalowania maszyn wirtualnych ([ustaw](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) szyfrowania, pobrać ([uzyskać](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stanu szyfrowania, a następnie usuń ([wyłączyć](/powershell/module/az.compute/disable-azvmssdiskencryption)) wystąpienie zestawu szyfrowania na skali.


| Polecenie | Wersja |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 lub nowszym | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Obsługiwane scenariusze dotyczące szyfrowania dysku
* Szyfrowanie dla zestawów skalowania maszyn wirtualnych jest obsługiwana tylko w przypadku zestawów skalowania utworzonych za pomocą dysków zarządzanych. Nie jest obsługiwana dla zestawów skalowania dysku natywnego (lub niezarządzanego).
* Szyfrowanie i wyłączenie szyfrowania są obsługiwane w przypadku woluminów systemu operacyjnego i danych w zestawach skalowania maszyn wirtualnych w systemie Linux. 
* Operacji odtworzenia z obrazu i uaktualnianie maszyny wirtualnej (VM) dla zestawów skalowania maszyn wirtualnych nie są obsługiwane w bieżącej wersji zapoznawczej.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Tworzenie nowego klastra i włączanie szyfrowania dysków

Użyj następujących poleceń do utworzenia klastra i włączyć szyfrowanie dysku przy użyciu szablonu usługi Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure  

Zaloguj się przy użyciu następujących poleceń:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, która już istnieje 

Jeśli musisz utworzyć szablon niestandardowy, zdecydowanie zalecamy użycie jednego z szablonów na [przykłady szablonów tworzenia klastra usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) strony. 

Jeśli masz już szablon niestandardowy, należy dokładnie sprawdzić następujące nazwy wszystkich trzech parametrów związane z certyfikatem w szablonu i pliku parametrów. Ponadto upewnij się, że wartości null w następujący sposób:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Tylko szyfrowanie dysku danych jest obsługiwane dla zestawów skalowania maszyn wirtualnych w systemie Linux, należy dodać dysk z danymi przy użyciu szablonu usługi Resource Manager. Zaktualizuj szablon do dostarczania dysku danych w następujący sposób:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

W tym miejscu jest równoważne polecenia interfejsu wiersza polecenia. Zmień wartości w instrukcji declare odpowiednie wartości. Interfejs wiersza polecenia obsługuje wszystkie parametry, które obsługuje poprzednie polecenie programu PowerShell.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Zainstaluj dysk danych do wystąpienia systemu Linux
Przed kontynuowaniem za pomocą szyfrowania na zestaw skalowania maszyn wirtualnych, upewnij się, że dysk dodane dane jest poprawnie zainstalowany. Zaloguj się do klastra systemu Linux maszyny Wirtualnej, a następnie uruchom **LSBLK** polecenia. Dane wyjściowe powinny być widoczne na tym dysku dodane dane w **punkt instalacji** kolumny.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric w systemie Linux
Aby wdrożyć aplikację w klastrze, wykonaj kroki i wskazówek dostępnych pod adresem [Szybki Start: Wdrażanie kontenerów systemu Linux w usłudze Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Włącz szyfrowanie dysków dla zestawów skalowania maszyn wirtualnych utworzoną wcześniej
Aby włączyć szyfrowanie dysków dla skalowania maszyn wirtualnych ustawia został utworzony przez poprzednie kroki, uruchom następujące polecenia:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Sprawdź, czy ma włączone szyfrowanie dysku maszyny wirtualnej zestawu skalowania w systemie Linux
Aby uzyskać stan zestawu skalowania całej maszyny wirtualnej lub dowolnego wystąpienia w zestawie skalowania, uruchom następujące polecenia.
Ponadto możesz zalogować się do klastra systemu Linux maszyny Wirtualnej i uruchomić **LSBLK** polecenia. Dane wyjściowe powinny być widoczne na dysku dodane dane w **punkt instalacji** kolumny, a **typu** kolumny powinni przeczytać *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Wyłącz szyfrowanie dysków dla maszyny wirtualnej zestawu skalowania w klastrze usługi Service Fabric
Wyłącz szyfrowanie dysków dla maszyny wirtualnej zestawu skalowania, uruchamiając następujące polecenia. Należy pamiętać, że wyłączenie szyfrowania dysku ma zastosowanie do całej maszyny wirtualnej zestawu skalowania, a nie poszczególnych wystąpień.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Kolejne kroki
W tym momencie należy mieć zabezpieczonego klastra i wiedzieć, jak włączyć lub wyłączyć szyfrowanie dysków dla zestawów skalowania maszyn wirtualnych i węzłów klastra usługi Service Fabric. Podobne wskazówki w węzłach klastra usługi Service Fabric w systemie Linux, zobacz [szyfrowania dysku dla Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
