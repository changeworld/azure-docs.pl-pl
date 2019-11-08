---
title: Tworzenie obrazu maszyny wirtualnej użytkownika dla portalu Azure Marketplace
description: Zawiera listę czynności i odwołań wymaganych do utworzenia obrazu maszyny wirtualnej użytkownika.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818698"
---
# <a name="create-a-user-vm-image"></a>Tworzenie obrazu maszyny wirtualnej użytkownika

W tym artykule opisano dwa ogólne kroki wymagane do utworzenia obrazu niezarządzanego na podstawie uogólnionego wirtualnego dysku twardego.  Podano informacje, które przeprowadzą Cię przez poszczególne kroki: Przechwytywanie obrazu i uogólnianie obrazu.


## <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny wirtualnej

Instrukcje zawarte w następującym artykule dotyczą przechwytywania maszyny wirtualnej, która odnosi się do podejścia dostępu:

-  PowerShell: [Tworzenie niezarządzanego obrazu maszyny wirtualnej na podstawie maszyny wirtualnej platformy Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfejs wiersza polecenia platformy Azure: [jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../../virtual-machines/linux/capture-image.md)
-  Interfejs API: [przechwytywanie Virtual Machines](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Uogólnianie obrazu maszyny wirtualnej

Ponieważ obraz użytkownika został wygenerowany z wcześniej uogólnionego wirtualnego dysku twardego, powinien również być uogólniony.  Ponownie wybierz następujący artykuł, który odnosi się do mechanizmu dostępu.  (Być może dysk został już uogólniony podczas przechwycenia).

-  PowerShell: [uogólnianie maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfejs wiersza polecenia platformy Azure: [krok 2. Tworzenie obrazu maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  Interfejs API: [Virtual Machines-generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Następne kroki

Następnie utworzysz [certyfikat](cpp-create-key-vault-cert.md) i zapiszesz go w nowym Azure Key Vault.  Ten certyfikat jest wymagany do nawiązania bezpiecznego połączenia usługi WinRM z maszyną wirtualną.
