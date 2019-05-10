---
title: Utwórz serwer systemu plików NFS (systemu plików NFS) Ubuntu do użycia przez zasobników Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin systemu plików NFS Ubuntu Linux Server do użytku z zasobników w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468502"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Ręcznie utworzyć i korzystać z woluminu systemu plików NFS (systemu plików NFS) Linux Server za pomocą usługi Azure Kubernetes Service (AKS)
Udostępnianie danych między kontenerów jest często niezbędnych składników usług opartych na kontenerach i aplikacji. Zazwyczaj mają różne zasobników, którzy potrzebują dostępu do tych samych informacji w zewnętrznych trwały wolumin.    
Usługa Azure files jest to możliwe, tworzenia serwera systemu plików NFS na Maszynie wirtualnej platformy Azure jest inna forma trwałego magazynu udostępnionego. 

W tym artykule pokazano sposób tworzenia serwera systemu plików NFS na maszynie wirtualnej systemu Ubuntu. A także zapewnij dostęp kontenerów AKS do tego udostępnionego systemu plików.

## <a name="before-you-begin"></a>Przed rozpoczęciem
W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Klaster AKS musi znajdować się w tej samej lub równorzędnej sieci wirtualnych jako serwer systemu plików NFS. Klaster musi zostać utworzona w istniejącej sieci Wirtualnej, który może być tej samej sieci Wirtualnej jako maszyny Wirtualnej.

Procedura konfigurowania za pomocą istniejącej sieci Wirtualnej są opisane w dokumentacji: [Tworzenie klastra AKS w istniejącej sieci Wirtualnej] [ aks-virtual-network] i [łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci Wirtualnej][peer-virtual-networks]

Przyjęto również założenie, że utworzono maszyny wirtualnej systemu Linux Ubuntu (na przykład 18.04 LTS). Ustawienia rozmiaru może być do swoich potrzeb i można wdrażać za pośrednictwem platformy Azure. Linux — Szybki Start można zobaczyć [zarządzania maszyną Wirtualną systemu linux][linux-create].

W przypadku wdrożenia klastra usługi AKS najpierw, Azure zostaną wypełnione automatycznie pola sieci wirtualnej podczas wdrażania na maszynie z systemem Ubuntu, dzięki czemu na żywo w ramach tej samej sieci Wirtualnej. Ale jeśli chcesz pracować w sieciach równorzędnych zamiast tego zapoznaj się z dokumentacją powyżej.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Wdrażanie serwera systemu plików NFS na maszynie wirtualnej
Oto skrypt, aby skonfigurować serwer NFS maszynie wirtualnej systemu Ubuntu:
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
Serwer zostanie ponownie uruchomiony (ze względu na skrypt) i można zainstalować serwer systemu plików NFS w usłudze AKS

>[!IMPORTANT]  
>Upewnij się zastąpić **AKS_SUBNET** poprawne plikiem z klastra lub "*" spowoduje otwarcie serwer systemu plików NFS do wszystkich portów i połączeń.

Po utworzeniu maszyny Wirtualnej, skopiuj skrypt powyżej do pliku. Następnie można go przenieść, korzystając z komputera lokalnego lub wszędzie tam, gdzie skrypt jest do maszyny Wirtualnej przy użyciu: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Po umieszczeniu skryptu na maszynie wirtualnej możesz ssh z maszyną wirtualną i uruchomić go za pomocą polecenia:
```console
sudo ./nfs-server-setup.sh
```
Jeśli jej wykonanie nie powiedzie się ze względu na błąd odmowy uprawnień, należy ustawić uprawnień wykonywanie za pomocą polecenia:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Połączenie klastra AKS, serwer systemu plików NFS
Możemy połączyć serwer NFS do klastra, aprowizując trwały wolumin i oświadczenia trwały wolumin, który określa sposób dostępu do woluminu.  
Niezbędne jest połączenie obydwu usług w tej samej lub równorzędnej sieci wirtualnych. Instrukcje dotyczące konfigurowania klastra w tej samej sieci Wirtualnej są w tym miejscu: [Tworzenie klastra AKS w istniejącej sieci Wirtualnej][aks-virtual-network]

Po ich znajdują się w tej samej sieci wirtualnej (lub równorzędnej), należy aprowizować, które trwały wolumin i trwały wolumin oświadczenia w klastrze AKS. Kontenery mogą następnie zainstalować dysku systemu plików NFS do swojego katalogu lokalnego.

Oto przykład definicji kubernetes trwały wolumin (Ta definicja przyjęto założenie, że Twoje klastrów i maszyn wirtualnych znajdują się w tej samej sieci Wirtualnej):

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
Zastąp **NFS_INTERNAL_IP**, **NFS_NAME** i **NFS_EXPORT_FILE_PATH** informacje o serwerze systemu plików NFS.

Należy także plik oświadczenia trwały wolumin. Oto przykład tego, co do uwzględnienia:

>[!IMPORTANT]  
>**"storageClassName"** musi pozostać pusty ciąg lub oświadczenia nie będą działać.

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
Jeśli nie możesz połączyć z serwerem z klastra, problem może być eksportowanego katalogu lub jego element nadrzędny, nie ma wystarczających uprawnień dostępu do serwera.

Upewnij się, że zarówno w katalogu eksportu, jak i w katalogu nadrzędnym ma 777 uprawnień.

Sprawdź uprawnienia, uruchamiając poniższe polecenie, a powinien mieć katalogi *"drwxrwxrwx"* uprawnienia:
```console
ls -l
```

## <a name="more-information"></a>Więcej informacji
Aby uzyskać pełny przewodnik lub ułatwienia debugowania konfiguracji serwera NFS, poniżej przedstawiono szczegółowy samouczek:
  - [Samouczek systemu plików NFS][nfs-tutorial]

## <a name="next-steps"></a>Kolejne kroki

Najlepsze rozwiązania dotyczące skojarzone, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze AKS][operator-best-practices-storage].

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
