---
title: SSH w węzłach klastra usługi Kubernetes Azure (AKS)
description: Utwórz połączenie SSH z klastrem usługi Kubernetes Azure (AKS) węzły
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c2b77e558db0e323370c24b87a75357235677f7e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-into-azure-kubernetes-service-aks-cluster-nodes"></a>SSH w węzłach klastra usługi Kubernetes Azure (AKS)

Czasami może być konieczne dostępu węzła usługi Kubernetes Azure (AKS) konserwacji, zbierania dzienników lub inne operacje dotyczące rozwiązywania problemów. Azure Service Kubernetes (AKS) węzły nie są widoczne w Internecie. Wykonaj kroki szczegółowo opisane w tym dokumencie, aby utworzyć połączenie SSH za pomocą węzła AKS.

## <a name="get-aks-node-address"></a>Pobierz adres węzła AKS

Uzyskaj adres IP AKS klastra węzła przy użyciu `az vm list-ip-addresses` polecenia. Zastąp nazwę grupy zasobów o nazwie AKS grupie zasobów.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Utwórz połączenie SSH

Uruchom `debian` kontener obrazu i Dołącz do niej sesję terminala. Aby utworzyć sesję SSH za pomocą dowolnego węzła w klastrze AKS można następnie kontenera.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Zainstaluj klienta SSH w kontenerze.

```console
apt-get update && apt-get install openssh-client -y
```

Lista wszystkich stanowiskami można uzyskać nazwy nowo utworzony pod i otwórz terminal drugiego.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Skopiuj klucz SSH do pod, zastąp nazwę pod poprawnej wartości.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Aktualizacja `id_rsa` pliku tak, aby użytkownika tylko do odczytu.

```console
chmod 0600 id_rsa
```

Teraz utworzyć połączenie SSH do węzła AKS. Domyślna nazwa użytkownika dla klastra AKS to `azureuser`. Jeśli to konto zostało zmienione w czasie tworzenia klastra, zamiast nazwy użytkownika administratora właściwe.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Usuń dostęp SSH

Po zakończeniu zamknij sesji SSH, a następnie sesji interakcyjne kontenera. Ta akcja spowoduje usunięcie pod używane dla dostępu SSH z AKS klastra.