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
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808691"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Tworzenie klastra Kubernetes za pomocą usługi Azure Cloud Shell

Możesz użyć [usługi Azure Cloud Shell](/azure/cloud-shell) do utworzenia klastra usługi Azure Kubernetes Service za pomocą **wypróbuj** przycisku z tej strony. Jeśli użytkownik nie jest zalogowany, postępuj zgodnie z monitami, aby zalogować się przy użyciu konta platformy Azure, a następnie wpisz polecenie w wierszu usługi Azure Cloud Shell polecenia, gdy się pojawi.

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

> [!IMPORTANT]
> Wiele miejsc do magazynowania Azure Dev przewodników Szybki Start i samouczków korzystanie z wiersza polecenia platformy Azure Dev miejsca do magazynowania w celu wykonywania operacji. Nie można zainstalować interfejs wiersza polecenia Azure Dev miejsca do magazynowania w usłudze Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations