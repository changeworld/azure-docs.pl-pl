---
title: Samouczek — usuwanie klastra OpenShift red hat azure
description: W tym samouczku dowiesz się, jak usunąć klaster Azure Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278771"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Samouczek: Usuwanie klastra OpenShift red hat azure

To jest koniec samouczka. Po zakończeniu testowania przykładowego klastra, oto jak go usunąć i skojarzone z nim zasoby, aby nie pobierać opłat za to, czego nie używasz.

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

* Utwórz klaster, wykonując samouczek [tworzenia klastra OpenShift red hat.](tutorial-create-cluster.md)

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Zaloguj się na platformie Azure

Jeśli korzystasz z interfejsu wiersza polecenia `az login` platformy Azure lokalnie, uruchom, aby zalogować się na platformie Azure.

```bash
az login
```

Jeśli masz dostęp do wielu `az account set -s {subscription ID}` subskrypcji, uruchom zastąpienie `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="step-2-delete-the-cluster"></a>Krok 2: Usuwanie klastra

Otwórz terminal Bash i ustaw CLUSTER_NAME zmienną na nazwę klastra:

```bash
CLUSTER_NAME=yourclustername
```

Teraz usuń klaster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Zostanie wyświetlony monit o usunięcie klastra. Po potwierdzenie za pomocą `y`funkcji , usunięcie klastra zajmie kilka minut. Po zakończeniu polecenia cała grupa zasobów i wszystkie znajdujące się w niej zasoby, w tym klaster, zostaną usunięte.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Usuwanie klastra przy użyciu witryny Azure portal

Alternatywnie można usunąć skojarzoną grupę zasobów klastra za pośrednictwem witryny Azure portal online. Nazwa grupy zasobów jest taka sama jak nazwa klastra.

Obecnie `Microsoft.ContainerService/openShiftManagedClusters` zasób, który jest tworzony podczas tworzenia klastra jest ukryty w witrynie Azure portal. W `Resource group` widoku sprawdź, czy `Show hidden types` grupa zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukrytego](./media/aro-portal-hidden-type.png)

Usunięcie grupy zasobów spowoduje usunięcie wszystkich powiązanych zasobów, które zostaną utworzone podczas tworzenia klastra OpenShift usługi Azure Red Hat.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift

Dowiedz się więcej o korzystaniu z OpenShift z oficjalną [dokumentacją Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
