---
title: Samouczek — Usuwanie klastra usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak usunąć klaster usługi Azure Red Hat OpenShift przy użyciu wiersza polecenia platformy Azure
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: ce4142bdcdfb7a9ab687bb60dca91d6aab00c7bd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080716"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Samouczek: Usuwanie klastra usługi Azure Red Hat OpenShift

To jest koniec samouczka. Po zakończeniu testowania klastra przykład poniżej przedstawiono sposób usunięcie i skojarzone z nią zasoby, dzięki czemu nie jest naliczana za co nie jest używany.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * [Skalowanie klastra usługi Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * Usuwanie klastra usługi Azure Red Hat OpenShift

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Utwórz klaster, postępując zgodnie z [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Logowanie do platformy Azure

Jeśli korzystasz z wiersza polecenia platformy Azure lokalnie, uruchom `az login` zalogować się do platformy Azure.

```bash
az login
```

Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` z subskrypcją, w której chcesz użyć.

## <a name="step-2-delete-the-cluster"></a>Krok 2: Usuwanie klastra

Otwórz terminal powłoki Bash i ustaw zmienną nazwa_klastra nazwę klastra:

```bash
CLUSTER_NAME=yourclustername
```

Teraz usunąć klaster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Zostanie wyświetlony monit, czy chcesz usunąć klaster. Po upewnieniu się za pomocą `y`, potrwa kilka minut, aby usunąć klaster. Po zakończeniu działania polecenia całą grupę zasobów i wszystkie zasoby wewnątrz niej, w tym klastrze, zostaną usunięte.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Usuwanie klastra przy użyciu witryny Azure portal

Alternatywnie możesz usunąć skojarzonej grupy zasobów klastra za pośrednictwem usługi online w witrynie Azure portal. Nazwa grupy zasobów jest taka sama jak nazwa klastra.

Obecnie `Microsoft.ContainerService/openShiftManagedClusters` zasób, który jest tworzony podczas tworzenia klastra jest ukryty w witrynie Azure portal. W `Resource group` widoku wyboru `Show hidden types` Aby wyświetlić grupę zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukryte](./media/aro-portal-hidden-type.png)

Usunięcie grupy zasobów spowoduje usunięcie wszystkich powiązanych zasobów, które są tworzone podczas tworzenia klastra usługi Azure Red Hat OpenShift.

## <a name="next-steps"></a>Kolejne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift

Dowiedz się więcej o korzystaniu z platformy OpenShift z oficjalną [dokumentacji Red Hat OpenShift](https://access.redhat.com/documentation/openshift_dedicated/3/)