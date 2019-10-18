---
title: Włącz Azure Disk Encryption dla Virtual Machine Scale Sets
description: Ten artykuł zawiera instrukcje dotyczące włączania Microsoft Azure szyfrowania dysków dla Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530838"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption Virtual Machine Scale Sets

Azure Disk Encryption zapewnia szyfrowanie woluminów systemu operacyjnego i dysków danych maszyn wirtualnych, co pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań w zakresie zabezpieczeń i bezpieczeństwa organizacji. Aby dowiedzieć się więcej, zobacz [Azure Disk Encryption: maszyny wirtualne systemu Linux](../virtual-machines/linux/disk-encryption-overview.md) i [Azure Disk Encryption: maszyny wirtualne z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption można również zastosować do zestawów skalowania maszyn wirtualnych z systemami Windows i Linux, w następujących przypadkach:
- Zestawy skalowania utworzone przy użyciu dysków zarządzanych. Usługa Azure Disk Encryption nie jest obsługiwana w przypadku zestawów skalowania dysków natywnych (lub niezarządzanych).
- System operacyjny i woluminy danych w zestawach skalowania systemu Windows.
- Woluminy danych w zestawach skalowania systemu Linux. Szyfrowanie dysków systemu operacyjnego nie jest obsługiwane w przypadku zestawów skalowania w systemie Linux.

Podstawy Azure Disk Encryption dla zestawów skalowania maszyn wirtualnych można uzyskać w zaledwie kilka minut, korzystając z [funkcji szyfrowania zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md) lub [szyfrowania zestawów skalowania maszyn wirtualnych przy użyciu samouczków interfejsu wiersza polecenia platformy Azure](disk-encryption-powershell.md) .

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md)
- [Użyj Azure Disk Encryption z sekwencjonowaniem rozszerzenia zestawu skalowania maszyn wirtualnych](disk-encryption-extension-sequencing.md)