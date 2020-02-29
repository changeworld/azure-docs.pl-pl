---
title: Nie można pulpitu zdalnego do maszyn wirtualnych platformy Azure z powodu statyczny adres IP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem protokołu RDP, którego przyczyną statyczny adres IP na platformie Microsoft Azure. | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 92ad33fbc759605ae901c3bcf09283c8e0b1c4b5
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918193"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Nie można pulpitu zdalnego do maszyn wirtualnych platformy Azure z powodu statyczny adres IP

W tym artykule opisano problem, w którym nie jest możliwe pulpitu zdalnego do platformy Azure Windows Virtual Machines (VMs) po skonfigurowaniu statyczny adres IP na maszynie wirtualnej.


## <a name="symptoms"></a>Objawy

Po wprowadzeniu z połączeniem RDP z maszyną wirtualną na platformie Azure, pojawi się następujący komunikat o błędzie:

**Pulpit zdalny nie może nawiązać połączenia z komputerem zdalnym z jednego z następujących powodów:**

1. **Dostęp zdalny do serwera nie jest włączony**

2. **Komputer zdalny jest wyłączony**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, że komputer zdalny jest włączony i podłączony do sieci, a dostęp zdalny jest włączony.**

Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w Azure Portal zobaczysz, że maszyna wirtualna jest uruchamiana normalnie i czeka na poświadczenia na ekranie logowania.

## <a name="cause"></a>Przyczyna

Maszyna wirtualna ma statyczny adres IP, która jest zdefiniowana w interfejsie sieciowym w Windows. Ten adres IP różni się od adresu, który jest zdefiniowany w witrynie Azure portal.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć kontrolki serial do włączenia protokołu DHCP lub [zresetowania interfejsu sieciowego](reset-network-interface.md) dla maszyny wirtualnej.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połącz się z [konsolą szeregową i Otwórz wystąpienie programu Cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
2. Sprawdź, czy DHCP jest wyłączony w interfejsie sieciowym:

        netsh interface ip show config
3. Jeżeli DHCP jest wyłączona, należy przywrócić konfigurację interfejsu sieciowego do używania protokołu DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Na przykład jeśli interfejs interwork nazwy "Ethernet 2", uruchom następujące polecenie:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Wyślij zapytanie do konfiguracji adresu IP, ponownie, aby upewnić się, że interfejs sieciowy jest teraz prawidłowo skonfigurowany. Nowy adres IP powinien pasuje do dostarczonego przez platformę Azure.

        netsh interface ip show config

    Nie trzeba ponownie maszynę Wirtualną, w tym momencie. Maszyna wirtualna będzie ponownie dostępny.

Po tym, jeśli chcesz skonfigurować statyczny adres IP dla maszyny wirtualnej, zobacz [Konfigurowanie statycznych adresów IP dla maszyny wirtualnej](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).