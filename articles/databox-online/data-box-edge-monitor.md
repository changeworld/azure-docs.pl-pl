---
title: Monitorowanie urządzenia usługi Azure Data Box Edge | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać witryny Azure portal i lokalnego interfejsu użytkownika sieci web do monitorowania usługi Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756756"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorowanie usługi Azure Data Box Edge

W tym artykule opisano sposób monitorowania usługi Azure Data Box Edge. Aby monitorować urządzenie, można użyć witryny Azure portal lub lokalnego interfejsu użytkownika sieci web. Użyj witryny Azure Portal, aby wyświetlać zdarzenia urządzenia, konfigurować alerty i zarządzać nimi oraz wyświetlać metryki. Użyj lokalnego interfejsu użytkownika sieci Web na urządzeniu fizycznym, aby wyświetlić stan sprzętu różnych składników urządzenia.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Wyświetlanie zdarzeń urządzenia i odpowiednich alertów
> * Wyświetlanie stanu sprzętu składników urządzenia
> * Wyświetlanie danych o pojemności i transakcjach dla urządzenia
> * Konfigurowanie alertów i zarządzanie nimi

## <a name="view-device-events"></a>Wyświetlanie zdarzeń urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Wyświetlanie stanu sprzętu

Aby wyświetlić stan sprzętu składników urządzenia, należy wykonać następujące kroki w lokalnym interfejsie użytkownika sieci Web.

1. Połącz się z lokalnym interfejsem użytkownika w sieci Web urządzenia.
2. Przejdź do **stanu konserwacji > sprzętu**. Można wyświetlić kondycję różnych składników urządzenia.

    ![Wyświetlanie stanu sprzętu](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Wyświetlanie metryk

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Zarządzanie alertami

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Następne kroki 

Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).