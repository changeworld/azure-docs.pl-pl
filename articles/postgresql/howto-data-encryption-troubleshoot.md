---
title: Rozwiązywanie problemów z szyfrowaniem danych — usługa Azure Database for PostgreSQL — single server
description: Dowiedz się, jak rozwiązywać problemy z szyfrowaniem danych w bazie danych azure dla postgreSQL — single server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2902ff17ac14a48f1a11259339c2ab1bc4595980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299264"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Rozwiązywanie problemów z szyfrowaniem danych w usłudze Azure Database for PostgreSQL — pojedynczy serwer

Ten artykuł ułatwia identyfikowanie i rozwiązywanie typowych problemów, które mogą wystąpić we wdrażaniu usługi Azure Database dla postgreSQL na jednym serwerze po skonfigurowaniu przy użyciu szyfrowania danych przy użyciu klucza zarządzanego przez klienta.

## <a name="introduction"></a>Wprowadzenie

Podczas konfigurowania szyfrowania danych do używania klucza zarządzanego przez klienta w usłudze Azure Key Vault serwer wymaga ciągłego dostępu do klucza. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w usłudze Azure Key Vault, odmówi wszystkich połączeń, zwróci odpowiedni komunikat o błędzie i zmieni jego stan ***na Niedostępny*** w witrynie Azure Portal.

Jeśli nie potrzebujesz już niedostępnej bazy danych Platformy Azure dla serwera PostgreSQL, możesz go usunąć, aby zatrzymać poniesienie kosztów. Żadne inne akcje na serwerze nie są dozwolone, dopóki dostęp do magazynu kluczy nie zostanie przywrócony, a serwer będzie dostępny. Nie można również zmienić opcji szyfrowania `Yes`danych z (zarządzanego przez klienta) na `No` (zarządzanego przez usługę) na niedostępnym serwerze, gdy jest on zaszyfrowany za pomocą klucza zarządzanego przez klienta. Przed ponownym dostępem do serwera należy ponownie ponownie ocenić klucz. Ta akcja jest niezbędna do ochrony danych przed nieautoryzowanym dostępem, podczas gdy uprawnienia do klucza zarządzanego przez klienta są odwoływane.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Typowe błędy powodujące niedostępność serwera

Następujące błędy konfiguracji powodują większość problemów z szyfrowaniem danych, które używają kluczy usługi Azure Key Vault:

- Magazyn kluczy jest niedostępny lub nie istnieje:
  - Magazyn kluczy został przypadkowo usunięty.
  - Sporadyczny błąd sieciowy powoduje, że magazyn kluczy jest niedostępny.

- Nie masz uprawnień dostępu do magazynu kluczy lub klucz nie istnieje:
  - Klucz wygasł lub został przypadkowo usunięty lub wyłączony.
- Tożsamość zarządzana wystąpienia usługi Azure Database for PostgreSQL została przypadkowo usunięta.
  - Tożsamość zarządzana wystąpienia usługi Azure Database for PostgreSQL ma niewystarczające uprawnienia klucza. Na przykład uprawnienia nie obejmują Get, Wrap i Unwrap.
  - Uprawnienia tożsamości zarządzanej do wystąpienia usługi Azure Database for PostgreSQL zostały odwołane lub usunięte.

## <a name="identify-and-resolve-common-errors"></a>Identyfikowanie i rozwiązywanie typowych błędów

### <a name="errors-on-the-key-vault"></a>Błędy w magazynie kluczy

#### <a name="disabled-key-vault"></a>Wyłączony przechowalnia kluczy

- `AzureKeyVaultKeyDisabledMessage`
- **Wyjaśnienie:** Nie można ukończyć operacji na serwerze, ponieważ klucz usługi Azure Key Vault jest wyłączony.

#### <a name="missing-key-vault-permissions"></a>Brak uprawnień do przechowalni kluczy

- `AzureKeyVaultMissingPermissionsMessage`
- **Wyjaśnienie:** Serwer nie ma wymaganych uprawnień Pobierz, Zawijaj i Rozpakuj do usługi Azure Key Vault. Udziel brakujących uprawnień podmiotowi usługi o identyfikatorze.

### <a name="mitigation"></a>Środki zaradcze

- Upewnij się, że klucz zarządzany przez klienta jest obecny w magazynie kluczy.
- Zidentyfikuj magazyn kluczy, a następnie przejdź do magazynu kluczy w witrynie Azure portal.
- Upewnij się, że klucz identyfikatora URI identyfikuje klucz, który jest obecny.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie szyfrowania danych za pomocą klucza zarządzanego przez klienta w usłudze Azure Database for PostgreSQL za pomocą portalu Azure](howto-data-encryption-portal.md)
