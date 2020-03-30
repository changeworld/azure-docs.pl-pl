---
title: Tworzenie serwera Ubuntu usługi NFS (Network File System) do użytku przez zasobników usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin NFS Ubuntu Linux Server do użytku z zasobnikami w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: e5676710bc47557318f3e2adcf36ec0ed13d47de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596627"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Ręczne tworzenie i używanie woluminu serwera Linux (NFS) z usługą Azure Kubernetes Service (AKS)
Udostępnianie danych między kontenerami jest często niezbędnym składnikiem usług i aplikacji opartych na kontenerach. Zazwyczaj masz różne zasobników, które potrzebują dostępu do tych samych informacji na zewnętrznym woluminie trwałym.    
Gdy pliki platformy Azure są opcją, tworzenie serwera NFS na maszynie Wirtualnej platformy Azure jest inną formą trwałego magazynu udostępnionego. 

W tym artykule pokazano, jak utworzyć serwer NFS na maszynie wirtualnej Ubuntu. A także dać kontenerom AKS dostęp do tego udostępnionego systemu plików.

## <a name="before-you-begin"></a>Przed rozpoczęciem
W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Klaster usługi AKS będzie musiał żyć w tych samych lub równorzędnych sieciach wirtualnych, co serwer systemu plików NFS. Klaster musi zostać utworzony w istniejącej sieci wirtualnej, która może być taka sama, jak maszyna wirtualna.

Kroki konfigurowania z istniejącą siecią wirtualną są opisane w dokumentacji: [tworzenie klastra AKS w istniejącej sieci wirtualnej][aks-virtual-network] i [łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej][peer-virtual-networks]

Zakłada się również, że utworzono maszynę wirtualną Ubuntu Linux (na przykład 18.04 LTS). Ustawienia i rozmiar mogą być według Twoich potrzeb i można je wdrożyć za pośrednictwem platformy Azure. Zobacz też: Szybki start systemu Linux w systemie [Linux][linux-create].

Jeśli najpierw wdrożysz klaster AKS, platforma Azure automatycznie wypełni pole sieci wirtualnej podczas wdrażania komputera Ubuntu, dzięki czemu będzie ona dostępna w tej samej sieci wirtualnej. Jeśli jednak chcesz pracować z sieciami równorzędnym, zapoznaj się z powyższą dokumentacją.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Wdrażanie serwera systemu plików NFS na maszynie wirtualnej
Oto skrypt do skonfigurowania serwera NFS w maszynie wirtualnej Ubuntu:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Serwer zostanie ponownie uruchomiony (ze względu na skrypt) i można zainstalować serwer NFS do AKS.

>[!IMPORTANT]  
>Pamiętaj, aby zastąpić **AKS_SUBNET** poprawną z klastra, w przeciwnym razie "*" otworzy serwer NFS do wszystkich portów i połączeń.

Po utworzeniu maszyny Wirtualnej skopiuj skrypt powyżej do pliku. Następnie można przenieść go z komputera lokalnego lub wszędzie tam, gdzie jest skrypt, do maszyny Wirtualnej przy użyciu: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Gdy skrypt znajduje się na maszynie wirtualnej, można ssh do maszyny Wirtualnej i wykonać go za pomocą polecenia:
```console
sudo ./nfs-server-setup.sh
```
Jeśli jego wykonanie nie powiedzie się z powodu błędu odmowy uprawnienia, ustaw uprawnienie do wykonania za pomocą polecenia:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Podłączanie klastra AKS do serwera NFS
Możemy połączyć serwer NFS z naszym klastrem, inicjując inicjowanie obsługi administracyjnej woluminu trwałego i oświadczenia woluminu trwałego, które określa sposób uzyskiwania dostępu do woluminu.

Konieczne jest połączenie dwóch usług w tej samej lub równorzędnej sieci wirtualnej. Instrukcje dotyczące konfigurowania klastra w tej samej sieci wirtualnej są tutaj: [Tworzenie klastra AKS w istniejącej sieci wirtualnej][aks-virtual-network]

Gdy znajdują się one w tej samej sieci wirtualnej (lub w trybie równorzędnym), należy aprowizować wolumin trwały i trwałe oświadczenie woluminu w klastrze AKS. Kontenery można następnie zainstalować dysk NFS do ich katalogu lokalnego.

Oto przykładowa definicja Kubernetes dla woluminu trwałego (Ta definicja zakłada, że klaster i maszyna wirtualna znajdują się w tej samej sieci wirtualnej):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Zastąp **NFS_INTERNAL_IP,** **NFS_NAME** i **NFS_EXPORT_FILE_PATH** informacjami o serwerze NFS.

Potrzebny jest również trwały plik oświadczenia woluminu. Oto przykład, co należy uwzględnić:

>[!IMPORTANT]  
>**"storageClassName"** musi pozostać pusty ciąg lub oświadczenie nie będzie działać.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli nie można połączyć się z serwerem z klastra, problem może być eksportowany katalog lub jego nadrzędny, nie ma wystarczających uprawnień dostępu do serwera.

Sprawdź, czy katalog eksportu i jego katalog nadrzędny mają uprawnienia 777.

Możesz sprawdzić uprawnienia, uruchamiając poniższe polecenie, a katalogi powinny mieć uprawnienia *'drwxrwxrwx':*
```console
ls -l
```

## <a name="more-information"></a>Więcej informacji
Aby uzyskać pełny instruktaż lub pomóc w debugowaniu konfiguracji serwera NFS, oto szczegółowy samouczek:
  - [Nfs - poradnik][nfs-tutorial]

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z skojarzonymi najlepszymi rozwiązaniami, zobacz [Najważniejsze wskazówki dotyczące przechowywania i tworzenia kopii zapasowych w u.][operator-best-practices-storage]

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
