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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827539"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Wersja zapoznawcza — używanie zarządzanych tożsamości w usłudze Azure Kubernetes Service

Obecnie użytkownicy muszą podać nazwę główną usługi lub AKS w Twoim imieniu w celu utworzenia dodatkowych zasobów, takich jak moduły równoważenia obciążenia i dyski zarządzane na platformie Azure. Nazwy główne usług są zwykle tworzone z datą wygaśnięcia. Klastry będą ostatecznie dotrzeć do stanu, w którym należy odnowić jednostkę usługi w przeciwnym razie klaster nie będzie działać. Zarządzanie jednostkami usługi zwiększa złożoność. Tożsamości zarządzane są zasadniczo otoką dla podmiotów usługi i upraszczają zarządzanie nimi. Przeczytaj więcej na temat [zarządzanych tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .

AKS tworzy dwie zarządzane tożsamości jednej tożsamości zarządzanej przypisanej przez system i drugą tożsamość przypisanej przez użytkownika. Tożsamość zarządzana przypisana przez system jest używana przez dostawcę chmury Kubernetes do tworzenia zasobów platformy Azure w imieniu użytkownika. Cykl życia tego przypisanej tożsamości zarządzanej jest powiązany z tym klastrem i jest usuwany po usunięciu klastra. AKS tworzy również tożsamość zarządzaną przypisaną przez użytkownika, która jest używana w klastrze do autoryzacji AKS do uzyskiwania dostępu do rekordami ACR, kubelet w celu uzyskania metadanych z platformy Azure itd.

W tym okresie korzystania z wersji zapoznawczej nadal wymagana jest jednostka usługi. Będzie on używany do autoryzacji dodatków, takich jak monitorowanie, węzeł wirtualny, zasady platformy Azure i Routing aplikacji protokołu HTTP. Jest to konieczne, aby usunąć zależność dodatków do nazw SPN, a ostatecznie żądanie głównej nazwy SPN w programie AKS zostanie całkowicie usunięte.

> [!IMPORTANT]
> Funkcja AKS w wersji zapoznawczej to samoobsługowe uczestnictwo. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o najlepszy nakład pracy. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby dowiedzieć się więcej, zobacz następujące artykuły pomocy technicznej:
>
> * [Zasady pomocy technicznej AKS](support-policies.md)
> * [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagane są następujące elementy:

* Potrzebujesz także interfejsu wiersza polecenia platformy Azure w wersji 2.0.70 lub nowszej oraz rozszerzenia AKS-Preview 0.4.14

## <a name="install-latest-aks-cli-preview-extension"></a>Zainstaluj rozszerzenie najnowszej wersji zapoznawczej interfejsu wiersza polecenia AKS

Potrzebujesz rozszerzenia **AKS-Preview 0.4.14** lub nowszego.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej można użyć wartości domyślnych dla wszystkich klastrów AKS utworzonych w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Korzystaj z oddzielnej subskrypcji, aby testować funkcje w wersji zapoznawczej i zbierać opinie.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Wyświetlenie *zarejestrowanego*stanu może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list] [AZ-Feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Po zarejestrowaniu stanu Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register] [AZ-Provider-Register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Tworzenie klastra AKS z tożsamością zarządzaną

Teraz można utworzyć klaster AKS z tożsamościami zarządzanymi przy użyciu poniższego polecenia interfejsu CLI
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Uzyskiwanie poświadczeń w celu uzyskania dostępu do klastra
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Po utworzeniu klastra w ciągu kilku minut można wdrożyć obciążenia aplikacji i współdziałać z nim, tak jak w przypadku klastrów AKS opartych na jednostkach usługi. 

> [!IMPORTANT]
> * Klastry AKS z tożsamościami zarządzanymi można włączać tylko podczas tworzenia klastra
> * Nie można zaktualizować/uaktualnić istniejących klastrów AKS, aby umożliwić zarządzanie tożsamościami