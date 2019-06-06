---
title: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
description: dołączanie pliku (strumienie urządzeń — wersja zapoznawcza)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 93c71fa8b0c39cc16d2a8e24472e8d68717a6c32
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733244"
---
W tej sekcji opisano sposób tworzenia Centrum IoT hub przy użyciu [witryny Azure portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **Utwórz zasób**, a następnie wybierz pozycję **Internet of Things**.

1. Z listy po prawej stronie wybierz **usługi Iot Hub**. Zostanie otwarta strona pierwszego, do tworzenia Centrum IoT hub.

   ![Tworzenie Centrum IoT hub w witrynie Azure portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Wypełnij następujące pola:

   a. W **subskrypcji** listy rozwijanej wybierz subskrypcję do użycia dla usługi IoT hub.

   b. Aby uzyskać **grupy zasobów**, wykonaj jedną z następujących czynności: 
      * Aby utworzyć nową grupę zasobów, wybierz **Utwórz nową** i wprowadź nazwę, której chcesz użyć. 
      * Aby użyć istniejącej grupy zasobów, wybierz **Użyj istniejącej** a następnie na liście rozwijanej wybierz grupę zasobów. 
      
        Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. W **Region** listę rozwijaną, wybierz region, w którym chcesz Centrum znajdował się na liście. Wybierz region, który obsługuje Podgląd strumieni urządzenia usługi IoT Hub, albo **środkowe stany USA** lub **centralnej stany USA — EUAP**.

   d. W **nazwy Centrum IoT** wprowadź nazwę Centrum IoT. Nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Aby kontynuować tworzenie Centrum IoT hub, wybierz pozycję **dalej: Rozmiar i skalę**.

   ![Ustawianie rozmiaru i skali dla nowego centrum IoT przy użyciu witryny Azure portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   W tym okienku można zaakceptować ustawienia domyślne i wybierz **przeglądu + Utwórz** u dołu. należy wziąć pod uwagę następujące opcje:

   * W **ceny i warstwa skalowania** listy rozwijanej, wybierz jedną z warstwy standardowa (**S1**, **S2**, lub **S3**) lub **F1: W warstwie bezpłatna**. Ten wybór można również kierować się rozmiar floty i obsługiwane strumieniowo obciążeń, które oczekują w Centrum (na przykład komunikaty telemetryczne). Na przykład warstwa Bezpłatna służy do testowania i oceny. Obsługuje ona 500 urządzeń połączonych z centrum IoT i do 8000 komunikatów dziennie. Każda subskrypcja platformy Azure można utworzyć jedno centrum IoT w ramach warstwy bezpłatna. 

   * Aby uzyskać **jednostki numer usługi IoT Hub**: Ten wybór zależy od obciążenia — przesyłanie strumieniowe, których oczekujesz od Centrum. Teraz możesz wybrać 1.

   Aby uzyskać więcej informacji na temat opcji warstwy, zobacz [wybrać właściwą warstwę Centrum IoT](../articles/iot-hub/iot-hub-scaling.md).

1. Aby przejrzeć wybrane opcje, wybierz pozycję **Przejrzyj + Utwórz** kartę. Okienko w którym zostanie otwarty jest podobny do następującego:

   ![Informacje dotyczące tworzenia nowego centrum IoT hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Aby utworzyć nowe Centrum IoT hub, wybierz **Utwórz**. Ten proces trwa kilka minut.
