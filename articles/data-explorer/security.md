---
title: Zabezpieczanie klastrów eksploratora danych platformy Azure na platformie Azure
description: Dowiedz się, jak zabezpieczyć klastry w Eksploratorze danych platformy Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373357"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Zabezpieczanie klastrów eksploratora danych platformy Azure na platformie Azure

Ten artykuł zawiera wprowadzenie do zabezpieczeń w Eksploratorze danych platformy Azure, aby pomóc chronić dane i zasoby w chmurze i spełniać potrzeby w zakresie bezpieczeństwa twojej firmy. Ważne jest, aby zapewnić bezpieczeństwo klastrów. Zabezpieczanie klastrów obejmuje co najmniej jedną funkcję platformy Azure, która obejmuje bezpieczny dostęp i magazyn. Ten artykuł zawiera informacje ułatwiające bezpieczeństwo klastra.

## <a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Typowym wyzwaniem podczas tworzenia aplikacji w chmurze jest zarządzanie poświadczeniami w kodzie do uwierzytelniania w usługach w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. Poświadczenia nie powinny być przechowywane na stacjach roboczych deweloperów lub zaewidencjonowane w formancie źródłowym. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault.

Tożsamości zarządzane usługi Azure Active Directory (Azure AD) dla zasobów platformy Azure rozwiązują ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie. Aby uzyskać więcej informacji na temat tej usługi, zobacz [tożsamości zarządzane dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) na stronie przeglądu.

## <a name="data-encryption"></a>Szyfrowanie danych

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

[Usługa Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomaga chronić i chronić dane w celu spełnienia zobowiązań dotyczących zabezpieczeń i zgodności organizacji. Zapewnia szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych klastra. Usługa Azure Disk Encryption integruje się również z [usługą Azure Key Vault,](/azure/key-vault/)która umożliwia nam kontrolowanie kluczy i wpisów tajnych szyfrowania dysków i zarządzanie nimi oraz zapewnia szyfrowanie wszystkich danych na dyskach maszyn wirtualnych. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault

Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych. Szyfrowanie danych można zarządzać na poziomie pamięci masowej za pomocą własnych kluczy. Klucz zarządzany przez klienta służy do ochrony i kontrolowania dostępu do głównego klucza szyfrowania, który służy do szyfrowania i odszyfrowywania wszystkich danych. Klucze zarządzane przez klienta oferują większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony danych.

Użyj usługi Azure Key Vault do przechowywania kluczy zarządzanych przez klienta. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsu API usługi Azure Key Vault do generowania kluczy. Klaster usługi Azure Data Explorer i usługa Azure Key Vault muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-overview). Aby uzyskać szczegółowe wyjaśnienie dotyczące kluczy zarządzanych przez klienta, zobacz [Klucze zarządzane przez klienta za pomocą usługi Azure Key Vault](/azure/storage/common/storage-service-encryption). Konfigurowanie kluczy zarządzanych przez klienta w klastrze eksploratora danych platformy Azure przy użyciu języka [C#](/azure/data-explorer/customer-managed-keys-csharp) lub [szablonu usługi Azure Resource Manager](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> Klucze zarządzane przez klienta opierają się na zarządzanych tożsamościach zasobów platformy Azure, funkcja usługi Azure Active Directory (Azure AD). Aby skonfigurować klucze zarządzane przez klienta w witrynie Azure portal, należy skonfigurować tożsamość zarządzaną **przypisaną przez system** do klastra, jak opisano w [temacie Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Data Explorer](/azure/data-explorer/managed-identities).

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowywanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Aby włączyć klucze zarządzane przez klienta w klastrze, użyj usługi Azure Key Vault do przechowywania kluczy. Należy włączyć właściwości **Usuwanie nietrwałe** i **Nie przeczyszczaj** w magazynie kluczy. Magazyn kluczy musi znajdować się w tej samej subskrypcji co klaster. Usługa Azure Data Explorer używa zarządzanych tożsamości dla zasobów platformy Azure do uwierzytelniania w magazynie kluczy w celu operacji szyfrowania i odszyfrowywania. Tożsamości zarządzane nie obsługują scenariuszy między katalogami.

#### <a name="rotate-customer-managed-keys"></a>Obracanie kluczy zarządzanych przez klienta

Klucz zarządzany przez klienta można obrócić w usłudze Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować klaster, aby użyć nowego klucza URI. Obracanie klucza nie powoduje ponownego szyfrowania danych w klastrze. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Cofanie dostępu do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) lub [Azure Key Vault CLI](/cli/azure/keyvault). Cofnięcie dostępu blokuje dostęp do wszystkich danych na poziomie magazynu klastra, ponieważ klucz szyfrowania jest w związku z tym niedostępny przez Eksploratora danych platformy Azure.

> [!Note]
> Gdy Usługa Azure Data Explorer stwierdzi, że dostęp do klucza zarządzanego przez klienta zostanie odwołany, automatycznie zawiesi klaster, aby usunąć wszystkie buforowane dane. Po przywróceniu dostępu do klucza klaster musi zostać wznowiony ręcznie.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Za pomocą [kontroli dostępu opartej na rolach (RBAC)](/azure/role-based-access-control/overview)można segregować obowiązki w zespole i udzielić tylko wymaganego dostępu do użytkowników klastra. Zamiast dawać wszystkim nieograniczone uprawnienia do klastra, można zezwolić tylko na niektóre akcje. Kontrolę dostępu [dla baz danych](/azure/data-explorer/manage-database-permissions) można skonfigurować w [witrynie Azure Portal](/azure/role-based-access-control/role-assignments-portal), używając interfejsu [wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)lub programu Azure [PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Następne kroki

* [Zabezpiecz swój klaster w Eksploratorze danych platformy Azure — portal,](manage-cluster-security.md) włączając szyfrowanie w spoczynku.
* [Konfigurowanie tożsamości zarządzanych dla klastra usługi Azure Data Explorer](managed-identities.md)
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu szablonu usługi Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Konfigurowanie kluczy zarządzanych przez klienta przy użyciu języka C #](customer-managed-keys-csharp.md)

