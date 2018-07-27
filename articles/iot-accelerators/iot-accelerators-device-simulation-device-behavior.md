---
title: Symulowane zachowanie urządzenia w rozwiązaniu do symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zdefiniować zachowanie symulowanego urządzenia w rozwiązaniu do symulowania urządzenia przy użyciu języka JavaScript.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b68550bce1f4e3fbe3845c21598720083c8e384c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285761"
---
# <a name="implement-the-device-model-behavior"></a>Implementowanie zachowania modelu urządzenia

Artykuł [zrozumieć schematu modelu urządzenia](iot-accelerators-device-simulation-device-schema.md) opisano schemat, który definiuje model urządzenia symulowanego. Artykuł z określonych dwa rodzaje plików JavaScript implementujące zachowanie symulowanego urządzenia.

- **Stan**: pliki JavaScript, uruchamianą w ustalonych odstępach czasu, aby zaktualizować stan wewnętrzny urządzenia.
- **Metoda**: pliki JavaScript, który uruchamiany, gdy rozwiązanie wywołuje metodę na urządzeniu.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Kontrola stanu symulowanego urządzenia
> * Zdefiniuj, jak symulowane urządzenie odpowiada na wywołanie metody z usługi IoT hub, który jest podłączony do
> * Debugowanie skryptów

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób definiowania zachowania modelu niestandardowego symulowanego urządzenia. Ten artykuł pokazuje, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrola stanu symulowanego urządzenia
> * Zdefiniuj, jak symulowane urządzenie odpowiada na wywołanie metody z usługi IoT hub, który jest podłączony do
> * Debugowanie skryptów

Teraz, gdy wyjaśniono sposób określić zachowanie symulowane urządzenie, sugerowane następnym krokiem jest Dowiedz się, jak [utworzyć symulowane urządzenie](iot-accelerators-remote-monitoring-test.md).

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu symulacji urządzenia, zobacz [przewodnik informacyjny dla deweloperów](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
