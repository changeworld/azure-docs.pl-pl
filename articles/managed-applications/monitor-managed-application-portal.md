---
title: Monitorowanie aplikacji zarządzanej za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Pokazuje, jak używać witryny Azure portal do monitorowania dostępności oraz alerty dla aplikacji zarządzanej.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 754d2a246a86585e9f05f8a070c51e158f73affd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253583"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitor wdrożone wystąpienie aplikacji zarządzanej

Po wdrożeniu aplikacji zarządzanej do subskrypcji platformy Azure, można sprawdzić stan aplikacji. W tym artykule przedstawiono opcje w witrynie Azure portal. Trwa sprawdzanie stanu. Można monitorować dostępność zasobów w aplikacji zarządzanej. Można również skonfigurować i wyświetlać alerty.

## <a name="view-resource-health"></a>Wyświetlanie kondycji zasobu

1. Wybierz wystąpienie usługi zarządzanej aplikacji.

   ![Wybierz zarządzaną aplikację](./media/monitor-managed-application-portal/select-managed-application.png)

1. Wybierz **Resource Health**.

   ![Wybierz opcję Usługa resource health](./media/monitor-managed-application-portal/select-resource-health.png)

1. Wyświetl dostępność zasobów w aplikacji zarządzanej.

   ![Wyświetlanie kondycji zasobu](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Wyświetlanie alertów

1. Wybierz **alerty**.

   ![Wybierz alerty](./media/monitor-managed-application-portal/select-alerts.png)

1. Jeśli masz skonfigurowanych reguł alertów, możesz sprawdzić informacje dotyczące alertów, które zostały zgłoszone.

   ![Wyświetlanie alertów](./media/monitor-managed-application-portal/view-alerts.png)

1. Aby dodać reguły alertów, wybierz **+ Nowa reguła alertu**.

   ![Tworzenie alertu](./media/monitor-managed-application-portal/create-new-alert.png)

Alerty można utworzyć wystąpienie aplikacji zarządzanej lub zasobów w aplikacji zarządzanej. Aby uzyskać informacje dotyczące tworzenia alertów, zobacz [Przegląd alertów na platformie Microsoft Azure](../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* Przykłady aplikacji zarządzanych, zobacz [przykładowych projektów dla platformy Azure zarządzane aplikacje](sample-projects.md).
* Aby wdrożyć aplikację zarządzaną, zobacz [Wdróż usługi katalogu aplikacji za pośrednictwem witryny Azure portal](deploy-service-catalog-quickstart.md).