---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372195"
---
### <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta

Do przechowywania kluczy zarządzanych przez klienta należy używać usługi Azure Key Vault. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Zasób usług Cognitive Services i magazyn kluczy muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Nowy zasób usług Cognitive Services jest zawsze szyfrowany przy użyciu kluczy zarządzanych przez firmę Microsoft. Nie jest możliwe włączenie kluczy zarządzanych przez klienta w czasie tworzenia zasobu. Klucze zarządzane przez klienta są przechowywane w usłudze Azure Key Vault, a magazyn kluczy musi być aprowizowany za pomocą zasad dostępu, które przyznają uprawnienia klucza do tożsamości zarządzanej skojarzonej z zasobem usług Cognitive Services. Tożsamość zarządzana jest dostępna tylko po utworzeniu zasobu przy użyciu warstwy cenowej dla cmk.

Aby dowiedzieć się, jak używać kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Azure Key Vault for Cognitive Services, zobacz:

- [Konfigurowanie kluczy zarządzanych przez klienta za pomocą szyfrowania usługi Key Vault dla usług Cognitive Services z witryny Azure portal](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

Włączenie kluczy zarządzanych przez klienta umożliwi również systemowi przypisaną tożsamość zarządzaną, funkcję usługi Azure AD. Po włączeniu tożsamości zarządzanej przypisanej przez system ten zasób zostanie zarejestrowany w usłudze Azure Active Directory. Po zarejestrowaniu, tożsamość zarządzana otrzyma dostęp do usługi Key Vault wybranej podczas konfiguracji klucza zarządzanego przez klienta. Więcej informacji na temat [tożsamości zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)można dowiedzieć się więcej.

> [!IMPORTANT]
> Jeśli wyłączysz tożsamości zarządzane przypisane przez system, dostęp do magazynu kluczy zostanie usunięty, a wszelkie dane zaszyfrowane za pomocą kluczy klienta nie będą już dostępne. Wszystkie funkcje zależne od tych danych przestaną działać.

> [!IMPORTANT]
> Tożsamości zarządzane nie obsługują obecnie scenariuszy między katalogami. Podczas konfigurowania kluczy zarządzanych przez klienta w witrynie Azure portal, tożsamość zarządzana jest automatycznie przypisywana w ramach okładek. Jeśli następnie przenieść subskrypcję, grupę zasobów lub zasób z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z zasobem nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą już nie działać. Aby uzyskać więcej informacji, zobacz **Przenoszenie subskrypcji między katalogami usługi Azure AD** w [często zadawanych pytaniach i znanych problemach z tożsamościami zarządzanymi dla zasobów platformy Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Przechowywanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Aby włączyć klucze zarządzane przez klienta, należy użyć usługi Azure Key Vault do przechowywania kluczy. Należy włączyć właściwości **Usuwanie nietrwałe** i **Nie przeczyszczaj** w magazynie kluczy.

Tylko klucze RSA o rozmiarze 2048 są obsługiwane za pomocą szyfrowania usług Cognitive Services. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze magazynu kluczy,** wpisy tajne i certyfikaty usługi Azure Key Vault — informacje o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)usługi Azure Key Vault .

### <a name="rotate-customer-managed-keys"></a>Obracanie kluczy zarządzanych przez klienta

Klucz zarządzany przez klienta można obrócić w usłudze Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować zasób usług Cognitive Services, aby użyć nowego klucza URI. Aby dowiedzieć się, jak zaktualizować zasób w celu użycia nowej wersji klucza w witrynie Azure portal, zobacz sekcję **"Aktualizowanie wersji klucza** w [obszarze Konfigurowanie kluczy zarządzanych przez klienta dla usług Cognitive Services za pomocą portalu Azure](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md).

Obracanie klucza nie powoduje ponownego szyfrowania danych w zasobie. Nie ma żadnych dalszych działań wymaganych od użytkownika.

### <a name="revoke-access-to-customer-managed-keys"></a>Cofanie dostępu do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) lub [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Cofnięcie dostępu skutecznie blokuje dostęp do wszystkich danych w zasobie usług Cognitive Services, ponieważ klucz szyfrowania jest niedostępny dla usług Cognitive Services.


