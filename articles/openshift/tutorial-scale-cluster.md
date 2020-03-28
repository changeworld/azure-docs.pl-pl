---
title: Samouczek — skalowanie klastra OpenShift red hat
description: Dowiedz się, jak skalować klaster OpenShift Red Hat platformy Microsoft Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c6334aa20b543dfbf87fedcfe45d54bbcf7a219a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477021"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Samouczek: Skalowanie klastra OpenShift programu Azure Red Hat

Ten samouczek jest drugą częścią serii. Dowiesz się, jak utworzyć klaster OpenShift Red Hat platformy Microsoft Azure przy użyciu interfejsu wiersza polecenia platformy Azure, skalować go, a następnie usunąć, aby oczyścić zasoby.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skalowanie klastra OpenShift Red Hat

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Skalowanie klastra usługi Azure Red Hat OpenShift
> * [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Utwórz klaster, wykonując samouczek [tworzenia klastra OpenShift red hat.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Zaloguj się na platformie Azure

Jeśli korzystasz z interfejsu wiersza polecenia `az login` platformy Azure lokalnie, uruchom, aby zalogować się na platformie Azure.

```azurecli
az login
```

Jeśli masz dostęp do wielu `az account set -s {subscription ID}` subskrypcji, uruchom zastąpienie `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Krok 2: Skalowanie klastra za pomocą dodatkowych węzłów

W terminalu Bash ustaw zmienną CLUSTER_NAME na nazwę klastra:

```bash
CLUSTER_NAME=yourclustername
```

Teraz skalujmy klaster do pięciu węzłów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Po kilku minutach `az openshift scale` zakończy się pomyślnie i zwróci dokument JSON zawierający skalowane szczegóły klastra.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skalowanie klastra usługi Azure Red Hat OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)
