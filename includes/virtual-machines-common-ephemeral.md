---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155503"
---
Dyski efemeryczny system operacyjny są tworzone w magazynie lokalnej maszyny wirtualnej (VM) i nie są zapisywane w zdalnym usłudze Azure Storage. Efemeryczne dyski systemu operacyjnego działają dobrze dla obciążeń bezstanowych, gdzie aplikacje są tolerancyjne dla poszczególnych awarii maszyn wirtualnych, ale są bardziej dotknięte czasem wdrażania maszyny Wirtualnej lub ponownego zagłuszania poszczególnych wystąpień maszyn wirtualnych. Dzięki dyskowi Ephemeral OS otrzymujesz mniejsze opóźnienie odczytu/zapisu na dysku systemu operacyjnego i szybsze odtwarzanie maszyn wirtualnych. 
 
Najważniejsze cechy dysków efemerycznych to: 
- Idealny do zastosowań bezstanowych.
- Mogą być używane zarówno z marketplace, jak i z obrazami niestandardowymi.
- Możliwość szybkiego resetowania lub ponownego zamłania maszyn wirtualnych i skalowania ustawianie wystąpień do pierwotnego stanu rozruchu.  
- Mniejsze opóźnienie, podobne do dysku tymczasowego. 
- Efemeryczne dyski systemu operacyjnego są bezpłatne, nie ponosisz żadnych kosztów magazynowania dysku systemu operacyjnego.
- Są one dostępne we wszystkich regionach platformy Azure. 
- Efemeryczny dysk systemu operacyjnego jest obsługiwany przez [Galerię obrazów udostępnionych](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Kluczowe różnice między dyskami systemu operacyjnego trwałe i efemeryczne:

|                             | Dysk systemu operacyjnego trwałego                          | Efemeryczny dysk systemu operacyjnego                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limit rozmiaru dysku systemu operacyjnego      | 2 TiB                                                                                        | Rozmiar pamięci podręcznej dla rozmiaru maszyny Wirtualnej lub 2TiB, w zależności od tego, który jest mniejszy. Aby uzyskać **rozmiar pamięci podręcznej w GiB**, zobacz [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)i [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Obsługiwane rozmiary maszyn wirtualnych          | Wszystkie                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Obsługa typów dysków           | Zarządzany i niezarządzany dysk systemu operacyjnego                                                                | Tylko dysk zarządzanego systemu operacyjnego                                                               |
| Wsparcie regionu              | Wszystkie regiony                                                                                  | Wszystkie regiony                              |
| Trwałość danych            | Dane dysku systemu operacyjnego zapisane na dysku systemu operacyjnego są przechowywane w usłudze Azure Storage                                  | Dane zapisane na dysku systemu operacyjnego są przechowywane w lokalnym magazynie maszyn wirtualnych i nie są utrwalone w usłudze Azure Storage. |
| Stan zatrzymania      | Maszyny wirtualne i wystąpienia zestawu skalowania mogą być zatrzymane i ponownie uruchomione ze stanu stop-deallocated | Nie można zatrzymać alokacji maszyn wirtualnych i scenowanych                                  |
| Specjalistyczna obsługa dysków systemu operacyjnego | Tak                                                                                          | Nie                                                                                 |
| Rozmiar dysku systemu operacyjnego              | Obsługiwane podczas tworzenia maszyny Wirtualnej i po zatrzymaniu alokacji maszyny Wirtualnej                                | Obsługiwane tylko podczas tworzenia maszyny wirtualnej                                                  |
| Zmiana rozmiaru na nowy rozmiar maszyny Wirtualnej   | Dane dysku systemu operacyjnego są zachowywane                                                                    | Dane na dysku systemu operacyjnego są usuwane, system operacyjny jest ponownie aprowizowana                                      |

## <a name="size-requirements"></a>Wymagania dotyczące rozmiaru

Można wdrożyć obrazy maszyn wirtualnych i wystąpień do rozmiaru pamięci podręcznej maszyny Wirtualnej. Na przykład standardowe obrazy systemu Windows Server z marketplace są około 127 GiB, co oznacza, że potrzebujesz rozmiaru maszyny Wirtualnej, który ma pamięć podręczną większą niż 127 GiB. W takim przypadku [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) ma rozmiar pamięci podręcznej 86 GiB, który nie jest wystarczająco duży. Standard_DS3_v2 ma rozmiar pamięci podręcznej 172 GiB, który jest wystarczająco duży. W takim przypadku Standard_DS3_v2 jest najmniejszym rozmiarem z serii DSv2, którego można użyć z tym obrazem. Podstawowe obrazy systemu Linux w obrazach marketplace i `[smallsize]` windows server, które są oznaczone przez wydają się być około 30 GiB i można użyć większości dostępnych rozmiarów maszyn wirtualnych.

Dyski efemeryczne wymagają również, aby rozmiar maszyny Wirtualnej obsługuje magazyn w wersji Premium. Rozmiary zwykle (ale nie zawsze) mają `s` nazwę, taką jak DSv2 i EsV3. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych platformy Azure, aby](../articles/virtual-machines/linux/sizes.md) uzyskać szczegółowe informacje o rozmiarach, które obsługują magazyn w usłudze Premium.

## <a name="powershell"></a>PowerShell

Aby użyć dysku efemerycznego do wdrożenia maszyny Wirtualnej programu PowerShell, użyj [funkcji Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) w konfiguracji maszyny Wirtualnej. Ustaw `-DiffDiskSetting` do `Local` `-Caching` i `ReadOnly`na .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

W przypadku wdrożeń zestawu skalowania należy użyć polecenia cmdlet [Set-AzVmssStorageProfile w](/powershell/module/az.compute/set-azvmssstorageprofile) konfiguracji. Ustaw `-DiffDiskSetting` do `Local` `-Caching` i `ReadOnly`na .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Interfejs wiersza polecenia

Aby użyć dysku efemerycznego do wdrożenia maszyny `--ephemeral-os-disk` wirtualnej cli, ustaw `true` parametr `--os-disk-caching` w `ReadOnly` [az vm create](/cli/azure/vm#az-vm-create) to i parametr na .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

W przypadku zestawów skalowania `--ephemeral-os-disk true` należy użyć tego samego parametru dla [az-vmss-create](/cli/azure/vmss#az-vmss-create) i ustawić parametr na `--os-disk-caching` `ReadOnly`.

## <a name="portal"></a>Portal   

W witrynie Azure portal można użyć dysków tymczasowych podczas wdrażania maszyny Wirtualnej, otwierając sekcję **Zaawansowane** na karcie **Dyski.** Dla **korzystania z efemerycznego dysku systemu operacyjnego** wybierz **Tak**.

![Zrzut ekranu przedstawiający przycisk opcji umożliwiający użycie efemerycznego dysku systemu operacyjnego](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Jeśli opcja korzystania z dysku efemerycznego jest wyszarzona, być może wybrano rozmiar maszyny Wirtualnej, który nie ma rozmiaru pamięci podręcznej większy niż obraz systemu operacyjnego lub który nie obsługuje magazynu w warstwie Premium. Wróć do strony **Podstawy** i spróbuj wybrać inny rozmiar maszyny Wirtualnej.

Można również tworzyć zestawy skalowania z efemerycznymi dyskami systemu operacyjnego za pomocą portalu. Wystarczy wybrać rozmiar maszyny Wirtualnej o wystarczająco dużym rozmiarze pamięci podręcznej, a następnie w **obszarze Użyj efemerycznego dysku systemu operacyjnego** wybierz **tak**.

![Zrzut ekranu przedstawiający przycisk opcji umożliwiający użycie efemerycznego dysku systemu operacyjnego dla zestawu skalowania](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Wdrażanie szablonu zestawu skalowania  
Proces tworzenia zestawu skalowania, który używa efemerycznego dysku systemu `diffDiskSettings` operacyjnego, polega na dodaniu właściwości do typu zasobu `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` w szablonie. Ponadto zasady buforowania muszą być `ReadOnly` ustawione na efemeryczny dysk systemu operacyjnego. 


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

## <a name="vm-template-deployment"></a>Wdrażanie szablonu maszyny Wirtualnej 
Maszynę Wirtualną można wdrożyć z efemerycznym dyskiem systemu operacyjnego przy użyciu szablonu. Proces tworzenia maszyny Wirtualnej, która używa dysków efemeryczny `diffDiskSettings` system operacyjny jest dodanie właściwości do microsoft.Compute/virtualMachines typu zasobu w szablonie. Ponadto zasady buforowania muszą być `ReadOnly` ustawione na efemeryczny dysk systemu operacyjnego. 

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


## <a name="reimage-a-vm-using-rest"></a>Ponowne ujednerzenie maszyny Wirtualnej przy użyciu rest
Wystąpienie maszyny wirtualnej można ponownie zować z efemerycznym dyskiem systemu operacyjnego przy użyciu interfejsu API REST, jak opisano poniżej i za pośrednictwem usługi Azure Portal, przechodząc do okienka przeglądowa maszyny wirtualnej. W przypadku zestawów skalowania ponowne zagosnanie jest już dostępne za pośrednictwem programu Powershell, interfejsu wiersza polecenia i portalu.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Jaki jest rozmiar dysków lokalnego systemu operacyjnego?**

Odp.: Obsługujemy obrazy platformy i niestandardowe, aż do rozmiaru pamięci podręcznej maszyny Wirtualnej, gdzie wszystkie odczyt/zapisy na dysku systemu operacyjnego będą lokalne w tym samym węźle co maszyna wirtualna. 

**Pyt.: Czy można zwymiarować tymczasowy dysk systemu operacyjnego?**

Odp.: Nie, po zainicjowaniu obsługi administracyjnej dysku systemu operacyjnego nie można zwymiarować dysku systemu operacyjnego. 

**Pyt.: Czy można dołączyć dyski zarządzane do maszyny Wirtualnej Efemerycznej?**

Odp.: Tak, dysk zarządzanych danych można dołączyć do maszyny Wirtualnej, która używa efemerycznego dysku systemu operacyjnego. 

**Pyt.: Czy wszystkie rozmiary maszyn wirtualnych będą obsługiwane dla tymczasowych dysków systemu operacyjnego?**

Odp.: Nie, obsługiwane są wszystkie rozmiary maszyn wirtualnych magazynu w wersji premium (DS, ES, FS, GS i M) z wyjątkiem rozmiarów serii B, serii N i serii H.  
 
**Pyt.: Czy efemeryczny dysk systemu operacyjnego można zastosować do istniejących maszyn wirtualnych i zestawów skalowania?**

Odp.: Nie, efemeryczny dysk systemu operacyjnego może być używany tylko podczas tworzenia maszyny Wirtualnej i zestawu skalowania. 

**Pyt.: Czy można mieszać efemeryczne i normalne dyski systemu operacyjnego w zestawie skalowania?**

Odp.: Nie, nie można mieć kombinacji ulotnych i trwałych wystąpień dysku systemu operacyjnego w tym samym zestawie skalowania. 

**Pyt.: Czy można utworzyć tymczasowy dysk systemu operacyjnego przy użyciu programu Powershell lub CLI?**

Odp.: Tak, maszyny wirtualne można tworzyć za pomocą efemerycznego dysku systemu operacyjnego przy użyciu rest, szablonów, programu PowerShell i interfejsu wiersza polecenia.

**Pyt.: Jakie funkcje nie są obsługiwane na efemerycznym dysku systemu operacyjnego?**

Odp.: Dyski efemeryczne nie obsługują:
- Przechwytywanie obrazów maszyn wirtualnych
- Migawki dysków 
- Usługa Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Zamiana dysku systemu operacyjnego 
