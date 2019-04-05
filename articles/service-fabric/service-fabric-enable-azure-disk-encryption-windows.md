---
title: Włącz szyfrowanie dysków Azure Service Fabric Windows klastrów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania szyfrowania dysku dla węzłów klastra usługi Service Fabric na platformie Azure przy użyciu usługi Azure Resource Manager, usługi Azure Key Vault.
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
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050446"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Włącz szyfrowanie dysków dla węzłów klastra Windows fabric service 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Wykonaj poniższe kroki, aby włączyć szyfrowanie dysku w węzłach klastra Windows Fabric Service. Konieczne będzie wykonywane dla każdego węzła typy/wirtualnych zestawów skalowania. Szyfrowanie na węzłach, będziemy korzystać możliwości usługi Azure Disk Encryption na zestawach skalowania maszyn wirtualnych.

Przewodnik obejmuje następujące procedury:

* Ustaw kluczowe założenia, które należy znać wyłączanie umożliwia włączenie szyfrowania dysku na skalowania maszyn wirtualnych klastra Windows usługi Service Fabric.
* Wymagania wstępne instrukcje występować przed włączeniem szyfrowania dysków na zestaw skalowania maszyn wirtualnych klastra Windows usługi Service Fabric.
* Ustaw kroki, aby wykonać umożliwia włączenie szyfrowania dysku na skalowania maszyn wirtualnych klastra Windows usługi Service Fabric.

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
* **Usługa Azure Key Vault** — Utwórz magazyn kluczy w tej samej subskrypcji i regionu, ponieważ skali ustawiony, a także ustawić zasady dostępu "EnabledForDiskEncryption" w magazynie kluczy, za pomocą polecenia cmdlet jego PS. Można też ustawić zasady za pomocą usługi KeyVault interfejsie użytkownika w witrynie Azure portal: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Zainstaluj najnowsze [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , który udostępnia nowe polecenia szyfrowania.
* Zainstaluj najnowszą wersję [zestawu Azure SDK za pomocą programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) wydania. ADE poleceń cmdlet, aby włączyć zestaw skalowania maszyn wirtualnych są następujące ([ustaw](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) szyfrowania, pobrać ([uzyskać](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stanu szyfrowania i Usuń ([wyłączyć](/powershell/module/az.compute/disable-azvmssdiskencryption)) szyfrowanie w zestawie skalowania wystąpienie.

| Polecenie | Wersja |  Element źródłowy  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 lub nowszym | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Obsługiwane scenariusze dotyczące szyfrowania dysku
* Szyfrowanie zestaw skali maszyny wirtualnej jest obsługiwana tylko w przypadku zestawów skalowania utworzonych za pomocą dysków zarządzanych, a nie jest obsługiwane dla zestawów skalowania dysku natywnego (lub niezarządzanego).
* Szyfrowanie zestaw skali maszyny wirtualnej jest obsługiwana dla woluminów systemu operacyjnego i danych dla zestawu skalowania maszyn wirtualnych Windows. Wyłącz szyfrowanie jest obsługiwane w przypadku woluminów systemu operacyjnego i danych dla zestawu skalowania Windows.
* Odtworzenia z obrazu maszyny Wirtualnej zestawu skalowania maszyn wirtualnych i uaktualniania przeprowadzanych nie są obsługiwane w bieżącej wersji zapoznawczej.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Tworzenie nowego klastra i włączanie szyfrowania dysków

Użyj następujących poleceń, aby utworzyć klaster i włączyć szyfrowanie dysku przy użyciu szablonu usługi Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, która już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów usługi Usługa azure service fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Postępuj zgodnie z wskazówki i objaśnienia na [dostosowywania szablonu klastra] [ customize-your-cluster-template] poniższej sekcji.

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze Windows Service Fabric
Wykonaj kroki i wskazówki dotyczące [wdrożyć aplikację w klastrze](service-fabric-deploy-remove-applications.md)


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Włącz szyfrowanie dysków dla utworzonego powyżej zestawu skalowania maszyn wirtualnych klastra usługi Service Fabric
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Sprawdź, czy zestawu skalowania maszyn wirtualnych Windows jest włączone szyfrowanie dysków.
Pobierz stan zestawu skalowania całej maszyny wirtualnej lub dowolnego wystąpienia w zestawie skalowania. Zobacz poniższe polecenia.
Ponadto użytkownik może zalogować się do maszyny Wirtualnej w zestawie skalowania i upewnij się, że dyski są szyfrowane.

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

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Kolejne kroki
W tym momencie masz bezpiecznego klastra, jak włączyć/wyłączyć szyfrowanie dysków dla zestawu skalowania maszyn wirtualnych klastra usługi Service Fabric. Następnie [dysku szyfrowanie dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md) 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
