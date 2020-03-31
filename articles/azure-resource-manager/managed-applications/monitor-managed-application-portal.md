---
title: Monitorowanie zarządzanej aplikacji za pomocą witryny Azure Portal
description: Pokazuje, jak używać witryny Azure portal do monitorowania dostępności i alertów dla aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651750"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorowanie wdrożonego wystąpienia aplikacji zarządzanej

Po wdrożeniu zarządzanej aplikacji do subskrypcji platformy Azure można sprawdzić stan aplikacji. W tym artykule przedstawiono opcje w witrynie Azure portal do sprawdzania stanu. Można monitorować dostępność zasobów w aplikacji zarządzanej. Można również skonfigurować i wyświetlić alerty.

## <a name="view-resource-health"></a>Wyświetlanie kondycji zasobu

1. Wybierz wystąpienie aplikacji zarządzanej.

   ![Wybierz aplikację zarządzana](./media/monitor-managed-application-portal/select-managed-application.png)

1. Wybierz **pozycję Kondycja zasobów**.

   ![Wybierz kondycję zasobów](./media/monitor-managed-application-portal/select-resource-health.png)

1. Służy do wyświetlania dostępności zasobów w aplikacji zarządzanej.

   ![Wyświetlanie kondycji zasobu](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Wyświetlanie alertów

1. Wybierz **pozycję Alerty**.

   ![Wybieranie alertów](./media/monitor-managed-application-portal/select-alerts.png)

1. Jeśli skonfigurowano reguły alertów, zobaczysz informacje o alertach, które zostały zgłoszone.

   ![Wyświetlanie alertów](./media/monitor-managed-application-portal/view-alerts.png)

1. Aby dodać reguły alertów, wybierz **pozycję + Nowa reguła alertu**.

   ![Tworzenie alertu](./media/monitor-managed-application-portal/create-new-alert.png)

Można utworzyć alerty dla wystąpienia aplikacji zarządzanej lub zasobów w aplikacji zarządzanej. Aby uzyskać informacje dotyczące tworzenia alertów, zobacz [Omówienie alertów na platformie Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Następne kroki

* W przypadku przykładów aplikacji zarządzanych zobacz [Przykładowe projekty dla aplikacji zarządzanych platformy Azure.](sample-projects.md)
* Aby wdrożyć aplikację zarządzaną, zobacz [Wdrażanie aplikacji wykazu usług za pośrednictwem witryny Azure Portal](deploy-service-catalog-quickstart.md).