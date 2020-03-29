---
title: Nie można zdalnie oddezywować na maszynach wirtualnych platformy Azure z powodu statycznego adresu IP| Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem z prowędrzem RDP spowodowany statycznym adresem IP na platformie Microsoft Azure.| Dokumenty firmy Microsoft
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918193"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Nie można zdalnie oddezywować do maszyn wirtualnych platformy Azure z powodu statycznego adresu IP

W tym artykule opisano problem, w którym nie można zdalnie pulpitu do maszyn wirtualnych systemu Azure Systemu Windows (VMs) po skonfigurowaniu statycznego adresu IP na maszynie wirtualnej.


## <a name="symptoms"></a>Objawy

Po nawiązaniu połączenia RDP z maszyną wirtualną na platformie Azure zostanie wyświetlony następujący komunikat o błędzie:

**Pulpit zdalny nie może połączyć się z komputerem zdalnym z jednego z następujących powodów:**

1. **Dostęp zdalny do serwera nie jest włączony**

2. **Komputer zdalny jest wyłączony**

3. **Komputer zdalny nie jest dostępny w sieci**

**Upewnij się, że komputer zdalny jest włączony i podłączony do sieci, a dostęp zdalny jest włączony.**

Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w witrynie Azure portal, widzisz bootiowania maszyny Wirtualnej normalnie i czeka na poświadczenia na ekranie logowania.

## <a name="cause"></a>Przyczyna

Maszyna wirtualna ma statyczny adres IP, który jest zdefiniowany w interfejsie sieciowym w systemie Windows. Ten adres IP różni się od adresu zdefiniowanego w witrynie Azure portal.

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemu operacyjnego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj kontrolki szeregowej, aby włączyć usługę DHCP lub [zresetować interfejs sieciowy](reset-network-interface.md) maszyny Wirtualnej.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowego

1. Połącz się z [konsolą szeregową i otwórz wystąpienie CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, zobacz [Resetowanie interfejsu sieciowego](reset-network-interface.md).
2. Sprawdź, czy usługa DHCP jest wyłączona w interfejsie sieciowym:

        netsh interface ip show config
3. Jeśli usługa DHCP jest wyłączona, przywróć konfigurację interfejsu sieciowego, aby użyć usługi DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Na przykład, jeśli interfejs interwork nazywa "Ethernet 2", uruchom następujące polecenie:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Ponownie skonserwuj konfigurację IP, aby upewnić się, że interfejs sieciowy jest teraz poprawnie skonfigurowany. Nowy adres IP powinien być zgodny z adresem dostarczonym przez platformę Azure.

        netsh interface ip show config

    W tym momencie nie trzeba ponownie uruchamiać maszyny Wirtualnej. Maszyna wirtualna będzie z powrotem osiągalne.

Następnie, jeśli chcesz skonfigurować statyczny adres IP dla maszyny Wirtualnej, zobacz [Konfigurowanie statycznych adresów IP dla maszyny Wirtualnej](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).