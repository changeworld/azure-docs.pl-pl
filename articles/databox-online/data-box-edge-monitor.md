---
title: Monitorowanie usługi Azure Data Box Edge urządzenia | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak monitorować krawędzi pola danych sieci platformy Azure za pomocą witryny Azure portal i lokalnego internetowego interfejsu użytkownika.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002650"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorowanie krawędzi sieci usługi Azure Data Box

W tym artykule opisano sposób monitorowania krawędzi pola danych sieci platformy Azure. Aby monitorować urządzenie, można użyć witryny Azure portal lub lokalnego Interfejsu w przeglądarce. Użyj witryny Azure portal, aby wyświetlić zdarzenia urządzenia, konfigurowanie i Zarządzanie alertami i wyświetlanie metryk. Użyj lokalnego internetowego interfejsu użytkownika na urządzenie fizyczne, aby wyświetlić stan sprzętu z różnych składników urządzenia.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Wyświetl zdarzenia urządzenia i odpowiednie alerty
> * Wyświetlanie stanu sprzętu elementów urządzenia
> * Wyświetlanie metryk pojemności i transakcji dla Twojego urządzenia
> * Skonfiguruj alerty i zarządzaj nimi

## <a name="view-device-events"></a>Wyświetl zdarzenia dotyczące urządzeń

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Wyświetlanie stanu sprzętu

W przypadku lokalnego internetowego interfejsu użytkownika, aby wyświetlić stan sprzętu składniki urządzenia, wykonaj następujące kroki.

1. Nawiązać połączenia lokalnego internetowego interfejsu użytkownika urządzenia.
2. Przejdź do **konserwacji > Stan sprzętu**. Można wyświetlić kondycję różnych składników urządzenia.

    ![Wyświetlanie stanu sprzętu](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Wyświetlanie metryk

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Zarządzanie alertami

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Kolejne kroki 

Dowiedz się, jak [zarządzać przepustowością](data-box-edge-manage-bandwidth-schedules.md).