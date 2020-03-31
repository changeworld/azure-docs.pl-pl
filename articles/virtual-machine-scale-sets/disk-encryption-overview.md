---
title: Włączanie szyfrowania dysków platformy Azure dla zestawów skalowania maszyn wirtualnych
description: Ten artykuł zawiera instrukcje dotyczące włączania szyfrowania dysków platformy Microsoft Azure dla zestawów skalowania maszyn wirtualnych
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278969"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Szyfrowanie dysków platformy Azure dla zestawów skalowania maszyn wirtualnych

Usługa Azure Disk Encryption zapewnia szyfrowanie woluminów dla dysków systemu operacyjnego i danych maszyn wirtualnych, pomagając chronić i chronić dane w celu spełnienia zobowiązań dotyczących zabezpieczeń i zgodności organizacji. Aby dowiedzieć się więcej, zobacz [Szyfrowanie dysków platformy Azure: maszyny wirtualne z systemem Linux](../virtual-machines/linux/disk-encryption-overview.md) i [szyfrowanie dysków platformy Azure: maszyny wirtualne z systemem Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Szyfrowanie dysków platformy Azure można również zastosować do zestawów skalowania maszyn wirtualnych systemu Windows i Linux, w następujących przypadkach:
- Zestawy skalowania utworzone za pomocą dysków zarządzanych. Szyfrowanie usługi Azure Disk nie jest obsługiwane dla natywnych (lub niezarządzanych) zestawów skalowania dysku.
- System operacyjny i woluminy danych w zestawach skalowania systemu Windows.
- Woluminy danych w zestawach skalowania systemu Linux. Szyfrowanie dysku systemu operacyjnego NIE jest obecnie obsługiwane dla zestawów skalowania systemu Linux.

Możesz poznać podstawy szyfrowania dysków platformy Azure dla zestawów skalowania maszyny wirtualnej w ciągu zaledwie kilku minut za pomocą [zestawów skalowania szyfruj maszynę wirtualną przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md) lub [zestawów skalowania maszyny wirtualnej przy użyciu samouczków programu Azure PowerShell.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Następne kroki

- [Szyfruj zestawy skalowania maszyny wirtualnej przy użyciu usługi Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md)
- [Używanie szyfrowania dysku platformy Azure z sekwencjonowaniem rozszerzenia zestawu skalowania maszyny wirtualnej](disk-encryption-extension-sequencing.md)
