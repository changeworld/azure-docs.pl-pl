---
title: Szyfrowanie dysków platformy Azure dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych
description: Ten artykuł zawiera omówienie szyfrowania dysków platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062123"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Szyfrowanie dysków platformy Azure dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych

Szyfrowanie dysków Azure można zastosować zarówno do maszyn wirtualnych systemu Linux i Windows, jak i do zestawów skalowania maszyny wirtualnej. 

## <a name="linux-virtual-machines"></a>Maszyny wirtualne z systemem Linux

Poniższe artykuły zawierają wskazówki dotyczące szyfrowania maszyn wirtualnych systemu Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Bieżąca wersja szyfrowania dysków platformy Azure

- [Omówienie usługi Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu programu Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu witryny Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Schemat rozszerzenia szyfrowania dysków platformy Azure dla systemu Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Usługa Azure Disk Encryption — często zadawane pytania](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)

- [Omówienie szyfrowania dysków platformy Azure za pomocą usługi Azure AD dla maszyn wirtualnych systemu Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Szyfrowanie dysków platformy Azure w scenariuszach usługi Azure AD na maszynach wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Maszyny wirtualne z systemem Windows

Poniższe artykuły zawierają wskazówki dotyczące szyfrowania maszyn wirtualnych systemu Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Bieżąca wersja szyfrowania dysków platformy Azure

- [Omówienie usługi Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Scenariusze usługi Azure Disk Encryption na maszynach wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu programu Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu witryny Azure Portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Schemat rozszerzenia szyfrowania dysków platformy Azure dla systemu Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla usługi Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Przykładowe skrypty usługi Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów z usługą Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Usługa Azure Disk Encryption — często zadawane pytania](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Szyfrowanie dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)

- [Omówienie szyfrowania dysków platformy Azure za pomocą usługi Azure AD dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Szyfrowanie dysków platformy Azure w scenariuszach usługi Azure AD na maszynach wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure za pomocą usługi Azure AD (poprzednia wersja)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Poniższe artykuły zawierają wskazówki dotyczące szyfrowania zestawów skalowania maszyny wirtualnej.

- [Omówienie szyfrowania dysków platformy Azure dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Szyfruj zestawy skalowania maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Szyfrowanie zestawów skalowania maszyny wirtualnej przy użyciu programu Azure Powershell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Szyfruj zestawy skalowania maszyny wirtualnej przy użyciu usługi Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Tworzenie i konfigurowanie magazynu kluczy dla szyfrowania dysków platformy Azure](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Używanie szyfrowania dysku platformy Azure z sekwencjonowaniem rozszerzenia zestawu skalowania maszyny wirtualnej](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Następne kroki

- [Omówienie szyfrowania platformy Azure](encryption-overview.md)
- [Szyfrowanie danych w spoczynku](encryption-atrest.md)
- [Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych](data-encryption-best-practices.md)
