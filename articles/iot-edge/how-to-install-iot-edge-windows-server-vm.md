---
title: Uruchamianie usługi Azure IoT Edge na maszyny wirtualne z systemem Windows Server | Dokumentacja firmy Microsoft
description: Instrukcje instalacji w usłudze Azure IoT Edge w witrynie Marketplace maszyny wirtualne Windows Server
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159783"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Uruchamianie usługi Azure IoT Edge na maszyny wirtualne z systemem Windows Server
Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze.

Aby dowiedzieć się więcej na temat sposobu działania środowiska uruchomieniowego usługi IoT Edge i jakie składniki wchodzą, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby uruchomić środowisko uruchomieniowe usługi Azure IoT Edge na maszynie wirtualnej systemu Windows Server 2019 przy użyciu [systemu Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) oferty w portalu Azure Marketplace. Postępuj zgodnie z instrukcjami w artykule [zainstalować środowisko uruchomieniowe usługi Azure IoT Edge](how-to-install-iot-edge-windows.md) na Windows do użycia z innymi wersjami.

> [!NOTE]
> Środowisko uruchomieniowe usługi IoT Edge w systemie Windows Server znajduje się w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Wdrażanie portalu Azure Marketplace
1.  Przejdź do [systemu Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) oferty w portalu Azure Marketplace lub przeszukując "Windows Server" na [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Wybierz **Pobierz teraz** 
3.  W **plan oprogramowania**, Znajdź "Kontenery systemu Windows Server 2019 Datacenter Server Core za pomocą", a następnie wybierz **Kontynuuj** w następnym oknie dialogowym.
    * Umożliwia również w instrukcjach w przypadku innych wersji systemu Windows Server za pomocą kontenerów
4.  Jeden raz w witrynie Azure portal wybierz **Utwórz** i użyj kreatora Aby wdrożyć maszynę Wirtualną. 
    *   Jeśli po raz pierwszy wypróbowanie Maszynę wirtualną, najłatwiej haseł i Włącz protokół RDP i SSH w menu publicznego portu wejściowego. 
    *   W przypadku obciążeń intensywnie korzystających z zasobów, należy uaktualnić rozmiar maszyny wirtualnej, dodając więcej procesorów lub pamięci.
5.  Po wdrożeniu maszyny wirtualnej należy skonfigurować tak, aby nawiązać połączenie z Centrum IoT:
    1.  Skopiuj parametry połączenia urządzenia na urządzeniu usługi IoT Edge utworzone w usłudze IoT Hub (możesz wykonać [zarejestrować nowe urządzenie usługi Azure IoT Edge w witrynie Azure portal](how-to-register-device-portal.md) poradnik, jeśli nie znasz tego procesu)
    1.  Wybierz zasób nowo utworzoną maszyną wirtualną w witrynie Azure portal i Otwórz **Uruchom polecenie** opcji
    1.  Wybierz **RunPowerShellScript** opcji
    1.  Skopiuj ten skrypt w oknie wiersza polecenia przy użyciu parametrów połączenia urządzenia: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Uruchom skrypt do zainstalowania środowiska uruchomieniowego usługi Edge i ustawić parametry połączenia, wybierając **uruchamiania**
    1.  Po minucie lub dwóch wyświetlony komunikat informujący o tym, czy środowiska uruchomieniowego usługi Edge została zainstalowana i pomyślnie aprowizowane.


## <a name="deploy-from-the-azure-portal"></a>Wdrażanie w witrynie Azure portal
1. W witrynie Azure portal, wyszukaj frazę "Windows Server" i wybierz **systemie Windows Datacenter 2019** umożliwiającą przepływ pracy tworzenia maszyny Wirtualnej. 
2. Z **wybierz plan oprogramowania** wybierz "Kontenery systemu Windows Server 2019 Datacenter Server Core za pomocą", a następnie wybierz **Create**
3. Wykonaj instrukcje krok 5 w "Wdrożyć z portalu Azure Marketplace" powyżej.

## <a name="deploy-from-azure-cli"></a>Wdrażanie z wiersza polecenia platformy Azure
1. Jeśli używasz interfejsu wiersza polecenia platformy Azure na komputerze, należy uruchomić po zalogowaniu się:

   ```azurecli-interactive
   az login
   ```
    
1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, której chcesz użyć:
   1. Wyświetlić listę swoich subskrypcji:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kopiuj pole SubscriptionID dla subskrypcji, którą chcesz użyć
   1. Za pomocą skopiowanego Identyfikatora, uruchom następujące polecenie:
    
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
   * To polecenie spowoduje wyświetlenie monitu o hasło, ale można dodać opcję `--admin-password` można ustawić je łatwiej w skrypcie
   * Obraz systemu Windows Server Core zawiera polecenie Wiersz obsługę tylko przy użyciu pulpitu zdalnego, zatem jeśli chcesz środowisko pełnej pulpitu, określ `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` jako obraz

1. Ustaw parametry połączenia urządzenia (możesz wykonać [zarejestrować nowe urządzenie usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure](how-to-register-device-cli.md) poradnik, jeśli nie znasz tego procesu):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy ze środowiska uruchomieniowego usługi Edge instalacji prawidłowo, zapoznaj się z [Rozwiązywanie problemów z](troubleshoot.md) strony.

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).

Dowiedz się więcej o używaniu maszyn wirtualnych Windows [dokumentacja dotycząca maszyn wirtualnych Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

SSH do tej maszyny Wirtualnej po zakończeniu instalacji, należy wykonać [instalacji z OpenSSH dla systemu Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) przewodnik przy użyciu zdalnego pulpitu lub zdalne programu powershell.
