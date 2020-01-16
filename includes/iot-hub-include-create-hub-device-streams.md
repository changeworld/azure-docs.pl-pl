---
title: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
description: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 700dd305cf5365a604a5613bd5b48dbd2ac8f840
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021086"
---
W tej sekcji opisano, jak utworzyć centrum IoT przy użyciu witryny [Azure Portal](https://portal.azure.com).

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**, a następnie wprowadź *IoT Hub* w polu **Wyszukaj w witrynie Marketplace** .

1. Wybierz **IoT Hub** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Wypełnij pola w następujący sposób:

   - **Subskrypcja**: wybierz subskrypcję, która ma być używana przez centrum.

   - **Grupa zasobów**: Wybierz grupę zasobów lub Utwórz nową. Aby utworzyć nowy, wybierz pozycję **Utwórz nową** i wprowadź nazwę, której chcesz użyć. Aby użyć istniejącej grupy zasobów, wybierz tę grupę zasobów. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Region**: Wybierz region, w którym ma się znajdować centrum. Wybierz region, który obsługuje IoT Hub Podgląd strumieni urządzeń — **środkowe stany USA** lub **środkowe stany USA — euap**.

   - **Nazwa IoT Hub**: Wprowadź nazwę centrum. Ta nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   ![Tworzenie Centrum IoT Hub w Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Wybierz pozycję **Dalej: rozmiar i skala** , aby kontynuować tworzenie centrum.

   ![Ustawianie rozmiaru i skali nowego centrum IoT Hub przy użyciu Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   W obszarze **rozmiar i skala**można zaakceptować ustawienia domyślne i wybrać pozycję **Przegląd + Utwórz** u dołu. Należy wziąć pod uwagę następujące opcje:

   - **Cennik i warstwa skalowania**: wybrana warstwa. Wybierz jedną z warstw standardowych (**S1**, **S2**lub **S3**) lub **F1: warstwa Bezpłatna**. Ten wybór może być również zależeć od rozmiaru floty oraz obciążeń niestrumieniowych, które są oczekiwane w Twoim centrum, na przykład komunikaty telemetryczne. Na przykład warstwa Bezpłatna służy do testowania i oceny. Obsługuje ona 500 urządzeń połączonych z centrum IoT i do 8000 komunikatów dziennie. Każda subskrypcja platformy Azure może utworzyć jedno Centrum IoT Hub w warstwie Bezpłatna. 

   - **Liczba jednostek IoT Hub**: liczba komunikatów dozwolonych na jednostkę dziennie zależy od warstwy cenowej centrum. Wybór ten zależy od obciążenia niestrumieniowego, które jest oczekiwane w Twoim centrum. Dla tej pory możesz wybrać 1.

   - **Ustawienia zaawansowane** > **partycji między urządzeniami a chmurą**: Ta właściwość wiąże komunikaty z urządzenia z chmurą z liczbą jednoczesnych czytników komunikatów. Większość centrów potrzebuje tylko czterech partycji.

   Aby uzyskać więcej informacji o opcjach warstwy, zobacz [Wybieranie odpowiedniej warstwy usługi IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Aby przejrzeć wybrane opcje, wybierz pozycję **Przegląd + Utwórz**. Wyniki będą podobne do następujących:

   ![Informacje na temat tworzenia nowego centrum IoT Hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Aby utworzyć nowe centrum IoT Hub, wybierz pozycję **Utwórz**. Proces trwa kilka minut.
