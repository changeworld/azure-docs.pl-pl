---
title: Umożliwia skonfigurowanie przekazywania pliku programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Sposób użycia poleceń cmdlet programu Azure PowerShell do konfigurowania Centrum IoT włączyć plik zostanie przesłany z połączonych urządzeń. Zawiera informacje o konfigurowaniu docelowym kontem magazynu platformy Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a4a52b6a028f4c656404e90fe05f201ac77204d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631857"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurowanie Centrum IoT przekazywania plików przy użyciu programu PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby użyć [plików funkcji przekazywania w Centrum IoT][lnk-upload], należy najpierw powiązać konta magazynu platformy Azure z Centrum IoT. Możesz użyć istniejącego konta magazynu lub Utwórz nową.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Polecenia cmdlet programu PowerShell systemu Azure][lnk-powershell-install].
* Centrum Azure IoT. Jeśli nie masz Centrum IoT, możesz użyć [polecenia cmdlet New-AzureRmIoTHub] [ lnk-powershell-iothub] można utworzyć jeden lub Portal umożliwia [tworzenia Centrum IoT][lnk-portal-hub].
* Konto usługi Azure Storage. Jeśli nie masz konta magazynu platformy Azure, możesz użyć [poleceń cmdlet programu PowerShell magazynu Azure] [ lnk-powershell-storage] można utworzyć jeden lub Portal umożliwia [Utwórz konto magazynu] [lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawić konta platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia programu PowerShell, uruchom **Connect-AzureRmAccount** polecenia cmdlet:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Jeśli masz wiele subskrypcji Azure, logowanie do platformy Azure udziela dostępu do subskrypcji platformy Azure skojarzone z poświadczeniami użytkownika. Aby wyświetlić listę dostępnych przy użyciu subskrypcji platformy Azure, użyj następującego polecenia:

    ```powershell
    Get-AzureRMSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma być używany do uruchamiania polecenia do zarządzania Centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie informacji o koncie magazynu

W następujących krokach założono, używając konta magazynu utworzone **Resource Manager** modelu wdrażania i nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywania plików z urządzeń, należy parametry połączenia dla konta magazynu platformy Azure. Konta magazynu musi być w tej samej subskrypcji co Centrum IoT. Należy również nazwę kontenera obiektów blob na koncie magazynu. Aby pobrać kluczy konta magazynu, użyj następującego polecenia:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Zanotuj **klucz1** wartość klucza konta magazynu. Należy go w kolejnych krokach.

Można użyć istniejącego kontenera obiektów blob z przekazywania plików lub Utwórz nowe:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następujących poleceń:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, należy użyć następujących poleceń:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurowanie Centrum IoT

Aby włączyć Centrum IoT można teraz skonfigurować [plików funkcji przekazywania] [ lnk-upload] przy użyciu informacji o koncie magazynu.

Konfiguracja wymaga następujących wartości:

**Kontener magazynu**: kontener obiektów blob na koncie magazynu Azure w Twojej bieżącej subskrypcji platformy Azure do skojarzenia z Centrum IoT. Możesz pobrać informacje o koncie magazynu niezbędne w poprzedniej sekcji. Centrum IoT automatycznie generuje identyfikator URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas ich przekazywania plików.

**Odbieranie powiadomień dla przekazanych plików**: Włącz lub Wyłącz powiadomienia o przekazywania plików.

**Czas wygaśnięcia SAS**: to ustawienie jest time-to-live identyfikatorów SAS zwrócony do urządzenia przez Centrum IoT. Domyślnie do godzinę.

**Plik powiadomienia, ustawienia domyślne TTL**: czas wygaśnięcia pliku Przekaż powiadomienia przed jego wygaśnięciem. Domyślnie na jeden dzień.

**Powiadomienie dostarczania maksymalna liczba plików**: liczba prób Centrum IoT dostarczyć plik Przekaż powiadomień. Domyślnie do 10.

Użyj następującego polecenia cmdlet programu PowerShell umożliwiają skonfigurowanie pliku Przekaż ustawienia Centrum IoT:

```powershell
Set-AzureRmIotHub `
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

Aby uzyskać więcej informacji na temat możliwości przekazywania plików Centrum IoT, zobacz [przekazywania plików z urządzeniem][lnk-upload].

Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu Centrum IoT Azure:

* [Zbiorcze zarządzania urządzeniami IoT][lnk-bulk]
* [Metryki Centrum IoT][lnk-metrics]
* [Operacje monitorowania][lnk-monitor]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]
* [Zabezpieczanie rozwiązania IoT od podstaw w górę][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
