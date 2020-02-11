---
title: Korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać tożsamości zarządzanych w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121433"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Wersja zapoznawcza — używanie zarządzanych tożsamości w usłudze Azure Kubernetes Service

Obecnie klaster usługi Azure Kubernetes Service (AKS) (w odróżnieniu od dostawcy usług w chmurze Kubernetes) wymaga jednostki *usługi* , aby utworzyć dodatkowe zasoby, takie jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure. Należy podać nazwę główną usługi lub AKS, która jest tworzona w Twoim imieniu. Nazwy główne usług zazwyczaj mają datę wygaśnięcia. Klastry ostatecznie osiągną stan, w którym należy przeprowadzić odnowienie jednostki usługi w celu zachowania działania klastra. Zarządzanie jednostkami usługi zwiększa złożoność.

*Tożsamości zarządzane* są zasadniczo otoką dla podmiotów usługi i upraszczają zarządzanie nimi. Aby dowiedzieć się więcej, Przeczytaj o [zarządzanych tożsamościach dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS tworzy dwie zarządzane tożsamości:

- **Tożsamość zarządzana przypisana przez system**: tożsamość wykorzystywana przez dostawcę chmury Kubernetes do tworzenia zasobów platformy Azure w imieniu użytkownika. Cykl życia tożsamości przypisanej do systemu jest powiązany z tym klastrem. Tożsamość jest usuwana po usunięciu klastra.
- **Tożsamość zarządzana przypisana przez użytkownika**: tożsamość, która jest używana na potrzeby autoryzacji w klastrze. Na przykład tożsamość przypisana przez użytkownika służy do autoryzacji AKS do używania rekordów kontroli dostępu (rekordami ACR) lub do autoryzacji kubelet do uzyskiwania metadanych z platformy Azure.

W tym okresie korzystania z wersji zapoznawczej nadal wymagana jest jednostka usługi. Jest on używany do autoryzacji dodatków, takich jak monitorowanie, węzły wirtualne, Azure Policy i Routing aplikacji protokołu HTTP. Działa w trakcie usuwania zależności dodatków w głównej nazwie usługi (SPN). Ostatecznie wymóg nazwy SPN w AKS zostanie całkowicie usunięty.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej AKS są dostępne w ramach samoobsługowego i samodzielnego wyboru. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wykluczone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta na podstawie najlepszego wysiłku. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> - [Zasady pomocy technicznej AKS](support-policies.md)
> - [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.0.70 lub nowszej
- Rozszerzenie AKS-Preview 0.4.14

Aby zainstalować rozszerzenie AKS-Preview 0.4.14 lub nowsze, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej mogą być stosowane wartości domyślne dla wszystkich klastrów AKS utworzonych później w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Zamiast tego należy użyć oddzielnej subskrypcji do testowania funkcji w wersji zapoznawczej i zebrania opinii.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego**może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację dostawcy zasobów `Microsoft.ContainerService` przy użyciu polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Tworzenie klastra AKS z tożsamościami zarządzanymi

Można teraz utworzyć klaster AKS z tożsamościami zarządzanymi przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Następnie utwórz klaster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Na koniec Uzyskaj poświadczenia, aby uzyskać dostęp do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Klaster zostanie utworzony w ciągu kilku minut. Następnie można wdrożyć obciążenia aplikacji w nowym klastrze i korzystać z nich w taki sam sposób, jak w przypadku klastrów AKS opartych na głównej usłudze.

> [!IMPORTANT]
>
> - Klastry AKS z tożsamościami zarządzanymi można włączać tylko podczas tworzenia klastra.
> - Istniejących klastrów AKS nie można zaktualizować ani uaktualnić, aby umożliwić zarządzanie tożsamościami.
