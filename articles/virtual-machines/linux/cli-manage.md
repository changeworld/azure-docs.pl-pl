---
title: Typowe polecenia interfejsu wiersza polecenia platformy Azure
description: Poznaj niektóre typowe polecenia interfejsu wiersza polecenia platformy Azure, aby rozpocząć zarządzanie maszynami wirtualnymi w trybie usługi Azure Resource Manager
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969249"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Typowe polecenia interfejsu wiersza polecenia platformy Azure do zarządzania zasobami platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi w systemach macOS, Linux i Windows. W tym artykule opisano niektóre z najczęstszych poleceń do tworzenia maszyn wirtualnych (VM) i zarządzania nimi.

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli chcesz przeprowadzić uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Możesz również użyć [Cloud Shell](/azure/cloud-shell/quickstart) z przeglądarki.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Podstawowe polecenia usługi Azure Resource Manager w interfejsie wiersza polecenia platformy Azure
Aby uzyskać bardziej szczegółową pomoc dotyczącą określonych przełączników i opcji wiersza `az <command> <subcommand> --help`polecenia, możesz użyć pomocy i opcji polecenia online, wpisując polecenie .

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

### <a name="get-vm-info"></a>Uzyskaj informacje o maszynie wirtualnej
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
| Tworzenie maszyny wirtualnej na podstawie obrazu | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe przykłady poleceń interfejsu wiersza polecenia, zobacz [Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą samouczka interfejsu wiersza polecenia platformy Azure.](tutorial-manage-vm.md)



