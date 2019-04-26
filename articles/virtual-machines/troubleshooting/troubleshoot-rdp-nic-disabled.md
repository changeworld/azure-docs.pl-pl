---
title: Nie można zdalne łączenie z usługą Azure Virtual Machines ponieważ karta sieciowa jest wyłączony | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 742026a8ff35f318f58674ebc2fb5c03e45161a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319007"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nie można wykonać pulpitu zdalnego z maszyną wirtualną, ponieważ interfejs sieciowy jest wyłączona.

W tym artykule wyjaśniono, jak rozwiązać problem, w której nie można wprowadzać Podłączanie pulpitu zdalnego do platformy Azure Windows Virtual Machines (VMs) Jeśli interfejs sieciowy jest wyłączona.

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptoms"></a>Objawy

Nie można wprowadzać z połączeniem RDP lub dowolny inny typ połączenia do innych portów maszyny Wirtualnej na platformie Azure, ponieważ interfejs sieciowy w maszynie Wirtualnej jest wyłączona.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby włączyć interfejs dla maszyny Wirtualnej, należy użyć formantu szeregowego lub [interfejs sieciowy resetowania](##reset-network-interface) dla maszyny Wirtualnej.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, zobacz [interfejs sieciowy resetowania](#reset-network-interface).
2. Sprawdź stan interfejsu sieciowego:

        netsh interface show interface

    Zanotuj nazwę interfejsu sieciowego wyłączone.

3. Włącz interfejs sieciowy:

        netsh interface set interface name="interface Name" admin=enabled

    Na przykład jeśli interwork interfejsu ma nazwę "Ethernet 2", uruchom następujące polecenie:

        netsh interface set interface name=""Ethernet 2" admin=enabled


4.  Sprawdź stan interfejsu sieciowego, ponownie, aby upewnić się, że włączono interfejs sieciowy.

        netsh interface show interface

    Nie trzeba ponownie maszynę Wirtualną, w tym momencie. Maszyna wirtualna będzie ponownie dostępny.

5.  Łączenie z maszyną wirtualną i zobacz, czy problem został rozwiązany.

## <a name="reset-network-interface"></a>Zresetuj interfejs sieciowy

Aby zresetować interfejsu sieciowego, należy zmienić adres IP na inny adres IP, który jest dostępny w tej podsieci. Aby to zrobić, należy użyć witryny Azure portal lub programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [interfejs sieciowy resetowania](reset-network-interface.md).