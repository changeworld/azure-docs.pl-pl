---
title: Jak utworzyć klaster usługi Kubernetes, włączone dla usługi Azure Dev spacji, za pomocą usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 10/04/2018
ms.topic: article
description: Dowiedz się, jak szybko utworzyć klaster usługi Kubernetes, włączone dla usługi Azure Dev miejsca do magazynowania bezpośrednio z przeglądarki, bez konieczności instalowania żadnych.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: f10a84a602ce152d5c428525aa50f678b50c8b41
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48871689"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Tworzenie klastra Kubernetes za pomocą usługi Azure Cloud Shell

Możesz użyć [usługi Azure Cloud Shell](/azure/cloud-shell) do tworzenia klastra dla usługi Azure Dev miejsca do magazynowania przy użyciu **wypróbuj** przycisku z tej strony. Jeśli użytkownik nie jest zalogowany, postępuj zgodnie z monitami, aby zalogować się przy użyciu konta platformy Azure, a następnie wpisz polecenie w wierszu usługi Azure Cloud Shell polecenia, gdy się pojawi.

## <a name="create-the-cluster"></a>Tworzenie klastra

Najpierw utwórz grupę zasobów. Użyj jednej z aktualnie obsługiwanych regionach (EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral lub CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Tworzenie klastra Kubernetes za pomocą następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing
```

Trwa kilka minut na utworzenie klastra.  Po ukończeniu dane wyjściowe jest wyświetlana w formacie JSON. Wyszukaj `provisioningState` i sprawdź, ma ona `Succeeded`.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [miejsca do magazynowania Azure Dev](/azure/dev-spaces/) linki do samouczków, pełne.