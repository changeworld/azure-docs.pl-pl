---
title: Tworzenie obrazu maszyny Wirtualnej użytkownika w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyświetla listę kroków i odwołań wymagane do utworzenia obrazu maszyny Wirtualnej użytkownika.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744112"
---
# <a name="create-a-user-vm-image"></a>Tworzenie obrazu maszyny wirtualnej użytkownika

W tym artykule opisano dwa ogólne kroki wymagane do utworzenia obrazu niezarządzanego z uogólnionego wirtualnego dysku twardego.  Przeprowadzenie Cię przez kolejne kroki są podane odwołania: przechwycenie obrazu i obraz.


## <a name="capture-the-vm-image"></a>Przechwytywanie obrazu maszyny Wirtualnej

Postępuj zgodnie z instrukcjami w artykule na przechwytywanie maszyny Wirtualnej, która odnosi się do danej metody dostępu:

-  Program PowerShell: [Sposób tworzenia obrazu maszyny Wirtualnej niezarządzanej maszyny wirtualnej platformy Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfejs wiersza polecenia platformy Azure: [Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines — przechwytywania](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Uogólnij obraz maszyny Wirtualnej

Ponieważ obraz użytkownika został wygenerowany z wcześniej uogólnionego wirtualnego dysku twardego, również powinien być uogólniony.  Ponownie wybierz następującego artykułu, który odnosi się do mechanizmu Twojego dostępu.  (Możesz może mieć już uogólniony dysku podczas została przechwycona.)

-  Program PowerShell: [Uogólnianie maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfejs wiersza polecenia platformy Azure: [Krok 2. Tworzenie obrazu maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Uogólnianie maszyny wirtualne —](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Kolejne kroki

Następnie zostanie [Utwórz certyfikat z](cpp-create-key-vault-cert.md) i zapisz go w nowej usłudze Azure Key Vault.  Ten certyfikat jest wymagany do ustanowienia bezpiecznego połączenia usługi WinRM na maszynie wirtualnej.
