---
title: Funkcje platformy Azure Key Vault klienta danych — usługa Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o danych klientów w usłudze Key Vault
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: f044c0da1cb1ed3f3c7f118764cc0e685cb3998f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687035"
---
# <a name="azure-key-vault-customer-data-features"></a>Funkcje danych klienta w usłudze Azure Key Vault

Usługa Azure Key Vault otrzymuje dane klienta, podczas tworzenia lub aktualizowania magazynów kluczy, wpisów tajnych, certyfikatów i kont magazynu zarządzanego. Te dane klienta są bezpośrednio widoczne w witrynie Azure portal i za pośrednictwem interfejsu API REST. Dane klienta można edytować lub usunąć, aktualizowania lub usuwania obiektu, który zawiera dane.

Dzienniki dostępu systemu są generowane, gdy użytkownik lub aplikacja uzyskuje dostęp do usługi Key Vault. Dostęp do szczegółowych dzienników są dostępne dla klientów korzystających z usługi Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identyfikowanie danych klienta

Poniższe informacje identyfikuje dane klienta w ramach usługi Azure Key Vault:

- Zasady dostępu do usługi Azure Key Vault zawierają identyfikatory obiektów reprezentująca użytkownikom, grupom i aplikacjom
- Podmiotom certyfikatów może zawierać adresy e-mail lub innego użytkownika lub identyfikatorów organizacyjnych
- Kontakty certyfikatu może zawierać adresy e-mail użytkowników, nazw lub numerów telefonów
- Wystawcy certyfikatu może zawierać adresy e-mail, nazwy, numerów telefonów, poświadczenia konta i szczegóły organizacyjne
- Dowolne tagi można zastosować do obiektów w usłudze Azure Key Vault. Te obiekty obejmują magazynów kluczy, wpisów tajnych, certyfikatów i kont magazynu. Tagi używane mogą zawierać dane osobowe
- Dzienniki dostępu w usłudze Azure Key Vault zawierają identyfikatory obiektów [UPN](../active-directory/hybrid/plan-connect-userprincipalname.md)i adresy IP dla każdego wywołania interfejsu API REST
- Dzienniki diagnostyczne platformy Azure Key Vault może zawierać identyfikatory obiektów i adresy IP dla wywołań interfejsu API REST

## <a name="deleting-customer-data"></a>Usuwanie danych klienta

Interfejsy API REST, środowisko portalu i zestawy SDK umożliwia tworzenie magazynów kluczy, wpisów tajnych, certyfikatów i kont magazynu zarządzanego mogą również aktualizowanie i usuwanie tych obiektów.

Usuwanie nietrwałe umożliwia odzyskanie danych usuniętych przez 90 dni po usunięciu. Korzystając z usuwania nietrwałego, dane mogą trwale usunięte przed 90 dni, okres przechowywania wygasa po za pomocą operacji przeczyszczania. Jeśli magazyn lub subskrypcja została skonfigurowana do bloku przeczyścić operacji, nie jest możliwe trwale usunąć danych, dopóki nie upłynął okres przechowywania zaplanowane.

## <a name="exporting-customer-data"></a>Eksportowanie danych klienta

Tych samych interfejsów API REST, środowisko portalu i zestawów SDK, które są używane do tworzenia magazynów, kluczy, wpisów tajnych i certyfikatów, a także zarządzane konta usługi storage umożliwiają wyświetlanie i eksportowanie tych obiektów.

Usługa Azure Key Vault, rejestrowanie dostępu jest opcjonalna funkcja, która można włączyć w celu generowania dzienników dla każdego wywołania interfejsu API REST. Te dzienniki będą przekazywane do konta magazynu w ramach subskrypcji, którym zastosować zasady przechowywania, który spełnia wymagania organizacji.

Usługa Key Vault dzienniki diagnostyczne platformy Azure, które zawierają dane osobowe mogą być pobierane przez wysłał żądanie eksportu w portalu rozwiązania prywatność użytkownika. Żądanie musi zostać wykonane przez administratora dzierżawy.

## <a name="next-steps"></a>Kolejne kroki

- [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md)

- [Omówienie usuwania nietrwałego w usłudze Azure Key Vault](key-vault-soft-delete-cli.md)

- [Usługa Azure Key Vault kluczowych operacji](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Usługa Azure operacji dla wpisu tajnego usługi Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Usługa Azure Key Vault certyfikaty i zasady](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Operacje konta magazynu w usłudze Azure usługi Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
