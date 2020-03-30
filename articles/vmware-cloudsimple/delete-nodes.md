---
title: Usuwanie węzłów dla rozwiązania VMware przez CloudSimple — Azure
description: Dowiedz się, jak usunąć węzły z usługi VMWare za pomocą wdrożenia CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024742"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Usuwanie węzłów z rozwiązania Azure VMware przez CloudSimple

Węzły CloudSimple są mierzone po ich utworzeniu.  Węzły muszą zostać usunięte, aby zatrzymać pomiary węzłów.  Usuń węzły, które nie są używane z witryny Azure portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzeł można usunąć tylko w następujących warunkach:

* Chmura prywatna utworzona z węzłami zostanie usunięta.  Aby usunąć chmurę [prywatną, zobacz Usuwanie rozwiązania Azure VMware firmy CloudSimple Private Cloud](delete-private-cloud.md).
* Węzeł został usunięty z chmury prywatnej przez zmniejszenie chmury prywatnej.  Aby zmniejszyć chmurę prywatną, zobacz [Zmniejszanie rozwiązania Azure VMware firmy CloudSimple private cloud.](shrink-private-cloud.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Usuń węzeł CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **węzły CloudSimple**.

   ![Szukaj węzłów CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **opcję Węzły CloudSimple**.

4. Wybierz węzły, które nie należą do chmury prywatnej, aby usunąć.  **Kolumna NAZWA chmury prywatnej** zawiera nazwę chmury prywatnej, do której należy węzeł.  Jeśli węzeł nie jest używany przez chmurę prywatną, wartość będzie pusta. 

    ![Wybierz węzły CloudSimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Można usunąć tylko węzły, które nie są częścią chmury prywatnej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [private cloud](cloudsimple-private-cloud.md)
