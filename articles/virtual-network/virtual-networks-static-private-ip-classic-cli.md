---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych (klasyczny) — interfejs wiersza polecenia
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skonfigurować prywatnych adresów IP maszyn wirtualnych (klasyczne) za pomocą platformy Azure klasyczny interfejs wiersza polecenia (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 9deaf4b1d80ad4e55e7c971998e8b1f5ea562257
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196580"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej (model klasyczny) przy użyciu platformy Azure klasyczny interfejs wiersza polecenia

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Można także [zarządzać statycznym prywatnym adresem IP w modelu wdrażania Menedżer zasobów](virtual-networks-static-private-ip-arm-cli.md).

Przykład klasycznego wiersza polecenia platformy Azure polecenia tego postępuj zgodnie z oczekiwaniami proste środowisko już utworzony. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe opisane w temacie [Tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak określić statyczny prywatny adres IP podczas tworzenia maszyny Wirtualnej
Aby utworzyć nową maszynę wirtualną o nazwie *DNS01* w nowej usłudze w chmurze o nazwie *TestService* na podstawie powyższego scenariusza, wykonaj następujące kroki:

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz artykuł [Instalowanie i konfigurowania interfejsu wiersza polecenia Azure](/cli/azure/install-cli-version-1.0) i postępuj zgodnie z instrukcjami aż do punktu, w którym należy wybrać konto platformy Azure i subskrypcję.
2. Uruchom polecenie **Utwórz usługę platformy Azure** , aby utworzyć usługę w chmurze.
   
        azure service create TestService --location uscentral
   
    Oczekiwane dane wyjściowe:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Uruchom polecenie **Azure Create VM** , aby utworzyć maszynę wirtualną. Należy zauważyć wartość statyczny prywatny adres IP. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Oczekiwane dane wyjściowe:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (lub --location)** . Region platformy Azure, w którym zostanie utworzona maszyna wirtualna. W naszym scenariuszu jest to *centralus*.
   * **-n (lub--VM-Name)** . Nazwa maszyny Wirtualnej, która ma zostać utworzony.
   * **-w (lub--Virtual-Network-Name)** . Nazwa sieci wirtualnej, w którym zostanie utworzona maszyna wirtualna. 
   * **-S (lub--Static-IP)** . Statyczny prywatny adres IP dla maszyny Wirtualnej.
   * **TestService**. Nazwa usługi w chmurze tworzona maszyna wirtualna.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v 14.2**. Obraz użyty do utworzenia maszyny Wirtualnej.
   * **AdminUser**. Administrator lokalny na maszynie Wirtualnej Windows.
   * <strong>AdminP@ssw0rd</strong>. Hasło administratora lokalnego dla maszyn wirtualnych Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić informacje o statycznym prywatnym adresie IP dla maszyny wirtualnej utworzonej za pomocą powyższego skryptu, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure i obserwuj wartość *StaticIP sieci*:

    azure vm static-ip show DNS01

Oczekiwane dane wyjściowe:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny Wirtualnej
Aby usunąć statyczny prywatny adres IP dodane do maszyny Wirtualnej w skrypcie powyżej, uruchom następujące polecenie z wiersza polecenia platformy Azure:

    azure vm static-ip remove DNS01

Oczekiwane dane wyjściowe:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny Wirtualnej
Aby dodać statyczny prywatny adres IP do maszyny wirtualnej utworzonej przy użyciu skryptu powyżej, uruchom następujące polecenie:

    azure vm static-ip set DNS01 192.168.1.101

Oczekiwane dane wyjściowe:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne. Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest ten sam adres jako prywatny adres IP przypisany do maszyny Wirtualnej platformy Azure, lub można utracić łączność z maszyną wirtualną. Nie należy ręcznie przypisywać publicznego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zarezerwowanych publicznych adresów IP](virtual-networks-reserved-public-ip.md) .
* Dowiedz się więcej o publicznych adresach [IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Zapoznaj się z [interfejsami API REST zastrzeżony adres IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).