---
title: Tworzenie obrazu maszyny Wirtualnej użytkownika dla portalu Azure Marketplace
description: Wyświetla listę kroków i odwołań wymaganych do utworzenia obrazu maszyny Wirtualnej użytkownika.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278011"
---
# <a name="create-a-user-vm-image"></a>Tworzenie obrazu maszyny wirtualnej użytkownika

W tym artykule wyjaśniono dwa ogólne kroki wymagane do utworzenia obrazu niezarządzanego z uogólnionego dysku VHD.  Odniesienia są dostarczane, aby poprowadzić Cię przez każdy krok: przechwyć obraz i uogólnić obraz.


## <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny Wirtualnej

Użyj instrukcji w poniższym artykule na przechwytywanie maszyny Wirtualnej, która odpowiada podejście dostępu:

-  Program PowerShell: [jak utworzyć niezarządzany obraz maszyny Wirtualnej z maszyny Wirtualnej platformy Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Narzędzie CLI platformy Azure: [jak utworzyć obraz maszyny wirtualnej lub dysku VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [Maszyny wirtualne — przechwytywanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Uogólnianie obrazu maszyny Wirtualnej

Ponieważ obraz użytkownika został wygenerowany z wcześniej uogólnionego dysku VHD, powinien on również zostać uogólniony.  Ponownie wybierz następujący artykuł, który odpowiada mechanizmowi dostępu.  (Być może dysk został już uogólniony podczas jego przechwytywania).

-  Program PowerShell: [Uogólnianie maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfejsu wiersza polecenia platformy Azure: [krok 2: tworzenie obrazu maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  INTERFEJS API: [Maszyny wirtualne — uogólnianie](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Następne kroki

Następnie [utworzysz certyfikat](cpp-create-key-vault-cert.md) i zapiszesz go w nowej usłudze Azure Key Vault.  Ten certyfikat jest wymagany do ustanowienia bezpiecznego połączenia Usługi WinRM z maszyną wirtualną.
