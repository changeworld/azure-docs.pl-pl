---
title: Zarządzanie IoT Central w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Zarządzanie IoT Central w witrynie Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2018
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 73c8d64c53e23601b19a775a07b0b630c675b91f
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200475"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Zarządzanie IoT Central w witrynie Azure portal 
Oprócz tworzenia i zarządzania aplikacjami IoT Central w witrynie sieci Web IoT Central, można również zarządzać IoT Central w witrynie Azure portal. W tym artykule opisano, co jest możliwe i jak to zrobić.

## <a name="create-iot-central-applications"></a>Tworzenie aplikacji IoT Central
Aby utworzyć nową aplikację, przejdź do [witryny Azure portal](https://ms.portal.azure.com) i kliknij pozycję "Utwórz zasób" w menu główne menu nawigacji po lewej stronie. 

![Portal zarządzania: menu nawigacji](media/howto-manage-iot-central-from-portal/image0.png)

W typie pasek wyszukiwania w perspektywie "IoT Central".

![Portal zarządzania: wyszukiwanie](media/howto-manage-iot-central-from-portal/image0a.png)

Kliknij element wiersza IoT Central aplikacji w wynikach wyszukiwania.

![Portalu zarządzania: wyniki wyszukiwania](media/howto-manage-iot-central-from-portal/image0b.png)

Teraz kliknij przycisk "Utwórz", aby wyświetlić formularz, który należy wypełnić.

![Portal zarządzania: Zasobów IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Wypełnij wszystkie pola w formularzu. Ta forma jest podobne do formularza, do którego należy wypełnić do tworzenia aplikacji z witryny sieci Web IoT Central. Aby dowiedzieć się więcej na temat sposobu wypełniania każdego pola, zapoznaj się z [tworzenia aplikacji IoT Central](quick-deploy-iot-central.md) Szybki Start. 

![Portal zarządzania: Utwórz zasób IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Po wypełnieniu wszystkich pól, kliknij przycisk "Utwórz".

## <a name="manage-existing-iot-central-applications"></a>Zarządzanie istniejącymi aplikacjami IoT Central
Jeśli masz już aplikację usługi Azure IoT Central można usunąć, przenieś go do innej subskrypcji lub grupy zasobów w witrynie Azure portal. Wersje próbne aplikacji w witrynie Azure portal nie są widoczne, ponieważ subskrypcja nie wykonuje kopię tych prób.

Aby rozpocząć, kliknij pozycję "Wszystkie zasoby" główne menu nawigacji po lewej stronie. Użyj pola wyszukiwania, wpisz nazwę aplikacji i go znaleźć na liście zasobów. Następnie kliknij aplikację IoT Central, którą chcesz zarządzać.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image2.png)

Aby przejść do aplikacji, kliknij adres URL IoT Central aplikacji.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image3.png)

Aby przenieść aplikację do innej grupy zasobów, kliknij przycisk **zmienić** łącze obok grupy zasobów. Wybierz grupę zasobów, do którego chcesz przeprowadzić migrację tej aplikacji w oknie dialogowym.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image4.png)

Aby przenieść aplikację do innej subskrypcji, kliknij przycisk **zmienić** łącze obok subskrypcji. Wybierz subskrypcję, do którego chcesz przeprowadzić migrację tej aplikacji w oknie dialogowym.

![Portal zarządzania: Zarządzanie zasobami](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak zarządzać aplikacjami usługi Azure IoT Central w witrynie Azure portal, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Zarządzać aplikacją](howto-administer.md)