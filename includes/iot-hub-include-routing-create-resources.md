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
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76020936"
---
## <a name="create-base-resources"></a>Tworzenie zasobów podstawowych

Przed skonfigurowaniem routingu wiadomości należy utworzyć centrum IoT hub, konto magazynu i kolejkę usługi Service Bus. Te zasoby można utworzyć przy użyciu jednego z czterech artykułów, które są dostępne dla części 1 tego samouczka: portal Azure, szablon usługi Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

Użyj tej samej grupy zasobów i lokalizacji dla wszystkich zasobów. Następnie na końcu można usunąć wszystkie zasoby w jednym kroku, usuwając grupę zasobów.

Poniżej znajduje się podsumowanie kroków, które należy wykonać w następujących sekcjach: 

1. Tworzenie [grupy zasobów](../articles/azure-resource-manager/management/overview.md).

2. Utwórz centrum IoT Hub w warstwie S1. Dodaj grupę użytkowników do centrum IoT Hub. Grupa użytkowników jest używana przez usługę Azure Stream Analytics podczas pobierania danych.

   > [!NOTE]
   > Aby ukończyć ten samouczek, należy użyć usługi IoT Hub w warstwie płatnej. Warstwa bezpłatna umożliwia wyłącznie skonfigurowanie jednego punktu końcowego, a w tym samouczku należy skonfigurować ich kilka.
   > 

3. Utwórz standardowe konto magazynu w wersji 1 przy użyciu replikacji Standard_LRS.

4. Utwórz przestrzeń nazw i kolejkę usługi Service Bus.

5. Utwórz tożsamość urządzenia symulowanego, które wysyła wiadomości do centrum. Zapisz klucz na potrzeby fazy testowania. (W przypadku tworzenia szablonu Menedżera zasobów odbywa się to po wdrożeniu szablonu).