---
title: Tworzenie obrazu maszyny Wirtualnej użytkownika dla portalu Azure Marketplace
description: Wyświetla listę kroków i odwołań wymaganych do utworzenia obrazu maszyny Wirtualnej użytkownika.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273957"
---
# <a name="create-a-user-vm-image"></a>Tworzenie obrazu maszyny wirtualnej użytkownika

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](https://aka.ms/AzureVMTechAsset) do zarządzania zmigrowanych ofert.

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
