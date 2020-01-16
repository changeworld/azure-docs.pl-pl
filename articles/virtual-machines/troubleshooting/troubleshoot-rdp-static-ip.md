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
ms.openlocfilehash: 7756d4c808e104b0de14e1cea4af5e843cff755c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981768"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Nie można pulpitu zdalnego do maszyn wirtualnych platformy Azure z powodu statyczny adres IP

W tym artykule opisano problem, w którym nie jest możliwe pulpitu zdalnego do platformy Azure Windows Virtual Machines (VMs) po skonfigurowaniu statyczny adres IP na maszynie wirtualnej.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptoms"></a>Objawy

Po wprowadzeniu z połączeniem RDP z maszyną wirtualną na platformie Azure, pojawi się następujący komunikat o błędzie:

**Pulpit zdalny nie może połączyć się z komputerem zdalnym dla jednego z następujących powodów:**

1. **Zdalny dostęp do serwera nie jest włączony.**

2. **Komputer zdalny jest wyłączona.**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, komputer zdalny jest włączone i połączone z siecią i włączenie dostępu zdalnego.**

Podczas ewidencjonowania zrzucie ekranu [diagnostykę rozruchu](../troubleshooting/boot-diagnostics.md) w witrynie Azure portal, zostanie wyświetlony wykonać normalnego rozruchu maszyny Wirtualnej i czeka na poświadczenia na ekranie logowania.

## <a name="cause"></a>Przyczyna

Maszyna wirtualna ma statyczny adres IP, która jest zdefiniowana w interfejsie sieciowym w Windows. Ten adres IP różni się od adresu, który jest zdefiniowany w witrynie Azure portal.

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć Serial kontroli, Włącz protokół DHCP lub [interfejs sieciowy resetowania](reset-network-interface.md) dla maszyny Wirtualnej.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, zobacz [interfejs sieciowy resetowania](reset-network-interface.md).
2. Sprawdź, czy DHCP jest wyłączony w interfejsie sieciowym:

        netsh interface ip show config
3. Jeżeli DHCP jest wyłączona, należy przywrócić konfigurację interfejsu sieciowego do używania protokołu DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Na przykład jeśli interfejs interwork nazwy "Ethernet 2", uruchom następujące polecenie:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Wyślij zapytanie do konfiguracji adresu IP, ponownie, aby upewnić się, że interfejs sieciowy jest teraz prawidłowo skonfigurowany. Nowy adres IP powinien pasuje do dostarczonego przez platformę Azure.

        netsh interface ip show config

    Nie trzeba ponownie maszynę Wirtualną, w tym momencie. Maszyna wirtualna będzie ponownie dostępny.

Po tym, jeśli chcesz skonfigurować statyczny adres IP dla maszyny Wirtualnej, zobacz [skonfiguruj statyczne adresy IP dla maszyny Wirtualnej](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).