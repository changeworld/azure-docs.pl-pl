---
title: Już istniejące Centrum IoT za pomocą rozwiązania do symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania symulacji urządzenia akceleratora rozwiązań do użycia już istniejące Centrum IoT.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203948"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Już istniejące Centrum IoT za pomocą symulacji urządzenia akceleratora rozwiązań

Podczas wdrażania symulacji urządzenia można także wdrożyć usługi IoT hub do użycia w symulacji. Ta opcja wdraża [Centrum IoT hub warstwy S2 o jednostce skalowania pojedynczej](../iot-hub/iot-hub-scaling.md). W przypadku wdrożenia tego opcjonalne Centrum IoT hub, można nadal docelowy inny Centrum IoT, uruchamiania symulacji.

Jeśli możesz zdecydować o niewdrażaniu opcjonalne usługi IoT Hub, należy użyć własnego Centrum dla dowolnego symulacje, po uruchomieniu.

Jeśli nie masz usługi IoT hub, zawsze można utworzyć nowy [witryny Azure portal](https://portal.azure.com).

Za pomocą istniejące Centrum IoT, potrzebne są parametry połączenia dla **iothubowner** udostępnione zasad dostępu. Możesz uzyskać te parametry połączenia z [witryny Azure portal](https://portal.azure.com):

1. Na stronie konfiguracji Centrum w portalu, kliknij przycisk **zasady dostępu współdzielonego**.

1. Kliknij przycisk **iothubowner**.

1. Skopiuj parametry połączenia podstawowej lub dodatkowej.

[![Pobieranie parametrów połączenia](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Użyj parametrów połączenia skopiowaną po skonfigurowaniu symulacji:

![Konfigurowanie symulacji](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Kolejne kroki

W tym przewodniku wyjaśniono sposób użycia już istniejące Centrum IoT w symulacji. Następnie możesz chcieć Dowiedz się, jak [tworzenia modelu urządzenia zaawansowane](iot-accelerators-device-simulation-advanced-device.md) symulacji.
