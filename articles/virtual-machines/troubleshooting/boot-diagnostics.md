---
title: Diagnostyka rozruchu dla maszyn wirtualnych na platformie Azure | Microsoft docs
description: Przegląd debugowania funkcji dwóch maszyn wirtualnych na platformie Azure
services: virtual-machines
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 64641f8acfe7b58763756e2a0707fa799ee804b2
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414088"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak rozwiązywać problemy z maszyn wirtualnych na platformie Azure przy użyciu diagnostyki rozruchu

Pomoc techniczna dotycząca dwie funkcje debugowania jest teraz dostępna na platformie Azure: dane wyjściowe konsoli i tworzenia zrzutów ekranu pomocy technicznej dla modelu wdrażania usługi Resource Manager w maszynach wirtualnych platformy Azure. 

Podczas przełączania z własnego obrazu na platformie Azure lub nawet wykonywania rozruchu jednego z obrazów platformy, może wystąpić wiele przyczyn, dlaczego pobiera maszynę wirtualną do stanu uniemożliwiającego. Te funkcje umożliwiają łatwe diagnozowanie i odzyskiwanie maszyn wirtualnych po niepowodzeniach rozruchu.

W przypadku maszyn wirtualnych systemu Linux można łatwo wyświetlić dane wyjściowe konsoli dziennika z poziomu portalu. W przypadku systemów Windows i Linux maszyn wirtualnych platformy Azure umożliwia również wyświetlenie zrzutu ekranu maszyny Wirtualnej z funkcji hypervisor. Obie te funkcje są obsługiwane w przypadku maszyn wirtualnych platformy Azure we wszystkich regionach. Należy pamiętać, że może minąć do 10 minut, zanim zrzuty ekranu i dane wyjściowe pojawią się na koncie magazynu.

## <a name="common-boot-errors"></a>Typowe błędy rozruchu

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Nie znaleziono systemu operacyjnego](https://support.microsoft.com/help/4010142)
- [Niepowodzenie rozruchu lub błąd INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Włączanie diagnostyki na nowej maszynie wirtualnej
1. Podczas tworzenia nowej maszyny wirtualnej w witrynie Azure Portal wybierz **usługi Azure Resource Manager** z listy rozwijanej modelu wdrażania:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. W **ustawienia**, Włącz **diagnostykę rozruchu**, a następnie wybierz konto magazynu, które chcesz umieścić te pliki diagnostyczne.
 
    ![Tworzenie maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Funkcja diagnostyki rozruchu nie obsługuje konta magazynu premium storage. Jeśli używasz konta usługi premium storage dla diagnostyki rozruchu po uruchomieniu maszyny Wirtualnej można otrzymać błąd StorageAccountTypeNotSupported.
    >
    > 

3. Jeśli wykonujesz wdrożenie z szablonu usługi Azure Resource Manager, przejdź do zasobu maszyny wirtualnej i Dołącz sekcję profilu diagnostyki. Pamiętaj, aby użyć nagłówka wersji interfejsu API „2015-06-15”.

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

Aby wdrożyć przykładowe maszyny wirtualnej z włączoną diagnostyką rozruchu, zapoznaj się z naszym repozytorium w tym miejscu.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Włącz diagnostykę rozruchu na istniejącej maszyny wirtualnej 

Aby włączyć diagnostykę rozruchu na istniejącej maszyny wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie wybierz maszynę wirtualną.
2. W **pomoc techniczna i rozwiązywanie problemów z**, wybierz opcję **diagnostykę rozruchu** > **ustawienia**, Zmień stan na **na**, a następnie Wybierz konto magazynu. 
4. Upewnij się, że wybrano opcję Diagnostyka rozruchu, a następnie zapisz zmiany.

    ![Aktualizowanie istniejącej maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Uruchom ponownie maszynę wirtualną, aby zmiany zaczęły obowiązywać.


