---
title: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
description: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a02c16a75bbdf4827ce6a6c5f50751caf9c5aab8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445969"
---
W tej sekcji opisano sposób tworzenia Centrum IoT Hub przy użyciu [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**, a następnie wybierz pozycję **Internet rzeczy**.

1. Z listy znajdującej się po prawej stronie wybierz pozycję **IoT Hub**. Zostanie otwarta pierwsza strona tworzenia Centrum IoT Hub.

   ![Tworzenie Centrum IoT Hub w Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Wypełnij następujące pola:

   a. Z listy rozwijanej **subskrypcja** wybierz subskrypcję do użycia w usłudze IoT Hub.

   b. W obszarze **Grupa zasobów**wykonaj jedną z następujących czynności: 
      * Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** , a następnie wprowadź nazwę, której chcesz użyć. 
      * Aby użyć istniejącej grupy zasobów, wybierz pozycję **Użyj istniejącej** , a następnie z listy rozwijanej wybierz grupę zasobów. 
      
        Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. Z listy rozwijanej **region** wybierz region, w którym ma się znajdować centrum. Wybierz region, który obsługuje IoT Hub Podgląd strumieni urządzeń — **środkowe stany USA** lub **środkowe stany USA — euap**.

   d. W polu **nazwa IoT Hub** wprowadź nazwę Centrum IoT. Nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Aby kontynuować tworzenie Centrum IoT, wybierz pozycję **dalej: Rozmiar i skala**.

   ![Ustawianie rozmiaru i skali nowego centrum IoT Hub przy użyciu Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   W tym okienku można zaakceptować ustawienia domyślne i wybrać pozycję **Przegląd + Utwórz** u dołu. Należy wziąć pod uwagę następujące opcje:

   * Z listy rozwijanej **Cennik i warstwa skali** wybierz jedną z warstw standardowych (**S1**, **S2**lub **S3**) lub **F1: Warstwa**bezpłatna. Ten wybór może być również zależeć od rozmiaru floty oraz obciążeń niestrumieniowych, które oczekują w Twoim centrum (na przykład komunikaty telemetryczne). Na przykład warstwa Bezpłatna służy do testowania i oceny. Obsługuje ona 500 urządzeń połączonych z centrum IoT i do 8000 komunikatów dziennie. Każda subskrypcja platformy Azure może utworzyć jedno Centrum IoT Hub w warstwie Bezpłatna. 

   * **Liczba jednostek IoT Hub**: Wybór ten zależy od obciążenia niestrumieniowego, które jest oczekiwane w Twoim centrum. Dla tej pory możesz wybrać 1.

   Aby uzyskać więcej informacji o opcjach warstwy, zobacz [Wybieranie odpowiedniej warstwy usługi IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Aby przejrzeć wybrane opcje, wybierz kartę **Recenzja + tworzenie** . Otwarte okienko jest podobne do następujących:

   ![Informacje na temat tworzenia nowego centrum IoT Hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Aby utworzyć nowe centrum IoT Hub, wybierz pozycję **Utwórz**. Proces trwa kilka minut.
