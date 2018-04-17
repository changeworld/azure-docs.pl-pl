---
title: Tworzenie i przekazywanie obrazu OpenBSD maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie i przekazywanie wirtualnego dysku twardego (VHD) z systemem operacyjnym OpenBSD, aby utworzyć maszynę wirtualną platformy Azure za pomocą wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 6c0eae36874c6d2738385c4530cc208a0b1362c4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Tworzenie i przekazywanie obrazu dysku OpenBSD na platformie Azure
W tym artykule opisano tworzenie i przekazywanie wirtualnego dysku twardego (VHD) z systemem operacyjnym OpenBSD. Po wysłaniu, służy ona jako własnego obrazu można utworzyć maszynę wirtualną (VM) na platformie Azure za pomocą wiersza polecenia platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz następujące elementy:

* **Subskrypcja platformy Azure** — Jeśli nie masz konta, możesz utworzyć jedną w zaledwie kilka minut. Jeśli masz subskrypcję MSDN, zobacz [Azure miesięczne środki dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). W przeciwnym razie Dowiedz się, jak [utworzyć bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI 2.0** — upewnij się, że masz najnowszą [Azure CLI 2.0](/cli/azure/install-azure-cli) zainstalowane i zalogowany do konta platformy Azure z [logowania az](/cli/azure/reference-index#az_login).
* **OpenBSD zainstalowanego systemu operacyjnego w pliku VHD** — OpenBSD obsługiwany system operacyjny ([wersji 6.1 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)) musi być zainstalowany na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD. Na przykład umożliwia rozwiązanie wirtualizacji, takich jak funkcja Hyper-V Utwórz plik VHD i zainstalowania systemu operacyjnego. Aby uzyskać instrukcje dotyczące instalowania i używania funkcji Hyper-V, zobacz [Instalowanie funkcji Hyper-V i tworzenie maszyny wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Przygotowywanie obrazu OpenBSD dla platformy Azure
Na maszynie Wirtualnej, w którym zainstalowano system operacyjny OpenBSD 6.1, dodanego funkcji Hyper-V obsługi, wykonaj następujące procedury:

1. Jeśli podczas instalacji nie jest włączony protokół DHCP, włącz usługę w następujący sposób:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konfigurowanie konsoli szeregowej w następujący sposób:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Instalacja pakietu należy skonfigurować w następujący sposób:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Domyślnie `root` użytkownik jest wyłączony na maszynach wirtualnych platformy Azure. Użytkownicy mogą uruchamiać polecenia z podwyższonym poziomem uprawnień przy użyciu `doas` na OpenBSD maszyny Wirtualnej. Doas jest domyślnie włączona. Aby uzyskać więcej informacji, zobacz [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Zainstaluj i skonfiguruj wymagania wstępne dotyczące agenta platformy Azure w następujący sposób:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Najnowszą wersję agenta programu Azure zawsze można znaleźć w [Github](https://github.com/Azure/WALinuxAgent/releases). Zainstaluj agenta w następujący sposób:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po zainstalowaniu agenta platformy Azure jest dobrym pomysłem jest sprawdzenie, czy działa w następujący sposób:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Anulowanie zastrzeżenia systemu do czyszczenia go i była odpowiednia dla reprovisioning. Następujące polecenie spowoduje również usunięcie ostatnie konto użytkownika elastycznie i skojarzone dane:

    ```sh
    waagent -deprovision+user -force
    ```

Teraz można zamknąć maszyny Wirtualnej.


## <a name="prepare-the-vhd"></a>Przygotowywanie wirtualnego dysku twardego
VHDX format jest nieobsługiwane w systemie Azure, tylko **stały VHD**. Można konwertować na dysk stały format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub programu Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) polecenia cmdlet. Przykładem jest następujące.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Utwórz zasoby magazynu i przekaż
Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby przekazać dysk VHD, Utwórz konto magazynu z [Tworzenie konta magazynu az](/cli/azure/storage/account#az_storage_account_create). Nazwy konta magazynu musi być unikatowa, więc podać własną nazwę. Poniższy przykład tworzy konto magazynu o nazwie *mojekontomagazynu*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Aby kontrolować dostęp do konta magazynu, należy uzyskać klucza magazynu z [listy kluczy konta magazynu az](/cli/azure/storage/account/keys#az_storage_account_keys_list) w następujący sposób:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Aby rozdzielić logicznie możesz przekazać pliki VHD, należy utworzyć kontener w ramach konta magazynu z [utworzyć kontenera magazynu az](/cli/azure/storage/container#az_storage_container_create):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Na koniec, Przekaż dysk VHD o [az magazynu obiektów blob przekazywania](/cli/azure/storage/blob#az_storage_blob_upload) w następujący sposób:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Tworzenie maszyny Wirtualnej z dysk VHD
Można utworzyć maszyny Wirtualnej z [przykładowy skrypt](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) lub bezpośrednio z [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create). Aby określić OpenBSD wirtualnego dysku twardego, należy przekazać, użyj `--image` parametru w następujący sposób:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Uzyskaj adres IP dla maszyny Wirtualnej z OpenBSD [az vm--adresy ip](/cli/azure/vm#list-ip-addresses) w następujący sposób:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Teraz możesz SSH do maszyny Wirtualnej OpenBSD normal:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz dowiedzieć się więcej na temat obsługi funkcji Hyper-V na OpenBSD6.1 odczytu [OpenBSD 6.1](https://www.openbsd.org/61.html) i [hyperv.4](http://man.openbsd.org/hyperv.4).

Jeśli chcesz utworzyć Maszynę wirtualną od dysków zarządzanych w odczytu [dysku az](/cli/azure/disk). 
