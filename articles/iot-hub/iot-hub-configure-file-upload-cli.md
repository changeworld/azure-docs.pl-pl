---
title: Konfigurowanie przekazywania pliku z Centrum IoT przy użyciu wiersza polecenia platformy Azure (az.py) | Dokumentacja firmy Microsoft
description: Jak skonfigurować fileuploads z Centrum IoT Azure przy użyciu interfejsu wiersza polecenia Azure i platform w 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 378fb06f7ac9cbb6dc645994682786f474077d03
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633856"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurowanie Centrum IoT przekazywania plików przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby użyć [plików funkcji przekazywania w Centrum IoT][lnk-upload], należy najpierw powiązać konta usługi Azure Storage z Centrum IoT. Możesz użyć istniejącego konta magazynu lub Utwórz nową.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure CLI 2.0][lnk-CLI-install].
* Centrum Azure IoT. Jeśli nie masz Centrum IoT, możesz użyć `az iot hub create` [polecenia] [ lnk-cli-create-iothub] można utworzyć jeden lub za pomocą portalu [tworzenia Centrum IoT] [lnk-portal Centrum].
* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, możesz użyć [2.0 interfejsu wiersza polecenia platformy Azure — Zarządzanie kontami magazynu] [ lnk-manage-storage] można utworzyć jeden lub Portal umożliwia [Utwórz konto magazynu][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawić konta platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia uruchom [polecenie logowania][lnk-login-command]:

    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

1. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure][lnk-az-account-command] dostępnych do użycia:

    ```azurecli
    az account list
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie informacji o koncie magazynu

W następujących krokach założono, używając konta magazynu utworzone **Resource Manager** modelu wdrażania i nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywania plików z urządzeń, należy parametry połączenia dla konta magazynu platformy Azure. Konta magazynu musi być w tej samej subskrypcji co Centrum IoT. Należy również nazwę kontenera obiektów blob na koncie magazynu. Aby pobrać kluczy konta magazynu, użyj następującego polecenia:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Zanotuj **connectionString** wartość. Należy go w kolejnych krokach.

Można użyć istniejącego kontenera obiektów blob z przekazywania plików lub Utwórz nowe:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Przekazywanie pliku

Aby włączyć Centrum IoT można teraz skonfigurować [plików funkcji przekazywania] [ lnk-upload] przy użyciu informacji o koncie magazynu.

Konfiguracja wymaga następujących wartości:

**Kontener magazynu**: kontener obiektów blob na koncie magazynu Azure w Twojej bieżącej subskrypcji platformy Azure do skojarzenia z Centrum IoT. Możesz pobrać informacje o koncie magazynu niezbędne w poprzedniej sekcji. Centrum IoT automatycznie generuje identyfikator URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas ich przekazywania plików.

**Odbieranie powiadomień dla przekazanych plików**: Włącz lub Wyłącz powiadomienia o przekazywania plików.

**Czas wygaśnięcia SAS**: to ustawienie jest time-to-live identyfikatorów SAS zwrócony do urządzenia przez Centrum IoT. Domyślnie do godzinę.

**Plik powiadomienia, ustawienia domyślne TTL**: czas wygaśnięcia pliku Przekaż powiadomienia przed jego wygaśnięciem. Domyślnie na jeden dzień.

**Powiadomienie dostarczania maksymalna liczba plików**: liczba prób Centrum IoT dostarczyć plik Przekaż powiadomień. Domyślnie do 10.

Użyj następujących poleceń interfejsu wiersza polecenia Azure, aby skonfigurować ustawienia przekazywania pliku w Centrum IoT:

Użycie powłoki bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

W przypadku systemu Windows użyj wiersza polecenia:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Możesz przejrzeć konfigurację przekazywania pliku, na Centrum IoT przy użyciu następującego polecenia:

```azurecli
az iot hub show --name {your iot hub name}
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

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create