---
title: Kończenie żądań SSL z certyfikatami usługi Key Vault
description: Dowiedz się, jak zintegrować bramy aplikacji platformy Azure z usługą Key Vault dla certyfikatów serwera, które są dołączone do odbiorników obsługujące protokół HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/19/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014179"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Kończenie żądań SSL z certyfikatami usługi Key Vault

[Usługa Azure Key Vault](../key-vault/key-vault-whatis.md) jest zarządzane przez platformę magazynu wpisów tajnych można użyć do ochrony kluczy tajnych, klucze i certyfikaty SSL. Usługa Application Gateway obsługuje integrację z usługą Key Vault (w publicznej wersji zapoznawczej) dla serwera certyfikatów, które są dołączone do odbiorników obsługujące protokół HTTPS. Ta obsługa jest ograniczona do 2 jednostki SKU bramy Application Gateway.

> [!IMPORTANT]
> Integracja usługi Application Gateway Key Vault jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Istnieją dwa modele dla kończenia żądań SSL z tej publicznej wersji zapoznawczej:

- Możesz jawnie określić certyfikaty SSL, dołączone do odbiornika. Jest to tradycyjnego modelu używania przekazywanie certyfikatów SSL na bramie aplikacji dla kończenia żądań SSL.
- Opcjonalnie możesz podać odwołanie do istniejącego certyfikatu usługi Key Vault lub klucz tajny podczas HTTPS włączone tworzenia odbiornika.

Istnieje wiele korzyści dzięki integracji z usługą Key Vault w tym:

- Lepsze zabezpieczenia, ponieważ certyfikaty SSL nie są bezpośrednio obsługiwane przez zespół deweloperów aplikacji. Integracja z usługą Key Vault umożliwia zespołowi zabezpieczeń osobno do obsługi administracyjnej, kontroli cyklu życia i udzielić odpowiednich uprawnień, aby wybrać bramy Application Gateway do dostępu do certyfikatów przechowywanych w usłudze Key Vault.
- Obsługuje do importowania istniejących certyfikatów do usługi Key Vault lub tworzenie i zarządzanie nimi nowe certyfikaty z żadnym z zaufanych partnerów usługi Key Vault przy użyciu interfejsów API programu klucza magazynu.
- Obsługa certyfikatów przechowywanych w usłudze Key Vault, aby automatycznie odnowić.

Usługa Application Gateway obecnie obsługuje tylko certyfikaty oprogramowania zweryfikowane. Sprzęt zabezpieczeń modułu HSM zweryfikować certyfikaty nie są obsługiwane. Po skonfigurowaniu bramy aplikacji do używania certyfikatów usługi Key Vault jego wystąpienia pobrać certyfikatu z usługi Key Vault i je zainstalować lokalnie kończenia żądań SSL. Wystąpienia okresowo sondować usługi Key Vault w 24-godzinnym interwałem pobieranie wersji odnowionego certyfikatu, jeśli taki istnieje. Jeśli zostanie znaleziony zaktualizowany certyfikat, certyfikat SSL skojarzonych obecnie z odbiornik HTTPS jest automatycznie obracane.

## <a name="how-it-works"></a>Jak to działa

Integracja z usługą Key Vault wymaga procesu konfiguracji trzech kroków:

1. **Tworzenie zarządzanych tożsamości przypisanych przez użytkownika**

   Należy utworzyć lub ponownie użyć istniejącego użytkownika przypisane zarządzanych tożsamości, która używa bramy Application Gateway, można pobrać certyfikatów z usługi Key Vault w Twoim imieniu. Aby uzyskać więcej informacji, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md) Spowoduje to utworzenie nowej tożsamości w dzierżawie usługi Azure AD, która jest zaufana dla subskrypcji, używany do tworzenia tożsamości.
1. **Konfiguruj usługę Key Vault**

   Następnie należy albo importu lub Utwórz nowy certyfikat w usłudze Key Vault, używane przez aplikacje uruchomione przez bramę aplikacji. Klucz tajny usługi Key Vault, przechowywane jako hasło podstawowej 64 zakodowany plik PFX można również w tym kroku. Przy użyciu typu certyfikatu jest preferowana ze względu na możliwości ich automatyczne odnawianie, które są dostępne z obiektami typu certyfikatu w usłudze Key Vault. Po utworzeniu certyfikatu lub klucza tajnego, zasady dostępu musi być zdefiniowany w usłudze Key Vault umożliwia tożsamości należy przyznać *uzyskać* dostęp do pobierania klucza tajnego.

1. **Konfigurowanie bramy aplikacji**

   Po wykonaniu dwa poprzednie kroki, możesz aprowizować, lub zmodyfikować istniejącą bramę aplikacji do użycia z zarządzanych tożsamości przypisanych przez użytkownika. Możesz również skonfigurować certyfikat SSL odbiornika HTTP wskaż certyfikat pełny identyfikator URI z magazynu Key Vault lub identyfikator wpisu tajnego.

![Certyfikaty usługi Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie kończenia żądań SSL z certyfikatami usługi Key Vault przy użyciu programu Azure PowerShell](configure-keyvault-ps.md).