---
title: Szyfrowanie usługi Azure Storage dla danych magazynowanych | Dokumentacja firmy Microsoft
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed wprowadzeniem trwałych do chmury. Wszystkie dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika za pomocą 256-bitowego szyfrowania AES i jest zgodne ze standardem FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6eb7de7810ce23aed4031cca9f038da7149a6f9c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153084"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych

Usługa Azure Storage automatycznie szyfruje dane podczas utrwalania je do chmury. Szyfrowanie chroni Twoje dane i lepiej spełnić zabezpieczeń i zgodności obowiązującymi w organizacji. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika za pomocą 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)jednego bloku najsilniejszych szyfrów i jest zgodne ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobny do szyfrowania BitLocker na Windows.

Szyfrowanie usługi Azure Storage jest włączona dla wszystkich kont magazynu z nowymi i istniejącymi i nie może być wyłączony. Ponieważ Twoje dane są zabezpieczane domyślnie, nie trzeba modyfikować kodu lub aplikacji, aby móc korzystać z szyfrowania usługi Azure Storage. 

Konta magazynu są zaszyfrowane niezależnie od ich warstwę wydajności (standardowa / premium) lub modelu wdrażania (usługi Azure Resource Manager lub model klasyczny). Wszystkie opcje nadmiarowości usługi Azure Storage obsługuje szyfrowanie, oraz szyfrowania wszystkich kopii na koncie magazynu. Wszystkie zasoby usługi Azure Storage są szyfrowane, łącznie z obiektów blob, dyski, pliki, kolejki i tabele. Wszystkie metadane obiektu również są szyfrowane.

Szyfrowanie nie wpływa na wydajność usługi Azure Storage. Nie ma żadnych dodatkowych kosztów, szyfrowanie usługi Azure Storage.

Aby uzyskać więcej informacji na temat modułów kryptograficznych służących do bazowego szyfrowania usługi Azure Storage, zobacz [Cryptography API: Następna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Zarządzanie kluczami

Możesz polegać na zarządzanych przez firmę Microsoft klucze szyfrowania konta magazynu lub szyfrowania można zarządzać za pomocą własnych kluczy, wraz z usługi Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Klucze zarządzane przez firmę Microsoft

Domyślnie konto magazynu używa kluczy szyfrowania zarządzanego przez firmę Microsoft. Zobaczysz ustawienia szyfrowania dla konta magazynu w **szyfrowania** części [witryny Azure portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetl konto zaszyfrowany za pomocą kluczy zarządzanych przez firmę Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Klucze zarządzane przez klienta

Szyfrowanie usługi Azure Storage można zarządzać za pomocą kluczy zarządzanych przez klienta. Kluczy zarządzanych przez klienta zapewniają większą elastyczność i możliwość tworzenia, obracanie, wyłącz i odwołać kontroli dostępu. Można również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych. 

Usługa Azure Key Vault umożliwia zarządzanie kluczami i przeprowadzanie inspekcji użycia kluczy. Możesz utworzyć własne klucze i przechowywać je w magazynie kluczy lub interfejsów API usługi Azure Key Vault można użyć do wygenerowania kluczy. Konto magazynu i magazynu kluczy musi być w tym samym regionie, ale można je w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [co to jest usługa Azure Key Vault?](../../key-vault/key-vault-overview.md).

Aby odwołać dostęp do kluczy zarządzanych przez klienta, zobacz [programu PowerShell dla usługi Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [wiersza polecenia platformy Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odbieranie prawa dostępu skuteczne blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta z usługą Azure Storage, zobacz jedną z następujących artykułów:

- [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage w witrynie Azure portal](storage-encryption-keys-portal.md)
- [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania usługi Azure Storage za pomocą programu PowerShell](storage-encryption-keys-powershell.md)
- [Użyj kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Azure Storage z wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)

> [!NOTE]  
> Klucze zarządzane przez klienta nie są obsługiwane dla [usługi Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Szyfrowanie usługi Azure Storage, a szyfrowanie dysków

Przy użyciu szyfrowania usługi Azure Storage wszystkich kont usługi Azure Storage i zasoby, które zawierają są szyfrowane, w tym stronicowe obiekty BLOB, które wykonują kopie dysków maszyny wirtualnej platformy Azure. Ponadto dyski maszyny wirtualnej platformy Azure może być zaszyfrowany przy użyciu [usługi Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Usługa Azure Disk Encryption używa standardowych [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) na Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux, aby zapewnić szyfrowanie oparte na systemie operacyjnym rozwiązania, które są zintegrowane z usługą Azure Key Vault.

## <a name="next-steps"></a>Kolejne kroki

- [Co to jest usługa Azure Key Vault?](../../key-vault/key-vault-overview.md)
