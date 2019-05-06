---
title: Samouczek — skalowanie klastra usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować klastra systemu Microsoft Azure Red Hat OpenShift przy użyciu wiersza polecenia platformy Azure
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: f86b60fbe20fc630863d6e177d45f2c1f06a7863
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080702"
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