---
title: Konfigurowanie przekazywania plików do usługi IoT Hub przy użyciu wiersza polecenia platformy Azure (az.py) | Dokumentacja firmy Microsoft
description: Jak skonfigurować fileuploads do usługi Azure IoT Hub przy użyciu dla wielu platform Azure CLI 2.0 (az.py).
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0eac620d44967827f7703da9cf409703a123ab07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460198"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurowanie usługi IoT Hub, operacje przekazywania plików przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby użyć [pliku funkcję przekazywania w usłudze IoT Hub][lnk-upload], należy najpierw powiązać konto usługi Azure Storage za pomocą usługi IoT hub. Można użyć istniejącego konta magazynu lub Utwórz nową.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure CLI 2.0][lnk-CLI-install].
* Usługi Azure IoT hub. Jeśli nie masz usługi IoT hub możesz użyć `az iot hub create` [polecenia] [ lnk-cli-create-iothub] można utworzyć jedną lub [tworzenie Centrum IoT hub] za pomocą portalu [lnk-portal-hub].
* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, możesz użyć [Azure CLI 2.0 — zarządzanie kontami magazynu] [ lnk-manage-storage] można utworzyć jedną lub korzystać z portalu do [Tworzenie konta magazynu] [ lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawianie konta platformy Azure

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

## <a name="retrieve-your-storage-account-details"></a>Pobieranie szczegółów konta magazynu

W następujących krokach założono, utworzyć Twoje konta magazynu przy użyciu **usługi Resource Manager** modelu wdrażania przy użyciu i nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywanie plików z urządzeń, będą potrzebne parametry połączenia konta usługi Azure storage. Konto magazynu musi być w tej samej subskrypcji co Centrum IoT hub. Należy również nazwę kontenera obiektów blob na koncie magazynu. Aby pobrać klucze konta magazynu, użyj następującego polecenia:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Zwróć uwagę na **connectionString** wartość. Potrzebny w kolejnych krokach.

Można użyć istniejącego kontenera obiektów blob dla przekazywanie plików lub utworzyć nowy:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Przekazywanie pliku

Teraz można skonfigurować usługi IoT hub, aby umożliwić [funkcję przekazywania pliku] [ lnk-upload] przy użyciu swojego konta magazynu.

Konfiguracja wymaga następujących wartości:

**Kontener magazynu**: kontener obiektów blob na koncie magazynu platformy Azure w Twojej bieżącej subskrypcji platformy Azure do skojarzenia z Centrum IoT hub. Możesz pobrać informacje o koncie magazynu konieczne w poprzedniej sekcji. Centrum IoT automatycznie generuje identyfikatorów URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas ich przekazywania plików.

**Odbieraj powiadomienia dotyczące przekazywanych plików**: Włącza lub wyłącza powiadomienia przekazywania plików.

**Czas wygaśnięcia połączenia SAS**: to ustawienie jest time-to-live identyfikatorów URI sygnatury dostępu Współdzielonego, zwrócone na urządzeniu przez usługę IoT Hub. Domyślnie do godzinę.

**Plik powiadomienia, ustawienia domyślne czasu wygaśnięcia**: czas wygaśnięcia pliku przekazywania powiadomień, przed jego wygaśnięciem. Domyślnie ustawiany na jeden dzień.

**Plik powiadomienia maksymalna liczba prób dostarczenia**: liczba prób Centrum IoT w celu dostarczenia pliku przekazywania powiadomień. Domyślnie do 10.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby skonfigurować ustawienia przekazywania pliku w usłudze IoT hub:

Użycie powłoki bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Przy użyciu wiersza polecenia Windows:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Konfiguracja przekazywania plików możesz przejrzeć w Centrum IoT przy użyciu następującego polecenia:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat funkcji przekazywania plików usługi IoT Hub, zobacz [przekazywanie plików z urządzenia][lnk-upload].

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Zbiorcze zarządzanie urządzeniami IoT][lnk-bulk]
* [Metryki usługi IoT Hub][lnk-metrics]
* [Monitorowanie operacji][lnk-monitor]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]
* [Zabezpieczać rozwiązanie IoT od podstaw w górę][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: /azure/iot-fundamentals/iot-security-ground-up


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
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create