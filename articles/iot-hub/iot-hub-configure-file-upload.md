---
title: Konfigurowanie przekazywania plików za pomocą portalu Azure | Dokumenty firmy Microsoft
description: Jak korzystać z witryny Azure portal, aby skonfigurować centrum IoT hub, aby włączyć przekazywanie plików z podłączonych urządzeń. Zawiera informacje dotyczące konfigurowania docelowego konta magazynu platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735057"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurowanie przekazywania plików w usłudze IoT Hub przy użyciu witryny Azure Portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Przekazywanie plików

Aby użyć [funkcji przekazywania plików w centrum IoT Hub,](iot-hub-devguide-file-upload.md)należy najpierw skojarzyć konto usługi Azure Storage z centrum. Wybierz **opcję Przekazywanie plików,** aby wyświetlić listę właściwości przekazywania plików dla modyfikowanego centrum IoT Hub.

![Wyświetlanie ustawień przekazywania plików w centrum IoT w portalu](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Kontener magazynu:** Użyj witryny Azure Portal, aby wybrać kontener obiektów blob na koncie usługi Azure Storage w bieżącej subskrypcji platformy Azure, aby skojarzyć z centrum IoT Hub. W razie potrzeby można utworzyć konto usługi Azure Storage w kontenerze **konta magazynu** i obiektu blob na **kontenerach.** Usługa IoT Hub automatycznie generuje identyfikatory URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas przekazywania plików.

   ![Wyświetlanie kontenerów magazynu do przekazywania plików w portalu](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Otrzymuj powiadomienia o przesłanych plikach**: Włącz lub wyłącz powiadomienia o przekazywaniu plików za pomocą przełącznika.

* **Czas wygaśnięcia SAS:** To ustawienie jest czas wygaśnięcia identyfikatorów URI sygnatury dostępu Współdzielonego zwróconych do urządzenia przez centrum IoT Hub. Domyślnie ustawiona na jedną godzinę, ale można dostosować do innych wartości za pomocą suwaka.

* **Domyślne czasy powiadomienia**o pliku: Czas wygaśnięcia powiadomienia o przekazywaniu pliku przed jego wygaśnięciem. Domyślnie ustawiona na jeden dzień, ale można dostosować do innych wartości za pomocą suwaka.

* **Maksymalna liczba dostarczenia powiadomień o plikach:** liczba prób dostarczenia powiadomienia o przekazywaniu pliku przez centrum IoT Hub. Domyślnie ustawiona wartość 10, ale można ją dostosować do innych wartości za pomocą suwaka.

   ![Konfigurowanie przekazywania plików w centrum IoT w portalu](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat możliwości przekazywania plików usługi IoT Hub, zobacz [Przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md) w przewodniku dla deweloperów usługi IoT Hub.

Skorzystaj z tych łączy, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpiecz swoje rozwiązanie IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)
