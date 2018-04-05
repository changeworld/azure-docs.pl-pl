---
title: Tworzenie Centrum IoT przy użyciu wiersza polecenia platformy Azure (az.py) | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum Azure IoT przy użyciu interfejsu wiersza polecenia Azure i platform w 2.0 (az.py).
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: c89ecc31754d355d6469d54dcd45e1cc2ef106f0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Tworzenie Centrum IoT przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Wprowadzenie

Azure CLI 2.0 (az.py) umożliwia tworzenie i zarządzanie nimi centra Azure IoT programowo. W tym artykule przedstawiono sposób umożliwiają utworzenie Centrum IoT Azure CLI 2.0 (az.py).

Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) — interfejsu wiersza polecenia dla modeli wdrażania zarządzania classic i zasobów.
* Azure CLI 2.0 (az.py) - generacji interfejsu wiersza polecenia do zarządzania model wdrażania zasobów zgodnie z opisem w tym artykule.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Zaloguj się i ustawić konta platformy Azure

Zaloguj się do konta platformy Azure i wybierz subskrypcję.

1. W wierszu polecenia uruchom [polecenie logowania][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Postępuj zgodnie z instrukcjami w celu uwierzytelnienia przy użyciu kodu i zaloguj się na konto platformy Azure za pośrednictwem przeglądarki internetowej.

2. Jeśli masz wiele subskrypcji platformy Azure, zalogowanie się do platformy Azure zapewnia dostęp do wszystkich kont platformy Azure skojarzonych z poświadczeniami użytkownika. Użyj następującego [polecenia, aby wyświetlić listę kont platformy Azure][lnk-az-account-command] dostępnych do użycia:
    
    ```azurecli
    az account list 
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma zostać użyta do uruchamiania poleceń w celu utworzenia centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT

Użyj wiersza polecenia platformy Azure, Utwórz grupę zasobów, a następnie dodaj Centrum IoT.

1. Podczas tworzenia Centrum IoT należy utworzyć ją w grupie zasobów. Użyj istniejącej grupy zasobów lub uruchom następujące [polecenie, aby utworzyć grupę zasobów][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > W poprzednim przykładzie tworzona jest grupa zasobów w lokalizacji Zachodnie stany USA. Listę dostępnych lokalizacji można wyświetlić, uruchamiając polecenie `az account list-locations -o table`.
    >
    >

2. Uruchom następujące polecenie [polecenie, aby utworzyć Centrum IoT] [ lnk-az-iot-command] w grupie zasobów, przy użyciu globalnie unikatowej nazwy Centrum IoT:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> Poprzednie polecenie powoduje utworzenie Centrum IoT w S1, dla której są rozliczane warstwy cenowej. Aby uzyskać więcej informacji, zobacz [cennik Centrum IoT Azure][lnk-iot-pricing].
>

## <a name="remove-an-iot-hub"></a>Usuń Centrum IoT

Korzystając z wiersza polecenia platformy Azure do [usunąć pojedynczego zasobu][lnk-az-resource-command], na przykład Centrum IoT lub Usuń grupę zasobów i wszystkie jego zasoby, w tym wszystkie centra IoT.

Aby usunąć centrum IoT, uruchom następujące polecenie:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Aby usunąć grupę zasobów i jej zasoby, uruchom następujące polecenie:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz następujące artykuły:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przy użyciu portalu Azure do zarządzania Centrum IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
