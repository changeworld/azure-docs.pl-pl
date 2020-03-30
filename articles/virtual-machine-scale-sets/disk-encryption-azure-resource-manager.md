---
title: Tworzenie i szyfrowanie zestawu skalowania maszyny wirtualnej za pomocą szablonów usługi Azure Resource Manager
description: W tym przewodniku Szybki start dowiesz się, jak tworzyć i szyfrować zestaw skalowania maszyny wirtualnej za pomocą szablonów usługi Azure Resource Manager
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72530000"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Szyfruj zestawy skalowania maszyny wirtualnej za pomocą usługi Azure Resource Manager

Można szyfrować lub odszyfrowywać zestawy skalowania maszyny wirtualnej systemu Linux przy użyciu szablonów usługi Azure Resource Manager.

## <a name="deploying-templates"></a>Wdrażanie szablonów

Najpierw wybierz szablon, który pasuje do twojego scenariusza.

- [Włączanie szyfrowania dysku w uruchomionym zestawie skalowania maszyny wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Włączanie szyfrowania dysku w uruchomionym zestawie skalowania maszyny wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Wdrażanie zestawu maszyn wirtualnych maszyn wirtualnych z systemem Linux za pomocą jumpboxa i umożliwia szyfrowanie w zestawach skalowania maszyn wirtualnych systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Wdrażanie zestawu skalowania maszyny wirtualnej maszyn z systemem Windows z jumpbox i umożliwia szyfrowanie w zestawach skalowania maszyny wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Wyłącz szyfrowanie dysku w uruchomionym zestawie skalowania maszyny wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Wyłączanie szyfrowania dysku w uruchomionym zestawie skalowania maszyny wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Następnie wykonaj poniższe czynności:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Następne kroki

- [Szyfrowanie dysków platformy Azure dla zestawów skalowania maszyn wirtualnych](disk-encryption-overview.md)
- [Szyfruj zestawy skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](disk-encryption-cli.md)
- [Szyfrowanie zestawów skalowania maszyny wirtualnej przy użyciu programu Azure PowerShell](disk-encryption-powershell.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](disk-encryption-key-vault.md)
- [Używanie szyfrowania dysku platformy Azure z sekwencjonowaniem rozszerzenia zestawu skalowania maszyny wirtualnej](disk-encryption-extension-sequencing.md)