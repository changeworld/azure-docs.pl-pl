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
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 59602977c1b7f6dd0524c6535d8458d3eb1a3f26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505915"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak rozwiązywać problemy z maszyn wirtualnych na platformie Azure przy użyciu diagnostyki rozruchu

Może istnieć wiele przyczyn, że maszyna wirtualna przechodzi do stanu uniemożliwiającego. Aby rozwiązać problemy z przy użyciu maszyn wirtualnych utworzonych za pomocą modelu wdrażania usługi Resource Manager, można użyć następujących funkcji debugowania: Dane wyjściowe konsoli i tworzenia zrzutów ekranu pomocy technicznej dla maszyn wirtualnych platformy Azure. 

W przypadku maszyn wirtualnych systemu Linux można wyświetlić dane wyjściowe konsoli dziennika z poziomu portalu. Dla maszyn wirtualnych systemów Windows i Linux system Azure umożliwia wyświetlenie zrzutu ekranu maszyny Wirtualnej z funkcji hypervisor. Obie funkcje są obsługiwane dla maszyn wirtualnych platformy Azure we wszystkich regionach. Należy pamiętać, że może minąć do 10 minut, zanim zrzuty ekranu i dane wyjściowe pojawią się na koncie magazynu.

Możesz wybrać **diagnostykę rozruchu** opcję, aby wyświetlić dziennik i zrzutu ekranu.

![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Włączanie diagnostyki na maszynie wirtualnej utworzonej przy użyciu witryny Azure Portal

Poniższa procedura dotyczy maszyny wirtualnej utworzonej przy użyciu modelu wdrażania usługi Resource Manager.

Na **zarządzania** na karcie **monitorowanie** sekcji, upewnij się, że **diagnostykę rozruchu** jest włączona. Z **konto magazynu diagnostyki** listę rozwijaną, wybierz konto magazynu, w której chcesz umieścić pliki diagnostyczne na liście.
 
![Tworzenie maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funkcja diagnostyki rozruchu nie obsługuje konta magazynu premium storage. Jeśli używasz konta usługi premium storage dla diagnostyki rozruchu po uruchomieniu maszyny Wirtualnej można otrzymać błąd StorageAccountTypeNotSupported.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Wdrażanie przy użyciu szablonu usługi Azure Resource Manager

Jeśli wykonujesz wdrożenie z szablonu usługi Azure Resource Manager, przejdź do zasobu maszyny wirtualnej i Dołącz sekcję profilu diagnostyki. Ustaw nagłówka wersji interfejsu API "2015-06-15" lub nowszej. Najnowsza wersja to "2018-10-01".

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

Profil diagnostyki umożliwia wybranie konta magazynu, na którym chcesz umieścić te dzienniki.

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

Aby uzyskać więcej informacji na temat wdrażania zasobów za pomocą szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Włącz diagnostykę rozruchu na istniejącej maszyny wirtualnej 

Aby włączyć diagnostykę rozruchu na istniejącej maszyny wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie wybierz maszynę wirtualną.
2. W **pomoc techniczna i rozwiązywanie problemów z** zaznacz **diagnostykę rozruchu**, a następnie wybierz **ustawienia** kartę.
3. W **diagnostykę rozruchu** ustawienia, zmienić stan **na**i z **konta magazynu** listy rozwijanej wybierz konto magazynu. 
4. Zapisz zmianę.

    ![Aktualizowanie istniejącej maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Należy ponownie uruchomić maszynę wirtualną, aby zmiana zaczęła obowiązywać.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Włącz diagnostykę rozruchu przy użyciu wiersza polecenia platformy Azure

Aby włączyć diagnostykę rozruchu na istniejącej maszynie wirtualnej platformy Azure, można użyć wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [diagnostyki rozruchu az vm](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
