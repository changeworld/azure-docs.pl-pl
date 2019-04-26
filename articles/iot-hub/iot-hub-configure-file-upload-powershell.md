---
title: Konfigurowanie przekazywania plików za pomocą programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Jak używać poleceń cmdlet programu Azure PowerShell do konfigurowania usługi IoT hub, aby włączyć plik zostanie przesłany z połączonych urządzeń. Zawiera informacje na temat konfigurowania miejsca docelowego konta magazynu platformy Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318464"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurowanie usługi IoT Hub, operacje przekazywania plików przy użyciu programu PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby użyć [pliku funkcję przekazywania w usłudze IoT Hub](iot-hub-devguide-file-upload.md), należy najpierw powiązać konto magazynu platformy Azure za pomocą usługi IoT hub. Można użyć istniejącego konta magazynu lub Utwórz nową.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Polecenia cmdlet programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Usługi Azure IoT hub. Jeśli nie masz usługi IoT hub możesz użyć [polecenia cmdlet New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) można utworzyć jedną lub korzystać z portalu do [Tworzenie Centrum IoT](iot-hub-create-through-portal.md).

* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure storage, możesz użyć [poleceń cmdlet programu PowerShell z magazynu Azure](https://docs.microsoft.com/powershell/module/az.storage/) można utworzyć jedną lub korzystać z portalu do [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawianie konta platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia programu PowerShell Uruchom **Connect AzAccount** polecenia cmdlet:

    ```powershell
    Connect-AzAccount
    ```

2. Jeśli masz wiele subskrypcji platformy Azure, logowanie do platformy Azure zapewnia dostęp do wszystkich subskrypcji platformy Azure skojarzonych z poświadczeniami użytkownika. Aby wyświetlić listę subskrypcji platformy Azure, która jest dostępna do użycia, użyj następującego polecenia:

    ```powershell
    Get-AzSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, dla której chcesz użyć, aby uruchomić polecenia do zarządzania Centrum IoT hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie szczegółów konta magazynu

W następujących krokach założono, utworzyć Twoje konta magazynu przy użyciu **usługi Resource Manager** modelu wdrażania przy użyciu i nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywanie plików z urządzeń, będą potrzebne parametry połączenia konta usługi Azure storage. Konto magazynu musi być w tej samej subskrypcji co Centrum IoT hub. Należy również nazwę kontenera obiektów blob na koncie magazynu. Aby pobrać klucze konta magazynu, użyj następującego polecenia:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Zwróć uwagę na **klucz1** wartość klucza konta magazynu. Potrzebny w kolejnych krokach.

Można użyć istniejącego kontenera obiektów blob dla przekazywanie plików lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następujących poleceń:

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

## <a name="configure-your-iot-hub"></a>Konfigurowanie usługi IoT hub

Teraz można skonfigurować usługi IoT hub i [przekazywanie plików do usługi IoT hub](iot-hub-devguide-file-upload.md) przy użyciu swojego konta magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu**: Kontener obiektów blob na koncie magazynu platformy Azure w Twojej bieżącej subskrypcji platformy Azure do skojarzenia z Centrum IoT hub. Możesz pobrać informacje o koncie magazynu konieczne w poprzedniej sekcji. Centrum IoT automatycznie generuje identyfikatorów URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas ich przekazywania plików.

* **Odbieraj powiadomienia dotyczące przekazywanych plików**: Włącz lub Wyłącz powiadomienia o przekazywania plików.

* **CZAS WYGAŚNIĘCIA POŁĄCZENIA SAS**: To ustawienie jest time-to-live identyfikatorów URI sygnatury dostępu Współdzielonego, zwrócone na urządzeniu przez usługę IoT Hub. Domyślnie do godzinę.

* **Plik powiadomienia, ustawienia domyślne czasu wygaśnięcia**: Czas wygaśnięcia pliku przekazywania powiadomień, przed jego wygaśnięciem. Domyślnie ustawiany na jeden dzień.

* **Plik powiadomienia maksymalna liczba prób dostarczenia**: Liczba prób Centrum IoT w celu dostarczenia pliku przekazywania powiadomień. Domyślnie do 10.

Użyj następującego polecenia cmdlet PowerShell, aby skonfigurować plik przekazać ustawienia Centrum IoT:

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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat funkcji przekazywania plików usługi IoT Hub, zobacz [przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md).

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT](iot-hub-bulk-identity-mgmt.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Monitorowanie operacji](iot-hub-operations-monitoring.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Zabezpieczać rozwiązanie IoT od podstaw w górę](../iot-fundamentals/iot-security-ground-up.md)
