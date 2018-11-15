---
title: Nie można wykonać pulpitu zdalnego do maszyn wirtualnych platformy Azure, ponieważ karta sieciowa jest wyłączona. | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym RDP zakończy się niepowodzeniem, ponieważ karta sieciowa jest wyłączone w maszynie Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 6b14530bd6b4c1b6617cb1d5c88d710a32e5372c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634709"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nie można wykonać pulpitu zdalnego z maszyną wirtualną, ponieważ interfejs sieciowy jest wyłączona.

W tym artykule pokazano, jak rozwiązać problem, w którym można pulpitu zdalnego do platformy Azure Windows Virtual Machines (VMs), ponieważ interfejs sieciowy jest wyłączona.

> [!NOTE] 
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania. 

## <a name="symptoms"></a>Objawy 

Nie można wprowadzać z połączeniem RDP lub dowolny inny typ połączenia do innych portów maszyny Wirtualnej na platformie Azure, ponieważ interfejs sieciowy w maszynie Wirtualnej jest wyłączona.

## <a name="solution"></a>Rozwiązanie 

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby włączyć interfejs dla maszyny Wirtualnej, należy użyć formantu szeregowego lub [interfejs sieciowy resetowania](##reset-network-interface) dla maszyny Wirtualnej.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, zobacz [interfejs sieciowy resetowania](#reset-network-interface).
2. Sprawdź stan interfejsu sieciowego:

        netsh interface show interface

    Zanotuj nazwę interfejsu sieciowego wyłączone. 

3. Włącz interfejs sieciowy:

        netsh interface set interface name="interface Name" admin=enabled

    Na przykład jeśli interfejs interwork nazwy "Ethernet 2", uruchom następujące polecenie:

        netsh interface set interface name=""Ethernet 2" admin=enabled
    

4.  Sprawdź stan interfejsu sieciowego, ponownie, aby upewnić się, że włączono interfejs sieciowy.

        netsh interface show interface

    Nie trzeba ponownie maszynę Wirtualną, w tym momencie. Maszyna wirtualna będzie ponownie dostępny.
        
5.  Łączenie z maszyną wirtualną i zobacz, czy problem został rozwiązany.

## <a name="reset-network-interface"></a>Zresetuj interfejs sieciowy

Aby zresetować interfejsu sieciowego, należy zmienić adres IP na inny adres IP, który jest dostępny w podsieci za pomocą witryny Azure portal lub programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [interfejs sieciowy resetowania](reset-network-interface.md). 