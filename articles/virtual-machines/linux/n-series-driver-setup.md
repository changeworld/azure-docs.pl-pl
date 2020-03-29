---
title: Konfiguracja sterownika gpu platformy Azure z serii N dla systemu Linux
description: Jak skonfigurować sterowniki GPU NVIDIA dla maszyn wirtualnych z serii N z systemem Linux na platformie Azure
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944553"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalowanie sterowników GPU NVIDIA na maszynach wirtualnych z serii N z systemem Linux

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure z systemem Linux, należy zainstalować sterowniki GPU NVIDIA. [Rozszerzenie sterownika GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub zarządzaj nim za pomocą witryny Azure portal lub narzędzi, takich jak szablony interfejsu wiersza polecenia platformy Azure lub usługi Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterowników procesora GRAFICZNEGO NVIDIA, aby](../extensions/hpccompute-gpu-linux.md) uzyskać obsługiwane dystrybucje i kroki wdrażania.

Jeśli zdecydujesz się zainstalować sterowniki GPU ręcznie, ten artykuł zawiera obsługiwane dystrybucje, sterowniki oraz kroki instalacji i weryfikacji. Informacje o ręcznej konfiguracji sterownika są również dostępne dla [maszyn wirtualnych z systemem Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby zapoznać się ze specyfikacjami maszyn wirtualnych z serii N, pojemnościami pamięci masowej i szczegółami dysku, zobacz [rozmiary maszyn wirtualnych gpu z systemem Linux](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalowanie sterowników CUDA na maszynach wirtualnych serii N

Oto kroki, aby zainstalować sterowniki CUDA z zestawu narzędzi NVIDIA CUDA na maszynach wirtualnych z serii N. 


Deweloperzy języka C i C++ mogą opcjonalnie zainstalować pełny zestaw narzędzi do tworzenia aplikacji z akcelerowanymi procesorami GPU. Aby uzyskać więcej informacji, zobacz [Podręcznik instalacji CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Aby zainstalować sterowniki CUDA, nawiązuj połączenie SSH do każdej maszyny Wirtualnej. Aby sprawdzić, czy system ma procesor graficzny obsługujący cuda, uruchom następujące polecenie:

```bash
lspci | grep -i NVIDIA
```
Zobaczysz wyjście podobne do poniższego przykładu (pokazujące kartę NVIDIA Tesla K80):

![wyjście polecenia lspci](./media/n-series-driver-setup/lspci.png)

Następnie uruchom polecenia instalacji specyficzne dla dystrybucji.

### <a name="ubuntu"></a>Ubuntu 

1. Pobierz i zainstaluj sterowniki CUDA ze strony nvidia. Na przykład dla Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   Instalacja może potrwać kilka minut.

2. Aby opcjonalnie zainstalować kompletny zestaw narzędzi CUDA, należy wpisać:

   ```bash
   sudo apt-get install cuda
   ```

3. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.

#### <a name="cuda-driver-updates"></a>Aktualizacje sterowników CUDA

Zaleca się okresowe aktualizowanie sterowników CUDA po wdrożeniu.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>System CentOS lub Red Hat Enterprise Linux

1. Zaktualizuj jądro (zalecane). Jeśli zdecydujesz się nie aktualizować jądra, `kernel-devel` upewnij `dkms` się, że wersje i są odpowiednie dla twojego jądra.

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
 
3. Podłącz ponownie do maszyny Wirtualnej i kontynuuj instalację za pomocą następujących poleceń:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Instalacja może potrwać kilka minut. 

4. Aby opcjonalnie zainstalować kompletny zestaw narzędzi CUDA, należy wpisać:

   ```bash
   sudo yum install cuda
   ```

5. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.

### <a name="verify-driver-installation"></a>Weryfikowanie instalacji sterownika

Aby zbadać stan urządzenia GPU, SSH do maszyny Wirtualnej i uruchomić narzędzie wiersza polecenia [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem. 

Jeśli sterownik jest zainstalowany, zobaczysz dane wyjściowe podobne do następujących. Należy zauważyć, że **GPU-Util** pokazuje 0% chyba że aktualnie jest uruchomione obciążenie gpu na maszynie wirtualnej. Wersja sterownika i szczegóły gpu mogą się różnić od podanych.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Łączność sieciowa RDMA

Łączność sieciowa RDMA może być włączona na maszynach wirtualnych z serii N obsługujących technologię RDMA, takich jak NC24r wdrożonych w tym samym zestawie dostępności lub w jednej grupie miejsc docelowych w zestawie skalowania maszyn wirtualnych. Sieć RDMA obsługuje ruch mpi (Message Passing Interface) dla aplikacji z procesorem Intel MPI 5.x lub nowszą wersją. Należy przestrzegać dodatkowych wymagań:

### <a name="distributions"></a>Dystrybucji

Wdrażanie maszyn wirtualnych z obsługą technologii N z obsługą usług RDMA z jednego z obrazów w portalu Azure Marketplace obsługującego łączność RDMA na maszynach wirtualnych z serii N:
  
* **Ubuntu 16.04 LTS** - Skonfiguruj sterowniki RDMA na maszynie wirtualnej i zarejestruj się w firmie Intel, aby pobrać intel mpi:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* Na maszynie wirtualnej są zainstalowane sterowniki HPC oparte na **centos 7.4 -** RDMA i Intel MPI 5.1.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalowanie sterowników GRID na maszynach wirtualnych serii NV lub NVv3

Aby zainstalować sterowniki NVIDIA GRID na maszynach wirtualnych z serii NV lub NVv3, nawiąż połączenie SSH z każdą maszyną wirtualną i wykonaj kroki dotyczące dystrybucji systemu Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Uruchom polecenie `lspci`. Sprawdź, czy karta lub karty NVIDIA M60 są widoczne jako urządzenia PCI.

2. Zainstaluj aktualizacje.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem NVIDIA. (Ze sterownika NVIDIA należy używać wyłącznie na maszynach wirtualnych NV lub NVv2). Aby to zrobić, utwórz plik `/etc/modprobe.d` o nazwie `nouveau.conf` z następującą zawartością:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Uruchom ponownie maszynę wirtualną i ponownie nałącz. Wyjście z serwera X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Pobierz i zainstaluj sterownik GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Gdy pojawi się pytanie, czy chcesz uruchomić narzędzie nvidia-xconfig, aby zaktualizować plik konfiguracyjny X, wybierz **tak**.

7. Po zakończeniu instalacji, skopiować /etc/nvidia/gridd.conf.template do nowego pliku gridd.conf w lokalizacji /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Dodaj następujące `/etc/nvidia/gridd.conf`elementy do:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Usuń z `/etc/nvidia/gridd.conf` tego, czy jest on obecny, usuń następujące elementy:
 
   ```
   FeatureType=0
   ```
10. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.


### <a name="centos-or-red-hat-enterprise-linux"></a>System CentOS lub Red Hat Enterprise Linux 

1. Zaktualizuj jądro i DKMS (zalecane). Jeśli zdecydujesz się nie aktualizować jądra, `kernel-devel` upewnij `dkms` się, że wersje i są odpowiednie dla twojego jądra.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Wyłącz sterownik jądra Nouveau, który jest niezgodny ze sterownikiem NVIDIA. (Ze sterownika NVIDIA należy używać wyłącznie na maszynach wirtualnych NV lub NV2). Aby to zrobić, utwórz plik `/etc/modprobe.d` o nazwie `nouveau.conf` z następującą zawartością:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Uruchom ponownie maszynę wirtualną, połącz się ponownie i zainstaluj najnowsze [usługi integracji systemu Linux dla funkcji Hyper-V i platformy Azure.](https://www.microsoft.com/download/details.aspx?id=55106)
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Połącz się ponownie z `lspci` maszyną wirtualną i uruchom polecenie. Sprawdź, czy karta lub karty NVIDIA M60 są widoczne jako urządzenia PCI.
 
5. Pobierz i zainstaluj sterownik GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Gdy pojawi się pytanie, czy chcesz uruchomić narzędzie nvidia-xconfig, aby zaktualizować plik konfiguracyjny X, wybierz **tak**.

7. Po zakończeniu instalacji, skopiować /etc/nvidia/gridd.conf.template do nowego pliku gridd.conf w lokalizacji /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Dodaj następujące `/etc/nvidia/gridd.conf`elementy do:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Usuń z `/etc/nvidia/gridd.conf` tego, czy jest on obecny, usuń następujące elementy:
 
   ```
   FeatureType=0
   ```
10. Uruchom ponownie maszynę wirtualną i przejdź do weryfikacji instalacji.


### <a name="verify-driver-installation"></a>Weryfikowanie instalacji sterownika


Aby zbadać stan urządzenia GPU, SSH do maszyny Wirtualnej i uruchomić narzędzie wiersza polecenia [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) zainstalowane ze sterownikiem. 

Jeśli sterownik jest zainstalowany, zobaczysz dane wyjściowe podobne do następujących. Należy zauważyć, że **GPU-Util** pokazuje 0% chyba że aktualnie jest uruchomione obciążenie gpu na maszynie wirtualnej. Wersja sterownika i szczegóły gpu mogą się różnić od podanych.

![Stan urządzenia NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Serwer X11
Jeśli potrzebujesz serwera X11 do zdalnego połączenia z maszyną wirtualną NV lub NVv2, zaleca się [x11vnc,](http://www.karlrunge.com/x11vnc/) ponieważ umożliwia on akcelerację sprzętową grafiki. BusID urządzenia M60 musi być ręcznie dodany do pliku konfiguracyjnego X11 (zwykle). `etc/X11/xorg.conf` Dodaj `"Device"` sekcję podobną do następującej:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ponadto zaktualizuj sekcję, `"Screen"` aby korzystać z tego urządzenia.
 
Dziesiętny busid można znaleźć, uruchamiając

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID można zmienić, gdy maszyna wirtualna zostanie ponownie przydzielona lub ponownie uruchomiony. W związku z tym można utworzyć skrypt, aby zaktualizować BusID w konfiguracji X11 po ponownym uruchomieniu maszyny Wirtualnej. Na przykład utwórz `busidupdate.sh` skrypt o nazwie (lub inną nazwę, którą wybierzesz) z zawartością podobną do następującej:

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

Następnie utwórz wpis dla skryptu aktualizacji, aby `/etc/rc.d/rc3.d` skrypt był wywoływany jako katalog główny podczas rozruchu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Można ustawić tryb trwałości przy użyciu `nvidia-smi` tak dane wyjściowe polecenia jest szybszy, gdy trzeba kwerendy kart. Aby ustawić tryb trwałości, wykonaj `nvidia-smi -pm 1`polecenie . Należy zauważyć, że jeśli maszyna wirtualna zostanie ponownie uruchomiona, ustawienie trybu znika. Zawsze można skrypt ustawienie trybu do wykonania po uruchomieniu.
* Jeśli zaktualizowano sterowniki NVIDIA CUDA do najnowszej wersji i okaże się, że rdma connectivcity nie działa, [zainstaluj ponownie sterowniki RDMA,](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) aby ponownie roztrząsać tę łączność. 

## <a name="next-steps"></a>Następne kroki

* Aby przechwycić obraz maszyny wirtualnej z systemem Linux z zainstalowanymi sterownikami NVIDIA, zobacz [Jak uogólnić i przechwycić maszynę wirtualną Systemu Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
