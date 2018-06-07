---
title: Funkcje danych klienta w usłudze Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o danych klienta w magazynie kluczy
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/22/2018
ms.author: barclayn
ms.openlocfilehash: 1ddc74b1960095509a77d4b3072017847df42d90
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637365"
---
# <a name="azure-key-vault-customer-data-features"></a>Funkcje danych klienta w usłudze Azure Key Vault

Usługa Azure Key Vault otrzymuje dane klienta podczas tworzenia lub aktualizacji magazynów, kluczy, kluczy tajnych, certyfikatów i kont magazynu zarządzanego. Te dane klienta są bezpośrednio widoczne w portalu Azure i za pośrednictwem interfejsu API REST. Dane klienta można edytowane lub usunięte przez aktualizowanie lub usuwanie obiektu, który zawiera dane.

Dzienniki systemu dostępu są generowane, gdy użytkownik lub aplikacja uzyskuje dostęp do magazynu kluczy. Dostęp szczegółowe dzienniki są dostępne dla klientów korzystających z usługi Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Poniższe informacje identyfikuje dane klienta w ramach usługi Azure Key Vault:

- Zasady dostępu dla usługi Azure Key Vault zawiera identyfikatory obiektów reprezentujących użytkowników, grup lub aplikacji
- Podmiotom certyfikatów może zawierać adresów e-mail lub innego użytkownika lub organizacyjne identyfikatorów
- Kontakty certyfikat może zawierać adresu e-mail użytkownika, nazwy i numery telefonów
- Wystawców certyfikatów może zawierać adresów e-mail, nazwy, numery telefonów, poświadczenia konta i szczegóły organizacji
- Dowolne tagi może odnosić się do obiektów w usłudze Azure Key Vault. Te obiekty obejmują magazynów kluczy, kluczy tajnych, certyfikatów i kont magazynu. Tagów używanych mogą zawierać dane osobowe
- Identyfikatory obiektów zawierają dzienniki dostępu w usłudze Azure Key Vault [UPN](../active-directory/connect/active-directory-aadconnect-userprincipalname.md)i adresów IP dla każdego wywołania interfejsu API REST
- Usługa Azure Key Vault dzienników diagnostycznych może zawierać identyfikatory obiektów i adresów IP dla interfejsu API REST

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Interfejsów API REST, portalu i zestawy SDK używany do tworzenia magazynów, kluczy, kluczy tajnych, certyfikatów i kont magazynu zarządzanego, mogą również aktualizowanie i usuwanie tych obiektów.

Usuwania nietrwałego umożliwia odzyskanie danych usuniętych przez 90 dni, po usunięciu. Używając usuwania nietrwałego, danych może zostać trwale usunięty przed 90 dni, za pomocą operacji przeczyszczania wygasa po okresu przechowywania. Jeśli subskrypcja lub magazynie został skonfigurowany do bloku przeczyścić operacji, nie jest możliwe trwale usunąć danych, aż do okresu przechowywania zaplanowane.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Tych samych interfejsów API REST, obsługi portalu i zestawy SDK, które są używane do tworzenia magazynów, kluczy, kluczy tajnych, certyfikaty i magazynu zarządzanego konta są również umożliwiają wyświetlanie i eksportowanie tych obiektów.

Usługa Azure Key Vault rejestrowanie dostępu jest funkcją opcjonalną, którą można włączyć na generowanie dzienników dla każdego wywołania interfejsu API REST. Te dzienniki będą przekazywane do konta magazynu w ramach subskrypcji, których zastosować zasady przechowywania, który spełnia wymagania organizacji.

Azure dzienników diagnostycznych usługi Key Vault zawierających dane osobowe mogą zostać pobrane przez wysłał żądanie eksportu w portalu zasady zachowania poufności użytkownika. Żądanie musi zostać wykonane przez administratora dzierżawy.

## <a name="next-steps"></a>Kolejne kroki

- [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md)

- [Omówienie usługi Azure Key Vault soft-delete](key-vault-soft-delete-cli.md)

- [magazynów](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Operacje klucza platformy Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Usługa Azure Key Vault operacje kluczy tajnych](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault certyfikaty i zasady](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Wystawców certyfikatów](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Operacje konta magazynu w usłudze Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
