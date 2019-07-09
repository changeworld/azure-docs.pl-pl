---
title: Samouczek — skalowanie klastra usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować klastra systemu Microsoft Azure Red Hat OpenShift przy użyciu wiersza polecenia platformy Azure
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: deb136a70c24cb7bd1b6c60505cc6ab0376a7b02
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672438"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Samouczek: Skalowanie klastra usługi Azure Red Hat OpenShift

Ten samouczek jest drugą częścią serii. Dowiesz się, jak utworzyć klaster systemu Microsoft Azure Red Hat OpenShift, za pomocą wiersza polecenia platformy Azure, przeskalujesz ją, a następnie usuń go, aby wyczyścić zasoby.

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skalowanie klastra w systemie Red Hat OpenShift

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * Skalowanie klastra usługi Azure Red Hat OpenShift
> * [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Utwórz klaster, postępując zgodnie z [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Logowanie do platformy Azure

Jeśli korzystasz z wiersza polecenia platformy Azure lokalnie, uruchom `az login` zalogować się do platformy Azure.

```bash
az login
```

Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` z subskrypcją, w której chcesz użyć.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Krok 2: Skalowanie klastra za pomocą dodatkowych węzłów

Z terminala powłoki Bash należy ustawić zmiennej nazwa_klastra na nazwę klastra:

```bash
CLUSTER_NAME=yourclustername
```

Teraz wykonamy teraz skalowanie klastra do pięciu węzłów przy użyciu wiersza polecenia platformy Azure:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Po kilku minutach `az openshift scale` zostanie ukończone pomyślnie i zwracają dokument JSON zawierającego szczegółowe informacje o skalowalny klaster.

## <a name="next-steps"></a>Kolejne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skalowanie klastra usługi Azure Red Hat OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)