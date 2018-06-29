---
title: Tworzenie klastra usługi Kubernetes Azure (AKS)
description: Tworzenie klastra AKS z poziomu interfejsu wiersza polecenia lub w portalu Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 684bf44c6c3c67ce1d5c798f3406270d76a8e2fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029236"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Tworzenie klastra usługi Kubernetes Azure (AKS)

Klaster usługi Azure Service Kubernetes (AKS) mogą być tworzone z wiersza polecenia platformy Azure lub w portalu Azure.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [utworzyć az aks] [ az-aks-create] polecenie, aby utworzyć klaster AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Poniższe opcje są dostępne z `az aks create` polecenia. Zobacz [odwołanie do wiersza polecenia platformy Azure] [ az-aks-create] dla AKS, aby uzyskać więcej informacji na temat każdego z tych argumentów.

| Argument | Opis | Wymagane |
|---|---|:---:|
| `--name` `-n` | Nazwa zasobu zarządzanego klastra. | tak |
| `--resource-group` `-g` | Nazwa grupy zasobów Azure Kubernetes usługi. | tak |
| `--admin-username` `-u` | Nazwa użytkownika dla maszyn wirtualnych systemu Linux.  Domyślne: azureuser. | nie |
| `--aad-client-app-id` | (WERSJA ZAPOZNAWCZA) Identyfikator klienta aplikacji usługi Azure Active Directory typu "Native". | nie |
| `--aad-server-app-id` | (WERSJA ZAPOZNAWCZA) Identyfikator aplikacji usługi Azure Active Directory serwera typu "Aplikacja/interfejs API sieci Web". | nie |
| `--aad-server-app-secret` | (WERSJA ZAPOZNAWCZA) Klucz tajny aplikacji serwera usługi Azure Active Directory. | nie |
| `--aad-tenant-id` | (WERSJA ZAPOZNAWCZA) Identyfikator dzierżawy usługi Azure Active Directory. | nie |
| `--admin-username` `-u` | Konto użytkownika, można utworzyć w węźle maszyn wirtualnych dla dostępu SSH.  Domyślne: azureuser. | nie |
| ` --client-secret` | Wpis tajny skojarzony z jednostką usługi. | nie |
| `--dns-name-prefix` `-p` | Prefiks DNS dla adresu publicznego adresu ip klastrów. | nie |
| `--dns-service-ip` | Adres IP przypisanego do usługi Kubernetes DNS. | nie |
| `--docker-bridge-address` | Adres IP i maski podsieci przypisane do mostka Docker. | nie |
| `--enable-addons` `-a` | Włączenie dodatki Kubernetes w postaci listy rozdzielanej przecinkami. | nie |
| `--enable-rbac` `-r` | Włącz Kubernetes kontroli dostępu opartej na rolach. | nie |
| `--generate-ssh-keys` | Generuj SSH publicznego i prywatnego klucza plików, jeśli brak. | nie |
| `--kubernetes-version` `-k` | Wersja Kubernetes do użycia podczas tworzenia klastra, takie jak "1.7.9" lub "pytanie 1.9.6". | nie |
| `--locaton` `-l` | Lokalizacja grupy zasobów utworzonych automatycznie | nie |
| `--max-pods` `-m` | Maksymalna liczba stanowiskami można wdrożyć do węzła. | nie |
| `--network-plugin` | Wtyczki sieci Kubernetes do użycia. | nie |
| `--no-ssh-key` `-x` | Nie używać lub utworzyć lokalnego klucza SSH. | nie |
| `--no-wait` | Nie należy czekać na zakończenie operacji długotrwałych. | nie |
| `--node-count` `-c` | Domyślna liczba węzłów dla pul węzła.  Domyślne: 3. | nie |
| `--node-osdisk-size` | Parametr osDisk rozmiar w GB puli węzła maszyny wirtualnej. | nie |
| `--node-vm-size` `-s` | Rozmiar maszyny wirtualnej.  Domyślne: Standard_D1_v2. | nie |
| `--pod-cidr` | Zakres IP notacji CIDR z którego ma zostać przypisany pod adresy IP, gdy kubenet jest używany. | nie |
| `--service-cidr` | Zakres IP notacji CIDR z którego ma zostać przypisany do klastra usługi adresów IP. | nie |
| `--service-principal` | Nazwy głównej usługi używany do uwierzytelniania klastra. | nie |
| `--ssh-key-value` | SSH pliku klucza wartość lub klucz ścieżka pliku.  Wartość domyślna: ~ /.ssh/id_rsa.pub. | nie |
| `--tags` | Tagi [= value] klucza spacjami format. Użyj "Wyczyść istniejące znaczniki. | nie |
| `--vnet-subnet-id` | Identyfikator podsieci w istniejącej sieci wirtualnej, w której chcesz wdrożyć w klastrze. | nie |
| `--workspace-resource-id` | Identyfikator zasobu istniejący obszar roboczy analizy dzienników do przechowywania danych monitorowania. | nie |

## <a name="azure-portal"></a>Azure Portal

Aby uzyskać instrukcje dotyczące wdrażania klastra AKS z portalu Azure, zobacz usługi Kubernetes Azure (AKS) [Azure Szybki Start portalu][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
