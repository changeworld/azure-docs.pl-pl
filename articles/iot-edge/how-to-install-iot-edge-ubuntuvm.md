---
title: Uruchom Azure IoT Edge na Ubuntu Virtual Machines | Microsoft Docs
description: Instrukcje dotyczące konfiguracji Azure IoT Edge w witrynie Azure Marketplace Ubuntu 16,04 Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: philmea
ms.openlocfilehash: 49a783e1360aeddc8eeaadba442acf578d9d6f7f
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046041"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Uruchom Azure IoT Edge na Ubuntu Virtual Machines

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze.

Aby dowiedzieć się więcej o tym, jak działa środowisko uruchomieniowe IoT Edge i jakie składniki są uwzględnione, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę uruchamiania środowiska uruchomieniowego Azure IoT Edge na maszynie wirtualnej Ubuntu 16,04 przy użyciu wstępnie skonfigurowanego [Azure IoT Edge w ofercie usługi Ubuntu Azure Marketplace](https://aka.ms/azure-iot-edge-ubuntuvm).

Podczas pierwszego Azure IoT Edge rozruchu na maszynie wirtualnej Ubuntu jest preinstalowany Najnowsza wersja środowiska uruchomieniowego Azure IoT Edge. Zawiera również skrypt służący do ustawiania parametrów połączenia, a następnie ponownego uruchomienia środowiska uruchomieniowego, które może zostać wyzwolone zdalnie za pośrednictwem portalu maszyny wirtualnej platformy Azure lub wiersza polecenia platformy Azure, co pozwala łatwo skonfigurować i połączyć urządzenie IoT Edge bez konieczności uruchamiania protokołu SSH lub zdalnego sesja pulpitu. Ten skrypt będzie oczekiwał na ustawienie parametrów połączenia do momentu, gdy klient IoT Edge nie zostanie w pełni zainstalowany, aby nie trzeba było kompilować go do automatyzacji.

## <a name="deploy-from-the-azure-marketplace"></a>Wdrażanie z poziomu portalu Azure Marketplace

1. Przejdź do [Azure IoT Edge w witrynie Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace lub Wyszukaj w witrynie [Azure marketplace](https://azuremarketplace.microsoft.com/) pozycję "Azure IoT Edge on Ubuntu"
2. Wybierz pozycję **Pobierz teraz** , a następnie **Kontynuuj** w następnym oknie dialogowym.
3. Raz w Azure Portal wybierz pozycję **Utwórz** i postępuj zgodnie z instrukcjami kreatora, aby wdrożyć maszynę wirtualną.
    * Jeśli po raz pierwszy wypróbuje maszynę wirtualną, najłatwiej użyć hasła i włączyć protokół SSH w menu Publiczny port wejściowy.
    * W przypadku obciążeń intensywnie korzystających z zasobów należy uaktualnić rozmiar maszyny wirtualnej, dodając więcej procesorów CPU i/lub pamięć.
4. Po wdrożeniu maszyny wirtualnej skonfiguruj ją w celu nawiązania połączenia z IoT Hub:
    1. Skopiuj parametry połączenia z urządzeniem IoT Edge utworzonym w IoT Hub (możesz użyć polecenia [Pobierz parametry połączenia w procedurze Azure Portal,](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) Jeśli nie znasz tego procesu)
    1. Wybierz nowo utworzony zasób maszyny wirtualnej z Azure Portal i Otwórz opcję **Uruchom polecenie**
    1. Wybierz opcję **RunShellScript**
    1. Wykonaj Poniższy skrypt za pomocą okna poleceń z parametrami połączenia urządzenia: `/etc/iotedge/configedge.sh "{device_connection_string}"`
    1. Wybierz pozycję **Uruchom**
    1. Poczekaj chwilę, a na ekranie powinien zostać wyświetlony komunikat o powodzeniu wskazujący, że parametry połączenia zostały prawidłowo ustawione.

## <a name="deploy-from-the-azure-portal"></a>Wdróż z Azure Portal

W Azure Portal Wyszukaj ciąg "Azure IoT Edge" i wybierz pozycję **Ubuntu Server 16,04 LTS + środowisko uruchomieniowe Azure IoT Edge** , aby rozpocząć przepływ pracy tworzenia maszyny wirtualnej. Z tego miejsca wykonaj kroki 3 i 4 w sekcji "wdrażanie z poziomu portalu Azure Marketplace" powyżej.

## <a name="deploy-from-azure-cli"></a>Wdrażanie z poziomu interfejsu wiersza polecenia platformy Azure

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure na pulpicie, Zacznij od zalogowania się:

   ```azurecli-interactive
   az login
   ```

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, której chcesz użyć:
   1. Wyświetl listę Twoich subskrypcji:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Skopiuj pole identyfikatora subskrypcji, którego chcesz użyć.

   1. Ustaw swoją działającą subskrypcję z IDENTYFIKATORem, który właśnie został skopiowany:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Utwórz nową grupę zasobów (lub określ istniejącą w następnych krokach):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Zaakceptuj warunki użytkowania maszyny wirtualnej. Jeśli chcesz najpierw przejrzeć warunki, wykonaj kroki opisane w sekcji [wdrażanie z poziomu portalu Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Utwórz nową maszynę wirtualną:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Ustaw parametry połączenia urządzenia (można postępować zgodnie z procedurą [pobierania parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure,](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) Jeśli nie masz doświadczenia z tym procesem):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Aby SSH do tej maszyny wirtualnej po zakończeniu instalacji, użyj publicIpAddress z poleceniem: `ssh azureuser@{publicIpAddress}`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją środowiska uruchomieniowego IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).

Jeśli chcesz otworzyć porty w celu uzyskania dostępu do maszyny wirtualnej za pośrednictwem protokołu SSH lub innych połączeń przychodzących, zapoznaj się z dokumentacją maszyny wirtualnej platformy Azure dotyczącą [otwierania portów i punktów końcowych na maszynę wirtualną z systemem Linux](../virtual-machines/linux/nsg-quickstart.md)
