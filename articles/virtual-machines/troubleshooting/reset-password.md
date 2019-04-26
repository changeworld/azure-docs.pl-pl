---
title: Jak zresetować lokalne hasło systemu Linux na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Wprowadź instrukcje resetowania hasła lokalnego systemu Linux na maszynie Wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306955"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak zresetować lokalne hasło systemu Linux na maszynach wirtualnych platformy Azure

W tym artykule przedstawiono kilka metod do resetowania haseł usługi lokalnego systemu Linux maszyny wirtualnej (VM). Jeśli konto użytkownika wygasło lub po prostu chcesz utworzyć nowe konto, można użyć następujących metod do tworzenia nowego konta administratora lokalnego i ponownie uzyskać dostęp do maszyny Wirtualnej.

## <a name="symptoms"></a>Objawy

Nie można zalogować się do maszyny Wirtualnej, a pojawi się komunikat informujący o tym, że hasło używane jest niepoprawny. Ponadto nie można użyć VMAgent, aby zresetować hasło w witrynie Azure portal.

## <a name="manual-password-reset-procedure"></a>Procedury resetowania haseł usługi ręczna

1.  Usuń maszynę Wirtualną i Zachowaj dołączonych dysków.

2.  Dołącz dysk systemu operacyjnego jako dysku danych do innej maszyny Wirtualnej z danych czasowych w tej samej lokalizacji.

3.  Uruchom następujące polecenie SSH na maszynie Wirtualnej danych czasowych stają się administrator.

    ```bash
    sudo su
    ```

4.  Uruchom **fdisk -l** lub Przyjrzyj się dzienniki systemowe, aby znaleźć nowo dołączonego dysku. Znajdź nazwę dysku do zainstalowania. Następnie na tymczasowa maszyna wirtualna, Znajdź odpowiedni plik dziennika.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe polecenia grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Tworzenie punktu instalacji o nazwie **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Zainstaluj dysk systemu operacyjnego w punkcie instalacji. Zazwyczaj trzeba zainstalować *sdc1* lub *sdc2*. To zależy od hostingu partycji w */etc* katalogu z dysku maszyny przerwane.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Przed wprowadzeniem jakichkolwiek zmian, należy utworzyć kopie podstawowe pliki poświadczeń:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Resetowanie hasła użytkownika, które są potrzebne:

    ```bash
    passwd <<USER>> 
    ```

9.  Przenieś zmodyfikowanych plików do poprawnej lokalizacji na dysku maszyny przerwane.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Wróć do głównego i odinstalować dysk.

    ```bash
    cd /
    umount /tempmount
    ```

11. Odłącz dysk z portalu zarządzania.

12. Ponowne utworzenie maszyny Wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z maszyny Wirtualnej platformy Azure, dołączając dysk systemu operacyjnego do innej maszyny Wirtualnej platformy Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Interfejs wiersza polecenia platformy Azure: Jak usunąć i ponownie wdróż Maszynę wirtualną z wirtualnego dysku twardego](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
