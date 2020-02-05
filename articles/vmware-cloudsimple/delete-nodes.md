---
title: Usuwanie węzłów dla rozwiązań VMware (Automatyczna synchronizacja) — Azure
description: Dowiedz się, jak usuwać węzły z programu VMWare z wdrożeniem automatycznej synchronizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024742"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Usuwanie węzłów z rozwiązania Azure VMware przez narzędzie automatyczna synchronizacja

Po utworzeniu węzły automatyczna synchronizacja jest naliczana. Aby zatrzymać pomiar węzłów, należy usunąć węzły. Węzły, które nie są używane w programie Azure Portal, zostaną usunięte.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Węzeł można usunąć tylko w następujących warunkach:

* Chmura prywatna do automatycznej synchronizacji utworzona z węzłami jest usuwana. Aby usunąć chmurę prywatną w wersji zapoznaj się z artykułem [usuwanie rozwiązania Azure VMware, PRZEAUTOMATYCZNA synchronizacja chmury prywatnej](delete-private-cloud.md).
* Węzeł został usunięty z chmury prywatnej automatycznej synchronizacji, zmniejszając chmurę prywatną do automatycznej synchronizacji. Aby obzmniejszyć chmurę prywatną do automatycznej synchronizacji, zobacz artykuł [zmniejszanie rozwiązań VMware dla platformy Azure dzięki automatycznej synchronizacji chmury prywatnej](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Usuń węzeł automatycznej synchronizacji

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **węzły automatycznej synchronizacji**.

   ![Wyszukaj węzły automatycznej synchronizacji](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły automatyczna synchronizacja**.

4. Wybierz węzły, które nie należą do chmury prywatnej automatycznej synchronizacji do usunięcia. W kolumnie **Nazwa chmury prywatnej do automatycznej** wersji programu wyświetlana jest nazwa chmury prywatnej automatycznej synchronizacji, do której należy węzeł. Jeśli węzeł nie jest używany przez chmurę prywatną do automatycznej synchronizacji, wartość będzie pusta. 

    ![Wybierz węzły automatycznej synchronizacji](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Można usunąć tylko węzły, które nie są częścią chmury prywatnej do automatycznej synchronizacji.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [automatycznej synchronizacji chmury prywatnej](cloudsimple-private-cloud.md)
