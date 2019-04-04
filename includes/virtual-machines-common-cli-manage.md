---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: beece95164f0d82b1aa7f22d56f4dce02f4bb38c
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891047"
---
Interfejs wiersza polecenia platformy Azure umożliwia tworzenie i zarządzanie zasobami platformy Azure w systemach macOS, Linux i Windows. Ten artykuł szczegółowo opisuje niektóre z najczęściej używanych poleceń, aby utworzyć i zarządzać maszyn wirtualnych (VM).

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli musisz uaktualnić, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli). Można również użyć [Cloud Shell](/azure/cloud-shell/quickstart) z poziomu przeglądarki.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Podstawowe polecenia usługi Azure Resource Manager w interfejsie wiersza polecenia platformy Azure
Aby uzyskać bardziej szczegółową pomoc dotyczącą konkretnych przełączników wiersza polecenia i opcji, wpisując można użyć Pomocy online dotyczącej poleceń i opcji `az <command> <subcommand> --help`.

### <a name="create-vms"></a>Tworzenie maszyn wirtualnych
| Zadanie | Polecenia interfejsu wiersza polecenia platformy Azure |
| --- | --- |
| Tworzenie grupy zasobów | `az group create --name myResourceGroup --location eastus` |
| Tworzenie maszyny wirtualnej z systemem Linux | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Tworzenie maszyny wirtualnej z systemem Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Zarządzanie stanem maszyny Wirtualnej
| Zadanie | Polecenia interfejsu wiersza polecenia platformy Azure |
| --- | --- |
| Uruchamianie maszyny wirtualnej | `az vm start --resource-group myResourceGroup --name myVM` |
| Zatrzymywanie maszyny wirtualnej | `az vm stop --resource-group myResourceGroup --name myVM` |
| Cofanie przydziału maszyny wirtualnej | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Ponowne uruchamianie maszyny wirtualnej | `az vm restart --resource-group myResourceGroup --name myVM` |
| Ponowne wdrażanie maszyny wirtualnej | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Usuwanie maszyny wirtualnej | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Pobierz informacje dotyczące maszyny Wirtualnej
| Zadanie | Polecenia interfejsu wiersza polecenia platformy Azure |
| --- | --- |
| Wyświetlanie listy maszyn wirtualnych | `az vm list` |
| Uzyskiwanie informacji o maszynie wirtualnej | `az vm show --resource-group myResourceGroup --name myVM` |
| Używanie zasobów maszyny wirtualnej | `az vm list-usage --location eastus` |
| Pobieranie wszystkich rozmiarów maszyn wirtualnych | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Dyski i obrazy
| Zadanie | Polecenia interfejsu wiersza polecenia platformy Azure |
| --- | --- |
| Dodawanie dysku danych do maszyny wirtualnej | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Usuwanie dysku danych z maszyny wirtualnej | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Zmienianie rozmiaru dysku | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Tworzenie migawki dysku | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Tworzenie obrazu maszyny Wirtualnej | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Tworzenie maszyny Wirtualnej na podstawie obrazu | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać dodatkowe przykłady poleceń interfejsu wiersza polecenia, zobacz [tworzenie i zarządzanie maszynami wirtualnymi systemu Linux przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md) samouczka.

