---
title: Utwórz klaster Azure Kubernetes Service (AKS)
description: Tworzenie klastra AKS przy użyciu interfejsu wiersza polecenia lub witryny Azure portal.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5c133c61c989bf19be3e84287cb76a7d110dccc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440477"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Utwórz klaster Azure Kubernetes Service (AKS)

Klaster Azure Kubernetes Service (AKS) mogą być tworzone przy użyciu wiersza polecenia platformy Azure lub w portalu Azure.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [tworzenie az aks] [ az-aks-create] polecenie, aby utworzyć klaster usługi AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Poniższe opcje są dostępne za pomocą `az aks create` polecenia. Zobacz [odwołanie do wiersza polecenia platformy Azure] [ az-aks-create] dla usługi AKS, aby uzyskać więcej informacji dotyczących każdej z tych argumentów.

| Argument | Opis | Wymagane |
|---|---|:---:|
| `--name` `-n` | Nazwa zasobu zarządzanego klastra. | tak |
| `--resource-group` `-g` | Nazwa grupy zasobów w usłudze Azure Kubernetes Service. | tak |
| `--admin-username` `-u` | Nazwa użytkownika dla maszyn wirtualnych systemu Linux.  Domyślne: azureuser. | nie |
| `--aad-client-app-id` | (WERSJA ZAPOZNAWCZA) Identyfikator aplikacji klienckiej usługi Azure Active Directory typu "Native". | nie |
| `--aad-server-app-id` | (WERSJA ZAPOZNAWCZA) Identyfikator aplikacji serwera usługi Azure Active Directory typu "Aplikacja/interfejsu API sieci Web". | nie |
| `--aad-server-app-secret` | (WERSJA ZAPOZNAWCZA) Klucz tajny aplikacji serwera usługi Azure Active Directory. | nie |
| `--aad-tenant-id` | (WERSJA ZAPOZNAWCZA) Identyfikator dzierżawy usługi Azure Active Directory. | nie |
| `--admin-username` `-u` | Konto użytkownika do utworzenia w węźle maszyn wirtualnych dla dostępu SSH.  Domyślne: azureuser. | nie |
| ` --client-secret` | Wpis tajny skojarzony z jednostką usługi. | nie |
| `--dns-name-prefix` `-p` | Prefiks DNS dla klastrów publiczny adres ip. | nie |
| `--dns-service-ip` | Adres IP przypisany do usługi DNS platformy Kubernetes. | nie |
| `--docker-bridge-address` | Adres IP i maska sieci przypisane do mostka platformy Docker. | nie |
| `--enable-addons` `-a` | Włącz dodatków rozwiązania Kubernetes na liście rozdzielanej przecinkami. | nie |
| `--enable-rbac` `-r` | Włączanie kontroli dostępu opartej na rolach platformy Kubernetes. | nie |
| `--generate-ssh-keys` | Generuj publicznych i prywatnych kluczy pliki SSH, jeśli brak. | nie |
| `--kubernetes-version` `-k` | Wersja rozwiązania Kubernetes na potrzeby tworzenia klastra, takie jak "1.7.9" lub "pytanie 1.9.6". | nie |
| `--locaton` `-l` | Lokalizacja grupy zasobów utworzony automatycznie | nie |
| `--max-pods` `-m` | Maksymalna liczba zasobników można wdrożyć do węzła. | nie |
| `--network-plugin` | Wtyczki sieci Kubernetes do użycia. | nie |
| `--no-ssh-key` `-x` | Nie używać ani tworzenia lokalnego klucza SSH. | nie |
| `--no-wait` | Nie Czekaj na zakończenie operacji długotrwałych. | nie |
| `--node-count` `-c` | Domyślna liczba węzłów dla pule węzłów.  Domyślne: 3. | nie |
| `--node-osdisk-size` | OsDisk rozmiar w GB puli węzłów maszyny wirtualnej. | nie |
| `--node-vm-size` `-s` | Rozmiar maszyny wirtualnej.  Domyślne: maszyna wirtualna Standard_D1_v2. | nie |
| `--pod-cidr` | Zakres adresów IP notacji CIDR od którego ma zostać przypisany pod adresy IP w przypadku używania wtyczki kubenet. | nie |
| `--service-cidr` | Zakres adresów IP notacji CIDR od którego ma zostać przypisany do klastra usługi adresów IP. | nie |
| `--service-principal` | Jednostki usługi używany do uwierzytelniania w klastrze. | nie |
| `--ssh-key-value` | SSH pliku klucza wartość lub klucz ścieżka pliku.  Wartość domyślna: ~ /.ssh/id_rsa.pub. | nie |
| `--tags` | Spacjami tagów w [= value] klucz formatu. Użyj "Wyczyść istniejące tagi. | nie |
| `--vnet-subnet-id` | Identyfikator podsieci w istniejącej sieci wirtualnej, do której ma zostać wdrożony do klastra. | nie |
| `--workspace-resource-id` | Identyfikator zasobu istniejącego obszaru roboczego analizy dzienników na potrzeby przechowywania danych monitorowania. | nie |

## <a name="azure-portal"></a>Azure Portal

Aby uzyskać instrukcje dotyczące wdrażania klastra AKS w witrynie Azure portal, zobacz Azure Kubernetes Service (AKS) [szybkiego startu portalu platformy Azure][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
