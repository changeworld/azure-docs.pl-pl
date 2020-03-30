---
title: Linux VM buty do Grub Rescue
description: Uruchomienie maszyny wirtualnej nie powiodło się, ponieważ maszyna wirtualna weszła do konsoli ratunkowej
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561953"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM buty do Grub Rescue

Firma Microsoft zidentyfikowała, że maszyna wirtualna (VM) weszła do konsoli ratunkowej. Ten problem występuje, gdy maszyna wirtualna z systemem Linux miała ostatnio zastosowane zmiany jądra, takie jak uaktualnienie jądra, i nie uruchamia się poprawnie z powodu błędów jądra podczas procesu rozruchu. Podczas procesu rozruchu, gdy program ładujący rozruchu próbuje zlokalizować jądro Linuksa i przekazać mu kontrolkę rozruchu, maszyna wirtualna wchodzi do konsoli ratunkowej, gdy przekazanie nie powiedzie się.

Jeśli okaże się, że nie można połączyć się z maszyną wirtualną w przyszłości, można wyświetlić zrzut ekranu maszyny Wirtualnej przy użyciu bloku diagnostyki rozruchu w witrynie Azure portal. Może to pomóc zdiagnozować problem i określić, czy przyczyną jest podobny błąd rozruchu.

## <a name="recommended-steps"></a>Zalecane czynności

Wykonaj poniższe kroki ograniczające zagrożenie w zależności od otrzymanego błędu:

### <a name="error---unknown-filesystem"></a>Błąd — nieznany system plików

* Jeśli pojawia się błąd **Nieznany system plików,** ten błąd może wynikać z uszkodzenia systemu plików na partycji rozruchowej lub niepoprawnej konfiguracji jądra.

   * W przypadku problemów z systemem plików wykonaj kroki opisane w artykule [Linux Recovery: Cannot SSH to Linux VM z powodu błędów systemu plików (fsck, iod)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * W przypadku problemów z jądrem wykonaj czynności opisane w artykule [Linux Recovery: Ręczne rozwiązywanie problemów nie rozruchowych związanych z problemami z jądrem](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)lub [Odzyskiwaniem Linuksa: Rozwiązywanie problemów innych niż bootowanie związanych z problemami z jądrem przy użyciu chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Błąd — nie znaleziono pliku

* Jeśli pojawia się błąd **15: Nie znaleziono pliku lub nie znaleziono początkowego dysku RAM** lub pliku **initrd/initramfs**, wykonaj poniższe czynności.

    * W przypadku `/boot/grub2/grub.cfg` brakującego pliku lub `initrd/initramfs` przejdź do następującego procesu:

    1. Upewnij `/etc/default/grub` się, że istnieją i ma poprawne / żądane ustawienia. Jeśli nie wiesz, które ustawienia są domyślne, możesz sprawdzić za pomocą działającej maszyny Wirtualnej.

    2. Następnie uruchom następujące polecenie, aby ponownie wygenerować jego konfigurację:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Jeśli brakującym `/boot/grub/menu.lst`plikiem jest , ten błąd jest dla starszych wersji systemu operacyjnego **(RHEL 6.x**, **Centos 6.x** i **Ubuntu 14.04**), więc polecenia mogą się różnić. Będziesz musiał rozkręcić stary serwer i przetestować, aby upewnić się, że odpowiednie polecenia są dostarczane.

### <a name="error---no-such-partition"></a>Błąd — brak takiej partycji

* Jeśli pojawia się błąd **Brak takiej partycji,** zapoznaj się z [case scenario: "no such partition" błąd podczas próby uruchomienia maszyny Wirtualnej po próbie rozszerzenia dysku systemu operacyjnego](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Błąd - nie znaleziono pliku grub.cfg

* Jeśli **nie znaleziono błędu /boot/grub2/grub.cfg,** wykonaj poniższe czynności.

    * W przypadku `/boot/grub2/grub.cfg` brakującego pliku lub `initrd/initramfs` przejdź do następującego procesu:

    1. Upewnij `/etc/default/grub` się, że istnieją i ma poprawne / żądane ustawienia. Jeśli nie wiesz, które ustawienia są domyślne, możesz sprawdzić za pomocą działającej maszyny Wirtualnej.

    2. Następnie uruchom następujące polecenie, aby ponownie `grub2-mkconfig -o /boot/grub2/grub.cfg`wygenerować jego konfigurację: .

   * Jeśli brakującym `/boot/grub/menu.lst`plikiem jest , ten błąd jest dla starszych wersji systemu operacyjnego **(RHEL 6.x**, **Centos 6.x** i **Ubuntu 14.04**), więc polecenia mogą odroczyć. Rozkręć stary serwer i przetestuj go, aby upewnić się, że są podane poprawne polecenia.

## <a name="next-steps"></a>Następne kroki

* [Usługa Azure Virtual Machine Agent — omówienie](../extensions/agent-windows.md)
* [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows](../extensions/features-windows.md)

