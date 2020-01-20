---
title: Samouczek — skalowanie klastra usługi Azure Red Hat OpenShift
description: Dowiedz się, jak skalować Microsoft Azure klastrze Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278305"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Samouczek: skalowanie klastra usługi Azure Red Hat OpenShift

Ten samouczek jest drugą częścią serii. Dowiesz się, jak utworzyć Microsoft Azure klaster Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure, skalować go, a następnie usunąć, aby wyczyścić zasoby.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skalowanie klastra z systemem Red Hat OpenShift

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Skalowanie klastra usługi Azure Red Hat OpenShift
> * [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Utwórz klaster, wykonując czynności opisane w samouczku [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="step-1-sign-in-to-azure"></a>Krok 1. Logowanie do platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom `az login`, aby zalogować się do platformy Azure.

```bash
az login
```

Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Krok 2: skalowanie klastra z dodatkowymi węzłami

W terminalu bash Ustaw zmienną CLUSTER_NAME na nazwę klastra:

```bash
CLUSTER_NAME=yourclustername
```

Teraz można skalować klaster do pięciu węzłów przy użyciu interfejsu wiersza polecenia platformy Azure:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Po kilku minutach `az openshift scale` zakończy się pomyślnie i zwróci dokument JSON zawierający szczegóły skalowanego klastra.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skalowanie klastra usługi Azure Red Hat OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)
