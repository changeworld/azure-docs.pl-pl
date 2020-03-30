---
title: Używanie tożsamości zarządzanych w usłudze Azure Kubernetes
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369965"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Używanie tożsamości zarządzanych w usłudze Azure Kubernetes

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w szczególności dostawca chmury Kubernetes) wymaga *jednostki usługi* do tworzenia dodatkowych zasobów, takich jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure. Musisz podać jednostkę usługi lub AKS tworzy jeden w Twoim imieniu. Jednostki usługi zazwyczaj mają datę wygaśnięcia. Klastry po pewnym czasie osiągną stan, w którym podmiot zabezpieczeń usługi musi zostać odnowiony, aby klaster działał. Zarządzanie jednostkami usługi zwiększa złożoność.

*Tożsamości zarządzane* są zasadniczo otoki wokół podmiotów usługi i uczynić ich zarządzanie prostsze. Aby dowiedzieć się więcej, przeczytaj o [tożsamościach zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Usługa AKS tworzy dwie tożsamości zarządzane:

- **Tożsamość zarządzana przypisana do systemu:** tożsamość używana przez dostawcę chmury Kubernetes do tworzenia zasobów platformy Azure w imieniu użytkownika. Cykl życia tożsamości przypisanej do systemu jest powiązany z cyklem życia klastra. Tożsamość jest usuwana po usunięciu klastra.
- **Tożsamość zarządzana przypisana przez użytkownika:** tożsamość używana do autoryzacji w klastrze. Na przykład tożsamość przypisana przez użytkownika jest używana do autoryzowania usługi AKS do używania rejestratorów kontenerów platformy Azure (ARS) lub do autoryzowania kubelet w celu uzyskania metadanych z platformy Azure.

Dodatki są również uwierzytelniane przy użyciu tożsamości zarządzanej. Dla każdego dodatku zarządzana tożsamość jest tworzona przez usługi AKS i trwa przez cały okres ważności dodatku. Aby utworzyć własną sieć wirtualną, statyczny adres IP lub dołączony dysk platformy Azure, na którym zasoby znajdują się poza grupą zasobów MC_*, użyj identyfikatora głównego klastra, aby wykonać przypisanie roli. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Delegowanie dostępu do innych zasobów platformy Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Musi być zainstalowany następujący zasób:

- Interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra AKS z tożsamościami zarządzanymi

Teraz można utworzyć klaster AKS z zarządzanymi tożsamościami przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Następnie utwórz klaster usługi AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Pomyślne utworzenie klastra przy użyciu tożsamości zarządzanych zawiera informacje o profilu jednostki usługi:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Na koniec uzyskaj poświadczenia dostępu do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klaster zostanie utworzony w ciągu kilku minut. Następnie można wdrożyć obciążenia aplikacji do nowego klastra i współdziałać z nim tak samo, jak to zrobić z klastrów AKS opartych na jednostkach usług.

> [!IMPORTANT]
>
> - Klastry AKS z tożsamościami zarządzanymi można włączyć tylko podczas tworzenia klastra.
> - Istniejących klastrów AKS nie można zaktualizować ani uaktualnić, aby włączyć tożsamości zarządzane.
