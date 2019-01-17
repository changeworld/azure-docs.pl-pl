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
ms.openlocfilehash: 89109dec83342a8f4b5962778b1803eb36656e42
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352220"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzanie IoT Central w witrynie Azure portal

Zamiast tworzenia aplikacji i zarządzaniem nimi IoT Central z IoT Central [Menedżer aplikacji](https://aka.ms/iotcentral) strony, można użyć [witryny Azure portal](https://portal.azure.com) do zarządzania aplikacjami.

## <a name="create-iot-central-applications"></a>Tworzenie aplikacji IoT Central

Aby utworzyć aplikację, przejdź do [witryny Azure portal](https://ms.portal.azure.com) i kliknij przycisk **Utwórz zasób** w główne menu nawigacji po lewej stronie.

![Portal zarządzania: menu nawigacji](media/howto-manage-iot-central-from-portal/image0.png)

Na pasku wyszukiwania wpisz **IoT Central**.

![Portal zarządzania: wyszukiwanie](media/howto-manage-iot-central-from-portal/image0a.png)

Kliknij przycisk **IoT Central aplikacji** pozycji w wynikach wyszukiwania.

![Portalu zarządzania: wyniki wyszukiwania](media/howto-manage-iot-central-from-portal/image0b.png)

Teraz, kliknij pozycję **Utwórz** przycisku.

![Portal zarządzania: Zasobów IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Wypełnij wszystkie pola w formularzu. Jest on podobny do formularza, wypełnij tworzenie aplikacji na IoT Central [Menedżer aplikacji](https://aka.ms/iotcentral) strony. Aby uzyskać więcej informacji, zobacz [tworzenia aplikacji IoT Central](quick-deploy-iot-central.md) Szybki Start.

![Portal zarządzania: Utwórz zasób IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Po wypełnieniu wszystkich pól, kliknij przycisk **Utwórz** przycisku.

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi aplikacjami IoT Central

Jeśli masz już aplikację usługi Azure IoT Central, można ją usunąć lub przenieść je do innej subskrypcji lub grupy zasobów w witrynie Azure portal.

> [!NOTE]
> Nie widzisz aplikacji w wersji próbnej w witrynie Azure portal, ponieważ nie są one powiązane z Twoją subskrypcją

Aby rozpocząć, kliknij pozycję **wszystkie zasoby** w główne menu nawigacji po lewej stronie. Użyj pola wyszukiwania, aby wpisz nazwę aplikacji w taki sposób, aby je znaleźć na liście zasobów. Następnie kliknij żądaną aplikację IoT Central, którą chcesz zarządzać.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image2.png)

Aby przejść do aplikacji, kliknij adres URL IoT Central aplikacji.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, kliknij przycisk **zmienić** obok grupy zasobów. Na **przenoszenia zasobów** stronie, wybierz grupę zasobów, możesz przeprowadzić migrację tej aplikacji.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image4.png)

Aby przenieść aplikację do innej subskrypcji, kliknij przycisk **zmienić** łącze obok subskrypcji. Wybierz subskrypcję, do którego chcesz przeprowadzić migrację tej aplikacji w oknie dialogowym.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak zarządzać aplikacjami usługi Azure IoT Central w witrynie Azure portal, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)