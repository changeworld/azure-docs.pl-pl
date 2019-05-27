---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162625"
---
## <a name="create-base-resources"></a>Tworzenie zasobów podstawowego

Aby można było skonfigurować routing komunikatów, musisz utworzyć Centrum IoT hub, konto magazynu i kolejki usługi Service Bus. Te zasoby można utworzyć przy użyciu jednej z czterech artykułach, które jest dostępne dla części 1 tego samouczka: wiersza polecenia platformy Azure, programu Azure PowerShell, witryny Azure portal lub szablonu usługi Azure Resource Manager.

Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Następnie na końcu, możesz usunąć wszystkie zasoby w jednym kroku przez usunięcie grupy zasobów.

W poniższych sekcjach opisano kroki do wykonania.

1. Utwórz [grupę zasobów](../articles/azure-resource-manager/resource-group-overview.md).

2. Utwórz centrum IoT Hub w warstwie S1. Dodaj grupę użytkowników do centrum IoT Hub. Grupa użytkowników jest używana przez usługę Azure Stream Analytics podczas pobierania danych.

   > [!NOTE]
   > Aby ukończyć ten samouczek, należy użyć usługi IoT Hub w warstwie płatnej. Warstwa bezpłatna umożliwia wyłącznie skonfigurowanie jednego punktu końcowego, a w tym samouczku należy skonfigurować ich kilka.
   > 

3. Utwórz standardowe konto magazynu w wersji 1 przy użyciu replikacji Standard_LRS.

4. Utwórz przestrzeń nazw i kolejkę usługi Service Bus.

5. Utwórz tożsamość urządzenia symulowanego, które wysyła wiadomości do centrum. Zapisz klucz na potrzeby fazy testowania. (W przypadku tworzenia szablonu usługi Resource Manager, jest to wykonywane po wdrożeniu szablonu).