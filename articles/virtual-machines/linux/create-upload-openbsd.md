---
title: Tworzenie i przesyłanie obrazu OpenBSD
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy (VHD), który zawiera system operacyjny OpenBSD w celu utworzenia maszyny wirtualnej platformy Azure za pośrednictwem interfejsu wiersza polecenia platformy Azure
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 1ad1a66d67be7aefe4d9a7acae993e8788cbb193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066745"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Tworzenie i przekazywanie obrazu dysku OpenBSD na platformę Azure
W tym artykule pokazano, jak utworzyć i przekazać wirtualny dysk twardy (VHD), który zawiera system operacyjny OpenBSD. Po przekazaniu go można użyć go jako własnego obrazu do utworzenia maszyny wirtualnej (VM) na platformie Azure za pośrednictwem interfejsu wiersza polecenia platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz następujące elementy:

* **Subskrypcja platformy Azure** — jeśli nie masz konta, możesz je utworzyć w ciągu zaledwie kilku minut. Jeśli masz subskrypcję MSDN, zobacz [Miesięczne środki platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). W przeciwnym razie dowiedz się, jak [utworzyć bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).  
* **Interfejs wiersza polecenia platformy Azure** — upewnij się, że masz zainstalowaną i zalogowaną do konta platformy Azure najnowszą [platformę Azure](/cli/azure/install-azure-cli) za pomocą [logowania az.](/cli/azure/reference-index)
* **System operacyjny OpenBSD zainstalowany w pliku vhd** - Obsługiwany system operacyjny OpenBSD[(6.6 wersja AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) musi być zainstalowany na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików vhd. Na przykład można użyć rozwiązania wirtualizacji, takiego jak Funkcja Hyper-V, aby utworzyć plik vhd i zainstalować system operacyjny. Aby uzyskać instrukcje dotyczące instalowania i używania funkcji Hyper-V, zobacz [Instalowanie funkcji Hyper-V i tworzenie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Przygotowywanie obrazu OpenBSD dla platformy Azure
Na maszynie wirtualnej, na której zainstalowano system operacyjny OpenBSD 6.1, który dodał obsługę funkcji Hyper-V, wykonaj następujące procedury:

1. Jeśli usługa DHCP nie jest włączona podczas instalacji, włącz usługę w następujący sposób:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Skonfiguruj konsolę szeregową w następujący sposób:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Skonfiguruj instalację pakietu w następujący sposób:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Domyślnie `root` użytkownik jest wyłączony na maszynach wirtualnych na platformie Azure. Użytkownicy mogą uruchamiać polecenia z podwyższonymi uprawnieniami za pomocą `doas` polecenia na openbsd maszyny wirtualnej. Doas jest domyślnie włączona. Aby uzyskać więcej informacji, zobacz [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Zainstaluj i skonfiguruj wymagania wstępne dla agenta platformy Azure w następujący sposób:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Najnowszą wersję agenta platformy Azure zawsze można znaleźć w [usłudze GitHub.](https://github.com/Azure/WALinuxAgent/releases) Zainstaluj agenta w następujący sposób:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po zainstalowaniu usługi Azure Agent warto sprawdzić, czy działa w następujący sposób:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Deprovision systemu, aby go wyczyścić i sprawiają, że nadaje się do ponownego udostępnienia. Następujące polecenie usuwa również ostatnie aprowizowane konto użytkownika i skojarzone dane:

    ```sh
    waagent -deprovision+user -force
    ```

Teraz możesz zamknąć maszynę wirtualną.


## <a name="prepare-the-vhd"></a>Przygotowanie dysku VHD
Format VHDX nie jest obsługiwany na platformie Azure, tylko **stały dysk VHD**. Dysk można przekonwertować na stały format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet [powershell convert-vhd.](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) Przykład jest następujący.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Tworzenie zasobów magazynu i przekazywanie
Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby przesłać dysk VHD, utwórz konto pamięci masowej z [kontem az storage create](/cli/azure/storage/account). Nazwy kont magazynu muszą być unikatowe, więc podaj własną nazwę. W poniższym przykładzie utworzy się konto magazynu o nazwie *mystorageaccount:*

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Aby kontrolować dostęp do konta magazynu, uzyskaj klucz magazynu z [listą kluczy konta magazynu az](/cli/azure/storage/account/keys) w następujący sposób:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Aby logicznie oddzielić przekazane dyski VHD, utwórz kontener na koncie magazynu za pomocą [kontenera magazynu AZ create:](/cli/azure/storage/container)

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Na koniec prześlij dysk VHD z [az storage blob upload](/cli/azure/storage/blob) w następujący sposób:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Tworzenie maszyny wirtualnej z dysku VHD
Maszynę wirtualną można utworzyć za pomocą [przykładowego skryptu](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) lub bezpośrednio za pomocą [az vm create](/cli/azure/vm). Aby określić przesłany dysk VHD OpenBSD, użyj następującego parametru: `--image`

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Uzyskaj adres IP maszyny Wirtualnej OpenBSD z [az vm list-ip-addresses](/cli/azure/vm) w następujący sposób:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Teraz możesz ssh do maszyny Wirtualnej OpenBSD jak zwykle:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Następne kroki
Jeśli chcesz dowiedzieć się więcej o obsłudze funkcji Hyper-V w openbsd6.1, przeczytaj [OpenBSD 6.1](https://www.openbsd.org/61.html) i [hyperv.4](https://man.openbsd.org/hyperv.4).

Jeśli chcesz utworzyć maszynę wirtualną z dysku zarządzanego, odczyt [dysku az](/cli/azure/disk). 
