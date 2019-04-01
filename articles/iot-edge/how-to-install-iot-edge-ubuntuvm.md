---
title: Uruchamianie usługi Azure IoT Edge na maszynach wirtualnych Ubuntu | Dokumentacja firmy Microsoft
description: Usługa Azure IoT Edge instrukcje dotyczące konfigurowania systemu Ubuntu 16.04 Azure Marketplace Virtual Machines
author: gregman
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: 5cb15f45508b60a1b7f8c9049b8779c1b7d31150
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757924"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Uruchamianie usługi Azure IoT Edge na maszynach wirtualnych z systemem Ubuntu

Środowisko uruchomieniowe usługi Azure IoT Edge to, co jest przekształcany urządzenia urządzenia usługi IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako małej, jak Raspberry Pi lub tak duże jak serwer przemysłowe. Gdy urządzenie zostanie skonfigurowany ze środowiskiem uruchomieniowym usługi IoT Edge, możesz rozpocząć wdrażanie logikę biznesową w chmurze.

Aby dowiedzieć się więcej na temat sposobu działania środowiska uruchomieniowego usługi IoT Edge i jakie składniki wchodzą, zobacz [zrozumieć środowisko uruchomieniowe usługi Azure IoT Edge oraz jej architektury](iot-edge-runtime.md).

W tym artykule wymieniono kroki, aby uruchomić środowisko uruchomieniowe usługi Azure IoT Edge w systemie Ubuntu 16.04 maszyny wirtualnej przy użyciu wstępnie skonfigurowanych [usługi Azure IoT Edge w ramach oferty w portalu Azure Marketplace Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm). 

Przy pierwszym uruchomieniu usługi Azure IoT Edge na maszynie Wirtualnej z systemem Ubuntu preinstaluje najnowszą wersję środowiska uruchomieniowego usługi Azure IoT Edge. Zawiera również skrypt ustawimy parametry połączenia, a następnie ponownie uruchomić środowiska uruchomieniowego, które może być uruchamiane zdalnie za pośrednictwem portalu maszyny Wirtualnej platformy Azure lub wiersza polecenia platformy Azure, dzięki czemu możesz łatwo skonfigurować i połączyć urządzenia IoT Edge bez uruchamiania klienta SSH lub zdalnego Sesja pulpitu. Ten skrypt będzie czekać można ustawić parametrów połączenia, dopóki nie pełni zainstalowaniu klienta usługi IoT Edge, dzięki czemu nie trzeba do utworzenia do automatyzacji.

## <a name="deploy-from-the-azure-marketplace"></a>Wdrażanie portalu Azure Marketplace
1.  Przejdź do [usługi Azure IoT Edge w systemie Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) oferty w portalu Marketplace lub przez wyszukiwanie "Azure IoT Edge w systemie Ubuntu" [w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Wybierz **UZYSKAĆ IT** i następnie **Kontynuuj** w następnym oknie dialogowym.
3.  Jeden raz w witrynie Azure portal wybierz **Utwórz** i użyj kreatora Aby wdrożyć maszynę Wirtualną. 
    *   Jeśli po raz pierwszy wypróbowanie Maszynę wirtualną, najłatwiej hasła i włączyć protokół SSH, w menu publicznego portu wejściowego. 
    *   W przypadku obciążeń intensywnie korzystających z zasobów, należy uaktualnić rozmiar maszyny wirtualnej, dodając więcej procesorów lub pamięci.
4.  Po wdrożeniu maszyny wirtualnej należy skonfigurować tak, aby nawiązać połączenie z Centrum IoT:
    1.  Skopiuj parametry połączenia urządzenia na urządzeniu usługi IoT Edge utworzone w usłudze IoT Hub (możesz wykonać [zarejestrować nowe urządzenie usługi Azure IoT Edge w witrynie Azure portal](how-to-register-device-portal.md) poradnik, jeśli nie znasz tego procesu)
    1.  Wybierz zasób nowo utworzoną maszyną wirtualną w witrynie Azure portal i Otwórz **Uruchom polecenie** opcji
    1.  Wybierz **RunShellScript** opcji
    1.  Wykonaj poniższy skrypt, za pośrednictwem oknie wiersza polecenia przy użyciu parametrów połączenia urządzenia: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Wybierz **uruchamiania**
    1.  Poczekaj chwilę, a ekran powinien następnie zapewnić komunikat informujący o pomyślnym parametry połączenia została ustawiona pomyślnie.


## <a name="deploy-from-the-azure-portal"></a>Wdrażanie w witrynie Azure Portal
W witrynie Azure portal, wyszukaj frazę "Azure IoT Edge" i wybierz **Ubuntu Server 16.04 LTS i środowisko uruchomieniowe usługi Azure IoT Edge** umożliwiającą przepływ pracy tworzenia maszyny Wirtualnej. Z tego miejsca, wykonaj kroki 3 i 4 w powyższych instrukcji "Wdrożyć z portalu Azure Marketplace".

## <a name="deploy-from-azure-cli"></a>Wdrażanie z wiersza polecenia platformy Azure
1. Jeśli jest to pierwsza wdrażania maszyny wirtualnej z interfejsu wiersza polecenia, należy włączyć wdrożenia programowe dla Twojej subskrypcji platformy Azure:
   1. Otwórz [usługi Azure IoT Edge w systemie Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) oferty w portalu Marketplace
   1. Wybierz **UZYSKAĆ IT** i **Kontynuuj** na kolejne okno dialogowe
   1. Wybierz **mają zostać wdrożone programowo? Rozpoczynanie pracy** w dolnej części okna dialogowego w portalu
   1. Kliknij pozycję **Włącz** znajdujący się w **konfigurowania wdrażania programowego** stronie, a następnie kliknij przycisk **Zapisz**
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
   1. Identyfikatorem, który właśnie został skopiowany, uruchom następujące polecenie:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Utwórz nową grupę zasobów (lub określ istniejącą w następnych krokach):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Utwórz nową maszynę wirtualną:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM –-image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

1. Ustaw parametry połączenia urządzenia (możesz wykonać [zarejestrować nowe urządzenie usługi Azure IoT Edge przy użyciu wiersza polecenia platformy Azure](how-to-register-device-cli.md) poradnik, jeśli nie znasz tego procesu):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Jeśli chcesz SSH do tej maszyny Wirtualnej po zakończeniu instalacji, należy użyć publiczny adres IP przy użyciu polecenia: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz urządzenia usługi IoT Edge zaopatrzony zainstalowanego środowiska uruchomieniowego, można [wdrożyć moduły usługi IoT Edge](how-to-deploy-modules-portal.md).

Jeśli występują problemy ze środowiska uruchomieniowego usługi Edge instalacji prawidłowo, zapoznaj się z [Rozwiązywanie problemów z](troubleshoot.md) strony.

Aby zaktualizować istniejącą instalację do najnowszej wersji usługi IoT Edge, zobacz [aktualizacji demona zabezpieczeń usługi IoT Edge i środowisko uruchomieniowe](how-to-update-iot-edge.md).
