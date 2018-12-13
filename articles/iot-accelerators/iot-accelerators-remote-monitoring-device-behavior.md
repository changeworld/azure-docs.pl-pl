---
title: Symulowane zachowanie urządzenia w rozwiązaniu do monitorowania zdalnego — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zdefiniować zachowanie symulowanego urządzenia w rozwiązaniu do zdalnego monitorowania przy użyciu języka JavaScript.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 70f9ccbbe737bad4d6f88365e804d4421c418d28
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164011"
---
# <a name="implement-the-device-model-behavior"></a>Implementowanie zachowania modelu urządzenia

Artykuł [zrozumieć schematu modelu urządzenia](iot-accelerators-remote-monitoring-device-schema.md) opisano schemat, który definiuje model urządzenia symulowanego. Artykuł z określonych dwa rodzaje plików JavaScript implementujące zachowanie symulowanego urządzenia.

- **Stan** pliki JavaScript, uruchamianą w ustalonych odstępach czasu, aby zaktualizować stan wewnętrzny urządzenia.
- **Metoda** pliki JavaScript, który uruchamiany, gdy rozwiązanie wywołuje metodę na urządzeniu.

W tym artykule omówiono sposób wykonywania następujących zadań:

>[!div class="checklist"]
> * Kontrola stanu symulowanego urządzenia
> * Zdefiniuj, jak symulowane urządzenie odpowiada na wywołanie metody rozwiązania do zdalnego monitorowania
> * Debugowanie skryptów

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano sposób definiowania zachowania modelu niestandardowego symulowanego urządzenia. Ten artykuł pokazuje, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrola stanu symulowanego urządzenia
> * Zdefiniuj, jak symulowane urządzenie odpowiada na wywołanie metody rozwiązania do zdalnego monitorowania
> * Debugowanie skryptów

Teraz, gdy wyjaśniono sposób określić zachowanie symulowane urządzenie, sugerowane następnym krokiem jest Dowiedz się, jak [utworzyć symulowane urządzenie](iot-accelerators-remote-monitoring-create-simulated-device.md).

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
