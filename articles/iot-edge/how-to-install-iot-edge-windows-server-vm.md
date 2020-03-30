---
title: Uruchamianie usługi Azure IoT Edge na maszynach wirtualnych systemu Windows Server | Dokumenty firmy Microsoft
description: Instrukcje dotyczące konfigurowania usługi Azure IoT Edge na maszynach wirtualnych w portalu Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045890"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Uruchamianie usługi Azure IoT Edge na maszynach wirtualnych systemu Windows Server

Środowisko uruchomieniowe usługi Azure IoT Edge przekształca urządzenie w urządzenie usługi IoT Edge. Środowisko wykonawcze można wdrożyć na urządzeniach tak małych jak Raspberry Pi lub tak dużych jak serwer przemysłowy. Gdy urządzenie jest skonfigurowane ze środowiska wykonawczego usługi IoT Edge, można rozpocząć wdrażanie logiki biznesowej do niego z chmury.

Aby dowiedzieć się więcej o tym, jak działa środowisko wykonawcze usługi IoT Edge i jakie składniki są uwzględniane, zobacz [Opis środowiska wykonawczego usługi Azure IoT Edge i jego architektury.](iot-edge-runtime.md)

W tym artykule wymieniono kroki uruchamiania środowiska wykonawczego usługi Azure IoT Edge na maszynie wirtualnej systemu Windows Server 2019 przy użyciu oferty [windows server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) azure marketplace. Postępuj zgodnie z instrukcjami w [install the Azure IoT Edge runtime](how-to-install-iot-edge-windows.md) w systemie Windows do użytku z innymi wersjami.

## <a name="deploy-from-the-azure-marketplace"></a>Wdrażanie z portalu Azure Marketplace

1. Przejdź do oferty [portalu Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace lub wyszukując program "Windows Server" w portalu Azure [Marketplace](https://azuremarketplace.microsoft.com/)
2. Wybierz **POBIERZ TERAZ**
3. W **programie Software plan (Plan oprogramowania)** znajdź pozycję "Windows Server 2019 Datacenter Server Core with Containers", a następnie wybierz pozycję **Kontynuuj** w następnym oknie dialogowym.
    * Można również użyć tych instrukcji dla innych wersji systemu Windows Server z kontenerami
4. Po wejściu do witryny Azure Portal wybierz pozycję **Utwórz** i postępuj zgodnie z kreatorem, aby wdrożyć maszynę wirtualną.
    * Jeśli po raz pierwszy wypróbowają maszynę wirtualną, najłatwiej jest użyć hasła i włączyć prow i ssh w menu publicznego portu przychodzącego.
    * Jeśli masz obciążenie intensywnie korzystające z zasobów, należy uaktualnić rozmiar maszyny wirtualnej, dodając więcej procesorów i/lub pamięci.
5. Po wdrożeniu maszyny wirtualnej skonfiguruj ją tak, aby łączyła się z centrum IoT Hub:
    1. Skopiuj ciąg połączenia urządzenia z urządzenia Usługi IoT Edge utworzonego w centrum IoT Hub. Zobacz procedurę [Pobieranie ciągu połączenia w witrynie Azure portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Wybierz nowo utworzony zasób maszyny wirtualnej z witryny Azure Portal i otwórz opcję **polecenia uruchom**
    1. Wybierz opcję **RunPowerShellScript**
    1. Skopiuj ten skrypt do okna polecenia za pomocą ciągu połączenia urządzenia:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Wykonaj skrypt, aby zainstalować środowisko uruchomieniowe usługi IoT Edge i ustawić parametry połączenia, wybierając **pozycję Uruchom**
    1. Po minucie lub dwóch powinien zostać wyświetlony komunikat, że środowisko uruchomieniowe usługi Edge zostało zainstalowane i pomyślnie zainicjowane.

## <a name="deploy-from-the-azure-portal"></a>Wdrażanie z witryny Azure portal

1. W portalu Azure wyszukaj hasło "Windows Server" i wybierz **centrum danych systemu Windows Server 2019,** aby rozpocząć przepływ pracy tworzenia maszyny Wirtualnej.
2. Z **wybierz plan oprogramowania** wybierz "Windows Server 2019 Datacenter Server Core with Containers", a następnie wybierz pozycję **Utwórz**
3. Wykonaj krok 5 w instrukcjach "Wdrażanie z portalu Azure Marketplace" powyżej.

## <a name="deploy-from-azure-cli"></a>Wdrażanie z interfejsu wiersza polecenia platformy Azure

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure na pulpicie, zacznij od zalogowania się:

   ```azurecli-interactive
   az login
   ```

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, której chcesz użyć:
   1. Wyświetl listę swoich subskrypcji:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Skopiuj pole Identyfikator subskrypcji dla subskrypcji, której chcesz użyć
   1. Uruchom to polecenie przy obliczu skopiowanego identyfikatora:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Utwórz nową grupę zasobów (lub określ istniejącą w następnych krokach):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Utwórz nową maszynę wirtualną:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * To polecenie wyświetli monit o podanie hasła, `--admin-password` ale można dodać opcję, aby łatwiej ustawić je w skrypcie
   * Obraz Windows Server Core obsługuje wiersz polecenia tylko z pulpitem zdalnym, więc `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` jeśli chcesz mieć pełne środowisko pulpitu, określ jako obraz

1. Ustaw parametry połączenia urządzenia (możesz wykonać procedurę [Pobierz parametry połączenia z platformą Azure CLI,](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) jeśli nie jesteś zaznajomiony z tym procesem):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie IoT Edge aprowizowane z zainstalowanym czasem wykonywania, można [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z prawidłową instalacją środowiska wykonawczego edge, zapoznaj się ze stroną [rozwiązywania problemów.](troubleshoot.md)

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [Aktualizowanie demona zabezpieczeń usługi IoT Edge i środowiska wykonawczego](how-to-update-iot-edge.md).

Dowiedz się więcej o używaniu maszyn wirtualnych z systemem Windows w [dokumentacji maszyn wirtualnych systemu Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Jeśli chcesz ssh do tej maszyny Wirtualnej po instalacji, postępuj zgodnie [z instalacji OpenSSH dla systemu Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) przewodnik przy użyciu pulpitu zdalnego lub zdalnego powershell.
