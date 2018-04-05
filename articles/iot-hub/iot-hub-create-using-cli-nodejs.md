---
title: Tworzenie Centrum IoT przy użyciu wiersza polecenia platformy Azure (azure.js) | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum Azure IoT przy użyciu wiersza polecenia platformy Azure i platform (azure.js).
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 7e761ba66f4b28dd7cc59dc8998c23810dd2b7d1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Tworzenie Centrum IoT przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Wprowadzenie

Tworzenie i zarządzanie nimi centra Azure IoT programowo, można użyć wiersza polecenia platformy Azure (azure.js). W tym artykule przedstawiono sposób użycia interfejsu wiersza polecenia Azure (azure.js) do tworzenia Centrum IoT.

Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

* Azure CLI (azure.js) — interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania modeli wdrażania zgodnie z opisem w tym artykule.
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) — generacji interfejsu wiersza polecenia do zarządzania model wdrażania zasobów.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure CLI 0.10.4] [ lnk-CLI-install] lub nowszym. Jeśli masz już zainstalowany interfejsu wiersza polecenia Azure, możesz sprawdzić bieżącą wersję za pomocą następującego polecenia w wierszu polecenia:

```azurecli
azure --version
```

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Azure CLI musi być w trybie Azure Resource Manager:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Ustawianie konta i subskrypcji platformy Azure

1. W wierszu polecenia, logowania, wpisując następujące polecenie:

   ```azurecli
    azure login
   ```

   Użyj przeglądarki sieci web sugerowane i kod do uwierzytelniania.
1. Jeśli masz wiele subskrypcji Azure, nawiązywania Azure udziela dostępu do subskrypcji platformy Azure skojarzone z poświadczeń. Wyświetlanie subskrypcji platformy Azure i określenie, co jest ustawieniem domyślnym, za pomocą polecenia:

   ```azurecli
    azure account list
   ```

   Aby ustawić kontekst subskrypcji, w którym chcesz uruchomić rest, użyj polecenia:

   ```azurecli
    azure account set <subscription name>
   ```

1. Jeśli nie masz grupy zasobów, można utworzyć jedną o nazwie **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Artykuł [użyć wiersza polecenia platformy Azure do zarządzania zasobami Azure i grup zasobów] [ lnk-CLI-arm] zawiera więcej informacji o sposobie używania interfejsu wiersza polecenia Azure do zarządzania zasobami Azure.

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT

Wymagane parametry:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **Grupa zasobów**. Nazwa grupy zasobów. Format jest bez uwzględniania wielkości liter alfanumeryczne, podkreślenia i łączniki, długość 1-64.
* **name**. Nazwa centrum IoT, który ma zostać utworzony. Format jest rozróżniana wielkość znaków alfanumerycznych i łącznik, 3 – 50 długości.
* **Lokalizacja**. Lokalizacja (region/centrum danych azure) do obsługi administracyjnej Centrum IoT.
* **Nazwa jednostki SKU**. Nazwa jednostki sku, jeden z: [F1, S1, S2, S3]. Aby uzyskać szczegółowe informacje o każdej jednostki sku, zobacz [cennik Centrum IoT Azure](https://azure.microsoft.com/pricing/details/iot-hub/). Obecnie warstwy podstawowej są dostępne tylko za pośrednictwem portalu. 
* **jednostki**. Liczba jednostek elastycznie. Aby uzyskać więcej informacji dotyczących ograniczeń jednostki, zobacz [cennik Centrum IoT Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Aby wyświetlić wszystkie dostępne na potrzeby tworzenia parametry, można użyć polecenia Pomoc w wierszu polecenia:

```azurecli
azure iothub create -h
```

Prosty przykład: do tworzenia Centrum IoT o nazwie **exampleIoTHubName** w grupie zasobów **exampleResourceGroup**, uruchom następujące polecenie:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> To polecenie interfejsu wiersza polecenia Azure umożliwia utworzenie Centrum IoT standardowe S1 dla której są rozliczane. Centrum IoT można usunąć **exampleIoTHubName** za pomocą następujących poleceń:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz następujący artykuł:

* [Zestawy SDK IoT][lnk-sdks]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przy użyciu portalu Azure do zarządzania Centrum IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
