---
title: Konfigurowanie przekazywania plików do usługi IoT Hub przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować plik zostanie przesłany do usługi Azure IoT Hub przy użyciu wiersza polecenia platformy Azure dla wielu platform.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: fe6ce23b9e87235521739b7808712a9d541dabf9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048966"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurowanie usługi IoT Hub, operacje przekazywania plików przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Aby [przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md), należy najpierw powiązać konto usługi Azure Storage za pomocą usługi IoT hub. Można użyć istniejącego konta magazynu lub Utwórz nową.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Usługi Azure IoT hub. Jeśli nie masz usługi IoT hub możesz użyć [ `az iot hub create` polecenia](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) ją utworzyć lub [Tworzenie Centrum IoT przy użyciu portalu](iot-hub-create-through-portal.md).

* Konto usługi Azure Storage. Jeśli nie masz konta usługi Azure Storage, możesz użyć [wiersza polecenia platformy Azure — Zarządzanie kontami magazynu](../storage/common/storage-azure-cli.md#manage-storage-accounts) można utworzyć jedną lub korzystać z portalu do [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawianie konta platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia Uruchom [polecenie logowania](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Należy użyć następującego [polecenie, aby wyświetlić listę kont platformy Azure](https://docs.microsoft.com/cli/azure/account) dostępne do użycia:

    ```azurecli
    az account list
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, dla której chcesz używać do uruchamiania poleceń w celu utworzenia Centrum IoT hub. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Pobieranie szczegółów konta magazynu

W następujących krokach założono, utworzyć Twoje konta magazynu przy użyciu **usługi Resource Manager** modelu wdrażania przy użyciu i nie **klasycznego** modelu wdrażania.

Aby skonfigurować przekazywanie plików z urządzeń, będą potrzebne parametry połączenia konta usługi Azure storage. Konto magazynu musi być w tej samej subskrypcji co Centrum IoT hub. Należy również nazwę kontenera obiektów blob na koncie magazynu. Aby pobrać klucze konta magazynu, użyj następującego polecenia:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Zwróć uwagę na **connectionString** wartość. Potrzebny w kolejnych krokach.

Można Użyj istniejącego kontenera obiektów blob usługi przekazywania plików lub Utwórz nową:

* Aby wyświetlić listę istniejących kontenerów obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Aby utworzyć kontener obiektów blob na koncie magazynu, użyj następującego polecenia:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Przekazywanie pliku

Teraz można skonfigurować usługi IoT hub, aby włączyć możliwość [przekazywanie plików do usługi IoT hub](iot-hub-devguide-file-upload.md) przy użyciu swojego konta magazynu.

Konfiguracja wymaga następujących wartości:

* **Kontener magazynu**: Kontener obiektów blob na koncie magazynu platformy Azure w Twojej bieżącej subskrypcji platformy Azure do skojarzenia z Centrum IoT hub. Możesz pobrać informacje o koncie magazynu konieczne w poprzedniej sekcji. Centrum IoT automatycznie generuje identyfikatorów URI sygnatury dostępu Współdzielonego z uprawnieniami do zapisu do tego kontenera obiektów blob dla urządzeń do użycia podczas ich przekazywania plików.

* **Odbieraj powiadomienia dotyczące przekazywanych plików**: Włącz lub Wyłącz powiadomienia o przekazywania plików.

* **CZAS WYGAŚNIĘCIA POŁĄCZENIA SAS**: To ustawienie jest time-to-live identyfikatorów URI sygnatury dostępu Współdzielonego, zwrócone na urządzeniu przez usługę IoT Hub. Domyślnie do godzinę.

* **Plik powiadomienia, ustawienia domyślne czasu wygaśnięcia**: Czas wygaśnięcia pliku przekazywania powiadomień, przed jego wygaśnięciem. Domyślnie ustawiany na jeden dzień.

* **Plik powiadomienia maksymalna liczba prób dostarczenia**: Liczba prób Centrum IoT w celu dostarczenia pliku przekazywania powiadomień. Domyślnie do 10.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure, aby skonfigurować ustawienia przekazywania pliku w usłudze IoT hub:

<!--Robinsh this is out of date, add cloud powershell -->

W powłoce bash należy użyć:

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

Konfiguracja przekazywania plików możesz przejrzeć w Centrum IoT przy użyciu następującego polecenia:

```azurecli
az iot hub show --name {your iot hub name}
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
