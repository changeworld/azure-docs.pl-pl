---
title: Konfigurowanie przekazywania plików do usługi IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: Jak skonfigurować przekazywanie plików do usługi Azure IoT Hub przy użyciu wieloplatformowego interfejsu wiersza polecenia platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302529"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurowanie przekazywania plików usługi IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby [przekazać pliki z urządzenia,](iot-hub-devguide-file-upload.md)należy najpierw skojarzyć konto usługi Azure Storage z centrum IoT Hub. Można użyć istniejącego konta magazynu lub utworzyć nowe.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Centrum Usługi Azure IoT. Jeśli nie masz centrum IoT hub, możesz użyć [ `az iot hub create` tego polecenia,](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) aby go utworzyć lub [utworzyć centrum IoT za pomocą portalu](iot-hub-create-through-portal.md).

* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, możesz użyć interfejsu wiersza polecenia platformy Azure, aby je utworzyć. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustaw swoje konto platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia uruchom [polecenie logowania](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure](https://docs.microsoft.com/cli/azure/account) dostępnych do użycia:

    ```azurecli
    az account list
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz użyć do uruchomienia poleceń w celu utworzenia centrum IoT Hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie szczegółów konta magazynu

W poniższych krokach przyjęto założenie, że konto magazynu zostało utworzone przy użyciu modelu wdrażania **Menedżera zasobów,** a nie modelu wdrażania **klasycznego.**

Aby skonfigurować przekazywanie plików z urządzeń, potrzebujesz ciągu połączenia dla konta magazynu platformy Azure. Konto magazynu musi znajdować się w tej samej subskrypcji co centrum IoT Hub. Potrzebna jest również nazwa kontenera obiektów blob na koncie magazynu. Aby pobrać klucze konta magazynu, użyj następującego polecenia:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Zanotuj wartość **connectionString.** Jest to potrzebne w poniższych krokach.

Można użyć istniejącego kontenera obiektów blob do przekazywania plików lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Przekazywanie plików

Teraz można skonfigurować centrum IoT, aby umożliwić [przekazywanie plików do centrum IoT przy](iot-hub-devguide-file-upload.md) użyciu szczegółów konta magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu:** kontener obiektów blob na koncie usługi Azure storage w bieżącej subskrypcji platformy Azure, aby skojarzyć z centrum IoT hub. Pobrano niezbędne informacje o koncie magazynu w poprzedniej sekcji. Usługa IoT Hub automatycznie generuje identyfikatory URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas przekazywania plików.

* **Otrzymuj powiadomienia o przesłanych plikach**: Włącz lub wyłącz powiadomienia o przekazywaniu plików.

* **Czas wygaśnięcia SAS:** To ustawienie jest czas wygaśnięcia identyfikatorów URI sygnatury dostępu Współdzielonego zwróconych do urządzenia przez centrum IoT Hub. Domyślnie ustawiona na jedną godzinę.

* **Domyślne czasy powiadomienia**o pliku: Czas wygaśnięcia powiadomienia o przekazywaniu pliku przed jego wygaśnięciem. Domyślnie ustawiona na jeden dzień.

* **Maksymalna liczba dostarczenia powiadomień o plikach:** liczba prób dostarczenia powiadomienia o przekazywaniu pliku przez centrum IoT Hub. Domyślnie ustawiono wartość 10.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby skonfigurować ustawienia przekazywania plików w centrum IoT hub:

<!--Robinsh this is out of date, add cloud powershell -->

W powłoce bash użyj:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Konfigurację przekazywania plików w centrum IoT hub można przejrzeć za pomocą następującego polecenia:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat możliwości przekazywania plików w centrum IoT Hub, zobacz [Przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md).

Skorzystaj z tych łączy, aby dowiedzieć się więcej o zarządzaniu usługą Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpiecz swoje rozwiązanie IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)
