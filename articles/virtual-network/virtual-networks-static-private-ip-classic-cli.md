---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych (klasyczne) - Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP maszyn wirtualnych (klasyczne) za pomocą interfejsu wiersza polecenia platformy Azure (CLI) 1.0.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c23419fdee7330d091d4699714f4ec08e72be2df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej (klasyczne) przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [Zarządzanie statycznego prywatnego adresu IP w modelu wdrażania usługi Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

Poniższe przykładowe polecenia interfejsu wiersza polecenia Azure oczekiwać środowisku niezłożonym już utworzone. Aby uruchomić polecenia wyświetlaną w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [utworzyć sieć wirtualną](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Sposób określania statycznego prywatnego adresu IP, podczas tworzenia maszyny Wirtualnej
Aby utworzyć nową maszynę Wirtualną o nazwie *DNS01* w nową usługę w chmurze o nazwie *TestService* oparte na powyższym scenariuszu, wykonaj następujące kroki:

1. Jeśli po raz pierwszy używasz interfejsu wiersza polecenia Azure, zobacz artykuł [Instalowanie i konfigurowania interfejsu wiersza polecenia Azure](../cli-install-nodejs.md) i postępuj zgodnie z instrukcjami aż do punktu, w którym należy wybrać konto platformy Azure i subskrypcję.
2. Uruchom **tworzenia usługi azure** polecenie, aby utworzyć usługę w chmurze.
   
        azure service create TestService --location uscentral
   
    Oczekiwane dane wyjściowe:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Uruchom **azure tworzenie maszyny wirtualnej** polecenie, aby utworzyć maszynę Wirtualną. Zwróć uwagę, wartość statycznego prywatnego adresu IP. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.
   
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
   
   * **-l (lub --location)**. Region platformy Azure, w której zostanie utworzona maszyna wirtualna. W naszym scenariuszu jest to *centralus*.
   * **-n (lub nazwę maszyny wirtualnej —)**. Nazwa maszyny wirtualnej, która ma zostać utworzony.
   * **-w (lub--wirtualnej nazwy sieciowej)**. Nazwa sieci wirtualnej, w której zostanie utworzona maszyna wirtualna. 
   * **-S (lub--static ip)**. Statycznego prywatnego adresu IP dla maszyny Wirtualnej.
   * **TestService**. Nazwa usługi chmury, w której zostanie utworzona maszyna wirtualna.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Obraz używany do tworzenia maszyny Wirtualnej.
   * **adminuser**. Administrator lokalny dla maszyny Wirtualnej systemu Windows.
   * **AdminP@ssw0rd**. Hasło administratora lokalnego dla maszyny Wirtualnej systemu Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statycznych prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić informacje statycznych adresów IP prywatne dla maszyny Wirtualnej utworzone za pomocą skryptu powyżej, uruchom następujące polecenie z wiersza polecenia platformy Azure i sprawdź wartość *StaticIP sieci*:

    azure vm static-ip show DNS01

Oczekiwane dane wyjściowe:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statycznego prywatnego adresu IP z maszyny Wirtualnej
Aby usunąć statycznego prywatnego adresu IP dodane do maszyny Wirtualnej w skrypcie powyżej, uruchom następujące polecenie z wiersza polecenia platformy Azure:

    azure vm static-ip remove DNS01

Oczekiwane dane wyjściowe:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Jak dodać statycznego prywatnego adresu IP do istniejącej maszyny Wirtualnej
Aby dodać statycznego prywatnych adresów IP do maszyny Wirtualnej utworzone za pomocą skryptu powyżej runt następujące polecenie:

    azure vm static-ip set DNS01 192.168.1.101

Oczekiwane dane wyjściowe:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawianie adresów IP w ramach systemu operacyjnego

Zaleca się, że nie zostanie statycznie przypisany prywatny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny Wirtualnej, o ile to konieczne. Jeśli ręcznie ustawić prywatnego adresu IP w ramach systemu operacyjnego, upewnij się, że jest ten sam adres prywatny adres IP przypisany do maszyny Wirtualnej platformy Azure lub można utracić łączność z maszyną wirtualną. Ręcznie nigdy nie należy przypisywać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [zastrzeżone publicznego adresu IP](virtual-networks-reserved-public-ip.md) adresów.
* Dowiedz się więcej o [poziomie wystąpienia publicznego adresu IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adresów.
* Zapoznaj się [zastrzeżone interfejsów API REST IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

