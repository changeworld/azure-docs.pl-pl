---
title: Tworzenie klastra usługi Kubernetes z włączoną funkcją Azure Dev Spaces — usługa Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes włączony dla usługi Azure Dev Spaces bezpośrednio z przeglądarki bez instalowania czegokolwiek.
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605307"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Tworzenie klastra usługi Kubernetes z włączoną usługą Azure Dev Spaces przy załączeniu usługi Azure Cloud Shell

Za pomocą [usługi Azure Cloud Shell](/azure/cloud-shell) można utworzyć klaster usługi Azure Kubernetes przy użyciu przycisku **Wypróbuj** na tej stronie. Jeśli nie jesteś zalogowany, postępuj zgodnie z monitami, aby zalogować się za pomocą konta platformy Azure, a następnie wpisz polecenia w wierszu usługi Azure Cloud Shell, gdy się pojawi.

## <a name="create-the-cluster"></a>Tworzenie klastra

Najpierw utwórz grupę zasobów w [regionie obsługującym usługi Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Utwórz klaster Kubernetes za pomocą następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Utworzenie klastra trwa kilka minut.  Po zakończeniu dane wyjściowe są wyświetlane w formacie JSON. Poszukaj `provisioningState` i `Succeeded`sprawdź, czy jest .

## <a name="next-steps"></a>Następne kroki

Zobacz [usługi Azure Dev Spaces,](/azure/dev-spaces/) aby uzyskać łącza do pełnych samouczków.

> [!IMPORTANT]
> Wiele przewodników Szybki start i samouczków usługi Azure Dev Spaces używa interfejsu wiersza polecenia azure dev spaces do wykonywania operacji. Nie można zainstalować interfejsu wiersza polecenia usługi Azure Dev Spaces w usłudze Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service