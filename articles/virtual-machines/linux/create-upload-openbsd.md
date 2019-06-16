---
title: Tworzenie i przekazywanie obrazu maszyny Wirtualnej OpenBSD na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie i przekazywanie wirtualnego dysku twardego (VHD) z systemem operacyjnym OpenBSD, aby utworzyć maszynę wirtualną platformy Azure przy użyciu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 2e580a94e568f201587c06efa827006386cd6bd9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60327684"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Tworzenie i przekazywanie obrazu dysku OpenBSD na platformie Azure
Ten artykuł pokazuje, jak tworzenie i przekazywanie wirtualnego dysku twardego (VHD) z systemem operacyjnym OpenBSD. Po przekazaniu go można użyć go jako swój własny obraz, aby utworzyć maszynę wirtualną (VM) na platformie Azure przy użyciu wiersza polecenia platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz następujące elementy:

* **Subskrypcja platformy Azure** — Jeśli nie masz konta, możesz utworzyć w zaledwie kilka minut. Jeśli masz subskrypcję MSDN, zobacz [Azure miesięczne środki dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). W przeciwnym razie Dowiedz się, jak [Utwórz bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).  
* **Interfejs wiersza polecenia Azure** — upewnij się, że zainstalowano najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) zainstalowane i zalogować się do konta platformy Azure za pomocą [az login](/cli/azure/reference-index).
* **OpenBSD zainstalowanego systemu operacyjnego w pliku VHD** — OpenBSD obsługiwany system operacyjny ([wersji 6.2 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.2/amd64/)) musi być zainstalowany na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD. Na przykład można użyć rozwiązania wirtualizacji, takie jak funkcji Hyper-V do tworzenia plików VHD i zainstalować system operacyjny. Aby uzyskać instrukcje dotyczące sposobu instalowania i korzystania z funkcji Hyper-V, zobacz [instalacji funkcji Hyper-V i utworzenie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Przygotowywanie obrazu OpenBSD dla platformy Azure
Na maszynie Wirtualnej, w którym zainstalowano system operacyjny OpenBSD 6.1, który dodaje funkcji Hyper-V obsługuje, wykonaj następujące procedury:

1. Jeśli podczas instalacji nie jest włączony protokół DHCP, włącz usługę w następujący sposób:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konfigurowanie konsoli szeregowej w następujący sposób:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Skonfiguruj instalacji pakietu aktualizacji w następujący sposób:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Domyślnie `root` użytkownik jest wyłączony w przypadku maszyn wirtualnych na platformie Azure. Użytkownicy mogą uruchamiać polecenia z podwyższonym poziomem uprawnień przy użyciu `doas` polecenia na maszynie Wirtualnej OpenBSD. Doas jest domyślnie włączona. Aby uzyskać więcej informacji, zobacz [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Zainstaluj i skonfiguruj wymagania wstępne dotyczące agenta usługi Azure w następujący sposób:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Najnowsza wersja agenta programu Azure zawsze można znaleźć na [GitHub](https://github.com/Azure/WALinuxAgent/releases). Zainstaluj agenta w następujący sposób:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po zainstalowaniu agenta usługi Azure, jest dobry pomysł, aby sprawdzić, czy działa w następujący sposób:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Anulowanie aprowizacji systemu, czyszczenia i odpowiednie do reprovisioning. Następujące polecenie spowoduje również usunięcie, ostatnie aprowizowane konto użytkownika i skojarzone dane:

    ```sh
    waagent -deprovision+user -force
    ```

Teraz można zamknąć maszyny Wirtualnej.


## <a name="prepare-the-vhd"></a>Przygotowywanie wirtualnego dysku twardego
VHDX format jest nieobsługiwane na platformie Azure, tylko **stałej wirtualnego dysku twardego**. Można konwertować na dysk stały format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub programu Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) polecenia cmdlet. Przykładem jest jak poniższy.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Tworzenie zasobów magazynu i przekaż
Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby przekazać wirtualnego dysku twardego, należy utworzyć konto magazynu przy użyciu [Tworzenie konta magazynu az](/cli/azure/storage/account). Nazwy kont magazynu muszą być unikatowe, dlatego podaj własną nazwę. Poniższy przykład tworzy konto magazynu o nazwie *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Aby kontrolować dostęp do konta magazynu, uzyskanie klucza magazynu przy użyciu [listy kluczy kont magazynu az](/cli/azure/storage/account/keys) w następujący sposób:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Do logicznego odseparowania wirtualne dyski twarde, możesz przekazać, należy utworzyć kontener w ramach konta magazynu przy użyciu [utworzyć kontenera magazynu az](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Na koniec Przekaż wirtualnego dysku twardego z [az storage blob upload](/cli/azure/storage/blob) w następujący sposób:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Tworzenie maszyny Wirtualnej na podstawie wirtualnego dysku twardego
Można utworzyć maszynę Wirtualną z [przykładowy skrypt](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) lub bezpośrednio z [tworzenie az vm](/cli/azure/vm). Aby określić OpenBSD wirtualnego dysku twardego, został przekazany, użyj `--image` parametru w następujący sposób:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Uzyskaj adres IP dla maszyny Wirtualnej za pomocą OpenBSD [az vm-— adresy ip](/cli/azure/vm) w następujący sposób:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Teraz możesz SSH z maszyną wirtualną OpenBSD, jak zwykle:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz dowiedzieć się więcej na temat obsługi funkcji Hyper-V na OpenBSD6.1, zapoznaj się z [OpenBSD 6.1](https://www.openbsd.org/61.html) i [hyperv.4](https://man.openbsd.org/hyperv.4).

Jeśli chcesz utworzyć Maszynę wirtualną z dysków zarządzanych, zapoznaj się z [dysku az](/cli/azure/disk). 
