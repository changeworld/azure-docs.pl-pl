---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781479"
---
## <a name="access-the-virtual-machine"></a>Dostęp do maszyny wirtualnej

W poniższych krokach użyj interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell. Jeśli wolisz, możesz [zainstalować interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na komputerze deweloperskim i uruchomić polecenia lokalnie.

Poniższe kroki pokazują, jak skonfigurować maszynę wirtualną platformy Azure, aby umożliwić dostęp **SSH.** Kroki pokazane zakładać, że nazwa wybrana dla akceleratora rozwiązania jest **contoso-simulation** - zastąpić tę wartość nazwą wdrożenia:

1. Wyświetl listę zawartości grupy zasobów zawierającej zasoby akceleratora rozwiązań:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Zanotuj nazwę maszyny wirtualnej, publiczny adres IP i grupę zabezpieczeń sieci — te wartości są potrzebne później.

1. Zaktualizuj sieciową grupę zabezpieczeń, aby zezwolić na dostęp do SSH. Następujące polecenie zakłada, że nazwa sieciowej grupy zabezpieczeń to **contoso-simulation-nsg** — zastępuje tę wartość nazwą sieciowej grupy zabezpieczeń:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Włącz dostęp SSH tylko podczas testowania i rozwoju. Jeśli włączysz SSH, [należy wyłączyć go ponownie tak szybko, jak to możliwe](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Zaktualizuj hasło dla konta **użytkownika platformy azure** na maszynie wirtualnej do hasła, które znasz. Po uruchomieniu następującego polecenia wybierz własne hasło:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Znajdź publiczny adres IP maszyny wirtualnej. Następujące polecenie zakłada, że nazwa maszyny wirtualnej to **vm-vikxv** — zastąpić tę wartość nazwą maszyny wirtualnej, na którą wcześniej zanotowano:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Zanotuj publiczny adres IP maszyny wirtualnej.
