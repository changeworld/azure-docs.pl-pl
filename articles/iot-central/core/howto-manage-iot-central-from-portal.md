---
title: Zarządzanie usługami IoT Central za pomocą witryny Azure Portal | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób tworzenia aplikacji IoT Central i zarządzania nimi z witryny Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157929"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzanie usługami IoT Central za pomocą witryny Azure portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie [Azure IoT Central manager,](https://aka.ms/iotcentral) można użyć [witryny Azure Portal](https://portal.azure.com) do zarządzania aplikacjami.

## <a name="create-iot-central-applications"></a>Tworzenie aplikacji IoT Central

Aby utworzyć aplikację, przejdź do [witryny Azure Portal](https://ms.portal.azure.com) i wybierz pozycję **Utwórz zasób**.

W **polu Wyszukaj pasek Marketplace** wpisz *IoT Central*:

![Portal zarządzania: wyszukiwanie](media/howto-manage-iot-central-from-portal/image0a1.png)

Wybierz kafelek **Aplikacji Centralnej IoT** w wynikach wyszukiwania:

![Portal zarządzania: wyniki wyszukiwania](media/howto-manage-iot-central-from-portal/image0b1.png)

Teraz wybierz pozycję **Utwórz**:

![Portal zarządzania: zasób IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Wypełnij wszystkie pola w formularzu. Ten formularz jest podobny do formularza wypełniane do tworzenia aplikacji w witrynie sieci Web [menedżera aplikacji Usługi Azure IoT Central.](https://aka.ms/iotcentral) Aby uzyskać więcej informacji, zobacz [Tworzenie szybkiego startu aplikacji IoT Central.](quick-deploy-iot-central.md)

![Tworzenie formularza centralnego IoT](media/howto-manage-iot-central-from-portal/image6a.png)

**Lokalizacja** to [lokalizacja,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz utworzyć aplikację. Zazwyczaj należy wybrać lokalizację, która jest fizycznie najbliżej urządzeń, aby uzyskać optymalną wydajność. Usługa Azure IoT Central jest obecnie dostępna w regionach **Australia,** **Azja i Pacyfik,** **Europa,** **Stany Zjednoczone,** **Wielka Brytania**i **Japonia.** Po wybraniu lokalizacji nie można później przenieść aplikacji do innej lokalizacji.

Po wypełnieniu wszystkich pól wybierz pozycję **Utwórz**.

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi aplikacjami IoT Central

Jeśli masz już aplikację Azure IoT Central, możesz ją usunąć lub przenieść do innej subskrypcji lub grupy zasobów w witrynie Azure portal.

> [!NOTE]
> Nie widać aplikacji utworzonych w planie bezpłatnych cen w witrynie Azure portal, ponieważ nie są one skojarzone z subskrypcją.

Aby rozpocząć, wybierz **pozycję Wszystkie zasoby** w portalu. Wybierz **pozycję Pokaż ukryte typy** i zacznij wpisywać nazwę aplikacji w **filtrze według nazwy,** aby ją znaleźć. Następnie wybierz aplikację IoT Central, którą chcesz zarządzać.

Aby przejść do aplikacji, wybierz **adres URL aplikacji centralnej IoT:**

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, wybierz **zmień** obok grupy zasobów. Na stronie **Przenoszenie zasobów** wybierz grupę zasobów, do której chcesz przenieść tę aplikację:

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image4a.png)

Aby przenieść aplikację do innej subskrypcji, wybierz **zmień** obok subskrypcji. Na stronie **Przenieś zasoby** wybierz subskrypcję, do której chcesz przenieść tę aplikację:

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak zarządzać aplikacjami Usługi Azure IoT Central z witryny Azure portal, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administrowanie aplikacją](howto-administer.md)