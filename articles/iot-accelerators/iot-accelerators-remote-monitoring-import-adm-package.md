---
title: Rozwiązania do zdalnego monitorowania Importuj pakiet — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano jak zaimportować pakiet zarządzania automatyczne urządzenia do akceleratora rozwiązania monitorowania zdalnego
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009856"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importowanie pakietu zarządzania urządzenia automatycznego Twój akcelerator rozwiązań zdalnego monitorowania

Konfiguracja zarządzania urządzeniami automatyczne definiuje zmian konfiguracji, aby wdrożyć do grupy urządzeń. W tym artykule przyjęto założenie, że deweloper w Twojej organizacji został już utworzony przez konfigurację zarządzania urządzeniami automatyczne. Aby dowiedzieć się, jak deweloper tworzy konfigurację, zobacz jeden z następujących artykułów z instrukcjami usługi IoT Hub:

- [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu witryny Azure portal](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu wiersza polecenia platformy Azure](../iot-hub/iot-hub-auto-device-config-cli.md)

Deweloper tworzy i testuje Konfiguracja zarządzania urządzeniami automatyczne w środowisku programistycznym. Gdy wszystko będzie gotowe, możesz zaimportować konfigurację w akceleratorze rozwiązanie monitorowania zdalnego.

## <a name="export-a-configuration"></a>Eksportowanie konfiguracji

Aby wyeksportować konfigurację zarządzania urządzeniami automatyczne w środowisku deweloperskim, należy użyć witryny Azure portal:

1. W witrynie Azure portal przejdź do Centrum IoT hub, używane do tworzenia i testowania urządzeń IoT. Kliknij przycisk **konfiguracji urządzenia IoT**:

    [![Konfiguracja urządzenia IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Kliknij pozycję Konfiguracja, którego chcesz użyć. **Szczegóły konfiguracji urządzenia** spowoduje wyświetlenie strony:

    [![Szczegóły konfiguracji urządzenia IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Kliknij przycisk **pobierania pliku konfiguracyjnego**:

    [![Pobierz plik konfiguracji](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Zapisz plik JSON jako plik lokalny o nazwie **configuration.json**.

Masz teraz plik, który zawiera konfigurację zarządzania urządzeniami automatyczne. W następnej sekcji można zaimportować tej konfiguracji w pakiecie do rozwiązania do zdalnego monitorowania.

## <a name="import-a-package"></a>Importowanie pakietu

Wykonaj poniższe kroki, aby zaimportować konfigurację zarządzania urządzeniami automatyczne jako pakiet do rozwiązania:

1. Przejdź do **pakietów** strony w internetowym interfejsie użytkownika zdalnego monitorowania:  ![Strona pakietów](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Kliknij przycisk **+ nowy pakiet**, wybierz **konfiguracji** typ pakietu, a następnie kliknij przycisk **Przeglądaj** wybrać **configuration.json** plik zapisany w poprzedniej sekcji:

    ![Wybierz konfigurację](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kliknij przycisk **przekazywanie** Dodaj pakiet do rozwiązania monitorowania zdalnego:

    ![Przekazany pakiet.](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Konfiguracja zarządzania urządzeniami automatyczne przekazaniu teraz jako pakiet. Na **wdrożeń** strony, można wdrożyć tego pakietu do połączonych urządzeń.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak utworzyć pakiet konfiguracji i zaimportować go do z rozwiązania do zdalnego monitorowania, następnym krokiem jest Dowiedz się, jak [urządzeń podłączonych do monitorowania zdalnego zbiorcze zarządzanie](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
