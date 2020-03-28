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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67183853"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

W kolejnym kroku utwórz tożsamość urządzenia i zapisz jego klucz do późniejszego użycia. Ta tożsamość urządzenia jest używana przez aplikację symulacji do wysyłania komunikatów do centrum IoT Hub. Ta funkcja nie jest dostępna w programie PowerShell lub podczas korzystania z szablonu usługi Azure Resource Manager. W poniższych krokach opisano sposób tworzenia symulowanego urządzenia za pomocą [witryny Azure Portal](https://portal.azure.com).

1. Otwórz witrynę [Azure Portal](https://portal.azure.com) i zaloguj się do konta platformy Azure.

2. Wybierz **pozycję Grupy zasobów,** a następnie wybierz grupę zasobów. W tym samouczku jest używana grupa **ContosoResources**.

3. Na liście zasobów wybierz centrum IoT Hub. W tym samouczku jest używane centrum **ContosoTestHub**. Wybierz pozycję **Urządzenia IoT** w okienku centrum.

4. Wybierz pozycję **+ Dodaj**. W okienku Dodawanie urządzenia wypełnij pole identyfikatora urządzenia. W tym samouczku jest używany identyfikator **Contoso-Test-Device**. Pozostaw klucze puste i zaznacz pole **Automatycznie generuj klucze**. Upewnij się, że pozycja **Połącz urządzenie z centrum IoT Hub** została włączona. Wybierz **pozycję Zapisz**.

   ![Ekran urządzenia dodatkowego](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Teraz, gdy został utworzony, wybierz urządzenie, aby zobaczyć wygenerowane klucze. Wybierz ikonę Kopiuj na kluczu podstawowym i zapisz ją w miejscu, takim jak Notatnik, na etapie testowania tego samouczka.

   ![Dane urządzenia, w tym klawisze](./media/iot-hub-include-create-simulated-device-portal/device-details.png)