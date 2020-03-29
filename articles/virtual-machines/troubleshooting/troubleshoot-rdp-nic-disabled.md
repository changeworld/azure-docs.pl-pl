---
title: Nie można połączyć się zdalnie z maszynami wirtualnymi platformy Azure, ponieważ karta sieciowa jest wyłączona | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym rdp kończy się niepowodzeniem, ponieważ karta sieciowa jest wyłączona w usłudze Azure VM| Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918244"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Nie można zdalnie od pulpici na maszynie Wirtualnej, ponieważ interfejs sieciowy jest wyłączony

W tym artykule wyjaśniono, jak rozwiązać problem, w którym nie można nawiązać połączenia pulpitu zdalnego z maszynami wirtualnymi systemu Azure Windows,jeśli interfejs sieciowy jest wyłączony.


## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia RDP ani innego typu połączenia z innymi portami do maszyny Wirtualnej na platformie Azure, ponieważ interfejs sieciowy na maszynie Wirtualnej jest wyłączony.

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemu operacyjnego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby włączyć interfejs maszyny Wirtualnej, użyj funkcji Formant szeregowy lub [zresetuj interfejs sieciowy](#reset-network-interface) maszyny Wirtualnej.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowego

1. Połącz się z [konsolą szeregową i otwórz wystąpienie CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, zobacz [resetowanie interfejsu sieciowego](#reset-network-interface).
2. Sprawdź stan interfejsu sieciowego:

        netsh interface show interface

    Zanotuj nazwę wyłączonego interfejsu sieciowego.

3. Włącz interfejs sieciowy:

        netsh interface set interface name="interface Name" admin=enabled

    Na przykład, jeśli interfejs międzydziałowy nosi nazwę "Ethernet 2", uruchom następujące polecenie:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Sprawdź stan interfejsu sieciowego ponownie, aby upewnić się, że interfejs sieciowy jest włączony.

        netsh interface show interface

    W tym momencie nie trzeba ponownie uruchamiać maszyny Wirtualnej. Maszyna wirtualna będzie z powrotem osiągalne.

5.  Połącz się z maszyną wirtualną i zobacz, czy problem został rozwiązany.

## <a name="reset-network-interface"></a>Resetowanie interfejsu sieciowego

Aby zresetować interfejs sieciowy, zmień adres IP na inny adres IP, który jest dostępny w podsieci. Aby to zrobić, użyj witryny Azure portal lub usługi Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
