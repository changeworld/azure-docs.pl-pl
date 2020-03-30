---
title: Uruchamianie usługi Azure IoT Edge na maszynach wirtualnych Ubuntu | Dokumenty firmy Microsoft
description: Instrukcje konfiguracji usługi Azure IoT Edge dla maszyn wirtualnych Ubuntu 18.04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349634"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Uruchamianie usługi Azure IoT Edge na maszynach wirtualnych Ubuntu

Środowisko uruchomieniowe usługi Azure IoT Edge przekształca urządzenie w urządzenie usługi IoT Edge. Środowisko wykonawcze można wdrożyć na urządzeniach tak małych jak Raspberry Pi lub tak dużych jak serwer przemysłowy. Gdy urządzenie jest skonfigurowane ze środowiska wykonawczego usługi IoT Edge, można rozpocząć wdrażanie logiki biznesowej do niego z chmury.

Aby dowiedzieć się więcej o tym, jak działa środowisko wykonawcze usługi IoT Edge i jakie składniki są uwzględniane, zobacz [Opis środowiska wykonawczego usługi Azure IoT Edge i jego architektury.](iot-edge-runtime.md)

W tym artykule wymieniono kroki wdrażania maszyny wirtualnej Ubuntu 18.04 LTS z zainstalowanym i skonfigurowanym środowiskiem uruchomieniowym usługi Azure IoT Edge przy użyciu wstępnie dostarczonego ciągu połączenia urządzenia. Wdrożenie jest realizowane przy użyciu [szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/overview.md) oparte na [chmurze](../virtual-machines/linux/using-cloud-init.md
) obsługiwane w repozytorium projektu [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)

Przy pierwszym rozruchu maszyna wirtualna Ubuntu 18.04 LTS [instaluje najnowszą wersję środowiska wykonawczego usługi Azure IoT Edge za pośrednictwem funkcji cloud-init.](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt) Ustawia również dostarczony ciąg połączenia przed uruchomieniem środowiska wykonawczego, co pozwala na łatwe konfigurowanie i podłączanie urządzenia Usługi IoT Edge bez konieczności uruchamiania sesji SSH lub pulpitu zdalnego. 

## <a name="deploy-using-deploy-to-azure-button"></a>Wdrażanie przy użyciu przycisku Wdrażanie w usłudze Azure

[Przycisk Wdrażania na platformie Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) umożliwia usprawnione wdrażanie [szablonów usługi Azure Resource Manager obsługiwanych](../azure-resource-manager/templates/overview.md) w usłudze GitHub.  W tej sekcji zostanie zademonstrowane użycie przycisku Wdrażania na platformie Azure zawartego w repozytorium projektu [iotedge-vm-deploy.](https://github.com/Azure/iotedge-vm-deploy)  


1. Firma Microsoft wdroży maszynę wirtualną z systemem Linux z obsługą usługi Azure IoT Edge przy użyciu szablonu usługi Azure Resource Manager iotedge-vm-deploy.  Aby rozpocząć, kliknij przycisk poniżej:

    [![Wdrażanie przycisku platformy Azure w celu wdrożenia iotedge-vm](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. W nowo uruchomionym oknie wypełnij dostępne pola formularza:

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający szablon iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Subskrypcja:** aktywna subskrypcja platformy Azure do wdrożenia maszyny wirtualnej w.

    **Grupa zasobów:** istniejąca lub nowo utworzona grupa zasobów zawierająca maszynę wirtualną i skojarzone z nią zasoby.

    **Prefiks etykiety DNS:** Wymagana wartość wybranej wartości używanej do prefiksu nazwy hosta maszyny wirtualnej.

    **Nazwa użytkownika administratora**: Nazwa użytkownika, która będzie mieć uprawnienia administratora podczas wdrażania.

    **Parametry połączenia urządzenia:** [ciąg połączenia urządzenia](how-to-register-device.md) dla urządzenia utworzonego w zamierzonym centrum [IoT Hub](../iot-hub/about-iot-hub.md).

    **Rozmiar maszyny wirtualnej:** [rozmiar](../cloud-services/cloud-services-sizes-specs.md) maszyny wirtualnej, która ma zostać wdrożona

    **Ubuntu OS Wersja:** Wersja systemu operacyjnego Ubuntu do zainstalowania na podstawowej maszynie wirtualnej.

    **Lokalizacja:** [Region geograficzny,](https://azure.microsoft.com/global-infrastructure/locations/) w którym ma być wdrożona maszyna wirtualna, wartość ta jest domyślnie domyślna dla lokalizacji wybranej grupy zasobów.

    **Typ uwierzytelniania:** Wybierz **sshPublicKey** lub **hasło** w zależności od preferencji.

    **Hasło administratora lub klucz:** Wartość klucza publicznego SSH lub wartość hasła w zależności od wyboru typu uwierzytelniania.

    Po wypełnieniu wszystkich pól zaznacz pole wyboru u dołu strony, aby zaakceptować warunki, i wybierz **pozycję Kup,** aby rozpocząć wdrażanie.

1. Sprawdź, czy wdrożenie zostało pomyślnie ukończone.  Zasób maszyny wirtualnej powinien zostać wdrożony w wybranej grupie zasobów.  Zanotuj nazwę urządzenia, powinno to `vm-0000000000000`być w formacie . Należy również zwrócić uwagę na skojarzoną **nazwę DNS,** która powinna być w formacie `<dnsLabelPrefix>`. `<location>`.cloudapp.azure.com.

    **Nazwę DNS** można uzyskać z sekcji **Przegląd** nowo wdrożonej maszyny wirtualnej w witrynie Azure portal.

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający nazwę dns iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Jeśli chcesz ssh do tej maszyny wirtualnej po skonfigurowaniu, użyj skojarzonej **nazwy DNS** z poleceniem:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Wdrażanie z interfejsu wiersza polecenia platformy Azure

1. Upewnij się, że zostało zainstalowane rozszerzenie interfejsu wiersza polecenia platformy Azure z:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Następnie, jeśli używasz interfejsu wiersza polecenia platformy Azure na pulpicie, zacznij od zalogowania się:

   ```azurecli-interactive
   az login
   ```

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, której chcesz użyć:
   1. Wyświetl listę swoich subskrypcji:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Skopiuj pole SubscriptionID dla subskrypcji, której chcesz użyć.

   1. Ustaw subskrypcję roboczą przy obliczu skopiowanego identyfikatora:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Utwórz nową grupę zasobów (lub określ istniejącą w następnych krokach):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Utwórz nową maszynę wirtualną:

    Aby użyć **authenticationType** of `password`, zobacz poniższy przykład:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Aby uwierzytelnić się za pomocą klucza SSH, można `sshPublicKey`to zrobić, określając **authenticationType** of , a następnie podać wartość klucza SSH w **adminPasswordOrKey** parametru.  Przykład przedstawiono poniżej.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Sprawdź, czy wdrożenie zostało pomyślnie ukończone.  Zasób maszyny wirtualnej powinien zostać wdrożony w wybranej grupie zasobów.  Zanotuj nazwę urządzenia, powinno to `vm-0000000000000`być w formacie . Należy również zwrócić uwagę na skojarzoną **nazwę DNS,** która powinna być w formacie `<dnsLabelPrefix>`. `<location>`.cloudapp.azure.com.

    **Nazwę DNS** można uzyskać z danych wyjściowych w formacie JSON poprzedniego kroku, w sekcji **dane wyjściowe** jako część publicznego wpisu **SSH.**  Wartość tego wpisu może służyć do SSH do nowo wdrożonego komputera.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **Nazwę DNS** można również uzyskać z sekcji **Przegląd** nowo wdrożonej maszyny wirtualnej w witrynie Azure portal.

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający nazwę dns iotedge vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Jeśli chcesz ssh do tej maszyny wirtualnej po skonfigurowaniu, użyj skojarzonej **nazwy DNS** z poleceniem:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie IoT Edge aprowizowane z zainstalowanym czasem wykonywania, można [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z prawidłową instalacją środowiska wykonawczego IoT Edge, zapoznaj się ze stroną [rozwiązywania problemów.](troubleshoot.md)

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska wykonawczego](how-to-update-iot-edge.md).

Jeśli chcesz otworzyć porty, aby uzyskać dostęp do maszyny wirtualnej za pośrednictwem SSH lub innych połączeń przychodzących, zapoznaj się z dokumentacją maszyn wirtualnych platformy Azure dotyczącą [otwierania portów i punktów końcowych na maszynę wirtualną z systemem Linux](../virtual-machines/linux/nsg-quickstart.md)
