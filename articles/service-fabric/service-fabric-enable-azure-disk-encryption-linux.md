---
title: Włącz szyfrowanie dysków dla klastrów systemu Linux usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć szyfrowanie dysków dla usługi Service Fabric klaster zestawie skalowania na platformie Azure za pomocą usługi Azure Resource Manager, usługi Azure Key Vault.
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
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161784"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Włącz szyfrowanie dysków dla węzłów klastra systemu Linux usługi w sieci szkieletowej 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Szyfrowanie dysków dla Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Wykonaj poniższe kroki, aby włączyć szyfrowanie dysku w węzłach usługi Service Fabric systemu Linux klastra. Konieczne będzie wykonywane dla każdego węzła typy/wirtualnych zestawów skalowania. Szyfrowanie na węzłach, będziemy korzystać możliwości usługi Azure Disk Encryption na zestawach skalowania maszyn wirtualnych.

Przewodnik obejmuje następujące procedury:

* Ustaw kluczowe założenia, które należy znać wyłączanie umożliwia włączenie szyfrowania dysku na skalowania maszyn wirtualnych klastra systemu Linux usługi Service Fabric.
* Wymagania wstępne instrukcje występować przed włączeniem szyfrowania dysków na zestaw skalowania maszyn wirtualnych klastra systemu Linux usługi Service Fabric.
* Ustaw kroki, aby wykonać umożliwia włączenie szyfrowania dysku na skalowania maszyn wirtualnych klastra systemu Linux usługi Service Fabric.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* **Autorejestracja** — aby można było używać, maszyna wirtualna skalowanie zestawu dysków szyfrowania w wersji zapoznawczej wymaga rejestracji automatycznej
* Mogą samodzielnie zarejestrować subskrypcję, uruchamiając następujące czynności: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Poczekaj około 10 minut, aż stan jako "Zarejestrowane". Stan możesz sprawdzić, uruchamiając następujące polecenie: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Usługa Azure Key Vault** — Utwórz magazyn kluczy w tej samej subskrypcji i regionu, ponieważ ustawiony skalowania maszyn wirtualnych, a także ustawić zasady dostępu "EnabledForDiskEncryption" w magazynie kluczy, za pomocą polecenia cmdlet jego PS. Można też ustawić zasady za pomocą usługi KeyVault interfejsie użytkownika w witrynie Azure portal: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Zainstaluj najnowsze [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , który udostępnia nowe polecenia szyfrowania.
* Zainstaluj najnowszą wersję [zestawu Azure SDK za pomocą programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) wydania. Oto ADE poleceń cmdlet, aby włączyć zestaw skalowania maszyn wirtualnych ([ustaw](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) szyfrowania, pobrać ([uzyskać](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stanu szyfrowania i Usuń ([wyłączyć](/powershell/module/az.compute/disable-azvmssdiskencryption)) szyfrowania w skali wystąpienie zestawu. 

| Polecenie | Wersja |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 lub nowszym | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Obsługiwane scenariusze dotyczące szyfrowania dysku
* Szyfrowanie zestaw skali maszyny wirtualnej jest obsługiwana tylko w przypadku zestawów skalowania utworzonych za pomocą dysków zarządzanych, a nie jest obsługiwane dla zestawów skalowania dysku natywnego (lub niezarządzanego).
* Szyfrowanie zestaw skali maszyny wirtualnej jest obsługiwana dla wolumin danych dla zestawu skalowania maszyn wirtualnych systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla systemu Linux.
* Odtworzenia z obrazu maszyny Wirtualnej zestawu skalowania maszyn wirtualnych i uaktualniania przeprowadzanych nie są obsługiwane w bieżącej wersji zapoznawczej.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Utwórz nowy klaster systemu Linux i włączyć szyfrowanie dysku

Użyj następujących poleceń, aby utworzyć klaster i włączyć szyfrowanie dysku przy użyciu szablonu usługi Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, która już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów usługi Usługa azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) dla klastra systemu Linux. 

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że wartości i sprawdź następujące nazwy wszystkich trzech związane z certyfikatem parametrów w szablonie i plik parametrów ma wartość null w następujący sposób.

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

Ponieważ dla zestawu skalowania maszyn wirtualnych systemu Linux — tylko szyfrowanie dysku danych jest obsługiwana, dlatego musimy dodać dysku danych przy użyciu szablonu usługi Azure Resource Manager. Aktualizowanie szablonu do dostarczania dysku danych, tak jak pokazano poniżej:

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

W tym miejscu jest równoważne polecenia interfejsu wiersza polecenia to samo. Zmień wartości w instrukcji declare odpowiednie wartości. Interfejs wiersza polecenia obsługuje wszystkie parametry, które obsługuje powyższego polecenia programu powershell.

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

#### <a name="linux-data-disk-mounting"></a>Instalowanie dysk danych systemu Linux
Aby można było kontynuować za pomocą szyfrowania na zestaw skalowania maszyn wirtualnych systemu Linux, musimy upewnić się, że dysk dodane dane jest zainstalowany poprawnie, czy nie. Zaloguj się do maszyny Wirtualnej klastra systemu Linux, a następnie uruchom polecenie LSBLK. Dane wyjściowe powinny pokazywać dysku dodane dane w kolumnie punktu instalacji.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze systemu Linux usługi Service Fabric
Wykonaj kroki i wskazówki dotyczące [wdrożyć aplikację w klastrze](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Włącz szyfrowanie dysków dla zestawu skalowania maszyn wirtualnych klastra systemu Linux usługi Service Fabric utworzonego powyżej
 
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Sprawdź, czy zestawu skali maszyny wirtualnej systemu Linux jest włączone szyfrowanie dysków.
Pobierz stan całej maszyny wirtualnej zestawu skalowania lub dowolne wystąpienie maszyny Wirtualnej w zestawie skalowania. Zobacz poniższe polecenia.
Ponadto użytkownik może zalogować się do maszyny Wirtualnej klastra systemu Linux i uruchom polecenie LSBLK. Dane wyjściowe powinny pokazywać dysku dodane dane na kolumny punktu instalacji i typ jako Crypt dodane dane dysku.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Wyłącz szyfrowanie dysków dla zestawu skalowania maszyn wirtualnych klastra usługi Service Fabric 
Wyłącz szyfrowanie dysków ma zastosowanie do całej maszyny wirtualnej zestawu skalowania, a nie przez wystąpienia 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz bezpiecznego klastra, jak włączyć/wyłączyć szyfrowanie dysków dla zestawu skalowania maszyn wirtualnych klastra systemu Linux usługi Service Fabric. Następnie [dysku szyfrowanie dla Windows](service-fabric-enable-azure-disk-encryption-windows.md) 
