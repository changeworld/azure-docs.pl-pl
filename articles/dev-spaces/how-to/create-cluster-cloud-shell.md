---
title: Jak utworzyć klaster Kubernetes z włączoną obsługą Azure Dev Spaces przy użyciu Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Dowiedz się, jak szybko utworzyć klaster Kubernetes z włączoną funkcją Azure Dev Spaces bezpośrednio z przeglądarki bez konieczności instalowania żadnych elementów.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
ms.openlocfilehash: dbdc9226e417b3142284386ae3586819cda802d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280112"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Tworzenie klastra Kubernetes za pomocą usługi Azure Cloud Shell

Możesz użyć [Azure Cloud Shell](/azure/cloud-shell) , aby utworzyć klaster usługi Azure Kubernetes, używając przycisku **Wypróbuj** na tej stronie. Jeśli użytkownik nie jest zalogowany, postępuj zgodnie z monitami, aby zalogować się przy użyciu konta platformy Azure, a następnie wpisz polecenia w wierszu Azure Cloud Shell, gdy zostanie wyświetlony.

## <a name="create-the-cluster"></a>Tworzenie klastra

Najpierw utwórz grupę zasobów w [regionie, który obsługuje Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Utwórz klaster Kubernetes za pomocą następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Utworzenie klastra trwa kilka minut.  Po ukończeniu dane wyjściowe są wyświetlane w formacie JSON. Poszukaj `provisioningState` i sprawdź, czy jest `Succeeded`.

## <a name="next-steps"></a>Następne kroki

Zobacz [Azure dev Spaces](/azure/dev-spaces/) , aby uzyskać linki do pełnych samouczków.

> [!IMPORTANT]
> Wiele przewodników Szybki Start i samouczków Azure Dev Spaces korzysta z interfejsu wiersza polecenia Azure Dev Spaces do wykonywania operacji. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations