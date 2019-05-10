---
title: Zarządzanie IoT Central w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Zarządzanie IoT Central w witrynie Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c72de0ef874659a5d7840689e38bd7857c25b840
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464073"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzanie IoT Central w witrynie Azure portal

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Zamiast tworzenia aplikacji i zarządzaniem nimi IoT Central z IoT Central [Menedżer aplikacji](https://aka.ms/iotcentral) strony, można użyć [witryny Azure portal](https://portal.azure.com) do zarządzania aplikacjami.

## <a name="create-iot-central-applications"></a>Tworzenie aplikacji IoT Central

Aby utworzyć aplikację, przejdź do [witryny Azure portal](https://ms.portal.azure.com) i wybierz **Utwórz zasób** w główne menu nawigacji po lewej stronie.

![Portal zarządzania: menu nawigacji](media/howto-manage-iot-central-from-portal/image0.png)

Na pasku wyszukiwania wpisz **IoT Central**.

![Portal zarządzania: wyszukiwanie](media/howto-manage-iot-central-from-portal/image0a1.png)

Wybierz **IoT Central aplikacji** pozycji w wynikach wyszukiwania.

![Portalu zarządzania: wyniki wyszukiwania](media/howto-manage-iot-central-from-portal/image0b1.png)

Następnie wybierz pozycję **Utwórz**.

![Portal zarządzania: Zasobów IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Wypełnij wszystkie pola w formularzu. Jest on podobny do formularza, wypełnij tworzenie aplikacji na IoT Central [Menedżer aplikacji](https://aka.ms/iotcentral) strony. Aby uzyskać więcej informacji, zobacz [tworzenia aplikacji IoT Central](quick-deploy-iot-central.md) Szybki Start.

![Portal zarządzania: Utwórz zasób IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Po wypełnieniu wszystkich pól, zaznacz **Utwórz**.

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi aplikacjami IoT Central

Jeśli masz już aplikację usługi Azure IoT Central, można ją usunąć lub przenieść je do innej subskrypcji lub grupy zasobów w witrynie Azure portal.

> [!NOTE]
> Nie widzisz aplikacji w wersji próbnej w witrynie Azure portal, ponieważ nie są one powiązane z Twoją subskrypcją

Aby rozpocząć, wybierz **wszystkie zasoby** w główne menu nawigacji po lewej stronie. Użyj pola wyszukiwania, aby wpisz nazwę aplikacji w taki sposób, aby je znaleźć na liście zasobów. Następnie wybierz aplikację IoT Central, którą chcesz zarządzać.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image2a.png)

Aby przejść do aplikacji, wybierz adres URL IoT Central aplikacji.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, wybierz pozycję **zmienić** obok grupy zasobów. Na **przenoszenia zasobów** stronie, wybierz grupę zasobów, możesz przeprowadzić migrację tej aplikacji.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image4a.png)

Aby przenieść ją do innej subskrypcji, wybierz pozycję **zmienić** łącze obok subskrypcji. Wybierz subskrypcję, do którego chcesz przeprowadzić migrację tej aplikacji w oknie dialogowym.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak zarządzać aplikacjami usługi Azure IoT Central w witrynie Azure portal, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)