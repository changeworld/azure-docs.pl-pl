---
title: Kończenie połączeń SSL z certyfikatami Azure Key Vault
description: Dowiedz się, jak zintegrować Application Gateway platformy Azure z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 76807c8ed10e30c554b6aa06ec096c830a86e36e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571976"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Kończenie połączeń SSL z certyfikatami Key Vault

[Azure Key Vault](../key-vault/key-vault-overview.md) to magazyn tajny zarządzany przez platformę, którego można użyć do zabezpieczenia kluczy tajnych, klucze i certyfikatów SSL. Usługa Azure Application Gateway obsługuje integrację z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS. Ta obsługa jest ograniczona do wersji 2 jednostki SKU systemu Application Gateway.

Integracja Key Vault oferuje dwa modele do zakończenia protokołu SSL:

- Można jawnie podać certyfikaty SSL dołączone do odbiornika. Ten model jest tradycyjnym sposobem przekazywania certyfikatów SSL do Application Gateway na potrzeby zakończenia protokołu SSL.
- Opcjonalnie można podać odwołanie do istniejącego certyfikatu Key Vault lub klucza tajnego podczas tworzenia odbiornika z obsługą protokołu HTTPS.

Integracja Application Gateway z Key Vault oferuje wiele korzyści, w tym:

- Silniejsze zabezpieczenia, ponieważ certyfikaty SSL nie są bezpośrednio obsługiwane przez zespół deweloperów aplikacji. Integracja umożliwia oddzielnemu zespołowi ds. zabezpieczeń:
  * Konfigurowanie bram aplikacji.
  * Kontroluj cykle życia bramy aplikacji.
  * Udziel uprawnień wybranym bramom aplikacji dostępu do certyfikatów przechowywanych w magazynie kluczy.
- Obsługa importowania istniejących certyfikatów do magazynu kluczy. Lub użyj Key Vault interfejsów API, aby tworzyć nowe certyfikaty i zarządzać nimi za pomocą dowolnych zaufanych partnerów Key Vault.
- Obsługa automatycznego odnawiania certyfikatów przechowywanych w magazynie kluczy.

Application Gateway obecnie obsługuje tylko certyfikaty zweryfikowane przez oprogramowanie. Sprzętowy moduł zabezpieczeń (HSM) — zweryfikowane certyfikaty nie są obsługiwane. Gdy Application Gateway jest skonfigurowany do korzystania z certyfikatów Key Vault, jego wystąpienia pobierają certyfikat z Key Vault i instalują je lokalnie w celu zakończenia protokołu SSL. Wystąpienia są również sondowane Key Vault co 24 godziny w celu pobrania odnowionej wersji certyfikatu (jeśli istnieje). W przypadku znalezienia zaktualizowanego certyfikatu certyfikat SSL aktualnie skojarzony z odbiornikiem HTTPS jest automatycznie obracany.

## <a name="how-integration-works"></a>Jak działa integracja

Integracja Application Gateway z Key Vault wymaga procesu konfiguracji z trzema krokami:

1. **Tworzenie tożsamości zarządzanej przypisanej przez użytkownika**

   Należy utworzyć lub ponownie użyć istniejącej tożsamości zarządzanej przypisanej przez użytkownika, która Application Gateway używa do pobierania certyfikatów z usługi Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md). Ten krok powoduje utworzenie nowej tożsamości w dzierżawie Azure Active Directory. Tożsamość jest zaufana przez subskrypcję, która jest używana do tworzenia tożsamości.

1. **Konfigurowanie magazynu kluczy**

   Następnie można zaimportować istniejący certyfikat lub utworzyć nowy w magazynie kluczy. Certyfikat będzie używany przez aplikacje uruchamiane za pomocą bramy aplikacji. W tym kroku można także użyć klucza tajnego magazynu kluczy, który jest przechowywany jako hasło mniejsze niż 64 zakodowany plik PFX. Zalecamy używanie typu certyfikatu z powodu możliwości autoodnawiania, która jest dostępna z obiektami typu certyfikat w magazynie kluczy. Po utworzeniu certyfikatu lub wpisu tajnego należy zdefiniować zasady dostępu w magazynie kluczy, aby *umożliwić udzielenie tożsamości* dostępu do klucza tajnego.

1. **Konfigurowanie bramy aplikacji**

   Po wykonaniu dwóch powyższych kroków można skonfigurować lub zmodyfikować istniejącą bramę aplikacji tak, aby korzystała z tożsamości zarządzanej przypisanej przez użytkownika. Istnieje również możliwość skonfigurowania certyfikatu SSL odbiornika HTTP w taki sposób, aby wskazywał pełny identyfikator URI certyfikatu Key Vault lub identyfikatora tajnego.

   ![Certyfikaty magazynu kluczy](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie kończenia połączeń SSL z certyfikatami Key Vault przy użyciu Azure PowerShell](configure-keyvault-ps.md)
