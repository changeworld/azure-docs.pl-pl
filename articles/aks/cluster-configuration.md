---
title: Konfiguracja klastra w usługach Kubernetes platformy Azure (AKS)
description: Dowiedz się, jak skonfigurować klaster w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479165"
---
# <a name="configure-an-aks-cluster"></a>Konfigurowanie klastra usługi AKS

W ramach tworzenia klastra AKS może być konieczne dostosowanie konfiguracji klastra do własnych potrzeb. W tym artykule przedstawiono kilka opcji dostosowywania klastra AKS.

## <a name="os-configuration-preview"></a>Konfiguracja systemu operacyjnego (wersja zapoznawcza)

AKS obsługuje teraz Ubuntu 18.04 jako system operacyjny węzła (OS) w wersji zapoznawczej. W okresie podglądu dostępne są zarówno Ubuntu 16.04, jak i Ubuntu 18.04.

Muszą być zainstalowane następujące zasoby:

- Interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej
- Rozszerzenie aks-preview 0.4.35

Aby zainstalować rozszerzenie aks-preview 0.4.35 lub nowsze, należy użyć następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Zarejestruj `UseCustomizedUbuntuPreview` funkcję:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Może upłynąć kilka minut, aby stan był pokazywał jako **zarejestrowany**. Stan rejestracji można sprawdzić za pomocą polecenia [az feature list:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowany, `Microsoft.ContainerService` odśwież rejestrację dostawcy zasobów za pomocą polecenia [az provider register:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Skonfiguruj klaster do używania Ubuntu 18.04 podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18.04 jako domyślny system operacyjny.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć zwykły klaster Ubuntu 16.04, możesz to zrobić, pomijając niestandardowy `--aks-custom-headers` tag.

## <a name="custom-resource-group-name"></a>Niestandardowa nazwa grupy zasobów

Po wdrożeniu klastra usługi Azure Kubernetes na platformie Azure zostanie utworzona druga grupa zasobów dla węzłów procesu roboczego. Domyślnie program AKS będzie nazywał grupę `MC_resourcegroupname_clustername_location`zasobów węzła, ale można również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji 0.3.2 lub nowszej w wersji aks-preview Azure. Za pomocą interfejsu wiersza polecenia platformy Azure użyj `--node-resource-group` parametru `az aks create` polecenia, aby określić niestandardową nazwę dla grupy zasobów. Jeśli używasz szablonu usługi Azure Resource Manager do wdrażania klastra AKS, można zdefiniować nazwę grupy zasobów przy użyciu `nodeResourceGroup` właściwości.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Pomocnicza grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure w ramach własnej subskrypcji. Należy zauważyć, że można określić nazwę niestandardowej grupy zasobów tylko podczas tworzenia klastra. 

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

- Określ istniejącą grupę zasobów dla grupy zasobów węzła.
- Określ inną subskrypcję dla grupy zasobów węzła.
- Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
- Określ nazwy zasobów zarządzanych w grupie zasobów węzła.
- Modyfikowanie lub usuwanie znaczników utworzonych przez platformę Azure zasobów zarządzanych w grupie zasobów węzła.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, `Kured` jak używać aktualizacji [zabezpieczeń i jąder do węzłów systemu Linux](node-updates-kured.md) w klastrze.
- Zobacz [Uaktualnianie klastra usługi Azure Kubernetes (AKS),](upgrade-cluster.md) aby dowiedzieć się, jak uaktualnić klaster do najnowszej wersji usługi Kubernetes.
- Zobacz listę [często zadawanych pytań dotyczących aks,](faq.md) aby znaleźć odpowiedzi na niektóre typowe pytania AKS.