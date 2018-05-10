---
title: Rozruch diagnostyki dla maszyn wirtualnych systemu Linux na platformie Azure | Doc firmy Microsoft
description: Omówienie dwóch funkcji debugowania dla maszyn wirtualnych systemu Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>Rozwiązywanie problemów z maszyn wirtualnych systemu Linux na platformie Azure przy użyciu diagnostyki rozruchu

Na platformie Azure jest teraz dostępna obsługa dwóch funkcji debugowania: obsługa danych wyjściowych konsoli i zrzutu ekranu dla modelu wdrażania przy użyciu usługi Azure Virtual Machines Resource Manager. 

Podczas korzystania z własnego obrazu na platformie Azure, a nawet wykonywania rozruchu jednego z obrazów platformy, może wystąpić wiele przyczyn przejścia maszyny wirtualnej do stanu uniemożliwiającego rozruch. Te funkcje umożliwiają łatwe diagnozowanie i odzyskiwanie maszyn wirtualnych po niepowodzeniach rozruchu.

Dla maszyn wirtualnych z systemem Linux można łatwo wyświetlić dane wyjściowe konsoli dziennika za pomocą portalu:

![Azure Portal](./media/boot-diagnostics/screenshot1.png)
 
Jednak zarówno dla maszyn wirtualnych z systemem Linux, jak i Windows, platforma Azure umożliwia również wyświetlenie zrzutu ekranu maszyny wirtualnej za pomocą funkcji hypervisor:

![Błąd](./media/boot-diagnostics/screenshot2.png)

Obie te funkcje są obsługiwane dla maszyn wirtualnych platformy Azure we wszystkich regionach. Należy pamiętać, że może minąć do 10 minut, zanim zrzuty ekranu i dane wyjściowe pojawią się na koncie magazynu.

## <a name="common-boot-errors"></a>Typowe błędy rozruchu

- [Zagadnienia dotyczące systemu plików](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [Problemy z jądra](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [Błędy FSTAB](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Włączanie diagnostyki na nowej maszynie wirtualnej
1. Podczas tworzenia nowej maszyny wirtualnej w portalu Azure, wybierz **usługi Azure Resource Manager** z listy rozwijanej modelu wdrażania:
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. W **ustawienia**, Włącz **diagnostyki rozruchu**, a następnie wybierz konto magazynu, które chcesz umieścić te pliki diagnostyczne.
 
    ![Tworzenie maszyny wirtualnej](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Funkcja diagnostyki rozruchu obsługuje konto magazynu w warstwie premium. Jeśli używasz konta magazynu w warstwie premium dla diagnostyki rozruchu po ponownym uruchomieniu maszyny Wirtualnej zostanie zgłoszony błąd StorageAccountTypeNotSupported. 
    >
    > 

3. Jeśli wdrażasz za pomocą szablonu usługi Azure Resource Manager, przejdź do zasobu maszyny wirtualnej i Dołącz sekcji profilu diagnostyki. Pamiętaj, aby użyć nagłówka wersji interfejsu API „2015-06-15”.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Profil diagnostyki umożliwia wybranie konta magazynu, na którym chcesz umieścić te dzienniki.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Aby wdrożyć przykładowe maszyny wirtualnej z włączoną diagnostykę rozruchu, zapoznaj się z naszym repozytorium, w tym miejscu.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Włącz diagnostykę rozruchu w istniejącej maszyny wirtualnej 

Aby włączyć diagnostyki rozruchu na już istniejącej maszynie wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie wybierz maszynę wirtualną.
2. W **pomocy technicznej i rozwiązywania problemów**, wybierz pozycję **diagnostyki rozruchu** > **ustawienia**, Zmień stan na **na**, a następnie Wybierz konto magazynu. 
4. Upewnij się, że jest zaznaczona opcja diagnostyki rozruchu, a następnie zapisz zmiany.

    ![Aktualizowanie istniejącej maszyny wirtualnej](./media/boot-diagnostics/enable-for-existing-vm.png)

3. Uruchom ponownie maszynę wirtualną, aby zmiany zaczęły obowiązywać.

## <a name="next-steps"></a>Kolejne kroki

Jeśli zostanie wyświetlony komunikat o błędzie "Nie można pobrać zawartości dziennika", korzystając z diagnostyki rozruchu maszyny Wirtualnej, zobacz [nie może pobrać zawartości błędu dziennika w diagnostyki rozruchu maszyny Wirtualnej](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur).