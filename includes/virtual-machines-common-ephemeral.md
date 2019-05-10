---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 47407df90a83501b8739a428789e20cddc59e83d
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468406"
---
Efemeryczne dyski systemu operacyjnego do tworzenia lokalnego magazynu maszyny wirtualnej (VM) i nie są zachowywane do zdalnej usługi Azure Storage. Efemeryczne dyski systemu operacyjnego działają dobrze w przypadku obciążeń bezstanowych, gdzie aplikacje są odporne na awariami pojedynczych maszyn wirtualnych, ale są bardziej zajmującym się ochroną czas, jaki zajmuje wdrożeń na dużą skalę lub czasu, aby odtworzyć poszczególnych wystąpień maszyn wirtualnych. Jest również odpowiedni w przypadku aplikacji wdrożonych za pomocą klasycznego modelu wdrażania, aby przejść do modelu wdrażania usługi Resource Manager. W przypadku dysków tymczasowych systemu operacyjnego będzie obserwować mniejsze opóźnienie odczytu/zapisu na dysku systemu operacyjnego i szybsze reimage maszyny Wirtualnej. Ponadto dysk systemu operacyjnego tymczasowych jest bezpłatny, pociągnąć za sobą żadnych kosztów magazynu dla dysku systemu operacyjnego. 
 
Najważniejsze funkcje efemeryczne dyski są: 
- Można ich używać z niestandardowych obrazów i obrazów z witryny Marketplace.
- Przy rozmiarze pamięci podręcznej maszyny Wirtualnej, mogą wdrażać obrazy maszyny Wirtualnej.
- Możliwość szybkiego resetowania lub odtworzyć swoje maszyny wirtualne do pierwotnego stanu rozruchu.  
- Mniejsze opóźnienie środowiska wykonawczego jest podobne do dysku tymczasowego. 
- Bezpłatnie dla dysku systemu operacyjnego. 
 
 
Podstawowe różnice między trwałe i efemeryczne dyski systemu operacyjnego:

|                             | Trwałe dysku systemu operacyjnego                          | Efemeryczny dysk systemu operacyjnego                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limit rozmiaru dysku systemu operacyjnego      | 2 TiB                                                                                        | Rozmiar dla rozmiaru maszyny Wirtualnej lub 2TiB, pamięci podręcznej obowiązuje mniejsza - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), i [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Obsługiwane rozmiary maszyn wirtualnych          | Wszyscy                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Obsługa typu dysku           | Zarządzane i niezarządzane dysku systemu operacyjnego                                                                | Zarządzanego dysku systemu operacyjnego                                                               |
| Obsługa regionu              | Wszystkie regiony                                                                                  | Wszystkie regiony                              |
| Trwałość danych            | System operacyjny dysku danych zapisanych na dysku systemu operacyjnego są przechowywane w usłudze Azure Storage                                  | Dane zapisane na dysku systemu operacyjnego są przechowywane w lokalnym magazynie maszyny Wirtualnej i nie jest trwały do usługi Azure Storage. |
| Zatrzymana z cofniętą alokacją stanu      | Zatrzymana z cofniętą alokacją i ponownego uruchomienia ze stanu zatrzymana z cofniętą alokacją maszyn wirtualnych i wystąpień zestawu skalowania | Maszyn wirtualnych i wystąpień zestawu skalowania nie może być zatrzymana z cofniętą alokacją                                  |
| Wyspecjalizowane obsługę dysku systemu operacyjnego | Tak                                                                                          | Nie                                                                                 |
| Zmiany rozmiaru dysku systemu operacyjnego              | Obsługiwana podczas tworzenia maszyny Wirtualnej i maszyna wirtualna jest zatrzymana z cofniętą alokacją                                | Obsługiwana podczas tworzenia maszyny Wirtualnej tylko                                                  |
| Zmiana rozmiaru do nowego rozmiaru maszyny Wirtualnej   | Dane z dysku systemu operacyjnego są zachowywane.                                                                    | Usunięcie danych na dysku systemu operacyjnego, system operacyjny jest ponownie zainicjowana                                      |

## <a name="register-for-the-preview"></a>Zarejestruj się w celu korzystania z wersji zapoznawczej


Zarejestrować się samodzielnie Efemeryczne dyski systemu operacyjnego przy użyciu najnowszej wersji interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell w wersji zapoznawczej.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature –FeatureName LocalDiffDiskPreview -ProviderNamespace Microsoft.Compute
```

Aby sprawdzić, jeśli są rejestrowane w wersji zapoznawczej:

```azurepowershell-interactive
Get-AzProviderFeature –FeatureName LocalDiffDiskPreview -ProviderNamespace Microsoft.Compute
```

### <a name="cli"></a>Interfejs wiersza polecenia

```azurecli-interactive
az provider register --namespace Microsoft.Compute
az feature register --namespace Microsoft.Compute --name LocalDiffDiskPreview
```

Aby sprawdzić, jeśli są rejestrowane w wersji zapoznawczej:
 
```azurecli-interactive
az provider show --namespace Microsoft.Compute
```


## <a name="scale-set-deployment"></a>Wdrożenie zestawu skalowania  
Proces tworzenia zestawu skalowania, który korzysta z efemerycznego dysku systemu operacyjnego jest dodanie `diffDiskSettings` właściwość `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` typ zasobu w szablonie. Ponadto zasad buforowania musi być równa `ReadOnly` dla efemerycznego dysku systemu operacyjnego. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-deployment"></a>Wdrażanie maszyny Wirtualnej 
Możesz wdrożyć Maszynę wirtualną z efemerycznego dysku systemu operacyjnego przy użyciu szablonu. Proces tworzenia maszyny Wirtualnej, która używa efemeryczne dyski systemu operacyjnego jest dodanie `diffDiskSettings` właściwości z typem zasobu Microsoft.Compute/virtualMachines w szablonie. Ponadto zasad buforowania musi być równa `ReadOnly` dla efemerycznego dysku systemu operacyjnego. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Odtwórz z obrazu maszyny Wirtualnej przy użyciu usługi REST
Obecnie jedyną metodą odtwarzanie z obrazu wystąpienie maszyny wirtualnej z efemerycznego dysku systemu operacyjnego jest za pomocą interfejsu API REST. Dla zestawów skalowania odtwarzanie z obrazu jest już dostępna za pośrednictwem programu Powershell, interfejsu wiersza polecenia i portalu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Jaki jest rozmiar lokalne dyski systemu operacyjnego?**

Odp.: W wersji zapoznawczej będą obsługiwane platformy i/lub obrazy o rozmiarze maszyny Wirtualnej pamięci podręcznej, gdzie będzie można lokalnie na tym samym węźle jako maszyna wirtualna wszystkich odczyt/zapis na dysku systemu operacyjnego. 

**Pyt.: Efemerycznego dysku systemu operacyjnego można zmienić rozmiar?**

Odp.: Nie, po zaaprowizowaniu efemerycznego dysku systemu operacyjnego, dysku systemu operacyjnego nie można zmienić rozmiaru. 

**Pyt.: Managed Disks można dołączyć do Efemeryczne maszyny Wirtualnej?**

Odp.: Tak, można dołączyć zarządzanego dysku danych do maszyny Wirtualnej, która korzysta z efemerycznego dysku systemu operacyjnego. 

**Pyt.: Wszystkie rozmiary maszyn wirtualnych będą obsługiwane dla efemeryczne dyski systemu operacyjnego?**

Odp.: Nie wszystkie rozmiary maszyny Wirtualnej usługi Premium Storage są obsługiwane (DS, ES, FS, GS i M) z wyjątkiem seria B, N-series i rozmiary serii H.  
 
**Pyt.: Efemerycznego dysku systemu operacyjnego można zastosować do istniejących maszyn wirtualnych i zestawów skalowania?**

Odp.: Nie, efemerycznego dysku systemu operacyjnego może być używana tylko podczas maszyny Wirtualnej i tworzenie zestawu skalowania. 

**Pyt.: Możesz mieszać zwykłego i efemeryczne dyski systemu operacyjnego w zestawie skalowania**

Odp.: Nie, nie mogą mieć różnych wystąpień tymczasowych i trwały system operacyjny dysku w ramach tego samego zestawu skalowania. 

**Pyt.: Efemerycznego dysku systemu operacyjnego można utworzyć przy użyciu programu Powershell lub interfejsu wiersza polecenia?**

Odp.: Tak, można utworzyć maszyny wirtualne z Efemerycznego dysku systemu operacyjnego przy użyciu REST, szablonów, programu PowerShell i interfejsu wiersza polecenia.

**Pyt.: Jakie funkcje nie są obsługiwane z efemerycznego dysku systemu operacyjnego?**

Odp.: Efemeryczne dyski nie obsługują:
- Przechwytywanie obrazów maszyn wirtualnych
- Migawki dysków 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Wymiana dysku systemu operacyjnego 
