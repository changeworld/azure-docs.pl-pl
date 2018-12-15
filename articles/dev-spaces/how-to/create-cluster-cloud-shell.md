---
title: Jak utworzyć klaster usługi Kubernetes, włączone dla usługi Azure Dev spacji, za pomocą usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: article
description: Dowiedz się, jak szybko utworzyć klaster usługi Kubernetes, włączone dla usługi Azure Dev miejsca do magazynowania bezpośrednio z przeglądarki, bez konieczności instalowania żadnych.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 531781ec48617d2038698665696fdc61b3c52cd9
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413544"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Tworzenie klastra Kubernetes za pomocą usługi Azure Cloud Shell

Możesz użyć [usługi Azure Cloud Shell](/azure/cloud-shell) do tworzenia klastra dla usługi Azure Dev miejsca do magazynowania przy użyciu **wypróbuj** przycisku z tej strony. Jeśli użytkownik nie jest zalogowany, postępuj zgodnie z monitami, aby zalogować się przy użyciu konta platformy Azure, a następnie wpisz polecenie w wierszu usługi Azure Cloud Shell polecenia, gdy się pojawi.

## <a name="create-the-cluster"></a>Tworzenie klastra

Najpierw utwórz grupę zasobów. Użyj jednego z aktualnie obsługiwanych regionów (EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral lub CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Utwórz klaster Kubernetes za pomocą następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing
```

Utworzenie klastra trwa kilka minut.  Po ukończeniu dane wyjściowe jest wyświetlana w formacie JSON. Wyszukaj `provisioningState` i sprawdź, ma ona `Succeeded`.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [miejsca do magazynowania Azure Dev](/azure/dev-spaces/) linki do samouczków, pełne.
