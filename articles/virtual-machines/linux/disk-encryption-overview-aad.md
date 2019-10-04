---
title: Azure Disk Encryption z aplikacja usługi Azure AD wymaganiami wstępnymi (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące używania Microsoft Azure szyfrowania dysków dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b0e3e683b2c103bc7f9b6812115e2e7a5d871034
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828677"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption z usługą Azure AD (poprzednia wersja)

**Nowa wersja Azure Disk Encryption eliminuje wymóg udostępniania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.**

Ten artykuł zawiera dodatkowe [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md) z dodatkowymi wymaganiami i wymaganiami wstępnymi dotyczącymi Azure Disk Encryption z usługą Azure AD (poprzednie wydanie).

Informacje w tych sekcjach pozostają takie same:

- [Obsługiwane maszyny wirtualne i systemy operacyjne](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Dodatkowe wymagania dotyczące maszyn wirtualnych](disk-encryption-overview.md#additional-vm-requirements)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="networking-and-group-policy"></a>Sieci i zasady grupy

**Aby włączyć funkcję Azure Disk Encryption przy użyciu starszej składni parametrów usługi AAD, maszyny wirtualne IaaS muszą spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:** 
  - Aby uzyskać token, aby połączyć się z magazynem kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym Azure Active Directory @no__t -0login. microsoftonline. com @ no__t-1.
  - Aby zapisać klucze szyfrowania do magazynu kluczy, maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym magazynu kluczy.
  - Maszyna wirtualna IaaS musi mieć możliwość nawiązania połączenia z punktem końcowym usługi Azure Storage, który obsługuje repozytorium rozszerzeń platformy Azure i konto usługi Azure Storage, które obsługuje pliki VHD.
  -  Jeśli zasady zabezpieczeń ograniczają dostęp z maszyn wirtualnych platformy Azure do Internetu, można rozwiązać poprzedni identyfikator URI i skonfigurować określoną regułę, aby zezwolić na połączenia wychodzące z adresami IP. Aby uzyskać więcej informacji, zobacz [Azure Key Vault za zaporą](../../key-vault/key-vault-access-behind-firewall.md).
  - W systemie Windows, jeśli protokół TLS 1,0 został jawnie wyłączony, a wersja platformy .NET nie została zaktualizowana do wersji 4,6 lub nowszej, następująca zmiana w rejestrze umożliwi wybranie nowszej wersji protokołu TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Zasady grupy:**
 - Rozwiązanie Azure Disk Encryption używa funkcji ochrony klucza zewnętrznego funkcji BitLocker dla maszyn wirtualnych z systemem Windows IaaS. W przypadku maszyn wirtualnych przyłączonych do domeny nie wypychanie żadnych zasad grupy, które wymuszają funkcje ochrony modułu TPM. Aby uzyskać informacje o zasadach grupy dla "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [BitLocker zasady grupy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny z niestandardowymi zasadami grupy muszą zawierać następujące ustawienie: [Konfigurowanie magazynu użytkownika informacje odzyskiwania funkcji BitLocker — > Zezwalaj na 256-bitowy klucz odzyskiwania](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption zakończy się niepowodzeniem w przypadku niezgodności niestandardowych ustawień zasad grupy dla funkcji BitLocker. Na maszynach, które nie mają poprawnego ustawienia zasad, Zastosuj nowe zasady, Wymuś aktualizację nowych zasad (gpupdate. exe/Force), a następnie ponowne uruchomienie może być wymagane.  

## <a name="encryption-key-storage-requirements"></a>Wymagania dotyczące magazynu kluczy szyfrowania  

Azure Disk Encryption wymaga Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi. Magazyn kluczy i maszyny wirtualne muszą znajdować się w tym samym regionie i subskrypcji platformy Azure.

Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednie wydanie)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Następne kroki

- [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)](disk-encryption-key-vault-aad.md)
- [Włączanie Azure Disk Encryption z usługą Azure AD na maszynach wirtualnych z systemem Linux (poprzednia wersja)](disk-encryption-linux-aad.md)
- [Skrypt interfejsu wiersza polecenia Azure Disk Encryption preinstalacji](https://github.com/ejarvi/ade-cli-getting-started)
- [Skrypt programu PowerShell dla Azure Disk Encryption wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)