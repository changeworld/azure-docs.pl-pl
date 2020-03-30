---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297890"
---
Usługa Azure Data Explorer szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta do szyfrowania danych. 

Klucze zarządzane przez klienta muszą być przechowywane w [magazynie kluczy platformy Azure.](/azure/key-vault/key-vault-overview) Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsu API usługi Azure Key Vault do generowania kluczy. Klaster usługi Azure Data Explorer i magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Szczegółowe informacje na temat kluczy zarządzanych przez klienta można znaleźć w [kluczach zarządzanych przez klienta za pomocą usługi Azure Key Vault](/azure/storage/common/storage-service-encryption). 

W tym artykule pokazano, jak skonfigurować klucze zarządzane przez klienta.

## <a name="configure-azure-key-vault"></a>Konfigurowanie usługi Azure Key Vault

Aby skonfigurować klucze zarządzane przez klienta za pomocą Eksploratora danych platformy Azure, należy [ustawić dwie właściwości w magazynie kluczy:](/azure/key-vault/key-vault-ovw-soft-delete)Usuwanie **nietrwałe** i **Nie czyścić**. Te właściwości nie są domyślnie włączone. Aby włączyć te właściwości, wykonaj **włączanie usuwania nietrwale** i **włączanie ochrony przeczyszczania** w [programie PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) lub [narzędzia Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) w nowym lub istniejącym magazynie kluczy. Obsługiwane są tylko klucze RSA o rozmiarze 2048. Aby uzyskać więcej informacji o kluczach, zobacz [Klawisze Przechowalni kluczy](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Szyfrowanie danych przy użyciu kluczy zarządzanych przez klienta nie jest obsługiwane w [klastrach linii odniesienia i zwolenników](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Przypisywanie tożsamości do klastra

Aby włączyć klucze zarządzane przez klienta dla klastra, należy najpierw przypisać do klastra tożsamość zarządzaną przypisaną przez system. Użyjesz tej tożsamości zarządzanej, aby udzielić uprawnień klastra, aby uzyskać dostęp do magazynu kluczy. Aby skonfigurować tożsamości zarządzane przypisane do systemu, zobacz [tożsamość zarządzana](/azure/data-explorer/managed-identities).