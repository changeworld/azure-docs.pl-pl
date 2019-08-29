---
title: Zarządzaj IoT Central z Azure Portal | Microsoft Docs
description: Zarządzaj IoT Central z Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 25751db785bf5d0b67195d1e1db660c0b9b5bf4e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100946"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzaj IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz użyć [Azure Portal](https://portal.azure.com) do zarządzania aplikacjami.

## <a name="create-iot-central-applications"></a>Tworzenie aplikacji IoT Central

Aby utworzyć aplikację, przejdź do [Azure Portal](https://ms.portal.azure.com) i wybierz pozycję **Utwórz zasób** w głównym menu nawigacji po lewej stronie.

![Portal zarządzania: menu nawigacji](media/howto-manage-iot-central-from-portal/image0.png)

Na pasku wyszukiwania wpisz **IoT Central**.

![Portal zarządzania: wyszukiwanie](media/howto-manage-iot-central-from-portal/image0a1.png)

Wybierz **IoT Central wiersz aplikacji** w wynikach wyszukiwania.

![Portal zarządzania: wyniki wyszukiwania](media/howto-manage-iot-central-from-portal/image0b1.png)

Następnie wybierz pozycję **Utwórz**.

![Portal zarządzania: Zasób IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Wypełnij wszystkie pola w formularzu. Ten formularz jest podobny do formularza wypełnianego, aby utworzyć aplikacje w witrynie sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji IoT Central](quick-deploy-iot-central.md) — Szybki Start.

![Portal zarządzania: Tworzenie zasobu IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Po wypełnieniu wszystkich pól wybierz pozycję **Utwórz**.

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi aplikacjami IoT Central

Jeśli masz już aplikację IoT Central platformy Azure, możesz ją usunąć lub przenieść do innej subskrypcji lub grupy zasobów w Azure Portal.

> [!NOTE]
> W Azure Portal nie są widoczne aplikacje próbne, ponieważ nie są one skojarzone z Twoją subskrypcją.

Aby rozpocząć, wybierz pozycję **wszystkie zasoby** w głównym menu nawigacji po lewej stronie. Użyj pola wyszukiwania, aby wpisać nazwę aplikacji, aby znaleźć ją na liście zasobów. Następnie wybierz aplikację IoT Central, którą chcesz zarządzać.

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image2a.png)

Aby przejść do aplikacji, wybierz adres URL aplikacji IoT Central.

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, wybierz pozycję **Zmień** obok grupy zasobów. Na stronie **przenoszenie zasobów** wybierz grupę zasobów, do której chcesz migrować tę aplikację.

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image4a.png)

Aby przenieść aplikację do innej subskrypcji, wybierz łącze **Zmień** obok subskrypcji. Wybierz subskrypcję, do której chcesz migrować tę aplikację w wyświetlonym oknie dialogowym.

![Portal zarządzania: zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure z poziomu Azure Portal, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Administruj swoją aplikacją](howto-administer.md)