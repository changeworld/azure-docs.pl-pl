---
title: Konfigurowanie przekazywania plików za pomocą programu Azure PowerShell | Dokumenty firmy Microsoft
description: Jak używać poleceń cmdlet programu Azure PowerShell do konfigurowania centrum IoT hub, aby włączyć przekazywanie plików z podłączonych urządzeń. Zawiera informacje dotyczące konfigurowania docelowego konta magazynu platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60318464"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurowanie przekazywania plików centrum IoT przy użyciu programu PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby użyć [funkcji przekazywania plików w centrum IoT Hub,](iot-hub-devguide-file-upload.md)należy najpierw skojarzyć konto magazynu platformy Azure z centrum IoT Hub. Można użyć istniejącego konta magazynu lub utworzyć nowe.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Polecenia cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Centrum Usługi Azure IoT. Jeśli nie masz centrum IoT hub, możesz użyć [polecenia cmdlet New-AzIoTHub,](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) aby go utworzyć lub użyć portalu do [utworzenia centrum IoT](iot-hub-create-through-portal.md)hub .

* Konto usługi Azure Storage. Jeśli nie masz konta magazynu platformy Azure, możesz użyć [poleceń cmdlet programu Azure Storage PowerShell,](https://docs.microsoft.com/powershell/module/az.storage/) aby je utworzyć lub użyć portalu do [utworzenia konta magazynu](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustaw swoje konto platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu programu PowerShell uruchom polecenie cmdlet **Connect-AzAccount:**

    ```powershell
    Connect-AzAccount
    ```

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się na platformie Azure zapewnia dostęp do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami. Użyj następującego polecenia, aby wyświetlić listę subskrypcji platformy Azure dostępnych do użycia:

    ```powershell
    Get-AzSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, której chcesz użyć do uruchomienia poleceń do zarządzania centrum IoT hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie szczegółów konta magazynu

W poniższych krokach przyjęto założenie, że konto magazynu zostało utworzone przy użyciu modelu wdrażania **Menedżera zasobów,** a nie modelu wdrażania **klasycznego.**

Aby skonfigurować przekazywanie plików z urządzeń, potrzebujesz ciągu połączenia dla konta magazynu platformy Azure. Konto magazynu musi znajdować się w tej samej subskrypcji co centrum IoT Hub. Potrzebna jest również nazwa kontenera obiektów blob na koncie magazynu. Aby pobrać klucze konta magazynu, użyj następującego polecenia:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Zanotuj wartość klucza konta magazynu **key1.** Jest to potrzebne w poniższych krokach.

Można użyć istniejącego kontenera obiektów blob do przekazywania plików lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, należy użyć następujących poleceń:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, użyj następujących poleceń:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurowanie centrum IoT

Teraz możesz skonfigurować centrum IoT hub do [przekazywania plików do centrum IoT przy](iot-hub-devguide-file-upload.md) użyciu szczegółów konta magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu:** kontener obiektów blob na koncie usługi Azure storage w bieżącej subskrypcji platformy Azure, aby skojarzyć z centrum IoT hub. Pobrano niezbędne informacje o koncie magazynu w poprzedniej sekcji. Usługa IoT Hub automatycznie generuje identyfikatory URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas przekazywania plików.

* **Otrzymuj powiadomienia o przesłanych plikach**: Włącz lub wyłącz powiadomienia o przekazywaniu plików.

* **Czas wygaśnięcia SAS:** To ustawienie jest czas wygaśnięcia identyfikatorów URI sygnatury dostępu Współdzielonego zwróconych do urządzenia przez centrum IoT Hub. Domyślnie ustawiona na jedną godzinę.

* **Domyślne czasy powiadomienia**o pliku: Czas wygaśnięcia powiadomienia o przekazywaniu pliku przed jego wygaśnięciem. Domyślnie ustawiona na jeden dzień.

* **Maksymalna liczba dostarczenia powiadomień o plikach:** liczba prób dostarczenia powiadomienia o przekazywaniu pliku przez centrum IoT Hub. Domyślnie ustawiono wartość 10.

Użyj następującego polecenia cmdlet programu PowerShell, aby skonfigurować ustawienia przekazywania plików w centrum IoT hub:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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
