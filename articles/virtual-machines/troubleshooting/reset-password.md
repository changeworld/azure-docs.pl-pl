---
title: Jak zresetować lokalne hasło systemu Linux na maszynach wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Przedstawianie kroków resetowania lokalnego hasła systemu Linux na maszynie Wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153572"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak zresetować lokalne hasło systemu Linux na maszynach wirtualnych platformy Azure

W tym artykule przedstawiono kilka metod resetowania haseł lokalnej maszyny wirtualnej systemu Linux (VM). Jeśli konto użytkownika wygasło lub chcesz po prostu utworzyć nowe konto, można użyć następujących metod, aby utworzyć nowe konto administratora lokalnego i ponownie uzyskać dostęp do maszyny Wirtualnej.

## <a name="symptoms"></a>Objawy

Nie można zalogować się do maszyny Wirtualnej i zostanie wyświetlony komunikat wskazujący, że użyte hasło jest niepoprawne. Ponadto nie można użyć programu VMAgent do resetowania hasła w witrynie Azure portal.

## <a name="manual-password-reset-procedure"></a>Procedura ręcznego resetowania hasła

> [!NOTE]
> Poniższe kroki nie ma zastosowania do maszyny Wirtualnej z dysku niezarządzanego.

1. Zrób migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, utwórz dysk z migawki, a następnie dołącz dysk do maszyny wirtualnej w celu rozwiązania problemu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną systemu Windows, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu portalu Azure](troubleshoot-recovery-disks-portal-linux.md).

2. Połącz się z maszyną wirtualną do rozwiązywania problemów za pomocą pulpitu zdalnego.

3.  Uruchom następujące polecenie SSH na maszynie wirtualnej, aby stać się superużytkownikiem.

    ```bash
    sudo su
    ```

4.  Uruchom **fdisk -l** lub spójrz na dzienniki systemowe, aby znaleźć nowo podłączony dysk. Znajdź nazwę dysku do zainstalowania. Następnie na maszynie Wirtualnej czasowej poszukaj w odpowiednim pliku dziennika.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe polecenia grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Utwórz punkt instalacji o nazwie **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Zamontuj dysk systemu operacyjnego w punkcie instalacji. Zwykle trzeba zamontować *sdc1* lub *sdc2*. Będzie to zależało od partycji hostingowej w *katalogu /etc* z uszkodzonego dysku komputera.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Przed wprowadzeniem jakichkolwiek zmian utwórz kopie podstawowych plików poświadczeń:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Zresetuj potrzebne hasło użytkownika:

    ```bash
    passwd <<USER>> 
    ```

9.  Przenieś zmodyfikowane pliki we właściwe miejsce na uszkodzonym dysku komputera.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Wróć do katalogu głównego i odinstaluj dysk.

    ```bash
    cd /
    umount /tempmount
    ```

11. W witrynie Azure portal odłączyć dysk od rozwiązywania problemów z maszyną wirtualną.

12. [Zmień dysk systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z maszyną wirtualną platformy Azure, dołączając dysk systemu operacyjnego do innej maszyny Wirtualnej platformy Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Narzędzie CLI platformy Azure: jak usunąć i ponownie wdrożyć maszynę wirtualną z dysku VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
