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
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805860"
---
Efemeryczne dyski systemu operacyjnego są tworzone w pamięci lokalnej maszyny wirtualnej (VM) i nie są zapisywane do zdalnej usługi Azure Storage. Efemeryczne dyski systemu operacyjnego działają dobrze w przypadku obciążeń bezstanowych, gdy aplikacje są odporne na awariami pojedynczych maszyn wirtualnych, ale są one bardziej wpływ czas wdrażania maszyny Wirtualnej lub odtwarzanie z obrazu poszczególnych wystąpień maszyn wirtualnych. Umożliwia skorzystanie z dysku systemu operacyjnego tymczasowych, mniejsze opóźnienia odczytu/zapisu na dysku systemu operacyjnego i szybsze reimage maszyny Wirtualnej. 
 
Najważniejsze funkcje efemeryczne dyski są: 
- Idealne rozwiązanie dla aplikacji bezstanowych.
- Używać zarówno w portalu Marketplace, jak i obrazów niestandardowych.
- Możliwość szybkiego resetowania lub odtworzenia z obrazu maszyny wirtualne i skalowania Ustaw wystąpień do pierwotnego stanu rozruchu.  
- Mniejsze opóźnienia, podobnie jak dysk tymczasowy. 
- Efemeryczne dyski systemu operacyjnego są bezpłatne, pociągnąć za sobą żadnych kosztów magazynu dla dysku systemu operacyjnego.
- Są one dostępne we wszystkich regionach platformy Azure. 
- Efemerycznego dysku systemu operacyjnego jest obsługiwana przez [galerii obrazów współdzielona](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Podstawowe różnice między trwałe i efemeryczne dyski systemu operacyjnego:

|                             | Trwałe dysku systemu operacyjnego                          | Efemeryczny dysk systemu operacyjnego                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limit rozmiaru dysku systemu operacyjnego      | 2 TiB                                                                                        | Rozmiar dla rozmiaru maszyny Wirtualnej lub 2TiB, pamięci podręcznej obowiązuje mniejsza wartość. Aby uzyskać **rozmiar w GiB pamięci podręcznej**, zobacz [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), i [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Obsługiwane rozmiary maszyn wirtualnych          | Wszyscy                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Obsługa typu dysku           | Zarządzane i niezarządzane dysku systemu operacyjnego                                                                | Zarządzanego dysku systemu operacyjnego                                                               |
| Obsługa regionu              | Wszystkie regiony                                                                                  | Wszystkie regiony                              |
| Trwałość danych            | System operacyjny dysku danych zapisanych na dysku systemu operacyjnego są przechowywane w usłudze Azure Storage                                  | Dane zapisane na dysku systemu operacyjnego są przechowywane w lokalnym magazynie maszyny Wirtualnej i nie jest trwały do usługi Azure Storage. |
| Zatrzymana z cofniętą alokacją stanu      | Zatrzymana z cofniętą alokacją i ponownego uruchomienia ze stanu zatrzymana z cofniętą alokacją maszyn wirtualnych i wystąpień zestawu skalowania | Maszyn wirtualnych i wystąpień zestawu skalowania nie może być zatrzymana z cofniętą alokacją                                  |
| Wyspecjalizowane obsługę dysku systemu operacyjnego | Tak                                                                                          | Nie                                                                                 |
| Zmiany rozmiaru dysku systemu operacyjnego              | Obsługiwana podczas tworzenia maszyny Wirtualnej i maszyna wirtualna jest zatrzymana z cofniętą alokacją                                | Obsługiwana podczas tworzenia maszyny Wirtualnej tylko                                                  |
| Zmiana rozmiaru do nowego rozmiaru maszyny Wirtualnej   | Dane z dysku systemu operacyjnego są zachowywane.                                                                    | Usunięcie danych na dysku systemu operacyjnego, system operacyjny jest ponownie zainicjowana                                      |

## <a name="size-requirements"></a>Wymagań dotyczących rozmiaru

Można wdrażać obrazy maszyn wirtualnych i wystąpień rozmiarze pamięci podręcznej maszyny Wirtualnej. Na przykład obrazy standardowych systemu Windows Server w witrynie marketplace są GiB około 127, co oznacza, że potrzebujesz rozmiaru maszyny Wirtualnej, która ma większy niż 127 GiB pamięci podręcznej. W tym przypadku [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) ma rozmiar pamięci podręcznej 86 GiB, który nie jest wystarczająco duży. Standard_DS2_v2 ma rozmiar pamięci podręcznej 172 GiB, co jest wystarczająco duży. W tym przypadku standardowa_ds3_v2 to najmniejszy rozmiar z serii DSv2 korzystających z tego obrazu. Podstawowe obrazy systemu Linux w obrazów witryny Marketplace i systemu Windows Server, które są wskazywane przez `[smallsize]` zwykle około 30 GiB i mogą korzystać z większości dostępnych rozmiarów maszyn wirtualnych.

Efemeryczne dyski wymaga również, że rozmiar maszyny Wirtualnej obsługuje usługę Premium storage. Rozmiary zazwyczaj (ale nie zawsze) mają `s` w nazwie, takich jak DSv2 i EsV3. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych Azure](../articles/virtual-machines/linux/sizes.md) Aby uzyskać szczegółowe informacje, na których rozmiary obsługuje usługi Premium storage.

## <a name="powershell"></a>PowerShell

Aby korzystać z efemerycznego dysku dla wdrożenia maszyny Wirtualnej programu PowerShell, użyj [AzVMOSDisk zestaw](/powershell/module/az.compute/set-azvmosdisk) w konfiguracji maszyny Wirtualnej. Ustaw `-DiffDiskSetting` do `Local` i `-Caching` do `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Dla wdrożeń zestawu skalowania, użyj [AzVmssStorageProfile zestaw](/powershell/module/az.compute/set-azvmssstorageprofile) polecenia cmdlet w konfiguracji. Ustaw `-DiffDiskSetting` do `Local` i `-Caching` do `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Interfejs wiersza polecenia

Aby korzystać z efemerycznego dysku dla wdrożenia maszyny Wirtualnej interfejs wiersza polecenia, ustaw `--ephemeral-os-disk` parametru w [tworzenie az vm](/cli/azure/vm#az-vm-create) do `true` i `--os-disk-caching` parametr `ReadOnly`.

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

Dla zestawów skalowania, możesz używać tego samego `--ephemeral-os-disk true` parametr [Tworzenie zestawu skalowania maszyn wirtualnych az](/cli/azure/vmss#az-vmss-create) i ustaw `--os-disk-caching` parametr `ReadOnly`.

## <a name="portal"></a>Portal   

W witrynie Azure portal, użytkownik może korzystać z dysków tymczasowych, podczas wdrażania maszyny Wirtualnej, otwierając **zaawansowane** części **dysków** kartę. Aby uzyskać **użycia efemerycznego dysku systemu operacyjnego** wybierz **tak**.

![Zrzut ekranu przedstawiający przycisk radiowy na wybór użycia efemerycznego dysku systemu operacyjnego](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Jeśli opcja korzystających z efemerycznego dysku są wyszarzone, być może wybrano rozmiar maszyny Wirtualnej, który nie ma rozmiar pamięci podręcznej większy niż obraz systemu operacyjnego lub która nie obsługuje usługę Premium storage. Wróć do **podstawy** strony, a następnie spróbuj wybrać inny rozmiar maszyny Wirtualnej.

Zestawy skalowania można również utworzyć przy użyciu efemeryczne dyski systemu operacyjnego przy użyciu portalu. Upewnij się, wybierz rozmiar maszyny Wirtualnej o rozmiarze pamięci podręcznej wystarczająco duży, a następnie w **użycia efemerycznego dysku systemu operacyjnego** wybierz **tak**.

![Zrzut ekranu przedstawiający przycisk radiowy na wybór użycia efemerycznego dysku systemu operacyjnego dla zestawu skalowania](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Wdrożenie szablonu zestawu skalowania  
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

## <a name="vm-template-deployment"></a>Wdrożenie szablonu maszyny Wirtualnej 
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

Odp.: Obsługiwane platformy i niestandardowych obrazów, rozmiarze pamięci podręcznej maszyny Wirtualnej, gdzie będzie można lokalnie na tym samym węźle jako maszyna wirtualna wszystkich odczyt/zapis na dysku systemu operacyjnego. 

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
- Usługa Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Wymiana dysku systemu operacyjnego 
