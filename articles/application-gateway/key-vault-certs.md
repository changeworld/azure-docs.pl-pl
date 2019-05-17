---
title: Kończenie żądań SSL z certyfikatami w usłudze Azure Key Vault
description: Dowiedz się więcej na temat integracji usługi Azure Application Gateway z usługą Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827625"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Kończenie żądań SSL z certyfikatami usługi Key Vault

[Usługa Azure Key Vault](../key-vault/key-vault-whatis.md) jest wpis tajny zarządzane platformy przechowywania, można użyć do ochrony kluczy tajnych, klucze i certyfikaty SSL. Usługa Azure Application Gateway obsługuje integrację z usługą Key Vault (w publicznej wersji zapoznawczej) dla certyfikatów serwera, które są dołączone do odbiorników z włączonym protokołem HTTPS. Ta obsługa jest ograniczona do 2 jednostki SKU bramy Application Gateway.

> [!IMPORTANT]
> Integracja usługi Application Gateway, za pomocą usługi Key Vault jest obecnie w publicznej wersji zapoznawczej. Tej wersji zapoznawczej jest oferowana bez Umowa dotycząca poziomu usług (SLA) i nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ta publiczna wersja zapoznawcza oferuje dwa modele do kończenia żądań SSL:

- Możesz jawnie określić certyfikaty SSL, dołączone do odbiornika. Ten model jest tradycyjny sposób przekazania certyfikaty SSL do usługi Application Gateway dla kończenia żądań SSL.
- Opcjonalnie możesz podać odwołanie do istniejącego certyfikatu usługi Key Vault lub klucz tajny, podczas tworzenia odbiornika z włączonym protokołem HTTPS.

Integracja bramy aplikacji z usługą Key Vault oferuje wiele korzyści, w tym:

- Lepsze zabezpieczenia, ponieważ certyfikaty SSL nie są bezpośrednio obsługiwane przez zespół deweloperów aplikacji. Dzięki integracji zespołu zabezpieczeń osobno do:
  * Konfigurowanie bramy aplikacji.
  * Kontrolka bramy procesy cyklów życia aplikacji.
  * Udziel uprawnień do bram wybranej aplikacji do dostępu do certyfikatów, które są przechowywane w magazynie kluczy.
- Obsługa importowania istniejących certyfikatów do magazynu kluczy. Lub Użyj klucza magazynu z interfejsów API do tworzenia i zarządzania nowe certyfikaty z żadnym z zaufanych partnerów usługi Key Vault.
- Obsługa automatycznego odnawiania certyfikatów, które są przechowywane w magazynie kluczy.

Usługa Application Gateway obecnie obsługuje tylko certyfikaty z zweryfikowana przez oprogramowanie. Sprzętowy moduł zabezpieczeń (HSM)-zweryfikowanych certyfikaty nie są obsługiwane. Po skonfigurowaniu bramy aplikacji do używania certyfikatów usługi Key Vault jego wystąpienia pobrać certyfikatu z usługi Key Vault i je zainstalować lokalnie kończenia żądań SSL. Wystąpienia również wykonać sondowanie usługi Key Vault w odstępach czasu 24-godzinnym, pobieranie wersji odnowionego certyfikatu, jeśli taki istnieje. Jeśli zostanie znaleziony zaktualizowany certyfikat, certyfikat SSL skojarzonych obecnie z odbiornik HTTPS jest automatycznie obracane.

## <a name="how-integration-works"></a>Jak działa Integracja

Integracja bramy aplikacji z usługą Key Vault wymaga procesu konfiguracji trzech kroków:

1. **Tworzenie zarządzanych tożsamości przypisanych przez użytkownika**

   Utwórz lub ponowne używanie istniejących przypisanych do użytkowników zarządzanych tożsamości, która używa bramy Application Gateway, można pobrać certyfikatów z usługi Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md). Spowoduje to utworzenie nowej tożsamości w dzierżawie usługi Azure Active Directory. Tożsamość jest zaufana dla subskrypcji, która służy do tworzenia tożsamości.

1. **Konfigurowanie magazynu kluczy**

   Możesz następnie zaimportuj istniejący certyfikat lub Utwórz nową w magazynie kluczy. Certyfikat będzie używany przez aplikacje działające przez bramę aplikacji. W tym kroku możesz również użyć wpisu tajnego usługi key vault, który jest przechowywany jako hasło, podstawowy Base64 pliku PFX. Firma Microsoft zaleca użycie typu certyfikatu z powodu możliwości ich automatyczne odnawianie, który jest dostępny z obiektami typu certyfikatu w magazynie kluczy. Po utworzeniu certyfikatu lub klucza tajnego, definiowanie zasad dostępu w usłudze key vault, aby umożliwić tożsamości należy przyznać *uzyskać* dostępu do klucza tajnego.

1. **Konfigurowanie bramy aplikacji**

   Po wykonaniu dwa poprzednie kroki, można skonfigurować lub zmodyfikować istniejącą bramę aplikacji do używania zarządzanych tożsamości przypisanych przez użytkownika. Możesz również skonfigurować certyfikat SSL odbiornika HTTP wskaż pełny identyfikator URI z certyfikatem usługi Key Vault lub identyfikator wpisu tajnego.

   ![Certyfikaty usługi Key vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie kończenia żądań SSL z certyfikatami usługi Key Vault przy użyciu programu Azure PowerShell](configure-keyvault-ps.md)
