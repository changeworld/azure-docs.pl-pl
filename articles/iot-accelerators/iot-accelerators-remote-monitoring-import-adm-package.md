---
title: Pakiet importu rozwiązania do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób importowania automatycznego pakietu zarządzania urządzeniami do akceleratora rozwiązań zdalnego monitorowania
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443449"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importowanie automatycznego pakietu zarządzania urządzeniami do akceleratora rozwiązań do zdalnego monitorowania

Automatyczna konfiguracja zarządzania urządzeniami definiuje zmiany konfiguracji do wdrożenia w grupie urządzeń. W tym artykule założono, że deweloper w organizacji utworzył już automatyczną konfigurację zarządzania urządzeniami. Aby dowiedzieć się, jak deweloper tworzy konfigurację, zobacz jeden z następujących artykułów infiguracyjnych usługi IoT Hub:

- [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu witryny Azure portal](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-auto-device-config-cli.md)

Deweloper tworzy i testuje automatyczną konfigurację zarządzania urządzeniami w środowisku deweloperskim. Gdy będziesz gotowy, możesz zaimportować konfigurację do akceleratora rozwiązań do zdalnego monitorowania.

## <a name="export-a-configuration"></a>Eksportowanie konfiguracji

Użyj witryny Azure portal, aby wyeksportować konfigurację automatycznego zarządzania urządzeniami ze środowiska deweloperskiego:

1. W witrynie Azure portal przejdź do centrum IoT hub, którego używasz do tworzenia i testowania urządzeń IoT. Kliknij pozycję **Konfiguracja urządzenia IoT:**

    [![Konfiguracja urządzenia IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Kliknij konfigurację, której chcesz użyć. Zostanie wyświetlona strona **Szczegóły konfiguracji urządzenia:**

    [![Szczegóły konfiguracji urządzenia IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Kliknij **pobierz plik konfiguracyjny**:

    [![Pobierz plik konfiguracyjny](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Zapisz plik JSON jako plik lokalny o nazwie **configuration.json**.

Teraz masz plik, który zawiera automatyczną konfigurację zarządzania urządzeniami. W następnej sekcji zaimportujesz tę konfigurację jako pakiet do rozwiązania do zdalnego monitorowania.

## <a name="import-a-package"></a>Importowanie pakietu

Wykonaj poniższe czynności, aby zaimportować do rozwiązania automatyczną konfigurację zarządzania urządzeniami jako pakiet:

1. Przejdź do strony **Pakiety** na stronie ![Interfejs użytkownika sieci Web zdalnego monitorowania: pakiety](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Kliknij **przycisk + Nowy pakiet**, wybierz pozycję **Konfiguracja** jako typ pakietu, a następnie kliknij przycisk **Przeglądaj,** aby wybrać plik **configuration.json** zapisany w poprzedniej sekcji:

    ![Wybierz konfigurację](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Kliknij **przycisk Przekaż,** aby dodać pakiet do rozwiązania do zdalnego monitorowania:

    ![Przesłany pakiet](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Teraz prześlesz konfigurację automatycznego zarządzania urządzeniami jako pakiet. Na stronie **Wdrożenia** można wdrożyć ten pakiet na podłączonych urządzeniach.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak utworzyć pakiet konfiguracyjny i zaimportować go do rozwiązania zdalnego monitorowania, następnym krokiem jest nauczenie się [zbiorczego zarządzania urządzeniami podłączonymi do zdalnego monitorowania.](iot-accelerators-remote-monitoring-bulk-configuration-update.md)
