---
title: Konfigurowanie przekazywania plików za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: Sposób konfigurowania usługi IoT hub, aby włączyć przekazywanie plików z połączonych urządzeń przy użyciu witryny Azure portal. Zawiera informacje na temat konfigurowania miejsca docelowego konta magazynu platformy Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: a9f9eeaed2716c5d492099568fd6f90080471af2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42059255"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurowanie usługi IoT Hub, operacje przekazywania plików przy użyciu witryny Azure portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Przekazywanie pliku

Aby użyć [pliku funkcję przekazywania w usłudze IoT Hub](iot-hub-devguide-file-upload.md), należy najpierw powiązać konto usługi Azure Storage w Centrum. Wybierz **przekazywanie pliku** do wyświetlania listy właściwości przekazywania plików dla usługi IoT hub, która jest modyfikowana.

![Przekazywanie plików usługi IoT Hub widoku ustawień w portalu](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Kontener magazynu**: Użyj witryny Azure portal, aby wybrać kontener obiektów blob na koncie magazynu platformy Azure w Twojej bieżącej subskrypcji platformy Azure do skojarzenia z Centrum IoT Hub. Jeśli to konieczne, możesz utworzyć konto usługi Azure Storage na **kont magazynu** bloku i obiektów blob kontenera na **kontenery** bloku. Centrum IoT automatycznie generuje identyfikatorów URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas ich przekazywania plików.

   ![Wyświetl kontenery magazynu w celu przekazania pliku w portalu](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Odbieraj powiadomienia dotyczące przekazywanych plików**: Włącza lub wyłącza powiadomienia przekazywanie plików za pośrednictwem przełącznika.

* **Czas wygaśnięcia połączenia SAS**: to ustawienie jest time-to-live identyfikatorów URI sygnatury dostępu Współdzielonego, zwrócone na urządzeniu przez usługę IoT Hub. Domyślnie ustawiony na jedną godzinę, ale można dostosować do innych wartości przy użyciu suwaka.

* **Plik powiadomienia, ustawienia domyślne czasu wygaśnięcia**: czas wygaśnięcia pliku przekazywania powiadomień, przed jego wygaśnięciem. Domyślnie ustawiany na jeden dzień, ale można dostosować do innych wartości przy użyciu suwaka.

* **Plik powiadomienia maksymalna liczba prób dostarczenia**: liczba prób Centrum IoT w celu dostarczenia pliku przekazywania powiadomień. Domyślnie ustawiony na 10, ale można dostosować do innych wartości przy użyciu suwaka.

   ![Konfigurowanie przekazywania plików Centrum IoT w portalu](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat funkcji przekazywania plików usługi IoT Hub, zobacz [przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md) w przewodniku dla deweloperów usługi IoT Hub.

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpieczać rozwiązanie IoT od podstaw w górę](../iot-fundamentals/iot-security-ground-up.md)
