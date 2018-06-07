---
title: Włącz szyfrowanie dysków dla usługi sieci szkieletowej Linux klastrów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć szyfrowanie dysków ustawić na platformie Azure przy użyciu usługi Azure Resource Manager, usługi Azure Key Vault skali klastra sieci szkieletowej usług.
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
ms.date: 05/24/2018
ms.author: v-viban
ms.openlocfilehash: 46f7f88768ab7ae9d84f392f340750865fef3b96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655750"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Włącz szyfrowanie dysków dla węzłów klastra Linux sieci szkieletowej usług 
> [!div class="op_single_selector"]
> * [Szyfrowanie dysków dla systemu Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Szyfrowanie dysków dla systemu Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Wykonaj poniższe kroki, aby włączyć szyfrowanie dysków w węzłach klastra Linux sieci szkieletowej usług. Konieczne będzie wykonywane dla każdego zestawy skalowania maszyn wirtualnych/typy węzła. Do szyfrowania w węzłach, firma Microsoft będzie korzystać z możliwości szyfrowania dysków Azure na zestawy skalowania maszyny wirtualnej.

Przewodnik obejmuje następujące procedury:

* Ustaw kluczowe założenia, które należy znać wyłączanie umożliwia włączenie szyfrowania dysków na skali maszyny wirtualnej klastra Linux sieci szkieletowej usług.
* Wymagania wstępne kroki, aby występować przed włączeniem szyfrowania dysków na zestaw skali maszyny wirtualnej klastra Linux sieci szkieletowej usług.
* Ustaw kroki można wykonać, aby włączyć szyfrowanie dysków na skali maszyny wirtualnej klastra Linux sieci szkieletowej usług.


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
4. **Usługa Azure Key Vault** — tworzenie KeyVault w tej samej subskrypcji i regionu, ponieważ ustawiony skali maszyny wirtualnej, a ustawienie zasad dostępu "EnabledForDiskEncryption" na KeyVault przy użyciu polecenia cmdlet jego PS. Można też ustawić zasady przy użyciu interfejsu użytkownika KeyVault w portalu Azure: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Zainstaluj najnowsze [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) , który zawiera nowe polecenia szyfrowania.
6. Zainstaluj najnowszą wersję pakietu [zestawu SDK platformy Azure z programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases) wersji. Poniżej przedstawiono polecenia cmdlet VMSS ADE, aby włączyć ([ustawić](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) szyfrowania, pobrać ([uzyskać](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) stanu szyfrowania i Usuń ([wyłączyć](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) szyfrowania na skali Ustaw wystąpienia. 

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
* Szyfrowanie zestawu skali maszyny wirtualnej jest obsługiwana dla woluminów danych dla zestawu skalowania maszyny wirtualnej systemu Linux. Szyfrowanie dysku systemu operacyjnego nie jest obsługiwane w bieżącej wersji zapoznawczej dla systemu Linux.
* Ponowne instalowanie obrazu maszyny Wirtualnej zestawu skalowania maszyn wirtualnych i operacje uaktualniania nie są obsługiwane w bieżącej wersji zapoznawczej.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Utwórz nowy klaster systemu Linux i włączenia szyfrowania dysku

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

Jeśli musisz utworzyć szablon niestandardowy zgodnie z potrzebami, zalecane jest uruchomienie z jednego z szablonów, które są dostępne na [przykłady szablonu sieci szkieletowej usług azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) dla klastra z systemem Linux. 

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

Od czasu dla zestawu skalowania maszyny wirtualnej systemu Linux — tylko dane dysku szyfrowanie jest obsługiwane, więc musimy dodać dysk danych przy użyciu szablonu usługi Azure Resource Manager. Aktualizacja szablonu dla dostarczania dysku danych, jak pokazano poniżej:

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

Oto analogiczne polecenie interfejsu wiersza polecenia, aby zrobić to samo. Zmień wartości w instrukcji declare odpowiednie wartości. Interfejs wiersza polecenia obsługuje wszystkie parametry, które obsługuje powyższe polecenie programu powershell.

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

#### <a name="linux-data-disk-mounting"></a>Instalowanie dysk danych systemu Linux
Aby można było kontynuować z szyfrowaniem zestaw skali maszyny wirtualnej systemu Linux, musimy upewnij się, że dysk dodanych danych jest poprawnie zainstalowany, lub nie. Zaloguj się do maszyny Wirtualnej systemu Linux klastra i uruchom polecenie LSBLK. Dane wyjściowe powinny wskazywać tego dysku dodanych danych w kolumnie punktu instalacji.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Wdrażanie aplikacji do klastra usługi sieć szkieletowa usług systemu Linux
Wykonaj kroki i wskazówki dotyczące [wdrażanie aplikacji do klastra](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-quickstart-containers-linux)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Włącz szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej klastra Linux sieci szkieletowej usług utworzone powyżej
 
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Sprawdź, czy wartość skali maszyny wirtualnej systemu Linux jest włączone szyfrowanie dysków.
Pobierz stan zestaw skali całej maszyny wirtualnej lub dowolnego wystąpienia zestawu skali maszyny Wirtualnej. Zobacz poniższe polecenia.
Ponadto użytkownik może zalogować się do maszyny Wirtualnej systemu Linux klastra i uruchom polecenie LSBLK. Dane wyjściowe powinny wskazywać, ten dysk dodanych danych na kolumny punktu instalacji i typ jako Crypt dla dysku dodanych danych.

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
W tym momencie masz bezpiecznego klaster z sposób włączyć lub wyłączyć szyfrowanie dysków dla zestawu skalowania maszyny wirtualnej klastra sieci szkieletowej usług dla systemu Linux. Następnie [dysku szyfrowanie dla systemu Windows](service-fabric-enable-azure-disk-encryption-windows.md) 

