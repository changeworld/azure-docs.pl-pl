---
title: Włączanie szyfrowania dysku dla klastrów systemu Windows
description: W tym artykule opisano sposób włączania szyfrowania dysku dla węzłów klastra sieci szkieletowej usług Azure przy użyciu usługi Azure Key Vault w usłudze Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251815"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Włączanie szyfrowania dysku dla węzłów klastra sieci szkieletowej usługi Azure w systemie Windows 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysku dla systemu Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Szyfrowanie dysku dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

W tym samouczku dowiesz się, jak włączyć szyfrowanie dysków w węzłach klastra sieci szkieletowej usług w systemie Windows. Należy wykonać następujące kroki dla każdego z typów węzłów i zestawów skalowania maszyny wirtualnej. Do szyfrowania węzłów użyjemy funkcji szyfrowania dysków platformy Azure w zestawach skalowania maszyny wirtualnej.

Przewodnik obejmuje następujące tematy:

* Kluczowe pojęcia, o które należy pamiętać podczas włączania szyfrowania dysku w zestawach skalowania maszyny wirtualnej klastra usług w systemie Windows.
* Kroki, których należy wykonać przed włączeniem szyfrowania dysku w węzłach klastra sieci szkieletowej usług w systemie Windows.
* Kroki, które należy wykonać, aby włączyć szyfrowanie dysku w węzłach klastra sieci szkieletowej usług w systemie Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

**Rejestracja własna** 

Podgląd szyfrowania dysku dla zestawu skalowania maszyny wirtualnej wymaga samodzielnej rejestracji. Wykonaj następujące czynności: 

1. Najpierw uruchom następujące polecenie:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Poczekaj około 10 minut, aż stan odczyta *Zarejestrowany*. Stan można sprawdzić, uruchamiając następujące polecenie: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Utwórz magazyn kluczy w tej samej subskrypcji i regionie co zestaw skalowania, a następnie wybierz zasadę dostępu **EnabledForDiskEncryption** w magazynie kluczy przy użyciu polecenia cmdlet programu PowerShell. Zasady można również ustawić przy użyciu interfejsu użytkownika usługi Key Vault w witrynie Azure portal za pomocą następującego polecenia:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), która ma nowe polecenia szyfrowania.
3. Zainstaluj najnowszą wersję pakietu [Azure SDK z wersji programu Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) Poniżej przedstawiono zestaw poleceń cmdlet szyfrowania partycji Azure Disk Encryption, aby włączyć[(zestaw](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) szyfrowania, pobrać[(pobrać](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stan szyfrowania i usunąć[(wyłączyć)](/powershell/module/az.compute/disable-azvmssdiskencryption)szyfrowanie w wystąpieniu zestawu skalowania.

| Polecenie | Wersja |  Element źródłowy  |
| ------------- |-------------| ------------|
| Status Get-AzVmssDiskEncryptionStatus   | 1.0.0 lub nowsze | Az.Compute |
| Status Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 lub nowsze | Az.Compute |
| Wyłącz-AzVmssDiskEncryption   | 1.0.0 lub nowsze | Az.Compute |
| Get-AzVmssDiskSzyktałtowanie   | 1.0.0 lub nowsze | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 lub nowsze | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 lub nowsze | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Obsługiwane scenariusze szyfrowania dysku
* Szyfrowanie zestawów skalowania maszyny wirtualnej jest obsługiwane tylko w przypadku zestawów skalowania utworzonych za pomocą dysków zarządzanych. Nie jest obsługiwany dla natywnych (lub niezarządzanych) zestawów skalowania dysku.
* Szyfrowanie jest obsługiwane dla systemu operacyjnego i woluminów danych w zestawach skalowania maszyny wirtualnej w systemie Windows. Wyłącz szyfrowanie jest również obsługiwane dla systemu operacyjnego i woluminów danych dla zestawów skalowania maszyny wirtualnej w systemie Windows.
* Operacje ponownego zamłania i uaktualniania maszyny wirtualnej dla zestawów skalowania maszyny wirtualnej nie są obsługiwane w bieżącej wersji zapoznawczej.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Tworzenie nowego klastra i włączanie szyfrowania dysku

Poniższe polecenia umożliwiają utworzenie klastra i włączenie szyfrowania dysku przy użyciu szablonu usługi Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się za pomocą następujących poleceń:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, który już masz 

Jeśli chcesz zasądnąć szablon niestandardowy zgodnie z potrzebami, zdecydowanie zalecamy rozpoczęcie od jednego z szablonów dostępnych na stronie [przykłady szablonów tworzenia klastra usługi Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Aby [dostosować sekcję szablonu klastra,][customize-your-cluster-template] zobacz następujące wskazówki.

Jeśli masz już szablon niestandardowy, sprawdź, czy wszystkie trzy parametry związane z certyfikatem w szablonie i pliku parametrów są nazwane w następujący sposób i czy wartości są zerowe w następujący sposób:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Wdrażanie aplikacji w klastrze sieci szkieletowej usług w systemie Windows
Aby wdrożyć aplikację w klastrze, postępuj zgodnie z instrukcjami i wskazówkami dotyczącymi [wdrażania i usuwania aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Włącz szyfrowanie dysku dla utworzonych wcześniej zestawów skalowania maszyny wirtualnej

Aby włączyć szyfrowanie dysku dla zestawów skalowania maszyny wirtualnej utworzonych w poprzednich krokach, uruchom następujące polecenia:
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Sprawdzanie poprawności, czy szyfrowanie dysku jest włączone dla skalowania maszyny wirtualnej ustawionej w systemie Windows
Uzyskaj stan całego zestawu skalowania maszyny wirtualnej lub dowolnego wystąpienia w zestawie skalowania, uruchamiając następujące polecenia.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Ponadto można zalogować się do zestawu skalowania maszyny wirtualnej i upewnij się, że dyski są szyfrowane.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Wyłączanie szyfrowania dysku dla skalowania maszyny wirtualnej ustawionej w klastrze sieci szkieletowej usług 
Wyłącz szyfrowanie dysku dla skalowania maszyny wirtualnej ustawionej, uruchamiając następujące polecenia. Należy zauważyć, że wyłączenie szyfrowania dysku dotyczy całego zestawu skalowania maszyny wirtualnej, a nie pojedynczego wystąpienia.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Następne kroki
W tym momencie powinieneś mieć bezpieczny klaster i wiedzieć, jak włączyć i wyłączyć szyfrowanie dysków dla węzłów klastra sieci szkieletowej usług i zestawów skalowania maszyn wirtualnych. Aby uzyskać podobne wskazówki dotyczące węzłów klastra sieci szkieletowej usług w systemie Linux, zobacz [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
