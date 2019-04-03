---
title: Instalacja sterowników procesora GPU z serii N usługi Azure dla systemu Linux | Dokumentacja firmy Microsoft
description: Jak skonfigurować sterowniki procesora GPU NVIDIA dla maszyn wirtualnych serii N z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c80b821d6bd0263473ba0178eea148f7a2d5773
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879050"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalowanie sterowników procesora GPU NVIDIA na maszynach wirtualnych serii N z systemem Linux

Aby móc korzystać z możliwości procesora GPU platformy Azure maszyn wirtualnych serii N z systemem Linux, muszą być zainstalowane sterowniki procesora GPU firmy NVIDIA. [Rozszerzenia sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub siatki na maszynie Wirtualnej serii N. Zainstaluj lub zarządzać rozszerzenia za pomocą witryny Azure portal lub narzędzi, takich jak szablony wiersza polecenia platformy Azure lub usługi Azure Resource Manager. Zobacz [dokumentację dotyczącą rozszerzeń sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) obsługiwane dystrybucje i kroki związane z wdrażaniem.

Jeśli zdecydujesz się zainstalować sterowniki procesora GPU ręcznie, ten artykuł zawiera obsługiwane dystrybucje, sterowników i kroki instalacji i weryfikacji. Informacje o konfiguracji ręcznej sterownik jest również dostępna dla [maszyn wirtualnych Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Specyfikacje, pojemności magazynu i dysku szczegółów maszyn wirtualnych serii N dla [rozmiarów maszyn wirtualnych systemu Linux GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Zainstaluj sterowniki CUDA na maszynach wirtualnych serii N

Poniżej przedstawiono kroki, aby zainstalować sterowniki CUDA w zestawie narzędzi programu NVIDIA CUDA, na maszynach wirtualnych serii N. 


Programiści C i C++ może opcjonalnie zainstalować pełny zestaw narzędzi do kompilowania aplikacji accelerated procesora GPU. Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Aby zainstalować sterowniki CUDA, Utwórz połączenie SSH do każdej maszyny Wirtualnej. Aby sprawdzić, czy system ma obsługą CUDA procesora GPU, uruchom następujące polecenie:

```bash
lspci | grep -i NVIDIA
```
Zostanie wyświetlone dane wyjściowe podobne do poniższego przykładu (wyświetlanie karty procesory GPU NVIDIA Tesla K80):

![dane wyjściowe polecenia lspci](./media/n-series-driver-setup/lspci.png)

Następnie polecenia uruchamiania instalacji specyficzne dla Twojej dystrybucji.

### <a name="ubuntu"></a>Ubuntu 

1. Pobierz i zainstaluj sterowniki CUDA w witrynie sieci Web firmy NVIDIA. Na przykład Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   Instalacja może zająć kilka minut.

2. Opcjonalnie można zainstalować pełny zestaw narzędzi CUDA, wpisz:

   ```bash
   sudo apt-get install cuda
   ```

3. Uruchom ponownie maszynę Wirtualną, a następnie przejść do weryfikowania instalacji usług.

#### <a name="cuda-driver-updates"></a>Aktualizacje sterowników CUDA

Firma Microsoft zaleca, okresowo aktualizować CUDA sterowników po wdrożeniu.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS or Red Hat Enterprise Linux

1. Zaktualizuj jądra (zalecane). Jeśli postanowisz nie aktualizować jądra, upewnij się, że wersje `kernel-devel` i `dkms` są odpowiednie dla Twojej jądra.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Ponowne łączenie z maszyną wirtualną i kontynuować instalację za pomocą następujących poleceń:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Instalacja może zająć kilka minut. 

4. Opcjonalnie można zainstalować pełny zestaw narzędzi CUDA, wpisz:

   ```bash
   sudo yum install cuda
   ```

5. Uruchom ponownie maszynę Wirtualną, a następnie przejść do weryfikowania instalacji usług.

### <a name="verify-driver-installation"></a>Weryfikacja instalacji sterowników

Zapytanie procesora GPU stanie urządzenia, SSH do maszyn wirtualnych i uruchamiany [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem narzędzie wiersza polecenia. 

Jeśli sterownik jest zainstalowany, pojawi się dane wyjściowe podobne do następujących. Należy pamiętać, że **GPU Util** pokazuje 0%, chyba że obciążenie procesora GPU są aktualnie uruchomione na maszynie Wirtualnej. Twoja wersja sterownika i szczegóły procesora GPU mogą różnić się od wyświetlonych.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Połączenie sieciowe RDMA

Łączności sieciowej RDMA można włączyć na maszynach wirtualnych z serii N funkcją RDMA, takie jak NC24r wdrożonych w tym samym zestawie dostępności lub w pojedynczej grupy umieszczania w zestawie skalowania maszyn wirtualnych. Sieć RDMA obsługuje ruch interfejsu przekazywania komunikatów (MPI) dla aplikacji uruchamianych przy użyciu Intel MPI 5.x lub nowszej wersji. Wykonaj dodatkowe wymagania:

### <a name="distributions"></a>Dystrybucje

Wdrażanie z obsługą dostępu RDMA maszyny wirtualne z serii N z jednego z obrazów w portalu Azure Marketplace obsługuje połączenia RDMA na maszynach wirtualnych serii N:
  
* **Ubuntu 16.04 LTS** — Konfigurowanie sterowników RDMA na maszynie Wirtualnej i zarejestrowanie firmy Intel, Intel MPI pobierania:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **Opartych na systemie centOS 7.4 HPC** — sterowniki RDMA i Intel MPI 5.1 są zainstalowane na maszynie Wirtualnej.

## <a name="install-grid-drivers-on-nv-or-nvv2-series-vms"></a>Zainstaluj sterowniki siatki na serii NV lub maszyny wirtualne z serii NVv2

Aby zainstalować sterowniki NVIDIA GRID NV lub maszyny wirtualne z serii NVv2, Utwórz połączenie SSH do każdej maszyny Wirtualnej, a następnie postępuj zgodnie z instrukcjami dla Twojej dystrybucji systemu Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Uruchom polecenie `lspci`. Sprawdź, czy karty M60 firmy NVIDIA lub kart są widoczne jako urządzenia PCI.

2. Instalowanie aktualizacji.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem firmy NVIDIA. (Tylko używać sterowników firmy NVIDIA na serii NV lub maszynach wirtualnych NVv2). Aby to zrobić, Utwórz plik w `/etc/modprobe.d` o nazwie `nouveau.conf` z następującą zawartością:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Ponowne uruchomienie maszyny Wirtualnej i ponownie. Serwer X zakończenia:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Pobierz i zainstaluj sterownik siatki:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Gdy pojawi się prośba czy chcesz uruchomić narzędzie nvidia xconfig, aby zaktualizować plik konfiguracji X, wybierz **tak**.

7. Po zakończeniu instalacji należy skopiować /etc/nvidia/gridd.conf.template do nowych gridd.conf pliku w lokalizacji/etc/nvidia /

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Dodaj następujące polecenie, aby `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   ```
9. Uruchom ponownie maszynę Wirtualną, a następnie przejść do weryfikowania instalacji usług.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS or Red Hat Enterprise Linux 

1. Zaktualizuj jądro i DKMS (zalecane). Jeśli postanowisz nie aktualizować jądra, upewnij się, że wersje `kernel-devel` i `dkms` są odpowiednie dla Twojej jądra.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   ```

2. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem firmy NVIDIA. (Tylko używać sterowników firmy NVIDIA na serii NV lub maszynach wirtualnych NV2). Aby to zrobić, Utwórz plik w `/etc/modprobe.d` o nazwie `nouveau.conf` z następującą zawartością:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Uruchom ponownie maszynę Wirtualną, połącz się ponownie i zainstaluj najnowszą wersję [usługi integracji systemu Linux dla funkcji Hyper-V i platformą Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Ponownie połączyć się z maszyną Wirtualną i uruchom `lspci` polecenia. Sprawdź, czy karty M60 firmy NVIDIA lub kart są widoczne jako urządzenia PCI.
 
5. Pobierz i zainstaluj sterownik siatki:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Gdy pojawi się prośba czy chcesz uruchomić narzędzie nvidia xconfig, aby zaktualizować plik konfiguracji X, wybierz **tak**.

7. Po zakończeniu instalacji należy skopiować /etc/nvidia/gridd.conf.template do nowych gridd.conf pliku w lokalizacji/etc/nvidia /
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Dodaj następujące polecenie, aby `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   ```
9. Uruchom ponownie maszynę Wirtualną, a następnie przejść do weryfikowania instalacji usług.

### <a name="verify-driver-installation"></a>Weryfikacja instalacji sterowników


Zapytanie procesora GPU stanie urządzenia, SSH do maszyn wirtualnych i uruchamiany [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem narzędzie wiersza polecenia. 

Jeśli sterownik jest zainstalowany, pojawi się dane wyjściowe podobne do następujących. Należy pamiętać, że **GPU Util** pokazuje 0%, chyba że obciążenie procesora GPU są aktualnie uruchomione na maszynie Wirtualnej. Twoja wersja sterownika i szczegóły procesora GPU mogą różnić się od wyświetlonych.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 serwera
Jeśli potrzebujesz X11 serwera dla połączeń zdalnych NV lub NVv2 VM [x11vnc](http://www.karlrunge.com/x11vnc/) jest zalecana, ponieważ umożliwia przyspieszanie sprzętowe grafiki. BusID urządzenia M60 należy ręcznie dodać do X11 pliku konfiguracji (zazwyczaj `etc/X11/xorg.conf`). Dodaj `"Device"` podobne do następujących sekcji:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ponadto należy zaktualizować swoje `"Screen"` sekcji, aby używać tego urządzenia.
 
Separatora dziesiętnego BusID można znaleźć, uruchamiając

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID można zmienić, gdy na maszynie Wirtualnej pobiera ponownie przydzielane lub ponownie uruchomiony. W związku z tym, możesz chcieć utworzyć skrypt, aby zaktualizować BusID w X11 Konfiguracja w przypadku ponownego rozruchu maszyny Wirtualnej. Na przykład utworzyć skrypt o nazwie `busidupdate.sh` (lub inną wybraną nazwę wybranej) z zawartością podobny do następującego:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Następnie należy utworzyć wpis dla skryptu aktualizacji w `/etc/rc.d/rc3.d` , skrypt jest wywoływana jako główny urząd certyfikacji na rozruch.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Można ustawić w trybie trwałości `nvidia-smi` , dane wyjściowe polecenia jest szybsza, kiedy trzeba karty zapytania. Aby ustawić tryb trwałości, należy wykonać `nvidia-smi -pm 1`. Należy pamiętać, że jeśli maszyna wirtualna zostanie ponownie uruchomiony, ustawienie trybu stanie się niepotrzebna. Można zawsze skryptu ustawienie trybu do wykonywania podczas uruchamiania.

## <a name="next-steps"></a>Kolejne kroki

* Aby przechwycić obraz maszyny Wirtualnej systemu Linux zainstalowane sterowniki NVIDIA, zobacz [jak uogólnić i przechwycić maszynę wirtualną z systemem Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
