---
title: Szyfrowanie usługi Azure Storage dla danych przechowywanych w usłudze REST | Microsoft Docs
description: Usługa Azure Storage chroni dane, automatycznie szyfrując je przed ich utrwaleniem do chmury. Wszystkie dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny przy użyciu 256-bitowego szyfrowania AES i jest zgodne ze standardem FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673042"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Szyfrowanie usługi Azure Storage dla danych magazynowanych

Usługa Azure Storage automatycznie szyfruje dane podczas ich utrwalania w chmurze. Szyfrowanie chroni dane i pomaga sprostać wymaganiom bezpieczeństwa i zgodności w organizacji. Dane w usłudze Azure Storage są szyfrowane i odszyfrowywane w sposób niewidoczny dla użytkownika przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych szyfrów blokowych i jest zgodny ze standardem FIPS 140-2. Szyfrowanie usługi Azure Storage jest podobne do szyfrowania funkcją BitLocker w systemie Windows.

Szyfrowanie usługi Azure Storage jest włączone dla wszystkich nowych i istniejących kont magazynu i nie można go wyłączyć. Ponieważ dane są zabezpieczone domyślnie, nie trzeba modyfikować kodu ani aplikacji, aby korzystać z szyfrowania usługi Azure Storage. 

Konta magazynu są szyfrowane niezależnie od ich warstwy wydajności (standardowa lub Premium) lub modelu wdrażania (Azure Resource Manager lub klasyczne). Wszystkie opcje nadmiarowości usługi Azure Storage obsługują szyfrowanie, a wszystkie kopie konta magazynu są szyfrowane. Wszystkie zasoby usługi Azure Storage są szyfrowane, w tym obiektów blob, dysków, plików, kolejek i tabel. Wszystkie metadane obiektu są również szyfrowane.

Szyfrowanie nie ma wpływu na wydajność usługi Azure Storage. Nie ma dodatkowych opłat za szyfrowanie usługi Azure Storage.

Aby uzyskać więcej informacji na temat modułów kryptograficznych związanych z szyfrowaniem usługi Azure Storage, zobacz [Cryptography API: Kolejna generacja @ no__t-0.

## <a name="key-management"></a>Zarządzanie kluczami

Możesz polegać na kluczach zarządzanych przez firmę Microsoft w celu szyfrowania konta magazynu. Możesz też zarządzać szyfrowaniem przy użyciu własnych kluczy wraz z Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Klucze zarządzane przez firmę Microsoft

Domyślnie konto magazynu używa zarządzanych przez firmę Microsoft kluczy szyfrowania. Ustawienia szyfrowania dla konta magazynu można wyświetlić w sekcji **szyfrowanie** [Azure Portal](https://portal.azure.com), jak pokazano na poniższej ilustracji.

![Wyświetlanie konta zaszyfrowanego przy użyciu kluczy zarządzanych przez firmę Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Klucze zarządzane przez klienta

Szyfrowaniem usługi Azure Storage można zarządzać przy użyciu kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych. 

Użyj Azure Key Vault do zarządzania kluczami i inspekcji użycia klucza. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Konto magazynu i Magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/key-vault-overview.md).

Aby odwołać dostęp do kluczy zarządzanych przez klienta, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [interfejs wiersza polecenia Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta w usłudze Azure Storage, zobacz jeden z następujących artykułów:

- [Configure customer-managed keys for Azure Storage encryption from the Azure portal (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage w witrynie Azure Portal)](storage-encryption-keys-portal.md)
- [Configure customer-managed keys for Azure Storage encryption from PowerShell (Konfigurowanie kluczy zarządzanych przez klienta w celu szyfrowania usługi Azure Storage za pomocą programu PowerShell)](storage-encryption-keys-powershell.md)
- [Korzystanie z kluczy zarządzanych przez klienta przy użyciu szyfrowania usługi Azure Storage z poziomu interfejsu wiersza polecenia platformy Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku transferu subskrypcji z jednego katalogu usługi Azure AD do innego, zarządzane tożsamości nie są aktualizowane, a klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz **transfer subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Klucze zarządzane przez klienta nie są obsługiwane w przypadku [usługi Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Szyfrowanie za pomocą usługi Azure Storage a szyfrowanie dysków

Przy użyciu szyfrowania usługi Azure Storage wszystkie konta usługi Azure Storage i zawarte w nich zasoby są szyfrowane, w tym stronicowe obiekty blob, które są dyskami maszyn wirtualnych platformy Azure. Ponadto dyski maszyny wirtualnej platformy Azure mogą być szyfrowane za pomocą [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption korzysta z standardowego w branży [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) w systemach Windows i [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux w celu zapewnienia opartych na systemie operacyjnym rozwiązań do szyfrowania zintegrowanych z programem Azure Key Vault.

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Key Vault?](../../key-vault/key-vault-overview.md)
