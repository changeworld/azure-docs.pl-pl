---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670986"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

W kolejnym kroku utwórz tożsamość urządzenia i zapisz jego klucz do późniejszego użycia. Ta tożsamość urządzenia jest używana przez aplikację symulacji do wysyłania komunikatów do centrum IoT Hub. Ta funkcja nie jest dostępna w programie PowerShell lub przy użyciu szablonu usługi Azure Resource Manager. Następujące informacje, jak utworzyć symulowane urządzenie przy użyciu [witryny Azure portal](https://portal.azure.com).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się do konta platformy Azure.

2. Wybierz **grup zasobów** a następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

3. Na liście zasobów wybierz Centrum IoT hub. W tym samouczku jest używane centrum **ContosoTestHub**. Wybierz pozycję **Urządzenia IoT** w okienku centrum.

4. Wybierz pozycję **+ Dodaj**. W okienku Dodawanie urządzenia wypełnij pole identyfikatora urządzenia. W tym samouczku jest używany identyfikator **Contoso-Test-Device**. Pozostaw klucze puste i zaznacz pole **Automatycznie generuj klucze**. Upewnij się, że pozycja **Połącz urządzenie z centrum IoT Hub** została włączona. Wybierz pozycję **Zapisz**.

   ![Na ekranie Dodaj urządzenie](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Teraz, gdy została utworzona, zaznacz urządzenie, aby wyświetlić wygenerowanych kluczy. Wybierz ikonę kopiowania przy użyciu klucza podstawowego, a następnie zapisz go zanotować takiego jak Notatnik w fazie testowania w tym samouczku.

   ![Szczegóły urządzenia, w tym do kluczy](./media/iot-hub-include-create-simulated-device-portal/device-details.png)