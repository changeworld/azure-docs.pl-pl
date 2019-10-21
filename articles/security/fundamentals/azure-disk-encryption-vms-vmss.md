---
title: Azure Disk Encryption maszyn wirtualnych i zestawów skalowania maszyn wirtualnych
description: Ten artykuł zawiera omówienie Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599957"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption maszyn wirtualnych i zestawów skalowania maszyn wirtualnych

Usługę Azure Disk Encryption można zastosować zarówno do maszyn wirtualnych z systemem Linux, jak i Windows, jak również do zestawów skalowania maszyn wirtualnych. 

## <a name="linux-virtual-machines"></a>Maszyny wirtualne z systemem Linux

Poniższe artykuły zawierają wskazówki dotyczące szyfrowania maszyn wirtualnych z systemem Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Bieżąca wersja Azure Disk Encryption

- [Omówienie Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Scenariusze Azure Disk Encryption na maszynach wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption przykładowe skrypty](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption często zadawane pytania](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Szyfrowanie dysków Azure za pomocą usługi Azure AD (poprzednia wersja)

- [Omówienie Azure Disk Encryption z usługą Azure AD dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption ze scenariuszami usługi Azure AD na maszynach wirtualnych z systemem Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Maszyny wirtualne z systemem Windows

Poniższe artykuły zawierają wskazówki dotyczące szyfrowania maszyn wirtualnych z systemem Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Bieżąca wersja Azure Disk Encryption

- [Omówienie Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Scenariusze Azure Disk Encryption na maszynach wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows za pomocą Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu Azure Portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption przykładowe skrypty](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Rozwiązywanie problemów Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption często zadawane pytania](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Szyfrowanie dysków Azure za pomocą usługi Azure AD (poprzednia wersja)

- [Omówienie Azure Disk Encryption z usługą Azure AD dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption ze scenariuszami usługi Azure AD na maszynach wirtualnych z systemem Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Poniższe artykuły zawierają wskazówki dotyczące szyfrowania zestawów skalowania maszyn wirtualnych.

- [Omówienie Azure Disk Encryption dla zestawów skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Szyfruj zestawy skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Szyfrowanie zestawów skalowania maszyn wirtualnych przy użyciu Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Użyj Azure Disk Encryption z sekwencjonowaniem rozszerzenia zestawu skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Następne kroki

- [Azure encryption overview (Szyfrowanie na platformie Azure — omówienie)](encryption-overview.md)
- [Szyfrowanie danych w spoczynku](encryption-atrest.md)
- [Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych](data-encryption-best-practices.md)
