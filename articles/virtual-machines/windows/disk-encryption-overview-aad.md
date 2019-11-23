---
title: Azure Disk Encryption z usługą Azure AD (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące korzystania z systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 33b257e9d344fc31df072509f105d2e8fd1bd29b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245177"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption z usługą Azure AD (poprzednia wersja)

**Nowa wersja Azure Disk Encryption eliminuje wymóg udostępniania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.**

W tym artykule opisano [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](disk-encryption-overview.md) z dodatkowymi wymaganiami i wymaganiami wstępnymi dotyczącymi Azure Disk Encryption z usługą Azure AD (poprzednie wydanie). Sekcja [obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems) pozostaje taka sama.

## <a name="networking-and-group-policy"></a>Sieci i zasady grupy

**Aby włączyć funkcję usługi Azure Disk Encryption przy użyciu starszej usługi AAD składnia parametru, maszyny wirtualne IaaS musi spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:** 
  - Aby uzyskać tokenu, połączyć się z magazynem kluczy, maszyn wirtualnych IaaS musi być w stanie połączyć się z punktem końcowym usługi Azure Active Directory, \[login.microsoftonline.com\].
  - Można zapisać klucze szyfrowania do magazynu kluczy, maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego magazynu kluczy.
  - Maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego usługi Azure storage, który hostuje repozytorium rozszerzenie platformy Azure i konto magazynu platformy Azure, w którym przechowywane są pliki wirtualnego dysku twardego.
  -  Zasady zabezpieczeń ogranicza dostęp do Internetu z maszyn wirtualnych platformy Azure, przypadku rozwiązać poprzedni identyfikator URI i skonfigurowania określonych Reguła zezwalająca na łączności wychodzącej do adresów IP. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault za zaporą](../../key-vault/key-vault-access-behind-firewall.md).
  - W Windows Jeśli jawnie wyłączyć protokół TLS 1.0, a nie zaktualizowano programu .NET w wersji 4.6 lub nowszy następującą zmianę w rejestrze powoduje włączenie ADE wybrać nowszą wersję protokołu TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Zasady grupy:**
 - Rozwiązanie Azure Disk Encryption dla maszyn wirtualnych IaaS Windows korzysta z zewnętrznego ochrony klucza funkcji BitLocker. Dla maszyn wirtualnych przyłączonych do domeny, nie Wypchnij żadnych zasad grupy, które wymuszają modułu TPM funkcje ochrony kluczy. Aby uzyskać informacje o zasadach grupy "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [dokumentacja zasad grupy funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika informacje odzyskiwania funkcji BitLocker — > Zezwalaj na 256-bitowy klucz odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption zakończy się niepowodzeniem w przypadku niezgodności niestandardowych ustawień zasad grupy dla funkcji BitLocker. Na komputerach, które nie mają poprawne ustawienie, należy zastosować nowe zasady, wymusić nowe zasady w celu aktualizacji (/ Force gpupdate.exe) i ponowne uruchomienie, może być wymagane.  

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące magazynu kluczy szyfrowania  

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednie wydanie)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Następne kroki

- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)
- [Włączanie Azure Disk Encryption z usługą Azure AD na maszynach wirtualnych z systemem Windows (poprzednia wersja)](disk-encryption-windows-aad.md)
- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)