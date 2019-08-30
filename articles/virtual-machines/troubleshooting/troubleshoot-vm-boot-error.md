---
title: Błąd rozruchu maszyny wirtualnej platformy Azure
description: Nie można uruchomić maszyny wirtualnej, ponieważ maszyna wirtualna została wprowadzona w konsoli ratowniczej
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 14bf7b289f3d03dd1437a18b0b5bd35fdec567ea
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143312"
---
# <a name="vm-boot-error"></a>Błąd rozruchu maszyny wirtualnej

Określono, że maszyna wirtualna (VM) została dodana do konsoli ratowniczej. Ten problem występuje, gdy maszyna wirtualna z systemem Linux ma ostatnio zastosowane zmiany jądra, takie jak uaktualnienie jądra, i nie jest już prawidłowo uruchamiana z powodu błędów jądra podczas procesu rozruchu. Podczas procesu rozruchu, gdy moduł ładujący rozruchu próbuje zlokalizować jądro systemu Linux i przełączać do niego kontrolę rozruchu, maszyna wirtualna przechodzi do konsoli ratowniczej, gdy nastąpi niepowodzenie.

Jeśli okaże się, że nie można połączyć się z maszyną wirtualną w przyszłości, można wyświetlić zrzut ekranu maszyny wirtualnej za pomocą bloku diagnostyki rozruchu w Azure Portal. Może to pomóc zdiagnozować problem i określić, czy przyczyną jest podobny błąd rozruchu.

## <a name="recommended-steps"></a>Zalecane czynności

Postępuj zgodnie z poniższą procedurą zaradczą w zależności od otrzymanego błędu:

### <a name="error---unknown-filesystem"></a>Błąd — nieznany system plików

* Jeśli zostanie wyświetlony błąd **nieznany system plików**, ten błąd może wynikać z uszkodzenia systemu plików na partycji rozruchowej lub nieprawidłowej konfiguracji jądra.

   * W przypadku problemów z systemem plików wykonaj kroki opisane w artykule [odzyskiwanie systemu Linux: Nie można przeprowadzić protokołu SSH z maszyną wirtualną z systemem Linux z powodu błędów](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)systemu plików (fsck, węzłów i).
   * W przypadku problemów jądra postępuj zgodnie z instrukcjami w [artykule Odzyskiwanie systemu Linux: Ręczne rozwiązywanie problemów z rozruchem,](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)związanych z [problemami jądra lub odzyskiwaniem systemu Linux: Rozwiązywanie problemów z rozruchem związanym z](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)problemami jądra przy użyciu chroot.
   
### <a name="error---file-not-found"></a>Błąd — nie znaleziono pliku

* Jeśli wystąpi **błąd błędu 15: Nie znaleziono pliku lub nie znaleziono początkowego** dysku pamięci RAM lub **pliku oryginalnych initrd/initramfs**, wykonaj poniższe kroki.

    * Dla brakującego pliku `/boot/grub2/grub.cfg` lub `initrd/initramfs` Kontynuuj przy użyciu następującego procesu:

    1. Upewnij `/etc/default/grub` się, że istnieją poprawne/odpowiednie ustawienia. Jeśli nie wiesz, które są ustawienia domyślne, możesz zaewidencjonować działającą maszynę wirtualną.

    2. Następnie uruchom następujące polecenie, aby ponownie wygenerować jego konfigurację:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Jeśli `/boot/grub/menu.lst`brakuje pliku, ten błąd jest przeznaczony dla starszych wersji systemu operacyjnego (**RHEL 6. x**, **CentOS 6. x** i **Ubuntu 14,04**), aby polecenia mogły odroczyć. Aby upewnić się, że zostaną podane poprawne polecenia, musisz uruchomić stary serwer i przetestować go.

### <a name="error---no-such-partition"></a>Błąd — Brak partycji

* Jeśli wystąpi błąd **nie ma takiej partycji**, zapoznaj się z [scenariuszem przypadku: "nie ma takiej partycji" podczas próby uruchomienia maszyny wirtualnej po próbie zwiększenia dysku systemu operacyjnego](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Błąd — nie znaleziono pliku grub. cfg

* Jeśli otrzymujesz błąd **/Boot/grub2/grub.cfg nie znaleziono pliku**, wykonaj poniższe kroki.

    * Dla brakującego pliku `/boot/grub2/grub.cfg` lub `initrd/initramfs` Kontynuuj przy użyciu następującego procesu:

    1. Upewnij `/etc/default/grub` się, że istnieją poprawne/odpowiednie ustawienia. Jeśli nie wiesz, które są ustawienia domyślne, możesz zaewidencjonować działającą maszynę wirtualną.

    2. Następnie uruchom następujące polecenie, aby ponownie wygenerować jego konfigurację: `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * Jeśli `/boot/grub/menu.lst`brakuje pliku, ten błąd jest przeznaczony dla starszych wersji systemu operacyjnego (**RHEL 6. x**, **CentOS 6. x** i **Ubuntu 14,04**), aby polecenia mogły odroczyć. Uruchom stary serwer i przetestuj go, aby upewnić się, że podano prawidłowe polecenia.

## <a name="next-steps"></a>Następne kroki

* [Omówienie agenta maszyny wirtualnej platformy Azure](../extensions/agent-windows.md)
* [Rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](../extensions/features-windows.md)

