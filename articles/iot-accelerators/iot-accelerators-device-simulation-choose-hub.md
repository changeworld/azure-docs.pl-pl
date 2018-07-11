---
title: Już istniejące Centrum IoT za pomocą rozwiązania do symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania symulacji urządzenia akceleratora rozwiązań do użycia już istniejące Centrum IoT.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967546"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Już istniejące Centrum IoT za pomocą symulacji urządzenia akceleratora rozwiązań

Podczas aprowizowania akcelerator rozwiązań symulacji urządzenia można wybrać do wdrożenia usługi IoT hub w grupie zasobów akceleratora rozwiązań do użycia w symulacji.

Jeśli nie zdecydujesz się wdrożyć opcjonalne usługi IoT Hub, należy użyć własnego Centrum dla dowolnego symulacje, po uruchomieniu. Jeśli zdecydujesz się wdrożyć opcjonalne usługi IoT Hub, można użyć tego koncentratora opcjonalne lub własnego Centrum.

Jeśli nie masz usługi IoT hub, zawsze można utworzyć nowy [witryny Azure portal](https://portal.azure.com).

Aby użyć istniejącego Centrum IoT, potrzebujesz ciąg połączenia dla **iothubowner** udostępnione zasad dostępu. Możesz uzyskać te parametry połączenia z [witryny Azure portal](https://portal.azure.com):

1. Na stronie konfiguracji Centrum w portalu, kliknij przycisk **zasady dostępu współdzielonego**.
1. Kliknij przycisk **iothubowner**.
1. Skopiuj parametry połączenia podstawowej lub dodatkowej.

[![Pobieranie parametrów połączenia](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Użyj parametrów połączenia skopiowaną po skonfigurowaniu symulacji:

[![Konfigurowanie symulacji](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku wyjaśniono sposób użycia już istniejące Centrum IoT w symulacji. Następnie możesz chcieć Dowiedz się, jak [konfigurowania modelu niestandardowego urządzenia](iot-accelerators-device-simulation-custom-model.md) symulacji.
