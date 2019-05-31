---
title: Jak utworzyć klaster usługi Kubernetes, włączone dla usługi Azure Dev spacji, za pomocą usługi Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Dowiedz się, jak szybko utworzyć klaster usługi Kubernetes, włączone dla usługi Azure Dev miejsca do magazynowania bezpośrednio z przeglądarki, bez konieczności instalowania żadnych.
keywords: Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s
ms.openlocfilehash: 532a094f96817f3c525384694b6fa60a292aa948
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391673"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Tworzenie klastra Kubernetes za pomocą usługi Azure Cloud Shell

Możesz użyć [usługi Azure Cloud Shell](/azure/cloud-shell) do tworzenia klastra dla usługi Azure Dev miejsca do magazynowania przy użyciu **wypróbuj** przycisku z tej strony. Jeśli użytkownik nie jest zalogowany, postępuj zgodnie z monitami, aby zalogować się przy użyciu konta platformy Azure, a następnie wpisz polecenie w wierszu usługi Azure Cloud Shell polecenia, gdy się pojawi.

## <a name="create-the-cluster"></a>Tworzenie klastra

Najpierw utwórz grupę zasobów w [region, który obsługuje usługi Azure Dev miejsca do magazynowania][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Utwórz klaster Kubernetes za pomocą następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Utworzenie klastra trwa kilka minut.  Po ukończeniu dane wyjściowe jest wyświetlana w formacie JSON. Wyszukaj `provisioningState` i sprawdź, ma ona `Succeeded`.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [miejsca do magazynowania Azure Dev](/azure/dev-spaces/) linki do samouczków, pełne.


[supported-regions]: ../about.md#supported-regions-and-configurations