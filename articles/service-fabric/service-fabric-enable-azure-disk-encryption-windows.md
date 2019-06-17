---
title: Włącz szyfrowanie dysków Azure Service Fabric Windows klastrów | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączania szyfrowania dysku dla węzłów klastra usługi Azure Service Fabric przy użyciu usługi Azure Key Vault w usłudze Azure Resource Manager.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471417"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Włącz szyfrowanie dysków dla węzłów klastra usługi Azure Service Fabric w Windows 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

W tym samouczku dowiesz się, jak włączyć szyfrowanie dysków w węzłach klastra usługi Service Fabric w Windows. Należy wykonać następujące kroki dla każdego z typów węzłów i zestawy skalowania maszyn wirtualnych. Szyfrowanie na węzłach, użyjemy funkcji usługi Azure Disk Encryption na zestawach skalowania maszyn wirtualnych.

Przewodnik obejmuje następujące tematy:

* Podstawowe pojęcia dotyczące pod uwagę podczas włączania szyfrowania dysków na skalowania maszyn wirtualnych z klastra usługi Service Fabric Ustawia w Windows.
* Kroki, aby wykonać przed włączeniem szyfrowania dysków w usłudze Service Fabric węzły klastra w Windows.
* Kroki, aby wykonać umożliwia włączenie szyfrowania dysku na węzłach klastra usługi Service Fabric w Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

**Autorejestracja** 

Podgląd szyfrowania dysku dla zestawu skalowania maszyn wirtualnych wymaga rejestracji automatycznej. Wykonaj następujące czynności: 

1. Po pierwsze uruchom następujące polecenie:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Odczekaj około 10 minut, aż stan odczytuje *zarejestrowanej*. Stan można sprawdzić, uruchamiając następujące polecenie: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Usługa Azure Key Vault** 

1. Tworzenie magazynu kluczy w tej samej subskrypcji i regionie co zestaw skalowania, a następnie wybierz **EnabledForDiskEncryption** dostęp do zasad w magazynie kluczy przy użyciu jego polecenia cmdlet programu PowerShell. Można także ustawić zasady przy użyciu interfejsu użytkownika z magazynu Key w witrynie Azure portal, za pomocą następującego polecenia:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), który udostępnia nowe polecenia szyfrowania.
3. Zainstaluj najnowszą wersję [zestawu Azure SDK za pomocą programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) wydania. Poniżej przedstawiono polecenia cmdlet usługi Azure Disk Encryption w celu włączenia zestawu skalowania maszyn wirtualnych ([ustaw](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) szyfrowania, pobrać ([uzyskać](/powershell/module/az.compute/get-azvmssvmdiskencryption)) stanu szyfrowania, a następnie usuń ([wyłączyć](/powershell/module/az.compute/disable-azvmssdiskencryption)) wystąpienie zestawu szyfrowania na skali.

| Polecenie | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 lub nowszym | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 lub nowszym | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 lub nowszym | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Obsługiwane scenariusze dotyczące szyfrowania dysku
* Szyfrowanie dla zestawów skalowania maszyn wirtualnych jest obsługiwana tylko w przypadku zestawów skalowania utworzonych za pomocą dysków zarządzanych. Nie jest obsługiwana dla zestawów skalowania dysku natywnego (lub niezarządzanego).
* Szyfrowanie jest obsługiwane dla systemu operacyjnego i ilości danych w skali maszyny wirtualnej zestawy w Windows. Wyłącz szyfrowanie jest również obsługiwana dla systemu operacyjnego i woluminów danych na potrzeby skalowania maszyn wirtualnych zestawy w Windows.
* Operacje odtworzenia z obrazu i uaktualnianie maszyny wirtualnej dla zestawów skalowania maszyn wirtualnych nie są obsługiwane w bieżącej wersji zapoznawczej.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Tworzenie nowego klastra i włączanie szyfrowania dysków

Użyj następujących poleceń do utworzenia klastra i włączyć szyfrowanie dysku przy użyciu szablonu usługi Azure Resource Manager i certyfikatu z podpisem własnym.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 
Zaloguj się przy użyciu następujących poleceń:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Użyj szablonu niestandardowego, która już istnieje 

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zdecydowanie zalecamy rozpoczęcie od jednego z szablonów, które są dostępne na [przykłady szablonów tworzenia klastra usługi Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) strony. Aby [dostosowywania szablonu klastra] [ customize-your-cluster-template] sekcji, zobacz poniższe wskazówki.

Jeśli masz już szablon niestandardowy, należy dokładnie sprawdzić następujące nazwy wszystkich trzech parametrów związane z certyfikatem w szablonu i pliku parametrów i że wartości są równe null w następujący sposób:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric na platformie Windows
Aby wdrożyć aplikację w klastrze, wykonaj kroki i wskazówek dostępnych pod adresem [Wdróż i usunąć aplikacje przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Sprawdź, czy ma włączone szyfrowanie dysku maszyny wirtualnej zestawu skalowania w Windows
Pobierz stan zestawu skalowania całej maszyny wirtualnej lub dowolnego wystąpienia w zestawie skalowania za pomocą poniższych poleceń.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Ponadto możesz zalogować się do zestawu skalowania maszyn wirtualnych i upewnij się, że dyski są szyfrowane.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Wyłącz szyfrowanie dysków dla maszyny wirtualnej zestawu skalowania w klastrze usługi Service Fabric 
Wyłącz szyfrowanie dysków dla maszyny wirtualnej zestawu skalowania, uruchamiając następujące polecenia. Należy pamiętać, że wyłączenie szyfrowania dysku ma zastosowanie do całej maszyny wirtualnej zestawu skalowania, a nie poszczególnych wystąpień.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Kolejne kroki
W tym momencie należy mieć zabezpieczonego klastra i wiedzieć, jak włączyć lub wyłączyć szyfrowanie dysków dla zestawów skalowania maszyn wirtualnych i węzłów klastra usługi Service Fabric. Podobne wskazówki w węzłach klastra usługi Service Fabric w systemie Linux, zobacz [Disk Encryption dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
