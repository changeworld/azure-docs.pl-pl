---
title: Włącz szyfrowanie dysków dla usługi sieci szkieletowej systemu Windows klastrów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć szyfrowanie dysków, węzłom klastra sieci szkieletowej usług na platformie Azure przy użyciu usługi Azure Resource Manager, usługi Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655749"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Włącz szyfrowanie dysków dla węzłów klastra systemu Windows sieci szkieletowej usług 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla systemu Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Wykonaj poniższe kroki, aby włączyć szyfrowanie dysków w węzłach klastra systemu Windows sieci szkieletowej usług. Konieczne będzie wykonywane dla każdego zestawy skalowania maszyn wirtualnych/typy węzła. Do szyfrowania w węzłach, firma Microsoft będzie korzystać z możliwości szyfrowania dysków Azure na zestawy skalowania maszyny wirtualnej.

Przewodnik obejmuje następujące procedury:

* Ustaw kluczowe założenia, które należy znać wyłączanie umożliwia włączenie szyfrowania dysków na skali maszyny wirtualnej klastra systemu Windows w sieci szkieletowej usług.
* Wymagania wstępne kroki, aby występować przed włączeniem szyfrowania dysków na zestaw skali maszyny wirtualnej klastra systemu Windows w sieci szkieletowej usług.
* Ustaw kroki można wykonać, aby włączyć szyfrowanie dysków na skali maszyny wirtualnej klastra systemu Windows w sieci szkieletowej usług.


## <a name="prerequisites"></a>Wymagania wstępne
1. **Autorejestracja** — w celu użycia, wersja zapoznawcza szyfrowania dysku maszyny wirtualnej skali zestaw wymaga rejestracji automatycznej
2. Subskrypcję można zarejestrować własnym, uruchamiając następujące czynności: 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Poczekaj około 10 minut, aż stan jako "Zarejestrowanej". Stan można sprawdzić, uruchamiając następujące polecenie: 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Usługa Azure Key Vault** — tworzenie KeyVault w tej samej subskrypcji i regionu skali ustawić i określać zasady dostępu "EnabledForDiskEncryption" na KeyVault przy użyciu polecenia cmdlet jego PS. Można też ustawić zasady przy użyciu interfejsu użytkownika KeyVault w portalu Azure: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Zainstaluj najnowsze [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , który zawiera nowe polecenia szyfrowania.
6. Zainstaluj najnowszą wersję pakietu [zestawu SDK platformy Azure z programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) wersji. Poniżej przedstawiono ADE poleceń cmdlet, aby włączyć zestawu skalowania maszyn wirtualnych ([ustawić](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) szyfrowania, pobrać ([uzyskać](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) stanu szyfrowania i Usuń ([wyłączyć](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) szyfrowania w zestawie skalowania wystąpienie.

| Polecenie | Wersja |  Element źródłowy  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 lub nowszy | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 lub nowszy | AzureRM.Compute |
| Wyłącz AzureRmVmssDiskEncryption   | 3.4.0 lub nowszy | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 lub nowszy | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 lub nowszy | AzureRM.Compute |
| Zestaw AzureRmVmssDiskEncryptionExtension   | 3.4.0 lub nowszy | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenariusze obsługiwane przez szyfrowanie dysków
* Szyfrowanie zestawu skali maszyny wirtualnej jest obsługiwana tylko dla zestawów skalowania utworzone za pomocą dysków zarządzanych i nieobsługiwane dla zestawów skalowania dysk lokalny (lub niezarządzanego).
* Szyfrowanie zestawu skali maszyny wirtualnej jest obsługiwana w woluminach systemu operacyjnego i danych dla VMSS systemu Windows. Wyłącz szyfrowanie jest obsługiwane w woluminach systemu operacyjnego i danych dla zestawu skalowania systemu Windows.
* Ponowne instalowanie obrazu maszyny Wirtualnej zestawu skalowania maszyn wirtualnych i operacje uaktualniania nie są obsługiwane w bieżącej wersji zapoznawczej.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Utwórz nowy klaster i włączenia szyfrowania dysku

Użyj następujących poleceń, aby utworzyć klaster i Włącz szyfrowanie dysków za pomocą szablonu usługi Azure Resource Manager & certyfikatu z podpisem własnym.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, który już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest uruchomienie z jednego z szablonów, które są dostępne na [przykłady szablonu sieci szkieletowej usług azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Wykonaj wskazówki i objaśnienia do [Dostosuj szablon klastra] [dostosować your klastra szablon] poniżej.

Jeśli już masz szablonu niestandardowego, a następnie upewnij się, że sprawdź następujące nazwy wszystkich trzech związane z certyfikatem parametrów szablonu i pliku parametrów i wartości mają wartość null w następujący sposób.

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```CLI

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Wdrażanie aplikacji do klastra usługi sieć szkieletowa usług systemu Windows
Wykonaj kroki i wskazówki dotyczące [wdrażanie aplikacji do klastra](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Włącz szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej klastra sieci szkieletowej usług utworzone powyżej
 
```Powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Sprawdź, czy wartość skali maszyny wirtualnej systemu Windows jest włączone szyfrowanie dysków.
Pobierz stan zestaw skali całej maszyny wirtualnej lub dowolnego wystąpienia w zestawie skalowania. Zobacz poniższe polecenia.
Ponadto użytkownik może zalogować się do maszyny Wirtualnej w zestawie skalowania i upewnij się, że dyski są szyfrowane.

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Wyłącz szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej klastra sieci szkieletowej usług 
Wyłącz szyfrowanie dysków ma zastosowanie do całej maszyny wirtualnej zestawu skali, a nie przez wystąpienia 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz bezpiecznego klaster z sposób włączyć lub wyłączyć szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej klastra sieci szkieletowej usług. Następnie [dysku szyfrowanie dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

