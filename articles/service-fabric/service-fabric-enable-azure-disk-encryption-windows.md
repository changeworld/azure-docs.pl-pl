---
title: Włącz szyfrowanie dysków klastrów usługi Windows Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć szyfrowanie dysków, węzłom klastra sieci szkieletowej usług na platformie Azure przy użyciu usługi Azure Resource Manager i usługi Azure Key Vault.
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
ms.openlocfilehash: 58f9481fa27f44de30fd80fe52e9b6d06d341b41
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750897"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Włącz szyfrowanie dysków, węzłom klastra usługi Windows Fabric 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla systemu Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Wykonaj następujące kroki, aby włączyć szyfrowanie dysków w węzłach klastra systemu Windows sieci szkieletowej usług Azure. Konieczne będzie wykonywane dla każdego z typów węzłów lub zestawy skalowania maszyny wirtualnej. Aby zaszyfrować węzły, użyjesz możliwość szyfrowania dysków Azure na zestawy skalowania maszyny wirtualnej.

Przewodnik obejmuje następujące procedury:

* Podstawowe pojęcia dotyczące włączania szyfrowania dysków na zestawy skalowania maszyn wirtualnych w klastrach usługi Windows Fabric.
* Wstępnie wymagane kroki, które należy wykonać przed włączeniem szyfrowania dysków na maszynie wirtualnej skalowanie zestawów dla klastrów usługi Windows Fabric.
* Kroki, aby włączyć lub wyłączyć dysku szyfrowania na zestawy skalowania maszyn wirtualnych w klastrach usługi Windows Fabric.


## <a name="prerequisites"></a>Wymagania wstępne
1. Samodzielnie zarejestrować swoją subskrypcję, wprowadzając następujące polecenie:

   ```PowerShell
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
   ```
   
   Poczekaj około 10 minut, aż stan jest `Registered`. Stan można sprawdzić, uruchamiając następujące polecenia:

   ```PowerShell
   Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
   ```

2. Tworzenie magazynu kluczy w tej samej subskrypcji i regionu jako zestaw skali. Ustawienie zasad dostępu `EnabledForDiskEncryption` w magazynie kluczy za pomocą jego polecenia cmdlet programu PowerShell. Można także ustawić zasady przy użyciu interfejsu użytkownika magazynu kluczy Azure w portalu Azure.

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
   ```

3. Zainstaluj [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), który zawiera najnowsze polecenia szyfrowania.

4. Zainstaluj najnowszą wersję pakietu [zestawu SDK platformy Azure z programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Użyj następujących poleceń cmdlet, aby włączyć ([ustawić](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) szyfrowania, pobrać ([uzyskać](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) stanu szyfrowania i Usuń ([wyłączyć](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) szyfrowania w skali ustawić wystąpienie:

| Polecenie | Wersja |  Element źródłowy  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 lub nowszy | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 lub nowszy | AzureRM.Compute |
| Wyłącz AzureRmVmssDiskEncryption   | 3.4.0 lub nowszy | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 lub nowszy | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 lub nowszy | AzureRM.Compute |
| Zestaw AzureRmVmssDiskEncryptionExtension   | 3.4.0 lub nowszy | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scenariusze obsługiwane przez szyfrowanie dysków
* Szyfrowanie zestawu skali maszyny wirtualnej jest obsługiwana tylko dla zestawów skalowania utworzone za pomocą dysków zarządzanych. Nie jest obsługiwana dla zestawów skalowania dysk lokalny (lub niezarządzanego).
* Szyfrowanie zestawu skali maszyny wirtualnej jest obsługiwana dla woluminów systemu operacyjnego i danych zestawy skalowania maszyny wirtualnej systemu Windows. Można włączyć lub wyłączyć szyfrowanie.
* Odtworzenia z obrazu maszyny Wirtualnej zestawu skalowania maszyn wirtualnych i operacje uaktualniania nie są obsługiwane w bieżącej wersji zapoznawczej.


## <a name="create-a-windows-cluster"></a>Tworzenie klastra z systemem Windows

Użyj następujących poleceń, aby utworzyć klaster i Włącz szyfrowanie dysków za pomocą szablonu usługi Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

```PowerShell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-a-custom-template"></a>Użycie szablonu niestandardowego 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zaleca się uruchamiania jednego z [przykłady szablonu usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master).

Jeśli masz już szablon niestandardowy, upewnij się, że następujące nazwy wszystkich trzech związane z certyfikatem parametrów szablonu i pliku parametrów. Ponadto upewnij się, czy wartości są następujące wartości null.

```JSON
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


```PowerShell


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

### <a name="deploy-an-application-to-the-windows-service-fabric-cluster"></a>Wdrażanie aplikacji do klastra usługi sieć szkieletowa usług systemu Windows
Wykonaj kroki i wskazówki dotyczące [wdrażania aplikacji do klastra](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications).


## <a name="enable-disk-encryption-for-a-virtual-machine-scale-set"></a>Włącz szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej
Włącz szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej, z wcześniej utworzonym dla klastra usługi Windows Fabric.
 
```PowerShell

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


## <a name="validate-that-disk-encryption-is-enabled-for-a-virtual-machine-scale-set"></a>Sprawdź poprawność tego dysku szyfrowanie jest włączone dla zestawu skalowania maszyny wirtualnej
Użyj następujących poleceń, aby pobrać stan zestaw skali całej maszyny wirtualnej lub dowolnego wystąpienia maszyny Wirtualnej w zestawie skalowania. Możesz także zalogować się do maszyny Wirtualnej w zestawie skalowania i upewnij się, że dyski są szyfrowane.

```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


## <a name="disable-disk-encryption-for-a-virtual-machine-scale-set"></a>Wyłącz szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej 
Jeśli trzeba wyłączyć szyfrowanie dysku dla klastra usługi Windows Fabric skali maszyny wirtualnej, użyj następujących poleceń. Wyłączenie szyfrowania dysku ma zastosowanie do całej maszyny wirtualnej zestawu skali, a nie przez wystąpienie. 


```PowerShell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Kolejne kroki
W tym momencie bezpiecznego klaster, i wiedzieć, jak włączyć lub wyłączyć szyfrowanie dysków klastra usługi Windows Fabric. Następnie Dowiedz się więcej o [szyfrowania dysku dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md). 

