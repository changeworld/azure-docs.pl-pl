---
title: Schemat urządzenia w rozwiązaniu do symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano schematu JSON, który definiuje symulowanego urządzenia w rozwiązaniu do symulacji urządzenia.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338159"
---
# <a name="understand-the-device-model-schema"></a>Zrozumienie schematu modelu urządzenia

Symulacja urządzenia akceleratora rozwiązań służy do generowania testu obciążenia dla rozwiązania, które używają usługi IoT Hub. Podczas wdrażania rozwiązania symulacji urządzenia zbiór symulowanych urządzeń jest automatycznie inicjowana obsługa. Można dostosować istniejący Symulowane urządzenia, lub Utwórz swoje własne.

W tym artykule opisano schematu modelu urządzenia, która określa zachowanie symulowanego urządzenia. Model urządzenia jest przechowywany w pliku JSON.

Następujące artykuły odnoszą się do bieżącego artykułu:

* [Implementowanie zachowania modelu urządzenia](iot-accelerators-device-simulation-device-behavior.md) opisano pliki języka JavaScript umożliwia Implementowanie zachowania urządzenia symulowanego.
* [Utwórz nowe urządzenie symulowane](iot-accelerators-device-simulation-create-simulated-device.md) umieszcza go wszystko ze sobą i dowiesz się, jak wdrożyć nowy typ symulowanego urządzenia do rozwiązania.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Zdefiniuj model symulowanego urządzenia przy użyciu pliku JSON
> * Określ właściwości symulowane urządzenie
> * Określ, które symulowane urządzenia wysyłają dane telemetryczne
> * Określają metody chmury do urządzenia, które odpowiada urządzenie

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób tworzenia modelu niestandardowego symulowanego urządzenia. Ten artykuł pokazuje, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Zdefiniuj model symulowanego urządzenia przy użyciu pliku JSON
> * Określ właściwości symulowane urządzenie
> * Określ, które symulowane urządzenia wysyłają dane telemetryczne
> * Określają metody chmury do urządzenia, które odpowiada urządzenie

Teraz, kiedy znasz już o schematu JSON, sugerowane następnym krokiem jest Dowiedz się, jak [Implementowanie zachowania urządzenia symulowanego](iot-accelerators-device-simulation-device-behavior.md).

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu symulacji urządzenia, zobacz [przewodnik informacyjny dla deweloperów](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
