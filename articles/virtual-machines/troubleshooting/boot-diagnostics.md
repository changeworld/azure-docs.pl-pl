---
title: Diagnostyka rozruchu maszyn wirtualnych na platformie Azure | Microsoft Doc
description: Omówienie dwóch funkcji debugowania maszyn wirtualnych na platformie Azure
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: fe2427d008b49daa6222ca981994f0dc2fbea355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476590"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>Jak używać diagnostyki rozruchu do rozwiązywania problemów z maszynami wirtualnymi na platformie Azure

Może istnieć wiele powodów, dla których maszyna wirtualna wchodzi w stan nierozkładalny. Aby rozwiązać problemy z maszynami wirtualnymi utworzonymi przy użyciu modelu wdrażania usługi Resource Manager, można użyć następujących funkcji debugowania: Dane wyjściowe konsoli i obsługa zrzutu ekranu dla maszyn wirtualnych platformy Azure. 

W przypadku maszyn wirtualnych systemu Linux można wyświetlić dane wyjściowe dziennika konsoli z portalu. Dla maszyn wirtualnych systemu Windows i Linux platformy Azure umożliwia wyświetlenie zrzut ekranu maszyny wirtualnej z funkcji hypervisor. Obie funkcje są obsługiwane dla maszyn wirtualnych platformy Azure we wszystkich regionach. Uwaga, zrzuty ekranu i dane wyjściowe mogą potrwać do 10 minut, aby pojawić się na koncie magazynu.

Można wybrać opcję **Diagnostyka rozruchu,** aby wyświetlić dziennik i zrzut ekranu.

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

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Włączanie diagnostyki na maszynie wirtualnej utworzonej przy użyciu portalu Azure Portal

Poniższa procedura dotyczy maszyny wirtualnej utworzonej przy użyciu modelu wdrażania Menedżera zasobów.

Na karcie **Zarządzanie** w sekcji **Monitorowanie** upewnij się, że **diagnostyka rozruchu** jest włączona. Z listy rozwijanej **Konto magazynu diagnostyki** wybierz konto magazynu, na którym mają być umieszczane pliki diagnostyczne.
 
![Tworzenie maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> Funkcja diagnostyki rozruchu nie obsługuje konta magazynu w warstwie Premium. Jeśli używasz konta magazynu w wersji premium do diagnostyki rozruchowej, może pojawić się błąd StorageAcCountTypeNotSupported po uruchomieniu maszyny Wirtualnej.
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Wdrażanie z szablonu usługi Azure Resource Manager

Jeśli wdrażasz z szablonu usługi Azure Resource Manager, przejdź do zasobu maszyny wirtualnej i dołącz sekcję profilu diagnostyki. Ustaw nagłówek wersji interfejsu API na "2015-06-15" lub nowszych. Najnowsza wersja to "2018-10-01".

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

Aby uzyskać więcej informacji na temat wdrażania zasobów przy użyciu szablonów, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Włączanie diagnostyki rozruchu na istniejącej maszynie wirtualnej 

Aby włączyć diagnostykę rozruchu na istniejącej maszynie wirtualnej, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie wybierz maszynę wirtualną.
2. W sekcji **Pomoc techniczna + rozwiązywanie problemów** wybierz pozycję **Diagnostyka rozruchu**, a następnie wybierz kartę **Ustawienia.**
3. W **ustawieniach diagnostyki rozruchu** zmień stan **na Włączone**, a z listy rozwijanej Konto **magazynu** wybierz konto magazynu. 
4. Zapisz zmianę.

    ![Aktualizowanie istniejącej maszyny wirtualnej](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

Aby zmiana została uwzględnić, należy ponownie uruchomić maszynę wirtualną.

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Włączanie diagnostyki rozruchu przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można włączyć diagnostykę rozruchu na istniejącej maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest).
