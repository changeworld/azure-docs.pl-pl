---
title: Samouczek — usuwanie klastra usługi Azure Red Hat OpenShift
description: W tym samouczku dowiesz się, jak usunąć klaster Red Hat OpenShift platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278771"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Samouczek: usuwanie klastra usługi Azure Red Hat OpenShift

To jest koniec samouczka. Po zakończeniu testowania przykładowego klastra poniżej przedstawiono sposób usuwania go i skojarzonych zasobów, dzięki czemu nie jest naliczana opłata za to, czego nie używasz.

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

* Utwórz klaster, wykonując czynności opisane w samouczku [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="step-1-sign-in-to-azure"></a>Krok 1. Logowanie do platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, uruchom `az login`, aby zalogować się do platformy Azure.

```bash
az login
```

Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="step-2-delete-the-cluster"></a>Krok 2. Usuwanie klastra

Otwórz Terminal bash i Ustaw zmienną CLUSTER_NAME na nazwę klastra:

```bash
CLUSTER_NAME=yourclustername
```

Teraz Usuń klaster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Zostanie wyświetlony monit z pytaniem, czy chcesz usunąć klaster. Po potwierdzeniu `y`, usunięcie klastra zajmie kilka minut. Po zakończeniu działania polecenia zostanie usunięta cała grupa zasobów i wszystkie znajdujące się w niej zasoby, w tym klaster.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Usuwanie klastra przy użyciu Azure Portal

Alternatywnie możesz usunąć skojarzoną grupę zasobów klastra za pomocą Azure Portal online. Nazwa grupy zasobów jest taka sama jak nazwa klastra.

Obecnie `Microsoft.ContainerService/openShiftManagedClusters` zasób tworzony podczas tworzenia klastra jest ukryty w Azure Portal. W widoku `Resource group` zaznacz pozycję `Show hidden types`, aby wyświetlić grupę zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukrytego](./media/aro-portal-hidden-type.png)

Usunięcie grupy zasobów spowoduje usunięcie wszystkich powiązanych zasobów utworzonych podczas tworzenia klastra Red Hat OpenShift platformy Azure.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Usuwanie klastra usługi Azure Red Hat OpenShift

Dowiedz się więcej o korzystaniu z OpenShift z oficjalną [dokumentacją firmy Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
