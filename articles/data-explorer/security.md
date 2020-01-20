---
title: Zabezpieczanie klastrów usługi Azure Eksplorator danych na platformie Azure
description: Dowiedz się więcej na temat zabezpieczania klastrów w usłudze Azure Eksplorator danych.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5f3bceb8398f9837f6f8eaa390def41456daf08d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271590"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Zabezpieczanie klastrów usługi Azure Eksplorator danych na platformie Azure

Ten artykuł zawiera wprowadzenie do zabezpieczeń w usłudze Azure Eksplorator danych, które ułatwiają ochronę danych i zasobów w chmurze oraz zaspokajanie potrzeb związanych z bezpieczeństwem firmy. Ważne jest, aby zapewnić bezpieczeństwo klastrów. Zabezpieczanie klastrów obejmuje co najmniej jedną funkcję platformy Azure, która obejmuje bezpieczny dostęp i magazyn. Ten artykuł zawiera informacje ułatwiające zachowanie bezpieczeństwa klastra.

## <a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest zarządzanie poświadczeniami w kodzie do uwierzytelniania w usługach w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. Poświadczenia nie powinny być przechowywane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault.

Usługa Azure Active Directory (Azure AD) zarządzana tożsamość dla zasobów platformy Azure rozwiązuje ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie. Aby uzyskać więcej informacji na temat tej usługi, zobacz stronę Omówienie [zarządzanych tożsamości dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) .

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Zapewnia szyfrowanie woluminów systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Azure Disk Encryption integruje się również z [Azure Key Vault](/azure/key-vault/), co pozwala nam kontrolować klucze szyfrowania dysków i wpisy tajne oraz zarządzać nimi, a także upewnić się, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta z Azure Key Vault

Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych. Możesz zarządzać szyfrowaniem danych na poziomie magazynu przy użyciu własnych kluczy. Klucz zarządzany przez klienta służy do ochrony i kontrolowania dostępu do głównego klucza szyfrowania używanego do szyfrowania i odszyfrowywania wszystkich danych. Klucze zarządzane przez klienta zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony danych.

Użyj Azure Key Vault do przechowywania kluczy zarządzanych przez klienta. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć interfejsu API Azure Key Vault, aby generować klucze. Klaster Eksplorator danych platformy Azure i Azure Key Vault muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](/azure/key-vault/key-vault-overview). Szczegółowe wyjaśnienie kluczy zarządzanych przez klienta można znaleźć w temacie [klucze zarządzane przez klienta z Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). Aby skonfigurować klucze zarządzane przez klienta w Azure Portal, należy skonfigurować tożsamość zarządzaną **SystemAssigned** w klastrze.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowuj klucze zarządzane przez klienta w Azure Key Vault

Aby włączyć klucze zarządzane przez klienta w klastrze, użyj Azure Key Vault do przechowywania kluczy. Należy włączyć zarówno właściwości **nietrwałego usuwania** , jak i **nie przeczyszczania** w magazynie kluczy. Magazyn kluczy musi znajdować się w tej samej subskrypcji co klaster. Usługa Azure Eksplorator danych używa zarządzanych tożsamości dla zasobów platformy Azure do uwierzytelniania w magazynie kluczy na potrzeby operacji szyfrowania i odszyfrowywania. Zarządzane tożsamości nie obsługują scenariuszy między katalogami.

#### <a name="rotate-customer-managed-keys"></a>Obróć klucze zarządzane przez klienta

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować klaster, aby używał nowego identyfikatora URI klucza. Obracanie klucza nie wyzwala ponownego szyfrowania danych w klastrze. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) lub [interfejs wiersza polecenia Azure Key Vault](/cli/azure/keyvault). Odwoływanie dostępu uniemożliwia dostęp do wszystkich danych na poziomie magazynu klastra, ponieważ klucz szyfrowania jest w związku z tym niedostępny przez Eksplorator danych platformy Azure.

> [!Note]
> Gdy usługa Azure Eksplorator danych identyfikuje, że dostęp do klucza zarządzanego przez klienta jest odwołany, spowoduje to automatyczne wstrzymanie klastra w celu usunięcia wszystkich danych w pamięci podręcznej. Po zwróceniu dostępu do klucza klaster musi zostać wznowiony ręcznie.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Przy użyciu [kontroli dostępu opartej na rolach (RBAC)](/azure/role-based-access-control/overview)można oddzielić cła w zespole i przyznać dostęp tylko do użytkowników klastrów. Zamiast udzielać każdemu nieograniczonyemu uprawnienia do klastra, można zezwolić tylko na niektóre akcje. Można skonfigurować [kontrolę dostępu dla baz danych](/azure/data-explorer/manage-database-permissions) w [Azure Portal](/azure/role-based-access-control/role-assignments-portal)przy użyciu [interfejsu wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)lub [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Eksplorator danych](managed-identities.md)
* [Zabezpiecz klaster w usłudze Azure Eksplorator danych — Portal](manage-cluster-security.md) , włączając szyfrowanie w stanie spoczynku.
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciuC#](customer-managed-keys-csharp.md)

